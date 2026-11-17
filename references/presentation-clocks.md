# Relojes, frecuencia y trabajo autoritativo

Aplicar cuando se cambien Hz, FPS, esperas, interpolación, cadencia de UI, media
o procesamiento. La frecuencia solicitada, la del productor y la observada en
el consumidor son hechos distintos. No imponer un objetivo fijo a todo software.

## Identificar los relojes antes de acelerarlos

| Reloj / indicador | Qué acredita y qué no |
| --- | --- |
| Paso lógico / operación | Avance autoritativo. Contar la actualización completa, no sólo un marcador que alterna un banco o incrementa un float. |
| Render / composición | Producción de una imagen; puede reutilizar estado lógico. Más cuadros no implica más simulación ni datos más nuevos. |
| Presentación | Puede incluir bloqueo de VSync/cola/GPU. Contar llamadas no mide por sí solo frames únicos efectivamente mostrados. |
| Refresco físico | Capacidad/configuración del display. Seleccionar 240 Hz o crear un target grande no acredita 240 FPS entregados ni una mejora de lógica. |
| Audio / media | Unidades de muestra, PTS y duración. No ajustar pitch/velocidad para seguir FPS de pantalla. |
| UI / trabajador / datos | Redraw, polling, jobs y datos de origen pueden tener cadencias diferentes. Redibujar un indicador no aumenta el throughput del productor. |

Instrumentar el tramo real con tiempos monotónicos y contadores de eventos
pertinentes. Registrar callers, branch de modo, catch-up, punto de muestreo de
input y alcance de cada medida. El retorno de una función común puede preceder
a trabajo específico del modo. Un step nombrado como tal puede omitir una
actualización. Registrar resets, pausas y generaciones antes de asignar frame IDs.

## Separar espera de render y lógica

Cuando la evidencia demuestre que una espera limita render junto con la lógica,
adaptar esa espera o scheduling de presentación conservando timestep, argumentos
y procesamiento autoritativo originales. Si están acoplados, reconstruir primero
su contrato; no cambiar globalmente una constante de frecuencia o delta.

Verificar interpolación entre estados completos, fracción efectiva y continuidad
en límites. No presentar un endpoint y retroceder al siguiente cuadro. Inicializar
historia desde el primer estado válido tras recreación cuando su contrato lo
requiera; no leer bancos aún no escritos. Mantener input, edges, timers y orden
del paso bajo catch-up. Una corrección en un motor no fija una regla para todos.

Para una UI o pipeline de datos, separar presentación de trabajo caro, usar
invalidación/cachés y colas acotadas según la causa medida. Una UI más rápida no
debe duplicar commits, operaciones de negocio, decode, llamadas de red o trabajo
por tick. Respetar thread affinity, cancelación y generación de resultados.

## Demostrar mejora y conservar equivalencia

Comparar mismo flujo/escena/entradas, binario/configuración, calidad, dispositivo,
estado inicial y caché. Medir tiempos por etapa, frames únicos si importan,
p50/p95/p99 con muestra suficiente, stalls y presupuesto CPU/GPU; conservar
latencias y errores. Distinguir CPU ocupada, espera y trabajo GPU. Registrar
el overhead de sondas o separar una corrida sin trazas pesadas.

Probar caps pertinentes y continuidad de cronómetro/movimiento, animación,
audio, pausa/reanudación, foco, resize/reset y transiciones. En otros productos,
usar su resultado y cadencia autoritativa equivalentes. Un cap implementado o
una media de Present/s no es un FPS sostenido, una aceptación de VRR ni una
medición de latencia extremo a extremo. La prueba virtual no certifica hardware.

Una mejora puede ser funcional sin medir aceleración. Reportar por separado
configuración solicitada, tasa lógica observada, render/presentación medidos,
refresco disponible y pruebas pendientes. Aplicar [profiling](profiling.md) y
[protocolo general](software-general.md), sin convertir un valor del caso previo
en default universal ni prometer porcentajes por un selector.
