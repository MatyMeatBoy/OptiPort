# Online, simulación y presentación

Aplicar al optimizar aplicaciones con estado compartido, simulación por cuadros
o colaboración en tiempo real. Primero identificar su autoridad, tolerancia al
error, política de conflictos y contrato de respuesta. Rollback determinista es
una opción para ciertos motores; no imponerlo a todos los productos ni cambiar
sus reglas para producir mejores métricas.

## Separar los contratos

- **Transporte:** conexión, bytes y paquetes, RTT, jitter, pérdida, duplicación,
  reordenamiento, colas y reintentos. Un relay transporta datos; no demuestra
  simulación correcta ni reduce la distancia física entre participantes.
- **Simulación:** controles consumidos por identidad y cuadro, frecuencia lógica,
  confirmación, predicción, correcciones y resultados autoritativos.
- **Presentación:** intervalos reales de pantalla y audio, respuesta local,
  correcciones visibles y coste del replay. Un contador redondeado a60, cero
  desync o cero esperas de socket no acreditan velocidad completa.

Elegir la cadencia propia del producto, sin sustituirla por la frecuencia del
monitor. Medir cada participante contra tiempo real: frecuencia lógica,
intervalos p50/p95/p99/máximo, cuadros añadidos o repetidos, trabajo CPU y espera.
Un ajuste de ventaja que extiende periódicamente el reloj de presentación puede
frenar la experiencia aunque la función de red nunca bloquee. No quitarlo sin
entender sincronización, corrección y límites del motor.

## Estado que puede retroceder y estado que no

En replay determinista, guardar el contador lógico y toda la simulación que lo
consume. Mantener la sesión recibida, ACKs y controles locales ya publicados
fuera del snapshot: retroceder el juego no puede desrecibir paquetes ni publicar
otra tecla para el mismo cuadro. Probar ambas identidades, replay y correcciones
consecutivas; conservar la política de efectos externos, audio, logros y guardados
para que no se emitan dos veces.

Separar tres estados: corrección en cola, corrección tomada por el consumidor y
resimulación terminada. Un ACK confirma recepción de controles, no que el estado
predictivo ya se reconstruyó. Un hash sólo puede enviarse o compararse como
definitivo cuando sus entradas están confirmadas y ninguna corrección pendiente
o en curso afecta a ese estado. Probar una recepción entre dequeue y finalización
del replay: limpiar la cola no debe habilitar hashes especulativos. Recalcular
los hashes afectados y vincular su finalización al cuadro y generación que se
validan. Un protocolo puede elegir la barrera conservadora de alcanzar el
presente; no es universal: un hash histórico puede quedar definitivo mientras
se reconstruyen cuadros posteriores que no lo afectan.

Medir recepción→inicio→fin de cada corrección, su edad y el coste de resimular.
Definir un presupuesto propio de respuesta y profundidad útil; no aceptar una
corrección indefinidamente aplazada sólo porque se ocultaron sus hashes. Al
exceder ese contrato, aplicar recuperación o salida explícita con UI viva.

## Historial y retransmisión son vidas distintas

Una confirmación acumulativa válida puede permitir omitir del paquete controles
que el receptor ya retiene, sin borrar el historial usado para reconstruir el
estado. Conservar redundancia y reparación para datos aún no confirmados. Antes
de podar, comprobar qué confirma el ACK, cuánto retiene el receptor, si ese
estado sobrevive al rollback y qué pasa al reconectar o cambiar de sesión.

Acotar bytes, entradas, trabajo por bombeo y reintentos. En un ring, rechazar un
cuadro futuro que colisionaría con una entrada local aún necesaria; probar también
duplicados conflictivos, wrap y huecos antiguos. Medir el ahorro de bytes con el
mismo consumidor, carga, horario de bombeo y secuencia de fallos. Contar por
separado payload, cabeceras, wrapper y datagramas intentados o entregados; no
llamar reducción de latencia o CPU a una reducción de bytes.

## Admisión, errores y generaciones

Validar pertenencia a sesión antes de que un paquete atrasado pueda causar un
error de compatibilidad en la sesión actual. Esto no autentica al remitente:
conservar las comprobaciones de tamaño, identidad, versión, región y reglas.
Usar campos explícitos de ancho fijo y endian definido, no structs con padding
o punteros nativos. Separar protocolo de aplicación y proveedor de transporte.

Reservar los recursos necesarios antes de admitir una operación cuyo contrato
exige replay disponible. Un fallo de memoria, corrección demasiado antigua o
desacuerdo confirmado requiere la política explícita del producto: recuperación
o salida con interfaz viva. No ocultarlo volviendo a un modo bloqueante si se
prometió simulación sin esperas. Presupuestos y números de cuadros son propios
del motor; medirlos, no copiarlos como garantía universal.

## Pruebas comparables y límites

Probar RTT simétrico y asimétrico, jitter, pérdidas aisladas/en ráfaga,
duplicados, reordenamiento, pausas, salida/reconexión y ambos roles. Bombear con
la cadencia real del consumidor: una prueba cada milisegundo puede ocultar colas
y retransmisiones de una aplicación que bombea sólo una vez por cuadro.

Separar pruebas de parser/protocolo, integración con simulación, juego o flujo
real por servidor y dispositivos físicos en ubicaciones distintas. Un proxy
local que pasa por un VPS modela una ruta; no es una partida física entre países.
RTTs de regiones al servidor tampoco sustituyen medir el recorrido completo.

Registrar versiones, configuración, resultados correctos y baseline/candidato
con el mismo workload. Conservar los intentos fallidos. Si se ofrece vídeo como
evidencia de fluidez, preservar los timestamps reales: codificar cuadros a FPS
fijos puede ocultar pausas. Separar captura activada/desactivada y su coste.
Aplicar [profiling](profiling.md), [capas de verificación](verification-layers.md)
y [preparación de entrega](release-readiness.md) al alcance concreto. Estas
regresiones no certifican un motor completo ni conectividad mundial sin límites.
