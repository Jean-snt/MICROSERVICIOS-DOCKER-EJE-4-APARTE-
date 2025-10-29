# MICROSERVICIOS-DOCKER-EJE-4-APARTE-
🧭 DÍA 4 — - 28/10/ 2025 Backend: Email / Notifications Service
📦 Puerto sugerido: 8002
 📚 Nivel: Intermedio (enfocado en integración y comunicación entre servicios)

🎯 Objetivo general
Construir un microservicio independiente de notificaciones (Email Service) que:
Reciba mensajes o formularios desde otros microservicios (p. ej. Blog o Auth).


Envíe correos simulados (o reales si se tiene configuración SMTP).


Ejemplifique comunicación entre microservicios por HTTP y colas (opcional).


Refuerce conceptos de idempotencia, reintentos y observabilidad.



⚙️ Requisitos técnicos
Stack: Django + DRF + Redis + Docker + (opcional Celery)


Base de datos: independiente (PostgreSQL o SQLite local)


Servicios relacionados: Auth (8000) y Blog (8001) solo como emisores; Email recibe.



🧩 Requisitos funcionales
1️⃣ Endpoint principal
POST /api/contact/
{
  "name": "Carlos Rivas",
  "email": "carlos@mail.com",
  "message": "Me interesa una colaboración"
}

Respuesta
{ "status": "queued" }

Valida campos requeridos.


Persiste la solicitud en la BD (ContactMessage).


Envía una notificación (simulada por consola o archivo log).


2️⃣ Endpoint interno (opcional)
POST /api/notify/
{
  "to": "user@mail.com",
  "subject": "Nuevo post publicado",
  "body": "..."
}

→ Simula un evento recibido desde Blog u otro servicio.

🏗️ Estructura recomendada
email-service/
 ├── app/
 │   ├── notifications/
 │   │    ├── models.py      # ContactMessage, NotificationLog
 │   │    ├── serializers.py
 │   │    ├── views.py       # ContactViewSet, NotifyViewSet
 │   │    └── tasks.py       # (si usan Celery)
 │   └── utils/
 │        ├── mailer.py      # función send_email()
 │        └── logger.py
 ├── Dockerfile
 ├── requirements.txt
 ├── manage.py
 ├── openapi.yaml


⚙️ Configuración mínima
Dependencias
Django==5.0
djangorestframework==3.15
django-cors-headers
psycopg2-binary
redis
celery==5.4  # opcional

Correo simulado (settings.py)
EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"

(O bien filebased.EmailBackend para guardar en carpeta /sent_emails.)

🔁 Opcional: Celery + Redis (worker asíncrono)
Definir cola emails.


Encolar las tareas de envío en segundo plano.


Configurar retry máx. 3, delay 5 s.



🔍 Observabilidad y resiliencia
Healthcheck /healthz (DB + Redis).


Logs estructurados (JSON): entrada, salida, tiempo de procesamiento.


Reintento con backoff si fallan llamadas HTTP entrantes.


Idempotencia (basada en UUID del mensaje).



📦 Entregables
Entregable
Descripción
Microservicio Email funcional
POST /api/contact almacena y simula envío
Healthcheck + logging
/healthz y logs estructurados
OpenAPI documentado
Endpoints contact/notify
(Opcional) Celery + Redis
Reintentos asíncronos
README
Cómo correr + ejemplos cURL + docker-compose

