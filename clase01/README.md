# Clase 01 - Introducción a Angular y TypeScript 🚀

## ¿Qué es Angular y Por Qué Usarlo?

### Introducción 📚

#### ¿Qué es Angular?
Angular es un framework (marco de trabajo) creado por Google para desarrollar aplicaciones web modernas. Piensa en él como un conjunto de herramientas y reglas que nos ayudan a construir sitios web de manera organizada y eficiente.

##### Ventajas de Angular:
- ✅ Organiza mejor nuestro código
- ✅ Hace las aplicaciones más rápidas
- ✅ Facilita el trabajo en equipo
- ✅ Tiene muchas herramientas incluidas

#### ¿Qué es TypeScript?
TypeScript es una versión mejorada de JavaScript que nos ayuda a escribir código con menos errores. Es como JavaScript con superpoderes:

```typescript
// JavaScript normal
function sumar(a, b) {
    return a + b;  // ¿Qué pasa si a o b no son números?
}

// TypeScript
function sumar(a: number, b: number): number {
    return a + b;  // TypeScript nos avisa si intentamos usar texto en lugar de números
}
```

#### Conceptos Básicos

1. **Componentes**
Son como piezas de LEGO que forman nuestra página web:
```typescript
// Ejemplo de un componente simple
@Component({
    selector: 'app-saludo',     // Cómo llamamos a este componente
    template: `
        <h1>Hola {{nombre}}!</h1>
        <button (click)="saludar()">Saludar</button>
    `
})
class SaludoComponent {
    nombre = 'Estudiante';      // Datos que usa el componente
    
    saludar() {                 // Acciones que puede hacer
        alert('Bienvenido a Angular!');
    }
}
```

2. **Templates (Plantillas)**
Son el HTML de nuestros componentes, pero con superpoderes:
```html
<!-- Ejemplo de template con funciones básicas -->
<div>
    <!-- Mostrar datos -->
    <p>Nombre: {{nombre}}</p>
    
    <!-- Responder a eventos -->
    <button (click)="hacerAlgo()">Haz clic</button>
    
    <!-- Mostrar o esconder elementos -->
    @if (estaLogueado) {
        <p>Bienvenido!</p>
    }
</div>
```

#### ¿Por qué Angular 19.2.5?
Esta versión (lanzada en 2025) trae mejoras importantes:
- 💯 Más fácil de aprender
- ⚡ Más rápido
- 🛠️ Mejores herramientas
- 🌟 Código más limpio

### Preparando Nuestro Entorno

Antes de empezar a programar, necesitamos instalar algunas herramientas:

1. **Node.js y npm**
   - Node.js es el motor que necesitamos para desarrollar
   - npm es el gestor de paquetes que instala las herramientas

2. **Visual Studio Code**
   - Es el editor de código que usaremos
   - Gratis y muy potente
   - Ideal para Angular

3. **Angular CLI**
   - Es nuestra herramienta principal para trabajar con Angular
   - Se instala fácilmente con npm

## Fundamentos de Angular para Principiantes 💻

### ¿Cómo Funciona Angular?

#### 1. La Magia de la Actualización Automática 🎉

Cuando programamos en Angular, la página se actualiza automáticamente cuando cambian los datos. Esto funciona así:

```typescript
// Ejemplo simple de actualización automática
@Component({
    template: `
        <h1>Contador: {{contador}}</h1>
        <button (click)="incrementar()">+1</button>
    `
})
class ContadorComponent {
    contador = 0;
    
    incrementar() {
        this.contador++; // ¡La página se actualiza sola!
    }
}
```

##### ¿Por qué se actualiza sola?
1. Angular vigila los cambios en tus datos
2. Cuando algo cambia, actualiza solo lo necesario
3. Todo esto es automático ¡tú no tienes que hacer nada!

#### 2. Organización del Código 📂

Angular nos ayuda a organizar nuestro código en partes pequeñas y reutilizables:

```typescript
// Componente padre (página principal)
@Component({
    template: `
        <header-menu />        <!-- Menú de navegación -->
        <lista-productos />   <!-- Lista de productos -->
        <pie-pagina />        <!-- Pie de página -->
    `
})
```

Es como armar un rompecabezas:
- Cada pieza es un componente
- Los componentes se pueden reutilizar
- Es más fácil encontrar y arreglar errores

#### 3. Servicios y Datos Compartidos 🔗

Los servicios son como bibliotecas que guardan y comparten información:

```typescript
// Servicio simple para compartir datos
@Injectable()
class CarritoService {
    productos = [];
    
    agregarProducto(producto) {
        this.productos.push(producto);
        console.log('Producto agregado al carrito!');
    }
}

// Usar el servicio en un componente
@Component({
    template: `
        <button (click)="agregar()">Agregar al Carrito</button>
    `
})
class ProductoComponent {
    // Angular nos da el servicio automáticamente
    constructor(private carrito: CarritoService) {}
    
    agregar() {
        this.carrito.agregarProducto({ nombre: 'Laptop', precio: 999 });
    }
}
```

### Herramientas que nos Facilitan la Vida 🛠️

1. **Angular DevTools**
   - Es como una lupa para ver cómo funciona nuestra aplicación
   - Nos ayuda a encontrar errores
   - Se instala como extensión en Chrome

2. **Visual Studio Code + Extensiones**
   ```bash
   # Extensiones recomendadas:
   - Angular Language Service   # Autocompletado de código
   - Angular Snippets          # Plantillas de código
   - Prettier                  # Formatea el código automáticamente
   ```

3. **Angular CLI (Línea de Comandos)**
   ```bash
   # Crear un nuevo componente
   ng generate component mi-componente
   
   # Crear un nuevo servicio
   ng generate service mi-servicio
   ```

## Características Modernas de Angular 19.2.5 ✨

### 1. Signals: Una Forma Más Fácil de Manejar Datos 💾

Los Signals son como variables inteligentes que nos avisan cuando cambian:

```typescript
// Ejemplo práctico de Signals
class TiendaComponent {
    // Signal básico - como una variable normal pero más inteligente
    productos = signal([                    
        { nombre: 'Laptop', precio: 999 },
        { nombre: 'Teléfono', precio: 699 }
    ]);
    
    // Signal computado - se calcula automáticamente
    totalProductos = computed(() =>         
        this.productos().length
    );
    
    // Efecto - se ejecuta cuando cambian los datos
    constructor() {
        effect(() => {                      
            console.log(`Tenemos ${this.totalProductos()} productos`);
        });
    }
    
    agregarProducto(producto) {
        // Actualizar un signal
        this.productos.update(lista => [...lista, producto]);
        // ¡Todo se actualiza automáticamente!
    }
}
```

### 2. Control de Flujo Más Simple 🔄

Ahora es más fácil escribir condiciones y bucles en nuestras páginas:

```typescript
@Component({
    template: `
        <!-- Lista de Productos -->
        <div class="productos">
            @if (productos().length > 0) {
                <!-- Si hay productos, los mostramos -->
                <ul>
                    @for (prod of productos(); track prod.nombre) {
                        <li>
                            {{prod.nombre}} - ${{prod.precio}}
                            @if (prod.precio < 700) {
                                <span class="oferta">¡OFERTA!</span>
                            }
                        </li>
                    }
                </ul>
            } @else {
                <!-- Si no hay productos -->
                <p>No hay productos disponibles</p>
            }
        </div>
    `
})
```

### 3. Carga Inteligente (Deferrable Views) 🚀

Podemos hacer que nuestra página cargue más rápido cargando algunas partes solo cuando son necesarias:

```typescript
@Component({
    template: `
        <!-- Esta parte siempre se carga rápido -->
        <header>
            <h1>Mi Tienda</h1>
            <nav-menu/>
        </header>

        <!-- Esta parte pesada se carga cuando es visible -->
        @defer (on viewport) {
            <catalogo-completo/>
        } @loading {
            <!-- Mientras se carga mostramos algo bonito -->
            <div class="loading">
                <spinner/>
                <p>Cargando catálogo...</p>
            </div>
        }

        <!-- Esto se carga cuando el navegador está libre -->
        @defer (on idle) {
            <sugerencias-productos/>
        }
    `
})
```

#### ¿Por qué es mejor?
1. ⚡ **Más Velocidad**: La página inicial carga super rápido
2. 💻 **Menos Recursos**: Solo carga lo que necesitas
3. 💯 **Mejor Experiencia**: Los usuarios no tienen que esperar tanto


## Herramientas de Desarrollo Modernas 🛠️

### 1. Sistema de Construcción Super Rápido

Angular 19.2.5 usa herramientas modernas para hacer que todo funcione más rápido:

#### ¿Qué hace el sistema de construcción?
1. 📦 **Empaqueta tu código**: Junta todos tus archivos
2. 🔧 **Lo optimiza**: Hace que todo sea más pequeño y rápido
3. 🚀 **Lo prepara para producción**: Listo para subir a internet

```bash
# Comandos principales

# Iniciar modo desarrollo (super rápido)
ng serve

# Construir para producción (optimizado)
ng build --prod

# Analizar el tamaño del proyecto
ng build --stats-json
```

### 2. Herramientas de Desarrollo

#### DevTools de Angular
```typescript
// Puedes ver información útil en el navegador
@Component({
    template: `
        <div #debug>
            <!-- DevTools te muestra: -->
            <!-- - Qué datos están cambiando -->
            <!-- - Cuándo se actualizan -->
            <!-- - Si hay problemas de rendimiento -->
            {{datos | json}}
        </div>
    `
})
```

#### Extensiones Recomendadas para VS Code
1. **Angular Language Service**
   - Autocompletado inteligente
   - Detecta errores mientras escribes
   - Sugerencias de código

2. **Angular Snippets**
   - Plantillas rápidas de código
   - Ahorra tiempo al escribir

3. **ESLint**
   - Encuentra errores
   - Mejora la calidad del códigos

## Organizando Nuestro Proyecto Angular 📂

### ¿Cómo se Organiza un Proyecto Angular?

Piensa en un proyecto Angular como una casa con diferentes habitaciones. Cada carpeta tiene su propósito:

```
📂 src/                      # Aquí vive todo nuestro código
├── 📂 app/                  # El corazón de nuestra aplicación
│   ├── 📂 components/       # Piezas reutilizables (botones, menús, etc.)
│   ├── 📂 pages/           # Páginas completas
│   ├── 📂 services/        # Código que maneja datos
│   ├── 📝 app.component.*   # La página principal
│   └── 📝 app.routes.ts     # Define qué página mostrar
├── 📂 assets/              # Imágenes, iconos, etc.
└── 📝 styles.css           # Estilos globales
```

#### ¿Por qué esta organización?
- 📚 **Fácil de Encontrar**: Cada cosa en su lugar
- 👥 **Fácil de Compartir**: Varios desarrolladores pueden trabajar a la vez
- 🔧 **Fácil de Mantener**: Si algo falla, sabemos dónde buscar

## Conectando Datos con la Pantalla 🔗

### Bindeos: Cómo Angular Conecta Todo

#### 1. Mostrar Datos (Interpolación)
```typescript
@Component({
    template: `
        <!-- Del código a la pantalla -->
        <h1>Bienvenido {{nombreUsuario}}!</h1>
        <p>Tienes {{cantidadMensajes}} mensajes nuevos</p>
    `
})
class BienvenidaComponent {
    nombreUsuario = 'Ana';           // Se muestra en el h1
    cantidadMensajes = 5;           // Se muestra en el p
}
```

#### 2. Responder a Eventos
```typescript
@Component({
    template: `
        <!-- Cuando el usuario hace algo -->
        <button (click)="saludar()">Saluda</button>
        
        <!-- Formularios simples -->
        <input (input)="actualizarNombre($event)">
    `
})
class InteractivoComponent {
    saludar() {
        alert('Hola! 👋');
    }
    
    actualizarNombre(evento: any) {
        console.log('Nuevo nombre:', evento.target.value);
    }
}
```

#### 3. Bindeo de Doble Vía (Two-way Binding)
```typescript
@Component({
    template: `
        <!-- El dato va y viene entre el código y la pantalla -->
        <input [(ngModel)]="nombre">
        <p>Hola {{nombre}}!</p>
        
        <!-- Ejemplo con un toggle -->
        <label>
            <input type="checkbox" [(ngModel)]="recibirNotificaciones">
            Quiero recibir notificaciones
        </label>
    `
})
class FormularioComponent {
    nombre = '';                     // Se actualiza automáticamente
    recibirNotificaciones = false;   // También se actualiza solo
}
```

### ¿Por qué es Genial?
1. 🚀 **Automático**: Angular actualiza todo por ti
2. 🔧 **Fácil**: No tienes que escribir mucho código
3. 💯 **Confiable**: Menos errores, más consistencia

## Datos Inteligentes en Angular 🤓

### 1. Datos que se Calculan Solos (Computed Values)

```typescript
@Component({
    template: `
        <!-- Tienda Online Simple -->
        <div class="carrito">
            <h2>Tu Carrito</h2>
            
            <!-- Lista de Productos -->
            <ul>
                @for (producto of productos(); track producto.id) {
                    <li>
                        {{producto.nombre}} - ${{producto.precio}}
                        <button (click)="eliminar(producto.id)">Eliminar</button>
                    </li>
                }
            </ul>
            
            <!-- Los totales se calculan automáticamente -->
            <p>Subtotal: ${{subtotal()}}</p>
            <p>IVA (21%): ${{iva()}}</p>
            <p>Total: ${{total()}}</p>
        </div>
    `
})
class CarritoComponent {
    // Lista de productos en el carrito
    productos = signal([
        { id: 1, nombre: 'Laptop', precio: 999 },
        { id: 2, nombre: 'Mouse', precio: 29 }
    ]);
    
    // Se calculan automáticamente cuando cambia la lista de productos
    subtotal = computed(() => 
        this.productos().reduce((sum, p) => sum + p.precio, 0)
    );
    
    iva = computed(() => this.subtotal() * 0.21);
    
    total = computed(() => this.subtotal() + this.iva());
    
    eliminar(id: number) {
        // Al eliminar un producto, todos los totales se actualizan solos
        this.productos.update(lista => 
            lista.filter(p => p.id !== id)
        );
    }
}
```

### 2. Efectos: Reaccionar a los Cambios 💡

```typescript
@Component({
    template: `
        <!-- Ejemplo de Preferencias de Usuario -->
        <div class="preferencias">
            <h3>Configuración</h3>
            
            <!-- Cambiar tema claro/oscuro -->
            <label>
                <input type="checkbox" 
                       [(ngModel)]="temaOscuro()">
                Tema Oscuro
            </label>
            
            <!-- Tamaño de texto -->
            <select [(ngModel)]="tamanoTexto()">
                <option value="small">Pequeño</option>
                <option value="medium">Mediano</option>
                <option value="large">Grande</option>
            </select>
        </div>
    `
})
class PreferenciasComponent {
    // Configuraciones del usuario
    temaOscuro = signal(false);
    tamanoTexto = signal('medium');
    
    constructor() {
        // Cuando cambia el tema, actualizamos el estilo
        effect(() => {
            document.body.classList.toggle(
                'tema-oscuro', 
                this.temaOscuro()
            );
        });
        
        // Cuando cambia el tamaño, guardamos la preferencia
        effect(() => {
            localStorage.setItem(
                'tamanoTexto', 
                this.tamanoTexto()
            );
        });
    }
}
```

### ¿Por qué son Útiles?

1. **Computed Values (Cálculos Automáticos)**
   - 📈 Se actualizan solos cuando cambian los datos
   - 💪 Son rápidos porque guardan el resultado en caché
   - 🤓 Mantienen tu código organizado

2. **Effects (Efectos)**
   - 🔄 Reaccionan automáticamente a los cambios
   - 💾 Perfectos para guardar datos
   - 🔧 Ideales para actualizar la interfaz

## Creando Páginas Web Dinámicas 🌈

### Diferentes Formas de Mostrar y Actualizar Datos

#### 1. Mostrando Datos en la Página 📝

```typescript
@Component({
    template: `
        <!-- Ejemplo de una Tarjeta de Producto -->
        <div class="producto-card">
            <!-- Texto simple -->
            <h2>{{producto.nombre}}</h2>
            
            <!-- Números con formato -->
            <p class="precio">${{producto.precio | number:'1.2-2'}}</p>
            
            <!-- Imágenes dinámicas -->
            <img [src]="producto.imagen" [alt]="producto.nombre">
            
            <!-- Clases condicionales -->
            <span class="etiqueta"
                  [class.oferta]="producto.enOferta"
                  [class.agotado]="!producto.disponible">
                {{producto.etiqueta}}
            </span>
            
            <!-- Estilos dinámicos -->
            <div class="stock-bar"
                 [style.width.%]="producto.stockPorcentaje"
                 [style.background-color]="producto.stockColor">
            </div>
        </div>
    `
})
class ProductoCardComponent {
    producto = {
        nombre: 'Super Laptop 3000',
        precio: 999.99,
        imagen: 'laptop.jpg',
        enOferta: true,
        disponible: true,
        etiqueta: '¡OFERTA!',
        stockPorcentaje: 75,
        stockColor: '#4CAF50'
    };
}
```

#### 2. Interactuando con el Usuario 👆

```typescript
@Component({
    template: `
        <!-- Formulario de Búsqueda -->
        <div class="buscador">
            <!-- Input con actualización instantánea -->
            <input type="text"
                   [(ngModel)]="terminoBusqueda"
                   placeholder="¿Qué estás buscando?"
                   (keyup.enter)="buscar()">
            
            <!-- Botón con evento click -->
            <button (click)="buscar()">
                🔍 Buscar
            </button>
            
            <!-- Filtros con checkbox -->
            <div class="filtros">
                <label>
                    <input type="checkbox"
                           [(ngModel)]="soloOfertas">
                    Mostrar solo ofertas
                </label>
                
                <!-- Select con opciones -->
                <select [(ngModel)]="ordenarPor"
                        (change)="actualizarResultados()">
                    <option value="precio">Precio: Menor a Mayor</option>
                    <option value="-precio">Precio: Mayor a Menor</option>
                    <option value="nombre">Nombre: A-Z</option>
                </select>
            </div>
        </div>
    `
})
class BuscadorComponent {
    terminoBusqueda = '';
    soloOfertas = false;
    ordenarPor = 'precio';
    
    buscar() {
        console.log(`Buscando: ${this.terminoBusqueda}`);
        console.log(`Solo ofertas: ${this.soloOfertas}`);
        console.log(`Ordenar por: ${this.ordenarPor}`);
    }
    
    actualizarResultados() {
        console.log('Actualizando resultados...');
    }
}
```

### ¿Por qué es Especial? ✨

1. **Todo está Conectado**
   - 🔗 Los datos y la pantalla siempre están sincronizados
   - 💡 Angular actualiza automáticamente lo que cambia
   - 🌟 No necesitas escribir código extra

2. **Fácil de Entender**
   - 👍 Sintaxis clara y directa
   - 📚 Se parece mucho al HTML normal
   - 🔧 Herramientas que te ayudan a escribir código

3. **Potente y Flexible**
   - 💪 Puedes hacer cosas simples y complejas
   - 🌈 Fácil de personalizar
   - 🚀 Optimizado para mejor rendimiento  `
})
export class SearchComponent {
  searchTerm = signal('');
  
  onSearchChange(value: string) {
    // Actualización reactiva
    this.searchTerm.set(value);
  }
}
```

##### Control Flow Moderno
```typescript
@Component({
  template: `
    <!-- Control de Flujo Declarativo -->
    @if (isLoading()) {
      <loading-spinner />
    } @else if (hasError()) {
      <error-message [error]="error()" />
    } @else {
      <data-view [data]="data()" />
    }

    <!-- Iteración Optimizada -->
    @for (item of items(); track item.id) {
      <item-component
        [item]="item"
        (update)="onUpdate($event)"
      />
    }

    <!-- Switch Cases -->
    @switch (status()) {
      @case ('loading') {
        <loading-view />
      }
      @case ('error') {
        <error-view />
      }
      @default {
        <default-view />
      }
    }
  `
})
```

### 3. Arquitectura de Componentes en Angular 19.2.5 🧩

#### 3.1 Componentes Standalone y Arquitectura Moderna

##### Anatomía de un Componente Standalone
```typescript
@Component({
  // Metadatos fundamentales
  selector: 'app-component',
  standalone: true,
  
  // Importaciones locales (reemplazan a NgModule)
  imports: [
    CommonModule,
    RouterModule,
    ReactiveFormsModule
  ],
  
  // Gestión de recursos
  templateUrl: './component.html',
  styleUrl: './component.css',
  
  // Optimizaciones de rendimiento
  changeDetection: ChangeDetectionStrategy.OnPush,
  host: {
    '[class.active]': 'isActive',
    '(click)': 'onClick($event)'
  },
  
  // Providers locales
  providers: [
    {
      provide: SOME_TOKEN,
      useFactory: () => new Service()
    }
  ],
  
  // Queries y ViewChild
  viewProviders: [SomeService],
  queries: {
    contentChildren: new ContentChildren(ChildComponent)
  }
})
```

##### Ciclo de Vida Moderno
```typescript
export class ModernComponent implements OnInit {
  // Signals para estado
  private readonly state = signal<ComponentState>('initial');
  public readonly viewModel = computed(() => this.buildViewModel());
  
  // Inyección moderna
  private service = inject(DataService);
  private cd = inject(ChangeDetectorRef);
  
  // Ciclo de vida con cleanup automático
  constructor() {
    effect(() => {
      // Efectos automáticamente limpiados
      const sub = this.service.data$.subscribe(
        data => this.state.set(data)
      );
    });
  }
  
  // Hooks modernos
  ngOnInit() {
    // Inicialización con signals
    this.state.update(s => ({ ...s, initialized: true }));
  }
}
```

#### 3.2 Patrones de Diseño de Componentes

##### Patrón Presentacional/Contenedor
```typescript
// Componente Contenedor
@Component({
  selector: 'app-user-list-container',
  template: `
    <app-user-list
      [users]="users()"
      [loading]="loading()"
      (userSelected)="onUserSelected($event)"
    />
  `
})
export class UserListContainerComponent {
  users = signal<User[]>([]);
  loading = signal(false);
  
  onUserSelected(user: User) {
    // Lógica de negocio
  }
}

// Componente Presentacional
@Component({
  selector: 'app-user-list',
  template: `
    @if (loading) {
      <spinner/>
    } @else {
      @for (user of users; track user.id) {
        <user-card
          [user]="user"
          (click)="userSelected.emit(user)"
        />
      }
    }
  `
})
export class UserListComponent {
  @Input() users: User[] = [];
  @Input() loading = false;
  @Output() userSelected = new EventEmitter<User>();
}
```

### 4. Sistema de Routing Avanzado 🗺️

#### 4.1 Configuración de Rutas Moderna## Navegación en Angular: Cómo Moverse Entre Páginas 📖

### ¿Cómo Funciona la Navegación?

Piensa en tu aplicación como un libro con diferentes páginas. El sistema de rutas de Angular te ayuda a moverte entre ellas.

#### 1. Configurando las Páginas de tu App 🗺ï¸

```typescript
// app.routes.ts - Aquí defines qué páginas tiene tu app
const routes: Routes = [
    // Página principal
    { 
        path: '',                    // URL: http://tuapp.com/
        component: InicioComponent
    },
    
    // Página de productos
    { 
        path: 'productos',           // URL: http://tuapp.com/productos
        component: ProductosComponent
    },
    
    // Página de detalle de producto
    { 
        path: 'producto/:id',        // URL: http://tuapp.com/producto/123
        component: DetalleComponent
    },
    
    // Página de error 404
    { 
        path: '**',                  // Cualquier otra URL
        component: NoEncontradoComponent
    }
];
```

#### 2. Menú de Navegación 👉

```typescript
@Component({
    template: `
        <!-- Menú de Navegación Simple -->
        <nav class="menu">
            <!-- Enlaces con routerLink -->
            <a routerLink="/"
               routerLinkActive="activo"
               [routerLinkActiveOptions]="{exact: true}">
                🏠 Inicio
            </a>
            
            <a routerLink="/productos"
               routerLinkActive="activo">
                🛒 Productos
            </a>
            
            <a routerLink="/contacto"
               routerLinkActive="activo">
                ✉️ Contacto
            </a>
        </nav>

        <!-- Aquí se muestra la página actual -->
        <main>
            <router-outlet></router-outlet>
        </main>
    `
})
class AppComponent {}
```

#### 3. Navegación Programática 💻

```typescript
@Component({
    template: `
        <!-- Ejemplo de Tarjeta de Producto -->
        <div class="producto-card">
            <h3>{{producto.nombre}}</h3>
            <p>Precio: ${{producto.precio}}</p>
            
            <!-- Botón para ver detalles -->
            <button (click)="verDetalles(producto.id)">
                Ver Más Detalles ➡️
            </button>
            
            <!-- Botón para volver -->
            <button (click)="volver()">
                ⬅️ Volver
            </button>
        </div>
    `
})
class ProductoComponent {
    constructor(private router: Router) {}
    
    // Ir a la página de detalles
    verDetalles(id: number) {
        this.router.navigate(['/producto', id]);
    }
    
    // Volver a la página anterior
    volver() {
        window.history.back();
    }
}
```

### Protegiendo Páginas (Guards) 🔒

```typescript
// Ejemplo: Proteger páginas para usuarios logueados
const estaLogueado: CanActivateFn = (route, state) => {
    // Obtener el servicio de autenticación
    const auth = inject(AuthService);
    
    // Verificar si el usuario está logueado
    if (auth.estaLogueado()) {
        return true;    // Permitir acceso
    } else {
        // Redirigir al login
        const router = inject(Router);
        router.navigate(['/login']);
        return false;   // Denegar acceso
    }
};

// Usar el guard en las rutas
const routes: Routes = [
    { 
        path: 'perfil',
        component: PerfilComponent,
        canActivate: [estaLogueado]    // Proteger esta ruta
    }
];
```

### ¿Por qué es Útil? 💡

1. **Organización Clara**
   - 📚 Cada página tiene su propia URL
   - 🔖 Fácil de compartir enlaces
   - ⬅️ Botón de "atrás" funciona correctamente

2. **Seguridad**
   - 🔒 Puedes proteger páginas privadas
   - 🚪 Control de acceso por roles
   - 🛡️ Previene accesos no autorizados

3. **Experiencia de Usuario**
   - ⚡ Navegación instantánea
   - 📱 Funciona como una app nativa
   - 🌟 Menús activos se actualizan solos

## ¡Manos a la Obra! 💻

### Ejercicios Prácticos para Empezar

#### 1. Componente de Lista de Productos

```typescript
// Crear un componente para mostrar productos
@Component({
    selector: 'app-productos',
    template: `
        <div class="productos">
            <h2>Nuestros Productos</h2>
            
            <!-- Barra de búsqueda -->
            <input [(ngModel)]="busqueda"
                   placeholder="Buscar productos..."
                   (input)="filtrarProductos()">
            
            <!-- Lista de productos -->
            <div class="grid-productos">
                @for (producto of productosFiltrados(); track producto.id) {
                    <div class="producto-card">
                        <img [src]="producto.imagen" 
                             [alt]="producto.nombre">
                        <h3>{{producto.nombre}}</h3>
                        <p>{{producto.descripcion}}</p>
                        <p class="precio">
                            ${{producto.precio}}
                            @if (producto.enOferta) {
                                <span class="oferta">OFERTA!</span>
                            }
                        </p>
                        <button (click)="agregarAlCarrito(producto)">
                            🛒 Agregar al Carrito
                        </button>
                    </div>
                } @empty {
                    <p>No se encontraron productos</p>
                }
            </div>
        </div>
    `,
    styles: [`
        .grid-productos {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
            gap: 20px;
            padding: 20px;
        }
        
        .producto-card {
            border: 1px solid #ddd;
            padding: 15px;
            border-radius: 8px;
            text-align: center;
        }
        
        .oferta {
            background: #ff4444;
            color: white;
            padding: 2px 8px;
            border-radius: 4px;
            margin-left: 8px;
            font-size: 0.8em;
        }
    `]
})
class ProductosComponent {
    // Datos
    productos = signal([
        {
            id: 1,
            nombre: 'Laptop Pro',
            descripcion: 'Perfecta para desarrollo',
            precio: 1299.99,
            imagen: 'laptop.jpg',
            enOferta: true
        },
        // Agregar más productos...
    ]);
    
    busqueda = '';
    
    // Productos filtrados (se actualiza automáticamente)
    productosFiltrados = computed(() => {
        return this.productos().filter(p =>
            p.nombre.toLowerCase()
             .includes(this.busqueda.toLowerCase())
        );
    });
    
    agregarAlCarrito(producto: any) {
        // TODO: Implementar lógica del carrito
        console.log('Agregado:', producto.nombre);
    }
    
    filtrarProductos() {
        // La lista se actualiza automáticamente
        // gracias al computed!
    }
}
```

### Recursos para Aprender Más 📖

#### 1. Documentación Oficial
- [📖 Guía de Inicio Rápido](https://angular.dev/guide/quickstart)
- [🎥 Tutorial Paso a Paso](https://angular.dev/tutorials/first-app)
- [🔧 Referencia de API](https://angular.dev/api)

#### 2. Herramientas Esenciales
- [Visual Studio Code](https://code.visualstudio.com/)
- [Angular DevTools](https://chrome.google.com/webstore/detail/angular-devtools/ienfalfjdbdpebioblfackkekamfmbnh)
- [Angular CLI](https://angular.dev/tools/cli)

#### 3. Comunidad
- [💬 Stack Overflow](https://stackoverflow.com/questions/tagged/angular)
- [👥 Discord Angular](https://discord.gg/angular)
- [📈 Blog Angular](https://blog.angular.io/)

### Próximos Pasos 👣

1. **Practica lo Básico**
   - Crea más componentes
   - Experimenta con Signals
   - Prueba diferentes bindeos

2. **Mejora el Proyecto**
   - Agrega más funcionalidades
   - Mejora el diseño
   - Implementa navegación

3. **Explora Temas Avanzados**
   - Manejo de estado
   - Llamadas a APIs
   - Testing

---

👋 ¡Felicitaciones! Has completado la introducción a Angular 19.2.5.

Desarrollado con ❤️ para UTN FRRO - 2025
  
  return computed(() => {
    if (authService.isAuthenticated()) {
      return true;
    }
    
    return router.createUrlTree(['/login'], {
      queryParams: { returnUrl: state.url }
    });
  });
};
```

## Configuración del Entorno de Desarrollo 🚀

### Requisitos del Sistema para Angular 19.2.5
- Node.js v20.x o superior
- npm v10.x o superior
- TypeScript 5.4 o superior
- Navegadores modernos (Chrome, Firefox, Edge, Safari)

### Configuración Paso a Paso

1. **Preparación del Sistema**
```bash
# Actualizar npm a la última versión
npm install -g npm@latest

# Limpiar caché de npm (recomendado)
npm cache clean --force

# Instalar Angular CLI 19.2.5
npm install -g @angular/cli@19.2.5
```

2. **Crear Nuevo Proyecto**
```bash
# Crear proyecto con la última configuración
ng new mi-app --standalone --routing --style=scss

# Entrar al directorio
cd mi-app

# Instalar dependencias
npm install
```

3. **Configuración de Desarrollo**
```bash
# Iniciar servidor con recarga automática
ng serve

# Modo desarrollo con optimizaciones
ng serve --optimization

# Desarrollo con SSL (para PWA)
ng serve --ssl
```

4. **Verificación de la Instalación**
```bash
# Verificar versiones
ng version

# Deberías ver algo como:
# Angular CLI: 19.2.5
# Node: 20.x.x
# Package Manager: npm 10.x.x
# OS: Windows
```

## Ejercicios Técnicos Detallados 💻

### 1. Implementación de Signals y Control Flow

```typescript
// 1. Crear un servicio de estado
@Injectable({ providedIn: 'root' })
export class TaskService {
  private tasks = signal<Task[]>([]);
  private loading = signal(false);
  
  readonly completedTasks = computed(() => 
    this.tasks().filter(t => t.completed)
  );
  
  async loadTasks() {
    this.loading.set(true);
    try {
      const data = await fetchTasks();
      this.tasks.set(data);
    } finally {
      this.loading.set(false);
    }
  }
}

// 2. Implementar componente con control flow
@Component({
  template: `
    @if (loading()) {
      <loading-spinner/>
    } @else {
      @for (task of tasks(); track task.id) {
        <task-item
          [task]="task"
          (statusChange)="updateStatus($event)"
        />
      } @empty {
        <no-tasks-message/>
      }
    }
  `
})
```

### 2. Implementación de Lazy Loading y Prefetching

```typescript
// 1. Configuración de rutas con prefetching
const routes: Routes = [{
  path: 'dashboard',
  loadComponent: () => import('./dashboard.component'),
  providers: [providePreloadStrategy()],
  data: { preload: true }
}];

// 2. Estrategia de precarga personalizada
@Injectable()
export class CustomPreloadStrategy implements PreloadAllModules {
  preload(route: Route, load: () => Observable<any>): Observable<any> {
    return route.data?.['preload'] ? load() : EMPTY;
  }
}
```

### 3. Implementación de Deferrable Views

```typescript
@Component({
  template: `
    <!-- Carga inmediata del contenido crítico -->
    <header-component/>
    
    <!-- Carga diferida con triggers -->
    @defer (on viewport) {
      <heavy-chart [data]="chartData()"/>
    } @loading {
      <chart-placeholder/>
    } @error {
      <error-message/>
    }
    
    <!-- Carga diferida con prefetch -->
    @defer (prefetch on idle) {
      <comments-section/>
    }
  `
})
```

## Recursos Técnicos Avanzados 📚

### Documentación Oficial de Angular 19.2.5
- [Guía de Inicio Rápido](https://angular.dev/tutorials/learn-angular)
- [Referencia de Signals](https://angular.dev/guide/signals)
- [Control Flow](https://angular.dev/guide/templates/control-flow)
- [Deferrable Views](https://angular.dev/guide/defer)

### Herramientas y Desarrollo
- [Angular DevTools](https://angular.dev/tools/devtools)
- [Angular CLI](https://angular.dev/tools/cli)
- [Debugging](https://angular.dev/tools/cli/debugging)

### Conceptos Fundamentales
- [Componentes](https://angular.dev/guide/components)
- [Directivas](https://angular.dev/guide/directives)
- [Seguridad](https://angular.dev/guide/security)

## Próximos Temas Técnicos 🎯

1. **Arquitectura Avanzada**
   - Implementación de estado global con Signals
   - Patrones de composición de componentes
   - Estrategias de detección de cambios

2. **Optimización de Rendimiento**
   - Técnicas de lazy loading
   - Estrategias de caché
   - Optimización de bundles

3. **Testing Avanzado**
   - Testing de componentes con signals
   - E2E testing con Playwright
   - Performance testing

---

Desarrollado con ❤️ para UTN FRRO - 2025
