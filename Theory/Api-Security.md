# Roadmap.sh

## Api-security

Authentication: avoid basic auth use standart, do not reinvent the wheel in authentication mechanism, use max rety and jail features in login, use encryption on all sensivity data
- json web token: good jwt secret to make brute force attacks dificult, do not extract the algorithm from the header, use backend, make token expiration ttl rttl as short as possible, avoid storing sensitive data in jwt payload, keep the payload small to reduce the size of the jwt
- Access control: limit request trholting to avoid ddos/burute force, use https on server side and secure ciphers, use hsts header with ssl to avoid ssl strip attacks, turn off directory listings, private apis should only be accesible from safe-listed ips
- Oauth: Always validate redirect_uri on server-side, avoid response_type=token and try to exhange for code, use state parameter to prevent csrf attacks, have default scope, and validate scope for each application
-input: limit request throlting to avoid ddos / brute force use https on server side and secure ciphers, use hsts header with ssl to avoid ssl strip attacks, trn off directory listings, privite apis should only be accessible from safe listed ips
-processing: check if all endpints are protected behind autentication to avoid broken autehntication process, avoid users perosnal id in resource urls, prefer using uuid over auto-increment ids, disable entity parsing if parsing xml to avoid xxe attacks, disable entity expansing if using xml, yaml, or similar, use a cdn for file uploads, avoid http blockig wen handling large ammounts of data, make sure debug mode is off in production, use non executable stacks when avaible.
-output: send x content type options nonniff header, send x frame options deny header, send content secrity policity default src none header, remove fingerprint headers, force content type for you response, avoid returning sensitive data, return proper response codes as per the operation
-ci/cd: audit your desing and implementation with unit/integraiton test, use a code review process and disregard self approval, continuously run security analysis on your code, check your dependencies for know vulnerailites, design a rollback solution for deployments
-monitoring: use centralized logings for all services and components, use agents to monitor all reqest responses and errors, use alerts for sms slack email kibana cloud wahtch etc, ensure ou arent logging any sensitive data, use an ids and or ips s ystem for monitor verything
