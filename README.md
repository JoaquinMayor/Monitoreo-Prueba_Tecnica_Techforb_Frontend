# MonitoreoPruebaTecnicaTechfordFrontend

## Prueba técnica de FrontEnd Angular de sistema de plantas y alertas.
### Solicitante TechFord.

Aplicación web para el **monitoreo de plantas industriales**: muestra, por planta y de forma global, la cantidad de lecturas de sus sensores clasificadas por nivel de alerta (OK, medias y rojas), la cantidad de sensores deshabilitados y el detalle por tipo de lectura (temperatura, presión, viento, niveles, energía, tensión, monóxido de carbono y otros gases). Permite además crear, editar y eliminar plantas.

Este proyecto fue generado con  [Angular CLI](https://github.com/angular/angular-cli) version 17.1.0.

## Tecnologías

- **Angular 17** con componentes *standalone* y el nuevo control flow
- **TypeScript 5.3**
- **SCSS** para los estilos
- **Reactive Forms** (`FormGroup`, `FormControl`, `Validators`)
- **HttpClient** + **RxJS** (`Observable`, `BehaviorSubject`)
- **Angular SSR** (`@angular/ssr` + Express) con hidratación del cliente
- API externa [REST Countries](https://restcountries.com/) para países y banderas
- Karma + Jasmine para pruebas unitarias

## Requisitos

- [Node.js](https://nodejs.org/) 18 o superior y npm
- Angular CLI 17: `npm install -g @angular/cli@17`

Instalar las dependencias:

```bash
npm install
```

## Development server

Se corre con  `ng serve` para el server de desarrollo.Navegando a `http://localhost:4200/`. 

> La URL del backend se configura en `src/app/services/constantes.ts` (`API_URL`). Por defecto apunta al backend desplegado en Render (`https://monitoreo-prueba-tecnica-techforb.onrender.com`).

## Build

- `ng build`: compila el proyecto en `dist/`.
- `npm run watch`: compila en modo desarrollo observando cambios.
- `npm run serve:ssr:monitoreo-prueba-tecnica-techford-frontend`: sirve la versión SSR compilada.

## Running unit tests

Se corre con `ng test`para ejecutarlo mediante [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Se corre con  `ng e2e`para correr el  end-to-end testsde la plataforma que uno elija. 

# Secciones
Esta aplicación cuenta con 3 secciones principales, el Login, Registro y el Dashboard.
Dentro de esta aplicación podremos crearnos un usuario, cuya contraseñia se encoentrará encriptada, para luego iniciar sensión y poder acceder  a la información de las plantas.
Toda la información se encuentra almacenada en una base de datos personal, generada con un back-end propio ([Link backend](https://github.com/JoaquinMayor/monitoreo-prueba-tecnica-techford)).
El manejo de los paises y sus banderas se maneja mediante una api externade [Restcountries](https://restcountries.com/v3.1/all)

| Ruta | Componente | Acceso |
|---|---|---|
| `/` | `LoginComponent` | Público |
| `/registrarse` | `RegistroComponent` | Público |
| `/dashboard` | `DashboardComponent` | Protegida por `loginGuardGuard` |

## Funcionalidades

- **Registro**: nombre, apellido, email y contraseña (mínimo 9 caracteres). Valida el formato del email y consulta a la API si el email ya está registrado.
- **Login**: con email y contraseña; guarda el token JWT y los datos del usuario y redirige al dashboard. Muestra un mensaje si las credenciales son incorrectas. Botón para mostrar/ocultar la contraseña.
- **Guardián de rutas**: impide acceder al dashboard sin estar logueado y redirige al login.
- **Dashboard**:
  - **Barra de navegación**: muestra dinámicamente el nombre y apellido del usuario logueado.
  - **Menú lateral**: opción para cerrar sesión.
  - **Resumen global**: total de lecturas OK, alertas medias, alertas rojas y sensores deshabilitados entre todas las plantas.
  - **Detalle por tipo de lectura**: cantidad de lecturas OK, medias y rojas para cada uno de los 8 tipos de lectura.
  - **Tabla de plantas**: país con su bandera, nombre y cantidad de lecturas OK, medias y rojas de cada planta, ordenadas por país.
  - **Crear planta**: formulario con nombre y país (lista de países de REST Countries ordenada alfabéticamente).
  - **Editar planta**: cambia nombre y país, y regenera las lecturas de la planta indicando la cantidad total y cuántas son OK, medias y rojas (valida que la suma de los tipos coincida con el total).
  - **Eliminar planta**.
  - Todos los paneles se **actualizan automáticamente** después de crear, editar o eliminar una planta.

> Según los comentarios del código, los demás botones de la barra de navegación y del menú lateral no tienen funcionalidad porque la consigna no lo especificaba.

## Carpetas
Dentro  dle proyecto podremos observar los distintos **componentes utilizados**, como así la carpeta **Model** con las clases creadas y la carpeta **Services**, donde encontraremos el consumo de las apis.

```
src/app/
├── login/            # Inicio de sesión
├── registro/         # Registro de usuario
├── dashboard/        # Contenedor que une nav-bar, sidenav, global, plantas y lecturas
├── nav-bar/          # Barra superior con el nombre del usuario
├── sidenav/          # Menú lateral (cerrar sesión)
├── global/           # Resumen global de lecturas y sensores deshabilitados
├── lecturas/         # Lecturas por tipo y nivel de alerta
├── plantas/          # Tabla de plantas con opciones de editar y eliminar
├── crear-planta/     # Ventana de creación de planta
├── editar-planta/    # Ventana de edición de planta
├── guards/           # loginGuardGuard (CanActivateFn)
├── models/
│   ├── informacion/  # Planta, Pais, Lectura, TipoLectura, Alerta, Sensor
│   └── perfil/       # Usuario, Rol
└── services/
    ├── UsuarioService.service.ts         # Registro, login, token, datos del usuario y logout
    ├── PlantasService.service.ts         # CRUD de plantas, conteo y actualización de lecturas por planta
    ├── LecturaService.service.ts         # Conteos globales de lecturas y por tipo/alerta
    ├── SensoresService.service.ts        # Cantidad de sensores deshabilitados
    ├── PaisesService.service.ts          # Consumo de la API REST Countries
    ├── DatosCompartidosService.service.ts # Aviso entre componentes para refrescar datos
    └── constantes.ts                     # URL del backend (API_URL)
```

## Conceptos aplicados

- Componentes standalone y configuración con `ApplicationConfig` (`provideRouter`, `provideHttpClient`, `provideClientHydration`).
- Formularios reactivos con validaciones y mensajes de error.
- Autenticación con token JWT enviado en el header `Authorization: Bearer`, persistiendo el estado de sesión en `localStorage`.
- Guardián funcional de rutas (`CanActivateFn`) con `inject()`.
- Servicios singleton (`providedIn: 'root'`) para el consumo de la API REST propia y de una API externa.
- Comunicación entre componentes padre e hijo con `@Input()` y `@Output()` + `EventEmitter` (ventanas de crear y editar planta).
- Comunicación entre componentes no relacionados mediante un servicio con `BehaviorSubject` para refrescar la información.
- Uso de `HttpParams` para enviar parámetros de consulta.
- Modelos de dominio con atributos privados, getters y setters.
