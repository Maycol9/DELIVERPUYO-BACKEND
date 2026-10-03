# DeliverPuyo Backend

Backend REST de DeliverPuyo, implementado con Next.js y Node.js 22+, Prisma 7 y PostgreSQL. Redis se utiliza para caché; BullMQ procesa comprobantes mediante un worker opcional.

## Estructura

- `pages/api/`: rutas HTTP.
- `services/`, `middleware/`, `validations/`: lógica de negocio, autenticación y validación.
- `prisma/`: esquema, migraciones y scripts de seed.
- `database/`, `lib/`, `workers/`: clientes de infraestructura, utilidades y worker de comprobantes.

## Desarrollo local

1. Instala Node.js 22 o superior y configura PostgreSQL/Redis.
2. Copia `.env.example` a `.env` y completa valores locales ficticios/propios fuera de Git.
3. Ejecuta:

```sh
npm ci --include=dev
npm run generate
npx prisma migrate deploy
npm run dev
```

Para validar producción localmente:

```sh
npm run typecheck
npm run build
npm start
```

En Render, el build usa `npm ci --include=dev && npm run generate && npm run build`; el start usa `npm start`.

## Variables de entorno

Configura los valores fuera del repositorio. El backend requiere estos nombres:

- `DATABASE_URL`
- `REDIS_URL`
- `JWT_ACCESS_SECRET`
- `JWT_REFRESH_SECRET`
- `NODE_ENV`
- `QUEUE_ENABLED`
- `ENABLE_BENCHMARK_ENDPOINTS`

`CORS_ALLOWED_ORIGINS` es adicional cuando se consume desde Flutter Web. Flutter móvil nativo no requiere CORS.

## API en producción

Base URL: https://deliverpuyo-backend.onrender.com

Las rutas principales incluyen autenticación, categorías, productos, direcciones y pedidos. Consulta `docs/CONTRATO_API.md` para el contrato documentado.

## Seeds

`npm run seed` contiene datos de demostración y exige variables `SEED_ADMIN_EMAIL`, `SEED_ADMIN_PASSWORD`, `SEED_CLIENT_EMAIL` y `SEED_CLIENT_PASSWORD`; no lo ejecutes en producción. `npm run seed:catalog` carga únicamente categorías y productos, preserva los registros existentes y requiere revisión/autorización antes de apuntar a una base real.

Los comprobantes requieren ejecutar `npm run worker:receipt` como proceso separado con Redis disponible. Los archivos generados son locales y no deben tratarse como almacenamiento durable.
