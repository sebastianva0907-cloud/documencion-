# Puesto de Chance API

[![Node.js](https://img.shields.io/badge/Node.js-CommonJS-339933?logo=node.js&logoColor=white)](https://nodejs.org/)
[![Express](https://img.shields.io/badge/Express-4.x-000000?logo=express&logoColor=white)](https://expressjs.com/)
[![Jest](https://img.shields.io/badge/tests-Jest-C21325?logo=jest&logoColor=white)](https://jestjs.io/)
[![OpenAPI](https://img.shields.io/badge/API-OpenAPI%203.0-6BA539?logo=swagger&logoColor=white)](https://www.openapis.org/)

## Descripción y objetivo

API REST para una aplicación de punto de venta de chance y Baloto. La implementación permite autenticar administradores y vendedores, consultar y crear sorteos, vender y administrar tiquetes, liquidar resultados, pagar premios y consultar/cerrar la caja diaria.

Esta documentación reconstruye el comportamiento observable del código fuente; no representa requisitos originales externos. El almacenamiento actual es en memoria: los datos se reinician al reiniciar el proceso y no se utiliza una base de datos.

## Tecnologías

- Node.js y JavaScript (CommonJS)
- Express 4, CORS y Morgan
- JWT (`jsonwebtoken`) y hash de contraseñas (`bcryptjs`)
- Configuración mediante `dotenv`
- Jest y Supertest para pruebas

## Arquitectura

```text
Cliente HTTP
  └─ Express (src/app.js; /api/v1)
      ├─ rutas y middleware de autenticación/roles
      ├─ controladores HTTP
      ├─ servicios de dominio (sorteos, chance, Baloto, tiquetes, caja, auth)
      ├─ almacenamiento en memoria (src/data/store.js)
      └─ middleware de errores
```

Los controladores reciben y responden solicitudes; los servicios validan reglas de negocio y actualizan el almacenamiento en memoria. No hay persistencia, migraciones ni motor de base de datos configurados.

## Estructura

```text
src/
  app.js, server.js
  config/                 Variables de entorno y valores por defecto
  controllers/            Adaptadores HTTP
  data/                   Almacén en memoria y datos iniciales
  middlewares/            Autenticación, autorización y errores
  routes/                 Rutas /api/v1
  services/               Reglas de negocio
  utils/                  Errores y fechas
tests/                    Jest/Supertest
docs/                     Requisitos, trazabilidad, QA y OpenAPI
```

## Requisitos previos

- Node.js y npm instalados. El repositorio no fija una versión mínima/máxima de Node (`engines` no está definido en `package.json`).
- Git (para clonar el repositorio).

## Instalación y configuración

Desde la raíz del repositorio:

```powershell
npm ci
Copy-Item .env.example .env
```

Edita `.env` antes de usar el servicio fuera de pruebas/desarrollo. Define `JWT_SECRET` como un valor aleatorio privado y suficientemente largo; el valor de ejemplo es solo un marcador, no una credencial válida para producción. No publiques `.env`.

### Variables de entorno

| Variable | Valor por defecto en código | Uso |
|---|---:|---|
| `PORT` | `3000` | Puerto HTTP |
| `JWT_SECRET` | `cambiar-este-secreto` | Secreto de firma JWT; reemplazar antes de desplegar |
| `JWT_EXPIRES_IN` | `8h` | Vigencia del token |
| `ZONA_HORARIA` | `America/Bogota` | Zona para fechas de caja |
| `MINUTOS_ANULACION` | `10` | Ventana de anulación para vendedor |
| `MINUTOS_CIERRE_VENTAS` | `15` | Minutos antes del sorteo en que se bloquean ventas |
| `APUESTA_MIN_CHANCE` | `500` | Valor mínimo de una apuesta |
| `APUESTA_MAX_CHANCE` | `50000` | Valor máximo de una apuesta |
| `VALOR_BALOTO` | `5700` | Precio por jugada |
| `DIAS_CADUCIDAD_PREMIO` | `365` | Vigencia de cobro desde el sorteo |

El almacén incluye usuarios iniciales codificados en `src/data/store.js` (`admin`, `vendedor1`, `vendedor2`) con contraseñas de demostración en texto fuente que se hashean durante el seed. Son datos inseguros de desarrollo, no un mecanismo de alta de usuarios ni credenciales para despliegue. La aplicación no tiene persistencia duradera.

## Ejecución

```powershell
npm start
```

Modo de desarrollo con reinicio automático:

```powershell
npm run dev
```

La API escucha en `http://localhost:3000` por defecto. El endpoint de salud es `GET /health`. Las operaciones de negocio usan el prefijo `/api/v1`.

## Documentación de la API

- [Especificación OpenAPI 3.0](docs/openapi.yaml)
- [Guía de endpoints y reglas](docs/DOCUMENTACION_API.md)
- No se encontró una interfaz Swagger UI ni un generador OpenAPI instalado/configurado. El contrato se entrega como archivo OpenAPI YAML, sin agregar dependencias ni rutas a la aplicación.
- Las rutas protegidas requieren `Authorization: Bearer <token>` obtenido con `POST /api/v1/auth/login`. Las restricciones por rol se detallan en OpenAPI.

## Pruebas

```powershell
npm test
```

Las pruebas existentes usan Jest y Supertest bajo `tests/`. Tras restaurar dependencias con `npm ci`, `npm test` finalizó correctamente: 5 suites y 20 pruebas aprobadas en este entorno. Esto no implica cobertura de todos los flujos; ver [casos de prueba y estado QA](docs/CASOS_DE_PRUEBA.md) para los escenarios pendientes.

## Autores

No se identificaron autores en los archivos del repositorio. Repositorio indicado para este proyecto: [crgonzalez3212/documentacionApi](https://github.com/crgonzalez3212/documentacionApi).

## Licencia

No se identificó un archivo o declaración de licencia en el repositorio.
