# Preparación de cada entrega

Aplicar al preparar un build, paquete o release, ajustando profundidad al cambio y al alcance anunciado. Reutilizar el board y las pruebas existentes; no crear burocracia ni pedir permisos adicionales por esta guía. La autorización de publicación sigue siendo la que dio el usuario.

Elegir escenarios breves y automatizables por patrón de riesgo: transición, acceso límite, recurso fallido, cambio de arquitectura o recuperación de estado. No exigir una partida completa de principio a fin como gate universal. Reservar sesiones prolongadas para riesgos de acumulación y declarar por separado lo que no se cubrió.

Convertir cada crash de usuario reproducido en una regresión estable con nombre, escenario mínimo y postcondición observable; `exit 0` solo no prueba que el flujo se completó. Registrar matriz de arquitecturas/configuraciones afectadas y procedencia inmutable de fixtures privados, con sustituto público saneado si puede construirse y redistribuirse. Conservar la regresión en entregas futuras, incluidas migraciones de arquitectura, y ejecutarla sobre el artefacto empaquetado exacto tras reconstruirlo. Ejemplo: cargar la misma copia de SRAM y entrar al primer cuarto debe alcanzar el estado de mapa esperado, no sólo evitar la excepción. Un crash conocido reproducible dentro del alcance anunciado bloquea readiness hasta corregirlo y verificarlo; esto no promete ausencia de fallos desconocidos.

## Evidencia mínima para decidir

Registrar por control **pass**, **fail**, **unverified** o **N/A**, con evidencia y motivo. `N/A` necesita una razón de alcance; falta de hardware es `unverified`. Un harness, un arranque o una plataforma aprobada no certifican las demás.

Si hay reportes relacionados, cierres o reaperturas, contrastar la [cadena de issue a artefacto](progressive-retrieval.md#relacionar-reportes-y-entregas). Un fix integrado no demuestra que esté publicado o presente en el paquete del usuario.

| Control pertinente | Evidencia esperada |
| --- | --- |
| Regresión del fallo | Vincular bug/issue ID, reproducción original, resultado y flujo relacionado. Derivar las plataformas afectadas del código compartido y backends: un fallo reportado en Deck también puede afectar Windows. Probar el recorrido correspondiente donde sea viable. |
| Arquitecturas y progreso | Comparar funciones 32/64, incluido HD; probar interoperabilidad SRAM y configuración con copias. Para quicksaves, verificar compatibilidad o rechazo claro y seguro cuando no sean portables. No convertir una partida válida para un build en una promesa universal. |
| Ciclo de vida y recursos | Según las rutas tocadas: foco, pausa/reanudación, assets HD válidos y fallidos, presupuestos, audio/vídeo, memoria y sesiones prolongadas. Observar limpieza, crecimiento y estado al volver al juego. |
| Paquete final | Registrar SHA exacto de fuentes, configuración/toolchain y hash del artefacto. Probar instalación aislada, arranque y flujo afectado usando el paquete que se entregará, con sus bibliotecas y assets permitidos. Un ejecutable suelto no valida el archivo distribuido. |
| Vigencia | Asociar resultados a SHA, arquitectura, opciones, assets y paquete probados. Tras cambios relevantes, repetir controles afectados; justificar evidencia previa que siga siendo aplicable. Identificar evidencia obsoleta en lugar de arrastrar un pass. |

## Decisión y comunicación

Un fallo reproducible conocido bloquea declarar lista la entrega con ese alcance. Corregirlo y repetir la regresión o reducir explícitamente el alcance de una prerelease limitada ya autorizada; no presentar el fallo como resuelto. La evidencia pendiente impide afirmar verificación de esa plataforma o función. No prometer ausencia de bugs ni «arreglado en todas las plataformas» sin evidencia correspondiente.

Un fallback intencional puede conservar juego y progreso reduciendo calidad; registrar su causa y qué función/calidad se pierde. No ocultar pérdida de HD, audio, vídeo u otra función bajo una afirmación de paridad o estabilidad. Una prerelease limitada debe nombrar plataformas, arquitecturas, funciones y comprobaciones pendientes para que el usuario pueda decidir con información concreta.
