# Direcciones de memoria

Todas corresponden a **GTA San Andreas PC 1.0 US**. En otras versiones del ejecutable son distintas.

> **Regla del proyecto:** no se usa ninguna dirección sin verificarla. Una dirección errónea puede crashear el juego. Cada dirección se comprueba de dos formas: en una fuente pública de ingeniería inversa **y** leyendo sus bytes en el ejecutable real antes de parchear.

## Direcciones en uso

| Elemento | Símbolo | Dirección | Decimal | Tipo | Original | Parche |
|---|---|---|---|---|---|---|
| Icono de arma | `CHud::DrawWeaponIcon` | `0x58D7D0` | 5822416 | función cdecl | `161` (`0xA1`) | `195` (`0xC3`, ret) |
| Munición | `CHud::DrawAmmo` | `0x5893B0` | 5804976 | función cdecl | `83` (`0x53`) | `195` |
| Salud | `CHud::RenderHealthBar` | `0x589270` | 5804656 | función cdecl | `131` (`0x83`) | `195` |
| Blindaje | `CHud::RenderArmorBar` | `0x5890A0` | 5804192 | función cdecl | `131` (`0x83`) | `195` |
| Dinero | `call CFont::PrintString` dentro de `CHud::DrawPlayerInfo` | `0x58F607` | 5830151 | instrucción, 5 bytes | `E8 F4 B0 18 00` | `90 90 90 90 90` |
| Hora | `CHud::bDrawClock` | `0xBAA400` | 12231680 | `bool` (dato) | `1` | `0` |
| Dinero del jugador (lectura, Modo 2) | `CPlayerInfo::m_nMoney` de `CWorld::Players[0]` | `0xB7CE50` | 12045904 | `int` (dato) | — | solo lectura |

El radar no usa memoria: se controla con el opcode `0581: display_radar`.

### Detalle del dinero

La instrucción en `0x58F607` es `call rel32`:

- `E8` = opcode de `call`.
- `F4 B0 18 00` = desplazamiento relativo `0x0018B0F4`.
- Destino = `0x58F607 + 5 + 0x18B0F4 = 0x71A700` = `CFont::PrintString`.

Para leerla y escribirla desde CLEO se divide en 4 + 1 bytes:

| Parte | Dirección | Original | Parche |
|---|---|---|---|
| 4 bytes (entero con signo) | `0x58F607` | `414250216` (`0x18B0F4E8`) | `-1869574000` (`0x90909090`) |
| 1 byte | `0x58F60B` | `0` | `144` (`0x90`) |

Este parche viene de un truco publicado por Silent en GTAForums. No se encontró documentación pública de la dirección, así que se verificó comprobando que los bytes leídos en el juego son exactamente la llamada a `CFont::PrintString`.

## Otras direcciones conocidas (sin usar)

| Símbolo | Dirección | Notas |
|---|---|---|
| `CHud::RenderBreathBar` | `0x589190` | Barra de oxígeno, cdecl. Byte original **sin comprobar**. |
| `CHud::Draw` | `0x58FAE0` | Dibujo general del HUD. |
| `CHud::DrawPlayerInfo` | `0x58EAF0` | Dibuja dinero, hora y barras. |
| `CHud::bScriptDontDisplayRadar` | `0xBAA3FB` | Flag del radar (se usa el opcode en su lugar). |
| `CFont::PrintString` | `0x71A700` | cdecl `(float x, float y, const char* text)` |

## Fuentes

- **plugin-sdk**: [`plugin_sa/game_sa/CHud.cpp`](https://github.com/DK22Pac/plugin-sdk/blob/master/plugin_sa/game_sa/CHud.cpp) y [`CFont.cpp`](https://github.com/DK22Pac/plugin-sdk/blob/master/plugin_sa/game_sa/CFont.cpp): direcciones y convención de llamada de las funciones.
- **gta-reversed**: [`source/game_sa/Hud.cpp`](https://github.com/gta-reversed/gta-reversed/blob/master/source/game_sa/Hud.cpp) y [`Hud.h`](https://github.com/gta-reversed/gta-reversed/blob/master/source/game_sa/Hud.h): qué dibuja cada función y la dirección de `bDrawClock`.
- **Ejecutable real:** los bytes originales se leyeron en el juego con `read_memory` antes de fijarlos en el código.
