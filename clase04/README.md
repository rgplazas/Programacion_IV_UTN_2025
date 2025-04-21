# Guía Completa Angular 19.2.5 con Supabase - Clase04

## Requisitos Previos

Antes de comenzar, asegúrate de tener instalado:

1. **Node.js** (versión 18.x o superior)
2. **npm** (versión 10.x o superior)
3. **Angular CLI** (versión 19.2.5)
4. **Cuenta en Supabase** (gratuita)

```bash
# Instalar Angular CLI
npm install -g @angular/cli@19.2.5

# Instalar dependencias de Supabase
npm install @supabase/supabase-js
```

## Finalización Input/Output

### Patrones Avanzados de Comunicación

1. **Comunicación a través de Servicios**
```typescript
// classes/mensaje.class.ts
export class Mensaje {
  constructor(
    public id: number,
    public contenido: string,
    public autor: string,
    public timestamp: Date = new Date()
  ) {}

  get formatoHora(): string {
    return this.timestamp.toLocaleTimeString();
  }
}

// services/mensajes.service.ts
@Injectable({
  providedIn: 'root'
})
export class MensajesService {
  private mensajes = new BehaviorSubject<Mensaje[]>([]);
  mensajes$ = this.mensajes.asObservable();

  agregarMensaje(mensaje: Mensaje) {
    const mensajesActuales = this.mensajes.value;
    this.mensajes.next([...mensajesActuales, mensaje]);
  }
}

// components/chat/chat.component.ts
@Component({
  selector: 'app-chat',
  template: `
    <div class="chat-container">
      <app-lista-mensajes
        [mensajes]="mensajes$ | async"
        (eliminarMensaje)="eliminarMensaje($event)">
      </app-lista-mensajes>
      
      <app-nuevo-mensaje
        (nuevoMensaje)="agregarMensaje($event)">
      </app-nuevo-mensaje>
    </div>
  `
})
export class ChatComponent {
  mensajes$ = this.mensajesService.mensajes$;

  constructor(private mensajesService: MensajesService) {}

  agregarMensaje(contenido: string) {
    const mensaje = new Mensaje(
      Date.now(),
      contenido,
      'Usuario'
    );
    this.mensajesService.agregarMensaje(mensaje);
  }
}
```

2. **Comunicación entre Componentes No Relacionados**
```typescript
// services/eventos.service.ts
@Injectable({
  providedIn: 'root'
})
export class EventosService {
  private notificaciones = new Subject<string>();
  notificaciones$ = this.notificaciones.asObservable();

  enviarNotificacion(mensaje: string) {
    this.notificaciones.next(mensaje);
  }
}

// components/header/header.component.ts
@Component({
  selector: 'app-header',
  template: `
    <header>
      <h1>{{ titulo }}</h1>
      @if (notificacion) {
        <div class="notificacion">{{ notificacion }}</div>
      }
    </header>
  `
})
export class HeaderComponent implements OnInit, OnDestroy {
  @Input() titulo: string = 'Mi App';
  notificacion: string = '';
  private subscription: Subscription;

  constructor(private eventosService: EventosService) {
    this.subscription = this.eventosService.notificaciones$
      .pipe(
        tap(msg => this.notificacion = msg),
        delay(3000),
        tap(() => this.notificacion = '')
      )
      .subscribe();
  }

  ngOnDestroy() {
    this.subscription.unsubscribe();
  }
}
```

3. **Comunicación Bidireccional con ngModel**
```typescript
// components/editor/editor.component.ts
@Component({
  selector: 'app-editor',
  template: `
    <div class="editor">
      <input 
        [(ngModel)]="texto"
        (ngModelChange)="onTextoChange($event)">
      
      <div class="preview">
        {{ texto }}
      </div>
    </div>
  `
})
export class EditorComponent {
  @Input() texto: string = '';
  @Output() textoChange = new EventEmitter<string>();

  onTextoChange(nuevoTexto: string) {
    this.textoChange.emit(nuevoTexto);
  }
}
```

## Introducción a Supabase

Supabase es una alternativa de código abierto a Firebase que proporciona una base de datos PostgreSQL con funcionalidades avanzadas.

### Configuración Inicial

1. **Crear Proyecto en Supabase**
   - Visitar [supabase.com](https://supabase.com)
   - Registrarse y crear un nuevo proyecto
   - Guardar la URL y la API Key del proyecto

2. **Configurar Variables de Entorno**
```typescript
// environments/environment.ts
export const environment = {
  production: false,
  supabaseUrl: 'TU_URL_DE_SUPABASE',
  supabaseKey: 'TU_API_KEY_DE_SUPABASE'
};
```

3. **Crear Servicio de Supabase**
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

  get client() {
    return this.supabase;
  }
}
```

## Configuración de Base de Datos

### Crear Tablas

1. **Desde el Dashboard de Supabase**
```sql
-- Tabla de Usuarios
CREATE TABLE usuarios (
  id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  nombre VARCHAR NOT NULL,
  email VARCHAR UNIQUE NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc', NOW())
);

-- Tabla de Mensajes
CREATE TABLE mensajes (
  id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  contenido TEXT NOT NULL,
  usuario_id UUID REFERENCES usuarios(id) ON DELETE CASCADE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc', NOW())
);
```

2. **Configurar Políticas de Seguridad (RLS)**
```sql
-- Permitir lectura pública de usuarios
CREATE POLICY "Usuarios visibles para todos" ON usuarios
  FOR SELECT USING (true);

-- Permitir inserción de mensajes solo a usuarios autenticados
CREATE POLICY "Insertar mensajes como usuario autenticado" ON mensajes
  FOR INSERT WITH CHECK (auth.uid() = usuario_id);
```

### Tipos TypeScript

```typescript
// types/database.types.ts
export interface Database {
  public: {
    Tables: {
      usuarios: {
        Row: {
          id: string;
          nombre: string;
          email: string;
          created_at: string;
        };
        Insert: Omit<Tables['usuarios']['Row'], 'id' | 'created_at'>;
        Update: Partial<Tables['usuarios']['Insert']>;
      };
      mensajes: {
        Row: {
          id: string;
          contenido: string;
          usuario_id: string;
          created_at: string;
        };
        Insert: Omit<Tables['mensajes']['Row'], 'id' | 'created_at'>;
        Update: Partial<Tables['mensajes']['Insert']>;
      };
    };
  };
}
```

## Operaciones CRUD Básicas

### Implementación del Servicio

```typescript
// services/database.service.ts
@Injectable({
  providedIn: 'root'
})
export class DatabaseService {
  constructor(private supabase: SupabaseService) {}

  // CREATE
  async crearUsuario(usuario: Database['public']['Tables']['usuarios']['Insert']) {
    const { data, error } = await this.supabase.client
      .from('usuarios')
      .insert(usuario)
      .select()
      .single();

    if (error) throw error;
    return data;
  }

  // READ
  async obtenerUsuarios() {
    const { data, error } = await this.supabase.client
      .from('usuarios')
      .select('*')
      .order('created_at', { ascending: false });

    if (error) throw error;
    return data;
  }

  // UPDATE
  async actualizarUsuario(
    id: string,
    cambios: Database['public']['Tables']['usuarios']['Update']
  ) {
    const { data, error } = await this.supabase.client
      .from('usuarios')
      .update(cambios)
      .eq('id', id)
      .select()
      .single();

    if (error) throw error;
    return data;
  }

  // DELETE
  async eliminarUsuario(id: string) {
    const { error } = await this.supabase.client
      .from('usuarios')
      .delete()
      .eq('id', id);

    if (error) throw error;
  }
}
```

## Ejemplos paso a paso

### Estructura de Carpetas

```
clase04/
├── classes/
│   ├── mensaje.class.ts
│   └── usuario.class.ts
├── pages/
│   ├── chat/
│   │   ├── chat.component.ts
│   │   ├── chat.component.html
│   │   └── chat.component.scss
│   └── usuarios/
│       ├── usuarios.component.ts
│       ├── usuarios.component.html
│       └── usuarios.component.scss
└── services/
    ├── supabase.service.ts
    ├── database.service.ts
    └── eventos.service.ts
```

### Implementación Completa

1. **Crear Clases Base**
```typescript
// classes/usuario.class.ts
export class Usuario {
  constructor(
    public id: string,
    public nombre: string,
    public email: string,
    public created_at: string = new Date().toISOString()
  ) {}

  static fromJson(json: any): Usuario {
    return new Usuario(
      json.id,
      json.nombre,
      json.email,
      json.created_at
    );
  }

  toJson(): any {
    return {
      id: this.id,
      nombre: this.nombre,
      email: this.email,
      created_at: this.created_at
    };
  }
}
```

2. **Implementar Páginas**
```typescript
// pages/usuarios/usuarios.component.ts
@Component({
  selector: 'app-usuarios',
  template: `
    <div class="usuarios-container">
      <form [formGroup]="usuarioForm" (ngSubmit)="onSubmit()">
        <input formControlName="nombre" placeholder="Nombre">
        <input formControlName="email" placeholder="Email">
        <button type="submit" [disabled]="!usuarioForm.valid">
          Agregar Usuario
        </button>
      </form>

      @if (usuarios$ | async; as usuarios) {
        <div class="usuarios-lista">
          @for (usuario of usuarios; track usuario.id) {
            <div class="usuario-card">
              <h3>{{ usuario.nombre }}</h3>
              <p>{{ usuario.email }}</p>
              <button (click)="eliminarUsuario(usuario.id)">
                Eliminar
              </button>
            </div>
          }
        </div>
      } @else {
        <p>Cargando usuarios...</p>
      }
    </div>
  `
})
export class UsuariosComponent implements OnInit {
  usuarioForm = new FormGroup({
    nombre: new FormControl('', [Validators.required]),
    email: new FormControl('', [Validators.required, Validators.email])
  });

  usuarios$ = new BehaviorSubject<Usuario[]>([]);

  constructor(
    private databaseService: DatabaseService,
    private eventosService: EventosService
  ) {}

  async ngOnInit() {
    try {
      const usuarios = await this.databaseService.obtenerUsuarios();
      this.usuarios$.next(usuarios.map(u => Usuario.fromJson(u)));
    } catch (error) {
      this.eventosService.enviarNotificacion('Error al cargar usuarios');
    }
  }

  async onSubmit() {
    if (this.usuarioForm.valid) {
      try {
        const nuevoUsuario = await this.databaseService.crearUsuario(
          this.usuarioForm.value
        );
        
        this.usuarios$.next([
          Usuario.fromJson(nuevoUsuario),
          ...this.usuarios$.value
        ]);
        
        this.usuarioForm.reset();
        this.eventosService.enviarNotificacion('Usuario creado exitosamente');
      } catch (error) {
        this.eventosService.enviarNotificacion('Error al crear usuario');
      }
    }
  }

  async eliminarUsuario(id: string) {
    try {
      await this.databaseService.eliminarUsuario(id);
      this.usuarios$.next(
        this.usuarios$.value.filter(u => u.id !== id)
      );
      this.eventosService.enviarNotificacion('Usuario eliminado exitosamente');
    } catch (error) {
      this.eventosService.enviarNotificacion('Error al eliminar usuario');
    }
  }
}
```

### Buenas Prácticas

1. **Manejo de Estado**
   - Usar BehaviorSubject para estado local
   - Mantener la inmutabilidad de los datos
   - Implementar patrones de carga y error

2. **Tipado y Validación**
   - Definir interfaces y tipos para todos los datos
   - Usar clases para lógica de negocio
   - Validar formularios antes de enviar

3. **Gestión de Errores**
   - Implementar manejo centralizado de errores
   - Mostrar mensajes de error amigables
   - Registrar errores para debugging

4. **Seguridad**
   - Configurar RLS en Supabase
   - No exponer claves en el código
   - Validar datos en el servidor

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
