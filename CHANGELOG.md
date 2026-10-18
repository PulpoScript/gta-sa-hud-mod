# Registro de cambios

Formato basado en [Keep a Changelog](https://keepachangelog.com/es-ES/1.1.0/). Numeración explicada en [docs/VERSIONES.md](docs/VERSIONES.md). Aún no hay releases publicadas.

## [Sin publicar]

### Documentación

- Créditos completos de las fuentes consultadas (`docs/CREDITOS.md`) y aviso legal en el README.

### 0.2.0-dev.4 — Modo 2: aviso de poca salud (probado)

- En el Modo 2, salud y blindaje se quedan visibles mientras la salud esté por debajo de 25.

### 0.2.0-dev.3 — Modo 2, paso 3: salud + blindaje (probado)

- En el Modo 2, salud y blindaje se muestran 5 s al recibir daño o al subir.
- Se mide el tiempo con el reloj del juego (`0xB7CB84`), porque no quedan temporizadores de CLEO.
- La hora se restaura siempre a su valor de fábrica (1), lo que libera una variable.

### 0.2.0-dev.2 — Modo 2, paso 2: arma + munición (probado)

- En el Modo 2, el arma y la munición se muestran 5 s al cambiar de arma, conseguir un arma o munición o disparar.
- Las variables de verificación del arranque se reutilizan en el bucle.

### 0.2.0-dev.1 — Modo 2, paso 1: dinero (probado)

- En el Modo 2, el dinero solo se muestra durante 5 s tras ganar o perder dinero.
- Concepto y plan del Modo 2 en `docs/MODO2.md`.

## [0.1.0] - 2026-10-18 (beta)

### Añadido

- Modo 1 (manual): Shift + 1 radar, Shift + 2 icono de arma + munición, Shift + 3 salud + blindaje, Shift + 4 dinero, Shift + 5 hora.
- Selector de modos con Shift + 0 (1 → 2 → 3 → 1). Los Modos 2 y 3 aún no tienen lógica.
- F11 para encender y apagar el mod (temporal, solo para pruebas).
- Verificación de bytes originales al arrancar y restauración automática al cargar partida con elementos ocultos.
- Aviso en pantalla y desactivación del elemento afectado si el ejecutable no es 1.0 US.
- Documentación completa en `docs/`.
- Licencia MIT.
- `experimentos/explorar_hud.txt`: script de solo lectura para investigar el radar y las escalas del HUD.
- `docs/INVESTIGACION.md` (posición, tamaño y forma del HUD) y `docs/TRASPASO.md` (resumen para retomar el proyecto).

### Cambiado

- Al cambiar de modo con Shift + 0, el HUD vuelve a su estado de fábrica (todo visible). El Modo 1 recuerda sus elecciones y las reaplica al volver.
- El mensaje del selector es ahora "UI - Modo N", para distinguir este script de versiones antiguas.
- Shift + 1 a 5 solo funcionan en el Modo 1.
- F11 ya no reproduce un sonido extra (sonaban dos a la vez) y muestra "UI v0.1.0 activado / desactivado".
- El código fuente se llama ahora `src/UI.txt` y se compila como `UI.cs`. Hay que borrar el `MiMod.cs` antiguo de `CLEO\` para no tener dos copias.
