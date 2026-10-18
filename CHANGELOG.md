# Registro de cambios

Formato basado en [Keep a Changelog](https://keepachangelog.com/es-ES/1.1.0/). Numeración explicada en [docs/VERSIONES.md](docs/VERSIONES.md). Aún no hay releases publicadas.

## [Sin publicar]

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
