# Resolución, composición y memoria gráfica

Leer al adaptar escalado interno, supersampling, targets o upscalers entre
motores. Para precarga y procedencia, usar [adaptación de runtimes](runtime-adaptation.md);
para medir ganancias, usar [profiling](profiling.md). Estos contratos también
aplican a previews y exportaciones gráficas de herramientas creativas o técnicas.

## Elegir dónde cambia la resolución

Distinguir tamaño de ventana, salida lógica del producto, raster interno y
almacenamiento físico. Registrar quién produce cada tamaño y quién consume sus
coordenadas. Una ventana grande, una preferencia guardada o un target ampliado
no acreditan que la escena completa se dibuje a esa resolución.

Dos rutas pueden ser válidas: pedir al motor que reconstruya sus recursos, o
mantener sus tamaños lógicos y adaptar un conjunto identificado en el host.
Elegir por el contrato observado. En una combinación, asignar cada conversión a
un único dueño: si el motor ya produce viewports ampliados, multiplicarlos otra
vez por la escala del backing recorta o desplaza el dibujo. La escala del recurso
puede seguir siendo necesaria para otros consumidores, como readback lógico.

Cuando se intercepte una llamada de resize, seguir también los registros y
stores del caller después de su retorno. Un helper que respeta el ABI puede
dejar intactas dimensiones que el caller precalculó y copia después al contexto.
Corregir el productor o esas operaciones verificadas; conservar callee-saved y
el comportamiento nativo. Vincular los cambios de instrucciones al binario exacto.
Forzar un flag de reconstrucción no crea la configuración que un recurso necesita:
comprobar su inicialización, constructor y ciclo de vida antes de habilitarla.

## Cerrar la cadena de recursos y coordenadas

Identificar la clase por uso, descriptores, identidad/generación y consumidores;
coincidir en ancho/alto no basta. Incluir color, profundidad, MRT, postprocesado,
salida, HUD y transferencias pertinentes. Los storage images, máscaras, reducción
de profundidad, enteros e índices pueden representar píxeles semánticos: conservar
su cadena nativa o diseñar una conversión comprobada. No extender una excepción
de un donante a todas las texturas de otro motor.

| Frontera | Comprobación que cambia la implementación |
| --- | --- |
| Raster y composición | Mapear viewport, scissor, clears y crop con su origen; determinar las unidades por el productor/API, sin inferirlas del tamaño del rectángulo. Revisar attachments mixtos y separación escena/HUD. Distinguir recursos reconstruidos por el motor de backing ampliado por el host. |
| Rectángulos parciales | Fijar la regla del contrato, incluidos los empates, y convertir los extremos de cada endpoint. Si usa redondeo al más cercano: `round((x+w)*s)-round(x*s)`, con la misma regla para Y, límites y mip. No sustituirla por floor/ceil para aumentar cobertura sin validar el cambio: también cambia los texels copiados. Redondear ancho y origen por separado puede dejar huecos en escalas fraccionarias. |
| Copias GPU | Convertir fuente y destino con su propia escala; validar formato, aspecto, muestras y capas. Una copia de imagen ampliada a buffer lógico necesita un resolve/staging compatible, sus barreras y lifetime. Omitir la copia no conserva el contrato. |
| CPU y exportación | Conservar dimensiones, pitch, orden de filas y capacidad del buffer lógico. Un readback por vecino más cercano puede conservar tamaños y perder el filtro de reducción esperado. Definir color lineal/sRGB, alpha y filtro según el resultado requerido. |
| Shaders y cachés | Revisar coordenadas de fragmento, integer fetch, consultas de tamaño, storage y LOD. Incluir escala/clase/generación en las identidades que cambien su significado; no invalidar todo indiscriminadamente. |

## Presupuestar la memoria que realmente se usa

Duplicar cada eje multiplica por cuatro los píxeles de los recursos afectados;
no multiplica automáticamente por cuatro toda la memoria ni demuestra un coste
de tiempo concreto. Contabilizar formatos, muestras, mips, capas, targets en vuelo,
historial temporal, staging, espejos CPU/GPU y solapamiento durante recreación.
Medir asignaciones y pico/residencia pertinentes; tamaño lógico de pools o RAM
del proceso no sustituye a VRAM disponible ni a presión observada.

Un backing mayor no puede vincularse al rango del pool original más pequeño.
Comprobar requisitos reales de memoria, alineación y límites del dispositivo;
usar almacenamiento suficiente con ownership explícito. Conservar recursos que
aún usan comandos en vuelo hasta su fence. Antes de cambiar presupuestos del
motor, identificar el allocator y sus consumidores; no trasladar números del
donante ni aumentar todos los heaps por si acaso.

Elegir una política de fallo concreta: mantener la ruta nativa antes de activar
la alternativa, o terminar con un error accionable si el contrato ya exige los
recursos ampliados. Un fallo tardío no prueba que sea posible reducir escala en
caliente. Validar ambas decisiones sin prometer recuperación que no existe.

## Escalado temporal y cobertura de GPU

Que un runtime enlace DLSS, XeSS o FSR no identifica la escena de otro motor.
Antes de activarlos, establecer color/profundidad, matrices y jitter, vectores
de movimiento y unidades, exposición, transparencia/reactividad pertinentes,
reset del historial en cortes/resize y frontera con HUD y captura/exportación.
No alimentar offsets o firmas del donante a un backend sólo porque inicialice.

Seleccionar API/modelo por las capacidades requeridas por el SDK realmente
integrado y la memoria medida. Registrar versión/licencia, modelo efectivo y
fallback espacial/nativo. Nombre comercial, INT8/FP16 disponible o versión de
Vulkan por sí solos no establecen todo el soporte. Una prueba NVIDIA no valida
AMD; una ruta espacial portable tampoco certifica todos los dispositivos antiguos.

## Demostrar la mejora sin cambiar el resultado

Combinar pruebas de clasificación, regiones y límites con capturas GPU del mismo
estado: encuadre/logo completos, presencia de modelos, oclusión, zoom, movimiento,
HUD y cambios de modo. Cero errores de API/shaders puede coexistir con geometría
desalineada o composición recortada. Probar transferencias y resultado final,
no sólo que creció la profundidad o que el contador presenta cuadros.

Si los píxeles participan en evaluación, selección o exportación, comprobar ese
consumidor y la persistencia del resultado: una fotografía visible no acredita
identificación/puntuación ni recarga. Para previews, comprobar que la exportación
usa su calidad autorizada. Comparar rendimiento con escena, calidad, caché y
dispositivo equivalentes; una mejora visual de supersampling no es una reducción
de memoria ni una aceleración medida. Conservar las capacidades no probadas como
pendientes y aplicar [rendimiento adaptativo](adaptive-performance.md) al decidir
qué trabajo opcional puede reducirse.
