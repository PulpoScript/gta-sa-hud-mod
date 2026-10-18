# Traspaso para la próxima sesión

Pega este archivo (o su enlace) como primer mensaje al empezar una sesión nueva con Claude. Resume dónde quedó el proyecto y qué hacer después.

## Contexto

- Mod CLEO para **GTA San Andreas PC 1.0 US** que controla el HUD. Código: [`src/UI.txt`](../src/UI.txt) (Sanny Builder 4, CLEO 4.4 + CLEO+).
- Repositorio: https://github.com/PulpoScript/gta-sa-hud-mod (público, licencia MIT, **sin releases**).
- Idioma de trabajo: **español**. Un paso cada vez, esperando la confirmación del autor antes de seguir.
- Claude no puede ejecutar el juego ni compilar: el autor compila, prueba y cuenta el resultado.

## Cómo quiere trabajar el autor

- Decir qué es seguro y qué no. **No afirmar opcodes ni direcciones sin verificarlos.**
- Código completo y listo para pegar, con comentarios por secciones.
- Para cada prueba: qué probar y qué resultado esperar, en una tabla corta.
- Respuestas cortas a las pruebas: "funciona" o qué falló. **No pedirle diagnósticos largos** salvo que algo falle sin explicación.
- Mantener el repositorio documentado (README, `docs/`, CHANGELOG) con cada cambio.

## Estado al cerrar esta sesión

| Parte | Estado |
|---|---|
| Modo 1 (manual, Shift + 1 a 5) | ✅ Probado en el juego |
| Selector de modos (Shift + 0) | ✅ Probado: cambia y anuncia el modo |
| HUD de fábrica al salir del Modo 1 y **recuerdo** de las elecciones al volver | ⚠️ **Programado, sin probar** |
| Modos 2 y 3 | ⏳ Sin definir: preguntar al autor qué deben hacer |
| Script de exploración (`experimentos/explorar_hud.txt`) | ⚠️ Escrito, sin compilar ni probar |
| Radar cuadrado / rectangular y mover elementos | 🔎 Investigado, ver [INVESTIGACION.md](INVESTIGACION.md) |

### Problema abierto con la última prueba

El autor probó la versión que reiniciaba el HUD al cambiar de modo y vio que **los elementos seguían ocultos en el Modo 2**. El código revisado era correcto, y ese comportamiento coincide con la versión anterior del script. Es muy probable que el juego siguiera ejecutando el `MiMod.cs` antiguo (ya pasó una vez con el dinero). Para descartarlo, el mensaje del selector ahora dice **"UI - Modo N"**: si al cambiar de modo sale solo "Modo N", se está ejecutando un `.cs` viejo.

## Próximos pasos, en orden

1. **Probar la versión actual** (`docs/PRUEBAS.md`, M4–M7). Antes, borrar `MiMod.cs` de `CLEO\` y poner el `UI.cs` recién compilado.
2. Preguntar al autor **qué debe hacer el Modo 2** y construirlo paso a paso. Su regla se añade al cálculo del *estado deseado* de cada elemento (ver [ARQUITECTURA.md](ARQUITECTURA.md)).
3. Si quiere modificar el radar o la posición de elementos: compilar `experimentos/explorar_hud.txt`, **descompilarlo primero** (usa `gosub`, `+=` y `not ... >`, que todavía no se han verificado en SB4) y pasarlo en el juego.

## Lecciones clave (no repetir errores)

- En SB4, la forma antigua `0A8D: x = read_memory ...` **compila los parámetros en otro orden** y crashea. Usar `x = read_memory addr {size} n {vp} False` (ver [SANNY-BUILDER-4.md](SANNY-BUILDER-4.md)).
- Antes de probar: comprobar que en `CLEO\` hay **un solo** `.cs` del mod y que es el recién compilado.
- Antes de escribir en memoria: **leer y verificar** el byte original. Restaurar si encuentras el parche; no tocar nada si el valor es desconocido.
- No comparar dos variables entre sí en SB4 (error 0014).
- `'CHEAT1'` = "Truco activado"; `'CHEAT2'` **no** es "desactivado".

## Documentación

- [ARQUITECTURA.md](ARQUITECTURA.md): patrón de estados y técnicas
- [DIRECCIONES.md](DIRECCIONES.md): direcciones y bytes originales verificados
- [VARIABLES.md](VARIABLES.md): uso de `0@` a `31@`
- [INVESTIGACION.md](INVESTIGACION.md): posición, tamaño y forma del HUD
- [PRUEBAS.md](PRUEBAS.md) y [HISTORIAL.md](HISTORIAL.md)
