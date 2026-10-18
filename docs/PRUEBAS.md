# Pruebas en el juego

No hay pruebas automáticas: todo se prueba a mano en el juego. Esta lista sirve para repasar el mod antes de cada versión.

## Preparación

- Cierra el juego del todo antes de cada prueba, para que no quede ningún parche en memoria.
- Comprueba que en `CLEO\` solo haya un `MiMod.cs` y que sea el recién compilado.
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
| M1 | F11 | Sonido y "Truco activado" |
| M2 | F11 otra vez | Sonido y "Truco desactivado"; todo el HUD visible |
| M3 | Shift + 0 (×3) | "Modo 2", "Modo 3", "Modo 1" |

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
