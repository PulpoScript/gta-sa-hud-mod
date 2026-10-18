# Traspaso para la próxima sesión

Pega este archivo (o su enlace) como primer mensaje al empezar una sesión nueva con Claude. Resume dónde quedó el proyecto y qué hacer después.

## Contexto

- Mod CLEO para **GTA San Andreas PC 1.0 US** que controla el HUD. Código: [`src/UI.txt`](../src/UI.txt) (Sanny Builder 4, CLEO 4.4 + CLEO+).
- Repositorio: https://github.com/PulpoScript/gta-sa-hud-mod (público, licencia MIT, **sin releases**).
- Idioma de trabajo: **español**. Un paso cada vez, esperando la confirmación del autor antes de seguir.
- Claude no puede ejecutar el juego ni compilar: el autor compila, prueba y cuenta el resultado.

## Cómo quiere trabajar el autor

- Decir qué es seguro y qué no. **No afirmar opcodes ni direcciones sin verificarlos.**
- Código completo y listo para pegar, con comentarios por secciones. **Ponerlo en el chat, en un bloque de código, no como archivo adjunto**: el autor lo copia directamente a Sanny Builder.
- Para cada prueba: qué probar y qué resultado esperar, en una tabla corta.
- Respuestas cortas a las pruebas: "funciona" o qué falló. **No pedirle diagnósticos largos** salvo que algo falle sin explicación.
- Mantener el repositorio documentado (README, `docs/`, CHANGELOG) con cada cambio.
- **Dar crédito siempre:** toda fuente de la que se tome información (direcciones, estructuras, trucos) se añade a [CREDITOS.md](CREDITOS.md) con su autor y qué se usó.

## Estado al cerrar esta sesión

| Parte | Estado |
|---|---|
| Modo 1 (manual, Shift + 1 a 5) | ✅ Probado en el juego |
| Selector de modos (Shift + 0) | ✅ Probado: cambia y anuncia el modo |
| HUD de fábrica al salir del Modo 1 y **recuerdo** de las elecciones al volver | ✅ Probado |
| Versión **0.1.0** en cabecera y mensajes de F11; sin sonido doble | ⚠️ Programado, sin probar |
| Modo 2 (dinámico) | 🚧 Concepto en [MODO2.md](MODO2.md). Dinero ✅ (`0.2.0-dev.1`). Arma ✅ (`0.2.0-dev.2`). Salud ⚠️ (`0.2.0-dev.3`, sin probar) |
| Modo 3 | ⏳ Sin definir |
| Script de exploración (`experimentos/explorar_hud.txt`) | ⚠️ Escrito, sin compilar ni probar |
| Radar cuadrado / rectangular y mover elementos | 🔎 Investigado, ver [INVESTIGACION.md](INVESTIGACION.md) |

### Decisiones tomadas

- **Solo 3 modos.** Se probó un Modo 4 con radar cuadrado (parche `ret` en `CRadar::DrawRadarMask` `0x585700`, byte original **85**; el mapa se veía cuadrado, pero el aro seguía redondo) y el autor decidió descartarlo. Nunca se subió a GitHub.
- **Versiones:** ver [VERSIONES.md](VERSIONES.md). La actual es 0.1.0; el Modo 2 se desarrollará como `0.2.0-dev.N`.
- **CLEO o ASI:** este mod se queda en CLEO. Sirve de **prototipo** para materializar y pulir ideas; el mod definitivo se hará más adelante como un proyecto ASI aparte. Ver [CLEO-VS-ASI.md](CLEO-VS-ASI.md).

## Próximos pasos, en orden

1. **Probar la 0.1.0** (`docs/PRUEBAS.md`, M1–M3: un solo sonido y versión en pantalla).
2. Seguir con el **Modo 2** elemento por elemento según [MODO2.md](MODO2.md), ayudando al autor a pulir el concepto con ideas (inspirado en GTA V y RDR2, sin copiarlos). Sigue la guía "Cómo añadir un modo" de [ARQUITECTURA.md](ARQUITECTURA.md) y las marcas `PUNTO DE EXTENSIÓN` del código. Variables: se reutilizan las de verificación del arranque (ver [VARIABLES.md](VARIABLES.md)). No quedan temporizadores de CLEO (`32@` dinero, `33@` arma); desde la salud se usa el reloj del juego (`0xB7CB84`). Persistentes libres: ninguno.
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
