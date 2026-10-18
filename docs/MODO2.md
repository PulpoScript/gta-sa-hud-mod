# Modo 2: HUD dinámico

**Idea:** la información no está siempre en pantalla. Cada elemento aparece cuando aporta algo (un cambio, un peligro, una acción) y se va al rato. La inspiración es GTA V y RDR2, pero con un estilo propio, no una copia.

Se construye **un elemento cada vez**, en versiones `0.2.0-dev.N` (ver [VERSIONES.md](VERSIONES.md)).

## Mecanismo común

Cada elemento usa un **temporizador**: un "evento" lo reinicia, y el elemento se ve mientras el temporizador no supere su tiempo de visibilidad (de momento, 5 s). La lógica va en el bloque "Estado deseado" de cada elemento, dentro de un `if 19@ == 2` (ver [ARQUITECTURA.md](ARQUITECTURA.md#cómo-añadir-un-modo-guía-para-el-modo-2-y-el-3)).

**Recursos limitados:** CLEO da 32 variables locales y 2 temporizadores (`32@` y `33@`) por script. El dinero ya usa las 2 variables que quedaban libres y el temporizador `32@`. Para los demás elementos habrá que decidir entre **compartir temporizador** (por ejemplo, un temporizador de "actividad" común) o **dividir el mod en varios scripts**.

## Elementos

| # | Elemento | Se muestra cuando… (idea del autor) | Ideas para pulir | Estado |
|---|---|---|---|---|
| 1 | **Dinero** | Se gana o se pierde dinero | Mostrar también en tiendas. La hora podría ir con el dinero, como un bloque de "estado" | ⚠️ `0.2.0-dev.1`, sin probar |
| 2 | **Arma + munición** | Se obtiene o cambia de arma, o se recoge munición | También al **apuntar, disparar o recargar** (como GTA V), y con poca munición | ⏳ |
| 3 | **Salud + blindaje** | Se recibe daño o se regeneran | Que **se queden fijas con poca salud** (por ejemplo, < 25 %), como aviso | ⏳ |
| 4 | **Radar** | Se oculta tras X tiempo quieto o en la misma zona; se muestra conduciendo | Mostrar también con **nivel de búsqueda**, al correr o al tener un objetivo de misión | ⏳ |
| 5 | **Hora** | Por definir | Junto con el dinero, o solo al "consultar" | ⏳ |

## Ideas generales (para decidir más adelante)

- **Tecla de "consulta"**: mantener una tecla para ver todo el HUD un momento, como en RDR2.
- **Al entrar en el Modo 2**: mostrar todo 2–3 s para orientarse y que luego se vaya ocultando.
- **Tiempos distintos por elemento**: por ejemplo, dinero 5 s, salud 8 s.
- **Aparición gradual**: el juego no permite atenuar elementos con estos parches (solo mostrar u ocultar). Sería una mejora para la versión ASI.

## Paso 1: dinero (0.2.0-dev.1)

- **Dato:** dinero real del jugador en `0xB7CE50` (4 bytes, solo lectura). Es `CWorld::Players` (`0xB7CD98`) más `m_nMoney` (`0xB8`), verificado en plugin-sdk.
- **Detección:** en cada frame se resta el valor anterior (`10@`) al actual; si la diferencia no es 0, se reinicia `32@` y se guarda el valor nuevo. Se hace por diferencia porque SB4 da error al comparar dos variables.
- **Regla:** en el Modo 2, el dinero se oculta si `32@ > 5000` (más de 5 s sin cambios).
- **Al cargar la partida:** se ve 5 s, porque el temporizador arranca en 0.
- **Nota:** el contador del juego sube poco a poco (`m_nDisplayMoney`). Con una ganancia grande, la animación podría durar más de 5 s y cortarse; si pasa, se ajusta el tiempo.
