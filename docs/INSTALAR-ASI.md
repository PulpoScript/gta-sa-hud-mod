# Qué instalar para el mod ASI (Modo 3)

Guía para preparar el PC antes de empezar el mod ASI. Sigue los pasos en orden y apunta lo que falle.

**Aviso:** los nombres exactos de menús y scripts de plugin-sdk pueden haber cambiado. Si algo no coincide, manda una captura y se ajusta.

## Resumen

| # | Programa | Obligatorio | Tamaño aprox. |
|---|---|---|---|
| 1 | Visual Studio 2022 Community | Sí | 8–10 GB |
| 2 | Git para Windows | Sí | 300 MB |
| 3 | plugin-sdk (DK22Pac) | Sí | 1 GB al compilar |
| 4 | Cargador de ASI | Sí (ya lo tienes si usas modloader) | — |
| 5 | Ghidra o x32dbg | No, más adelante | — |

## 1. Visual Studio 2022 Community (gratis)

1. Descárgalo de https://visualstudio.microsoft.com/es/vs/community/
2. En el instalador, marca la carga de trabajo **"Desarrollo para el escritorio con C++"**.
3. Instala y reinicia el PC.

Comprobación: abre Visual Studio y verás **Crear un proyecto**, con plantillas de C++.

## 2. Git para Windows

1. Descárgalo de https://git-scm.com/download/win
2. Instálalo con las opciones por defecto.

Comprobación: abre **Símbolo del sistema** y escribe `git --version`. Debe responder con un número de versión.

## 3. plugin-sdk

Es la librería que trae las clases del juego (`CHud`, `CRadar`, `CPed`…) ya preparadas para C++.

1. Crea una carpeta **sin espacios ni acentos** en la ruta, por ejemplo `C:\dev\`.
2. En el Símbolo del sistema:
   ```
   cd C:\dev
   git clone https://github.com/DK22Pac/plugin-sdk.git
   ```
3. Crea la variable de entorno `PLUGIN_SDK_DIR`:
   - Menú Inicio → busca **"variables de entorno"** → **Editar las variables de entorno del sistema** → **Variables de entorno…**
   - En "Variables de usuario", pulsa **Nueva**: nombre `PLUGIN_SDK_DIR`, valor `C:\dev\plugin-sdk`.
4. Crea también `GTA_SA_DIR` con la carpeta del juego (la que contiene `gta_sa.exe`). Así, al compilar, el `.asi` se copiará solo allí.
5. Cierra y vuelve a abrir el Símbolo del sistema para que las variables se apliquen.
6. Genera la solución de Visual Studio y compila la librería de San Andreas. Sigue el README de plugin-sdk: dentro de `C:\dev\plugin-sdk\tools\` hay un script de **premake** que crea la solución (`.sln`) para VS 2022. Ábrela y compila solo el proyecto **`plugin_sa`** en **Release**.

Comprobación: la compilación termina con **"1 correcto"** y aparece un archivo `plugin.lib` dentro de `C:\dev\plugin-sdk\output\lib\` (o una carpeta parecida).

## 4. Cargador de ASI

Si usas **modloader**, ya carga archivos `.asi`. Si no, instala **Ultimate ASI Loader** (ThirteenAG): https://github.com/ThirteenAG/Ultimate-ASI-Loader/releases

Mientras tanto, apunta la lista de archivos `.asi` que tengas (junto a `gta_sa.exe`, en `scripts\` y en `modloader\`). Así veremos qué mod panorámico toca el arma y la munición.

## 5. Opcional, más adelante

| Herramienta | Para qué |
|---|---|
| Ghidra (gratis): https://ghidra-sre.org | Abrir `gta_sa.exe` y verificar direcciones |
| x32dbg: https://x64dbg.com | Ver la memoria del juego mientras está en marcha |

## Cuando esté todo listo

Avisa con:
1. "plugin_sa compila" (o el error, con captura).
2. La lista de `.asi`.

Con eso, el primer paso será un `.asi` mínimo que solo muestre un mensaje en el juego, para comprobar que todo el proceso funciona.
