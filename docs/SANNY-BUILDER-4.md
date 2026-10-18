# Notas de Sanny Builder 4

Lo que funciona, lo que no y cómo comprobarlo.

## ⚠️ La forma antigua de los opcodes no reordena parámetros

En Sanny Builder 4, si escribes el número de opcode con el texto antiguo, **los parámetros se compilan en el orden en que aparecen escritos** y el texto se descarta.

Ejemplo real que crasheaba el juego:

```
0A8D: 6@ = read_memory 0x58D7D0 size 1 virtual_protect 0
```

SB4 lo compiló así:

```
read_memory 6@ {size} 5822416 {vp} True {var_result} 0
```

| Parámetro | Debía ser | Quedó |
|---|---|---|
| dirección | `0x58D7D0` | `6@` (valía 0 → dirección nula) |
| tamaño | `1` | `5822416` |
| vp | `0` | `1` |
| resultado | `6@` | el número `0` |

Leer la dirección 0 y guardar el resultado en un número desincronizó el script. El juego crasheaba en opcodes que el script no usaba (`actorNearPoint`, `EIP = 0`).

`018C: play_sound 1058 at 0.0 0.0 0.0` tenía el mismo problema: el `1058` acababa como coordenada X y el sonido era el 0.

## Sintaxis que funciona

Usar la forma del descompilador de SB4. Lo que va entre `{ }` es un comentario (solo una etiqueta).

```
6@ = read_memory 0x58D7D0 {size} 1 {vp} False
write_memory 0x58D7D0 {size} 1 {value} 195 {vp} True
write_memory 0x58F607 {size} 4 {value} -1869574000 {vp} True
add_one_off_sound 0.0 {y} 0.0 {z} 0.0 {soundId} 1058
```

Opcodes con la forma antigua que **sí** compilan bien, porque sus parámetros ya van en orden:

```
0AB0: key_pressed 0x7A
03E5: text_box 'CHEAT1'
0ACE: show_formatted_text_box "Modo %d" 19@
0581: display_radar 0
```

## Otras reglas

- **Operaciones entre dos variables** (`31@ -= 10@`) también dan el error 0014, porque SB4 no sabe su tipo. Solución: escribir el opcode del tipo entero: `0062: 31@ -= 10@` (resta) y `005A: 10@ += 31@` (suma). Tienen dos parámetros en orden, así que no hay riesgo de desorden. Verificado: se descompilan como `sub_int_lvar_from_int_lvar 31@ -= 10@` y `add_int_lvar_to_int_lvar 10@ += 31@`. El temporizador `32@` aparece como `TIMERA`.
- **No comparar dos variables entre sí** (`if not 2@ == 3@` da el error 0014). Se compara una variable con un número y se combinan condiciones con `if and`.
- `03E5: text_box 'CHEAT1'` muestra "Truco activado". **`'CHEAT2'` no es "desactivado"**: es el texto del truco de armas. Para textos propios se usa `0ACE: show_formatted_text_box "..."`.
- Los valores de 4 bytes que pasan de `0x7FFFFFFF` se escriben como entero con signo (`0x90909090` → `-1869574000`).

## Cómo verificar una compilación

1. Compila.
2. Copia el `.cs` compilado a una carpeta vacía, sin ningún `.txt` al lado, y ábrelo en SB4 con **Archivo → Abrir**. Si hay un `.txt` junto al `.cs`, SB4 puede mostrar el fuente en lugar del descompilado.
3. Se reconoce un descompilado auténtico porque **no tiene comentarios** y usa etiquetas `:Noname_...` y `goto_if_false`.
4. Comprueba que cada `read_memory` / `write_memory` tenga **la dirección primero**, después `{size}` y luego el valor.

Hay que descompilar cuando se usa un opcode o un formato nuevo por primera vez. Para cambios que solo repiten formas ya comprobadas, no hace falta.

## Antes de probar en el juego

- Comprueba que el `.cs` de `CLEO\` sea el que acabas de compilar (mira la fecha).
- Comprueba que no haya copias duplicadas del mod en `CLEO\`.
- Si ves un mensaje que el código actual ya no contiene, el juego está ejecutando un `.cs` antiguo.
