# Semáforo peatonal inteligente

Prototipo (maqueta con ESP32) de un semáforo que **mantiene el verde vehicular** y solo
activa la fase peatonal cuando una **cámara detecta personas esperando** para cruzar.
Incluye un **dashboard de administración** para ver el estado en vivo y cambiar el modo
(p. ej. ámbar intermitente los fines de semana).

## Problema
El semáforo frente a la universidad funciona con un ciclo fijo, aunque no haya peatones
(fines de semana, horarios de poca actividad), y detiene autos sin necesidad.
Un botón peatonal tampoco basta: en hora pico lo presionan constantemente y no distingue
entre 1 persona y 40. La cámara permite **contar** a las personas que esperan y **cancelar**
las solicitudes cuando ya no hay nadie. La lógica de control (verde mínimo, espera máxima)
evita saturar el tráfico.

## Antecedentes
- **TU Graz / Viena (Austria):** detección por cámara de la intención de cruzar, con
  procesamiento local.
- **Pedestrian SCOOT, Highbury Corner (Londres, TfL):** ajuste del verde peatonal según
  la cantidad de peatones detectados.

El aporte de este proyecto es un prototipo **accesible, documentado y evaluado** para el
entorno universitario.

## Arquitectura
Consulta [docs/arquitectura.md](docs/arquitectura.md).

| Parte | Tecnología |
|---|---|
| Detector de personas | Python, OpenCV, YOLOv8 (preentrenado) |
| Backend / API | Python, FastAPI |
| Base de datos | PostgreSQL (SQLModel + Alembic) |
| Dashboard | HTML + JavaScript + Chart.js |
| Controlador del semáforo | ESP32 con MicroPython |
| Cámara | ESP32-CAM (en desarrollo: webcam de la laptop) |

## Estado
- [x] Fase 0: estructura del proyecto
- [ ] Fase 1: máquina de estados y pruebas
- [ ] Fase 2: base de datos y backend
- [ ] Fase 3: dashboard
- [ ] Fase 4: detector con cámara
- [ ] Fase 5: horarios, login y métricas
- [ ] Fase 6: hardware (ESP32 + ESP32-CAM)
- [ ] Fase 7: evaluación

## Aviso
Este es un prototipo educativo. Instalarlo en la vía pública requiere equipo certificado,
revisión técnica y la autorización de la autoridad de tránsito.
