# DATABASE.md — Modelo de Referencia de Base de Datos

> **Propósito:** Documento de referencia, NO "así trabajo hoy".
> Este archivo describe cómo debería funcionar la capa de datos en proyectos profesionales.
> La IA lo consulta cuando diseña esquemas, migraciones o queries. Yo lo internalizo con cada proyecto real.
> Ver AGENTS.md para saber cómo trabajo actualmente.

---

## 🗺️ Cuándo usar qué DB

| Contexto | DB | ORM / Driver |
|----------|-----|-------------|
| App local / agentes / Electron | **SQLite** | Drizzle ORM |
| Backend web / producción / multiusuario | **PostgreSQL** | Drizzle ORM |
| Cache o sesiones efímeras | **Redis** | ioredis |
| Prototipo rápido sin infraestructura | **SQLite** en memoria | Drizzle ORM |

**Regla:** SQLite para lo que vive en el dispositivo del usuario. PostgreSQL para lo que vive en un servidor compartido. No mezcles sin una razón clara.

---

## 📐 Diseño de Esquema

### Convenciones de naming
```sql
-- Tablas: snake_case, plural
users, game_sessions, pending_prizes

-- Columnas: snake_case
user_id, created_at, is_active

-- PKs: siempre uuid o integer autoincrement según el contexto
-- UUID para datos que se sincronizan o exportan
-- Integer para datos puramente locales y de alto volumen
id UUID PRIMARY KEY DEFAULT gen_random_uuid()
id INTEGER PRIMARY KEY AUTOINCREMENT
```

### Columnas que van en casi toda tabla
```sql
id          -- PK siempre
created_at  -- timestamp de creación, siempre
updated_at  -- timestamp de última modificación
-- En tablas con borrado lógico:
deleted_at  -- NULL = activo, timestamp = borrado
```

### Tipos de datos que más se confunden
```
Dinero      → INTEGER (centavos), nunca FLOAT
Fechas      → TIMESTAMPTZ (con timezone) en Postgres, INTEGER (unix ms) en SQLite
Booleanos   → BOOLEAN en Postgres, INTEGER 0/1 en SQLite
JSON libre  → JSONB en Postgres (indexable), TEXT en SQLite (con cuidado)
Enums       → columna TEXT + CHECK constraint, nunca enum nativo en Postgres (difícil de migrar)
```

---

## 🔄 Migraciones

**Regla de oro:** La DB nunca se toca a mano en producción. Todo cambio es una migración versionada.

### Con Drizzle ORM
```bash
# Generar migración desde cambios en el schema
npx drizzle-kit generate

# Aplicar migraciones pendientes
npx drizzle-kit migrate

# Ver estado actual
npx drizzle-kit studio
```

### Estructura de carpetas
```
/src
  /db
    schema.ts          ← definición de tablas (fuente de verdad)
    index.ts           ← instancia del cliente
    /migrations
      0001_init.sql
      0002_add_scores.sql
      0003_add_streaks.sql
```

### Reglas de migración segura
1. **Nunca DROP sin respaldo.** Si hay que eliminar una columna, marcarla `deprecated` primero, migrar el código, luego DROP en la siguiente release.
2. **Migraciones idempotentes.** Un `CREATE TABLE IF NOT EXISTS` es mejor que un `CREATE TABLE` que explota si ya existe.
3. **Una cosa por migración.** No mezcles ADD COLUMN con CREATE TABLE en el mismo archivo.
4. **Siempre un `down` conceptual.** Aunque no lo implementes automáticamente, saber cómo deshacer la migración es requisito antes de mergear.

---

## 📊 Queries y Rendimiento

### Índices — cuándo crearlos
```sql
-- Sí: columnas que aparecen en WHERE frecuentemente
CREATE INDEX idx_users_discord_id ON users(discord_id);

-- Sí: columnas de foreign key (Postgres no las crea automáticamente)
CREATE INDEX idx_scores_user_id ON scores(user_id);

-- Sí: columnas usadas en ORDER BY en queries grandes
CREATE INDEX idx_sessions_created_at ON game_sessions(created_at DESC);

-- No: columnas con muy pocos valores únicos (true/false, status con 3 opciones)
-- El query planner prefiere un seq scan cuando la selectividad es baja
```

### N+1 — el error más común con ORMs
```typescript
// ❌ Mal: N+1 — una query por usuario
const users = await db.select().from(usersTable);
for (const user of users) {
  const scores = await db.select().from(scoresTable).where(eq(scoresTable.userId, user.id));
}

// ✅ Bien: un solo join
const usersWithScores = await db
  .select()
  .from(usersTable)
  .leftJoin(scoresTable, eq(usersTable.id, scoresTable.userId));
```

### Transacciones — cuándo son obligatorias
Si dos o más escrituras deben ser atómicas (o todas pasan o ninguna), es una transacción. Sin excepción.

```typescript
// Ejemplo: crear sesión y registrar auditoría al mismo tiempo
await db.transaction(async (tx) => {
  const [session] = await tx.insert(sessionsTable).values(newSession).returning();
  await tx.insert(auditLogTable).values({ action: 'game_started', sessionId: session.id });
});
```

---

## 🌱 Seeds y Datos de Prueba

```typescript
// /src/db/seeds/dev.ts
// Solo corre en NODE_ENV=development
export async function seedDevData(db: Database) {
  await db.insert(usersTable).values([
    { discordId: 'test_player_001', guildId: 'test_guild', coins: 500 },
    { discordId: 'test_admin_001',  guildId: 'test_guild', coins: 0   },
  ]).onConflictDoNothing();
}
```

**Regla:** Los seeds de desarrollo nunca deben poder correr en producción. Siempre guardar bajo la condición `NODE_ENV !== 'production'` o en un script separado.

---

## 💾 Backups

### SQLite (local / Electron)
```typescript
// Backup antes de cada migración
import { copyFileSync } from 'fs';

function backupDb(dbPath: string) {
  const timestamp = new Date().toISOString().replace(/[:.]/g, '-');
  copyFileSync(dbPath, `${dbPath}.backup-${timestamp}`);
}
```

### PostgreSQL (producción)
```bash
# Backup manual
pg_dump DATABASE_URL > backup-$(date +%Y%m%d).sql

# Restore
psql DATABASE_URL < backup-20260101.sql
```

**Regla:** En producción, los backups son automáticos y probados. Un backup que nunca se restauró no es un backup, es una ilusión.

---

## 🚫 Prohibiciones de DB

- ❌ **Nunca `SELECT *` en producción** — especifica las columnas que necesitas
- ❌ **Nunca strings de SQL concatenados con inputs del usuario** — SQL injection
- ❌ **Nunca guardar contraseñas en texto plano** — bcrypt siempre
- ❌ **Nunca floats para dinero** — precisión decimal inexacta
- ❌ **Nunca migrar en caliente sin transacción** — una migración fallida a mitad deja la DB en estado inconsistente
- ❌ **Nunca un schema sin índice en las FKs** — queries lentas garantizadas

---

## 📋 Checklist antes de mergear cambios de schema

```
□ La migración tiene nombre descriptivo (0004_add_streak_system.sql)
□ Se probó la migración en DB local limpia
□ Se probó rollback conceptual (¿cómo se deshace si falla?)
□ Los índices necesarios están incluidos en la migración
□ El seed de dev fue actualizado si aplica
□ Los tests de integración corren con la nueva schema
```

---

*Versión: 1.0 | 2026-04-03*
*Referencia — no refleja experiencia vivida todavía. Se actualiza cuando algo de esto se vive en un proyecto real.*