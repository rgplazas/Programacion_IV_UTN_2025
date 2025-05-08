# Guía Angular 19.2.5 - UTN Programación IV 2025 🚀

## Índice
1. [Requisitos Previos](#requisitos-previos)
2. [Instalación y Configuración](#instalación-y-configuración)
3. [Estructura del Proyecto](#estructura-del-proyecto)
4. [Conceptos Fundamentales](#conceptos-fundamentales)
5. [Ejemplos Paso a Paso](#ejemplos-paso-a-paso)
6. [Buenas Prácticas](#buenas-prácticas)
7. [Recursos Adicionales](#recursos-adicionales)

## Requisitos Previos

Antes de comenzar, asegúrate de tener instalado:

- Node.js (versión 20.x o superior)
- npm (Node Package Manager, incluido con Node.js)
- Git (para control de versiones)
- Un editor de código (recomendado: Visual Studio Code)

## Instalación y Configuración

1. **Instalar Angular CLI globalmente:**
```bash
npm install -g @angular/cli@19.2.5
```

2. **Verificar la instalación:**
```bash
ng version
```

3. **Crear un nuevo proyecto:**
```bash
ng new mi-proyecto
cd mi-proyecto
```

4. **Iniciar el servidor de desarrollo:**
```bash
ng serve
```

## Estructura del Proyecto

La estructura básica de nuestro proyecto Angular incluye:

```
src/
├── app/
│   ├── classes/       # Clases y modelos
│   ├── directives/    # Directivas personalizadas
│   ├── pages/         # Componentes de páginas
│   ├── pipes/         # Pipes personalizados
│   ├── services/      # Servicios
│   ├── app.component.ts
│   └── app.routes.ts
├── environments/      # Configuraciones por entorno
└── assets/           # Recursos estáticos
```

## Conceptos Fundamentales

### 1. Componentes
Los componentes son los bloques fundamentales de una aplicación Angular. Cada componente consta de:

- Un archivo TypeScript (.ts) con la lógica
- Una plantilla HTML (.html)
- Estilos CSS (.css)
- Un archivo de pruebas (.spec.ts)

Ejemplo de un componente básico:

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-ejemplo',
  standalone: true,
  template: '<h1>¡Hola Mundo!</h1>'
})
export class EjemploComponent {
  // Lógica del componente
}
```

### 2. Servicios
Los servicios son clases que manejan la lógica de negocio y datos. Se utilizan para compartir información entre componentes.

Ejemplo básico de un servicio:

```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class EjemploService {
  getData() {
    return ['item1', 'item2', 'item3'];
  }
}
```

## Ejemplos Paso a Paso 🔍

### 1. Classes (Clases)

En la carpeta `src/app/classes` encontrarás ejemplos de clases TypeScript. Las clases son plantillas para crear objetos que encapsulan datos y comportamiento.

#### Ejemplo: Clase Usuario

```typescript
export class Usuario {
  constructor(
    public nombre: string,
    public email: string,
    private id?: string
  ) {}

  getNombreCompleto(): string {
    return this.nombre;
  }
}
```

#### Ejemplo: Clase Mensaje

```typescript
export class Mensaje {
  constructor(
    public contenido: string,
    public fecha: Date,
    public usuarioId: string
  ) {}

  formatearFecha(): string {
    return this.fecha.toLocaleString();
  }
}
```

### 2. Directives (Directivas)

Las directivas permiten extender o modificar el comportamiento del HTML. En `src/app/directives` encontrarás ejemplos como:

#### Ejemplo: Directiva Resaltar

```typescript
import { Directive, ElementRef, Input, OnInit } from '@angular/core';

@Directive({
  selector: '[appResaltar]',
  standalone: true
})
export class ResaltarDirective implements OnInit {
  @Input() color: string = 'yellow';

  constructor(private el: ElementRef) {}

  ngOnInit() {
    this.el.nativeElement.style.backgroundColor = this.color;
  }
}
```

Uso en HTML:
```html
<p [appResaltar]="'lightblue'">Este texto estará resaltado</p>
```

### 3. Pages (Páginas)

Las páginas son componentes principales que representan rutas completas en la aplicación.

#### Ejemplo: Componente Chat

```typescript
import { Component } from '@angular/core';
import { Usuario } from '../../classes/usuario';
import { Mensaje } from '../../classes/mensaje';

@Component({
  selector: 'app-chat',
  standalone: true,
  templateUrl: './chat.component.html',
  styleUrls: ['./chat.component.css']
})
export class ChatComponent {
  mensajes: Mensaje[] = [];
  usuario: Usuario;

  enviarMensaje(contenido: string) {
    const mensaje = new Mensaje(contenido, new Date(), this.usuario.id);
    this.mensajes.push(mensaje);
  }
}
```

### 4. Pipes (Tuberías)

Los pipes transforman datos para su visualización. En `src/app/pipes` encontrarás ejemplos como:

#### Ejemplo: Pipe Saludar

```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'saludar',
  standalone: true
})
export class SaludarPipe implements PipeTransform {
  transform(value: string): string {
    return `¡Hola ${value}!`;
  }
}
```

Uso en HTML:
```html
<p>{{ "Juan" | saludar }}</p> <!-- Muestra: ¡Hola Juan! -->
```

### 5. Services (Servicios)

Los servicios manejan la lógica de negocio y la comunicación con APIs.

#### Ejemplo: Servicio de Autenticación

```typescript
import { Injectable } from '@angular/core';
import { BehaviorSubject } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class AuthService {
  private userSubject = new BehaviorSubject<any>(null);
  user$ = this.userSubject.asObservable();

  async login(email: string, password: string) {
    try {
      // Lógica de autenticación
      const user = { email, id: '123' };
      this.userSubject.next(user);
      return user;
    } catch (error) {
      throw error;
    }
  }

  logout() {
    this.userSubject.next(null);
  }
}
```

## Buenas Prácticas 🎯

1. **Organización del Código**
   - Mantener una estructura de carpetas clara y modular
   - Usar lazy loading para optimizar la carga de módulos

2. **Rendimiento**
   - Implementar `trackBy` en las directivas `*ngFor`
   - Usar `OnPush` change detection cuando sea posible
   - Desuscribirse de observables en `ngOnDestroy`

3. **Seguridad**
   - Validar todas las entradas de usuario
   - Usar HttpInterceptors para manejar tokens
   - Implementar guards para proteger rutas

4. **Testing**
   - Escribir pruebas unitarias para servicios y componentes
   - Usar TestBed para configurar el entorno de pruebas
   - Mantener una cobertura de código alta

## Recursos Adicionales 📚

- [Documentación Oficial de Angular](https://angular.dev)
- [Angular CLI Commands](https://angular.io/cli)
- [Angular Style Guide](https://angular.io/guide/styleguide)
