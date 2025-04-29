# Guía Completa Angular 19.2.5 - Clase06

Esta guía detallada está diseñada para estudiantes que comienzan desde cero con Angular 19.2.5. Aquí encontrarás explicaciones profundas sobre los componentes principales de nuestra aplicación.

## Requisitos Previos

- Node.js (versión 18.x o superior)
- npm (incluido con Node.js)
- Angular CLI 19.2.5

### Instalación Inicial

```bash
# Instalar Angular CLI globalmente
npm install -g @angular/cli@19.2.5

# Verificar la instalación
ng version
```

## Estructura del Proyecto

Nuestra aplicación está organizada en las siguientes secciones principales:

### 1. Guards

Los guards son protectores de rutas que controlan el acceso a diferentes partes de la aplicación.

```typescript
// src/app/guards/auth.guard.ts
import { Injectable } from '@angular/core';
import { CanActivate, Router } from '@angular/router';
import { AuthService } from '../services/auth.service';

@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate {
  constructor(
    private authService: AuthService,
    private router: Router
  ) {}

  canActivate(): boolean {
    if (!this.authService.isAuthenticated()) {
      this.router.navigate(['/login']);
      return false;
    }
    return true;
  }
}
```

### 2. Juegos

Nuestra aplicación incluye dos juegos interactivos:

#### Ahorcado
Un juego clásico implementado con Angular, utilizando componentes reactivos y servicios para gestionar el estado.

#### Mayor-Menor
Juego de adivinanza de números que demuestra el uso de servicios y manejo de estados.

### 3. Pages

La aplicación está organizada en las siguientes páginas principales:

- **Home**: Página principal con navegación a los juegos
- **Juegos**: Catálogo de juegos disponibles
- **Login**: Autenticación de usuarios
- **Registro**: Registro de nuevos usuarios

### 4. Services

#### AuthService

```typescript
// src/app/services/auth.service.ts
import { Injectable } from '@angular/core';
import { BehaviorSubject } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class AuthService {
  private isAuthenticatedSubject = new BehaviorSubject<boolean>(false);

  isAuthenticated() {
    return this.isAuthenticatedSubject.value;
  }

  login(credentials: {email: string, password: string}) {
    // Implementación del login
  }

  logout() {
    this.isAuthenticatedSubject.next(false);
  }
}
```

### 5. Environments

Configuración de entornos para desarrollo y producción:

```typescript
// src/environments/environment.ts
export const environment = {
  production: false,
  apiUrl: 'http://localhost:3000',
  firebaseConfig: {
    // Configuración de Firebase
  }
};
```

## Ejemplos Paso a Paso

### 1. Implementación de Guards

```bash
# Generar un guard de autenticación
ng generate guard guards/auth
```

### 2. Creación de Juegos

#### Ahorcado

1. Generar el componente:
```bash
ng generate component games/hangman
```

2. Implementar la lógica del juego en el componente.

#### Mayor-Menor

1. Generar el componente:
```bash
ng generate component games/higher-lower
```

2. Implementar la lógica del juego.

### 3. Configuración de Páginas

```bash
# Generar componentes para las páginas
ng generate component pages/home
ng generate component pages/games
ng generate component pages/login
ng generate component pages/register
```

### 4. Implementación de Servicios

```bash
# Generar el servicio de autenticación
ng generate service services/auth
```

## Buenas Prácticas

1. **Estructura del Proyecto**
   - Mantener una estructura modular
   - Usar lazy loading para módulos grandes
   - Implementar guards para proteger rutas

2. **Servicios**
   - Mantener la lógica de negocio en servicios
   - Usar inyección de dependencias
   - Implementar manejo de errores global

3. **Componentes**
   - Seguir el principio de responsabilidad única
   - Usar OnPush change detection cuando sea posible
   - Implementar lifecycle hooks correctamente

## Comandos Útiles

```bash
# Iniciar servidor de desarrollo
ng serve

# Construir para producción
ng build --prod

# Ejecutar pruebas unitarias
ng test

# Generar nuevos componentes/servicios
ng generate component/service nombre
```

## Recursos Oficiales

- [Documentación oficial de Angular](https://angular.dev/)
- [Angular CLI](https://angular.dev/tools/cli)
- [Angular Material](https://material.angular.io/)
- [RxJS](https://rxjs.dev/)
- [TypeScript](https://www.typescriptlang.org/docs/)

## Notas Técnicas

- Esta aplicación utiliza Angular 19.2.5
- Se recomienda usar Node.js 18.x o superior
- TypeScript 5.x es requerido
- Configurar el IDE (VS Code recomendado) con las extensiones:
  - Angular Language Service
  - TypeScript Hero
  - ESLint
  - Prettier
