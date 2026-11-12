# Matriz de trazabilidad de requisitos (RTM)

La matriz vincula historias, RF, RNF y código comprobable. Se ejecutaron 5 suites y 20 pruebas existentes con éxito después de restaurar dependencias; los casos no ejecutados siguen identificados como propuestos.

| ID HU | Historia de usuario | ID RF | ID RNF | Módulo / Controlador | Archivo / Función | Caso de prueba | Estado QA |
|---|---|---|---|---|---|---|---|
| HU-001 | Autenticarse y consultar perfil | RF-001, RF-002, RF-018 | RNF-001, RNF-004, RNF-005, RNF-007 | Autenticación | `src/controllers/authController.js` `login`/`perfil`; `src/services/authService.js` `login`/`perfil`; `src/middlewares/auth.js` `autenticar` | TC-001–TC-003 (perfil TC-021) | TC-001–TC-003 aprobados; TC-021 propuesto pendiente |
| HU-002 | Consultar sorteos | RF-003, RF-004, RF-018 | RNF-001, RNF-004, RNF-005, RNF-007 | Sorteos | `src/controllers/sorteoController.js` `listar`/`obtener`; `src/services/sorteoService.js` `listar`/`obtener` | TC-022 | Propuesto; pendiente |
| HU-003 | Administrar sorteos y registrar resultados | RF-005, RF-006, RF-007, RF-009, RF-018 | RNF-001, RNF-005, RNF-008 | Sorteos / liquidación | `src/services/sorteoService.js` `crear`, `registrarResultado`, `liquidarTicket` | TC-013 (creación); TC-023 (validación, resultado Baloto y repetido) | TC-013 aprobado; TC-023 propuesto pendiente |
| HU-004 | Vender chance | RF-007, RF-008, RF-018 | RNF-001, RNF-004, RNF-005, RNF-008 | Tiquetes / Chance | `src/services/ticketService.js` `venderChance`; `src/services/chanceService.js` `validarApuesta`, `calcularPremio`; `src/services/sorteoService.js` `verificarVentaPermitida` | TC-004–TC-007, TC-014, TC-016–TC-017 | TC-004–TC-007 y TC-014, TC-016–TC-017 aprobados |
| HU-005 | Vender Baloto | RF-009, RF-010, RF-018 | RNF-001, RNF-004, RNF-005, RNF-008 | Tiquetes / Baloto | `src/services/ticketService.js` `venderBaloto`; `src/services/balotoService.js` `validarJugada`, `generarAleatoria`, `calcularPremio` | TC-008–TC-010, TC-015 | TC-008–TC-010 y TC-015 aprobados |
| HU-006 | Consultar tiquetes | RF-011, RF-012, RF-018 | RNF-001, RNF-004, RNF-005, RNF-008 | Tiquetes | `src/services/ticketService.js` `listar`, `consultar`, `buscar` | TC-020 (consulta en flujo de pago); TC-024–TC-025 (listado/código no encontrado) | TC-020 aprobado; TC-024–TC-025 propuestos pendientes |
| HU-007 | Anular tiquete | RF-013, RF-018 | RNF-001, RNF-004, RNF-008 | Tiquetes | `src/services/ticketService.js` `anular` | TC-018–TC-019, TC-026 | TC-018–TC-019 aprobados; TC-026 propuesto pendiente |
| HU-008 | Pagar premio | RF-014, RF-018 | RNF-001, RNF-004, RNF-008 | Tiquetes / pagos | `src/services/ticketService.js` `pagarPremio` | TC-020, TC-027 | TC-020 aprobado; TC-027 propuesto pendiente |
| HU-009 | Consultar resumen de caja | RF-015, RF-018 | RNF-001, RNF-004, RNF-005, RNF-008 | Caja | `src/services/cajaService.js` `resumen`; `src/controllers/cajaController.js` `resumen` | TC-011, TC-028 | TC-011 aprobado; TC-028 propuesto pendiente |
| HU-010 | Cerrar caja | RF-016, RF-018 | RNF-001, RNF-004, RNF-008 | Caja | `src/services/cajaService.js` `cerrar`; `src/controllers/cajaController.js` `cerrar` | TC-012, TC-029 | TC-012 aprobado; TC-029 propuesto pendiente |
| HU-011 | Verificar disponibilidad | RF-017 | RNF-003, RNF-004, RNF-007 | Aplicación | `src/app.js` `GET /health` | TC-030 | Propuesto; pendiente |

## Comprobaciones de consistencia

- HU-001–HU-011 referencian al menos un RF y existen en [HISTORIAS_USUARIO.md](HISTORIAS_USUARIO.md).
- RF-001–RF-018 apuntan a rutas, controladores, servicios o middleware existentes; [REQUISITOS_FUNCIONALES.md](REQUISITOS_FUNCIONALES.md) identifica la evidencia concreta.
- RNF-001–RNF-008 se clasifican con evidencia/limitación y estado en [REQUISITOS_NO_FUNCIONALES.md](REQUISITOS_NO_FUNCIONALES.md); ninguno se presenta como aprobado por pruebas.
- TC-001–TC-020 corresponden a nombres `test_*` observados en los cinco archivos Jest y se ejecutaron con éxito. TC-021–TC-030 son casos propuestos, no ejecutados ni afirmados como automatizados.
- Todas las rutas documentadas se cotejaron con `src/routes/index.js` y la ruta operativa `/health` con `src/app.js`.
