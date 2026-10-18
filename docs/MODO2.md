# Modo 2: HUD dinámico

> **Estado: completo y probado en la versión 0.2.0.**

**Idea:** la información no está siempre en pantalla. Cada elemento aparece cuando aporta algo (un cambio, un peligro, una acción) y se va al rato. La inspiración es GTA V y RDR2, pero con un estilo propio, no una copia.

Se construye **un elemento cada vez**, en versiones `0.2.0-dev.N` (ver [VERSIONES.md](VERSIONES.md)).

## Mecanismo común

Cada elemento usa un **temporizador**: un "evento" lo reinicia, y el elemento se ve mientras el temporizador no supere su tiempo de visibilidad (de momento, 5 s). La lógica va en el bloque "Estado deseado" de cada elemento, dentro de un `if 19@ == 2` (ver [ARQUITECTURA.md](ARQUITECTURA.md#cómo-añadir-un-modo-guía-para-el-modo-2-y-el-3)).

**Recursos limitados:** CLEO da 32 variables locales y 2 temporizadores (`32@` y `33@`) por script. El dinero usa `10@`, `31@` y `32@`; el arma, `33@`. Las 7 variables de verificación del arranque (`6@`, `9@`, `15@`, `17@`, `20@`, `21@`, `30@`) se reutilizan después. **Sin temporizadores libres:** desde la salud se usa el **reloj del juego** (`0xB7CB84`, ms): se guarda el momento del último cambio y se resta al momento actual. Para liberar variables, la hora se restaura siempre a 1 (su valor de fábrica) y la diferencia del dinero usa una temporal.

## Elementos

| # | Elemento | Se muestra cuando… (idea del autor) | Ideas para pulir | Estado |
|---|---|---|---|---|
| 1 | **Dinero** | Se gana o se pierde dinero | Mostrar también en tiendas. La hora podría ir con el dinero, como un bloque de "estado" | ✅ `0.2.0-dev.1`, probado (5 s aprobados) |
| 2 | **Arma + munición** | Se obtiene o cambia de arma, o se recoge munición | También al **apuntar, disparar o recargar** (como GTA V), y con poca munición | ✅ `0.2.0-dev.2`, probado (cambiar, conseguir, munición y disparar; recargar no cambia la munición total y apuntar queda pendiente) |
| 3 | **Salud + blindaje** | Se recibe daño o se regeneran | Que **se queden fijas con poca salud** (por ejemplo, < 25 %), como aviso | ✅ `0.2.0-dev.3` probado (daño y subida). ✅ `0.2.0-dev.4`: fijas con salud < 25, probado |
| 4 | **Radar** | Se oculta tras X tiempo quieto o en la misma zona; se muestra conduciendo | Decidido: visible **en vehículo**, **con nivel de búsqueda** o **al esprintar**; se oculta **8 s** después | ✅ `0.2.0-dev.5`, probado |
| 5 | **Hora** | Decidido tras probar: **solo con la tecla "."** (toque corto: hora 5 s). Ni con el dinero ni al cambiar la hora del juego | — | ✅ `0.2.0-dev.7`, probado |

## Ideas generales (para decidir más adelante)

- ✅ **Tecla de consulta "."** (`0.2.0-dev.8`): **toque corto** (menos de 0,8 s) → la hora 5 s; **mantener 0,8 s o más** → todo el HUD mientras siga pulsada, y se oculta a los 3 s de soltarla.
- ✅ **Al entrar en el Modo 2** (decidido, `0.2.0-dev.6`): se ve todo unos 3 s y luego se va ocultando.
- **Tiempos distintos por elemento**: por ejemplo, dinero 5 s, salud 8 s.
- **Aparición gradual**: el juego no permite atenuar elementos con estos parches (solo mostrar u ocultar). Sería una mejora para la versión ASI.

## Paso 5 (revisado): hora con la tecla "." (0.2.0-dev.7)

- **Cambio tras probar la 0.2.0-dev.6:** el autor no quiere la hora vinculada al dinero ni que aparezca al cambiar la hora del juego. Se quitó esa lógica.
- **Tecla ".":** toque corto (menos de 0,8 s; 2 s en la dev.7) → al soltar, la hora se ve 5 s. Mantener 0,8 s o más → todo el HUD (subrutina `MostrarTodo3s` en cada frame) y la hora; al soltar, todo se oculta a los 3 s.
- **Variable `18@` (codificada por signo):** `> 0` = tecla pulsada desde ese momento (ms); `< 0` = no pulsada y la hora se muestra desde el momento `-18@`; `0` = hora oculta. Así una sola variable guarda el estado de la tecla y el de la hora.
- **Entrada al Modo 2:** la hora también se ve 3 s (`18@ = -(ahora − 2000)`).

## Paso 5: hora, consulta y entrada al modo (0.2.0-dev.6) — sustituido por la 0.2.0-dev.7

- **Hora:** en el Modo 2 se oculta cuando el dinero lleva más de 5 s sin cambios (`32@ > 5000`), así que aparece y desaparece con él.
- **Cambio de hora del juego:** se lee `CClock::ms_nGameClockHours` (1 byte en `0xB70153`, plugin-sdk). Al cambiar, se reinicia `32@`, y dinero y hora aparecen juntos 5 s. La hora anterior se guarda en `18@`.
- **Subrutina `MostrarTodo3s`:** deja cada tiempo "a 3 s de ocultarse" (`32@` y `33@` = 2000; `27@` = ahora − 2000; `11@` = ahora − 5000). Se llama al entrar en el Modo 2 y en cada frame con la tecla "." pulsada (`0xBE`, `VK_OEM_PERIOD`; el punto del teclado numérico es otra tecla).
- **Variables:** `18@` deja de estar libre. No queda ninguna variable libre.

## Paso 4: radar (0.2.0-dev.5) ✅

- **Decisiones del autor:** 8 s; a pie solo cuenta **esprintar** (caminar o correr no lo muestran); también se muestra con **nivel de búsqueda**. "Estar en la misma zona" queda cubierto por "8 s sin motivo".
- **Datos** (plugin-sdk, solo lectura):
  - Vehículo: `bInVehicle` = bit 0 del byte en jugador + `0x46D` (indicadores de `CPed` desde `0x46C`; `bInVehicle` es el noveno).
  - Esprintar: jugador + `0x534` = `m_nMoveState`; `7` = `PEDMOVE_SPRINT`.
  - Búsqueda: jugador + `0x480` → `CPlayerData*`; `+0x0` → `CWanted*`; `+0x2C` = `m_nWantedLevel`.
- **Tiempo:** cada motivo guarda en `11@` el reloj del juego; el radar se oculta si `ahora − 11@ > 8000`.
- **Variables:** para tener `11@`, los indicadores "verificado" de icono + munición se unieron en `8@`, y los de salud + blindaje en `16@`. `18@` queda libre.
- **Opcode nuevo:** `08B7: test 9@ bit 0` (comprobar un bit). Funciona en el juego (prueba de vehículo superada).

## Paso 3: salud + blindaje (0.2.0-dev.3) ✅

- **Datos** (plugin-sdk, solo lectura): salud en jugador + `0x540` y blindaje en jugador + `0x548`, ambos `float`.
- **Firma:** la subrutina `FirmaSalud` suma los bits de los dos float. Cualquier cambio (daño, curación, chaleco) la cambia.
- **Tiempo:** al cambiar se guarda en `27@` el reloj del juego. Las barras se ocultan cuando `reloj actual − 27@ > 5000`.
- **Poca salud (0.2.0-dev.4):** con menos de **25 puntos** de salud las barras se quedan visibles. Como los float positivos se ordenan igual que sus bits, se comparan los bits de la salud con los de 25.0 (`1103626240`). El umbral es en puntos, no en porcentaje: con la salud máxima ampliada (hasta 176), 25 puntos son menos del 25 %.

## Paso 2: arma + munición (0.2.0-dev.2) ✅

- **Datos** (plugin-sdk, `VALIDATE_OFFSET`, solo lectura): puntero al jugador en `0xB7CD98`; ranura en la mano en jugador + `0x718` (1 byte); 13 ranuras en jugador + `0x5A0`, de `0x1C` bytes cada una, con el tipo en `+0x0` y la munición total en `+0xC`.
- **Firma:** la subrutina `FirmaArmas` suma la ranura en la mano y el tipo y la munición de las 13 ranuras. Cualquier cambio (otra arma, arma nueva, munición, disparo) cambia la suma. Recargar probablemente no, porque la munición total incluye el cargador. Dos cambios que se anulen en el mismo frame no se detectarían, pero en la práctica no pasa.
- **Regla:** en el Modo 2, el arma se oculta si `33@ > 5000`.
- **Seguridad:** si el puntero al jugador es 0, no se lee nada.

## Paso 1: dinero (0.2.0-dev.1) ✅

- **Dato:** dinero real del jugador en `0xB7CE50` (4 bytes, solo lectura). Es `CWorld::Players` (`0xB7CD98`) más `m_nMoney` (`0xB8`), verificado en plugin-sdk.
- **Detección:** en cada frame se resta el valor anterior (`10@`) al actual; si la diferencia no es 0, se reinicia `32@` y se guarda el valor nuevo. Se hace por diferencia porque SB4 da error al comparar dos variables.
- **Regla:** en el Modo 2, el dinero se oculta si `32@ > 5000` (más de 5 s sin cambios).
- **Al cargar la partida:** se ve 5 s, porque el temporizador arranca en 0.
- **Nota:** el contador del juego sube poco a poco (`m_nDisplayMoney`). Con una ganancia grande, la animación podría durar más de 5 s y cortarse; si pasa, se ajusta el tiempo.
