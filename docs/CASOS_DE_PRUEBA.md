# Casos de prueba y calidad

## Estado de ejecución

En el repositorio existen 20 pruebas Jest con los nombres indicados. Tras restaurar dependencias con `npm ci`, `npm test` finalizó correctamente: 5 suites y 20 pruebas aprobadas. Los casos TC-001–TC-020 se registran como **Aprobado en esta ejecución**. Los casos TC-021–TC-030 son propuestas manuales/automatizables y están **Pendiente de prueba**.

La tabla de pasos agrupa precondición/datos con el procedimiento para mantener legible la matriz; cada caso incluye resultado esperado explícito.

| ID | HU / RF | Objetivo, precondiciones y datos | Pasos | Resultado esperado | Estado |
|---|---|---|---|---|---|
| TC-001 | HU-001 / RF-001 | Iniciar con vendedor seed activo; `vendedor1` y contraseña seed correcta. | POST `/auth/login`. | HTTP 200, token y rol VENDEDOR; no expone password. | Implementado (`auth.test.js`), ejecución no verificada |
| TC-002 | HU-001 / RF-001 | Credencial incorrecta para usuario existente. | POST login con password errónea. | HTTP 401. | Implementado (`auth.test.js`), ejecución no verificada |
| TC-003 | HU-001 / RF-018 | No se envía token. | GET `/sorteos`. | HTTP 401. | Implementado (`auth.test.js`), ejecución no verificada |
| TC-004 | HU-004 / RF-007 | Apuesta chance válida de cuatro cifras, valor 1000; resultado igual/diferente. | Llamar `validarApuesta` y `calcularPremio` para ambos resultados. | Premio directo 4 cifras según factor; cero si no coincide. | Implementado (`chance.test.js`), ejecución no verificada |
| TC-005 | HU-004 / RF-007 | Apuestas de dos y tres cifras; resultado `1234`. | Calcular premios con sufijos coincidentes. | Se reconocen últimas cifras y se aplica factor correspondiente. | Implementado (`chance.test.js`), ejecución no verificada |
| TC-006 | HU-004 / RF-007 | Chance combinado de tres cifras `123`, valor 600. | Calcular frente a resultado con permutación ganadora. | Premio dividido por cantidad de permutaciones. | Implementado (`chance.test.js`), ejecución no verificada |
| TC-007 | HU-004 / RF-007 | Número no numérico, valor bajo/no múltiplo de 100 y combinado de dos cifras. | Invocar validador para cada dato inválido. | Cada entrada lanza error de validación. | Implementado (`chance.test.js`), ejecución no verificada |
| TC-008 | HU-005 / RF-009 | Jugada con `aleatoria:true`. | Generar jugada. | Cinco números únicos ordenados entre 1 y 43; superbalota 1–16. | Implementado (`baloto.test.js`), ejecución no verificada |
| TC-009 | HU-005 / RF-009 | Números repetidos y número 44. | Validar ambas jugadas. | El validador rechaza ambas. | Implementado (`baloto.test.js`), ejecución no verificada |
| TC-010 | HU-005 / RF-009 | Resultado y jugadas con categorías de aciertos distintas. | Calcular premio para premio mayor, categoría secundaria y sin premio. | Valores de premio acordes con tabla; 0 sin coincidencia premiada. | Implementado (`baloto.test.js`), ejecución no verificada |
| TC-011 | HU-009 / RF-015 | Vendedor vende una apuesta chance 5000 y una jugada Baloto 5700 en fecha actual. | GET `/caja/resumen` como vendedor. | 200, dos tiquetes, ventas por tipo y saldo esperado 10700. | Implementado (`caja.test.js`), ejecución no verificada |
| TC-012 | HU-010 / RF-016 | Venta de 5000 y efectivo reportado 4000; después se intenta cerrar otra vez. | POST cierre dos veces como vendedor. | Primer cierre 201, faltante -1000; segundo 409. | Implementado (`caja.test.js`), ejecución no verificada |
| TC-013 | HU-003 / RF-005 | Token ADMIN; sorteo futuro CHANCE con nombre y fecha válidos. | POST `/sorteos`. | 201 y estado ABIERTO. | Implementado (`caja.test.js`), ejecución no verificada |
| TC-014 | HU-004 / RF-008 | Vendedor y sorteo CHANCE seed abierto; dos apuestas válidas. | POST `/tickets/chance`. | 201, código `CH-`, total suma de apuestas. | Implementado (`tickets.test.js`), ejecución no verificada |
| TC-015 | HU-005 / RF-010 | Vendedor y sorteo BALOTO abierto; una jugada manual y otra aleatoria. | POST `/tickets/baloto`. | 201, total de dos jugadas a precio configurado. | Implementado (`tickets.test.js`), ejecución no verificada |
| TC-016 | HU-004 / RF-008 | Mover fecha de sorteo a 5 minutos desde ahora, menor que margen configurado. | Vender chance. | HTTP 409, venta rechazada por cierre. | Implementado (`tickets.test.js`), ejecución no verificada |
| TC-017 | HU-004 / RF-008 / RF-018 | Token ADMIN y solicitud de venta. | POST venta chance como ADMIN. | HTTP 403. | Implementado (`tickets.test.js`), ejecución no verificada |
| TC-018 | HU-007 / RF-013 | Vendedor A vende; vendedor B intenta anular, luego A con motivo. | POST anulación con ambos tokens. | Ajeno 403; propietario 200 y estado ANULADO. | Implementado (`tickets.test.js`), ejecución no verificada |
| TC-019 | HU-007 / RF-013 | Tiquete activo de vendedor con antigüedad de 60 minutos. | POST anulación con motivo. | HTTP 409 fuera de la ventana permitida. | Implementado (`tickets.test.js`), ejecución no verificada |
| TC-020 | HU-003, HU-006, HU-008 / RF-006, RF-012, RF-014 | Venta ganadora, admin registra resultado; vendedor consulta y paga. | Registrar resultado, consultar código, pagar y repetir pago. | Consulta indica ganador y premio; pago 200; duplicado 409. | Implementado (`tickets.test.js`), ejecución no verificada |
| TC-021 | HU-001 / RF-002, RF-018 | Token válido de usuario activo; repetir con token inválido/expirado. | GET `/auth/perfil` con tokens. | Perfil sin contraseña con token válido; 401 con inválido/expirado. | Propuesto; pendiente de prueba |
| TC-022 | HU-002 / RF-003, RF-004 | Token válido, sorteos seed de ambos tipos y estado ABIERTO. | Listar sin filtros, filtrar por tipo/estado, pedir id inexistente. | Colecciones correctas; detalle inexistente 404. | Propuesto; pendiente de prueba |
| TC-023 | HU-003 / RF-005, RF-006, RF-009 | Probar fecha pasada/tipo inválido; sorteo BALOTO y resultado inválido; repetir resultado. | Ejecutar POST de creación/resultado con casos válidos e inválidos y rol no ADMIN. | 400 validación; 403 rol; 200 primer resultado válido; 409 repetición; tiquetes liquidados según resultado. | Propuesto; pendiente de prueba |
| TC-024 | HU-006 / RF-011 | Dos vendedores con tiquetes; token ADMIN; filtros fecha y estado. | GET `/tickets` con cada rol/filtro. | Vendedor ve propios; ADMIN todos; filtros restringen resultado. | Propuesto; pendiente de prueba |
| TC-025 | HU-006 / RF-012 | Código de tiquete inexistente y existente antes del sorteo. | GET `/tickets/{codigo}`. | 404 inexistente; existente informa `ganador:null` si sorteo no jugado. | Propuesto; pendiente de prueba |
| TC-026 | HU-007 / RF-013 | Tiquete activo; probar motivo vacío, sorteo jugado y rol ADMIN. | POST anular con cada combinación. | Motivo faltante 400; jugado 409; ADMIN autorizado solo si tiquete anulable y motivo presente. | Propuesto; pendiente de prueba |
| TC-027 | HU-008 / RF-014 | Tiquete no ganador, premio caducado y token ADMIN. | Intentar pagar en cada escenario. | 409 sin premio; 410 caducado y estado CADUCADO; ADMIN recibe 403 por política de ruta. | Propuesto; pendiente de prueba |
| TC-028 | HU-009 / RF-015 | Cajas de dos vendedores; fecha y rol ADMIN. | Solicitar resumen propio, como ADMIN con y sin `vendedorId`. | ADMIN puede seleccionar vendedor; vendedor permanece limitado al suyo; sumas coinciden con datos sembrados. | Propuesto; pendiente de prueba |
| TC-029 | HU-010 / RF-016 | Efectivo negativo/decimal; cierre con saldo exacto y excedente. | POST cierre con cada importe y revisar estados. | Entrada inválida 400; diferencias clasificadas como CUADRADA/SOBRANTE; no crear cierre inválido. | Propuesto; pendiente de prueba |
| TC-030 | HU-011 / RF-017 | Servicio iniciado; ruta health pública. | GET `/health`. | 200, `estado:"ok"` y `hora` ISO parseable. No inferir disponibilidad de dependencias. | Propuesto; pendiente de prueba |

## Cobertura pendiente

Las pruebas actuales cubren reglas de chance/Baloto, login, algunas operaciones de ventas, cierre, anulación y pago. No se encontraron pruebas para perfil, consulta/listado de sorteos, filtros/listado de tiquetes, health, manejo global de errores, varios rechazos de validación, autorización de cada ruta ni concurrencia. Priorizar TC-021–TC-030 antes de declarar cobertura funcional completa.
