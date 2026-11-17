# Compatibilidad y modding de software de PC

Aplicar a un ejecutable existente, launcher o motor antiguo cuando el trabajo
requiera entender arranque, datos, presentación o restricciones heredadas.
No exigir decompilar todo el producto para resolver una frontera ya localizada.
Elegir sólo las etapas que cambian la siguiente decisión.

## Arranque reproducible y restricciones heredadas

1. Identificar revisión, arquitectura del proceso y DLL, hash del ejecutable,
   dependencias realmente cargadas, medio/datos compatibles, argumentos, cwd,
   configuración, idioma y perfil. Conservar los originales y trabajar sobre
   copias identificadas; una DLL host de 64 bits no sustituye una dependencia
   original de 32 bits por tener el mismo nombre.
2. Distinguir qué impide iniciar: ruta o medio ausente, API/servicio obsoleto,
   import faltante, arquitectura, manifest de elevación, permisos reales o una
   validación de contenido. Resolver el mecanismo demostrado. Suprimir un UAC
   no hace escribible una instalación protegida. Eliminar una comprobación
   aleatoria no demuestra compatibilidad ni corrige la dependencia que ocultaba.
3. Para restricciones o comprobaciones heredadas cuya modificación esté
   autorizada, preferir una adaptación acotada y reversible del ejecutable/copia
   identificada. No extenderla a protecciones del sistema ni a validaciones
   ajenas al flujo autorizado. Mantener medios/datos privados fuera del paquete;
   no redistribuir ejecutables propietarios ni credenciales.
4. Antes de interceptar perfiles/registro/rutas, inspeccionar los imports y las
   resoluciones dinámicas que este binario usa. Interceptar la función realmente
   invocada y su variante A/W/ABI. Preservar resultados, last-error, registros,
   stack y lifetimes; guardar un trampolín válido y revertir instalaciones
   parciales. Rechazar hashes/firmas incompatibles en vez de parchear por offset
   aproximado. Conservar la validación original de recursos no reemplazados.
5. Para ejecución sin administrador, manifest apropiado y almacenamiento son
   contratos distintos. Probar escritura portable real, incluidos subdirectorios y archivos mutables
   existentes; si falla, usar datos
   por usuario, con configuración/logs/cachés/importación dirigidos allí.
   Verificar los API heredados de perfil/registro que puedan escapar a esa ruta.
   Si una importación conserva read-only, retirar ese atributo sólo de las
   nuevas copias privadas identificadas cuya edición requiere el producto;
   conservar originales y ACL. Probar actualización y reemplazo atómico de
   configuración/logs, no sólo crear un archivo en el directorio raíz.
   Probar con token estándar y denegación real NTFS, no sólo un flag read-only.
   Comprobar instalación protegida, importación, preferencias, guardado, cierre
   y restauración de ACL del fixture. No cambiar permisos globales para aprobar.

## Localizar datos y fronteras del motor

Seguir productor → representación → consumidor → limpieza. Usar imports,
llamadores, formatos, encabezados, referencias, trazas acotadas y capturas para
ubicar la frontera pertinente; no tratar un nombre de función como su contrato.
Separar estado autoritativo, caché, presentación, recursos y datos persistidos.
Un contador o una cabecera vacía puede pertenecer a un staging ya consumido.
Conservar cada hipótesis como tal hasta contrastarla con el consumidor real.

Para lectura/escritura binaria, fijar anchos, endian, stride, pitch, unidades,
alineación y límites antes de decodificar. Exportar/interpretar sólo los formatos
entendidos y con presupuestos; no confundir un blob copiado con datos recuperados.
No sustituir guardados por un dump de memoria ni asumir que replay e interpolación
usan el mismo registro. Ver [auditoría de estado](logical-state-audits.md).

## Ampliar presentación sin cambiar reglas

| Cambio | Contrato a comprobar |
| --- | --- |
| Aspecto / Hor+ | Separar extensión horizontal, zoom y estiramiento. Conservar el zoom original si eso solicita el usuario. Identificar proyección principal, cámaras de preview y vídeo por productor/uso, no sólo por dimensiones. |
| Mundo / HUD | Determinar coordenadas lógicas, viewport, scissor, clears y destino de Present. Evitar aplicar dos conversiones a geometría ya ampliada. Un panel puede estar centrado mientras el mundo debe ocupar todo el ancho. |
| Pipeline heredado | Distinguir vértices pretransformados/RHW, matrices, shaders, stencil y composición final. Tratar cielo, malla de portería, sombra y UI según su productor. No desactivar depth/stencil globalmente para ocultar un recorte específico. |
| Reset / ventana | Recrear recursos y cachés por dispositivo/generación; invalidar aspect/proyección heredada cuando corresponda. Respetar tamaño de cliente elegido por el usuario y rectángulos de destino explícitos. |
| Captura | Verificar formato real, pitch, RGB565/BGRA, alpha, orientación y región. Una captura errónea no acredita un fallo del renderer. |
| Frecuencia | Separar espera de render, paso lógico, interpolación, audio y refresco físico. Ver [relojes de presentación](presentation-clocks.md). |

Usar fixtures GPU con controles negativos y estado original restaurado. El caso
sintético demuestra ese mecanismo; repetir escena/ángulo/transición real antes
de aceptar todos los mapas, replays o dispositivos. Elegir un conjunto acotado
de recursos/producers comprobados, no una excepción por resolución para todo.

## Instrumentación que no interfiere con el usuario

Para pruebas Windows fuera de pantalla, usar un desktop privado real y pasar
su nombre en `STARTUPINFO.lpDesktop` al llamar a `CreateProcessW`; asignar un
atributo Python que
su adaptador no transmite no crea aislamiento. Verificar el desktop efectivo
desde el hijo o su thread/ventana identificados, además de comprobar que el
input desktop del usuario no cambió. No cambiar el input desktop.
Usar entrada privada dirigida al proceso y mute por PID cuando proceda, sin
teclas globales ni cambiar el audio del usuario. Una excepción de foco de
laboratorio debe ser explícita, aislada y revocable; no debilitar el foco normal.

Navegar por fases verificadas, no sólo por segundos: una tecla enviada durante
carga puede acabar en otro menú. Conservar el caso inválido y excluirlo de la
comparación; no reiniciar un proceso sólo porque un observador agotó su timeout.
Consultar el handle/estado vivo y cerrar limpiamente; separar cierre forzado.
Una traza síncrona o captura pesada puede alterar timing: no usarla para prometer
FPS. Para input físico, mandos virtuales dirigidos al mailbox no prueban SDL/HID
ni compatibilidad de hardware. Ver [reproducciones](reproduction-harnesses.md).

## Entrega incremental

Verificar instalación, launcher, iconos, fuentes, dependencias, firma de binario
y archivos por usuario desde la copia extraída del paquete. Fijar source SHA,
configuración, arquitectura y hashes de los artefactos realmente ejecutados.
Si PE cambia sólo por timestamps/checksum, enumerar exactamente los campos
normalizados; no ignorar diferencias de código o datos para obtener equivalencia.
Probar inventario/rutas/UTF-8 del ZIP y descargas públicas; no sobrescribir tags
aceptados ni empaquetar diagnósticos o medios privados. Una prerelease limitada
puede sentar base sin declarar terminado todo el motor. Ver [entregas](release-readiness.md).
