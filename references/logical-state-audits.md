# Auditoría de estado lógico y serialización

Usar para replay, guardados, recuperación, checksum autoritativo o sincronización
entre procesos. Un hash de bytes es una comparación del buffer elegido, no una
prueba automática de equivalencia del estado lógico. Ver [online](online-simulation-contracts.md)
y [límites de decompilación](decompilation-contracts.md) cuando correspondan.

## Establecer qué se serializa y cuándo

- Seguir registro, inicialización, listas staging y persistentes, categorías,
  callbacks activos, buffers y consumidores. Una cabecera staging vacía después
  de construir tablas no significa que el subsistema esté inactivo. Capturar la
  tabla efectiva y llamadas reales por separado; registrar no demuestra ejecutar.
- Separar serializers de presentación/interpolación, input, estado del modo y
  snapshots persistidos. Identificar condición de modo y versión del formato.
  Un banco de poses/cámara puede omitir reglas, RNG, inventario o autoridad.
- Para cada componente, contrastar tamaño reservado, bytes realmente escritos,
  stride/end pointer devuelto, alineación, capacidad y wrap de ring. Copiar una
  reserva completa puede incluir padding no escrito; tampoco truncarla al tamaño
  observado autoriza a cambiar el contrato original.
- Registrar flags antes/después de un callback condicional. Si la rama copia,
  una diferencia no se puede declarar padding porque otra rama omita escribir.
  Igualdad de memoria tampoco prueba que se escribió: usar written ranges y
  controles con ambas ramas cuando sea pertinente.
- Ubicar el snapshot respecto de refresh de controles, seed, actualización común,
  trabajo específico de modo y final del paso. Un marcador de reloj/banco, una
  actualización común o el siguiente marker no acreditan consumo por los actores.
  Observar los callers y la entrada realmente registrada/consumida.

## Referencias y recursos

Clasificar campos por constructor, asignación, dereferencia, copia, uso y release.
Un delta común entre direcciones es una pista, no un esquema de punteros. No
borrar automáticamente las palabras distintas para hacer coincidir snapshots.
Preservar alias, null, orden/identidad de objetos, tipo, lifetime y generación.
Sustituir referencias verificadas por IDs estables y comparar el contenido
relevante de sus destinos; una referencia omitida puede esconder estado mutable.

Incluir campos iguales/null y objetos no alcanzados por la diferencia observada:
un mapa aprendido sólo del par que falló puede sobreajustarse. Comparar un conjunto de
buffers de ejemplo no prueba todos los recursos ni su necesidad lógica; no
convertir el conteo de un caso en una cobertura universal. Distinguir pose
calculable, caché, inputs y estado autoritativo antes de diseñar restore/export.
No ejecutar de nuevo un callback con efectos para inspeccionarlo; observar su
contrato real o una copia diagnóstica acotada, preservando ABI y last-error.

## Inicialización, RNG y entradas

Crear procesos nuevos con contenido, revisión, mapa/modo, opciones, equipos,
perfiles y bindings equivalentes. Verificar fases por estado/capturas, no sólo
input enviado. Conservar y excluir muestras que entraron a otro modo. Registrar
seeds/estados de todos los RNG relevantes, inicialización por reloj y scopes de
save/seed/restore; compartir un generador no inventaría todos los consumidores.
Una semilla común por paso no hace iguales actores iniciales o generadores
independientes. No fijar seeds en producción para forzar un resultado sin contrato.

Conservar held/pressed/released/previous, normalización, deadzones y restauración
de input por paso confirmado. Puede existir un refresh adicional dentro de la
actualización que invalide una conclusión tomada antes del marcador. Separar
estado latest-state de mailbox de una cola de frames confirmados. Validar espera,
catch-up, quit, pausa y recuperación; no aceptar un relay de cuatro clientes como
una partida sincronizada ni un menú de selección como determinismo de gameplay.

## Aceptación

Controlar divergencias reales, referencias distintas con contenido equivalente,
contenido apuntado distinto con direcciones normalizadas, padding/rama condicional,
flags desconocidos, frame/reset y pérdida/reconexión según el producto. Mantener
el mismo contrato de igualdad/tolerancia antes y después de observar el fallo.
Reportar componentes/bytes observados y límites, sin porcentaje global ficticio.
Una representación portable o recuperación sólo se acepta con cobertura de estado
y resultados de pasos completos, no porque sus pruebas no detectaron diferencias.
