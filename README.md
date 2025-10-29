📧 Microservicio de Notificaciones (Email Service)Día 4 — 28/10/2025Este proyecto es un microservicio independiente encargado de centralizar y gestionar el envío de notificaciones y correos electrónicos para el ecosistema de microservicios (Auth, Blog, etc.). Utiliza Celery y Redis para manejar las tareas de envío de forma asíncrona, garantizando resiliencia y escalabilidad.CaracterísticaDetalleRolMicroservicio Backend de Notificaciones/EmailPuerto Sugerido8002TecnologíaDjango 5.0, Django REST Framework, Celery, RedisBase de DatosPostgreSQL (o SQLite local)ObjetivoRecibir solicitudes de correo, persistirlas, y enviarlas de forma asíncrona.🎯 Requisitos Funcionales y EndpointsEl servicio expone dos endpoints principales, uno público para formularios de contacto y otro interno para notificaciones programadas.1. Endpoint Público: ContactoUtilizado por el frontend o cualquier servicio para recibir mensajes de contacto.MétodoURLDescripciónPOST/api/contact/Almacena un mensaje de contacto en BD y encola su notificación.Cuerpo de la Solicitud (Request Body):{
  "name": "Carlos Rivas",
  "email": "carlos@mail.com",
  "message": "Me interesa una colaboración"
}
Respuesta Exitosa (Response):{
  "status": "queued",
  "message": "Mensaje de contacto recibido y encolado para notificación."
}
2. Endpoint Interno: Notificación Genérica (Opcional)Utilizado internamente por otros microservicios (como Blog o Auth) para enviar correos específicos (ej. "restablecimiento de contraseña", "nuevo post").MétodoURLDescripciónPOST/api/notify/Recibe un evento de notificación y encola su envío.Cuerpo de la Solicitud (Request Body):{
  "to": "usuario@mail.com",
  "subject": "Nuevo Post Publicado",
  "body": "¡Hola! Hemos lanzado un nuevo artículo sobre Microservicios."
}
⚙️ Estructura del ProyectoLa aplicación sigue una estructura modular para Django, utilizando una app dedicada (notifications) para el manejo de modelos, vistas y tareas.email-service/
├── app/
│   ├── email_service/     # Configuración central (settings.py)
│   └── notifications/
│        ├── models.py      # ContactMessage, NotificationLog
│        ├── serializers.py # Validadores de datos
│        ├── views.py       # ContactViewSet, NotifyViewSet
│        └── tasks.py       # Tareas asíncronas con Celery (send_email_task)
├── openapi.yaml
├── requirements.txt
├── manage.py
└── Dockerfile
🛠️ Observabilidad y ResilienciaEl servicio está diseñado con enfoque en la resiliencia operativa:Cola Asíncrona (Celery + Redis):Todas las operaciones de envío de correo se encolan, evitando el bloqueo del hilo principal de la API.Reintentos: Las tareas de envío están configuradas con reintentos automáticos (máximo 3 reintentos con un delay de 5 segundos), garantizando la entrega a pesar de fallos transitorios.Healthcheck (/healthz):Endpoint disponible para monitorear el estado de los servicios vitales (conexión a la DB y Redis).Logs Estructurados:Se implementará logging estructurado (JSON) para registrar la entrada, salida y tiempo de procesamiento de las solicitudes, facilitando la observabilidad.Idempotencia (Opcional):Se puede implementar un mecanismo de idempotencia (basado en un UUID proporcionado por el cliente) para asegurar que un mismo mensaje entrante solo se procese una vez.🧪 Cómo Correr el Servicio1. RequisitosPython 3.10+pipUn servidor Redis corriendo localmente (necesario para Celery).2. Instalación y EjecuciónClonar el repositorio y navegar a la carpeta raíz.Instalar dependencias:# Crear y activar entorno virtual (si no lo hace el script)
python -m venv venv
.\venv\Scripts\activate  # En Windows Powershell

pip install -r requirements.txt
Ejecutar el script de inicio:scripts\start.bat
Este script iniciará el servidor de Django en el puerto 8002.Iniciar el Worker de Celery:El servidor de Celery debe correr en otra ventana de terminal.# Asegúrate de que el entorno virtual esté activo
celery -A email_service worker -l info
🚀 Ejemplos de cURLUtiliza estos comandos para interactuar con el servicio una vez que esté en ejecución.1. HealthcheckVerificar el estado del servicio, la DB y Redis:curl -X GET http://localhost:8002/healthz/
2. POST /api/contact/ (Formulario de Contacto)Enviar un mensaje que será persistido y encolado para notificación:curl -X POST http://localhost:8002/api/contact/ \
-H "Content-Type: application/json" \
-d '{
  "name": "Elon Musk",
  "email": "elon@xcorp.com",
  "message": "Necesito un billón de notificaciones."
}'
3. POST /api/notify/ (Notificación Interna)Simular una notificación enviada desde otro microservicio:curl -X POST http://localhost:8002/api/notify/ \
-H "Content-Type: application/json" \
-d '{
  "to": "receiver@example.com",
  "subject": "Alerta de Sistema",
  "body": "Se ha detectado una actividad inusual en el puerto 8000."
}'
