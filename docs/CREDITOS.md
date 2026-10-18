# Créditos y fuentes

Este mod **no descubre nada nuevo**: se apoya en el trabajo de la comunidad de modding de GTA San Andreas, que ha documentado durante años cómo funciona el juego por dentro. Aquí se reconoce cada fuente de la que sale información usada en el proyecto.

**No se ha copiado código** de ningún proyecto. Se han consultado **datos** (direcciones de memoria, desplazamientos dentro de estructuras, convenciones de llamada y el funcionamiento de funciones del juego) para escribir un script propio. Cada dato se verificó además en el ejecutable real antes de usarlo (ver [DIRECCIONES.md](DIRECCIONES.md)).

## Herramientas en las que se basa el mod

| Proyecto | Autores | Para qué se usa |
|---|---|---|
| [CLEO](https://cleo.li/) | Seemann, Alien, Deji y el equipo de CLEO | Biblioteca que ejecuta scripts personalizados en GTA SA. El mod es un script CLEO |
| [CLEO+](https://www.mixmods.com.br/2022/05/cleoplus/) | Junior_Djjr (MixMods) | Plugin de CLEO requerido por el script |
| [Sanny Builder](https://sannybuilder.com/) | Seemann y colaboradores | Editor y compilador del script, y su documentación de opcodes y errores (por ejemplo, el [error 0014](https://docs.sannybuilder.com/troubleshooting/errors/0014)) |

## Fuentes de información

| Fuente | Autores | Qué información se tomó | Dónde se usa |
|---|---|---|---|
| [plugin-sdk](https://github.com/DK22Pac/plugin-sdk) | DK22Pac y colaboradores | Direcciones y convención de llamada de `CHud::DrawWeaponIcon`, `CHud::DrawAmmo`, `CHud::RenderHealthBar`, `CHud::RenderArmorBar`, `CHud::RenderBreathBar`, `CHud::DrawRadar`, `CHud::Sprites`, `CFont::PrintString`. Estructuras `CWorld::Players`, `CPlayerInfo` (`m_pPed`, `m_nMoney`), `CPed` (salud, blindaje, armas, ranura activa), `CWeapon` y `CTimer::m_snTimeInMilliseconds` | Modo 1 (pasos 2 a 4) y Modo 2 |
| [gta-reversed](https://github.com/gta-reversed/gta-reversed) | Equipo y colaboradores de gta-reversed | Funcionamiento reconstruido de `CHud` (qué dibuja cada función, posición de la hora y del dinero, aro del radar, lista de sprites, dirección de `CHud::bDrawClock`) y de `CRadar` (`DrawRadarMask`, `LimitRadarPoint`) | Hora (paso 5), dinero, investigación del radar y de posiciones |
| [WidescreenFixesPack](https://github.com/ThirteenAG/WidescreenFixesPack) (`GTASA.WidescreenFix`) | ThirteenAG | Lista de instrucciones donde el juego aplica la escala de cada elemento del HUD y del radar | [INVESTIGACION.md](INVESTIGACION.md) y `experimentos/explorar_hud.txt` |
| [GTAForums](https://gtaforums.com/) | Silent | Truco para ocultar el dinero sustituyendo la llamada en `0x58F607` por NOPs | Dinero (Modo 1, paso 4) |
| [GTAForums: color del radardisc](https://gtaforums.com/topic/908566-sa-how-can-you-change-the-color-of-the-radardisc-from-addresses/) | Usuarios del hilo | Referencia a `CHud::DrawRadar` (`0x58A330`) | Investigación del radar |
| [MTA:SA](https://github.com/multitheftauto/mtasa-blue) (`Client/game_sa/CHudSA.cpp`) | Equipo de Multi Theft Auto | Constantes compartidas de escala del HUD (`0x859520` = 1/640, `0x859524` = 1/448) y puntos de llamada de dibujo de cada elemento | Investigación del Modo 3 (posiciones) |
| [ugbase.eu: GTA SA memories](https://ugbase.eu/threads/gta-sa-memories.11170/) y [blast.hk 61958](https://www.blast.hk/threads/61958/) | Usuarios de los foros | Listas de direcciones de posición del HUD (dinero, salud, blindaje, estrellas, arma). **Sin verificar:** solo se vieron fragmentos | Investigación del Modo 3 (candidatas a verificar) |
| [GTAMods Wiki: Memory Addresses (SA)](https://gtamods.com/wiki/Memory_Addresses_(SA)) | Comunidad de GTAMods | Contraste de la dirección del dinero del jugador (`0xB7CE50`) | Modo 2 (dinero) |

## Otros recursos

- [Keep a Changelog](https://keepachangelog.com/es-ES/1.1.0/) y [Versionado semántico](https://semver.org/lang/es/): formato del registro de cambios y de los números de versión.

## Si falta alguien

Si reconoces información de tu trabajo que no está acreditada aquí, abre un *issue* en el repositorio y se añadirá.
