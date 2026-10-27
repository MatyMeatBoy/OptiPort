# OptiPort

Una skill para que una IA **depure y optimice recompilaciones, decompilaciones y source ports**, aunque quien la use no sepa mucho de programación.

No se queda en un informe: investiga, implementa mejoras, las prueba y sube bloques revisables cuando está autorizado. Explica qué cambió, qué se midió y qué falta comprobar.

## Uso

Coloca este repositorio como carpeta `optiport` en el directorio de skills de tu asistente —por ejemplo, `~/.codex/skills/optiport` o `~/.claude/skills/optiport`—. Necesita acceso al código y herramientas de compilación del proyecto.

Pídele:

> Usa OptiPort para revisar y mejorar este juego. Prioriza cierres y tirones, conserva mis partidas e instalación principal, implementa lo que puedas verificar y sube los cambios por bloques. Explícame el avance sin tecnicismos.

## Qué revisa

- Memoria, alineación y compatibilidad entre arquitecturas.
- Fallbacks que conservan el juego original cuando falla un recurso opcional.
- Tiempos de carga, memoria y regularidad de cuadros mediante profiling.
- Cachés, presupuestos, carga asíncrona, precarga y streaming.
- Audio, vídeo, texturas, colas acotadas y trabajo sin progreso.
- Suspensión, reanudación, limpieza de recursos y protección del guardado.
- Logs rotativos, regresiones, pruebas prolongadas y builds verificables.

Sirve como método para cualquier plataforma; las pruebas disponibles dependen del proyecto, herramientas y dispositivos. No promete arreglar todos los fallos ni inventa porcentajes de mejora. Publicar releases o enviar builds requiere autorización.

[Instrucciones para la IA](SKILL.md) · [Capas de verificación](references/verification-layers.md)

Irá creciendo con aprendizajes comprobados. Para proponer uno, aporta el fallo, reproducción o evidencia, solución y límites; evita subir ROMs, assets comerciales, partidas o datos privados.
