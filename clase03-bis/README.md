# Guía Completa Angular 19.2.5 - Clase03-bis

## Requisitos Previos

Antes de comenzar, asegúrate de tener instalado:

1. **Node.js** (versión 18.x o superior)
2. **npm** (versión 10.x o superior)
3. **Angular CLI** (versión 19.2.5)
4. **Supabase CLI** (opcional)

```bash
npm install -g @angular/cli@19.2.5
npm install @supabase/supabase-js
```

## Comunicación entre Componentes Avanzada

### Patrones de Comunicación

1. **Padre a Hijo (@Input con Objetos)**
```typescript
// interfaces/tarea.interface.ts
export interface Tarea {
  id: number;
  titulo: string;
  completada: boolean;
  prioridad: 'alta' | 'media' | 'baja';
}

// components/tarea/tarea.component.ts
@Component({
  selector: 'app-tarea',
  template: `
    <div [class]="'tarea-card ' + tarea.prioridad">
      <h3>{{ tarea.titulo }}</h3>
      <div class="controles">
        <button (click)="onCompletar()">{{ tarea.completada ? 'Reabrir' : 'Completar' }}</button>
        <button (click)="onEliminar()">Eliminar</button>
      </div>
    </div>
  `
})
export class TareaComponent {
  @Input() tarea!: Tarea;
  @Output() completar = new EventEmitter<number>();
  @Output() eliminar = new EventEmitter<number>();

  onCompletar() {
    this.completar.emit(this.tarea.id);
  }

  onEliminar() {
    this.eliminar.emit(this.tarea.id);
  }
}
```

2. **Hijo a Padre (Eventos Personalizados)**
```typescript
// components/lista-tareas/lista-tareas.component.ts
@Component({
  selector: 'app-lista-tareas',
  template: `
    <div class="lista-tareas">
      @for (tarea of tareas; track tarea.id) {
        <app-tarea
          [tarea]="tarea"
          (completar)="completarTarea($event)"
          (eliminar)="eliminarTarea($event)">
        </app-tarea>
      }
    </div>
  `
})
export class ListaTareasComponent {
  tareas: Tarea[] = [
    { id: 1, titulo: 'Aprender Input/Output', completada: false, prioridad: 'alta' },
    { id: 2, titulo: 'Practicar Supabase', completada: false, prioridad: 'media' }
  ];

  completarTarea(id: number) {
    this.tareas = this.tareas.map(t =>
      t.id === id ? { ...t, completada: !t.completada } : t
    );
  }

  eliminarTarea(id: number) {
    this.tareas = this.tareas.filter(t => t.id !== id);
  }
}
```

## Introducción a Supabase

### Configuración Inicial

1. **Instalar Dependencias**
```bash
npm install @supabase/supabase-js
```

2. **Configurar Cliente**
```typescript
// services/supabase.service.ts
import { Injectable } from '@angular/core';
import { createClient, SupabaseClient } from '@supabase/supabase-js';
import { environment } from '../environments/environment';

@Injectable({
  providedIn: 'root'
})
export class SupabaseService {
  private supabase: SupabaseClient;

  constructor() {
    this.supabase = createClient(
      environment.supabaseUrl,
      environment.supabaseKey
    );
  }

  async getTareas() {
    const { data, error } = await this.supabase
      .from('tareas')
      .select('*')
      .order('created_at', { ascending: false });

    if (error) throw error;
    return data;
  }

  async crearTarea(tarea: Omit<Tarea, 'id'>) {
    const { data, error } = await this.supabase
      .from('tareas')
      .insert([tarea])
      .select();

    if (error) throw error;
    return data[0];
  }

  async actualizarTarea(id: number, cambios: Partial<Tarea>) {
    const { data, error } = await this.supabase
      .from('tareas')
      .update(cambios)
      .eq('id', id)
      .select();

    if (error) throw error;
    return data[0];
  }
}
```

### Integración con Componentes

```typescript
// pages/tareas/tareas.component.ts
@Component({
  selector: 'app-tareas',
  template: `
    <div class="container">
      <app-crear-tarea (nuevaTarea)="crearTarea($event)"></app-crear-tarea>
      
      @if (tareas$ | async; as tareas) {
        <app-lista-tareas
          [tareas]="tareas"
          (completar)="completarTarea($event)"
          (eliminar)="eliminarTarea($event)">
        </app-lista-tareas>
      } @else {
        <p>Cargando tareas...</p>
      }
    </div>
  `
})
export class TareasComponent implements OnInit {
  tareas$ = new BehaviorSubject<Tarea[]>([]);

  constructor(private supabaseService: SupabaseService) {}

  async ngOnInit() {
    try {
      const tareas = await this.supabaseService.getTareas();
      this.tareas$.next(tareas);
    } catch (error) {
      console.error('Error al cargar tareas:', error);
    }
  }

  async crearTarea(tarea: Omit<Tarea, 'id'>) {
    try {
      const nuevaTarea = await this.supabaseService.crearTarea(tarea);
      this.tareas$.next([nuevaTarea, ...this.tareas$.value]);
    } catch (error) {
      console.error('Error al crear tarea:', error);
    }
  }

  async completarTarea(id: number) {
    try {
      const tarea = this.tareas$.value.find(t => t.id === id);
      if (tarea) {
        const tareaActualizada = await this.supabaseService
          .actualizarTarea(id, { completada: !tarea.completada });
        
        this.tareas$.next(
          this.tareas$.value.map(t =>
            t.id === id ? tareaActualizada : t
          )
        );
      }
    } catch (error) {
      console.error('Error al actualizar tarea:', error);
    }
  }
}
```

### Buenas Prácticas

1. **Manejo de Estado**
```typescript
// Usar BehaviorSubject para estado local
private _tareas = new BehaviorSubject<Tarea[]>([]);
tareas$ = this._tareas.asObservable();

// Actualizar estado de forma inmutable
actualizarTarea(tarea: Tarea) {
  const tareas = this._tareas.value;
  const index = tareas.findIndex(t => t.id === tarea.id);
  if (index !== -1) {
    const nuevasTareas = [
      ...tareas.slice(0, index),
      tarea,
      ...tareas.slice(index + 1)
    ];
    this._tareas.next(nuevasTareas);
  }
}
```

2. **Manejo de Errores**
```typescript
// services/error.service.ts
@Injectable({
  providedIn: 'root'
})
export class ErrorService {
  private errores = new Subject<string>();
  errores$ = this.errores.asObservable();

  mostrarError(mensaje: string) {
    this.errores.next(mensaje);
  }
}

// Uso en componentes
try {
  await this.supabaseService.crearTarea(tarea);
} catch (error) {
  this.errorService.mostrarError('No se pudo crear la tarea');
}
```

3. **Tipado Estricto**
```typescript
// types/supabase.ts
export type TablasTareas = {
  tareas: {
    Row: Tarea;
    Insert: Omit<Tarea, 'id' | 'created_at'>;
    Update: Partial<Omit<Tarea, 'id' | 'created_at'>>;
  };
};

// Uso en el servicio
supabase: SupabaseClient<TablasTareas>;
```

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
