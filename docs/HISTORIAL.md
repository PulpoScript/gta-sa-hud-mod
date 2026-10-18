# Historial de desarrollo

Cómo se resolvió cada paso, incluidos los problemas. Sirve para no repetir errores.

## Paso 1: radar

Se usó el opcode `0581: display_radar`. Funcionó a la primera.

## Paso 2: icono de arma y munición

**Idea:** escribir `ret` (`0xC3`) al inicio de `CHud::DrawWeaponIcon` (`0x58D7D0`), guardando antes el byte original.

**Problema:** al añadir la lectura del byte original, el juego crasheaba al cargar la partida:

- Crash 1: `0x00486DDF` en `actorNearPoint` (lectura en `0x46C`, puntero nulo).
- Crash 2: `EIP = 0x00000000`.

El script no usaba ningún opcode de personajes, así que el crash no se explicaba por lo que hacía el código.

**Diagnóstico:** se descompiló el `.cs` y apareció `read_memory 6@ {size} 5822416 {vp} True {var_result} 0`. SB4 había compilado los parámetros en el orden escrito (ver [SANNY-BUILDER-4.md](SANNY-BUILDER-4.md)). El script leía la dirección 0 y escribía el resultado sobre un número, y eso desincronizaba la ejecución.

**Solución:** usar la forma del descompilador de SB4. El byte original resultó ser **161**.

Luego se vio que la munición seguía visible: se dibuja en otra función, `CHud::DrawAmmo` (`0x5893B0`). Su byte original es **83**.

## Paso 3: salud y blindaje

Se hizo barra por barra. `CHud::RenderHealthBar` (`0x589270`) parecía no responder. Se añadieron mensajes de diagnóstico, y con el juego reiniciado funcionó; el byte original es **131**. Lo más probable es que el fallo anterior se debiera a un parche que quedó en memoria de una versión previa.

Después se añadió `CHud::RenderArmorBar` (`0x5890A0`), con byte original **131**. Las dos barras comparten Shift + 3.

## Paso 4: dinero

El dinero no tiene función propia: se dibuja dentro de `CHud::DrawPlayerInfo`. Se partió del truco de Silent (NOPs en `0x58F607`), que no estaba documentado. Se verificó calculando que los bytes de esa dirección deben ser `E8 F4 B0 18 00` (`call CFont::PrintString`) y comprobándolo en el juego antes de parchear.

**Problema:** en la primera prueba, Shift + 4 no hacía nada y aparecía un mensaje que ya no existía en el código. El juego estaba ejecutando un `.cs` antiguo. Al reemplazar el archivo en `CLEO\`, funcionó.

## Paso 5: hora

Se pone a 0 el flag `CHud::bDrawClock` (`0xBAA400`). Al ser un dato y no código, no hace falta parchear instrucciones. Funcionó, y el juego no reescribe el flag por su cuenta.

## Selector de modos

Shift + 0 hace avanzar `19@` en ciclo y anuncia "Modo N". En la primera versión no se tocó la lógica del Modo 1, así que Shift + 1 a 5 funcionaban en todos los modos.

**Decisión:** al cambiar de modo, el HUD vuelve a su estado de fábrica y el nuevo modo empieza desde cero. Shift + 1 a 5 quedan limitados al Modo 1. Primero se implementó borrando los *pedidos* en el selector.

**Prueba:** el autor vio los elementos todavía ocultos en el Modo 2. El código era correcto y el comportamiento coincidía con la versión anterior, así que lo más probable es que el juego ejecutara el `MiMod.cs` viejo (el archivo se había renombrado a `UI`). Desde entonces el selector muestra "UI - Modo N" para identificar el script.

**Cambio:** el autor pidió que el Modo 1 recuerde sus elecciones. Ahora los *pedidos* no se borran: el *estado deseado* exige `19@ == 1`, de modo que fuera del Modo 1 todo se muestra y al volver se reaplica lo elegido.

## Modo 2 (0.2.0)

Se hizo elemento por elemento (`0.2.0-dev.1` a `dev.8`): dinero, arma, salud, aviso de poca salud, radar y hora. Detalle en [MODO2.md](MODO2.md).

- **Sin variables ni temporizadores libres:** se reutilizaron las variables de verificación del arranque, se unieron los indicadores "verificado" por parejas y se usó el reloj del juego (`0xB7CB84`) en lugar de temporizadores de CLEO.
- **SB4:** las operaciones entre dos variables necesitan el opcode explícito (`0062`, `005A`, `0085`); sin él da el error 0014.
- **Cambios tras probar:** la hora se vinculaba al dinero y a la hora del juego (dev.6); el autor prefirió que solo saliera con la tecla "." (dev.7). La pulsación larga bajó de 2 s a 0,8 s (dev.8).

## Lecciones generales

- **No inventar teorías sin evidencia:** descompilar resolvió en un paso lo que no se explicaba mirando el código.
- **Leer antes de escribir:** cada parche se verifica leyendo sus bytes reales antes de aplicarlo.
- **Una cosa cada vez:** cada paso se prueba solo antes de pasar al siguiente.
- **Mensajes temporales para obtener datos** (bytes originales y diagnóstico). Se quitan en cuanto ya no hacen falta.
