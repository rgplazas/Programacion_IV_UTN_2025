# Guía Completa Angular 19.2.5 - Clase02

## Requisitos Previos

Antes de comenzar, asegúrate de tener instalado:

1. **Node.js** (versión 18.x o superior)
2. **npm** (versión 10.x o superior)
3. **Angular CLI** (versión 19.2.5)

```bash
npm install -g @angular/cli@19.2.5
```

## Configuración Inicial

1. **Crear un nuevo proyecto Angular:**
```bash
ng new mi-proyecto
cd mi-proyecto
```

2. **Instalar NG Bootstrap:**
```bash
ng add @ng-bootstrap/ng-bootstrap
```

3. **Verificar la instalación:**
```bash
ng version
```

## Estructura del Proyecto

La carpeta `clase02` contiene los siguientes elementos principales:

- 📁 **class**: Definiciones de clases TypeScript
- 📁 **components**: Componentes reutilizables
- 📁 **interfaces**: Interfaces TypeScript
- 📁 **pages**: Componentes de página

---

## Ciclo de Vida de Componentes

Los componentes en Angular 19.2.5 tienen un ciclo de vida predefinido que nos permite ejecutar lógica en momentos específicos.

### Hooks Principales

1. **ngOnInit**: Se ejecuta una vez, después del constructor
```typescript
export class MiComponente implements OnInit {
  constructor() { }
  
  ngOnInit() {
    // Código de inicialización
  }
}
```

2. **ngOnChanges**: Se ejecuta cuando cambian las propiedades de entrada
```typescript
export class MiComponente implements OnChanges {
  @Input() data: any;
  
  ngOnChanges(changes: SimpleChanges) {
    if (changes['data']) {
      // Manejar cambios en data
    }
  }
}
```

3. **ngOnDestroy**: Se ejecuta antes de destruir el componente
```typescript
export class MiComponente implements OnDestroy {
  ngOnDestroy() {
    // Limpieza de recursos
  }
}
```

### Buenas Prácticas

- Implementar `ngOnDestroy` para limpiar suscripciones
- Usar `ngOnChanges` para reaccionar a cambios en inputs
- Evitar lógica pesada en el constructor

---

## Comunicación entre Componentes

En Angular 19.2.5, los componentes pueden comunicarse mediante `@Input()` y `@Output()`.

### @Input() - Recibiendo Datos

```typescript
// componente-hijo.ts
export class ComponenteHijo {
  @Input() mensaje: string;
  @Input('aliasNombre') nombre: string; // usando alias
}

// componente-hijo.html
<div>
  {{ mensaje }} {{ nombre }}
</div>

// componente-padre.html
<app-componente-hijo
  [mensaje]="'Hola'"
  [aliasNombre]="'Juan'">
</app-componente-hijo>
```

### @Output() - Enviando Eventos

```typescript
// componente-hijo.ts
export class ComponenteHijo {
  @Output() eventoClick = new EventEmitter<string>();
  
  emitirEvento() {
    this.eventoClick.emit('Datos del evento');
  }
}

// componente-hijo.html
<button (click)="emitirEvento()">Emitir</button>

// componente-padre.html
<app-componente-hijo
  (eventoClick)="manejarEvento($event)">
</app-componente-hijo>
```

### Buenas Prácticas

- Usar interfaces para tipar los datos
- Documentar los @Input() y @Output()
- Evitar modificar directamente los @Input()

---

## Directivas Estructurales

Angular 19.2.5 introduce nuevas directivas estructurales con una sintaxis más moderna y eficiente.

### @if

```typescript
// template.html
@if (condicion) {
  <p>La condición es verdadera</p>
} @else if (otraCondicion) {
  <p>La otra condición es verdadera</p>
} @else {
  <p>Ninguna condición es verdadera</p>
}
```

### @for

```typescript
// template.html
@for (item of items; track item.id) {
  <div>{{ item.nombre }}</div>
} @empty {
  <div>No hay items para mostrar</div>
}

// Accediendo a índices
@for (item of items; track item.id; let i = $index) {
  <div>Item #{{ i + 1 }}: {{ item.nombre }}</div>
}
```

### @switch

```typescript
// template.html
@switch (valor) {
  @case ('A') {
    <p>Caso A</p>
  }
  @case ('B') {
    <p>Caso B</p>
  }
  @default {
    <p>Caso por defecto</p>
  }
}
```

### Buenas Prácticas

- Usar `track` en @for para optimizar el rendimiento
- Preferir @if sobre [hidden] para ocultar elementos
- Implementar casos @empty en listas

---

## Integración con NG Bootstrap

NG Bootstrap proporciona componentes de Bootstrap nativos para Angular 19.2.5.

### Instalación

```bash
ng add @ng-bootstrap/ng-bootstrap
```

### Configuración en app.module.ts

```typescript
import { NgbModule } from '@ng-bootstrap/ng-bootstrap';

@NgModule({
  imports: [
    NgbModule
  ]
})
export class AppModule { }
```

### Ejemplos de Uso

1. **Modal**
```typescript
// componente.ts
export class MiComponente {
  constructor(private modalService: NgbModal) {}

  abrirModal() {
    const modal = this.modalService.open(ModalComponent);
    modal.result.then(
      (result) => console.log('Modal cerrado'),
      (reason) => console.log('Modal descartado')
    );
  }
}
```

2. **Alertas**
```typescript
// template.html
<ngb-alert [dismissible]="true" type="success">
  ¡Operación exitosa!
</ngb-alert>
```

3. **Carrusel**
```typescript
// template.html
<ngb-carousel>
  <ng-template ngbSlide>
    <div class="picsum-img-wrapper">
      <img src="..." alt="...">
    </div>
    <div class="carousel-caption">
      <h3>Slide 1</h3>
      <p>Descripción del slide 1</p>
    </div>
  </ng-template>
</ngb-carousel>
```

### Buenas Prácticas

- Importar solo los módulos necesarios
- Usar interfaces para tipos de datos
- Implementar manejo de errores en modales

---

## Ejemplos paso a paso

### Estructura de Carpetas Recomendada

```
clase02/
├── class/
│   ├── usuario.class.ts
│   └── producto.class.ts
├── components/
│   ├── header/
│   │   ├── header.component.ts
│   │   ├── header.component.html
│   │   └── header.component.scss
│   └── footer/
│       ├── footer.component.ts
│       ├── footer.component.html
│       └── footer.component.scss
├── interfaces/
│   ├── usuario.interface.ts
│   └── producto.interface.ts
└── pages/
    ├── home/
    │   ├── home.component.ts
    │   ├── home.component.html
    │   └── home.component.scss
    └── about/
        ├── about.component.ts
        ├── about.component.html
        └── about.component.scss
```

### Ejemplos de Implementación

1. **Clase Usuario**
```typescript
// class/usuario.class.ts
export class Usuario {
  constructor(
    public id: number,
    public nombre: string,
    public email: string
  ) {}

  obtenerNombreCompleto(): string {
    return `${this.nombre} (${this.email})`;
  }
}
```

2. **Interfaz de Usuario**
```typescript
// interfaces/usuario.interface.ts
export interface IUsuario {
  id: number;
  nombre: string;
  email: string;
  rol?: 'admin' | 'usuario';
}
```

3. **Componente Header**
```typescript
// components/header/header.component.ts
import { Component, Input, Output, EventEmitter } from '@angular/core';
import { IUsuario } from '../../interfaces/usuario.interface';

@Component({
  selector: 'app-header',
  templateUrl: './header.component.html',
  styleUrls: ['./header.component.scss']
})
export class HeaderComponent {
  @Input() usuario: IUsuario;
  @Output() cerrarSesion = new EventEmitter<void>();

  onLogout(): void {
    this.cerrarSesion.emit();
  }
}
```

4. **Página Home**
```typescript
// pages/home/home.component.ts
import { Component } from '@angular/core';
import { Usuario } from '../../class/usuario.class';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.scss']
})
export class HomeComponent {
  usuarios: Usuario[] = [
    new Usuario(1, 'Juan', 'juan@email.com'),
    new Usuario(2, 'Ana', 'ana@email.com')
  ];

  @if (usuarios.length > 0) {
    <ul>
      @for (usuario of usuarios; track usuario.id) {
        <li>{{ usuario.obtenerNombreCompleto() }}</li>
      }
    </ul>
  }
}
```

### Comandos Útiles

1. **Crear componentes:**
```bash
ng generate component components/header
ng generate component components/footer
ng generate component pages/home
ng generate component pages/about
```

2. **Crear clases e interfaces:**
```bash
ng generate class class/usuario
ng generate interface interfaces/usuario
```

3. **Ejecutar la aplicación:**
```bash
ng serve --open
```

---

## Development server

To start a local development server, run:

```bash
ng serve
```

Once the server is running, open your browser and navigate to `http://localhost:4200/`. The application will automatically reload whenever you modify any of the source files.

## Code scaffolding

Angular CLI includes powerful code scaffolding tools. To generate a new component, run:

```bash
ng generate component component-name
```

For a complete list of available schematics (such as `components`, `directives`, or `pipes`), run:

```bash
ng generate --help
```

## Building

To build the project run:

```bash
ng build
```

This will compile your project and store the build artifacts in the `dist/` directory. By default, the production build optimizes your application for performance and speed.

## Running unit tests

To execute unit tests with the [Karma](https://karma-runner.github.io) test runner, use the following command:

```bash
ng test
```

## Running end-to-end tests

For end-to-end (e2e) testing, run:

```bash
ng e2e
```

Angular CLI does not come with an end-to-end testing framework by default. You can choose one that suits your needs.

## Additional Resources

For more information on using the Angular CLI, including detailed command references, visit the [Angular CLI Overview and Command Reference](https://angular.dev/tools/cli) page.
