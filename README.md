# Control del HUD para GTA San Andreas

Script CLEO para **GTA San Andreas PC 1.0 US** que permite mostrar y ocultar a voluntad cada parte de la interfaz (HUD): radar, arma, barras, dinero y hora.

> **Estado:** en desarrollo. El Modo 1 (manual) está completo y probado en el juego. Los Modos 2 y 3 solo tienen el selector; su lógica aún no existe. Todavía no hay ninguna versión publicada (release).

## Contenido

- [Requisitos](#requisitos)
- [Instalación](#instalación)
- [Controles](#controles)
- [Modos](#modos)
- [Estado del proyecto](#estado-del-proyecto)
- [Documentación técnica](#documentación-técnica)
- [Estructura del repositorio](#estructura-del-repositorio)

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
| **Shift + 0** | Cambia de modo: 1 → 2 → 3 → 1. Muestra "Modo N" en pantalla y devuelve el HUD a su estado de fábrica (todo visible). |
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
| 2 | Por definir | ⏳ Solo selector |
| 3 | Por definir | ⏳ Solo selector |

**Al cambiar de modo**, el HUD vuelve a su estado de fábrica (todo visible) y el nuevo modo empieza desde cero. Las elecciones del Modo 1 no se conservan: al volver a él, todo está visible.

## Estado del proyecto

### Hecho

- [x] Paso 1: radar
- [x] Paso 2: icono de arma + munición
- [x] Paso 3: barras de salud + blindaje
- [x] Paso 4: dinero
- [x] Paso 5: hora
- [x] Selector de modos (Shift + 0), con reinicio del HUD al cambiar
- [x] Shift + 1 a 5 limitados al Modo 1
- [x] Restauración automática si se carga partida con elementos ocultos

### Pendientes

- [ ] Definir y programar el Modo 2
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
└── docs/
    ├── ARQUITECTURA.md
    ├── DIRECCIONES.md
    ├── VARIABLES.md
    ├── SANNY-BUILDER-4.md
    ├── PRUEBAS.md
    └── HISTORIAL.md
```

Los `.cs` compilados no se suben al repositorio (ver `.gitignore`). Se publicarán como archivos adjuntos de cada release.

## Licencia

[MIT](LICENSE). Puedes usar, modificar y redistribuir el código siempre que mantengas el aviso de copyright. Se ofrece sin garantía.
