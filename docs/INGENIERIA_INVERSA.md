# Informe de ingeniería inversa — Punto 3.4

## Propósito y alcance

Se reconstruyó la documentación funcional desde los archivos existentes del repositorio. La evidencia principal son rutas HTTP, middleware, controladores, servicios, configuración, almacén inicial y pruebas. Las historias de usuario y requisitos son una interpretación académica posterior a la implementación, no documentos originales del sistema. No se agregaron capacidades al código de negocio.

## Inventario analizado

- Entrada/ejecución/configuración: `package.json`, `src/app.js`, `src/server.js`, `src/config/index.js`.
- Rutas/controladores: `src/routes/index.js`, los cuatro archivos bajo `src/controllers/`.
- Dominio: `src/services/authService.js`, `balotoService.js`, `cajaService.js`, `chanceService.js`, `sorteoService.js`, `ticketService.js`.
- Estado/error/utilidades: `src/data/store.js`, `src/middlewares/auth.js`, `src/middlewares/errorHandler.js`, `src/utils/AppError.js`, `src/utils/fechas.js`.
- Pruebas: `tests/auth.test.js`, `baloto.test.js`, `caja.test.js`, `chance.test.js`, `tickets.test.js`, `helpers.js`.
- Gestión de dependencias: `package-lock.json`.

No se encontró base de datos configurada: `src/data/store.js` conserva usuarios, sorteos, tiquetes, pagos y cierres en memoria; el seed ocurre al cargar y `reset()` reinicia datos. No se encontraron README, `.env`, `.env.example`, documentación, especificación OpenAPI/Swagger, licencia, CI ni configuración de lint en el inventario inicial.

## Método

1. Se inventariaron carpetas y archivos rastreados.
2. Se inspeccionaron rutas para registrar métodos, paths y middleware.
3. Se siguió cada controlador hasta el servicio y almacén para extraer entradas, reglas, efectos y errores.
4. Se contrastaron reglas y flujos con los nombres y aserciones de las pruebas Jest.
5. Se intentó ejecutar la suite, se restauraron dependencias faltantes desde `package-lock.json` y se ejecutaron los tests; los casos adicionales se separaron como propuestas.
6. Se creó un OpenAPI 3.0 YAML, sin instalar dependencias ni exponer Swagger UI, dado que no había tecnología Swagger previa.

## Hallazgos arquitectónicos y funcionales

- API Node.js/Express bajo `/api/v1`; el único endpoint fuera de ese prefijo es `/health`.
- 14 rutas de negocio, con JWT Bearer en rutas protegidas y controles de rol ADMIN/VENDEDOR en rutas específicas.
- Dominios: autenticación, sorteos CHANCE/BALOTO, reglas de apuestas, tiquetes, liquidación/pagos y caja.
- Estado completamente en memoria; reiniciar el proceso pierde operaciones.
- Los datos iniciales incluyen tres usuarios de demostración y tres sorteos futuros.
- No existe API de alta de usuarios ni persistencia en base de datos identificada.
- Existe una clave JWT de reserva por defecto en código; hay que reemplazarla antes de cualquier despliegue. Los usuarios/contraseñas seed son demostrativos e inseguros.
- Hay 20 pruebas unitarias/de integración por nombre en el repositorio; tras `npm ci`, `npm test` aprobó las 5 suites y 20 pruebas en Node.js 24.16.0.

## Trazabilidad del resultado

- 11 historias: HU-001–HU-011.
- 18 requisitos funcionales: RF-001–RF-018.
- 8 requisitos no funcionales: RNF-001–RNF-008, clasificados según categorías solicitadas y con evidencia/estado explícitos.
- 15 endpoints documentados: 14 rutas de `src/routes/index.js` y `GET /health`.
- 8 funciones críticas con JSDoc añadidas al servicio: autenticación, validación chance, validación Baloto, creación de sorteo, registro/liquidación de resultado, venta de chance, pago de premio y resumen de caja.
- 30 casos de prueba: 20 existentes aprobados en la ejecución y 10 propuestos pendientes.

El detalle identificador a identificador está en [MATRIZ_TRAZABILIDAD_RTM.md](MATRIZ_TRAZABILIDAD_RTM.md); contratos en [DOCUMENTACION_API.md](DOCUMENTACION_API.md) y [openapi.yaml](openapi.yaml).

## Limitaciones y asuntos pendientes

1. Automatizar los casos TC-021–TC-030 y ampliar cobertura de permisos, errores, filtros, validaciones y pagos/caducidad.
2. `npm ci` reportó 8 vulnerabilidades en dependencias (6 low, 2 moderate); no se aplicó `npm audit fix` para evitar cambios fuera del alcance.
3. Decidir e implementar persistencia duradera si se requiere conservar información; hoy el almacenamiento es volátil.
4. Retirar secretos/credenciales seed de producción, exigir secreto JWT externo, configurar HTTPS y reforzar protección del login.
5. Definir versiones de Node y licencia del proyecto; no hay evidencia para declararlas.
6. Revisar autorización de lectura de tiquetes: el middleware solo exige autenticación y `consultar(codigo)` no comprueba propiedad; se documentó tal cual, sin cambiar el comportamiento.
7. El OpenAPI se preparó a partir del código pero no se validó con una herramienta específica de lint/generación; no hay interfaz Swagger activa.

## Recomendación QA

Tratar la documentación y la especificación como baseline reconstruido sujeto a revisión del responsable del producto. Antes de despliegue, confirmar reglas comerciales (tabla de premios, permisos, ventanas de tiempo, precisión de fechas) y resolver las limitaciones de seguridad/persistencia descritas, sin confundirlas con el comportamiento actual.
