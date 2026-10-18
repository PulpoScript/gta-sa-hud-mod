# Pruebas en el juego

No hay pruebas automáticas: todo se prueba a mano en el juego. Esta lista sirve para repasar el mod antes de cada versión.

## Preparación

- Cierra el juego del todo antes de cada prueba, para que no quede ningún parche en memoria.
- Comprueba que en `CLEO\` solo haya un `UI.cs` y que sea el recién compilado.
- Lleva un arma de fuego para ver la munición. Usa `HESOYAM` para tener blindaje.

## Arranque

| # | Acción | Resultado esperado |
|---|---|---|
| A1 | Cargar partida | No sale ningún mensaje a los 3 s |
| A2 | Shift + 0…5 sin pulsar F11 | No pasa nada |

Si a los 3 s sale "byte inesperado", el ejecutable no es 1.0 US: ese elemento queda desactivado y no se toca.

## Mod y modos

| # | Acción | Resultado esperado |
|---|---|---|
| M1 | F11 | **Un solo** sonido y "UI v0.1.0 activado" |
| M2 | F11 otra vez | "UI v0.1.0 desactivado"; todo el HUD visible |
| M3 | Shift + 0 (×3) | "UI - Modo 2", "UI - Modo 3", "UI - Modo 1" (si sale solo "Modo N", el `.cs` es antiguo) |
| M4 | Modo 1: ocultar varios elementos → Shift + 0 | "Modo 2" y todo el HUD vuelve a verse |
| M5 | En Modo 2 o 3: Shift + 1 a 5 | No pasa nada |
| M6 | Volver al Modo 1 | **Se vuelve a ocultar lo que estaba elegido** en M4 |
| M7 | En Modo 1, Shift + 1 a 5 | Funcionan con normalidad |

## Modo 1: un elemento cada vez (con el mod activo)

| # | Acción | Resultado esperado |
|---|---|---|
| E1 | Shift + 1 / otra vez | El radar se oculta / vuelve |
| E2 | Shift + 2 / otra vez | El icono de arma **y** la munición se ocultan / vuelven |
| E3 | Shift + 3 / otra vez | La salud **y** el blindaje se ocultan / vuelven (incluido el parpadeo con poca salud) |
| E4 | Shift + 4 / otra vez | Solo el dinero se oculta / vuelve |
| E5 | Shift + 5 / otra vez | La hora se oculta y **se queda oculta** / vuelve |

## Restauración

| # | Acción | Resultado esperado |
|---|---|---|
| R1 | Ocultar todo → F11 | Todo vuelve a verse |
| R2 | Ocultar todo → F11 → F11 | Vuelve a ocultarse lo que estaba elegido |
| R3 | Ocultar todo → guardar → cargar | Al cargar, todo se ve normal y no sale ningún mensaje |

## Resultados

| Fecha | Versión | Resultado |
|---|---|---|
| 2026-10 | Modo 1 + selector | A1–A2, M1–M3, E1–E5, R1 y R3 verificados por el autor |
| 2026-10 | Reinicio al cambiar de modo | El autor vio elementos ocultos en el Modo 2; probablemente se ejecutaba el `.cs` antiguo (ver TRASPASO.md) |
| 2026-10 | Recuerdo del Modo 1 | M3–M7 verificados por el autor (ciclo 1→2→3→1 y recuerdo del Modo 1) |
| 2026-10 | 0.1.0: un solo sonido y versión en pantalla | Pendiente: M1–M3 |
