# Documentación de la API

## Base y convenciones

- Base de negocio: `/api/v1`; protocolo HTTP con JSON.
- Salud: `GET /health` (fuera de `/api/v1`).
- Autenticación: JWT `Bearer`, salvo login y salud.
- Roles: `ADMIN` y `VENDEDOR`. Una ruta sin `permitir(...)` acepta cualquier usuario autenticado; no implica que todas las acciones estén limitadas al propietario.
- Errores de aplicación: JSON `{ "error": "mensaje" }`. Errores habituales: 400 validación/JSON, 401 token o credenciales, 403 rol/permisos, 404 recurso/ruta inexistente, 409 conflicto de estado, 410 premio caducado, 500 error inesperado.
- Los datos son en memoria y se pierden al reiniciar.

La especificación detallada (incluye esquemas de entrada, parámetros y respuestas) está en [openapi.yaml](openapi.yaml). No hay Swagger UI instalado ni configurado en la aplicación.

## Endpoints

### Salud y autenticación

| Método y ruta | Descripción / acceso | Parámetros y entrada | Respuesta y errores |
|---|---|---|---|
| `GET /health` | Estado HTTP básico del proceso; público. | Sin parámetros ni body. | 200 `{estado:"ok", hora:<ISO>}`. |
| `POST /api/v1/auth/login` | Iniciar sesión; público. | JSON `{usuario:string, password:string}`. | 200 `{token, usuario:{id,usuario,nombre,rol}}`; 400 datos faltantes; 401 credenciales inválidas. |
| `GET /api/v1/auth/perfil` | Consultar perfil; Bearer requerido. | Header Authorization. | 200 `{id,usuario,nombre,rol}`; 401 token requerido/inválido/expirado o usuario no válido. |

### Sorteos

| Método y ruta | Descripción / acceso | Parámetros y entrada | Respuesta y errores |
|---|---|---|---|
| `GET /api/v1/sorteos` | Listar sorteos; cualquier usuario autenticado. | Query opcionales `tipo` (`CHANCE`/`BALOTO`) y `estado`; sin body. | 200 arreglo; 401 sin token. |
| `GET /api/v1/sorteos/{id}` | Obtener sorteo; autenticado. | Path `id`; sin body. | 200 sorteo; 404 no encontrado; 401 sin token. |
| `POST /api/v1/sorteos` | Crear sorteo; ADMIN. | JSON `{tipo,nombre,fecha,acumulado?}`; fecha ISO parseable y futura. | 201 sorteo `ABIERTO`; 400 entrada inválida; 401/403 autenticación/rol. |
| `POST /api/v1/sorteos/{id}/resultado` | Registrar resultado; ADMIN. | Path `id`; JSON `{resultado}`: CHANCE cadena de 4 dígitos; BALOTO objeto de jugada. | 200 sorteo jugado/liquidado; 400 resultado inválido; 404 no encontrado; 409 ya jugado; 401/403. |

### Tiquetes

| Método y ruta | Descripción / acceso | Parámetros y entrada | Respuesta y errores |
|---|---|---|---|
| `POST /api/v1/tickets/chance` | Vender chance; VENDEDOR. | `{sorteoId, apuestas:[{numero, modalidad?, valor}], cliente?}`; 1–5 apuestas. | 201 tiquete; 400 apuesta inválida; 404 sorteo inexistente; 409 tipo/estado/cierre; 401/403. |
| `POST /api/v1/tickets/baloto` | Vender Baloto; VENDEDOR. | `{sorteoId, jugadas:[{numeros,superbalota}|{aleatoria:true}], cliente?}`; 1–10 jugadas. | 201 tiquete; 400 jugada inválida; 404 sorteo inexistente; 409 tipo/estado/cierre; 401/403. |
| `GET /api/v1/tickets` | Listar tiquetes; autenticado. ADMIN ve todos; vendedor ve los propios. | Query opcionales `fecha` (prefijo de fecha ISO) y `estado`. | 200 arreglo; 401 sin token. |
| `GET /api/v1/tickets/{codigo}` | Consultar por código; autenticado. La implementación no verifica propiedad para esta consulta. | Path `codigo`. | 200 tiquete más resumen de sorteo y `ganador`; 404 inexistente; 401 sin token. |
| `POST /api/v1/tickets/{codigo}/anular` | Anular; autenticado, propietario o ADMIN según lógica del servicio. | Path `codigo`; JSON `{motivo:string}`. | 200 tiquete anulado; 400 falta motivo; 403 vendedor ajeno; 404 inexistente; 409 estado/jugado/fuera de ventana; 401. |
| `POST /api/v1/tickets/{codigo}/pagar` | Pagar premio; VENDEDOR. | Path `codigo`; sin body. | 200 registro de pago; 404 inexistente; 409 sorteo sin jugar/tiquete no premiado o no activo/pagado; 410 premio caducado; 401/403. |

### Caja

| Método y ruta | Descripción / acceso | Parámetros y entrada | Respuesta y errores |
|---|---|---|---|
| `GET /api/v1/caja/resumen` | Resumen diario; autenticado. Vendedor ve su caja; ADMIN puede indicar `vendedorId`. | Query opcionales `fecha` (predeterminada según zona horaria) y `vendedorId`. | 200 con ventas, anulaciones, pagos, saldo y `cerrada`; 401 sin token. |
| `POST /api/v1/caja/cierre` | Cerrar caja; VENDEDOR. | JSON `{efectivoReportado:integer, fecha?:string}`. | 201 cierre con diferencia/estado; 400 efectivo inválido; 409 ya cerrada; 401/403. |

## Reglas que afectan las respuestas

- Chance: 1–4 dígitos; modalidades `DIRECTO` o `COMBINADO`; apuesta entre `APUESTA_MIN_CHANCE` y `APUESTA_MAX_CHANCE`, múltiplo de 100. Combinado solo para 3–4 cifras con permutaciones distintas.
- Baloto: cinco números únicos de 1–43 y superbalota de 1–16. Precio por jugada configurable (`VALOR_BALOTO`).
- Las ventas se rechazan dentro de `MINUTOS_CIERRE_VENTAS` minutos previos al sorteo.
- Anulación de vendedor permitida dentro de `MINUTOS_ANULACION`; ADMIN no tiene ese límite temporal en la lógica del servicio.
- Caducidad: `DIAS_CADUCIDAD_PREMIO` desde `jugadoEn`.
- La autenticación JWT toma secreto/expiración de configuración; actualmente existe un secreto de reserva en código. Véase [REQUISITOS_NO_FUNCIONALES.md](REQUISITOS_NO_FUNCIONALES.md).
