# Adaptar runtimes y preparar recursos derivados

Leer al reutilizar un runtime/recompilador, sustituir servicios de un SDK,
preparar gráficos antes del primer uso o reutilizar código generado. Son
contratos aplicables a otros productos; direcciones, títulos, claves, rutas de
usuario y cifras de una aplicación pertenecen a su evidencia privada.

## Reutilización comprobable del donante

Fijar commit, submódulos, traductor y toolchain, y comparar la revisión local con
la rama/release publicada cuando se pida la última versión. Una actualización
puede estar ya incorporada; no cambiar el gitlink sólo para aparentar una mejora.

Registrar cada función pertinente como **incluida en fuente**, **enlazada**,
**activada** o **verificada en el consumidor objetivo**. No son estados
intercambiables. Separar infraestructura común de parches ligados a direcciones,
shaders, buffers, cámaras, temporización o formatos del donante. Comprobar el
layout de módulos, stack, permisos y firmas/variantes del SDK desde los datos
del objetivo; compartir plataforma no acredita esos contratos. Conservar una
adaptación reproducible sobre el donante intacto, con rechazo de seams que ya
no coincidan, si ese mecanismo encaja con el proyecto.

Al cambiar resolución, composición o upscalers, seguir los [contratos de gráficos
y memoria](graphics-memory.md): las dimensiones del motor, backing físico y
coordenadas de cada consumidor pueden tener dueños distintos.

## Inicialización y servicios sustituidos

Si el host reemplaza `Initialize`, revisar los efectos que dejan de ocurrir:
creación de objetos, estado de sesión, tablas virtuales y recursos que getters,
finalizadores o destructores todavía esperan. Seguir la llamada que falla y
las variantes hermanas que usan ese mismo estado; no devolver éxito a todas.

Una apertura con resultado de éxito y un objeto ausente rompe el contrato aunque
no falle dentro del handler. Devolver el error real elimina ese falso éxito,
pero **no demuestra un fallback**: el SDK puede abortar también al recibirlo.
Verificar el resultado que consume el caller y la transición posterior.

Antes de pedir recursos externos, identificar qué contenido solicita el objetivo
y con qué finalidad. Fuentes para un idioma, reglas horarias y diccionarios de
validación son dependencias distintas. Que el donante no necesite un recurso
no prueba que el objetivo tampoco; que una ruta actual lo solicite no demuestra
que sea imprescindible para toda implementación del producto.

Para una ruta host alternativa, conservar argumentos, límites de entrada,
cancelación, ownership y limpieza. Si cambia una función visible del producto,
resolver esa decisión dentro de la autorización del usuario y declarar la
diferencia. No afirmar que un servicio original se inicializó cuando sólo se
eligió una ruta que no lo usa. Una representación opaca propia requiere poseer
y validar sus consumidores; no pasarla después a código que espera el formato
binario original ni aceptar variantes que no se implementaron.

## Datos prestados que sobreviven a la llamada

Cuando se instala un programa o recurso desde memoria temporal, comprobar si
el consumidor retiene punteros a controles, constantes o descriptores. La copia
del código no acredita que sus metadatos también sean propios. Si la API libera
el préstamo al retornar, capturar los metadatos necesarios en el objeto dueño
y mantenerlos hasta terminar replay, traducción o trabajos que los consuman.
Probar sustitución/finalización con datos válidos y reutilización de la memoria
prestada. Un caso de dibujo inicial correcto no valida todos los cambios de escena.

## Retornos no locales entre guest y host

Si el recompilador representa cada llamada guest mediante una llamada C nativa,
`setjmp`/`longjmp` rompe la equivalencia entre RET guest y retorno C ordinario.
Restaurar registros/SP/PC guest no retira los frames C intermedios. Añadir la
dirección interior a la tabla de funciones puede ocultar el primer fallo de
despacho mientras conserva un stack host incorrecto. Determinar si el motor usa
retornos no locales realmente; no heredar la suposición contraria del donante.

En esa arquitectura, un checkpoint nativo debe pertenecer al caller aún vivo
que continuará después del save, no a un helper que ya retornó. Vincularlo al
contexto guest/fiber y a la continuación comprobada; conservar las operaciones
originales que guardan/restauran el buffer y sus valores de retorno. Los registros
o flags cacheados en locales C pueden quedar obsoletos o indeterminados tras el
salto: sincronizarlos de forma válida o desactivar esas optimizaciones sólo en
los propietarios afectados. Respetar las restricciones del compilador para un
checkpoint que retorna dos veces: antes de sincronizar tampoco puede leerse un
local automático no volátil modificado y ya indeterminado. Conservar el estado
que realmente restaura la API guest, sin inventar preservación adicional.
Un `musttail` que elimina el frame propietario
también elimina su checkpoint; tratar esos tail exits según su vida real.

Invalidar checkpoints al retornar el propietario, al descartar descendientes
durante el salto y al destruir/reiniciar su contexto. Aislar fibers aunque
compartan thread host; TLS por sí solo no identifica su activación. El cleanup
normal de C no se ejecuta necesariamente al saltar: definir quién retira esas
estructuras y rechazar continuaciones vencidas o de otro contexto.

Un salto que cruza servicios host puede omitir locks, contadores o recursos que
su retorno normal liberaba. Comprobar esa frontera y rechazar las no soportadas;
un contador de profundidad sólo protege si su seguimiento está activado en todas
las configuraciones admitidas. No restaurar un contador a ciegas como sustituto
de la limpieza. Usar el mecanismo nativo compatible con ABI y protecciones de
la plataforma; esto no implementa automáticamente unwinding C++ ni otras APIs
de excepciones.

Probar el código emitido real con optimización, saves anidados y múltiples
buffers, valor cero/no cero, retorno normal, scopes descartados, contexto ajeno
y seguimiento host desactivado. Después repetir el checkpoint que fallaba y su
transición real. El fixture CPU acredita el mecanismo; una transición GPU
superada no certifica todo el SDK, otros tipos de unwind ni otras plataformas.

## Precarga gráfica: cuatro unidades distintas

| Unidad | Qué identifica y qué deja pendiente |
| --- | --- |
| Programa o código de una etapa | Código, control y constantes de una etapa. Encontrar el archivo o su hash no fija las respuestas del entorno que condicionan su traducción. |
| Variante de traducción | Programa más las respuestas efectivamente utilizadas por el traductor: tipos de textura y otras capacidades/entradas pertinentes. Respuestas por defecto no enumeran todas las variantes del producto. |
| Representación compilada intermedia | SPIR-V u otra salida validada del traductor. Su existencia no demuestra compilación o aceptación por el driver activo. |
| Pipeline del driver | Combinación utilizable de etapas, interfaces y estado de render/compute. Bibliotecas parciales de etapas no prueban que todas las combinaciones completas estén listas. |

Declarar denominadores distintos para programas inventariados, variantes
observadas, variantes sintetizadas, etapas no soportadas, traducciones aceptadas
y pipelines completos preparados. Conservar los resultados observados frente
a defaults de menor prioridad. No esconder una etapa no soportada dentro de
un total de éxito de las demás.

Cuando se combinan grabaciones o semillas, validar cabecera **y todo el framing**:
tamaños fijos, longitudes variables, límites y registros completos. Una cola
truncada puede desalinear todos los registros añadidos o detener el lector antes
de las semillas nuevas. Rechazarla o recuperar explícitamente una copia hasta
el último registro completo, conservando el original; no concatenar tras bytes
incompletos ni adivinar dónde reiniciar el parseo.

Resolver las ubicaciones efectivas de entrada y salida considerando entorno,
INI, argumentos y cwd; herramienta, validación y consumidor deben coincidir en
las rutas pertinentes, aunque el flujo separe entradas y resultados. Comprobar que el resultado pertenece a esta ejecución,
no a un archivo anterior que sobrevivió en otra carpeta. Usar salida nueva o
retirar del candidato el resultado viejo antes de ejecutar; no destruir la única
caché válida del usuario. Leer resultados parciales y fallos aunque el proceso
devuelva cero. Validar identidad de traductor, capacidades, GPU/driver y versión
del formato antes de reutilizar resultados dependientes de ellos.

Probar el arranque real con esa preparación y un recorrido acotado con sus
inputs: aceptación del driver, fallos, compilaciones/esperas en primer uso y
tiempo de inicio. Desplazar trabajo fuera de la interacción acredita precarga,
no una mejora global de frame time si las mediciones son mixtas. Las escenas
no visitadas y estados no registrados siguen pendientes.

## Procedencia transitiva de importaciones y código generado

Para reutilizar un resultado, vincularlo a sus entradas y a su productor:
fuentes base/actualización, contenido de control, módulos y layout pertinentes;
versión/configuración del generador, parches y flags que afecten su salida; hashes
de los archivos realmente generados. Validar también índices y salidas importadas
que consume el build. Un manifiesto sólo con hashes de entrada no detecta una
salida modificada después.

Una opción como `--no-generate` debe comprobar esos vínculos antes de compilar,
no sólo la existencia del directorio. Si se confía en metadata que alimenta
definiciones de compilación o presupuestos, rederivarla de la fuente verificada
o verificar su contenido completo; un JSON cacheado editable no hereda la
autenticidad del contenedor del que supuestamente procede.

Probar cambios independientes de entrada, metadata, índice, receta y salida,
incluyendo reutilización válida y una actualización de versión. Los receipts
prueban identidad del productor y sus datos; no prueban jugabilidad ni que todos
los subsistemas adaptados funcionen.
