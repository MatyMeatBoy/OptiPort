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

Para localizar packs y contrastar Xbox/PS4/Switch/Steam Deck, consultar el
[catálogo de assets de controles](controller-assets.md), incluida procedencia,
licencias y límites de Steam Input/hardware.


## Captura guiada para Saturn y Mega Drive / Genesis

Cuando un mando de seis botones se identifica como Xbox genérico o sus botones
C/Z/L/R no corresponden, no deducir la posición física desde el nombre normalizado.
Ofrecer una utilidad de diagnóstico con un esquema propio del mando objetivo:
Saturn muestra cruceta, Start, L/R y filas X-Y-Z / A-B-C; Genesis de tres botones
muestra A-B-C y Start, y el de seis añade X-Y-Z. Mantener los datos del esquema
separados de los perfiles de dispositivo. Back/Select es un extra de PC: permitir
omitirlo y no inventarlo como botón original Saturn.

La utilidad debe resaltar la posición solicitada y capturar el evento físico
del dispositivo elegido, además del mapping que el backend ya aplicaba. Distinguir
explícitamente el evento observado por el backend de un reporte USB/HID realmente
crudo: XInput o Steam Input pueden traducirlo antes. Registrar las capas conocidas
y marcar la procedencia que no pueda observarse, sin inventarla.
Registrar un informe versionado con plataforma/backend y versión, fecha, modelo
observado, GUID/VID/PID cuando existan, transporte (USB/Bluetooth/receptor) y protocolo/modo (XInput/DInput/Switch)
por separado, y cada acción con
su tipo (botón, hat o eje), índice, dirección, reposo y umbral aplicable. No tomar
un nombre XInput como identidad comercial confirmada ni recoger números de serie
sin una necesidad concreta. Guardar localmente y mostrar la ruta; no enviar ni
aplicar bindings silenciosamente. Conservar versión/build de la utilidad, esquema
y revisión, mapping previo y una traza ordenada acotada de observaciones aceptadas
y rechazadas con su causa y asignación resultante, suficiente para reproducir un
C/Z incorrecto o diagnosticar un hat diagonal, botón retenido o gatillo extremo.

Armar cada paso sólo en reposo, aceptar una transición nueva y exigir liberación
antes de avanzar. Rechazar entradas duplicadas accidentales, diagonales ambiguas
y ruido analógico; preservar signo/rango/reposo de ejes y máscaras de hats.
Contemplar gatillos compartidos y ejes con reposo en un extremo. Ofrecer repetir,
omitir, cancelar y recuperar un informe tras desconexión; invalidar el armado al
perder foco o cambiar dispositivo y volver a exigir reposo. No bloquear indefinidamente
por un botón que el modelo no tiene.

Probar captura→asignación→resaltado→guardado→lectura del informe, no sólo un parser.
Una prueba con joystick virtual valida esa ruta y sus tipos de evento; **no
valida el mando físico ni todos sus modos de conexión**. Contrastar después las
pulsaciones físicas, incluidas C/Z y hombros, con las acciones efectivas del juego.
Conservar por separado perfiles verificados, inferidos y pendientes, respetando
overrides del usuario. El mismo esquema de captura puede reutilizarse en otras
recompilaciones Saturn/Genesis sin copiar números de botones de otro dispositivo.

Resolver overrides por GUID del backend y mantener perfiles por modo cuando cambie
ese GUID. Un informe USB/XInput no documenta DInput ni demuestra que Bluetooth
exponga los mismos índices. Conservar la selección explícita del usuario y
contrastar las entradas normalizadas antes de reutilizar un perfil.
