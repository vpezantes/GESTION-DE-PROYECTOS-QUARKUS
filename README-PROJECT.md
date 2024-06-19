# Arquitectura de Microservicios - Gestión de Proyectos

## Descripción General

Este proyecto implementa una arquitectura de microservicios para la gestión de proyectos, específicamente en el ámbito de concesiones mineras. Cada servicio tiene responsabilidades específicas, desde la autenticación de usuarios hasta la gestión de notificaciones, asegurando una estructura modular y escalable.

## Servicios

### 1. Servicio de Autenticación y Autorización (Auth Service)
- **Responsabilidades:**
    - Registro y autenticación de usuarios.
    - Gestión de roles y permisos.
- **Endpoints:**
    - POST `/register` - Registro de nuevos usuarios.
    - POST `/login` - Autenticación de usuarios.
    - GET `/user/{id}` - Obtener detalles del usuario.
    - POST `/roles` - Asignación de roles y permisos.
- **Tecnología:** Keycloak (recomendado) o JWT.

### 2. Servicio de Gestión de Proyectos (Project Service)
- **Responsabilidades:**
    - Creación, actualización y eliminación de proyectos.
    - Gestión del ciclo de vida de los proyectos (cambio de estados).
- **Endpoints:**
    - POST `/projects` - Crear un nuevo proyecto.
    - GET `/projects` - Listar todos los proyectos.
    - GET `/projects/{id}` - Obtener detalles de un proyecto.
    - PUT `/projects/{id}` - Actualizar un proyecto.
    - DELETE `/projects/{id}` - Eliminar un proyecto.
    - PUT `/projects/{id}/status` - Cambiar el estado de un proyecto.
- **Estados del Proyecto:**
    - Planificación
    - En progreso
    - Completado
    - Cancelado

### 3. Servicio de Gestión de Concesiones (Concession Service)
- **Responsabilidades:**
    - Creación, actualización y eliminación de concesiones mineras.
    - Asociación de concesiones a proyectos específicos.
- **Endpoints:**
    - POST `/concessions` - Crear una nueva concesión.
    - GET `/concessions` - Listar todas las concesiones.
    - GET `/concessions/{id}` - Obtener detalles de una concesión.
    - PUT `/concessions/{id}` - Actualizar una concesión.
    - DELETE `/concessions/{id}` - Eliminar una concesión.
    - PUT `/concessions/{id}/projects/{projectId}` - Asociar una concesión a un proyecto.

### 4. Servicio de Almacenamiento de Imágenes (Image Service)
- **Responsabilidades:**
    - Subida y gestión de imágenes relacionadas con los proyectos y concesiones.
- **Endpoints:**
    - POST `/images/upload` - Subir una imagen.
    - GET `/images/{id}` - Obtener una imagen.
    - DELETE `/images/{id}` - Eliminar una imagen.
- **Tecnología:** MinIO o Amazon S3 para almacenamiento de imágenes.

### 5. Servicio de Notificaciones (Notification Service)
- **Responsabilidades:**
    - Envío de notificaciones y alertas a los usuarios.
- **Endpoints:**
    - POST `/notifications` - Enviar una notificación.
    - GET `/notifications` - Listar todas las notificaciones.
    - GET `/notifications/{id}` - Obtener detalles de una notificación.
- **Mensajería:** Kafka para la comunicación entre servicios y notificaciones en tiempo real.

## Herramientas y Tecnologías

- **Framework:** Quarkus
- **Base de Datos:** PostgreSQL para datos estructurados y MinIO/S3 para el almacenamiento de imágenes.
- **Autenticación y Autorización:** Keycloak o JWT (JSON Web Tokens).
- **Mensajería:** Kafka para la comunicación entre microservicios.
- **API Gateway:** NGINX o Spring Cloud Gateway.
- **Contenedores:** Docker y Kubernetes para despliegue.

## Implementación de Microservicios con Quarkus

### Configuración de Quarkus
1. Crear un nuevo proyecto Quarkus usando Maven o Gradle.
2. Configurar las dependencias necesarias para cada microservicio (RestEasy, Hibernate, Keycloak, etc.).

### Integración y Despliegue

- **Docker:** Crear archivos Docker para cada microservicio.
- **Kubernetes:** Definir deployments y servicios en archivos YAML.
- **CI/CD:** Configurar pipelines de integración y despliegue continuo usando herramientas como Jenkins o GitHub Actions.

## Modelo de Datos

```sql
-- Crear la tabla de usuarios
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    password VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    role VARCHAR(50) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

-- Crear la tabla de proyectos
CREATE TABLE projects (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    status VARCHAR(50) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    user_id INT,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL
);

-- Crear la tabla de concesiones
CREATE TABLE concessions (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    location VARCHAR(255),
    area DECIMAL(10, 2),
    status VARCHAR(50) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    project_id INT,
    FOREIGN KEY (project_id) REFERENCES projects(id) ON DELETE SET NULL
);

-- Crear la tabla de imágenes
CREATE TABLE images (
    id INT AUTO_INCREMENT PRIMARY KEY,
    url TEXT NOT NULL,
    description TEXT,
    project_id INT,
    concession_id INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (project_id) REFERENCES projects(id) ON DELETE CASCADE,
    FOREIGN KEY (concession_id) REFERENCES concessions(id) ON DELETE CASCADE
);

-- Crear la tabla de notificaciones
CREATE TABLE notifications (
    id INT AUTO_INCREMENT PRIMARY KEY,
    message TEXT NOT NULL,
    user_id INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    read BOOLEAN DEFAULT FALSE,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

## Relaciones Entre Entidades

- **Relación Usuario-Proyecto:**
    - Un usuario puede crear múltiples proyectos.
    - Un proyecto es creado por un único usuario.

- **Relación Proyecto-Concesión:**
    - Un proyecto puede tener múltiples concesiones.
    - Una concesión pertenece a un único proyecto.

- **Relación Proyecto-Imagen y Concesión-Imagen:**
    - Un proyecto puede tener múltiples imágenes.
    - Una concesión puede tener múltiples imágenes.
    - Una imagen puede estar asociada a un proyecto o a una concesión, pero no a ambos simultáneamente.

- **Relación Usuario-Notificación:**
    - Un usuario puede recibir múltiples notificaciones.
    - Una notificación es recibida por un único usuario.

---


