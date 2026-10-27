# OptiPort

Una skill para que una IA **depure y optimice recompilaciones, decompilaciones y source ports**, aunque quien la use no sepa mucho de programación.

No se queda en un informe: investiga, implementa mejoras, las prueba y sube bloques revisables cuando está autorizado. Explica qué cambió, qué se midió y qué falta comprobar.

## Uso

Coloca este repositorio como carpeta `optiport` en el directorio de skills de tu asistente —por ejemplo, `~/.codex/skills/optiport` o `~/.claude/skills/optiport`—. Necesita acceso al código y herramientas de compilación del proyecto.

Pídele:

> Usa OptiPort para revisar y mejorar este juego. Prioriza cierres y tirones, conserva mis partidas e instalación principal, implementa lo que puedas verificar y sube los cambios por bloques. Explícame el avance sin tecnicismos.

## Qué revisa

- Memoria, alineación y compatibilidad entre arquitecturas.
- Migración de 32 a 64 bits con compatibilidad de datos, partidas y funciones; comparar antes de recomendarla, porque 64 bits no garantiza más velocidad.
- Fallbacks que conservan el juego original cuando falla un recurso opcional.
- Tiempos de carga, memoria y regularidad de cuadros mediante profiling.
- Cachés, presupuestos, carga asíncrona, precarga y streaming.
- Audio, vídeo, texturas, colas acotadas y trabajo sin progreso.
- Suspensión, reanudación, limpieza de recursos y protección del guardado.
- Logs rotativos, regresiones, pruebas prolongadas y builds verificables.

Sirve como método para cualquier plataforma; las pruebas disponibles dependen del proyecto, herramientas y dispositivos. No promete arreglar todos los fallos ni inventa porcentajes de mejora. Publicar releases o enviar builds requiere autorización.

[Instrucciones para la IA](SKILL.md) · [Capas de verificación](references/verification-layers.md)

Para migraciones, consulta la [guía de arquitectura](references/architecture-migration.md). El objetivo puede ser un paquete Windows o Linux con ambos ejecutables y selección compatible; en Android, un APK con las ABI realmente soportadas. La disponibilidad depende del código, toolchains y pruebas de cada proyecto.

Irá creciendo con aprendizajes comprobados. Para proponer uno, aporta el fallo, reproducción o evidencia, solución y límites; evita subir ROMs, assets comerciales, partidas o datos privados.

## Próximos aprendizajes

Pendientes del método: incorporar ejemplos reproducibles de pruebas prolongadas, matrices de dispositivos y cancelación de resultados de una escena anterior al reiniciar recursos. Cada ejemplo deberá indicar qué probó el harness y qué se ejecutó en un dispositivo real.

Estos pendientes no certifican resultados de ningún juego. Los recorridos prolongados, la matriz de hardware y los resets reales de dispositivo siguen requiriendo evidencia propia de cada port; sus tickets y resultados se mantienen en el proyecto correspondiente.

## Licencia

El material original de esta skill se publica bajo [MIT](LICENSE). Esta licencia no concede derechos sobre código, ROMs, fuentes, assets ni otros materiales de los juegos analizados.
