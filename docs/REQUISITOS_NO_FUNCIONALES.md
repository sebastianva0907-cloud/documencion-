# Requisitos no funcionales y evidencia

## Escala de evidencia

- **Implementado y comprobado:** hay implementación y evidencia de ejecución de prueba en este análisis.
- **Implementado; pendiente de pruebas:** se observa implementación en código, pero no se verificó su operación con una prueba ejecutada.
- **No identificado:** no se encontró mecanismo/evidencia en el repositorio.
- **Recomendación:** mejora propuesta, no comportamiento actual ni compromiso del sistema.

En el primer intento `npm test` no pudo iniciar porque Jest no estaba instalado. Tras restaurar dependencias con `npm ci`, se ejecutaron 5 suites y 20 pruebas aprobadas. Esto comprueba solo los escenarios cubiertos, no la ausencia general de defectos ni requisitos no medidos.

| ID | Categoría | Requisito/evidencia observada | Estado |
|---|---|---|---|
| RNF-001 | Seguridad | Las rutas usan JWT Bearer; se verifica firma y usuario activo; autorización ADMIN/VENDEDOR declarada por ruta. Contraseñas seed hasheadas con bcrypt. Sin embargo, existe secreto JWT por defecto y credenciales de demostración fijas en código; transporte HTTPS, rate limiting, gestión/rotación de secretos y alta segura de usuarios no están identificados. | Implementación comprobada parcialmente por pruebas de login/token/roles; seguridad integral no demostrada. Hallazgos a mitigar antes de despliegue. |
| RNF-002 | Rendimiento | No se identifican límites de carga, mediciones, benchmarks, caché ni paginación. Almacenamiento y filtros son en memoria y recorren colecciones. | No identificado. Recomendación: medir cargas reales y definir límites/paginación. |
| RNF-003 | Disponibilidad | `GET /health` responde estado y timestamp del proceso. No se identifican redundancia, recuperación, health checks de dependencias, métricas o SLA. | Implementado; pendiente de pruebas solo para respuesta básica; disponibilidad robusta no identificada. |
| RNF-004 | Usabilidad | Hay mensajes JSON de error en español y nombres de campos comprensibles; no existe interfaz de usuario ni guía para el cliente aparte de la documentación añadida. | Implementado; pendiente de pruebas para mensajes/contratos. La UX de interfaz no está identificada. |
| RNF-005 | Mantenibilidad | Separación de rutas, controladores, servicios, utilidades y middleware; uso de CommonJS; pruebas Jest/Supertest. No hay scripts de lint, formato ni CI visibles en los archivos rastreados. | Estructura observada y suite de pruebas ejecutada; lint/CI no identificados. |
| RNF-006 | Escalabilidad | Datos guardados en variables de proceso (`src/data/store.js`); no hay base de datos, persistencia compartida ni estrategia de escalado. El reinicio elimina datos y cada instancia tendría estado independiente. | No identificado como capacidad; limitación comprobable en implementación. Recomendación: persistencia transaccional antes de escalar. |
| RNF-007 | Compatibilidad | API HTTP JSON y dependencias Node declaradas en `package.json`; no se declara rango de Node (`engines`) ni matriz de plataformas/consumidores probados. | Suite ejecutada en Node.js 24.16.0; compatibilidad con otras versiones/plataformas no identificada. |
| RNF-008 | Integridad de datos | Servicios validan rangos/reglas, estados de sorteo/tiquete, duplicidad de cierre y doble pago; operaciones actualizan memoria sin transacción o persistencia duradera. | Implementado y comprobado parcialmente por pruebas de reglas, estados y doble pago/cierre. Integridad ante concurrencia, caída o múltiples instancias no identificada. |

## Recomendaciones diferenciadas (no requisitos implementados)

1. Eliminar el secreto JWT y las cuentas predefinidas de demostración en despliegues; exigir secretos externos fuertes.
2. Incorporar persistencia duradera, transacciones y restricciones únicas para pagos y cierres antes de uso real.
3. Añadir HTTPS en el despliegue, limitación de intentos de login, límites de petición, controles de privacidad y auditoría.
4. Definir versiones de Node compatibles, ejecutar CI y probar autorización/validación/errores en todos los endpoints.
