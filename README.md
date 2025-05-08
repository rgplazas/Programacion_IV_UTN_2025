# Programación IV - UTN FRRO (2025) 🚀

## Descripción
Este repositorio contiene el material y proyectos desarrollados durante el curso de Programación IV en la Universidad Tecnológica Nacional - Facultad Regional Rosario, correspondiente al ciclo lectivo 2025.

## Tecnologías Principales
- **Angular**: Versión 19.2.5
- **TypeScript**
- **Bootstrap**
- **Node.js**
- **Supabase**

## Requisitos Previos
- Node.js (versión recomendada: 20.x o superior)
- npm (incluido con Node.js)
- Angular CLI (versión 19.2.5)

## Instalación
```bash
# Clonar el repositorio
git clone [URL del repositorio]

# Instalar dependencias
npm install

# Iniciar el servidor de desarrollo
ng serve
```

## Contenido del Curso

### 📚 Clase 01 (10/04/2025) - Fundamentos de Angular
- Introducción a Angular
- Sistema de Bindeos
- Componentes
- Rutas y Navegación

#### 🔗 Recursos
- [Guía Oficial de Angular](https://angular.dev/)
- [Guía de Instalación](https://angular.dev/installation)
- [Documentación de Componentes](https://angular.dev/guide/components)
- [Guía de Bindeos](https://angular.dev/guide/templates/binding)
- [Eventos y Listeners](https://angular.dev/guide/templates/event-listeners)
- [Two-way Binding](https://angular.dev/guide/templates/two-way-binding)
- [CLI y Generación de Componentes](https://angular.dev/cli/generate/component)
- [Ruteo Básico](https://angular.dev/guide/routing/common-router-tasks)

### 📚 Clase 02 (15/04/2025) - Componentes Avanzados
- Ciclo de vida de componentes
- Comunicación entre componentes (Input/Output)
- Directivas estructurales
- Integración con NG Bootstrap

#### 🔗 Recursos
- [Lifecycle Hooks](https://angular.dev/guide/components/lifecycle)
- [Inputs](https://angular.dev/guide/components/inputs)
- [Outputs](https://angular.dev/guide/components/outputs)
- [NG Bootstrap](https://ng-bootstrap.github.io/#/home)
- [Directivas @if](https://angular.dev/api/core/@if)
- [Directivas @for](https://angular.dev/api/core/@for)
- [Directivas @switch](https://angular.dev/api/core/@switch)

### 📚 Clase 03 (16/04/2025) - Formularios y Servicios
- Formularios reactivos
- Validación de formularios
- Servicios e inyección de dependencias
- HTTP Client y comunicación con APIs

#### 🔗 Recursos
- [Formularios Reactivos](https://angular.dev/guide/forms/reactive-forms)
- [Validación de Formularios](https://angular.dev/guide/forms/form-validation)
- [Inyección de Dependencias](https://angular.dev/guide/di/dependency-injection)
- [Servicios](https://angular.dev/guide/di/creating-injectable-service)
- [HTTP Client](https://angular.dev/guide/http/setup)
- [Peticiones HTTP](https://angular.dev/guide/http/making-requests)
- [Observables](https://rxjs.dev/guide/observable)

### 📚 Clase 04 (21/04/2025) - Integración con Supabase
- Finalización Input/Output
- Introducción a Supabase
- Configuración de Base de Datos
- Operaciones CRUD básicas

#### 🔗 Recursos
- [Supabase](http://supabase.com/)
- [Documentación Supabase](https://supabase.com/docs)
- [Documentación Supabase Database](https://supabase.com/docs/guides/database/overview)
- [Supabase Database Guía](https://supabase.com/docs/guides/database/tables?queryGroups=language&language=js)

### 📚 Clase 05 (23/04/2025) - Supabase Avanzado
- Suscripciones en tiempo real
- Autenticación y autorización
- Relaciones y consultas avanzadas

#### 🔗 Recursos
- [Tiempo Real en Supabase](https://supabase.com/docs/guides/realtime/subscribing-to-database-changes)
- [Autenticación](https://supabase.com/docs/guides/auth)
- [Relaciones](https://supabase.com/docs/guides/database/tables#foreign-key-constraints)

### 📚 Clase 06 (28/04/2025) - Guards y Juegos en Angular
- Implementación de Guards
- Desarrollo de juegos interactivos
  - Ahorcado
  - Mayor-Menor
- Estructura de páginas y servicios
- Configuración de environments

#### 🔗 Recursos
- [Guards en Angular](https://angular.dev/guide/routing/common-router-tasks#preventing-unauthorized-access)
- [Servicios y Dependencias](https://angular.dev/guide/di/dependency-injection-providers)
- [Environments en Angular](https://angular.dev/guide/environments)
- [Manejo de Estados](https://angular.dev/guide/signals)
- [Autenticación](https://angular.dev/guide/security)

#### Características Implementadas
- Guards para protección de rutas
- Juegos interactivos con Angular
- Sistema de autenticación
- Manejo de estados con servicios
- Configuración de ambientes

### 📚 Clase 07 (30/04/2025) - Directivas y Pipes Personalizados
- Desarrollo de directivas personalizadas
- Implementación de pipes personalizados
- Clases y modelos de datos
- Servicios de chat en tiempo real

#### 🔗 Recursos
- [Directivas Personalizadas](https://angular.dev/guide/directives)
- [Pipes Personalizados](https://angular.dev/guide/pipes/custom-pipes)
- [Clases en TypeScript](https://www.typescriptlang.org/docs/handbook/2/classes.html)
- [Supabase Realtime](https://supabase.com/docs/guides/realtime)

#### Características Implementadas
- Directiva `resaltar` para destacar elementos
- Pipe `saludar` para formateo de texto
- Clases `Usuario` y `Mensaje` para el chat
- Servicio de chat con Supabase
- Componente de chat en tiempo real

### 📚 Clase 08 (05/05/2025) - Progressive Web Apps (PWA)
- Introducción a PWA
- Service Workers
- Manifest y configuración
- Directivas de resaltado
- Servicios de autenticación

#### 🔗 Recursos
- [Angular PWA](https://angular.dev/guide/service-worker-getting-started)
- [Service Workers](https://angular.dev/guide/service-worker-intro)
- [Web App Manifest](https://web.dev/articles/add-manifest)
- [Autenticación con Supabase](https://supabase.com/docs/guides/auth/overview)

#### Características Implementadas
- Configuración de PWA
- Service Worker para caché
- Manifest personalizado
- Directiva `highlight`
- Servicio de autenticación mejorado
- Íconos y assets para PWA

## Contribución
Si deseas contribuir al proyecto:
1. Realiza un Fork del repositorio
2. Crea una nueva rama para tu feature
3. Realiza tus cambios
4. Envía un Pull Request

## Licencia
Este proyecto está bajo la Licencia MIT.

---
Desarrollado con ❤️ para la UTN FRRO
- [Peticiones HTTP](https://angular.dev/guide/http/making-requests)
- [Observables](https://rxjs.dev/guide/observable)