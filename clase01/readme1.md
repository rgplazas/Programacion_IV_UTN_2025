# Guía Paso a Paso: Angular 19.2.5 🚀

## Parte 1: Configuración Inicial

### Novedades de Angular 19.2.5 🌟

- **Arquitectura Standalone**: Sin módulos, componentes más independientes
- **Signals**: Sistema reactivo mejorado
- **Control Flow**: Nueva sintaxis `@if`, `@for`, `@switch`
- **Deferrable Views**: Carga diferida inteligente
- **SSR y SSG**: Mejoras en renderizado del lado del servidor

### Requisitos del Sistema 💻

1. **Software Necesario**:
   ```plaintext
   - Node.js: v20.x o superior
   - npm: v10.x o superior
   - Memoria RAM: 8GB mínimo recomendado
   - Espacio en Disco: 1GB mínimo
   ```

2. **Navegadores Soportados**:
   ```plaintext
   - Chrome: última versión
   - Firefox: última versión
   - Edge: última versión
   - Safari: última versión
   ```

### Paso 1: Preparación del Entorno 🔧

1. **Instalar Node.js**:
   ```bash
   # Verificar que no esté instalado
   node --version

   # Si no está instalado:
   # 1. Ve a https://nodejs.org
   # 2. Descarga la versión 20.x LTS
   # 3. Instala y verifica:
   node --version  # Debe mostrar v20.x.x
   ```

2. **Actualizar npm**:
   ```bash
   # Actualizar npm globalmente
   npm install -g npm@latest

   # Verificar versión
   npm --version  # Debe mostrar 10.x.x

   # Limpiar caché
   npm cache clean --force
   ```

### Paso 2: Instalar Angular CLI 19.2.5 🛠️

1. **Desinstalar versiones anteriores**:
   ```bash
   # Remover versión anterior si existe
   npm uninstall -g @angular/cli
   ```

2. **Instalar versión específica**:
   ```bash
   # Instalar Angular CLI 19.2.5
   npm install -g @angular/cli@19.2.5

   # Verificar instalación
   ng version

   # Deberías ver:
   # Angular CLI: 19.2.5
   # Node: 20.x.x
   # Package Manager: npm 10.x.x
   ```

### Paso 3: Crear Proyecto 📒

1. **Generar nuevo proyecto**:
   ```bash
   # Crear proyecto con configuración moderna
   ng new mi-app-angular --standalone --routing --style=scss

   # Opciones a seleccionar:
   # ✅ Would you like to add Angular routing? Yes
   # ✅ Which stylesheet format would you like to use? SCSS
   # ✅ Do you want to enable Server-Side Rendering (SSR)? Yes
   ```

2. **Estructura del proyecto**:
   ```plaintext
   mi-app-angular/
   ├── src/
   │   ├── app/                    # Componentes y lógica
   │   │   ├── components/         # Componentes standalone
   │   │   ├── services/           # Servicios con signals
   │   │   ├── app.config.ts       # Configuración moderna
   │   │   └── app.routes.ts       # Rutas con lazy loading
   │   ├── assets/                # Recursos estáticos
   │   └── styles/                # Estilos SCSS
   ├── package.json              # Dependencias
   └── tsconfig.json             # Config TypeScript
   ```

3. **Iniciar el desarrollo**:
   ```bash
   # Entrar al directorio
   cd mi-app-angular

   # Instalar dependencias
   npm install

   # Iniciar servidor de desarrollo
   ng serve
   ```

4. **Verificar instalación**:
   - Abrir `http://localhost:4200`
   - Deberías ver la página de bienvenida

[Continúa en Parte 2: Componentes Standalone...]

## Parte 2: Componente de Bienvenida 👋

### Paso 1: Crear el Componente

1. **Generar el componente standalone**:
   ```bash
   ng generate component components/bienvenida --standalone
   ```

### Paso 2: Implementar el Componente

1. **Editar bienvenida.component.ts**:
   ```typescript
   import { Component, signal, computed } from '@angular/core';
   import { CommonModule } from '@angular/common';
   import { RouterLink } from '@angular/router';

   interface Tema {
     titulo: string;
     descripcion: string;
     icono: string;
   }

   @Component({
     selector: 'app-bienvenida',
     standalone: true,
     imports: [CommonModule, RouterLink],
     template: `
       <main class="bienvenida">
         <!-- Encabezado -->
         <header class="hero">
           <h1>{{titulo}}</h1>
           <p class="subtitulo">{{descripcion()}}</p>
         </header>

         <!-- Formulario de Nombre -->
         <section class="seccion-nombre">
           <label for="nombre">Tu nombre:</label>
           <input
             #nombreInput
             type="text"
             id="nombre"
             [placeholder]="placeholderNombre()"
             (input)="actualizarNombre(nombreInput.value)"
           >
           @if (nombre()) {
             <p class="saludo">¡Hola, {{nombre()}}! 👋</p>
           }
         </section>

         <!-- Lista de Temas -->
         <section class="temas">
           <h2>Temas que Aprenderemos</h2>
           
           @for (tema of temas(); track tema.titulo) {
             <div class="tema-card">
               <span class="tema-icono">{{tema.icono}}</span>
               <div class="tema-contenido">
                 <h3>{{tema.titulo}}</h3>
                 <p>{{tema.descripcion}}</p>
               </div>
             </div>
           }
         </section>
       </main>
     `,
     styles: [`
       .bienvenida {
         max-width: 800px;
         margin: 0 auto;
         padding: 2rem;
       }

       .hero {
         text-align: center;
         margin-bottom: 3rem;

         h1 {
           font-size: 2.5rem;
           color: #7b1fa2;
           margin-bottom: 1rem;
         }

         .subtitulo {
           font-size: 1.2rem;
           color: #666;
         }
       }

       .seccion-nombre {
         text-align: center;
         margin-bottom: 3rem;

         label {
           display: block;
           margin-bottom: 0.5rem;
           color: #666;
         }

         input {
           padding: 0.8rem;
           font-size: 1rem;
           border: 2px solid #ddd;
           border-radius: 4px;
           width: 100%;
           max-width: 300px;

           &:focus {
             outline: none;
             border-color: #7b1fa2;
           }
         }

         .saludo {
           margin-top: 1rem;
           font-size: 1.2rem;
           color: #7b1fa2;
         }
       }

       .temas {
         h2 {
           text-align: center;
           margin-bottom: 2rem;
           color: #7b1fa2;
         }

         .tema-card {
           display: flex;
           align-items: center;
           padding: 1rem;
           background: white;
           border-radius: 8px;
           box-shadow: 0 2px 4px rgba(0,0,0,0.1);
           margin-bottom: 1rem;
           transition: transform 0.3s ease;

           &:hover {
             transform: translateY(-2px);
           }

           .tema-icono {
             font-size: 2rem;
             margin-right: 1rem;
           }

           .tema-contenido {
             h3 {
               margin: 0;
               color: #7b1fa2;
             }

             p {
               margin: 0.5rem 0 0;
               color: #666;
             }
           }
         }
       }
     `]
   })
   export class BienvenidaComponent {
     // Propiedades básicas
     titulo = 'Bienvenido a Angular 19.2.5';

     // Signals
     nombre = signal('');
     temas = signal<Tema[]>([
       {
         titulo: 'Componentes Standalone',
         descripcion: 'Componentes independientes sin necesidad de módulos',
         icono: '🌱'
       },
       {
         titulo: 'Control Flow',
         descripcion: 'Nueva sintaxis @if, @for, @switch para mejor control',
         icono: '🔄'
       },
       {
         titulo: 'Signals',
         descripcion: 'Sistema reactivo mejorado para estado y computaciones',
         icono: '📈'
       },
       {
         titulo: 'Deferrable Views',
         descripcion: 'Carga diferida inteligente de componentes',
         icono: '🚀'
       }
     ]);

     // Computed values
     descripcion = computed(() => 
       this.nombre() 
         ? `Personaliza tu experiencia de aprendizaje, ${this.nombre()}` 
         : 'Comienza tu viaje con la última versión de Angular'
     );

     placeholderNombre = computed(() => 
       this.nombre() ? 'Cambiar nombre' : 'Escribe tu nombre'
     );

     // Métodos
     actualizarNombre(nuevoNombre: string) {
       this.nombre.set(nuevoNombre.trim());
     }
   }
   ```

### Paso 3: Configurar la Ruta

1. **Editar app.routes.ts**:
   ```typescript
   import { Routes } from '@angular/router';

   export const routes: Routes = [
     {
       path: '',
       loadComponent: () => 
         import('./components/bienvenida/bienvenida.component')
         .then(m => m.BienvenidaComponent)
     }
   ];
   ```

### Paso 4: Probar el Componente

1. **Verificar que el servidor esté corriendo**:
   ```bash
   ng serve
   ```

1. **Generar el componente**:
   ```bash
   ng generate component components/bienvenida
   # o la versión corta:
   ng generate component components/bindeos
   ```

### Paso 2: Implementar el Componente

1. **Editar bindeos.component.ts**:
   ```typescript
   import { Component, signal, computed } from '@angular/core';
   import { CommonModule } from '@angular/common';
   import { FormsModule } from '@angular/forms';

   @Component({
     selector: 'app-bindeos',
     standalone: true,
     imports: [CommonModule, FormsModule],
     template: `
       <div class="bindeos-container">
         <h2>Ejemplos de Bindeos en Angular 19.2.5 🔗</h2>

         <!-- 1. Interpolación -->
         <section class="ejemplo">
           <h3>1. Interpolación</h3>
           <p>Mensaje: {{mensaje}}</p>
         </section>

         <!-- 2. Property Binding -->
         <section class="ejemplo">
           <h3>2. Property Binding</h3>
           <img [src]="imagenUrl" 
                [alt]="imagenAlt"
                [style.width.px]="imagenWidth">
         </section>

         <!-- 3. Event Binding -->
         <section class="ejemplo">
           <h3>3. Event Binding</h3>
           <button (click)="incrementarContador()"
                   [style.backgroundColor]="contadorColor()">
             Clicks: {{contador()}}
           </button>
         </section>

         <!-- 4. Two-way Binding -->
         <section class="ejemplo">
           <h3>4. Two-way Binding</h3>
           <input [(ngModel)]="textoUsuario"
                  placeholder="Escribe algo...">
           <p>Has escrito: {{textoUsuario}}</p>
         </section>

         <!-- 5. Signals y Computed -->
         <section class="ejemplo">
           <h3>5. Signals y Computed</h3>
           <div class="contador-acciones">
             <button (click)="decrementar()">-</button>
             <span>{{contador()}}</span>
             <button (click)="incrementarContador()">+</button>
           </div>
           <p>El doble es: {{dobleContador()}}</p>
         </section>

         <!-- 6. Estilos Condicionales -->
         <section class="ejemplo">
           <h3>6. Estilos Condicionales</h3>
           <p [class.positivo]="contador() > 0"
              [class.negativo]="contador() < 0">
             El contador es {{contador() > 0 ? 'positivo' : contador() < 0 ? 'negativo' : 'cero'}}
           </p>
         </section>
       </div>
     `,
     styles: [`
       .bindeos-container {
         max-width: 800px;
         margin: 2rem auto;
         padding: 2rem;
         background: white;
         border-radius: 8px;
         box-shadow: 0 2px 4px rgba(0,0,0,0.1);
       }

       .ejemplo {
         margin: 2rem 0;
         padding: 1rem;
         border: 1px solid #eee;
         border-radius: 4px;

         h3 {
           color: #7b1fa2;
           margin-top: 0;
         }
       }

       button {
         padding: 0.5rem 1rem;
         border: none;
         border-radius: 4px;
         cursor: pointer;
         font-size: 1rem;
         transition: all 0.3s ease;

         &:hover {
           opacity: 0.9;
         }
       }

       .contador-acciones {
         display: flex;
         gap: 1rem;
         align-items: center;
         
         button {
           width: 40px;
           height: 40px;
           display: flex;
           align-items: center;
           justify-content: center;
           background: #7b1fa2;
           color: white;
         }
       }

       input {
         padding: 0.5rem;
         border: 2px solid #ddd;
         border-radius: 4px;
         font-size: 1rem;
         width: 100%;
         max-width: 300px;

         &:focus {
           outline: none;
           border-color: #7b1fa2;
         }
       }

       .positivo { color: #4caf50; }
       .negativo { color: #f44336; }
     `]
   })
   export class BindeosComponent {
     // Propiedades básicas
     mensaje = 'Hola desde Angular 19.2.5';
     imagenUrl = 'https://angular.dev/assets/angular-logo.svg';
     imagenAlt = 'Logo de Angular';
     imagenWidth = 150;
     textoUsuario = '';

     // Signals
     contador = signal(0);
     
     // Computed signals
     dobleContador = computed(() => this.contador() * 2);
     contadorColor = computed(() => {
       const valor = this.contador();
       if (valor > 0) return '#4caf50';
       if (valor < 0) return '#f44336';
       return '#666666';
     });

     // Métodos
     incrementarContador() {
       this.contador.update(n => n + 1);
     }

     decrementar() {
       this.contador.update(n => n - 1);
     }
   }
   ```

### Paso 3: Agregar la Ruta

1. **Actualizar app.routes.ts**:
   ```typescript
   import { Routes } from '@angular/router';

   export const routes: Routes = [
     {
       path: '',
       loadComponent: () => 
         import('./components/bienvenida/bienvenida.component')
         .then(m => m.BienvenidaComponent)
     },
     {
       path: 'bindeos',
       loadComponent: () => 
         import('./components/bindeos/bindeos.component')
         .then(m => m.BindeosComponent)
     }
   ];
   ```

### Paso 4: Agregar Navegación

1. **Actualizar app.component.ts**:
   ```typescript
   import { Component } from '@angular/core';
   import { RouterModule } from '@angular/router';

   @Component({
     selector: 'app-root',
     standalone: true,
     imports: [RouterModule],
     template: `
       <nav class="navegacion">
         <a routerLink="/" routerLinkActive="activo"
            [routerLinkActiveOptions]="{exact: true}">
           🏠 Inicio
         </a>
         <a routerLink="/bindeos" routerLinkActive="activo">
           🔗 Bindeos
         </a>
       </nav>

       <main>
         <router-outlet/>
       </main>
     `,
     styles: [`
       .navegacion {
         padding: 1rem;
         background: white;
         box-shadow: 0 2px 4px rgba(0,0,0,0.1);
         display: flex;
         gap: 1rem;

         a {
           text-decoration: none;
           color: #666;
           padding: 0.5rem 1rem;
           border-radius: 4px;
           transition: all 0.3s ease;

           &:hover {
             background: #f0f0f0;
           }

           &.activo {
             background: #7b1fa2;
             color: white;
           }
         }
       }

       main {
         padding: 1rem;
       }
     `]
   })
   export class AppComponent {}
   ```

### Paso 5: Probar los Bindeos

1. **Verificar que el servidor esté corriendo**:
   ```bash
   ng serve
   ```

2. **Probar la aplicación**:
   - Ve a `http://localhost:4200`
   - Navega a la página de bindeos
   - Prueba cada tipo de binding:
     * Observa la interpolación
     * Interactúa con los botones
     * Escribe en el input
     * Observa los cambios de estilo

## Parte 4: Componentes de Error y Login 🔐

### Paso 1: Crear los Componentes

1. **Generar componente de error**:
   ```bash
   ng generate component components/error --standalone
   ```

2. **Generar componente de login**:
   ```bash
   ng generate component components/login --standalone
   ng generate component components/login
   ```

### Paso 2: Implementar Componente de Error

1. **Editar error.component.ts**:
   ```typescript
   import { Component } from '@angular/core';
   import { RouterModule } from '@angular/router';

   @Component({
     selector: 'app-error',
     standalone: true,
     imports: [RouterModule],
     template: `
       <div class="error-container">
         <div class="error-content">
           <h1>😖 404: Página No Encontrada</h1>
           
           <p>Lo sentimos, la página que buscas no existe.</p>
           
           <div class="acciones">
             <a routerLink="/" class="boton-primario">
               🏠 Volver al Inicio
             </a>
           </div>
         </div>
       </div>
     `,
     styles: [`
       .error-container {
         height: 100vh;
         display: flex;
         align-items: center;
         justify-content: center;
         background: #f8f9fa;
       }

       .error-content {
         text-align: center;
         padding: 2rem;
         background: white;
         border-radius: 8px;
         box-shadow: 0 2px 4px rgba(0,0,0,0.1);
         max-width: 400px;
         width: 90%;

         h1 {
           color: #7b1fa2;
           margin-bottom: 1rem;
         }

         p {
           color: #666;
           margin-bottom: 2rem;
         }
       }

       .acciones {
         margin-top: 2rem;
       }

       .boton-primario {
         display: inline-block;
         padding: 0.8rem 1.5rem;
         background: #7b1fa2;
         color: white;
         text-decoration: none;
         border-radius: 4px;
         transition: all 0.3s ease;

         &:hover {
           background: #6a1b9a;
           transform: translateY(-1px);
         }
       }
     `]
   })
   export class ErrorComponent {}
   ```

### Paso 3: Implementar Componente de Login

1. **Editar login.component.ts**:
   ```typescript
   import { Component, signal } from '@angular/core';
   import { CommonModule } from '@angular/common';
   import { FormsModule } from '@angular/forms';
   import { Router } from '@angular/router';

   @Component({
     selector: 'app-login',
     standalone: true,
     imports: [CommonModule, FormsModule],
     template: `
       <div class="login-container">
         <form class="login-form" (ngSubmit)="iniciarSesion()">
           <h2>🔐 Iniciar Sesión</h2>

           <!-- Mensaje de error -->
           @if (error()) {
             <div class="error-mensaje">
               {{error()}}
             </div>
           }

           <!-- Campo de email -->
           <div class="form-grupo">
             <label for="email">Email</label>
             <input type="email"
                    id="email"
                    [(ngModel)]="email"
                    name="email"
                    required
                    placeholder="ejemplo@mail.com">
           </div>

           <!-- Campo de contraseña -->
           <div class="form-grupo">
             <label for="password">Contraseña</label>
             <input type="password"
                    id="password"
                    [(ngModel)]="password"
                    name="password"
                    required
                    placeholder="Tu contraseña">
           </div>

           <!-- Botón de submit -->
           <button type="submit"
                   [disabled]="cargando()"
                   class="boton-login">
             @if (cargando()) {
               Iniciando sesión...
             } @else {
               Iniciar Sesión
             }
           </button>
         </form>
       </div>
     `,
     styles: [`
       .login-container {
         height: 100vh;
         display: flex;
         align-items: center;
         justify-content: center;
         background: #f8f9fa;
       }

       .login-form {
         background: white;
         padding: 2rem;
         border-radius: 8px;
         box-shadow: 0 2px 4px rgba(0,0,0,0.1);
         width: 90%;
         max-width: 400px;

         h2 {
           text-align: center;
           color: #7b1fa2;
           margin-bottom: 2rem;
         }
       }

       .form-grupo {
         margin-bottom: 1.5rem;

         label {
           display: block;
           margin-bottom: 0.5rem;
           color: #666;
         }

         input {
           width: 100%;
           padding: 0.8rem;
           border: 2px solid #ddd;
           border-radius: 4px;
           font-size: 1rem;

           &:focus {
             outline: none;
             border-color: #7b1fa2;
           }
         }
       }

       .boton-login {
         width: 100%;
         padding: 1rem;
         background: #7b1fa2;
         color: white;
         border: none;
         border-radius: 4px;
         font-size: 1rem;
         cursor: pointer;
         transition: all 0.3s ease;

         &:hover {
           background: #6a1b9a;
         }

         &:disabled {
           background: #ddd;
           cursor: not-allowed;
         }
       }

       .error-mensaje {
         background: #ffebee;
         color: #c62828;
         padding: 1rem;
         border-radius: 4px;
         margin-bottom: 1rem;
         text-align: center;
       }
     `]
   })
   export class LoginComponent {
     // Estado del formulario
     email = '';
     password = '';

     // Signals para el estado
     cargando = signal(false);
     error = signal('');

     constructor(private router: Router) {}

     async iniciarSesion() {
       // Validación básica
       if (!this.email || !this.password) {
         this.error.set('Por favor completa todos los campos');
         return;
       }

       // Simular carga
       this.cargando.set(true);
       this.error.set('');

       try {
         // Simular llamada al servidor
         await new Promise(resolve => setTimeout(resolve, 1500));

         // Validación simple (SOLO PARA DEMO)
         if (this.email === 'demo@angular.dev' && this.password === '123456') {
           // Login exitoso
           this.router.navigate(['/']);
         } else {
           // Login fallido
           this.error.set('Credenciales incorrectas');
         }
       } finally {
         this.cargando.set(false);
       }
     }
   }
   ```

### Paso 4: Actualizar las Rutas

1. **Actualizar app.routes.ts**:
   ```typescript
   import { Routes } from '@angular/router';
   import { ErrorComponent } from './components/error/error.component';

   export const routes: Routes = [
     {
       path: '',
       loadComponent: () => 
         import('./components/bienvenida/bienvenida.component')
         .then(m => m.BienvenidaComponent)
     },
     {
       path: 'bindeos',
       loadComponent: () => 
         import('./components/bindeos/bindeos.component')
         .then(m => m.BindeosComponent)
     },
     {
       path: 'login',
       loadComponent: () => 
         import('./components/login/login.component')
         .then(m => m.LoginComponent)
     },
     {
       path: '**',
       component: ErrorComponent
     }
   ];
   ```

### Paso 5: Actualizar la Navegación

1. **Actualizar app.component.ts** (agregar link de login):
   ```typescript
   // ... imports previos ...

   template: `
     <nav class="navegacion">
       <a routerLink="/" routerLinkActive="activo"
          [routerLinkActiveOptions]="{exact: true}">
         🏠 Inicio
       </a>
       <a routerLink="/bindeos" routerLinkActive="activo">
         🔗 Bindeos
       </a>
       <a routerLink="/login" routerLinkActive="activo">
         🔐 Login
       </a>
     </nav>

     <main>
       <router-outlet/>
     </main>
   `
   ```

### Paso 6: Probar los Componentes

1. **Verificar que el servidor esté corriendo**:
   ```bash
   ng serve
   ```

2. **Probar la aplicación**:
   - Ve a `http://localhost:4200`
   - Prueba la navegación a todas las rutas
   - Prueba el formulario de login:
     * Email: demo@angular.dev
     * Password: 123456
   - Prueba una ruta que no existe para ver el error 404

¡Felicitaciones! 🎉 Has completado la creación de una aplicación Angular 19.2.5 con:
- Componentes standalone
- Signals y computed values
- Routing moderno
- Formularios y validación
- Manejo de errores
- Estilos modernos con SCSS

Recuerda que puedes personalizar los estilos y agregar más funcionalidades según tus necesidades.
