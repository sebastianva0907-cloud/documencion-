# Historias de usuario reconstruidas

## Criterio de reconstrucción

Las historias siguientes describen capacidades observables en rutas, controladores, servicios, almacenamiento y pruebas. Los actores y beneficios se infieren del modelo de roles y del flujo implementado; no son requisitos originales recuperados. `ADMIN` y `VENDEDOR` son los roles comprobados en el código. Los criterios son verificables; su existencia no significa que todos tengan una prueba automatizada.

## Historias

### HU-001 — Autenticarse y consultar perfil
- **Como:** usuario registrado (administrador o vendedor).
- **Quiero:** iniciar sesión y consultar mi perfil autenticado.
- **Para:** obtener acceso a las operaciones permitidas para mi rol.
- **Criterios de aceptación:** credenciales correctas devuelven un JWT y perfil sin contraseña; credenciales ausentes o incorrectas producen error; el perfil requiere token válido.
- **Prioridad:** Alta.
- **Módulo relacionado:** autenticación, `src/controllers/authController.js`, `src/services/authService.js`, `src/middlewares/auth.js`.

### HU-002 — Consultar sorteos
- **Como:** usuario autenticado.
- **Quiero:** listar sorteos con filtros opcionales y consultar uno por identificador.
- **Para:** conocer tipo, fecha, estado, resultado y acumulado disponibles.
- **Criterios de aceptación:** el listado permite filtrar por `tipo` y `estado`; el detalle devuelve el sorteo identificado o 404; se requiere autenticación.
- **Prioridad:** Alta.
- **Módulo relacionado:** sorteos, `src/controllers/sorteoController.js`, `src/services/sorteoService.js`.

### HU-003 — Administrar sorteos y registrar resultados
- **Como:** administrador.
- **Quiero:** crear sorteos futuros y registrar el resultado de un sorteo no jugado.
- **Para:** habilitar ventas y liquidar los tiquetes asociados.
- **Criterios de aceptación:** solo ADMIN puede crear o registrar resultados; tipo/fecha/nombre se validan; al registrar se valida resultado según tipo, cambia el estado a `JUGADO` y se liquidan tiquetes activos; no se registra dos veces.
- **Prioridad:** Alta.
- **Módulo relacionado:** sorteos, `src/controllers/sorteoController.js`, `src/services/sorteoService.js`.

### HU-004 — Vender chance
- **Como:** vendedor.
- **Quiero:** registrar un tiquete de chance con una o más apuestas.
- **Para:** dejar constancia de la apuesta y su valor.
- **Criterios de aceptación:** se admite de 1 a 5 apuestas válidas; se valida número, modalidad, valor y sorteo CHANCE abierto dentro del cierre de ventas; se crea código `CH-`, total y estado `ACTIVO`; un rol distinto de VENDEDOR recibe 403.
- **Prioridad:** Alta.
- **Módulo relacionado:** tiquetes/chance, `src/controllers/ticketController.js`, `src/services/ticketService.js`, `src/services/chanceService.js`.

### HU-005 — Vender Baloto
- **Como:** vendedor.
- **Quiero:** registrar un tiquete de Baloto con jugadas manuales o aleatorias.
- **Para:** participar en un sorteo y conservar el código de compra.
- **Criterios de aceptación:** se admite de 1 a 10 jugadas válidas; cada jugada contiene cinco números únicos entre 1 y 43 y superbalota entre 1 y 16, o solicita generación aleatoria; el sorteo debe ser BALOTO y aceptar ventas; precio total es número de jugadas por `VALOR_BALOTO`.
- **Prioridad:** Alta.
- **Módulo relacionado:** tiquetes/Baloto, `src/controllers/ticketController.js`, `src/services/ticketService.js`, `src/services/balotoService.js`.

### HU-006 — Consultar tiquetes
- **Como:** usuario autenticado.
- **Quiero:** listar tiquetes según mi alcance y consultar un tiquete por código.
- **Para:** revisar su estado, sorteo asociado y condición de ganador si ya fue jugado.
- **Criterios de aceptación:** ADMIN lista todos y otros usuarios solo los propios; se pueden aplicar filtros `fecha` y `estado`; la consulta añade información del sorteo y determina ganador solo si está jugado; un código inexistente produce 404.
- **Prioridad:** Media.
- **Módulo relacionado:** tiquetes, `src/controllers/ticketController.js`, `src/services/ticketService.js`.

### HU-007 — Anular tiquete
- **Como:** vendedor propietario (dentro de la ventana permitida) o administrador.
- **Quiero:** anular un tiquete activo indicando un motivo.
- **Para:** corregir una venta no válida antes de su liquidación.
- **Criterios de aceptación:** se exige motivo; solo el propietario o ADMIN puede anular; solo estado `ACTIVO` y sorteo no jugado; el vendedor está sujeto a `MINUTOS_ANULACION`; se conserva usuario, motivo y fecha de anulación.
- **Prioridad:** Alta.
- **Módulo relacionado:** tiquetes, `src/controllers/ticketController.js`, `src/services/ticketService.js`.

### HU-008 — Pagar premio
- **Como:** vendedor autorizado.
- **Quiero:** registrar el pago de un tiquete ganador vigente.
- **Para:** reflejar el desembolso y evitar un segundo pago.
- **Criterios de aceptación:** el sorteo debe estar jugado, el tiquete activo y con premio; un pago repetido se rechaza; premios con antigüedad superior a `DIAS_CADUCIDAD_PREMIO` se marcan caducados y el pago se rechaza; ADMIN no tiene permiso de ruta para pagar.
- **Prioridad:** Alta.
- **Módulo relacionado:** tiquetes/pagos, `src/controllers/ticketController.js`, `src/services/ticketService.js`.

### HU-009 — Consultar resumen de caja
- **Como:** vendedor o administrador.
- **Quiero:** consultar las ventas y pagos de una fecha.
- **Para:** conocer el saldo esperado de caja.
- **Criterios de aceptación:** vendedores consultan sus propios datos; ADMIN puede indicar `vendedorId`; se devuelven conteos/valores por tipo, pagos, saldo y estado de cierre; la fecha predeterminada usa la zona configurada.
- **Prioridad:** Alta.
- **Módulo relacionado:** caja, `src/controllers/cajaController.js`, `src/services/cajaService.js`.

### HU-010 — Cerrar caja
- **Como:** vendedor.
- **Quiero:** registrar el efectivo reportado para cerrar la caja de una fecha.
- **Para:** comparar el efectivo con el saldo esperado y conocer diferencias.
- **Criterios de aceptación:** efectivo es entero no negativo; no se permite un segundo cierre para vendedor/fecha; se registra diferencia y estado `CUADRADA`, `SOBRANTE` o `FALTANTE`; respuesta HTTP 201.
- **Prioridad:** Alta.
- **Módulo relacionado:** caja, `src/controllers/cajaController.js`, `src/services/cajaService.js`.

### HU-011 — Verificar disponibilidad del servicio
- **Como:** consumidor u operador del servicio.
- **Quiero:** consultar el endpoint de salud.
- **Para:** obtener una señal básica de que el proceso HTTP responde.
- **Criterios de aceptación:** `GET /health` devuelve JSON con `estado: "ok"` y una hora ISO; no requiere autenticación. No comprueba dependencias externas ni persistencia.
- **Prioridad:** Media.
- **Módulo relacionado:** aplicación, `src/app.js`.

## Cobertura de requisitos

La relación HU–RF–RNF y los casos actuales/propuestos se encuentra en [MATRIZ_TRAZABILIDAD_RTM.md](MATRIZ_TRAZABILIDAD_RTM.md). Las pruebas existentes y pendientes se detallan en [CASOS_DE_PRUEBA.md](CASOS_DE_PRUEBA.md).
