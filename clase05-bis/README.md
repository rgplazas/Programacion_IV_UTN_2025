# Patrones Avanzados con Supabase y Angular 19.2.5 - Clase05-bis

## Requisitos Previos

Antes de comenzar, asegúrate de tener:

1. **Node.js** (versión 18.x o superior)
2. **npm** (versión 10.x o superior)
3. **Angular CLI** (versión 19.2.5)
4. **Cuenta en Supabase** con un proyecto creado
5. **Proyecto de la clase05** configurado

```bash
# Instalar dependencias adicionales
npm install @supabase/supabase-js rxjs@7.8.1 chart.js @types/chart.js
```

## Patrones Avanzados de Suscripción

### Estado Global con Store Pattern

1. **Implementación del Store**
```typescript
// store/app.store.ts
export interface AppState {
  user: User | null;
  productos: Producto[];
  carrito: CarritoItem[];
  loading: boolean;
  error: string | null;
}

@Injectable({
  providedIn: 'root'
})
export class AppStore {
  private state = new BehaviorSubject<AppState>({
    user: null,
    productos: [],
    carrito: [],
    loading: false,
    error: null
  });

  // Selectores
  readonly user$ = this.state.pipe(map(state => state.user));
  readonly productos$ = this.state.pipe(map(state => state.productos));
  readonly carrito$ = this.state.pipe(map(state => state.carrito));
  readonly loading$ = this.state.pipe(map(state => state.loading));
  readonly error$ = this.state.pipe(map(state => state.error));

  // Acciones
  setUser(user: User | null) {
    this.updateState({ user });
  }

  setProductos(productos: Producto[]) {
    this.updateState({ productos });
  }

  agregarAlCarrito(item: CarritoItem) {
    const carrito = [...this.state.value.carrito];
    const index = carrito.findIndex(i => i.productoId === item.productoId);

    if (index >= 0) {
      carrito[index].cantidad += item.cantidad;
    } else {
      carrito.push(item);
    }

    this.updateState({ carrito });
  }

  setLoading(loading: boolean) {
    this.updateState({ loading });
  }

  setError(error: string | null) {
    this.updateState({ error });
  }

  private updateState(newState: Partial<AppState>) {
    this.state.next({
      ...this.state.value,
      ...newState
    });
  }
}
```

### Suscripciones en Tiempo Real Optimizadas

1. **Servicio de Suscripción con Caché**
```typescript
// services/realtime-cache.service.ts
@Injectable({
  providedIn: 'root'
})
export class RealtimeCacheService {
  private cache = new Map<string, BehaviorSubject<any[]>>();
  private subscriptions = new Map<string, RealtimeChannel>();

  constructor(
    private supabase: SupabaseService,
    private store: AppStore
  ) {}

  getCollection<T>(tabla: string): Observable<T[]> {
    if (!this.cache.has(tabla)) {
      this.cache.set(tabla, new BehaviorSubject<T[]>([]));
      this.iniciarSuscripcion<T>(tabla);
    }

    return this.cache.get(tabla)!.asObservable();
  }

  private iniciarSuscripcion<T>(tabla: string) {
    // Cargar datos iniciales
    this.cargarDatos<T>(tabla);

    // Suscribirse a cambios
    const subscription = this.supabase.client
      .channel(`cambios_${tabla}`)
      .on(
        'postgres_changes',
        { event: '*', schema: 'public', table: tabla },
        (payload) => this.manejarCambio<T>(tabla, payload)
      )
      .subscribe();

    this.subscriptions.set(tabla, subscription);
  }

  private async cargarDatos<T>(tabla: string) {
    try {
      this.store.setLoading(true);
      const { data, error } = await this.supabase.client
        .from(tabla)
        .select('*');

      if (error) throw error;
      this.cache.get(tabla)?.next(data);
    } catch (error) {
      this.store.setError(`Error al cargar ${tabla}: ${error.message}`);
    } finally {
      this.store.setLoading(false);
    }
  }

  private manejarCambio<T>(tabla: string, payload: RealtimePostgresChangesPayload<T>) {
    const datos = this.cache.get(tabla)?.value ?? [];

    switch (payload.eventType) {
      case 'INSERT':
        this.cache.get(tabla)?.next([...datos, payload.new]);
        break;

      case 'UPDATE':
        this.cache.get(tabla)?.next(
          datos.map(item => 
            item.id === payload.new.id ? payload.new : item
          )
        );
        break;

      case 'DELETE':
        this.cache.get(tabla)?.next(
          datos.filter(item => item.id !== payload.old.id)
        );
        break;
    }
  }

  limpiarCache() {
    this.cache.clear();
    this.subscriptions.forEach(subscription => {
      this.supabase.client.removeChannel(subscription);
    });
    this.subscriptions.clear();
  }
}
```

### Autenticación con OAuth y Roles

1. **Servicio de Autenticación Avanzado**
```typescript
// services/auth-advanced.service.ts
@Injectable({
  providedIn: 'root'
})
export class AuthAdvancedService {
  constructor(
    private supabase: SupabaseService,
    private store: AppStore,
    private router: Router
  ) {
    this.inicializarAuth();
  }

  private async inicializarAuth() {
    // Recuperar sesión
    const { data: { session } } = await this.supabase.client.auth.getSession();
    if (session) {
      await this.actualizarPerfil(session.user);
    }

    // Escuchar cambios de auth
    this.supabase.client.auth.onAuthStateChange(async (event, session) => {
      if (event === 'SIGNED_IN') {
        await this.actualizarPerfil(session?.user);
        this.router.navigate(['/dashboard']);
      } else if (event === 'SIGNED_OUT') {
        this.store.setUser(null);
        this.router.navigate(['/login']);
      }
    });
  }

  async loginConGoogle() {
    const { error } = await this.supabase.client.auth.signInWithOAuth({
      provider: 'google',
      options: {
        redirectTo: `${window.location.origin}/auth/callback`
      }
    });
    if (error) throw error;
  }

  async verificarRol(rol: string): Promise<boolean> {
    const { data: { user } } = await this.supabase.client.auth.getUser();
    if (!user) return false;

    const { data: roles } = await this.supabase.client
      .from('user_roles')
      .select('rol')
      .eq('user_id', user.id)
      .single();

    return roles?.rol === rol;
  }

  private async actualizarPerfil(user: User | null) {
    if (!user) return;

    try {
      const { data: perfil, error } = await this.supabase.client
        .from('perfiles')
        .select('*')
        .eq('id', user.id)
        .single();

      if (error) throw error;

      this.store.setUser({
        ...user,
        perfil
      });
    } catch (error) {
      console.error('Error al cargar perfil:', error);
    }
  }
}
```

### Ejemplos paso a paso

#### Estructura de Carpetas

```
clase05-bis/
├── classes/
│   ├── producto.class.ts
│   ├── carrito-item.class.ts
│   └── pedido.class.ts
├── pages/
│   ├── dashboard/
│   │   ├── dashboard.component.ts
│   │   ├── dashboard.component.html
│   │   └── dashboard.component.scss
│   ├── productos/
│   │   ├── productos.component.ts
│   │   ├── productos.component.html
│   │   └── productos.component.scss
│   └── carrito/
│       ├── carrito.component.ts
│       ├── carrito.component.html
│       └── carrito.component.scss
├── services/
│   ├── realtime-cache.service.ts
│   ├── auth-advanced.service.ts
│   └── productos.service.ts
├── store/
│   └── app.store.ts
└── guards/
    ├── auth.guard.ts
    └── role.guard.ts
```

#### Buenas Prácticas

1. **Gestión de Estado**
   - Usar un store centralizado
   - Mantener la inmutabilidad
   - Implementar selectores eficientes

2. **Optimización de Rendimiento**
   - Caché de datos en tiempo real
   - Desuscripción automática
   - Lazy loading de módulos

3. **Seguridad**
   - Autenticación OAuth
   - Control de roles
   - Políticas RLS en Supabase

4. **Manejo de Errores**
   - Interceptores HTTP
   - Manejo centralizado
   - Mensajes amigables

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
