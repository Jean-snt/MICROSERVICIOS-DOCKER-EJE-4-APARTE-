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
