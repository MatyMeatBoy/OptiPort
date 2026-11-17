# Assets de controles y familias de dispositivo

Usar cuando se integren glifos o esquemas de mandos, no como cambio automático
de bindings. Conservar el contrato [acción → binding → prompt](control-layouts.md).

## Fuentes prácticas con licencia verificable

| Fuente primaria | Uso pertinente | Verificación necesaria |
| --- | --- | --- |
| [Xelu / Those Awesome Guys](https://thoseawesomeguys.com/prompts/) | Packs con materiales biselados y familias Xbox 360, PS4, Switch y Steam Deck, entre otras. | La página y Readme.txt declaran CC0. Comprobar miembros y readme de la descarga concreta; registrar URL, fecha/revisión y SHA-256. |
| [Kenney Input Prompts](https://kenney.nl/assets/input-prompts) | Glifos consistentes y variantes vectoriales para numerosas familias. | La página declara CC0. Verificar archivo elegido y cobertura; un espejo o una edición reducida puede omitir PlayStation aunque otro paquete lo incluya. |

Descargar sólo de procedencia comprobable; conservar fuente sin modificar,
licencia/readme y atribuciones pertinentes. Una licencia del pack no determina
la licencia de todas sus dependencias ni concede marcas. Inventariar imágenes
realmente seleccionadas, hashes, variantes y transformaciones derivadas. No
incluir archivos propietarios del producto por encontrarse junto al pack.

Integrar un atlas pequeño/reproducible con alpha, padding y escala adecuados.
Un cambio de tamaño/formato no debe alterar UV, orden BGRA/RGBA, pitch ni colores
por doble conversión. Para brillo/material, comprobar legibilidad, borde, alpha
y aspecto a tamaño real; no confundir más luminosidad con una textura correcta.
Validar archivo ausente/corrupto y upload fallido antes de suprimir el dibujo
original. Cachear por familia y generación de dispositivo; no cargar por frame.

## Posición física, nombre y acción

| Posición SDL | Xbox / Deck | PS4 | Switch |
| --- | --- | --- | --- |
| Sur | A | Cross | B |
| Este | B | Circle | A |
| Oeste | X | Square | Y |
| Norte | Y | Triangle | X |

[SDL_GamepadButton](https://wiki.libsdl.org/SDL3/SDL_GamepadButton) define estas
posiciones; [SDL_GamepadType](https://wiki.libsdl.org/SDL3/SDL_GamepadType) ayuda a
identificar familia. Leer el enum de la versión realmente cargada, no copiar
ordinales supuestos de otra revisión. No intercambiar acciones de Nintendo sólo
para que una letra coincida con Xbox. Si se ofrece convención A-aceptar o rebinding,
actualizar acción efectiva y todos sus prompts; separar preferencia visual y
binding. Mantener inicio/atrás con sus nombres reales (Share/Options, View/Menu,
Minus/Plus o equivalentes del dispositivo), no glifos aproximados de otra tecla.

Auto necesita propiedad de actividad y fuentes fiables; un nombre genérico no
acredita un modelo físico. Steam Input puede exponer XInput y ocultar la familia:
ofrecer override persistente y un fallback de esquema claramente identificado,
sin afirmar detección física de Steam Deck. Un tipo Steam tampoco acredita por
sí solo un Deck. No inferir región/convención a partir del color de los botones.

En mandos mixtos, cambiar sólo el dueño de prompts pertinente, conservar cada
slot independiente, controlar ruido/hotplug y liberar entradas al perder foco.
Probar los consumidores reales: launcher, menús originales, pausa, tutoriales,
F1/overlay y textos embebidos. Un panel con el estilo elegido no prueba que el
juego recibió el binding ni que todos los tutoriales resuelven sus tokens.

## Steam Deck y límites de plataforma

Separar glifos ABXY/L1/R1/L2/R2, botones View/Menu y acciones de sistema/trackpads
/back buttons. Sólo presentar acciones traseras como funcionales si un binding
real (por ejemplo Steam Input) las entrega y ha sido verificado. Iconos Deck,
SDL en Windows o una fixture virtual no demuestran Gaming Mode, Proton, Linux,
suspend/resume, pantalla táctil ni cuatro mandos físicos. Consultar
[contexto Linux](linux-launch-context.md) para esos fallos y registrar el límite.
