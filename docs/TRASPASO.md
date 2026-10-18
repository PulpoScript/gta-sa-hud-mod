# Traspaso para la próxima sesión

Pega este archivo (o su enlace) como primer mensaje al empezar una sesión nueva con Claude. Resume dónde quedó el proyecto y qué hacer después.

## Contexto

- Mod CLEO para **GTA San Andreas PC 1.0 US** que controla el HUD. Código: [`src/UI.txt`](../src/UI.txt) (Sanny Builder 4, CLEO 4.4 + CLEO+).
- Repositorio: https://github.com/PulpoScript/gta-sa-hud-mod (público, licencia MIT, **sin releases**).
- Idioma de trabajo: **español**. Un paso cada vez, esperando la confirmación del autor antes de seguir.
- Claude no puede ejecutar el juego ni compilar: el autor compila, prueba y cuenta el resultado.

## Cómo quiere trabajar el autor

- Decir qué es seguro y qué no. **No afirmar opcodes ni direcciones sin verificarlos.**
- Código completo y listo para pegar, con comentarios por secciones. **Ponerlo en el chat, en un bloque de código, no como archivo adjunto**: el autor lo copia directamente a Sanny Builder. **Siempre el código completo**, también cuando el cambio sea pequeño: nunca solo los bloques que cambian.
- Para cada prueba: qué probar y qué resultado esperar, en una tabla corta.
- **Respuestas de Claude cortas**, salvo que el autor pida una explicación detallada.
- **Código: el autor lo copia desde GitHub** (Raw de `src/UI.txt`). No pegar el código completo en el chat salvo que lo pida.
- **Documentación de `docs/` solo al cerrar cada versión** (`0.2.0`, `0.3.0`…). En los pasos `-dev` solo se tocan el código y el CHANGELOG.
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
| Modo 2 (dinámico) | 🚧 Concepto en [MODO2.md](MODO2.md). Dinero ✅ (`0.2.0-dev.1`). Arma ✅ (`0.2.0-dev.2`). Salud ✅ (`0.2.0-dev.3`). Aviso de poca salud ✅ (`0.2.0-dev.4`). Radar ✅ (`0.2.0-dev.5`). Hora con la tecla "." (toque = hora, mantener 0,8 s = todo) y entrada al modo ✅ (`0.2.0-dev.8`). **Completo en la `0.2.0`** |
| Modo 3 | ⏳ Sin definir |
| Script de exploración (`experimentos/explorar_hud.txt`) | ⚠️ Escrito, sin compilar ni probar |
| Radar cuadrado / rectangular y mover elementos | 🔎 Investigado, ver [INVESTIGACION.md](INVESTIGACION.md) |

### Decisiones tomadas

- **Solo 3 modos.** Se probó un Modo 4 con radar cuadrado (parche `ret` en `CRadar::DrawRadarMask` `0x585700`, byte original **85**; el mapa se veía cuadrado, pero el aro seguía redondo) y el autor decidió descartarlo. Nunca se subió a GitHub.
- **Versiones:** ver [VERSIONES.md](VERSIONES.md). La actual es **0.2.0**; el Modo 3 se desarrollará como `0.3.0-dev.N`.
- **CLEO o ASI:** este mod se queda en CLEO. Sirve de **prototipo** para materializar y pulir ideas; el mod definitivo se hará más adelante como un proyecto ASI aparte. Ver [CLEO-VS-ASI.md](CLEO-VS-ASI.md).

## Plan del Modo 3 (acordado con el autor)

- **Comportamiento:** el mismo que el Modo 2 (aparecer cuando pasa algo, ocultarse al rato), pero con **hora, dinero, barras de salud y blindaje, estrellas (nuevas) e icono de arma en otra posición**, sin solaparse. Referencia: la posición de la hora.
- **Mantener "." 0,8 s:** todo el HUD **en su posición de fábrica**.
- **Técnica elegida por el autor:** parchear las coordenadas del juego (no dibujar elementos propios). Investigación y direcciones candidatas en [INVESTIGACION.md](INVESTIGACION.md), sección 5: **ninguna verificada todavía**.
- **Dos scripts:** `UI.cs` sigue mandando (F11, Shift + 0, ".", mostrar y ocultar; en el Modo 3 oculta igual que en el Modo 2). `UI_Modo3.cs` (nuevo, `src/UI_Modo3.txt`) solo mueve las coordenadas en el Modo 3 y restaura las de fábrica al salir o al mantener ".". Se comunican con variables compartidas de CLEO 4 (`0AB3`/`0AB4`, **sin verificar en SB4**).
- **Primeros pasos:** (1) ✅ variables compartidas probadas en el juego (`set_cleo_shared_var {varId} 50 {value} 0@` y `0@ = get_cleo_shared_var {varId} 50`); (2) ✅ direcciones candidatas leídas con `experimentos/verificar_posiciones.txt` (resultados en el CHANGELOG, sección "Sin publicar"); (3) el autor describe dónde quiere cada elemento respecto a la hora.
- **Resultado del paso 2:** cada dirección candidata es el **operando** (puntero de 4 bytes) de un `fmul`/`fld`. Los floats se comparten entre elementos, así que se cambia el puntero, nunca el float. Iguales a 640 × 448 y a 1920 × 1080.
- **Pendiente:** `0x58F92F` (arma X) y `0x58F9F7` (munición X) apuntan fuera del ejecutable (`0x6EC38794`, `0x6EC387A8`): algún ASI (¿WidescreenFix o SilentPatch?) ya los ha redirigido. Preguntado al autor: lista de `.asi` (junto a `gta_sa.exe` y en `scripts\`) y qué 4 `.cs` cargaba CLEO durante la prueba. `0x58F9DC` vale 20: probablemente es la Y del icono, no de la munición.

## Próximos pasos, en orden

1. **Probar la 0.1.0** (`docs/PRUEBAS.md`, M1–M3: un solo sonido y versión en pantalla).
2. **Modo 3:** seguir el plan de arriba. En `UI.txt` no queda ninguna variable libre (ver [VARIABLES.md](VARIABLES.md)). Sigue la guía "Cómo añadir un modo" de [ARQUITECTURA.md](ARQUITECTURA.md) y las marcas `PUNTO DE EXTENSIÓN` del código. Variables: se reutilizan las de verificación del arranque (ver [VARIABLES.md](VARIABLES.md)). No quedan temporizadores de CLEO (`32@` dinero, `33@` arma); desde la salud se usa el reloj del juego (`0xB7CB84`). Persistentes libres: ninguno.
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
