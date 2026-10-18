# CLEO o ASI: comparación de arquitecturas

Las primeras versiones (`0.x`) siguen en CLEO. Este documento compara CLEO con un plugin ASI en C++ para decidir el futuro del proyecto.

## Qué es cada uno

- **CLEO (actual):** scripts `.cs` escritos en el lenguaje de Sanny Builder. El juego los ejecuta como sus propios scripts de misión, una vez por frame.
- **ASI:** una DLL de C++ que carga el ASI Loader (o modloader) al iniciar el juego. Normalmente se escribe con [plugin-sdk](https://github.com/DK22Pac/plugin-sdk), que ya trae las clases del juego (`CHud`, `CRadar`, `CPed`, ...) con sus direcciones.

## Comparación

| Aspecto | CLEO | ASI (C++ con plugin-sdk) |
|---|---|---|
| Herramientas | Sanny Builder: compilar es inmediato | Visual Studio + plugin-sdk: instalación más larga |
| Variables | **32 locales por script** (este mod ya usa 30) | Sin límite práctico |
| Ocultar elementos | Parches de bytes (lo que hacemos ahora) | Lo mismo, o envolviendo la función original con un *hook* |
| **Mover elementos o cambiar su tamaño** | Muy difícil: hay que parchear punteros y constantes | Natural: con un hook se llama a la función original con otras coordenadas, o se dibuja el elemento uno mismo |
| **Radar cuadrado completo** (mapa, aro e iconos) | El mapa sí; el aro y los iconos son muy difíciles | Factible: se reemplazan `LimitRadarPoint` y el dibujo del aro |
| Condiciones de partida e input (Modo 2) | Bien: hay opcodes para estado del jugador, vehículo y teclas | Igual de bien y con lógica más clara |
| Ver y depurar | Mensajes en pantalla y descompilar | Depurador de Visual Studio, logs a archivo |
| Riesgo de crash | Parches a mano con direcciones verificadas | Similar; plugin-sdk reduce errores de direcciones |
| Facilidad para editar sin programar | Alta: cualquiera abre el `.txt` en Sanny | Baja: hay que saber C++ |
| Instalación para el jugador | Requiere CLEO 4.4 + CLEO+ | Requiere ASI Loader (incluido en modloader) |

## Conclusión

- **Para ocultar y mostrar elementos según condiciones (Modos 2 y 3), CLEO es suficiente.** Lo que más va a limitar es el **número de variables**: quedan 2 libres (`10@` y `31@`). Si el Modo 2 necesita muchas más, habrá que dividir el mod en varios scripts o reutilizar variables.
- **Para mover elementos, cambiar su forma o dibujar un HUD propio, ASI es claramente mejor.** En CLEO cada uno de esos cambios es un parche frágil; en ASI es código normal.
- **Recomendación:** terminar los Modos 2 y 3 en CLEO (versiones `0.x`) y, si después se quiere personalizar posiciones o formas, empezar una versión **2.0 en ASI**. La lógica de estados del mod (pedido → deseado → aplicado) se traslada tal cual a C++.

## Decisión del autor

Este mod **se queda en CLEO** como prototipo: sirve para probar y pulir ideas rápido. El mod definitivo se hará más adelante como un **proyecto ASI aparte**, aprovechando lo aprendido aquí.

## Qué instalar para hacer mods ASI

### Imprescindible

| Herramienta | Para qué | Notas |
|---|---|---|
| **Visual Studio 2022 Community** (gratis) | Compilar el código C++ en un `.asi` | En el instalador, marca la carga de trabajo **"Desarrollo para el escritorio con C++"** (incluye el compilador MSVC y el SDK de Windows) |
| **Git para Windows** | Descargar plugin-sdk y trabajar con el repositorio | También sirve GitHub Desktop |
| **[plugin-sdk](https://github.com/DK22Pac/plugin-sdk)** | Las clases y funciones del juego ya preparadas para C++ | Trae un asistente para crear proyectos de plugin. Hay que compilar una vez su librería para SA (`plugin_sa`) y definir la variable de entorno `PLUGIN_SDK_DIR` |
| **ASI Loader** | Que el juego cargue el `.asi` | **Ya lo tienes:** modloader carga archivos `.asi`. Si no, Ultimate ASI Loader de ThirteenAG |

### Muy recomendable

| Herramienta | Para qué |
|---|---|
| **Ghidra** (gratis) o IDA Free | Abrir `gta_sa.exe` y **verificar direcciones** uno mismo: la parte que hoy depende de fuentes externas |
| **x32dbg** o **Cheat Engine** | Ver y depurar la memoria del juego mientras está en marcha |

### Cómo sería el flujo de trabajo

1. Claude escribe el código C++ en el repositorio.
2. El autor lo descarga, lo compila en Visual Studio y copia el `.asi` resultante a la carpeta de modloader.
3. El autor prueba en el juego y cuenta el resultado, igual que ahora.

Claude no puede compilar ni probar desde la sesión, igual que con CLEO.
