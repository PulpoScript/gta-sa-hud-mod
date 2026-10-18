# Variables locales

Los scripts CLEO tienen variables locales `0@` a `31@`. Así se usan:

| Variable | Grupo | Rol | Significado |
|---|---|---|---|
| `0@` | General | estado | Mod activo (0 = apagado, 1 = encendido) |
| `1@` | Radar | pedido | El usuario pidió ocultar el radar (Shift + 1) |
| `2@` | Radar | deseado | 1 = debe estar oculto |
| `3@` | Radar | aplicado | 1 = oculto en el juego |
| `4@` | Arma | pedido | El usuario pidió ocultar el arma (Shift + 2) |
| `5@` | Arma | aplicado | 1 = oculto en el juego |
| `6@` | Arma | verificación | Byte leído al arrancar en `0x58D7D0` |
| `7@` | Arma | deseado | 1 = debe estar oculto |
| `8@` | Arma | disponible | Icono verificado (original 161) |
| `9@` | Arma | verificación | Byte leído al arrancar en `0x5893B0` |
| `10@` | — | — | **Sin uso** |
| `11@` | Arma | disponible | Munición verificada (original 83) |
| `12@` | Barras | pedido | El usuario pidió ocultar las barras (Shift + 3) |
| `13@` | Barras | deseado | 1 = deben estar ocultas |
| `14@` | Barras | aplicado | 1 = ocultas en el juego |
| `15@` | Barras | verificación | Byte leído al arrancar en `0x589270` |
| `16@` | Barras | disponible | Salud verificada (original 131) |
| `17@` | Barras | verificación | Byte leído al arrancar en `0x5890A0` |
| `18@` | Barras | disponible | Blindaje verificado (original 131) |
| `19@` | Modo | estado | Modo actual (1, 2, 3) |
| `20@` | Dinero | verificación | 4 bytes leídos al arrancar en `0x58F607` |
| `21@` | Dinero | verificación | 1 byte leído al arrancar en `0x58F60B` |
| `22@` | Dinero | disponible | Llamada original verificada |
| `23@` | Dinero | pedido | El usuario pidió ocultar el dinero (Shift + 4) |
| `24@` | Dinero | deseado | 1 = debe estar oculto |
| `25@` | Dinero | aplicado | 1 = oculto en el juego |
| `26@` | Hora | pedido | El usuario pidió ocultar la hora (Shift + 5) |
| `27@` | Hora | guardado | Valor de `bDrawClock` antes de ocultar |
| `28@` | Hora | deseado | 1 = debe estar oculta |
| `29@` | Hora | aplicado | 1 = oculta en el juego |
| `30@` | Hora | verificación | Byte leído al arrancar en `0xBAA400` |
| `31@` | — | — | **Libre** |

Libres: **`10@`** y **`31@`**. Si se necesitan más variables para los Modos 2 y 3, habrá que reutilizar alguna o usar variables globales.

`32@` y `33@` existen en CLEO, pero son temporizadores que el juego incrementa solo: no sirven como variables normales.
