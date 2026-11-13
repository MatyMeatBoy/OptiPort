# Controles portables y prompts coherentes

Aplicar cuando el producto soporte distintos mandos/teclado o necesite adaptar tutoriales. Separar tres contratos: **acción semántica** (confirmar, cancelar, saltar), **binding físico efectivo** y **texto/glifo mostrado**. Cambiar el dibujo de un botón no debe cambiar silenciosamente la acción ni afirmar un binding inexistente.

Si encaja con el producto, ofrecer **Auto**, teclado, ratón, táctil y dispositivos especializados, con override explícito. Distinguir selección de familia visual y selección/rebinding de controles. Botones primarios de distintos dispositivos pueden representar convenciones seleccionables de confirmación; no asumir que lo sean en todas las regiones, aplicaciones o preferencias. Respetar confirmación/cancelación regional y rebinding del usuario.

Resolver tokens de tutorial/ayuda desde la acción y el mapping efectivo, con su convención seleccionada: un tutorial de confirmar debe mostrar el botón que realmente confirma. Si el texto original embebe nombres de botones, integrar su resolución en el punto apropiado del motor sin reemplazos globales ambiguos. Mantener nombres, glifos y acción coherentes también en menús, errores y accesibilidad.

## Cambio de dispositivo y presentación

En Auto, usar identificación fiable y actividad de entrada pertinente; definir comportamiento ante hotplug, desconexión y entradas mixtas evitando parpadeos de familia por ruido. Conservar el override hasta que el usuario lo cambie. Para dispositivos desconocidos, usar nombres/botones genéricos o teclado según el binding real, sin inventar identidad del mando. No borrar rebinding al cambiar sólo la familia visual.

Localizar nombres y texto, comprobar cobertura de glifos, ancho/escala, contraste y alternativa textual accesible. No depender sólo del color o un símbolo que la fuente no contiene. Reutilizar un atlas pequeño/cacheado y actualizar prompts al cambiar mapping/dispositivo/locale; no cargar imágenes por cuadro. Usar arte propio o con licencia compatible y conservar atribuciones; nombres de familias no conceden derechos sobre iconos o marcas.

## Continuidad de captura, sensores y ajustes

Una muestra relativa retenida por cuadro debe pertenecer también a la
generación de captura/dispositivo que la produjo. Liberar y recapturar entre
dos polls puede ocurrir sin cambiar el frame: un booleano observado al final
no revela la discontinuidad. Propagar un epoch desde el dueño de la captura
e invalidar muestras anteriores. Probar reposo, delta conocido, mismo frame,
foco perdido/recuperado y cuadro detenido, conservando sensibilidad y binding.

Reiniciar muestras/orientación fusionadas al cambiar o perder fuente, apagar
sensores o interrumpirse su lectura según el contrato. Comprobar el ajuste
antes de habilitar hardware y revertir habilitaciones parciales; una prueba
mock no debe encender sensores físicos. No duplicar con estáticos no atómicos
una configuración live que ya se publica de forma segura: una lectura antigua
puede terminar después de la nueva y dejar un valor viejo bajo una versión
nueva. Reproducir esa intercalación con hilos coordinados y comprobar el estado
posterior, no solo el valor devuelto por cada lector.

## Validación pertinente

Para una mira o cursor con movimiento interno, distinguir posición de ventana,
delta relativo del ratón, stick emulado, sensores y estado de cámara del motor.
Comparar reposo → movimiento conocido → reposo, además de captura/liberación y
cambio de foco. La posición visual fuera del centro no demuestra deriva: el
producto puede conservar puntería libre dentro de un marco. Medir entradas y
transición contra un control conocido antes de forzar recentrado o mover el HUD.
Al adoptar defaults de otro producto, comprobar cada acción semántica —por
ejemplo, disparar frente a interactuar— sin sobrescribir rebinding explícito.

Probar acción→binding→prompt en cada familia implementada, override/Auto, convenciones regionales y rebinding. Incluir tutorial real, confirm/cancel, teclado+mandos mezclados, hotplug/desconexión y dispositivo desconocido. Revisar localización/accesibilidad y que invalidar la caché actualice el prompt sin perder input ni introducir cargas por frame. Documentar familias no implementadas o no probadas; no afirmar soporte universal por reconocer un nombre de dispositivo.
