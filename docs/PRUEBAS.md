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
| M1 | F11 | **Un solo** sonido y "UI v0.2.0 activado" |
| M2 | F11 otra vez | "UI v0.2.0 desactivado"; todo el HUD visible |
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

## Modo 2 (con el mod activo y en "UI - Modo 2")

| # | Acción | Resultado esperado |
|---|---|---|
| D1 | Entrar en el Modo 2 sin tocar el dinero | A los 5 s del último cambio de dinero, el dinero desaparece |
| D2 | Ganar o gastar dinero (comprar algo, `HESOYAM`) | El dinero aparece y se oculta 5 s después del último cambio |
| D3 | Cambiar al Modo 1 o 3 | El dinero se ve siempre (salvo que el Modo 1 lo tenga oculto) |
| D4 | Cargar partida en el Modo 2 | El dinero se ve unos 5 s y se oculta |

| W1 | En el Modo 2, esperar 5 s sin tocar armas | El icono del arma desaparece |
| W2 | Cambiar de arma (rueda o Q/E) | Aparece el icono y se oculta a los 5 s |
| W3 | Recoger un arma o munición, o usar un truco de armas | Aparece el icono |
| W4 | Disparar | Aparece el icono mientras disparas (recargar sola probablemente no lo muestra) |

| H1 | En el Modo 2, esperar 5 s sin daño | Salud y blindaje desaparecen |
| H2 | Recibir daño (caída, pelea) | Aparecen y se ocultan 5 s después del último daño |
| H3 | Curarse o ponerse blindaje (`HESOYAM`) | Aparecen |
| H4 | Shift + 5 en el Modo 1 (hora) | Sigue ocultando y mostrando la hora |
| H5 | Bajar de 25 de salud (barra casi vacía) | Las barras **se quedan visibles** aunque pasen 5 s |
| H6 | Curarse por encima de 25 | A los 5 s se ocultan otra vez |

| R-1 | En el Modo 2, quedarse quieto 8 s | El radar desaparece |
| R-2 | Caminar o correr (sin esprintar) | El radar sigue oculto |
| R-3 | Esprintar | Aparece y se oculta 8 s después de dejar de esprintar |
| R-4 | Subir a un vehículo | Aparece y se mantiene mientras estés dentro (también parado) |
| R-5 | Bajar del vehículo | Se oculta a los 8 s |
| R-6 | Conseguir nivel de búsqueda | Visible mientras dure la búsqueda |
| R-7 | Modo 1: Shift + 2 y Shift + 3 | Siguen ocultando arma y barras (indicadores unidos) |

| C-1 | Entrar en el Modo 2 (Shift + 0) | Se ve todo unos 3 s y luego se oculta |
| C-2 | Toque corto de "." | Al soltar, aparece **solo la hora** 5 s |
| C-3 | Mantener "." 0,8 s | A los 0,8 s aparece todo; al soltar, se oculta a los 3 s |
| C-4 | Ganar o gastar dinero / cambio de hora del juego | Solo el dinero / nada; la hora no aparece |
| C-5 | En el Modo 1 o 3, pulsar "." | No pasa nada |

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
| 2026-10 | 0.2.0-dev.1: dinero en Modo 2 | D1–D3 verificados por el autor; 5 s aprobados |
| 2026-10 | 0.2.0-dev.2: arma en Modo 2 | W1–W4 verificados por el autor |
| 2026-10 | 0.2.0-dev.3: salud en Modo 2 | H1–H4 verificados por el autor |
| 2026-10 | 0.2.0-dev.4: aviso de poca salud | H5–H6 verificados por el autor |
| 2026-10 | 0.2.0-dev.5: radar en Modo 2 | R-1 a R-7 verificados por el autor |
| 2026-10 | 0.2.0-dev.6: hora, consulta y entrada al modo | Probada; el autor pidió desvincular la hora del dinero |
| 2026-10 | 0.2.0-dev.7: hora con la tecla "." | Verificado por el autor; pidió acortar la pulsación larga |
| 2026-10 | 0.2.0-dev.8 / 0.2.0: pulsación larga de 0,8 s | C-1 a C-5 verificados por el autor. **Modo 2 completo** |
