# Control del HUD para GTA San Andreas

Script CLEO para **GTA San Andreas PC 1.0 US** que permite mostrar y ocultar a voluntad cada parte de la interfaz (HUD): radar, arma, barras, dinero y hora.

> **Versión actual: 0.2.0 (beta).** **Estado:** en desarrollo. Los Modos 1 (manual) y 2 (dinámico) están completos y probados en el juego. El Modo 3 solo tiene el selector; su lógica aún no existe. Todavía no hay ninguna versión publicada (release).

## Contenido

- [Requisitos](#requisitos)
- [Instalación](#instalación)
- [Controles](#controles)
- [Modos](#modos)
- [Cómo se comporta en el juego](#cómo-se-comporta-en-el-juego)
- [Estado del proyecto](#estado-del-proyecto)
- [Documentación técnica](#documentación-técnica)
- [Estructura del repositorio](#estructura-del-repositorio)
- [Créditos](#créditos)
- [Aviso legal](#aviso-legal)
- [Licencia](#licencia)

## Requisitos

| Componente | Versión |
|---|---|
| GTA San Andreas PC | **1.0 US**. Otras versiones no son compatibles: las direcciones de memoria cambian. |
| CLEO | 4.4 |
| Plugin CLEO+ | `CLEO+.cleo` en `cleo\cleo_plugins\` |
| Sanny Builder | 4 (para compilar) |

Si el ejecutable no es 1.0 US, el script **no parchea nada**. Desactiva por su cuenta el elemento afectado y muestra un aviso en pantalla (ver [Protección al cargar partida](docs/ARQUITECTURA.md#protección-al-cargar-partida)).

## Instalación

Aún no hay archivos compilados publicados, así que hay que compilar el script.

1. Abre `src/UI.txt` en **Sanny Builder 4**.
2. Compila. Se genera `UI.cs`.
3. Copia `UI.cs` a la carpeta `CLEO\` del juego.
4. Comprueba que en `CLEO\` haya **una sola** copia del mod y que su fecha sea la de la compilación que acabas de hacer.

## Controles

| Tecla | Acción |
|---|---|
| **F11** | Enciende o apaga el mod. *Temporal, solo para pruebas; se eliminará en la versión final.* |
| **Shift + 0** | Cambia de modo: 1 → 2 → 3 → 1. Muestra "UI - Modo N" en pantalla y devuelve el HUD a su estado de fábrica (todo visible). |
| **"."** | Solo en Modo 2: **toque corto** muestra la hora 5 s; **mantener 0,8 s** muestra todo el HUD |
| **Shift + 1** | Solo en Modo 1: mostrar u ocultar el **radar** |
| **Shift + 2** | Solo en Modo 1: mostrar u ocultar el **icono de arma y la munición** |
| **Shift + 3** | Solo en Modo 1: mostrar u ocultar las **barras de salud y blindaje** |
| **Shift + 4** | Solo en Modo 1: mostrar u ocultar el **dinero** |
| **Shift + 5** | Solo en Modo 1: mostrar u ocultar la **hora** |

Al apagar el mod con F11, todo lo oculto vuelve a mostrarse.

## Modos

| Modo | Nombre | Estado |
|---|---|---|
| 1 | Manual | ✅ Completo: cada elemento se alterna con Shift + número |
| 2 | Dinámico: cada elemento aparece solo cuando pasa algo ([concepto](docs/MODO2.md)) | ✅ Completo: dinero, arma, salud, radar y hora con la tecla "." |
| 3 | Por definir | ⏳ Solo selector |

**Al cambiar de modo**, el HUD vuelve a su estado de fábrica (todo visible) y el nuevo modo empieza desde cero. **El Modo 1 recuerda sus elecciones:** al volver a él, se oculta otra vez lo que tenías oculto.

## Cómo se comporta en el juego

### Al empezar

El mod arranca **apagado**: el HUD se ve como siempre. Pulsa **F11** para encenderlo ("UI v0.2.0 activado"). Empieza en el **Modo 1**. Con **Shift + 0** cambias de modo y aparece "UI - Modo N" en pantalla.

### Modo 1: manual

Tú decides qué se ve. Cada combinación oculta un elemento y, si la pulsas otra vez, lo vuelve a mostrar. Un elemento oculto **se queda oculto** hasta que lo vuelvas a pulsar, pase lo que pase en el juego.

| Tecla | Elemento |
|---|---|
| Shift + 1 | Radar |
| Shift + 2 | Icono de arma + munición |
| Shift + 3 | Barras de salud + blindaje |
| Shift + 4 | Dinero |
| Shift + 5 | Hora |

Si cambias a otro modo y luego vuelves, el Modo 1 recuerda lo que tenías oculto.

### Modo 2: dinámico

La pantalla queda limpia. Cada elemento **aparece solo cuando pasa algo** y se oculta al rato. Shift + 1 a 5 no hacen nada en este modo.

| Elemento | Aparece cuando… | Se oculta… |
|---|---|---|
| Dinero | Ganas o gastas dinero | 5 s después del último cambio |
| Arma + munición | Cambias de arma, consigues un arma o munición, o disparas | 5 s después del último cambio |
| Salud + blindaje | Recibes daño o se recuperan | 5 s después; **se quedan fijas** si la salud baja de 25 |
| Radar | Vas en un vehículo, tienes nivel de búsqueda o esprintas | 8 s después de la última de esas situaciones |
| Hora | Solo si la pides con la tecla "." | 5 s después |

**Tecla "." (consulta):**
- **Toque corto:** muestra solo la hora durante 5 s.
- **Mantener 0,8 s:** muestra **todo** el HUD mientras la mantengas. Al soltarla, todo se oculta a los 3 s.

Al entrar en el Modo 2 se ve todo unos 3 s, para orientarte, y luego cada elemento se va ocultando.

### Modo 3

Aún no está definido. Por ahora el HUD se ve completo, como en el juego original.

### Al apagar el mod o cargar partida

- **F11 (apagar):** todo el HUD vuelve a verse al instante.
- **Cargar partida:** el mod restaura cualquier elemento que se hubiera quedado oculto. Si detecta un ejecutable que no es 1.0 US, avisa en pantalla y no toca ese elemento.

## Estado del proyecto

### Hecho

- [x] Paso 1: radar
- [x] Paso 2: icono de arma + munición
- [x] Paso 3: barras de salud + blindaje
- [x] Paso 4: dinero
- [x] Paso 5: hora
- [x] Selector de modos (Shift + 0), con reinicio del HUD al cambiar
- [x] Shift + 1 a 5 limitados al Modo 1
- [x] El Modo 1 recuerda sus elecciones al cambiar de modo
- [x] Restauración automática si se carga partida con elementos ocultos

### Pendientes

- [ ] Definir y programar el Modo 2
- [ ] Pasar `experimentos/explorar_hud.txt` (solo lectura) para los datos de radar y escalas
- [ ] Radar cuadrado o rectangular y reubicar elementos (ver [INVESTIGACION.md](docs/INVESTIGACION.md))
- [ ] Definir y programar el Modo 3
- [ ] Versión final: quitar F11 y arrancar con el mod activo (`0@ = 1`)
- [ ] Publicar la primera versión (release)

## Documentación técnica

| Documento | Contenido |
|---|---|
| [docs/ARQUITECTURA.md](docs/ARQUITECTURA.md) | Funcionamiento interno del script: patrón de estados, técnicas para ocultar y protección al cargar |
| [docs/DIRECCIONES.md](docs/DIRECCIONES.md) | Direcciones de memoria, bytes originales y fuentes de verificación |
| [docs/VARIABLES.md](docs/VARIABLES.md) | Para qué sirve cada variable local (`0@` a `31@`) |
| [docs/SANNY-BUILDER-4.md](docs/SANNY-BUILDER-4.md) | Sintaxis que funciona, errores conocidos y cómo verificar una compilación |
| [docs/INVESTIGACION.md](docs/INVESTIGACION.md) | Hora, forma del radar y cómo mover o redimensionar elementos: qué está verificado y qué falta |
| [docs/CREDITOS.md](docs/CREDITOS.md) | Fuentes consultadas y qué información se tomó de cada una |
| [docs/TRASPASO.md](docs/TRASPASO.md) | Resumen para retomar el proyecto en una sesión nueva |
| [docs/MODO2.md](docs/MODO2.md) | Concepto, ideas y plan del Modo 2 (HUD dinámico) |
| [docs/VERSIONES.md](docs/VERSIONES.md) | Esquema de numeración de versiones y plan hasta la 1.0.0 |
| [docs/CLEO-VS-ASI.md](docs/CLEO-VS-ASI.md) | Comparación entre seguir en CLEO o pasar a un plugin ASI |
| [docs/PRUEBAS.md](docs/PRUEBAS.md) | Lista de pruebas en el juego y resultados esperados |
| [docs/HISTORIAL.md](docs/HISTORIAL.md) | Cómo se resolvió cada paso, incluidos los problemas encontrados |
| [CHANGELOG.md](CHANGELOG.md) | Registro de cambios |

## Estructura del repositorio

```
.
├── README.md            Este archivo
├── CHANGELOG.md         Registro de cambios
├── LICENSE              Licencia MIT
├── src/
│   └── UI.txt           Código fuente (Sanny Builder 4)
├── experimentos/
│   └── explorar_hud.txt Script de solo lectura para investigar el HUD
└── docs/
    ├── ARQUITECTURA.md
    ├── DIRECCIONES.md
    ├── VARIABLES.md
    ├── SANNY-BUILDER-4.md
    ├── INVESTIGACION.md
    ├── CREDITOS.md
    ├── TRASPASO.md
    ├── MODO2.md
    ├── VERSIONES.md
    ├── CLEO-VS-ASI.md
    ├── PRUEBAS.md
    └── HISTORIAL.md
```

Los `.cs` compilados no se suben al repositorio (ver `.gitignore`). Se publicarán como archivos adjuntos de cada release.

## Créditos

Este mod se apoya en el trabajo de la comunidad de modding de GTA San Andreas; no descubre nada por sí mismo. Las direcciones de memoria y el funcionamiento interno del juego salen de **[plugin-sdk](https://github.com/DK22Pac/plugin-sdk)** (DK22Pac), **[gta-reversed](https://github.com/gta-reversed/gta-reversed)**, **[WidescreenFixesPack](https://github.com/ThirteenAG/WidescreenFixesPack)** (ThirteenAG), el truco del dinero de **Silent** (GTAForums) y la **[GTAMods Wiki](https://gtamods.com/wiki/Memory_Addresses_(SA))**. El mod funciona gracias a **[CLEO](https://cleo.li/)**, **[CLEO+](https://www.mixmods.com.br/2022/05/cleoplus/)** (Junior_Djjr) y **[Sanny Builder](https://sannybuilder.com/)**.

No se ha copiado código de ninguno de estos proyectos: solo se consultaron datos. Detalle de qué se tomó de cada fuente y dónde se usa: [docs/CREDITOS.md](docs/CREDITOS.md).

## Aviso legal

- Este proyecto **no está afiliado, respaldado ni patrocinado por Rockstar Games ni por Take-Two Interactive**. "Grand Theft Auto", "GTA" y "San Andreas" son marcas de sus respectivos propietarios.
- El repositorio **no contiene ni aceptará** archivos del juego ni material con derechos de Rockstar o Take-Two: ni el ejecutable (`gta_sa.exe`), ni texturas (`.txd`), modelos, audio, scripts originales (`main.scm`) u otros recursos del juego.
- Para usar el mod hace falta **una copia legal de GTA San Andreas para PC** (versión 1.0 US).
- El mod modifica la memoria del juego mientras se ejecuta, pero **no modifica ningún archivo del juego**. Se ofrece sin garantía (ver la licencia): úsalo bajo tu responsabilidad y haz copia de tus partidas guardadas.

## Licencia

[MIT](LICENSE). Puedes usar, modificar y redistribuir el código siempre que mantengas el aviso de copyright. Se ofrece sin garantía.
