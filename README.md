# Innovatech Chile - Sistema de Ventas y Despachos

Este repositorio contiene la arquitectura de microservicios para el sistema de Innovatech Chile. El proyecto está dividido en un **Frontend** (React/Vite) y dos microservicios de **Backend** (Ventas y Despachos desarrollados en Java Spring Boot), soportados por una base de datos **MySQL**.

Toda la infraestructura está dockerizada, orquestada mediante Docker Compose para entornos locales, y automatizada hacia la nube (AWS EC2) mediante un pipeline de CI/CD en GitHub Actions.

---

## Arquitectura del Sistema y Contenedorización

El proyecto implementa buenas prácticas de contenedorización (*Docker*), garantizando entornos aislados, seguros y eficientes:

* **Frontend (React + Vite + Nginx):** Utiliza un patrón *Multi-stage build*. Compila los activos estáticos con Node.js y los sirve mediante una imagen ligera de Nginx, minimizando la superficie de ataque y el peso de la imagen.
* **Backend (Java Spring Boot):** Implementa *Multi-stage build* con Maven para la compilación optimizada de dependencias (aprovechando caché) y `eclipse-temurin:17-jre-alpine` para la ejecución. Por seguridad, se ejecuta bajo un **usuario no root** (`spring:spring`).
* **Base de Datos (MySQL 8.0):** Cuenta con un **volumen persistente** (`db_data`) que asegura que la información operativa de la empresa no se pierda ante caídas o reinicios del contenedor.

---

## Requisitos Previos (Prerrequisitos)

Para ejecutar este proyecto de forma local, necesitas tener instalado:
- [Git](https://git-scm.com/)
- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

---

## Cómo utilizar y levantar el proyecto localmente

1. **Clonar el repositorio:**
   ```bash
   git clone <URL_DE_TU_REPOSITORIO>
   cd <NOMBRE_DE_TU_CARPETA>
Levantar los servicios:
Asegúrate de estar en el directorio donde se encuentra el archivo docker-compose.yml y ejecuta:

Bash
docker-compose up -d --build
Nota: La etiqueta -d ejecuta los contenedores en segundo plano y --build fuerza la reconstrucción de las imágenes.

Verificar el estado de los contenedores:

Bash
docker ps
Accesos a los servicios locales:
Una vez que los contenedores estén en estado Up, podrás acceder a:

Frontend Dashboard: http://localhost (Puerto 80)

API de Ventas: http://localhost:8081

API de Despachos: http://localhost:8082

Base de Datos MySQL: localhost:3306

Detener el entorno:
Para detener y eliminar los contenedores (conservando los datos gracias al volumen persistente), ejecuta:

Bash
docker-compose down
Flujo CI/CD (Despliegue Automatizado en AWS)
Este proyecto cuenta con Integración y Despliegue Continuo (CI/CD) completamente automatizado, documentado a continuación:

1. Trigger (Activación)
El pipeline está configurado en los archivos .github/workflows/ y se dispara exclusivamente cuando se detecta un push o la aceptación de un Pull Request hacia la rama main.

2. Etapas del Pipeline
El flujo de GitHub Actions realiza automáticamente los siguientes pasos:

Checkout & Auth: Clona el código y se autentica de forma segura en AWS utilizando GitHub Secrets (AWS Keys y Session Token).

Build & Push: Construye la imagen Docker del servicio modificado y la publica de forma privada en Amazon ECR (Elastic Container Registry) etiquetándola como latest.

Deploy (AWS SSM): Mediante AWS Systems Manager (Run Command), envía instrucciones remotas y seguras a la instancia AWS EC2. Esto le ordena al servidor conectarse a ECR, detener el contenedor antiguo, descargar la nueva imagen (docker pull) y volver a levantarlo, todo esto sin exponer el puerto 22 (SSH) a Internet.

3. Variables de Entorno (Secrets)
Por políticas de seguridad, ninguna credencial está quemada en el código. El despliegue requiere configurar los siguientes Secrets en el repositorio de GitHub:

AWS_ACCESS_KEY_ID

AWS_SECRET_ACCESS_KEY

AWS_SESSION_TOKEN

AWS_REGION

ECR_REGISTRY

Repositorios de ECR: ECR_REPO_URL_FRONTEND, ECR_REPO_URL_VENTAS, ECR_REPO_URL_DESPACHOS

IDs de Instancia: EC2_INSTANCE_ID (y/o EC2_FRONTEND_INSTANCE_ID)

🛠️ Tecnologías Aplicadas
Frontend: React, Vite, Tailwind CSS, Nginx

Backend: Java 17, Spring Boot, Maven

Base de Datos: MySQL 8.0

Infraestructura DevOps: Docker, Docker Compose, GitHub Actions, AWS EC2, AWS ECR, AWS SSM.
