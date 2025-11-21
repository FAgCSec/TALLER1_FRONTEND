# 📋 Análisis Completo de Tecnologías - Plataforma de Voluntariado

## 📌 Resumen del Proyecto
**Plataforma de Voluntariado** es una aplicación web completa que conecta voluntarios con creadores de eventos de voluntariado. El sistema incluye gestión de usuarios, verificación de documentos con IA, certificados digitales, análisis de sentimientos en reseñas, notificaciones en tiempo real y más.

---

## 🏗️ Arquitectura General

### **Arquitectura de Microservicios**
El proyecto está dividido en múltiples servicios especializados:

1. **Backend Principal (NestJS)** - API REST y WebSockets
2. **API de Sentimientos (FastAPI + Python)** - Análisis de sentimientos con IA
3. **API de Certificados (FastAPI + Python)** - Generación de certificados PDF
4. **API Verificador (FastAPI + Python)** - Verificación de documentos con OCR e IA
5. **Frontend (React)** - Interfaz de usuario web

---

## 🖥️ BACKEND

### 1️⃣ **Backend Principal - NestJS**

#### **Framework y Runtime**
- **Node.js** - Entorno de ejecución JavaScript del lado del servidor
- **NestJS 11.x** - Framework progresivo para construir aplicaciones del lado del servidor eficientes y escalables
- **TypeScript 5.7.x** - Superset de JavaScript con tipado estático para mayor seguridad y productividad

#### **Base de Datos y ORM**
- **MariaDB** - Sistema de gestión de bases de datos relacional (fork de MySQL)
- **TypeORM 0.3.27** - ORM (Object-Relational Mapping) para TypeScript/JavaScript
  - Mapeo de entidades a tablas
  - Migraciones de base de datos
  - Relaciones entre entidades (OneToMany, ManyToOne, etc.)
- **MySQL2 3.15.x** - Driver de MySQL/MariaDB para Node.js

#### **Autenticación y Seguridad**
- **Passport.js 0.7.x** - Middleware de autenticación para Node.js
  - `passport-jwt 4.0.x` - Estrategia de autenticación con JSON Web Tokens
  - `passport-local 1.0.x` - Estrategia de autenticación con usuario/contraseña
  - `passport-oauth2 1.8.x` - Estrategia OAuth 2.0
- **JWT (@nestjs/jwt 11.x)** - Tokens de autenticación para sesiones sin estado
- **bcrypt 6.x** - Librería para hash de contraseñas con algoritmo bcrypt
- **cookie-parser 1.4.x** - Middleware para parsear cookies HTTP

#### **WebSockets - Comunicación en Tiempo Real**
- **Socket.IO (@nestjs/platform-socket.io 11.x)** - Comunicación bidireccional en tiempo real
- **@nestjs/websockets 11.x** - Módulo de WebSockets de NestJS
- **Namespaces implementados:**
  - `/notificaciones` - Notificaciones push a usuarios
  - `/inscripcion` - Cambios en inscripciones de voluntariados
  - `/usuario` - Actualizaciones de perfil de usuario
  - `/verificacion-archivo` - Estado de verificación de documentos
  - `/verificacion-archivo-admin` - Panel de administración de verificaciones

#### **Gestión de Colas y Trabajos Asíncronos**
- **BullMQ 5.63.x** - Sistema de colas basado en Redis para trabajos en segundo plano
- **@nestjs/bullmq 11.x** - Integración de BullMQ con NestJS
- **Redis (IORedis 5.8.x)** - Base de datos en memoria usada como:
  - Backend para colas de BullMQ
  - Cache de datos
  - Almacenamiento de sesiones
- **@nestjs-modules/ioredis 2.x** - Módulo de Redis para NestJS

#### **Sistema de Eventos**
- **EventEmitter2 6.4.x** - Emisor de eventos para arquitectura event-driven
- **@nestjs/event-emitter 3.x** - Módulo de eventos de NestJS
- **Eventos implementados:**
  - `inscripcion.created`, `inscripcion.updated`, `inscripcion.asistencia_marked`
  - Comunicación desacoplada entre módulos

#### **Envío de Correos Electrónicos**
- **Nodemailer 7.x** - Librería para envío de emails
- **Handlebars 4.7.x** - Motor de plantillas para emails HTML
- **Funcionalidades:**
  - Verificación de correo electrónico
  - Recuperación de contraseña
  - Notificaciones por email
  - Templates personalizados (.hbs)

#### **Almacenamiento de Archivos**
- **Cloudinary 1.41.x** - Servicio de almacenamiento en la nube para imágenes y archivos
  - Fotos de perfil de usuarios
  - Imágenes de voluntariados
  - Documentos de verificación
  - Optimización automática de imágenes
- **Multer 2.x** - Middleware para manejo de multipart/form-data
- **multer-storage-cloudinary 4.x** - Integración de Multer con Cloudinary

#### **Validación y Transformación de Datos**
- **class-validator 0.14.x** - Decoradores para validación basada en clases
  - Validación de DTOs (Data Transfer Objects)
  - Reglas personalizadas de validación
- **class-transformer 0.5.x** - Transformación de objetos planos a instancias de clase
  - Serialización/deserialización
  - Exclusión de campos sensibles (@Exclude)
  - Exposición selectiva de propiedades (@Expose)

#### **HTTP y Comunicación con APIs Externas**
- **Axios 1.12.x** - Cliente HTTP para llamadas a:
  - API de Sentimientos (FastAPI)
  - API de Certificados (FastAPI)
  - API Verificador (FastAPI)
- **Form-Data 4.x** - Construcción de formularios multipart para envío de archivos

#### **Configuración y Variables de Entorno**
- **@nestjs/config 4.x** - Gestión de configuración y variables de entorno
- **dotenv** - Carga de variables desde archivos `.env`

#### **Testing**
- **Jest 29.x** - Framework de testing
- **Supertest 7.x** - Testing de APIs HTTP
- **@nestjs/testing** - Utilidades de testing para NestJS

#### **Calidad de Código**
- **ESLint 9.x** - Linter para código TypeScript/JavaScript
  - `@typescript-eslint` - Reglas específicas para TypeScript
  - `eslint-config-prettier` - Integración con Prettier
  - `eslint-plugin-prettier` - Plugin de Prettier
- **Prettier 3.x** - Formateador de código

#### **Compilación y Herramientas de Desarrollo**
- **SWC (@swc/core, @swc/cli)** - Compilador ultrarrápido de TypeScript/JavaScript
- **ts-node 10.x** - Ejecución de TypeScript sin compilación previa
- **ts-loader 9.x** - Loader de TypeScript para Webpack
- **tsconfig-paths 4.x** - Resolución de paths configurados en tsconfig.json

#### **Módulos Funcionales del Backend**
1. **Auth** - Autenticación, login, registro, recuperación de contraseña
2. **Usuario** - Gestión de usuarios (Voluntarios, Creadores, Administradores)
3. **Verificación** - Sistema de verificación de identidad
4. **Voluntariado** - CRUD de eventos de voluntariado
5. **Inscripción** - Gestión de inscripciones a voluntariados
6. **Certificados** - Generación y verificación de certificados
7. **Reseñas** - Sistema de valoraciones y comentarios
8. **Notificaciones** - Sistema de notificaciones push
9. **Estadísticas** - Métricas de voluntarios
10. **Ciudad/Departamento** - Gestión de ubicaciones geográficas
11. **Categoría** - Categorización de voluntariados
12. **Mail** - Envío de correos electrónicos
13. **Cloudinary** - Gestión de archivos en la nube
14. **Token** - Gestión de tokens de verificación
15. **Bull** - Configuración de colas de trabajos

---

### 2️⃣ **API de Sentimientos - FastAPI + Python**

#### **Framework**
- **FastAPI** - Framework web moderno para construir APIs con Python
  - Alto rendimiento
  - Validación automática con Pydantic
  - Documentación automática (OpenAPI/Swagger)
- **Uvicorn** - Servidor ASGI de alto rendimiento

#### **Inteligencia Artificial - Análisis de Sentimientos**
- **pysentimiento** - Librería de NLP (Natural Language Processing) para análisis de sentimientos en español
  - Basada en modelos transformer pre-entrenados
  - Clasificación: Positivo, Negativo, Neutro
  - Probabilidades de cada sentimiento
- **TensorFlow** - Framework de aprendizaje profundo (dependency de pysentimiento)
  - Ejecución de modelos de IA
  - Logs silenciados para producción

#### **Funcionalidad**
- **Endpoint `/upload-comment`**:
  - Recibe comentarios de reseñas de voluntariados
  - Analiza el sentimiento del texto
  - Calcula automáticamente calificación de 1-5 estrellas basada en sentimiento
  - Retorna: sentimiento, probabilidades y estrellas

---

### 3️⃣ **API de Certificados - FastAPI + Python**

#### **Framework**
- **FastAPI** - Framework web para la API
- **Uvicorn** - Servidor ASGI

#### **Generación de PDFs**
- **Jinja2** - Motor de plantillas para HTML
  - Plantillas de certificados personalizables
  - Renderizado dinámico de datos
- **Templates HTML** - Certificados diseñados en HTML/CSS
  - Ubicación: `templates/certificados/certificado.html`

#### **Códigos QR**
- **qrcode** - Generación de códigos QR
  - QR con URL de verificación del certificado
  - Incrustado en el PDF como imagen base64

#### **Procesamiento de Imágenes**
- **Pillow (PIL)** - Manipulación de imágenes
  - Logos de organizaciones
  - Firmas digitales
  - Generación de QR

#### **HTTP Client**
- **Requests** - Cliente HTTP para descargar imágenes (logos, firmas)

#### **Configuración**
- **python-dotenv** - Carga de variables de entorno

#### **Funcionalidad**
- **Endpoint `/generar-certificado`**:
  - Recibe datos del voluntario y voluntariado
  - Genera certificado PDF personalizado
  - Incluye: nombre, voluntariado, horas, fecha, QR de verificación
  - Retorna el PDF para descarga o almacenamiento

---

### 4️⃣ **API Verificador - FastAPI + Python**

#### **Framework**
- **FastAPI** - Framework web
- **Uvicorn** - Servidor ASGI

#### **Procesamiento de PDFs**
- **pdf2image** - Conversión de páginas PDF a imágenes
  - Extrae cada página del PDF como imagen PNG/JPG
  - Preprocesamiento para OCR e IA

#### **OCR (Reconocimiento Óptico de Caracteres)**
- **Tesseract OCR** - Motor OCR de código abierto
- **pytesseract** - Wrapper de Python para Tesseract
  - Extracción de texto de imágenes
  - Lenguaje: Español (`lang="spa"`)
  - Identificación de palabras clave en documentos

#### **Inteligencia Artificial - Clasificación de Documentos**
- **Roboflow** - Plataforma de visión por computadora
  - Modelo personalizado de clasificación de documentos
  - API Key: `voBwqId9so3ANHW7nMlj`
  - Modelo: `verificador-documentos-p1t7m`
  - Clases: Cédula de Ciudadanía, Cámara de Comercio
  - Confianza (confidence score)

#### **Procesamiento de Imágenes**
- **Pillow (PIL)** - Manipulación de imágenes
  - Conversión de formatos
  - Corrección de orientación EXIF
  - Redimensionamiento para IA
- **NumPy** - Operaciones numéricas con arrays

#### **Seguridad**
- **Cifrado AES** - Encriptación de archivos sensibles
  - Documentos de identidad cifrados en disco
  - Desencriptación bajo demanda

#### **Funcionalidad**
- **Endpoint `/analizar-pdf`**:
  - Recibe documento PDF
  - Convierte páginas a imágenes
  - Aplica OCR para extracción de texto
  - Clasifica con IA (Roboflow)
  - Combina resultados OCR + IA para determinar tipo de documento
  - Validación de cédulas y documentos empresariales

---

### 🔧 **Orquestación de Servicios**

#### **Concurrently 9.2.x**
- Ejecución simultánea de múltiples servicios desde un solo comando
- Script `npm start` en `/backend/package.json` inicia:
  1. NestJS (puerto 3000)
  2. API Sentimiento (puerto 8000)
  3. API Verificador (puerto 8001)
  4. API Certificados (puerto 8002)

#### **Docker**
- **Redis** ejecutándose en contenedor Docker
- Comando: `docker exec -it mi-redis redis-cli`

---

## 🌐 FRONTEND

### **Framework Principal**
- **React 19.2.x** - Librería de JavaScript para construir interfaces de usuario
- **React DOM 19.2.x** - Renderizado de React en el DOM

### **Routing y Navegación**
- **React Router 7.9.x** - Enrutamiento declarativo para React
- **React Router DOM 7.9.x** - Bindings DOM para React Router
- **Rutas protegidas implementadas:**
  - `ProtectedRoute` - Requiere autenticación
  - `AdministratorRoute` - Solo administradores
  - `CreatorRoute` - Solo creadores
  - `VolunteerRoute` - Solo voluntarios
  - `UnverifiedEmailRoute` - Manejo de emails no verificados

### **Build Tools**
- **Create React App (react-scripts 5.0.1)** - Configuración y scripts de build
  - Webpack configurado
  - Babel para transpilación
  - Hot Module Replacement (HMR)

### **HTTP Client**
- **Axios 1.12.x** - Cliente HTTP para peticiones a la API
  - Configuración centralizada en `AxiosConfig.jsx`
  - Interceptores para manejo de errores 401
  - Soporte de cookies (withCredentials: true)
  - Base URL configurable por entorno

### **WebSockets - Tiempo Real**
- **Socket.IO Client 4.8.x** - Cliente WebSocket para comunicación en tiempo real
- **Hooks personalizados:**
  - `useNotificacionesSocket.js` - Notificaciones push
  - `useInscripcionSocket.js` - Actualizaciones de inscripciones
  - `useUserSocket.js` - Cambios de usuario
  - `useVerificacionArchivoSocket.js` - Estado de verificación (usuario)
  - `useVerificacionArchivoSocketAdmin.js` - Panel admin de verificaciones

### **Gestión de Estado**
- **React Context API** - Estado global de la aplicación
  - `AuthContext.jsx` - Estado de autenticación del usuario
  - Información de sesión compartida entre componentes

### **UI Components y Diseño**

#### **Material-UI (MUI)**
- **@mui/material 7.3.x** - Biblioteca de componentes React con Material Design
- **@mui/lab 7.0.x** - Componentes experimentales de MUI
- **@mui/x-date-pickers 8.16.x** - Selectores de fecha y hora
- **@emotion/react 11.14.x** - Librería de CSS-in-JS
- **@emotion/styled 11.14.x** - Componentes estilizados con Emotion

#### **Alertas y Notificaciones**
- **SweetAlert2 11.26.x** - Alertas personalizadas y hermosas
  - Confirmaciones de acciones
  - Alertas de éxito/error
  - Inputs modales
  - Componentes personalizados: `ConfirmAlert.jsx`, `TextAreaAlert.jsx`
- **React Hot Toast 2.6.x** - Notificaciones toast minimalistas
  - Notificaciones no invasivas
  - Posición configurable

#### **Iconos**
- **React Icons 5.5.x** - Colección de iconos populares
  - Font Awesome
  - Material Design Icons
  - Bootstrap Icons
  - Y más...

#### **Formularios**
- **React Select 5.10.x** - Select/dropdown mejorado
  - Multi-select
  - Búsqueda
  - Autocompletado
  - Creación de opciones

### **Mapas Interactivos**
- **Mapbox GL JS 2.15.x** - Biblioteca de mapas interactivos
- **React Map GL 7.1.6** - Wrapper de React para Mapbox
- **Funcionalidades:**
  - Visualización de ubicaciones de voluntariados
  - Selección de ubicación en mapa
  - Marcadores personalizados
  - Geocodificación (coordenadas ↔ direcciones)
  - Estilos de mapas: `mapbox://styles/mapbox/streets-v11`
- **API de Geocodificación de Mapbox**:
  - Conversión de coordenadas a direcciones
  - Obtención de ciudad y departamento
  - Servicio: `geocodingService.jsx`

### **Utilidades de Fecha**
- **date-fns 4.1.x** - Librería moderna para manipulación de fechas
  - Formateo de fechas
  - Cálculos de tiempo
  - Comparaciones de fechas

### **Hooks Personalizados**
- `useSidebar.js` - Gestión de sidebar
- `useInscripcionSocket.js`, `useNotificacionesSocket.js`, etc. - WebSockets
- Hooks de React: `useState`, `useEffect`, `useContext`, `useCallback`, `useMemo`

### **Testing**
- **@testing-library/react 16.3.x** - Testing de componentes React
- **@testing-library/jest-dom 6.9.x** - Matchers personalizados de Jest
- **@testing-library/user-event 13.x** - Simulación de interacciones de usuario
- **@testing-library/dom 10.4.x** - Utilidades de testing para DOM

### **Performance y Métricas**
- **Web Vitals 2.1.x** - Métricas de rendimiento web
  - Largest Contentful Paint (LCP)
  - First Input Delay (FID)
  - Cumulative Layout Shift (CLS)

### **Utilidades**
- **ScrollToTop Component** - Scroll automático al cambiar de ruta

---

## 🗄️ BASE DE DATOS

### **MariaDB/MySQL**
- **Esquema:** `bd_voluntariado`
- **Charset:** `utf8mb4` (soporte completo de Unicode incluyendo emojis)
- **Collation:** `utf8mb4_unicode_ci`

### **Tablas Principales**
1. **Usuario** - Datos de usuarios (voluntarios, creadores, admins)
2. **Voluntario** - Perfil de voluntarios
3. **Creador** - Perfil de creadores (Persona Natural / Organización)
4. **Administrador** - Perfil de administradores
5. **Voluntariado** - Eventos de voluntariado
6. **Inscripcion** - Inscripciones a voluntariados
7. **Certificado** - Certificados digitales emitidos
8. **Resena_Voluntariado** - Reseñas y calificaciones
9. **Verificacion** - Solicitudes de verificación de identidad
10. **Verificacion_Archivo** - Archivos adjuntos (cédulas, documentos)
11. **Notificacion** - Sistema de notificaciones
12. **Categoria** - Categorías de voluntariados
13. **Ciudad / Departamento** - Datos geográficos
14. **Ubicacion** - Ubicaciones geográficas de voluntariados
15. **Estadisticas_Voluntario** - Métricas de participación
16. **Token** - Tokens de verificación y recuperación
17. **Fotos_Voluntariado** - Galería de fotos de eventos

### **Relaciones**
- Claves foráneas (Foreign Keys) entre todas las tablas
- Relaciones OneToMany, ManyToOne, OneToOne implementadas en TypeORM

---

## 🌍 SERVICIOS EXTERNOS

### **1. Cloudinary**
- **Uso:** Almacenamiento en la nube de imágenes y archivos
- **Configuración:** API Key, API Secret, Cloud Name
- **Archivos almacenados:**
  - Fotos de perfil de usuarios
  - Imágenes de voluntariados
  - Documentos de verificación (cédulas, cámara de comercio)
  - Certificados PDF generados

### **2. Mapbox**
- **Uso:** Mapas interactivos y geocodificación
- **API Token:** `REACT_APP_MAPBOX_TOKEN`
- **Servicios utilizados:**
  - Mapbox GL JS - Renderizado de mapas
  - Geocoding API - Conversión coordenadas ↔ direcciones
  - Estilos de mapas predefinidos

### **3. Roboflow**
- **Uso:** Clasificación de documentos con IA
- **Modelo:** `verificador-documentos-p1t7m/1`
- **API Key:** `voBwqId9so3ANHW7nMlj`
- **Clases:** Cédula de Ciudadanía, Cámara de Comercio

### **4. Tesseract OCR**
- **Uso:** Reconocimiento óptico de caracteres
- **Instalación local:** `C:\Program Files\Tesseract-OCR\tesseract.exe`
- **Idioma:** Español (spa)

### **5. Gmail SMTP**
- **Uso:** Envío de correos electrónicos
- **Host:** smtp.gmail.com
- **Puerto:** 587
- **Seguridad:** TLS

### **6. Redis**
- **Uso:** 
  - Colas de trabajos (BullMQ)
  - Cache de datos
  - Almacenamiento de sesiones
- **Contenedor:** Docker
- **Puerto:** 6379

---

## 🔐 SEGURIDAD

### **Autenticación**
- JWT (JSON Web Tokens) para sesiones stateless
- Cookies HTTP-only para almacenar tokens
- Refresh tokens para renovación de sesiones
- Tokens de recuperación de contraseña con expiración

### **Encriptación**
- **bcrypt** - Hash de contraseñas (salt rounds)
- **AES** - Cifrado simétrico de archivos sensibles
- **HTTPS** - Comunicación cifrada (en producción)

### **Validación**
- Validación de DTOs con `class-validator`
- Sanitización de inputs
- Validación de tipos de archivo (PDF, imágenes)
- Validación de tamaños de archivo

### **Autorización**
- Guards de autenticación (JwtAuthGuard)
- Guards de roles (AdminGuard, CreadorGuard, VoluntarioGuard)
- Verificación de permisos a nivel de ruta
- Verificación de estado de verificación de usuario

### **CORS**
- Configuración de CORS en backend
- Origen permitido: `http://localhost:3000`
- Credenciales habilitadas
- Métodos: GET, POST, PUT, DELETE, PATCH, OPTIONS

---

## 📦 GESTIÓN DE PAQUETES

### **Backend**
- **npm** - Gestor de paquetes de Node.js
- **package.json** - Dependencias y scripts

### **Frontend**
- **npm** - Gestor de paquetes
- **package.json** - Dependencias y scripts

### **Python**
- **pip** - Gestor de paquetes de Python
- **requirements.txt** - Dependencias de cada servicio FastAPI

---

## 🛠️ HERRAMIENTAS DE DESARROLLO

### **Control de Versiones**
- **Git** - Sistema de control de versiones
- **GitHub** - Repositorio remoto
  - Owner: Plataforma-Voluntariado
  - Repo: Voluntariado
  - Branch principal: main

### **IDE/Editor**
- **Visual Studio Code** - Editor de código recomendado

### **Terminal**
- **PowerShell** - Shell predeterminado (Windows)
- **Bash** - Shell alternativo

### **Scripts de Automatización**
- `copy-db.js` - Copia de archivos de base de datos
- Scripts npm para desarrollo y producción

---

## 🌐 VARIABLES DE ENTORNO

### **Backend NestJS (.env)**
```
# Base de datos
DB_HOST=localhost
DB_PORT=3306
DB_USER=root
DB_PASSWORD=
DB_DATABASE=bd_voluntariado

# JWT
JWT_SECRET=
JWT_RECOVERY_SECRET=
EXPIRES_IN=7d
EXPIRES_TEMPORAL_IN=24h

# Cloudinary
CLOUDINARY_CLOUD_NAME=
CLOUDINARY_API_KEY=
CLOUDINARY_API_SECRET=

# Email
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USER=
MAIL_PASSWORD=

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379

# URLs
FRONTEND_URL=http://localhost:3000
BACKEND_URL=http://localhost:3000
FASTAPI_URL=http://localhost:8001
FASTAPISENTIMIENTO_URL=http://localhost:8000/upload-comment
CERTIFICADOS_FASTAPI_URL=http://localhost:8002
```

### **Frontend React (.env)**
```
REACT_APP_URL_SERVER_VOLUNTARIADO=http://localhost:3000
REACT_APP_MAPBOX_TOKEN=
```

### **FastAPI Certificados (.env)**
```
# Variables específicas del servicio
```

---

## 📊 PATRONES Y ARQUITECTURA

### **Backend**
- **Arquitectura de Capas**:
  - Controllers - Manejo de peticiones HTTP
  - Services - Lógica de negocio
  - Repositories - Acceso a datos
  - Entities - Modelos de datos
  - DTOs - Data Transfer Objects
  - Guards - Protección de rutas
  - Interceptors - Transformación de respuestas
  - Filters - Manejo de excepciones
  - Gateways - WebSockets

- **Patrón Repository** - Abstracción de acceso a datos
- **Dependency Injection** - Inyección de dependencias (NestJS)
- **Event-Driven Architecture** - Comunicación basada en eventos
- **Microservices** - Servicios especializados independientes

### **Frontend**
- **Component-Based Architecture** - Componentes reutilizables
- **Container/Presentational Pattern** - Separación de lógica y presentación
- **Custom Hooks** - Reutilización de lógica
- **Context API** - Estado global compartido
- **Protected Routes** - Rutas protegidas por autenticación

---

## 📈 FEATURES PRINCIPALES

### **Usuarios**
- ✅ Registro de usuarios (Voluntario / Creador)
- ✅ Login y autenticación con JWT
- ✅ Verificación de email
- ✅ Recuperación de contraseña
- ✅ Perfiles de usuario con foto
- ✅ Gestión de información personal

### **Verificación de Identidad**
- ✅ Subida de documentos (cédula, cámara de comercio)
- ✅ Análisis automático con IA (OCR + Roboflow)
- ✅ Revisión manual por administradores
- ✅ Estados: Pendiente, Aceptado, Rechazado
- ✅ Notificaciones en tiempo real del estado

### **Voluntariados**
- ✅ Creación de eventos de voluntariado
- ✅ Búsqueda y filtrado de voluntariados
- ✅ Categorización
- ✅ Ubicación geográfica en mapa
- ✅ Galería de fotos
- ✅ Gestión de cupos
- ✅ Fechas de inicio y fin

### **Inscripciones**
- ✅ Inscripción a voluntariados
- ✅ Gestión de participantes por creadores
- ✅ Control de asistencia
- ✅ Estados: Pendiente, Aceptado, Rechazado, Asistió
- ✅ Notificaciones de cambios de estado

### **Certificados Digitales**
- ✅ Generación automática de certificados PDF
- ✅ Código QR de verificación
- ✅ Personalización con logos y firmas
- ✅ Descarga de certificados
- ✅ Verificación pública de autenticidad
- ✅ Almacenamiento en Cloudinary

### **Reseñas y Calificaciones**
- ✅ Sistema de comentarios
- ✅ Análisis de sentimientos con IA
- ✅ Cálculo automático de estrellas (1-5)
- ✅ Probabilidades: Positivo, Negativo, Neutro
- ✅ Solo usuarios que asistieron pueden reseñar

### **Notificaciones**
- ✅ Sistema de notificaciones push en tiempo real (WebSocket)
- ✅ Notificaciones por email
- ✅ Tipos:
  - Cambios en inscripciones
  - Verificaciones aprobadas/rechazadas
  - Nuevos voluntariados
  - Recordatorios
- ✅ Estados: Leído / No leído

### **Estadísticas**
- ✅ Métricas de voluntarios
- ✅ Horas totales de voluntariado
- ✅ Número de eventos completados
- ✅ Calificación promedio

### **Panel de Administración**
- ✅ Gestión de usuarios
- ✅ Revisión de verificaciones
- ✅ Moderación de contenido
- ✅ Bloqueo/desbloqueo de usuarios
- ✅ Estadísticas generales

---

## 🚀 COMANDOS DE EJECUCIÓN

### **Desarrollo**
```bash
# Backend (todos los servicios)
cd backend
npm start

# Frontend
cd frontend
npm start

# Redis (Docker)
docker run -d -p 6379:6379 --name mi-redis redis
```

### **Producción**
```bash
# Backend NestJS
cd backend/nest-app
npm run build
npm run start:prod

# Frontend React
cd frontend
npm run build
# Servir carpeta build/ con servidor estático
```

---

## 👥 EQUIPO DE DESARROLLO

- **David Alexis Medina Trujillo** (@Davidshtp)
- **Felipe Fajardo Castro** (@FelipeFajardo1)
- **Jose Manuel Pantoja** (@Neonwaac)
- **Edwin Fabian Agudelo** (@FAgCSec)

---

## 📝 CONCLUSIÓN

Este proyecto es una **plataforma completa de voluntariado** construida con tecnologías modernas y escalables. Integra:

- ✅ **Backend robusto** con NestJS, TypeORM y arquitectura de microservicios
- ✅ **Inteligencia Artificial** para análisis de sentimientos y verificación de documentos
- ✅ **Frontend moderno** con React, Material-UI y mapas interactivos
- ✅ **Comunicación en tiempo real** con WebSockets
- ✅ **Seguridad** con JWT, bcrypt y cifrado AES
- ✅ **Escalabilidad** con Redis, BullMQ y arquitectura event-driven
- ✅ **Servicios externos** (Cloudinary, Mapbox, Roboflow)

La arquitectura de microservicios permite escalar cada componente de forma independiente y mantener la separación de responsabilidades. El uso de TypeScript en el backend y JavaScript/JSX en el frontend garantiza código tipado y mantenible. Las tecnologías de IA proporcionan valor agregado mediante automatización inteligente de procesos.
