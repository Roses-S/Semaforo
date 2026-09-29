# Arquitectura del sistema

## Vista general

```
 [ESP32-CAM] --video--> [Detector (Python)]
                              | POST /api/detecciones  ("hay 3 personas esperando")
                              v
 [Navegador admin] <--HTTP / WebSocket--> [BACKEND FastAPI] <--> [PostgreSQL]
      (dashboard)                              ^
                                               | POST /api/controlador/heartbeat (cada ~1 s)
                                               | ESP32 envía: "estoy en VERDE_VEHICULAR"
                                               | Backend responde: "modo NORMAL, hay solicitud peatonal"
                                               |
                                   [ESP32 semáforo (MicroPython)]
                                   máquina de estados + LEDs
```

## ¿Quién decide qué?

| Componente | Hace | NO hace |
|---|---|---|
| Detector | Ver personas en la zona de espera y reportar cuántas hay | Decidir luces |
| Backend | Guardar el **modo** actual, los horarios, el historial, el login y las métricas | Encender LEDs directamente |
| ESP32 semáforo | Ejecutar la secuencia **segura** (amarillo y rojo total obligatorios) | Obedecer una orden que se salte un paso de seguridad |
| Dashboard | Mostrar y enviar órdenes del administrador | — |

**Principio:** la seguridad vive en el dispositivo más cercano a las luces. Si el ESP32
deja de recibir respuesta del servidor, entra solo en **modo seguro** (ciclo fijo).

## Modos
- `NORMAL_ADAPTATIVO`: verde vehicular hasta que se detecta un peatón.
- `CICLO_FIJO`: como el semáforo actual (respaldo y línea base para comparar).
- `AMBAR_INTERMITENTE`: p. ej. fines de semana.
- `SOLO_BOTON`: para comparar contra la cámara en la evaluación.

Prioridad para elegir el modo: **orden manual del admin > horario programado > NORMAL**.

## Tablas de la base de datos (PostgreSQL)
- `usuarios`: administradores (contraseña guardada con hash).
- `horarios`: día, hora de inicio, hora de fin y modo.
- `eventos`: bitácora de cambios de fase, solicitudes, fallas y acciones del admin.
- `detecciones`: cuántas personas detectó la cámara y cuándo.
- `ciclos_peatonales`: hora de la solicitud, hora del verde peatonal, espera y personas.
