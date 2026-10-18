# Esquema de versiones

El proyecto usa [versionado semántico](https://semver.org/lang/es/) adaptado a los modos: `MAYOR.MENOR.PARCHE`.

| Parte | Cuándo sube | Ejemplo |
|---|---|---|
| **MAYOR** | `0` mientras el mod está en beta. Pasa a `1` en la versión final: los 3 modos completos, F11 eliminado y todo probado. Volvería a subir con un cambio que rompa la forma de usarlo (por ejemplo, el paso de CLEO a ASI). | `0.x.x` → `1.0.0` |
| **MENOR** | Cada vez que se **completa un modo** o una función grande. | `0.1.0` = Modo 1 completo |
| **PARCHE** | Correcciones y mejoras pequeñas que no añaden un modo. | `0.1.1` = quitar el sonido doble |

## Versiones en desarrollo

Mientras un modo está a medias, la versión lleva un sufijo con el número de prueba:

```
0.2.0-dev.1   primer paso del Modo 2, sin probar
0.2.0-dev.2   segundo paso
...
0.2.0         Modo 2 completo y probado
```

Las versiones `-dev` no se publican como release: son estados intermedios en GitHub.

## Plan

| Versión | Contenido | Estado |
|---|---|---|
| `0.1.0` | Modo 1 completo + selector de modos + recuerdo del Modo 1 | ✅ Probado (falta confirmar el sonido único) |
| `0.2.0-dev.1` | Modo 2, paso 1: dinero | ✅ Probado |
| `0.2.0-dev.2` | Modo 2, paso 2: arma + munición | ✅ Probado |
| `0.2.0-dev.3` | Modo 2, paso 3: salud + blindaje | ✅ Probado |
| `0.2.0-dev.4` | Modo 2: aviso de poca salud | ✅ Probado |
| `0.2.0-dev.5` | Modo 2, paso 4: radar | ✅ Probado |
| `0.2.0-dev.6` | Modo 2, paso 5: hora, tecla de consulta y entrada al modo | ⚠️ Sin probar |
| `0.2.0-dev.N` | Resto de elementos del Modo 2 | ⏳ |
| `0.2.0` | Modo 2 completo | ⏳ |
| `0.3.0` | Modo 3 completo | ⏳ |
| `1.0.0` | Versión final: sin F11, mod activo al arrancar, primera release pública | ⏳ |

## Dónde se indica la versión

Al cambiar de versión hay que actualizar **los tres sitios a la vez**:

1. Cabecera de `src/UI.txt`: línea `Versión: ...`.
2. Mensajes de F11 en `src/UI.txt`: `"UI v0.1.0 activado"` y `"UI v0.1.0 desactivado"`. Así se ve en el juego qué versión está cargada.
3. `CHANGELOG.md`: nueva sección con la versión y la fecha.

Cuando una versión se publique como release, se creará también una etiqueta de git con el mismo nombre (por ejemplo, `v1.0.0`).
