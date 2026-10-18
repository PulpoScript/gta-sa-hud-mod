# Investigación: posición, tamaño y forma del HUD

Respuestas a las preguntas abiertas sobre hasta dónde se puede modificar el HUD. **Nada de esto está probado en el juego todavía.** Cada punto indica qué está verificado en código fuente público y qué falta comprobar.

## 1. ¿El Modo 1 puede recordar su estado al cambiar de modo?

**Sí, y ya está implementado** (ver [ARQUITECTURA.md](ARQUITECTURA.md#selector-de-modos)). Las elecciones del Modo 1 se guardan en los *pedidos* (`1@`, `4@`, `12@`, `23@` y `26@`), que ya no se borran al cambiar de modo. El *estado deseado* de cada elemento exige estar en el Modo 1 (`19@ == 1`), así que en los Modos 2 y 3 todo se muestra, y al volver al Modo 1 se vuelve a ocultar lo que estaba elegido.

## 2. ¿Dónde se dibuja exactamente la hora?

Verificado en gta-reversed ([`Hud.cpp`, `CHud::DrawClock`](https://github.com/gta-reversed/gta-reversed/blob/master/source/game_sa/Hud.cpp)):

```cpp
CFont::SetScale(SCREEN_STRETCH_X(0.55f), SCREEN_STRETCH_Y(1.1f));
CFont::SetFontStyle(FONT_PRICEDOWN);
CFont::SetOrientation(ALIGN_RIGHT);
CFont::PrintString(SCREEN_STRETCH_FROM_RIGHT(32.0f), SCREEN_STRETCH_Y(22.0f), "HH:MM");
```

El HUD de SA usa una pantalla virtual de **640 × 448** unidades que se estira a la resolución real:

| Dato | Valor en unidades | Significado |
|---|---|---|
| X | `640 − 32` | El **borde derecho** del texto queda a 32 unidades del borde derecho (alineado a la derecha) |
| Y | `22` | 22 unidades desde arriba |
| Escala de letra | `0.55 × 1.1` | Fuente Pricedown |

Conversión a píxeles: `X = ancho − 32 × ancho / 640` e `Y = 22 × alto / 448`. Por ejemplo, a 1920 × 1080: X = 1824 px (borde derecho del texto) e Y ≈ 53 px.

Los demás elementos siguen el mismo sistema. Por ejemplo, el dinero está en X = `640 − 32` e Y = 89, aunque esa Y se desplaza si la barra de salud es más larga (`GetYPosBasedOnHealth` en `CHud::DrawMoney`).

## 3. ¿Se puede cambiar la forma del radar?

**Sí, es posible, pero exige varios cambios.** Verificado en gta-reversed ([`Radar.cpp`](https://github.com/gta-reversed/gta-reversed/blob/master/source/game_sa/Radar.cpp)):

| Pieza | Función / dirección | Qué hace |
|---|---|---|
| Máscara circular | `CRadar::DrawRadarMask` `0x585700` | Dibuja las **esquinas fuera del círculo** para tapar el mapa. Sin ella, el mapa se ve **cuadrado**. |
| Límite de iconos | `CRadar::LimitRadarPoint` `0x5832F0` | Recorta los iconos (blips) lejanos al **borde del círculo** (normaliza a radio 1). |
| Aro del radar | Sprites "radardisc" en `CHud::DrawRadar` `0x58A330` (dirección confirmada en plugin-sdk y gta-reversed) | Dibuja el aro circular alrededor. |

### Radar cuadrado (plan)

1. **Mapa cuadrado:** parche `ret` en `CRadar::DrawRadarMask` (`0x585700`), con la misma técnica segura que ya usamos. Según gta-reversed es una función sin parámetros, así que un `ret` no desbalancea la pila. Falta el **byte original** (lo da `experimentos/explorar_hud.txt`).
2. **Iconos en el borde:** seguirían recortados a un círculo dentro del cuadrado. Para recortarlos a un cuadrado haría falta reemplazar `LimitRadarPoint`, y eso no se puede hacer con un parche de 1 byte. Hay que investigarlo.
3. **Aro:** seguiría siendo redondo. Opciones: ocultarlo (requiere localizar sus llamadas dentro de `CHud::DrawRadar`) o sustituir la textura `radardisc` en `hud.txd` (con modloader, sin código).

### Radar rectangular

El ancho y el alto del radar se calculan por separado. WidescreenFix ([`Legacy.ixx`](https://github.com/ThirteenAG/WidescreenFixesPack/blob/master/source/GTASA.WidescreenFix/Legacy.ixx)) cambia esas escalas en `0x58A441` (ancho del plano) y `0x58A473` (alto del plano), además de otras 20 instrucciones del aro, los iconos y la traza. Estirando solo el ancho se obtiene un radar **ovalado**. Combinado con el radar cuadrado, quedaría **rectangular**.

## 4. ¿Se pueden mover elementos a otra parte de la pantalla?

**Sí, es técnicamente posible, pero es lo más complejo.** Hay dos caminos:

### A. Cambiar la escala (verificado en WidescreenFix)

Cada elemento usa instrucciones del tipo `fmul dword ptr [float]` que multiplican por `1/640` (ancho) o `1/448` (alto). WidescreenFix sustituye el **puntero** de esas instrucciones (en `instrucción + 2`) para que apunten a un float propio. Lista parcial de [`Legacy.ixx`](https://github.com/ThirteenAG/WidescreenFixesPack/blob/master/source/GTASA.WidescreenFix/Legacy.ixx):

| Elemento | Instrucciones de ancho | Instrucciones de alto |
|---|---|---|
| Hora | `0x58EB3F`, `0x58EC0C` | `0x58EB29`, `0x58EBF9` |
| Dinero | `0x58F55C`, `0x58F5F4` | `0x58F546`, `0x58F5CE` |
| Barras de salud, blindaje y oxígeno | `0x5892CA`, `0x58937E`, `0x58EE7E`, `0x58EEF4`, `0x589155`, `0x58EF50`, `0x58EFC5`, `0x58922D`, `0x58F116`, `0x58F194` | `0x589346`, `0x58EE60`, `0x588B9C`, `0x58EEC8`, `0x58913E`, `0x58EF32`, `0x58EF99`, `0x589216`, `0x58F0F8`, `0x58F168` |
| Icono de arma | `0x58D92D`, `0x58D8C3`, `0x58F91C`, `0x58F92D` | `0x58D945`, `0x58D882`, `0x58F90B` |
| Munición | `0x5894C5`, `0x5894E9`, `0x58F9D0`, `0x58FA5D`, `0x58F9F5`, `0x58FA8E` | `0x5894AF`, `0x58F9C0`, `0x58FA4A` |
| Estrellas de búsqueda | `0x58DCB8`, `0x58DD00`, `0x58DD7E`, `0x58DF71`, `0x58DFE5` | `0x58DCA2`, `0x58DD68`, `0x58DDF4`, `0x58DF55`, `0x58DF9B`, `0x58DEE4` |

Como la posición también se calcula con esa escala (`X = ancho − 32 × escala`), cambiarla **desplaza y redimensiona a la vez** el elemento respecto a su esquina de anclaje. Sirve para ajustes, no para llevar un elemento a otra esquina.

**Lo que falta para usarlo desde CLEO:**
- Un float propio en memoria. CLEO puede reservar memoria (`0AC8`), pero la sintaxis en SB4 no está verificada.
- Comprobar en tu ejecutable que cada instrucción sea realmente `fmul [mem]` (`D8 0D`) y que apunte a `1/640` o `1/448`. Eso lo hace `experimentos/explorar_hud.txt`.
- Saber cuál de las dos instrucciones de cada elemento es la posición y cuál el tamaño de letra.

### B. Cambiar las coordenadas base (sin verificar)

Las constantes `32.0`, `22.0`, `89.0`, etc. están en la memoria del ejecutable, pero **no tenemos sus direcciones**, y algunas podrían compartirse con otros elementos. Haría falta un desensamblador (IDA o Ghidra) sobre `gta_sa.exe` 1.0 US o una fuente fiable. No se debe probar a ciegas.

### C. Dibujar los elementos uno mismo (alternativa)

Ocultar el elemento original (como ya hacemos) y dibujar uno propio donde se quiera, con textos y rectángulos de CLEO o CLEO+. Así se controla la posición, el color y el comportamiento sin parchear el motor. Es la vía más flexible para "otra parte de la pantalla con otro comportamiento". Su coste: hay que reproducir cada elemento (por ejemplo, una barra de salud con `CPed + 0x540`).

## Recomendación de orden

1. Pasar `experimentos/explorar_hud.txt` y anotar los valores.
2. Radar cuadrado, paso 1 (`ret` en `DrawRadarMask`): es lo más simple y usa la técnica ya probada.
3. Elegir entre A (escalas) y C (dibujo propio) según el resultado que se quiera.

## Fuentes

- gta-reversed: [`Hud.cpp`](https://github.com/gta-reversed/gta-reversed/blob/master/source/game_sa/Hud.cpp), [`Radar.cpp`](https://github.com/gta-reversed/gta-reversed/blob/master/source/game_sa/Radar.cpp)
- WidescreenFixesPack (ThirteenAG): [`GTASA.WidescreenFix/Legacy.ixx`](https://github.com/ThirteenAG/WidescreenFixesPack/blob/master/source/GTASA.WidescreenFix/Legacy.ixx)
- GTAForums: [cambiar el color del radardisc desde direcciones](https://gtaforums.com/topic/908566-sa-how-can-you-change-the-color-of-the-radardisc-from-addresses/) (menciona `CHud::DrawRadar` en `0x58A330`)
