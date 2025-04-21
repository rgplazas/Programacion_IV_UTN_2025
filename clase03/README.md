# Guía Completa Angular 19.2.5 - Clase03

## Requisitos Previos

Antes de comenzar, asegúrate de tener instalado:

1. **Node.js** (versión 18.x o superior)
2. **npm** (versión 10.x o superior)
3. **Angular CLI** (versión 19.2.5)

```bash
npm install -g @angular/cli@19.2.5
```

## Formularios Reactivos

Los formularios reactivos en Angular 19.2.5 ofrecen un enfoque basado en modelos para manejar entradas de formulario.

### Configuración Inicial

1. **Importar ReactiveFormsModule en app.module.ts:**
```typescript
import { ReactiveFormsModule } from '@angular/forms';

@NgModule({
  imports: [
    ReactiveFormsModule
  ]
})
export class AppModule { }
```

### Crear un Formulario Reactivo

```typescript
// pages/registro/registro.component.ts
import { Component } from '@angular/core';
import { FormGroup, FormControl, Validators } from '@angular/forms';

@Component({
  selector: 'app-registro',
  templateUrl: './registro.component.html'
})
export class RegistroComponent {
  registroForm = new FormGroup({
    nombre: new FormControl('', [Validators.required, Validators.minLength(3)]),
    email: new FormControl('', [Validators.required, Validators.email]),
    password: new FormControl('', [Validators.required, Validators.minLength(6)])
  });

  onSubmit() {
    if (this.registroForm.valid) {
      console.log(this.registroForm.value);
    }
  }
}
```

```html
<!-- registro.component.html -->
<form [formGroup]="registroForm" (ngSubmit)="onSubmit()">
  <div>
    <label for="nombre">Nombre:</label>
    <input id="nombre" type="text" formControlName="nombre">
    
    @if (registroForm.get('nombre')?.errors?.['required'] && 
         registroForm.get('nombre')?.touched) {
      <span class="error">El nombre es requerido</span>
    }
  </div>

  <div>
    <label for="email">Email:</label>
    <input id="email" type="email" formControlName="email">
    
    @if (registroForm.get('email')?.errors?.['email']) {
      <span class="error">Email inválido</span>
    }
  </div>

  <div>
    <label for="password">Contraseña:</label>
    <input id="password" type="password" formControlName="password">
    
    @if (registroForm.get('password')?.errors?.['minlength']) {
      <span class="error">La contraseña debe tener al menos 6 caracteres</span>
    }
  </div>

  <button type="submit" [disabled]="!registroForm.valid">Registrar</button>
</form>
```

### Buenas Prácticas

1. **Tipado de Formularios**
```typescript
interface RegistroForm {
  nombre: string;
  email: string;
  password: string;
}

registroForm = new FormGroup<RegistroForm>({
  // ...
});
```

2. **Extracción de Validadores**
```typescript
const passwordValidators = [
  Validators.required,
  Validators.minLength(6),
  Validators.pattern(/^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d]{6,}$/)
];
```

3. **Manejo de Estados**
```typescript
get nombreInvalido(): boolean {
  const control = this.registroForm.get('nombre');
  return control ? control.invalid && control.touched : false;
}
```

## Validación de Formularios

### Validadores Personalizados

```typescript
// validators/custom.validators.ts
import { AbstractControl, ValidationErrors } from '@angular/forms';

export class CustomValidators {
  static passwordMatch(control: AbstractControl): ValidationErrors | null {
    const password = control.get('password');
    const confirmPassword = control.get('confirmPassword');

    if (password?.value !== confirmPassword?.value) {
      return { passwordMismatch: true };
    }

    return null;
  }

  static noEspacios(control: AbstractControl): ValidationErrors | null {
    if (control.value && control.value.includes(' ')) {
      return { espacios: true };
    }
    return null;
  }
}
```

### Validación Asíncrona

```typescript
// services/validacion.service.ts
import { Injectable } from '@angular/core';
import { AbstractControl, AsyncValidatorFn } from '@angular/forms';
import { Observable, of } from 'rxjs';
import { map, delay } from 'rxjs/operators';

@Injectable({
  providedIn: 'root'
})
export class ValidacionService {
  emailExiste(email: string): Observable<boolean> {
    // Simular llamada a API
    return of(email === 'test@test.com').pipe(delay(1000));
  }

  emailUnicoValidator(): AsyncValidatorFn {
    return (control: AbstractControl): Observable<ValidationErrors | null> => {
      return this.emailExiste(control.value).pipe(
        map(existe => existe ? { emailExiste: true } : null)
      );
    };
  }
}
```

## Servicios e Inyección de Dependencias

### Creación de Servicios

```typescript
// services/usuario.service.ts
import { Injectable } from '@angular/core';
import { BehaviorSubject, Observable } from 'rxjs';

export interface Usuario {
  id: number;
  nombre: string;
  email: string;
}

@Injectable({
  providedIn: 'root'
})
export class UsuarioService {
  private usuarios = new BehaviorSubject<Usuario[]>([]);

  getUsuarios(): Observable<Usuario[]> {
    return this.usuarios.asObservable();
  }

  agregarUsuario(usuario: Usuario): void {
    const usuariosActuales = this.usuarios.value;
    this.usuarios.next([...usuariosActuales, usuario]);
  }

  eliminarUsuario(id: number): void {
    const usuariosActuales = this.usuarios.value;
    this.usuarios.next(usuariosActuales.filter(u => u.id !== id));
  }
}
```

### Uso del Servicio en Componentes

```typescript
// pages/usuarios/usuarios.component.ts
import { Component, OnInit } from '@angular/core';
import { UsuarioService, Usuario } from '../../services/usuario.service';

@Component({
  selector: 'app-usuarios',
  template: `
    @for (usuario of usuarios$ | async; track usuario.id) {
      <div class="usuario-card">
        <h3>{{ usuario.nombre }}</h3>
        <p>{{ usuario.email }}</p>
        <button (click)="eliminarUsuario(usuario.id)">Eliminar</button>
      </div>
    }
  `
})
export class UsuariosComponent implements OnInit {
  usuarios$ = this.usuarioService.getUsuarios();

  constructor(private usuarioService: UsuarioService) {}

  ngOnInit() {
    // Cargar usuarios iniciales si es necesario
  }

  eliminarUsuario(id: number) {
    this.usuarioService.eliminarUsuario(id);
  }
}
```

## HTTP Client y Comunicación con APIs

### Configuración Inicial

```typescript
// app.module.ts
import { HttpClientModule } from '@angular/common/http';

@NgModule({
  imports: [
    HttpClientModule
  ]
})
export class AppModule { }
```

### Implementación de Servicios HTTP

```typescript
// services/api.service.ts
import { Injectable } from '@angular/core';
import { HttpClient, HttpHeaders } from '@angular/common/http';
import { Observable, catchError, throwError } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class ApiService {
  private apiUrl = 'https://api.ejemplo.com';

  constructor(private http: HttpClient) {}

  private headers = new HttpHeaders({
    'Content-Type': 'application/json'
  });

  getRecursos(): Observable<any[]> {
    return this.http.get<any[]>(`${this.apiUrl}/recursos`)
      .pipe(
        catchError(this.manejarError)
      );
  }

  crearRecurso(data: any): Observable<any> {
    return this.http.post(`${this.apiUrl}/recursos`, data, { headers: this.headers })
      .pipe(
        catchError(this.manejarError)
      );
  }

  private manejarError(error: any) {
    console.error('Error en la API:', error);
    return throwError(() => new Error('Algo salió mal'));
  }
}
```

## Ejemplos paso a paso

### Estructura de Carpetas

```
clase03/
├── pages/
│   ├── registro/
│   │   ├── registro.component.ts
│   │   ├── registro.component.html
│   │   └── registro.component.scss
│   └── usuarios/
│       ├── usuarios.component.ts
│       ├── usuarios.component.html
│       └── usuarios.component.scss
├── services/
│   ├── api.service.ts
│   ├── usuario.service.ts
│   └── validacion.service.ts
└── validators/
    └── custom.validators.ts
```

### Implementación Completa

1. **Crear Servicios**
```bash
ng generate service services/api
ng generate service services/usuario
ng generate service services/validacion
```

2. **Crear Componentes**
```bash
ng generate component pages/registro
ng generate component pages/usuarios
```

3. **Crear Validadores**
```bash
ng generate class validators/custom-validators
```

### Integración de Componentes

```typescript
// pages/registro/registro.component.ts
import { Component } from '@angular/core';
import { FormGroup, FormControl, Validators } from '@angular/forms';
import { UsuarioService } from '../../services/usuario.service';
import { ValidacionService } from '../../services/validacion.service';
import { CustomValidators } from '../../validators/custom.validators';

@Component({
  selector: 'app-registro',
  templateUrl: './registro.component.html'
})
export class RegistroComponent {
  registroForm = new FormGroup({
    nombre: new FormControl('', [
      Validators.required,
      Validators.minLength(3),
      CustomValidators.noEspacios
    ]),
    email: new FormControl('', {
      validators: [Validators.required, Validators.email],
      asyncValidators: [this.validacionService.emailUnicoValidator()],
      updateOn: 'blur'
    }),
    password: new FormControl('', [
      Validators.required,
      Validators.minLength(6)
    ])
  });

  constructor(
    private usuarioService: UsuarioService,
    private validacionService: ValidacionService
  ) {}

  onSubmit() {
    if (this.registroForm.valid) {
      this.usuarioService.agregarUsuario({
        id: Date.now(),
        ...this.registroForm.value
      });
    }
  }
}
```

### Buenas Prácticas

1. **Manejo de Errores HTTP**
```typescript
import { HttpErrorResponse } from '@angular/common/http';

private manejarError(error: HttpErrorResponse) {
  if (error.error instanceof ErrorEvent) {
    console.error('Error del cliente:', error.error.message);
  } else {
    console.error(`Error del servidor: ${error.status}, mensaje: ${error.error}`);
  }
  return throwError(() => new Error('Por favor, intente nuevamente más tarde'));
}
```

2. **Interceptores HTTP**
```typescript
// interceptors/auth.interceptor.ts
import { Injectable } from '@angular/core';
import { HttpInterceptor, HttpRequest, HttpHandler } from '@angular/common/http';

@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler) {
    const token = localStorage.getItem('token');
    
    if (token) {
      const cloned = req.clone({
        headers: req.headers.set('Authorization', `Bearer ${token}`)
      });
      return next.handle(cloned);
    }
    
    return next.handle(req);
  }
}
```

3. **Caché de Datos**
```typescript
import { shareReplay } from 'rxjs/operators';

getUsuarios(): Observable<Usuario[]> {
  if (!this.usuarios$) {
    this.usuarios$ = this.http.get<Usuario[]>('/api/usuarios')
      .pipe(
        shareReplay(1)
      );
  }
  return this.usuarios$;
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
