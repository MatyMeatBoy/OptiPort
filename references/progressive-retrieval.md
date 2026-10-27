# Recuperación escalonada de evidencia

Aprender lo necesario para una decisión concreta y conservar evidencia reutilizable, sin sacrificar calidad por ahorrar tokens. Ajustar el presupuesto de lectura a incertidumbre y riesgo; no imponer una cuota universal de búsquedas.

## Buscar y ampliar

Empezar por logs locales, reproducción, símbolos del binario, código afectado y headers de la versión instalada. Buscar error, función, arquitectura o transición; leer rangos de función y callers pertinentes en lugar de archivos, repositorios o documentación completos. Perfilar el escenario antes de atribuir tirones a una optimización supuesta.

Si falta una explicación local, consultar metadatos filtrados por términos de error/función, arquitectura, labels y versión. Distinguir issues abiertos/cerrados y PR abiertos/cerrados/merged. Leer después uno o dos comentarios que aporten reproducción o resolución, el diff relevante y su prueba; ampliar sólo si no bastan. No descargar historiales completos por defecto.

Un issue cerrado no demuestra arreglo; un PR cerrado no demuestra merge. Si se atribuye una corrección al código actual, comprobar que su merge SHA está presente en la rama objetivo actual. Un caso cerrado parecido aporta una hipótesis, no prueba que sea el mismo bug. Estrellas y foros descubren candidatos; contrastar el mecanismo con código primario y versión concreta.

Ampliar la lectura ante evidencia contradictoria, reproducción que no concuerda, fallo de la validación mínima o una frontera ABI desconocida. Una búsqueda sin resultado no prueba ausencia. Consultar el siguiente caller, backend, versión o fuente que resuelva esa incertidumbre específica.

## Reutilizar y parar

Guardar en el board o caché local un registro compacto: pregunta, conclusión/hipótesis, repositorio y commit, versión de dependencia/toolchain relevante, fecha, archivo/rango o URL, prueba y límites. Usar hashes o identificadores necesarios; no guardar PII, secretos ni partidas privadas. Reutilizar lecturas con fingerprint sin cambios en vez de releerlas; invalidar la parte afectada cuando cambien fuente, versión, configuración, assets o binario, y volver a comprobar lo que dependa de ella.

Parar la exploración cuando haya una hipótesis concreta con evidencia suficiente y una validación mínima que pueda refutarla. Ejecutar esa validación y pasar a implementar, probar y entregar por bloques dentro de la autorización. Si falla, volver a la incertidumbre observada; no seguir navegando por inercia ni presentar una hipótesis como resultado. Resumir hallazgos como evidencia, decisión, prueba y pendiente, sin dumps de fuentes ni cifras de benchmark inventadas.

Cuando la delegación esté autorizada y aporte trabajo independiente, enviar una pregunta acotada, rutas/SHA, restricciones y resultado esperado. Preferir contexto mínimo (`fork_turns="none"` cuando la herramienta lo admita) y salidas concisas con evidencia y límites; mantener el modelo/esfuerzo autorizado. No crear workers sólo para releer evidencia ya válida.
