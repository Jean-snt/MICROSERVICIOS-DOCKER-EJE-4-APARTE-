# 📧 Microservicio de Notificaciones (Email Service)

**Día 4 — 28/10/2025**  
Microservicio independiente encargado de **centralizar y gestionar el envío de notificaciones y correos electrónicos** dentro del ecosistema de microservicios (**Auth**, **Blog**, etc.).  
Utiliza **Celery** y **Redis** para manejar las tareas de forma **asíncrona**, garantizando **resiliencia** y **escalabilidad**.

---

## 🧩 Características Generales

| Característica | Detalle |
|----------------|----------|
| **Rol** | Microservicio Backend de Notificaciones / Email |
| **Puerto sugerido** | `8002` |
| **Tecnología base** | Django 5.0, Django REST Framework, Celery, Redis |
| **Base de datos** | PostgreSQL (o SQLite local) |
| **Objetivo** | Recibir solicitudes de correo, persistirlas y enviarlas asíncronamente |

---

## 🎯 Requisitos Funcionales y Endpoints

El servicio expone **dos endpoints principales**:

### 1️⃣ Endpoint Público — Contacto  
Permite recibir mensajes de contacto desde el frontend o cualquier otro servicio.

**Método:** `POST`  
**URL:** `/api/contact/`  
**Descripción:** Almacena el mensaje en la base de datos y encola su notificación.

#### 🧾 Ejemplo de Request:
```json
{
  "name": "Carlos Rivas",
  "email": "carlos@mail.com",
  "message": "Me interesa una colaboración"
}
```
###Estructura del proyecto:
```
email-service/
├── app/
│   ├── email_service/       # Configuración central (settings.py, celery.py, urls.py)
│   └── notifications/
│        ├── models.py       # Modelos: ContactMessage, NotificationLog
│        ├── serializers.py  # Validadores de datos (DRF)
│        ├── views.py        # ContactViewSet, NotifyViewSet
│        └── tasks.py        # Tareas Celery (send_email_task)
├── openapi.yaml
├── requirements.txt
├── manage.py
└── Dockerfile
```
🛠️ Observabilidad y Resiliencia

El microservicio está diseñado para ser robusto y observable:
```
🧵 Cola Asíncrona (Celery + Redis):
Todas las operaciones de envío de correo se encolan, evitando bloquear el hilo principal.

🔁 Reintentos Automáticos:
Hasta 3 reintentos con un delay de 5 segundos, garantizando entrega confiable.

🩺 Healthcheck (/healthz):
Endpoint para verificar la conexión con la base de datos y Redis.

📊 Logs Estructurados (JSON):
Registra entradas, salidas y tiempos de procesamiento de cada solicitud.

🧱 Idempotencia (Opcional):
Permite evitar el reprocesamiento de mensajes duplicados mediante un UUID cliente.
```
🧪 Cómo Ejecutar el Servicio
1️⃣ Requisitos Previos

Python 3.10+

pip

Servidor Redis en ejecución local (localhost:6379)

2️⃣ Instalación
# Clonar el repositorio
```
git clone https://github.com/tuusuario/email-service.git
cd email-service
```
# Crear entorno virtual
```
python -m venv venv
.\venv\Scripts\activate  # En Windows PowerShell
```
```
# Instalar dependencias
pip install -r requirements.txt
```
```
3️⃣ Ejecución del Servidor Django
python manage.py runserver 8002
```
```
4️⃣ Iniciar el Worker de Celery

Ejecuta en una nueva terminal (con el entorno virtual activo):

celery -A email_service worker -l info
```
🚀 Comandos de Prueba (cURL)
🩺 Healthcheck

Verifica el estado del servicio:
```
curl -X GET http://localhost:8002/healthz/

💬 Enviar Mensaje de Contacto
curl -X POST http://localhost:8002/api/contact/ \
-H "Content-Type: application/json" \
-d '{
  "name": "Elon Musk",
  "email": "elon@xcorp.com",
  "message": "Necesito un billón de notificaciones."
}'

🔔 Enviar Notificación Interna
curl -X POST http://localhost:8002/api/notify/ \
-H "Content-Type: application/json" \
-d '{
  "to": "receiver@example.com",
  "subject": "Alerta de Sistema",
  "body": "Se ha detectado una actividad inusual en el puerto 8000."
}'
---
🧠 Notas Técnicas

Celery Broker: Redis

Backend: PostgreSQL / SQLite

API Docs: /docs/ (generado por DRF o Swagger según configuración)

Healthcheck: /healthz/

---
