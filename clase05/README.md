# Supabase Avanzado con Angular 19.2.5 - Clase05

## Requisitos Previos

Antes de comenzar, asegúrate de tener:

1. **Node.js** (versión 18.x o superior)
2. **npm** (versión 10.x o superior)
3. **Angular CLI** (versión 19.2.5)
4. **Cuenta en Supabase** con un proyecto creado
5. **Proyecto de la clase04** configurado

```bash
# Instalar dependencias adicionales
npm install @supabase/supabase-js rxjs@7.8.1
```

## Suscripciones en Tiempo Real

### Configuración Inicial

1. **Habilitar Realtime en Supabase**
   - Ir al Dashboard de Supabase
   - Navegar a Database > Replication
   - Habilitar las tablas que queremos observar

2. **Configurar Servicio de Tiempo Real**
```typescript
// services/realtime.service.ts
@Injectable({
  providedIn: 'root'
})
export class RealtimeService {
  private subscription: RealtimeChannel;

  constructor(private supabase: SupabaseService) {}

  suscribirseATabla<T>(
    tabla: string,
    callback: (cambio: RealtimePostgresChangesPayload<T>) => void
  ) {
    this.subscription = this.supabase.client
      .channel('cambios_db')
      .on(
        'postgres_changes',
        {
          event: '*',  // INSERT, UPDATE, DELETE
          schema: 'public',
          table: tabla
        },
        (payload) => callback(payload)
      )
      .subscribe();
  }

  desuscribirse() {
    if (this.subscription) {
      this.supabase.client.removeChannel(this.subscription);
    }
  }
}
```

### Implementación en Componentes

```typescript
// pages/chat-realtime/chat-realtime.component.ts
@Component({
  selector: 'app-chat-realtime',
  template: `
    <div class="chat-container">
      @for (mensaje of mensajes; track mensaje.id) {
        <div class="mensaje">
          <strong>{{ mensaje.autor }}:</strong>
          {{ mensaje.contenido }}
          <small>{{ mensaje.created_at | date:'short' }}</small>
        </div>
      }

      <form [formGroup]="mensajeForm" (ngSubmit)="enviarMensaje()">
        <input formControlName="contenido" placeholder="Escribe un mensaje...">
        <button type="submit">Enviar</button>
      </form>
    </div>
  `
})
export class ChatRealtimeComponent implements OnInit, OnDestroy {
  mensajes: Mensaje[] = [];
  mensajeForm = new FormGroup({
    contenido: new FormControl('', Validators.required)
  });

  constructor(
    private realtimeService: RealtimeService,
    private databaseService: DatabaseService
  ) {}

  ngOnInit() {
    this.cargarMensajes();
    this.suscribirseACambios();
  }

  ngOnDestroy() {
    this.realtimeService.desuscribirse();
  }

  private async cargarMensajes() {
    try {
      this.mensajes = await this.databaseService.obtenerMensajes();
    } catch (error) {
      console.error('Error al cargar mensajes:', error);
    }
  }

  private suscribirseACambios() {
    this.realtimeService.suscribirseATabla<Mensaje>('mensajes', (cambio) => {
      if (cambio.eventType === 'INSERT') {
        this.mensajes = [...this.mensajes, cambio.new];
      } else if (cambio.eventType === 'DELETE') {
        this.mensajes = this.mensajes.filter(m => m.id !== cambio.old.id);
      }
    });
  }

  async enviarMensaje() {
    if (this.mensajeForm.valid) {
      try {
        await this.databaseService.crearMensaje({
          contenido: this.mensajeForm.value.contenido,
          autor_id: this.supabase.auth.user()?.id
        });
        this.mensajeForm.reset();
      } catch (error) {
        console.error('Error al enviar mensaje:', error);
      }
    }
  }
}
```

## Autenticación y Autorización

### Configuración de Autenticación

1. **Servicio de Autenticación**
```typescript
// services/auth.service.ts
@Injectable({
  providedIn: 'root'
})
export class AuthService {
  private userSubject = new BehaviorSubject<User | null>(null);
  user$ = this.userSubject.asObservable();

  constructor(private supabase: SupabaseService) {
    this.supabase.client.auth.onAuthStateChange((event, session) => {
      this.userSubject.next(session?.user ?? null);
    });
  }

  async registrar(email: string, password: string) {
    const { data, error } = await this.supabase.client.auth.signUp({
      email,
      password
    });
    if (error) throw error;
    return data;
  }

  async iniciarSesion(email: string, password: string) {
    const { data, error } = await this.supabase.client.auth.signInWithPassword({
      email,
      password
    });
    if (error) throw error;
    return data;
  }

  async cerrarSesion() {
    const { error } = await this.supabase.client.auth.signOut();
    if (error) throw error;
  }

  obtenerUsuarioActual() {
    return this.supabase.client.auth.getUser();
  }
}
```

2. **Guard de Autenticación**
```typescript
// guards/auth.guard.ts
@Injectable({
  providedIn: 'root'
})
export class AuthGuard {
  constructor(private authService: AuthService, private router: Router) {}

  canActivate(): Observable<boolean> {
    return this.authService.user$.pipe(
      map(user => {
        if (user) return true;
        this.router.navigate(['/login']);
        return false;
      })
    );
  }
}
```

### Implementación de Login

```typescript
// pages/login/login.component.ts
@Component({
  selector: 'app-login',
  template: `
    <div class="login-container">
      <form [formGroup]="loginForm" (ngSubmit)="onSubmit()">
        <h2>Iniciar Sesión</h2>
        
        <div class="form-group">
          <label for="email">Email</label>
          <input 
            id="email"
            type="email"
            formControlName="email"
            placeholder="tu@email.com">
        </div>

        <div class="form-group">
          <label for="password">Contraseña</label>
          <input 
            id="password"
            type="password"
            formControlName="password">
        </div>

        @if (error) {
          <div class="error">{{ error }}</div>
        }

        <button type="submit" [disabled]="!loginForm.valid || loading">
          {{ loading ? 'Cargando...' : 'Ingresar' }}
        </button>
      </form>
    </div>
  `,
  styles: [`
    .login-container {
      max-width: 400px;
      margin: 2rem auto;
      padding: 2rem;
      border-radius: 8px;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }

    .form-group {
      margin-bottom: 1rem;
    }

    .error {
      color: red;
      margin: 1rem 0;
    }
  `]
})
export class LoginComponent {
  loginForm = new FormGroup({
    email: new FormControl('', [Validators.required, Validators.email]),
    password: new FormControl('', [Validators.required])
  });

  loading = false;
  error: string | null = null;

  constructor(
    private authService: AuthService,
    private router: Router
  ) {}

  async onSubmit() {
    if (this.loginForm.valid) {
      this.loading = true;
      this.error = null;

      try {
        await this.authService.iniciarSesion(
          this.loginForm.value.email!,
          this.loginForm.value.password!
        );
        this.router.navigate(['/chat']);
      } catch (error: any) {
        this.error = error.message;
      } finally {
        this.loading = false;
      }
    }
  }
}
```

## Relaciones y Consultas Avanzadas

### Relaciones entre Tablas

1. **Definición de Relaciones**
```sql
-- Tabla de Categorías
CREATE TABLE categorias (
  id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  nombre VARCHAR NOT NULL,
  descripcion TEXT
);

-- Tabla de Productos con Relación
CREATE TABLE productos (
  id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  nombre VARCHAR NOT NULL,
  precio DECIMAL(10,2) NOT NULL,
  categoria_id UUID REFERENCES categorias(id) ON DELETE CASCADE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc', NOW())
);

-- Tabla de Pedidos
CREATE TABLE pedidos (
  id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  usuario_id UUID REFERENCES auth.users(id),
  total DECIMAL(10,2) NOT NULL,
  estado VARCHAR NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc', NOW())
);

-- Tabla de Detalles de Pedido (Relación Muchos a Muchos)
CREATE TABLE detalles_pedido (
  id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  pedido_id UUID REFERENCES pedidos(id) ON DELETE CASCADE,
  producto_id UUID REFERENCES productos(id) ON DELETE CASCADE,
  cantidad INTEGER NOT NULL,
  precio_unitario DECIMAL(10,2) NOT NULL
);
```

2. **Tipos TypeScript para Relaciones**
```typescript
// types/database.types.ts
export interface Database {
  public: {
    Tables: {
      categorias: {
        Row: {
          id: string;
          nombre: string;
          descripcion: string | null;
        };
        Insert: Omit<Tables['categorias']['Row'], 'id'>;
        Update: Partial<Tables['categorias']['Insert']>;
      };
      productos: {
        Row: {
          id: string;
          nombre: string;
          precio: number;
          categoria_id: string;
          created_at: string;
        };
        Insert: Omit<Tables['productos']['Row'], 'id' | 'created_at'>;
        Update: Partial<Tables['productos']['Insert']>;
      };
      pedidos: {
        Row: {
          id: string;
          usuario_id: string;
          total: number;
          estado: string;
          created_at: string;
        };
        Insert: Omit<Tables['pedidos']['Row'], 'id' | 'created_at'>;
        Update: Partial<Tables['pedidos']['Insert']>;
      };
      detalles_pedido: {
        Row: {
          id: string;
          pedido_id: string;
          producto_id: string;
          cantidad: number;
          precio_unitario: number;
        };
        Insert: Omit<Tables['detalles_pedido']['Row'], 'id'>;
        Update: Partial<Tables['detalles_pedido']['Insert']>;
      };
    };
  };
}
```

### Consultas Avanzadas

1. **Servicio de Productos con Relaciones**
```typescript
// services/productos.service.ts
@Injectable({
  providedIn: 'root'
})
export class ProductosService {
  constructor(private supabase: SupabaseService) {}

  // Obtener productos con categoría
  async obtenerProductosConCategoria() {
    const { data, error } = await this.supabase.client
      .from('productos')
      .select(`
        *,
        categoria:categorias(*)
      `)
      .order('created_at', { ascending: false });

    if (error) throw error;
    return data;
  }

  // Obtener pedidos con detalles y productos
  async obtenerPedidoCompleto(pedidoId: string) {
    const { data, error } = await this.supabase.client
      .from('pedidos')
      .select(`
        *,
        detalles:detalles_pedido(*, producto:productos(*))
      `)
      .eq('id', pedidoId)
      .single();

    if (error) throw error;
    return data;
  }

  // Búsqueda con filtros múltiples
  async buscarProductos({
    categoria,
    precioMin,
    precioMax,
    busqueda
  }: {
    categoria?: string;
    precioMin?: number;
    precioMax?: number;
    busqueda?: string;
  }) {
    let query = this.supabase.client
      .from('productos')
      .select(`
        *,
        categoria:categorias(*)
      `);

    if (categoria) {
      query = query.eq('categoria_id', categoria);
    }

    if (precioMin) {
      query = query.gte('precio', precioMin);
    }

    if (precioMax) {
      query = query.lte('precio', precioMax);
    }

    if (busqueda) {
      query = query.ilike('nombre', `%${busqueda}%`);
    }

    const { data, error } = await query;
    if (error) throw error;
    return data;
  }

  // Estadísticas y agregaciones
  async obtenerEstadisticas() {
    const { data, error } = await this.supabase.client
      .from('detalles_pedido')
      .select(`
        producto_id,
        productos(nombre),
        total_vendido:sum(cantidad),
        ingresos:sum(cantidad * precio_unitario)
      `)
      .group('producto_id, productos(nombre)')
      .order('total_vendido', { ascending: false });

    if (error) throw error;
    return data;
  }
}
```

2. **Componente de Estadísticas**
```typescript
// pages/estadisticas/estadisticas.component.ts
@Component({
  selector: 'app-estadisticas',
  template: `
    <div class="estadisticas-container">
      <h2>Estadísticas de Ventas</h2>

      @if (estadisticas$ | async; as stats) {
        <div class="stats-grid">
          @for (stat of stats; track stat.producto_id) {
            <div class="stat-card">
              <h3>{{ stat.productos?.nombre }}</h3>
              <div class="stat-info">
                <p>
                  <strong>Unidades vendidas:</strong>
                  {{ stat.total_vendido }}
                </p>
                <p>
                  <strong>Ingresos totales:</strong>
                  {{ stat.ingresos | currency }}
                </p>
              </div>
            </div>
          }
        </div>

        <div class="graficos">
          <canvas #ventasChart></canvas>
        </div>
      } @else {
        <p>Cargando estadísticas...</p>
      }
    </div>
  `,
  styles: [`
    .estadisticas-container {
      padding: 2rem;
    }

    .stats-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
      gap: 1rem;
      margin-bottom: 2rem;
    }

    .stat-card {
      padding: 1rem;
      border-radius: 8px;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
      background: white;
    }

    .graficos {
      max-width: 800px;
      margin: 0 auto;
    }
  `]
})
export class EstadisticasComponent implements OnInit, AfterViewInit {
  @ViewChild('ventasChart') ventasChart!: ElementRef;
  estadisticas$ = new BehaviorSubject<any[]>([]);
  chart: Chart | null = null;

  constructor(private productosService: ProductosService) {}

  async ngOnInit() {
    try {
      const stats = await this.productosService.obtenerEstadisticas();
      this.estadisticas$.next(stats);
    } catch (error) {
      console.error('Error al cargar estadísticas:', error);
    }
  }

  ngAfterViewInit() {
    this.estadisticas$.pipe(
      filter(stats => stats.length > 0)
    ).subscribe(stats => {
      this.crearGrafico(stats);
    });
  }

  private crearGrafico(stats: any[]) {
    const ctx = this.ventasChart.nativeElement.getContext('2d');
    
    if (this.chart) {
      this.chart.destroy();
    }

    this.chart = new Chart(ctx, {
      type: 'bar',
      data: {
        labels: stats.map(s => s.productos.nombre),
        datasets: [
          {
            label: 'Unidades Vendidas',
            data: stats.map(s => s.total_vendido),
            backgroundColor: 'rgba(54, 162, 235, 0.5)'
          },
          {
            label: 'Ingresos ($)',
            data: stats.map(s => s.ingresos),
            backgroundColor: 'rgba(75, 192, 192, 0.5)'
          }
        ]
      },
      options: {
        responsive: true,
        scales: {
          y: {
            beginAtZero: true
          }
        }
      }
    });
  }
}
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
