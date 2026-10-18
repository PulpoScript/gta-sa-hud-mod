# Arquitectura

Explica cómo funciona `src/UI.txt` por dentro.

## Visión general

El script tiene dos fases:

1. **Inicio** (una vez, 3 s después de cargar la partida). Lee la memoria del juego para comprobar que cada parche se puede aplicar con seguridad.
2. **Bucle principal** (cada frame). Lee teclas, calcula qué debe estar oculto y aplica los cambios.

```
cargar partida
      │
      ▼
 wait 3000 ──► verificar bytes originales ──► marcar elementos disponibles
      │
      ▼
 ┌──────────────── bucle (cada frame) ────────────────┐
 │  F11 (testing)  → enciende / apaga el mod          │
 │  Shift+0        → cambia de modo                   │
 │  por cada elemento del HUD:                        │
 │     tecla → estado deseado → aplicar si cambió     │
 └────────────────────────────────────────────────────┘
```

## Patrón común de cada elemento

Cada elemento del HUD (radar, arma, barras, dinero y hora) es una sección independiente con tres partes y tres variables:

| Variable | Significado |
|---|---|
| **pedido** | Lo que el usuario eligió con su tecla (1 = ocultar). |
| **deseado** | Se recalcula cada frame: `1` solo si **el mod está activo**, **el modo es el 1** *y* el usuario lo pidió. |
| **aplicado** | Lo que ya se escribió en el juego. |

1. **Tecla:** con el mod activo, Shift + número alterna el *pedido*. Después se espera a que se suelte la tecla (antirrebote).
2. **Estado deseado:** `deseado = (mod activo) Y (Modo 1) Y (pedido)`.
3. **Aplicar solo si cambió:**
   - `deseado = 1` y `aplicado = 0` → ocultar y `aplicado = 1`.
   - `deseado = 0` y `aplicado = 1` → mostrar y `aplicado = 0`.

### Ventajas

- **El juego solo se toca cuando hay un cambio**, no en cada frame.
- **Al apagar el mod, todo se restaura solo:** con `0@ = 0`, todos los *deseado* pasan a 0 y la parte de aplicar restaura lo que hubiera oculto. No hace falta código extra.
- **Se recuerda la elección:** el *pedido* se conserva al apagar y encender el mod.
- **Preparado para más modos:** para añadir un modo solo hay que cambiar cómo se calcula *deseado*. La parte que escribe en memoria no cambia.

## Técnicas para ocultar

| Técnica | Elementos | Cómo funciona | Cómo se restaura |
|---|---|---|---|
| **Opcode del juego** | Radar | `0581: display_radar 0/1` | `display_radar 1` |
| **Parche `ret`** | Icono de arma, munición, salud, blindaje | Se escribe `0xC3` (`ret`) en el primer byte de la función de dibujo. La función sale sin dibujar nada. | Se escribe el byte original |
| **Parche NOP** | Dinero | El dinero no tiene función propia, así que se sustituye la instrucción `call CFont::PrintString` (5 bytes) por 5 × `0x90` (NOP). | Se escriben los 5 bytes originales |
| **Flag del juego** | Hora | Se pone a 0 el `bool` `CHud::bDrawClock` | Se escribe el valor guardado al ocultar |

### Por qué el parche `ret` es seguro

Las funciones parcheadas son `cdecl`: **quien llama limpia la pila**. Un `ret` al principio devuelve el control sin tocar la pila, así que la función que llamó sigue funcionando con normalidad. En una función `stdcall`, que limpia su propia pila, un `ret` sin más la desbalancearía y el juego crashearía.

### Por qué el parche NOP es seguro

`CFont::PrintString` también es `cdecl`. Los argumentos se meten en la pila antes de la llamada, y la función que llama los limpia después. Al quitar solo la instrucción `call`, la pila queda igual de balanceada.

### `vp` (virtual protect)

- Escribir en **código** necesita `{vp} True`, porque esas páginas de memoria son de solo lectura y ejecución.
- Escribir en **datos** (`bDrawClock`) usa `{vp} False`.
- Para **leer**, siempre se usa `{vp} False`.

## Protección al cargar partida

**Problema:** al cargar una partida, CLEO reinicia el script desde cero, pero los parches siguen escritos en la memoria del proceso. Sin protección, un elemento oculto se quedaría oculto para siempre.

**Solución:** al arrancar se lee cada dirección y se decide según lo que haya:

| Valor leído | Interpretación | Acción |
|---|---|---|
| Byte original conocido | Juego limpio | Marcar el elemento como **disponible** |
| Byte del parche (`195` o `0x90`) | Quedó un parche de antes de cargar | **Restaurar** el original y marcar como disponible |
| Cualquier otro valor | Ejecutable distinto a 1.0 US | **No tocar nunca** esa dirección y avisar en pantalla |

Para la hora (un dato, no código): si el flag vale 0 al arrancar, se pone a 1, que es el valor con el que el juego lo inicia.

Las variables de **disponible** (`8@`, `11@`, `16@`, `18@` y `22@`) protegen cada escritura: si un elemento no está verificado, su parche nunca se aplica.

## Selector de modos

`19@` guarda el modo (1, 2 o 3). Con el mod activo, Shift + 0 lo hace avanzar en ciclo y muestra "Modo N".

**HUD de fábrica al cambiar de modo:** el *estado deseado* de cada elemento del Modo 1 exige `19@ == 1`. Al pasar a otro modo, todos los *deseado* valen 0 y cada sección restaura lo oculto en ese mismo frame.

**Recuerdo del Modo 1:** los *pedidos* (`1@`, `4@`, `12@`, `23@` y `26@`) **no se borran**. Al volver al Modo 1, los *deseado* vuelven a valer 1 donde corresponda y se oculta otra vez lo que estaba elegido.

**Identificación:** el mensaje del selector es "UI - Modo N". Si sale solo "Modo N", el juego está ejecutando un `.cs` antiguo.

**Teclas del Modo 1:** la parte "Tecla" de cada elemento exige `0@ == 1` **y** `19@ == 1`. En los Modos 2 y 3, Shift + 1 a 5 no hacen nada.

**Para añadir un modo:** su regla se suma al cálculo del *estado deseado* de cada elemento (por ejemplo, `deseado = 1` si `19@ == 2` y se cumple su condición). La parte que escribe en memoria no cambia.

## Cómo añadir un modo (guía para el Modo 2 y el 3)

Cada elemento del HUD tiene tres bloques en `src/UI.txt`: **Tecla**, **Estado deseado** y **Aplicar**. Un modo nuevo **solo toca "Estado deseado"**, en el lugar marcado `PUNTO DE EXTENSIÓN`. La parte que escribe en memoria ya está hecha y probada.

Plantilla para un elemento (ejemplo con el radar):

```
    // ---- Estado deseado ... ----
    2@ = 0
    if and                 // Modo 1 (ya existe)
        0@ == 1
        19@ == 1
    then
        if 1@ == 1
        then
            2@ = 1
        end
    end
    if and                 // Modo 2 (nuevo)
        0@ == 1
        19@ == 2
    then
        // condición del Modo 2: si se cumple, ocultar
        if <condición>
        then
            2@ = 1
        end
    end
```

Reglas:

- **Deseado = 1 significa oculto.** Si ninguna regla lo pone a 1, el elemento se ve.
- **Cada modo, en su propio `if 19@ == N`.** Así los modos no se mezclan.
- **Las condiciones se evalúan en cada frame.** Para que un elemento aparezca unos segundos tras una acción del jugador, hace falta un temporizador (`32@` y `33@` son temporizadores automáticos de CLEO que cuentan milisegundos).
- **Variables:** solo quedan `10@` y `31@` libres (ver [VARIABLES.md](VARIABLES.md)). Si el Modo 2 necesita más, ver [CLEO-VS-ASI.md](CLEO-VS-ASI.md).
- **Las condiciones nuevas se verifican antes de usarlas**, como las direcciones: cada opcode nuevo se descompila la primera vez.

## Modo de pruebas (F11)

El bloque marcado con `// ===== TESTING ... =====` enciende y apaga el mod (`0@`) y muestra "UI v0.2.0 activado / desactivado" con la versión cargada. No usa `add_one_off_sound`, porque el cuadro de texto ya suena por sí solo y con los dos sonaban dos sonidos a la vez. Existe solo para probar. En la versión final se borra ese bloque y se pone `0@ = 1` en la sección de variables.
