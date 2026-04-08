# SECURITY.md — Modelo de Referencia de Seguridad

> **Propósito:** Documento de referencia, NO "así trabajo hoy".
> Este archivo describe cómo debería manejarse la seguridad en proyectos profesionales.
> La IA lo consulta al implementar auth, manejar secrets, o revisar código generado.
> Ver AGENTS.md para saber cómo trabajo actualmente.

---

## 🔑 Autenticación y Autorización

### La diferencia que importa
- **Autenticación:** ¿Quién eres? → verificar identidad (login, token)
- **Autorización:** ¿Qué puedes hacer? → verificar permisos (roles, scopes)

Confundirlas es el error más común y el más peligroso.

### JWT — cómo usarlo bien
```typescript
import jwt from 'jsonwebtoken';

// ✅ Generación correcta
const token = jwt.sign(
  { userId: user.id, role: user.role },  // payload mínimo — no meter datos sensibles
  process.env.JWT_SECRET!,               // secret desde env, nunca hardcodeado
  { expiresIn: '15m' }                   // access token corto; refresh token aparte
);

// ✅ Verificación correcta
try {
  const payload = jwt.verify(token, process.env.JWT_SECRET!) as JwtPayload;
  // payload.userId ya verificado
} catch (err) {
  // Token expirado, manipulado, o inválido
  throw new UnauthorizedError('Token inválido');
}
```

### Qué NO meter en un JWT
```
❌ Contraseñas (ni hasheadas)
❌ Datos bancarios o personales sensibles
❌ Permisos granulares que cambian frecuentemente (mejor consultarlos en DB)
✅ userId, role, sessionId — lo mínimo para identificar la sesión
```

### Roles y permisos — patrón recomendado
```typescript
// Definir roles como constantes, no strings mágicos
export const BotRole = {
  ADMIN: 'admin',
  PLAYER: 'player',
  MODERATOR: 'moderator',
} as const;

// Middleware de autorización
function requireRole(role: string) {
  return (ctx: Context, next: Next) => {
    if (ctx.user.role !== role) {
      throw new ForbiddenError('Permisos insuficientes');
    }
    return next();
  };
}
```

---

## 🔐 Contraseñas

```typescript
import bcrypt from 'bcrypt';

const SALT_ROUNDS = 12; // 10 mínimo, 12 recomendado, 14+ para datos críticos

// Hash al registrar
const hashedPassword = await bcrypt.hash(plainPassword, SALT_ROUNDS);

// Comparar al hacer login — NUNCA comparar strings directamente
const isValid = await bcrypt.compare(plainPassword, hashedPassword);
```

**Regla:** Si en algún momento guardas una contraseña como string en la DB, es un bug crítico de seguridad. Sin excepción.

---

## 🌍 Variables de Entorno y Secrets

### Estructura de archivos
```
.env.example     ← en git (sin valores reales, solo keys)
.env.local       ← en .gitignore (valores de desarrollo)
.env.production  ← nunca en git, en el servidor o vault
```

### .env.example — la fuente de verdad documentada
```env
# Auth
JWT_SECRET=         # min 32 chars random string
JWT_REFRESH_SECRET= # diferente al anterior

# DB
DATABASE_URL=       # postgresql://user:pass@host:5432/dbname

# Discord
DISCORD_TOKEN=      # bot token de Discord Developer Portal
DISCORD_CLIENT_ID=  # application ID

# Servicios externos
ANTHROPIC_API_KEY=
```

### Validación de env al arrancar (Zod)
```typescript
import { z } from 'zod';

const envSchema = z.object({
  JWT_SECRET:    z.string().min(32),
  DATABASE_URL:  z.string().url(),
  DISCORD_TOKEN: z.string().min(50),
  NODE_ENV:      z.enum(['development', 'production', 'test']),
});

export const env = envSchema.parse(process.env);
// Si falta alguna variable, la app falla en startup — nunca en runtime
```

**Regla:** La app debe fallar en el arranque si falta una variable de entorno crítica. Fallar silenciosamente en runtime es peor.

---

## 🛡️ OWASP Top 10 — los que más afectan mi stack

### 1. Injection (SQL, Command)
```typescript
// ❌ Vulnerable a SQL injection
const query = `SELECT * FROM users WHERE id = ${userId}`;

// ✅ Parámetros siempre — Drizzle lo hace automáticamente
const user = await db.select().from(usersTable).where(eq(usersTable.id, userId));
```

### 2. Broken Authentication
- Access tokens cortos (15 min)
- Refresh tokens con rotación (1 uso y se invalida)
- Rate limiting en endpoints de login (max 5 intentos / 15 min)
- Invalidar tokens al hacer logout — no solo borrar la cookie

### 3. Sensitive Data Exposure
```typescript
// ❌ Devolver objeto completo de DB
return user; // incluye hashedPassword, secretFields...

// ✅ Serializar explícitamente
return {
  id:        user.id,
  username:  user.username,
  role:      user.role,
  createdAt: user.createdAt,
  // hashedPassword: NUNCA
};
```

### 4. Security Misconfiguration
```typescript
// CORS — no usar * en producción
app.use(cors({
  origin: process.env.NODE_ENV === 'production'
    ? ['https://mi-dominio.com']
    : '*',
}));

// Headers de seguridad (usar helmet en Express/Fastify)
app.use(helmet());
```

### 5. Rate Limiting
```typescript
import rateLimit from 'express-rate-limit';

// General API
const apiLimiter = rateLimit({ windowMs: 15 * 60 * 1000, max: 100 });

// Endpoints sensibles (login, registro)
const authLimiter = rateLimit({ windowMs: 15 * 60 * 1000, max: 5 });

app.use('/api/', apiLimiter);
app.use('/api/auth/', authLimiter);
```

---

## 🤖 Seguridad específica de Discord Bots

### Verificar que el comando viene del guild correcto
```typescript
// Un bot multi-guild nunca debe ejecutar datos de un guild en otro
if (interaction.guildId !== expectedGuildId) {
  return interaction.reply({ content: 'Comando no disponible aquí.', ephemeral: true });
}
```

### Ephemeral replies para datos sensibles
```typescript
// Respuestas con datos del usuario solo visibles para él
await interaction.reply({
  content: `Tu saldo: ${user.coins} coins`,
  ephemeral: true  // solo el usuario lo ve
});
```

### Validar permisos de Discord además de los del bot
```typescript
// No asumir que si el usuario está en el server tiene permisos
const member = await interaction.guild.members.fetch(interaction.user.id);
if (!member.permissions.has(PermissionFlagsBits.Administrator)) {
  return interaction.reply({ content: 'Sin permisos.', ephemeral: true });
}
```

---

## 🔍 Code Review de Seguridad — checklist

Antes de mergear cualquier PR que toque auth, DB o APIs externas:

```
□ ¿Hay inputs del usuario sin validar (Zod/joi)?
□ ¿Hay queries construidas con strings concatenados?
□ ¿Hay secrets hardcodeados (incluso en comentarios)?
□ ¿Los errores exponen información interna (stack traces en producción)?
□ ¿Los endpoints sensibles tienen rate limiting?
□ ¿Los endpoints tienen autorización, no solo autenticación?
□ ¿Las respuestas serializan solo los campos necesarios?
□ ¿El console.log en desarrollo podría filtrar datos en producción?
```

---

## 🚫 Prohibiciones de Seguridad

- ❌ **Nunca `JWT_SECRET = 'secret'` o similar** — mínimo 32 chars aleatorios
- ❌ **Nunca secretos en el código fuente**, ni en comentarios, ni en el historial de git
- ❌ **Nunca `console.log(user)` en producción** — usa logging estructurado con campos controlados
- ❌ **Nunca confiar en datos del cliente** — validar y sanitizar siempre server-side
- ❌ **Nunca CORS `*` en producción** — whitelist explícita de origins
- ❌ **Nunca ignorar errores de JWT** — un catch vacío en verificación = brecha de seguridad
- ❌ **Nunca responder `404` diferente de `403`** — no confirmes si el recurso existe cuando no tiene permisos

---

## 🛠️ Herramientas del ecosistema

| Necesidad | Herramienta | Notas |
|-----------|-------------|-------|
| Hashing contraseñas | `bcrypt` | SALT_ROUNDS >= 12 |
| JWT | `jsonwebtoken` | access + refresh tokens |
| Validación de env | `zod` | falla en startup |
| Rate limiting (Express) | `express-rate-limit` | endpoints de auth más estrictos |
| Headers de seguridad | `helmet` | activar siempre en producción |
| Audit log automático en CI | `claude-code-security-review` | GitHub Action oficial Anthropic |

---

*Versión: 1.0 | 2026-04-03*
*Referencia — no refleja experiencia vivida todavía. Se actualiza cuando algo de esto se vive en un proyecto real.*