# Proyecto Enterprise Fleet Manager

Guía paso a paso para el desarrollo de la aplicación web **Enterprise Fleet Manager** usando Angular, Angular Material, json-server y ngx-translate, siguiendo el patrón del curso DAOS (1ASI0729).

> Reemplaza `<nrc>` con el NRC de tu sección y `<codigo>` con tu código de estudiante en minúsculas.
> Ejemplo: `ea7377u20241a972`

---

## Creación del proyecto

> **En equipos MAC:** antecede `sudo` a los comandos `ng` e ingresa la contraseña del administrador (`d3v3l0p3rUPC`).
>
> **En equipos Windows:** ubícate en la carpeta `IdeaProjects/` o una de tu preferencia.

### Crear el workspace y la aplicación inicial

Abre el terminal y ejecuta:

```
ng new ea<nrc>u<codigo>
```

Cuando el CLI muestre las opciones, selecciona:

*? Which stylesheet format would you like to use?*, seleccionar:

```
SCSS
```

*? Do you want to enable Server-Side Rendering (SSR) and Static Site Generation (SSG/Prerendering)?*, digitar:

```
N
```

### Instalación de Angular Material

Ingresar a la carpeta creada con el mismo nombre que el proyecto ejecutando:

```
cd ea<nrc>u<codigo>
```

Agregar Angular Material a la aplicación:

```
ng add @angular/material
```

Cuando pregunte:

- *Would you like to proceed? (Y/n)*, digitar: `Y`
- *Select a pair of starter prebuilt color palettes*, seleccionar:

```
Rose/Red
```

> Enterprise Rent-A-Car usa rojo como color principal. Esta paleta es la más cercana.

### Instalación de ngx-translate (i18n)

```
npm install @ngx-translate/core @ngx-translate/http-loader --save
```

### Instalación de json-server

```
npm install -g json-server@0.17.4
```

---

## Desarrollo del proyecto

Cargar IntelliJ IDEA y abrir el proyecto. Cargar el Terminal del IDE y ejecutar:

```
ng serve --port 4200
```

---

## Creación de los archivos de idioma

Crear las carpetas `assets` e `i18n` en la carpeta `public` ubicada en la raíz del proyecto:

```
📂 public
  📂 assets
    📂 i18n
```

Crear los archivos `en.json` y `es.json` en la carpeta `i18n` con el siguiente contenido:

### en.json

```json
{
  "toolbar": {
    "title": "Enterprise Fleet Manager",
    "home": "Home",
    "newRental": "New Rental"
  },
  "home": {
    "title": "Home",
    "welcome": "Welcome to Enterprise Rent-A-Car",
    "fleetUtilization": "Fleet Utilization Analytics",
    "nextUrgentIncident": "Next Urgent Incident"
  },
  "vehicleTypeStats": {
    "dailyRevenuePotential": "Daily Revenue Potential",
    "estimatedIncidentCost": "Estimated Incident Cost",
    "vehiclesRented": "Vehicles Rented"
  },
  "newRental": {
    "title": "New Rental",
    "subtitle": "Create a New Rental Contract",
    "vehicleId": "Vehicle",
    "clientId": "Client ID",
    "durationDays": "Duration (days)",
    "create": "Create",
    "cancel": "Cancel"
  },
  "notFound": {
    "message": "The route was not found:",
    "back": "Go to Home"
  }
}
```

### es.json

```json
{
  "toolbar": {
    "title": "Enterprise Fleet Manager",
    "home": "Inicio",
    "newRental": "Nuevo Alquiler"
  },
  "home": {
    "title": "Inicio",
    "welcome": "Bienvenido a Enterprise Rent-A-Car",
    "fleetUtilization": "Análisis de Utilización de Flota",
    "nextUrgentIncident": "Próximo Incidente Urgente"
  },
  "vehicleTypeStats": {
    "dailyRevenuePotential": "Potencial de Ingresos Diarios",
    "estimatedIncidentCost": "Costo Estimado de Incidentes",
    "vehiclesRented": "Vehículos Alquilados"
  },
  "newRental": {
    "title": "Nuevo Alquiler",
    "subtitle": "Crear un Nuevo Contrato de Alquiler",
    "vehicleId": "Vehículo",
    "clientId": "ID de Cliente",
    "durationDays": "Duración (días)",
    "create": "Crear",
    "cancel": "Cancelar"
  },
  "notFound": {
    "message": "La ruta no fue encontrada:",
    "back": "Ir al Inicio"
  }
}
```

---

## Configuración del json-server

Crear la carpeta `server` en la raíz del proyecto y copiar dentro el archivo `db.json` proporcionado en el examen:

```
📂 ea<nrc>u<codigo>
  📂 server
    db.json
```

Cargar el Terminal del IDE y agregar un nuevo Tab. Ejecutar el siguiente comando para iniciar el json-server:

```
cd server
json-server --watch db.json
```

Verificar que los endpoints funcionen en el navegador:

- http://localhost:3000/vehicles
- http://localhost:3000/rentals
- http://localhost:3000/incidents

---

## Configuración de environments

Cargar el Terminal del IDE y ejecutar:

```
ng generate environments
```

El archivo `environment.development.ts` ubicado en `src/environments` debe quedar así:

```typescript
export const environment = {
  production: false,
  serverBasePath: 'http://localhost:3000',
  vehiclesEndpointPath: '/vehicles',
  rentalsEndpointPath: '/rentals',
  incidentsEndpointPath: '/incidents'
};
```

El archivo `environment.ts` ubicado en `src/environments` debe quedar así:

```typescript
export const environment = {
  production: true,
  serverBasePath: 'http://localhost:3000',
  vehiclesEndpointPath: '/vehicles',
  rentalsEndpointPath: '/rentals',
  incidentsEndpointPath: '/incidents'
};
```

---

## Configuración del appConfig

Reemplazar el contenido del archivo `app.config.ts` ubicado en `src/app`:

```typescript
import { ApplicationConfig, provideAppInitializer, inject } from '@angular/core';
import { provideRouter } from '@angular/router';
import { provideHttpClient } from '@angular/common/http';
import { provideAnimationsAsync } from '@angular/platform-browser/animations/async';
import { provideTranslateService, TranslateService } from '@ngx-translate/core';
import { provideTranslateHttpLoader } from '@ngx-translate/http-loader';
import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    provideAnimationsAsync(),
    provideHttpClient(),
    provideTranslateService({
      loader: provideTranslateHttpLoader({ prefix: './assets/i18n/', suffix: '.json' }),
      lang: 'en',
      fallbackLang: 'en'
    }),
    provideAppInitializer(() => {
      const translate = inject(TranslateService);
      translate.use(translate.getBrowserLang() || 'en');
    })
  ]
};
```

---

## Creación de la estructura del proyecto

Crear la siguiente estructura de carpetas en `src/app`:

```
📂 src
  📂 app
    📂 masters
      📂 application
      📂 domain
        📂 model
      📂 infrastructure
      📂 presentation
        📂 components
        📂 views
    📂 operations
      📂 application
      📂 domain
        📂 model
      📂 infrastructure
      📂 presentation
        📂 components
        📂 views
    📂 shared
      📂 infrastructure
      📂 presentation
        📂 components
        📂 views
```

---

## Domain Layer — Modelos (entities)

### Creación del modelo Vehicle

Cargar el Terminal del IDE y ejecutar:

```
ng generate class masters/domain/model/vehicle --type=entity --skip-tests=true
```

Reemplazar el contenido del archivo `vehicle.entity.ts` ubicado en `src/app/masters/domain/model`:

```typescript
/**
 * @summary Vehicle entity representing a fleet unit in the masters bounded context.
 * @author Elynor Palma
 */
export class Vehicle {
  id: number;
  make: string;
  model: string;
  mileageKm: number;
  dailyRate: number;
  vehicleType: string;
  status: string;

  constructor() {
    this.id = 0;
    this.make = '';
    this.model = '';
    this.mileageKm = 0;
    this.dailyRate = 0;
    this.vehicleType = '';
    this.status = '';
  }
}
```

### Creación del modelo Rental

Ejecutar:

```
ng generate class operations/domain/model/rental --type=entity --skip-tests=true
```

Reemplazar el contenido del archivo `rental.entity.ts` ubicado en `src/app/operations/domain/model`:

```typescript
/**
 * @summary Rental entity representing a vehicle rental contract in the operations bounded context.
 * @author Elynor Palma
 */
export class Rental {
  id: number;
  vehicleId: number;
  clientId: number;
  startDate: string;
  endDate: string;
  durationDays: number;
  totalCost: number;
  status: string;

  constructor() {
    this.id = 0;
    this.vehicleId = 0;
    this.clientId = 0;
    this.startDate = '';
    this.endDate = '';
    this.durationDays = 0;
    this.totalCost = 0;
    this.status = '';
  }
}
```

### Creación del modelo Incident

Ejecutar:

```
ng generate class operations/domain/model/incident --type=entity --skip-tests=true
```

Reemplazar el contenido del archivo `incident.entity.ts` ubicado en `src/app/operations/domain/model`:

```typescript
/**
 * @summary Incident entity representing a vehicle incident or maintenance event in the operations bounded context.
 * @author Elynor Palma
 */
export class Incident {
  id: number;
  vehicleId: number;
  rentalId: number | null;
  incidentType: string;
  registeredAt: string;
  estimatedRepairCost: number;
  priority: string;

  constructor() {
    this.id = 0;
    this.vehicleId = 0;
    this.rentalId = null;
    this.incidentType = '';
    this.registeredAt = '';
    this.estimatedRepairCost = 0;
    this.priority = '';
  }
}
```

---

## Infrastructure Layer — Response interfaces

### Creación de VehicleResponse

Ejecutar:

```
ng generate interface masters/infrastructure/vehicle-response
```

Reemplazar el contenido del archivo `vehicle-response.ts` ubicado en `src/app/masters/infrastructure`:

```typescript
/**
 * @summary Vehicle response interface for REST API deserialization.
 * @author Elynor Palma
 */
export interface VehicleResponse {
  id: number;
  make: string;
  model: string;
  mileageKm: number;
  dailyRate: number;
  vehicleType: string;
  status: string;
}
```

### Creación de RentalResponse

Ejecutar:

```
ng generate interface operations/infrastructure/rental-response
```

Reemplazar el contenido del archivo `rental-response.ts` ubicado en `src/app/operations/infrastructure`:

```typescript
/**
 * @summary Rental response interface for REST API deserialization.
 * @author Elynor Palma
 */
export interface RentalResponse {
  id: number;
  vehicleId: number;
  clientId: number;
  startDate: string;
  endDate: string;
  durationDays: number;
  totalCost: number;
  status: string;
}
```

### Creación de IncidentResponse

Ejecutar:

```
ng generate interface operations/infrastructure/incident-response
```

Reemplazar el contenido del archivo `incident-response.ts` ubicado en `src/app/operations/infrastructure`:

```typescript
/**
 * @summary Incident response interface for REST API deserialization.
 * @author Elynor Palma
 */
export interface IncidentResponse {
  id: number;
  vehicleId: number;
  rentalId: number | null;
  incidentType: string;
  registeredAt: string;
  estimatedRepairCost: number;
  priority: string;
}
```

---

## Infrastructure Layer — Assemblers

### Creación de VehicleAssembler

Ejecutar:

```
ng generate class masters/infrastructure/vehicle-assembler --skip-tests=true
```

Agregar los siguientes imports al archivo `vehicle-assembler.ts` ubicado en `src/app/masters/infrastructure`:

```typescript
import { VehicleResponse } from './vehicle-response';
import { Vehicle } from '../domain/model/vehicle.entity';
```

Reemplazar el contenido de la clase `VehicleAssembler` con el siguiente código:

```typescript
/**
 * @summary Assembler for mapping VehicleResponse to Vehicle entity.
 * @author Elynor Palma
 */
static toEntityFromResponseArray(responseArray: VehicleResponse[]): Vehicle[] {
  return responseArray.map((response) => this.toEntityFromResponse(response));
}

static toEntityFromResponse(response: VehicleResponse): Vehicle {
  const vehicle = new Vehicle();
  vehicle.id = response.id;
  vehicle.make = response.make;
  vehicle.model = response.model;
  vehicle.mileageKm = response.mileageKm;
  vehicle.dailyRate = response.dailyRate;
  vehicle.vehicleType = response.vehicleType;
  vehicle.status = response.status;
  return vehicle;
}
```

### Creación de RentalAssembler

Ejecutar:

```
ng generate class operations/infrastructure/rental-assembler --skip-tests=true
```

Agregar los siguientes imports al archivo `rental-assembler.ts` ubicado en `src/app/operations/infrastructure`:

```typescript
import { RentalResponse } from './rental-response';
import { Rental } from '../domain/model/rental.entity';
```

Reemplazar el contenido de la clase `RentalAssembler` con el siguiente código:

```typescript
/**
 * @summary Assembler for mapping RentalResponse to Rental entity.
 * @author Elynor Palma
 */
static toEntityFromResponseArray(responseArray: RentalResponse[]): Rental[] {
  return responseArray.map((response) => this.toEntityFromResponse(response));
}

static toEntityFromResponse(response: RentalResponse): Rental {
  const rental = new Rental();
  rental.id = response.id;
  rental.vehicleId = response.vehicleId;
  rental.clientId = response.clientId;
  rental.startDate = response.startDate;
  rental.endDate = response.endDate;
  rental.durationDays = response.durationDays;
  rental.totalCost = response.totalCost;
  rental.status = response.status;
  return rental;
}
```

### Creación de IncidentAssembler

Ejecutar:

```
ng generate class operations/infrastructure/incident-assembler --skip-tests=true
```

Agregar los siguientes imports al archivo `incident-assembler.ts` ubicado en `src/app/operations/infrastructure`:

```typescript
import { IncidentResponse } from './incident-response';
import { Incident } from '../domain/model/incident.entity';
```

Reemplazar el contenido de la clase `IncidentAssembler` con el siguiente código:

```typescript
/**
 * @summary Assembler for mapping IncidentResponse to Incident entity.
 * @author Elynor Palma
 */
static toEntityFromResponseArray(responseArray: IncidentResponse[]): Incident[] {
  return responseArray.map((response) => this.toEntityFromResponse(response));
}

static toEntityFromResponse(response: IncidentResponse): Incident {
  const incident = new Incident();
  incident.id = response.id;
  incident.vehicleId = response.vehicleId;
  incident.rentalId = response.rentalId;
  incident.incidentType = response.incidentType;
  incident.registeredAt = response.registeredAt;
  incident.estimatedRepairCost = response.estimatedRepairCost;
  incident.priority = response.priority;
  return incident;
}
```

---

## Infrastructure Layer — API Services

### Creación del VehicleApi Service

Ejecutar:

```
ng generate service masters/infrastructure/vehicle-api --skip-tests=true
```

Agregar los siguientes imports al archivo `vehicle-api.service.ts` ubicado en `src/app/masters/infrastructure`:

```typescript
import { inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { map, Observable } from 'rxjs';
import { environment } from '../../../environments/environment';
import { Vehicle } from '../domain/model/vehicle.entity';
import { VehicleResponse } from './vehicle-response';
import { VehicleAssembler } from './vehicle-assembler';
```

Reemplazar el contenido de la clase `VehicleApiService` con el siguiente código:

```typescript
/**
 * @summary API service for vehicle data access via HTTP following the Api pattern.
 * @author Elynor Palma
 */
private baseUrl: string = environment.serverBasePath;
private vehiclesEndpoint: string = environment.vehiclesEndpointPath;
private http: HttpClient = inject(HttpClient);

getAll(): Observable<Vehicle[]> {
  return this.http.get<VehicleResponse[]>(`${this.baseUrl}${this.vehiclesEndpoint}`)
    .pipe(
      map(responseArray => VehicleAssembler.toEntityFromResponseArray(responseArray))
    );
}
```

### Creación del RentalApi Service

Ejecutar:

```
ng generate service operations/infrastructure/rental-api --skip-tests=true
```

Agregar los siguientes imports al archivo `rental-api.service.ts` ubicado en `src/app/operations/infrastructure`:

```typescript
import { inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { map, Observable } from 'rxjs';
import { environment } from '../../../environments/environment';
import { Rental } from '../domain/model/rental.entity';
import { RentalResponse } from './rental-response';
import { RentalAssembler } from './rental-assembler';
```

Reemplazar el contenido de la clase `RentalApiService` con el siguiente código:

```typescript
/**
 * @summary API service for rental data access via HTTP following the Api pattern.
 * @author Elynor Palma
 */
private baseUrl: string = environment.serverBasePath;
private rentalsEndpoint: string = environment.rentalsEndpointPath;
private http: HttpClient = inject(HttpClient);

getAll(): Observable<Rental[]> {
  return this.http.get<RentalResponse[]>(`${this.baseUrl}${this.rentalsEndpoint}`)
    .pipe(
      map(responseArray => RentalAssembler.toEntityFromResponseArray(responseArray))
    );
}

create(rental: Rental): Observable<Rental> {
  return this.http.post<RentalResponse>(`${this.baseUrl}${this.rentalsEndpoint}`, rental)
    .pipe(
      map(response => RentalAssembler.toEntityFromResponse(response))
    );
}
```

### Creación del IncidentApi Service

Ejecutar:

```
ng generate service operations/infrastructure/incident-api --skip-tests=true
```

Agregar los siguientes imports al archivo `incident-api.service.ts` ubicado en `src/app/operations/infrastructure`:

```typescript
import { inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { map, Observable } from 'rxjs';
import { environment } from '../../../environments/environment';
import { Incident } from '../domain/model/incident.entity';
import { IncidentResponse } from './incident-response';
import { IncidentAssembler } from './incident-assembler';
```

Reemplazar el contenido de la clase `IncidentApiService` con el siguiente código:

```typescript
/**
 * @summary API service for incident data access via HTTP following the Api pattern.
 * @author Elynor Palma
 */
private baseUrl: string = environment.serverBasePath;
private incidentsEndpoint: string = environment.incidentsEndpointPath;
private http: HttpClient = inject(HttpClient);

getAll(): Observable<Incident[]> {
  return this.http.get<IncidentResponse[]>(`${this.baseUrl}${this.incidentsEndpoint}`)
    .pipe(
      map(responseArray => IncidentAssembler.toEntityFromResponseArray(responseArray))
    );
}

create(incident: Incident): Observable<Incident> {
  return this.http.post<IncidentResponse>(`${this.baseUrl}${this.incidentsEndpoint}`, incident)
    .pipe(
      map(response => IncidentAssembler.toEntityFromResponse(response))
    );
}
```

---

## Application Layer — Store Services

### Creación del VehicleStore Service

Ejecutar:

```
ng generate service masters/application/vehicle-store --skip-tests=true
```

Agregar los siguientes imports al archivo `vehicle-store.service.ts` ubicado en `src/app/masters/application`:

```typescript
import { computed, inject, Signal, signal, WritableSignal } from '@angular/core';
import { Vehicle } from '../domain/model/vehicle.entity';
import { VehicleApiService } from '../infrastructure/vehicle-api.service';
```

Reemplazar el contenido de la clase `VehicleStoreService` con el siguiente código:

```typescript
/**
 * @summary Store service for vehicle state management using Angular Signals.
 * @author Elynor Palma
 */
private vehiclesSignal: WritableSignal<Vehicle[]> = signal<Vehicle[]>([]);
private vehicleApi: VehicleApiService = inject(VehicleApiService);

readonly vehicles: Signal<Vehicle[]> = computed(() => this.vehiclesSignal());

loadAll(): void {
  if (this.vehiclesSignal().length === 0) {
    this.vehicleApi.getAll().subscribe(vehicles => {
      this.vehiclesSignal.set(vehicles);
    });
  }
}
```

### Creación del OperationsStore Service

Ejecutar:

```
ng generate service operations/application/operations-store --skip-tests=true
```

Agregar los siguientes imports al archivo `operations-store.service.ts` ubicado en `src/app/operations/application`:

```typescript
import { computed, inject, Signal, signal, WritableSignal } from '@angular/core';
import { Observable } from 'rxjs';
import { Rental } from '../domain/model/rental.entity';
import { Incident } from '../domain/model/incident.entity';
import { RentalApiService } from '../infrastructure/rental-api.service';
import { IncidentApiService } from '../infrastructure/incident-api.service';
```

Reemplazar el contenido de la clase `OperationsStoreService` con el siguiente código:

```typescript
/**
 * @summary Store service for rentals and incidents state management using Angular Signals.
 * @author Elynor Palma
 */
private rentalsSignal: WritableSignal<Rental[]> = signal<Rental[]>([]);
private incidentsSignal: WritableSignal<Incident[]> = signal<Incident[]>([]);
private rentalApi: RentalApiService = inject(RentalApiService);
private incidentApi: IncidentApiService = inject(IncidentApiService);

readonly rentals: Signal<Rental[]> = computed(() => this.rentalsSignal());
readonly incidents: Signal<Incident[]> = computed(() => this.incidentsSignal());

loadAll(): void {
  if (this.rentalsSignal().length === 0) {
    this.rentalApi.getAll().subscribe(rentals => {
      this.rentalsSignal.set(rentals);
    });
  }
  if (this.incidentsSignal().length === 0) {
    this.incidentApi.getAll().subscribe(incidents => {
      this.incidentsSignal.set(incidents);
    });
  }
}

createRental(rental: Rental): Observable<Rental> {
  return this.rentalApi.create(rental);
}

createIncident(incident: Incident): Observable<Incident> {
  return this.incidentApi.create(incident);
}
```

---

## Routing

Reemplazar el contenido del archivo `app.routes.ts` ubicado en `src/app`:

```typescript
/**
 * @summary Application routing configuration with semantic routes and child routes by bounded context.
 * @author Elynor Palma
 */
import { Routes } from '@angular/router';

export const routes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full' },
  {
    path: 'home',
    loadComponent: () =>
      import('./shared/presentation/views/home/home.component')
        .then(m => m.HomeComponent)
  },
  {
    path: 'operations',
    children: [
      {
        path: 'rentals/new',
        loadComponent: () =>
          import('./operations/presentation/views/new-rental/new-rental.component')
            .then(m => m.NewRentalComponent)
      }
    ]
  },
  {
    path: '**',
    loadComponent: () =>
      import('./shared/presentation/views/page-not-found/page-not-found.component')
        .then(m => m.PageNotFoundComponent)
  }
];
```

---

## Creación de componentes

Cargar el Terminal del IDE y ejecutar los siguientes comandos uno a la vez:

```
ng generate component shared/presentation/components/toolbar --skip-tests=true
```

```
ng generate component shared/presentation/components/language-switcher --skip-tests=true
```

```
ng generate component shared/presentation/views/home --skip-tests=true
```

```
ng generate component shared/presentation/views/page-not-found --skip-tests=true
```

```
ng generate component masters/presentation/components/vehicle-type-stats --skip-tests=true
```

```
ng generate component operations/presentation/components/next-urgent-incident --skip-tests=true
```

```
ng generate component operations/presentation/views/new-rental --skip-tests=true
```

---

## Modificación del LanguageSwitcherComponent

Agregar los siguientes imports al archivo `language-switcher.component.ts` ubicado en `src/app/shared/presentation/components/language-switcher`:

```typescript
import { TranslateService } from '@ngx-translate/core';
import { MatButtonToggleModule } from '@angular/material/button-toggle';
```

Agregar las siguientes clases en el array `imports` del decorator `@Component`:

```typescript
MatButtonToggleModule
```

Reemplazar el contenido de la clase `LanguageSwitcherComponent` con el siguiente código:

```typescript
/**
 * @summary Language switcher component for toggling between EN and ES.
 * @author Elynor Palma
 */
currentLang = 'en';
languages = ['en', 'es'];

constructor(private translate: TranslateService) {
  this.currentLang = translate.currentLang || 'en';
}

useLanguage(language: string): void {
  this.currentLang = language;
  this.translate.use(language);
}
```

Reemplazar el contenido del archivo `language-switcher.component.html`:

```html
<mat-button-toggle-group
  [value]="currentLang"
  appearance="standard"
  aria-label="Language selector"
  name="language">
  @for (language of languages; track language) {
    <mat-button-toggle
      [value]="language"
      [aria-label]="language"
      (click)="useLanguage(language)">
      {{ language.toUpperCase() }}
    </mat-button-toggle>
  }
</mat-button-toggle-group>
```

---

## Modificación del ToolbarComponent

Agregar los siguientes imports al archivo `toolbar.component.ts` ubicado en `src/app/shared/presentation/components/toolbar`:

```typescript
import { MatToolbarModule } from '@angular/material/toolbar';
import { MatButtonModule } from '@angular/material/button';
import { RouterLink } from '@angular/router';
import { TranslateModule } from '@ngx-translate/core';
import { LanguageSwitcherComponent } from '../language-switcher/language-switcher.component';
```

Agregar las siguientes clases en el array `imports` del decorator `@Component`:

```typescript
MatToolbarModule, MatButtonModule, RouterLink, TranslateModule, LanguageSwitcherComponent
```

Reemplazar el contenido de la clase `ToolbarComponent` con el siguiente comentario TSDoc (el cuerpo queda vacío):

```typescript
/**
 * @summary Toolbar component with logo, navigation links and language switcher.
 * @author Elynor Palma
 */
```

Reemplazar el contenido del archivo `toolbar.component.html`:

```html
<mat-toolbar color="primary" role="navigation" aria-label="Enterprise Fleet Manager toolbar">
  <img
    src="https://logo.clearbit.com/enterprise.com"
    alt="Enterprise Rent-A-Car logo"
    height="36"
    aria-hidden="true"
    style="margin-right: 8px;"
  />
  <span>{{ 'toolbar.title' | translate }}</span>

  <span style="flex: 1 1 auto;"></span>

  <a mat-button routerLink="/home" aria-label="Navigate to Home">
    {{ 'toolbar.home' | translate }}
  </a>
  <a mat-button routerLink="/operations/rentals/new" aria-label="Navigate to New Rental">
    {{ 'toolbar.newRental' | translate }}
  </a>

  <span style="margin-left: 16px;">
    <app-language-switcher />
  </span>
</mat-toolbar>
```

Reemplazar el contenido del archivo `toolbar.component.scss`:

```scss
mat-toolbar {
  position: sticky;
  top: 0;
  z-index: 100;
}
```

---

## Modificación del HomeComponent

Agregar los siguientes imports al archivo `home.component.ts` ubicado en `src/app/shared/presentation/views/home`:

```typescript
import { Component, inject, OnInit, Signal } from '@angular/core';
import { TranslateModule } from '@ngx-translate/core';
import { MatGridListModule } from '@angular/material/grid-list';
import { VehicleStoreService } from '../../../../masters/application/vehicle-store.service';
import { OperationsStoreService } from '../../../../operations/application/operations-store.service';
import { Vehicle } from '../../../../masters/domain/model/vehicle.entity';
import { Incident } from '../../../../operations/domain/model/incident.entity';
import { VehicleTypeStatsComponent } from '../../../../masters/presentation/components/vehicle-type-stats/vehicle-type-stats.component';
import { NextUrgentIncidentComponent } from '../../../../operations/presentation/components/next-urgent-incident/next-urgent-incident.component';
```

Agregar las siguientes clases en el array `imports` del decorator `@Component`:

```typescript
TranslateModule, MatGridListModule, VehicleTypeStatsComponent, NextUrgentIncidentComponent
```

Agregar la interface `OnInit` a la clase `HomeComponent`:

```typescript
implements OnInit
```

Reemplazar el contenido de la clase `HomeComponent` con el siguiente código:

```typescript
/**
 * @summary Home view displaying Fleet Utilization Analytics and Next Urgent Incident sections.
 * @author Elynor Palma
 */
private vehicleStore: VehicleStoreService = inject(VehicleStoreService);
private operationsStore: OperationsStoreService = inject(OperationsStoreService);

readonly vehicles: Signal<Vehicle[]> = this.vehicleStore.vehicles;
readonly incidents: Signal<Incident[]> = this.operationsStore.incidents;

readonly vehicleTypes: string[] = ['ECONOMY', 'SUV', 'LUXURY'];

ngOnInit(): void {
  this.vehicleStore.loadAll();
  this.operationsStore.loadAll();
}

getVehiclesByType(type: string): Vehicle[] {
  return this.vehicles().filter(v => v.vehicleType === type);
}

getIncidentsByType(type: string): Incident[] {
  const vehicleIds = this.getVehiclesByType(type).map(v => v.id);
  return this.incidents().filter(i => vehicleIds.includes(i.vehicleId));
}

get nextUrgentIncident(): Incident | null {
  const normalIncidents = this.incidents()
    .filter(i => i.priority === 'NORMAL')
    .sort((a, b) => new Date(b.registeredAt).getTime() - new Date(a.registeredAt).getTime());
  return normalIncidents.length > 0 ? normalIncidents[0] : null;
}
```

Reemplazar el contenido del archivo `home.component.html`:

```html
<main aria-label="Home page" style="padding: 24px;">
  <h1>{{ 'home.title' | translate }}</h1>
  <p>{{ 'home.welcome' | translate }}</p>

  <section aria-labelledby="fleet-title">
    <h2 id="fleet-title">{{ 'home.fleetUtilization' | translate }}</h2>
    <mat-grid-list cols="3" rowHeight="220px" gutterSize="16px">
      @for (type of vehicleTypes; track type) {
        <mat-grid-tile>
          <app-vehicle-type-stats
            [vehicleType]="type"
            [vehicles]="getVehiclesByType(type)"
            [incidents]="getIncidentsByType(type)"
          />
        </mat-grid-tile>
      }
    </mat-grid-list>
  </section>

  <section aria-labelledby="incident-title" style="margin-top: 32px;">
    <h2 id="incident-title">{{ 'home.nextUrgentIncident' | translate }}</h2>
    @if (nextUrgentIncident) {
      <app-next-urgent-incident [incident]="nextUrgentIncident" />
    }
  </section>
</main>
```

---

## Modificación del VehicleTypeStatsComponent

Agregar los siguientes imports al archivo `vehicle-type-stats.component.ts` ubicado en `src/app/masters/presentation/components/vehicle-type-stats`:

```typescript
import { input, InputSignal } from '@angular/core';
import { MatCardModule } from '@angular/material/card';
import { TranslateModule } from '@ngx-translate/core';
import { CurrencyPipe } from '@angular/common';
import { Vehicle } from '../../../domain/model/vehicle.entity';
import { Incident } from '../../../../operations/domain/model/incident.entity';
```

Agregar en el array `imports` del decorator `@Component`:

```typescript
MatCardModule, TranslateModule, CurrencyPipe
```

Reemplazar el contenido de la clase `VehicleTypeStatsComponent` con el siguiente código:

```typescript
/**
 * @summary Component displaying fleet utilization statistics per vehicle type.
 * @author Elynor Palma
 */
vehicleType: InputSignal<string> = input.required<string>();
vehicles: InputSignal<Vehicle[]> = input.required<Vehicle[]>();
incidents: InputSignal<Incident[]> = input.required<Incident[]>();

get dailyRevenuePotential(): number {
  return this.vehicles()
    .filter(v => v.status === 'RENTED')
    .reduce((sum, v) => sum + v.dailyRate, 0);
}

get estimatedIncidentCost(): number {
  const total = this.incidents().reduce((sum, i) => {
    const factor = i.priority === 'HIGH' ? 2.0 : 1.0;
    return sum + (i.estimatedRepairCost * factor);
  }, 0);
  return Math.round(total * 100) / 100;
}

get vehiclesRented(): number {
  return this.vehicles().filter(v => v.status === 'RENTED').length;
}
```

Reemplazar el contenido del archivo `vehicle-type-stats.component.html`:

```html
<mat-card style="width: 100%;" aria-label="Vehicle type statistics card">
  <mat-card-header>
    <mat-card-title>{{ vehicleType() }}</mat-card-title>
  </mat-card-header>
  <mat-card-content>
    <p>
      <strong>{{ 'vehicleTypeStats.dailyRevenuePotential' | translate }}:</strong>
      {{ dailyRevenuePotential | currency }}
    </p>
    <p>
      <strong>{{ 'vehicleTypeStats.estimatedIncidentCost' | translate }}:</strong>
      {{ estimatedIncidentCost | currency }}
    </p>
  </mat-card-content>
  <mat-card-footer style="padding: 8px 16px;">
    <p>
      <strong>{{ 'vehicleTypeStats.vehiclesRented' | translate }}:</strong>
      {{ vehiclesRented }}
    </p>
  </mat-card-footer>
</mat-card>
```

---

## Modificación del NextUrgentIncidentComponent

Agregar los siguientes imports al archivo `next-urgent-incident.component.ts` ubicado en `src/app/operations/presentation/components/next-urgent-incident`:

```typescript
import { input, InputSignal } from '@angular/core';
import { MatCardModule } from '@angular/material/card';
import { DatePipe } from '@angular/common';
import { Incident } from '../../../domain/model/incident.entity';
```

Agregar en el array `imports` del decorator `@Component`:

```typescript
MatCardModule, DatePipe
```

Reemplazar el contenido de la clase `NextUrgentIncidentComponent` con el siguiente código:

```typescript
/**
 * @summary Component displaying the most recent incident with NORMAL priority.
 * @author Elynor Palma
 */
incident: InputSignal<Incident> = input.required<Incident>();
```

Reemplazar el contenido del archivo `next-urgent-incident.component.html`:

```html
<mat-card aria-label="Next urgent incident card" style="max-width: 400px;">
  <mat-card-header>
    <mat-card-title>{{ incident().incidentType }}</mat-card-title>
    <mat-card-subtitle>{{ incident().registeredAt | date:'medium' }}</mat-card-subtitle>
  </mat-card-header>
  <mat-card-content>
    <p><strong>Vehicle ID:</strong> {{ incident().vehicleId }}</p>
    <p><strong>Priority:</strong> {{ incident().priority }}</p>
    <p><strong>Estimated Repair Cost:</strong> {{ incident().estimatedRepairCost | currency }}</p>
  </mat-card-content>
</mat-card>
```

---

## Modificación del NewRentalComponent

Agregar los siguientes imports al archivo `new-rental.component.ts` ubicado en `src/app/operations/presentation/views/new-rental`:

```typescript
import { Component, inject, OnInit, signal, WritableSignal } from '@angular/core';
import { FormBuilder, FormGroup, ReactiveFormsModule, Validators } from '@angular/forms';
import { Router } from '@angular/router';
import { MatFormFieldModule } from '@angular/material/form-field';
import { MatInputModule } from '@angular/material/input';
import { MatButtonModule } from '@angular/material/button';
import { MatSelectModule } from '@angular/material/select';
import { TranslateModule } from '@ngx-translate/core';
import { VehicleStoreService } from '../../../../masters/application/vehicle-store.service';
import { OperationsStoreService } from '../../../application/operations-store.service';
import { Vehicle } from '../../../../masters/domain/model/vehicle.entity';
import { Rental } from '../../../domain/model/rental.entity';
import { Incident } from '../../../domain/model/incident.entity';
```

Agregar en el array `imports` del decorator `@Component`:

```typescript
ReactiveFormsModule, MatFormFieldModule, MatInputModule, MatButtonModule, MatSelectModule, TranslateModule
```

Agregar la interface `OnInit` a la clase `NewRentalComponent`:

```typescript
implements OnInit
```

Reemplazar el contenido de la clase `NewRentalComponent` con el siguiente código:

```typescript
/**
 * @summary View component for creating a new rental contract with automatic incident generation.
 * @author Elynor Palma
 */
private fb: FormBuilder = inject(FormBuilder);
private router: Router = inject(Router);
private vehicleStore: VehicleStoreService = inject(VehicleStoreService);
private operationsStore: OperationsStoreService = inject(OperationsStoreService);

availableVehicles: WritableSignal<Vehicle[]> = signal<Vehicle[]>([]);

form: FormGroup = this.fb.group({
  vehicleId: [null, Validators.required],
  clientId: [null, [Validators.required, Validators.min(1)]],
  durationDays: [1, [Validators.required, Validators.min(1)]]
});

ngOnInit(): void {
  this.vehicleStore.loadAll();
  this.operationsStore.loadAll();
  setTimeout(() => {
    const activeVehicleIds = this.operationsStore.rentals()
      .filter(r => r.status === 'ACTIVE')
      .map(r => r.vehicleId);
    this.availableVehicles.set(
      this.vehicleStore.vehicles().filter(v => !activeVehicleIds.includes(v.id))
    );
  }, 600);
}

onSubmit(): void {
  if (this.form.invalid) return;

  const { vehicleId, clientId, durationDays } = this.form.value;
  const vehicle = this.vehicleStore.vehicles().find(v => v.id === vehicleId);
  if (!vehicle) return;

  const startDate = new Date();
  const endDate = new Date(startDate);
  endDate.setDate(endDate.getDate() + durationDays);

  const rental = new Rental();
  rental.vehicleId = vehicleId;
  rental.clientId = clientId;
  rental.startDate = startDate.toISOString();
  rental.endDate = endDate.toISOString();
  rental.durationDays = durationDays;
  rental.totalCost = durationDays * vehicle.dailyRate;
  rental.status = 'ACTIVE';

  this.operationsStore.createRental(rental).subscribe(created => {
    const incident = new Incident();
    incident.vehicleId = vehicleId;
    incident.rentalId = created.id;
    incident.incidentType = 'CLEANING';
    incident.registeredAt = new Date().toISOString();
    incident.estimatedRepairCost = 50.00;
    incident.priority = 'NORMAL';

    this.operationsStore.createIncident(incident).subscribe(() => {
      this.router.navigate(['/home']);
    });
  });
}

onCancel(): void {
  this.router.navigate(['/home']);
}
```

Reemplazar el contenido del archivo `new-rental.component.html`:

```html
<main aria-label="New rental form" style="padding: 24px; max-width: 500px;">
  <h1>{{ 'newRental.title' | translate }}</h1>
  <h2>{{ 'newRental.subtitle' | translate }}</h2>

  <form [formGroup]="form" (ngSubmit)="onSubmit()" aria-label="Create new rental contract">

    <mat-form-field appearance="outline" style="width: 100%;">
      <mat-label>{{ 'newRental.vehicleId' | translate }}</mat-label>
      <mat-select formControlName="vehicleId" aria-label="Select vehicle">
        @for (vehicle of availableVehicles(); track vehicle.id) {
          <mat-option [value]="vehicle.id">
            {{ vehicle.make }} {{ vehicle.model }} — {{ vehicle.vehicleType }}
          </mat-option>
        }
      </mat-select>
    </mat-form-field>

    <mat-form-field appearance="outline" style="width: 100%;">
      <mat-label>{{ 'newRental.clientId' | translate }}</mat-label>
      <input
        matInput
        type="number"
        formControlName="clientId"
        aria-label="Client ID"
        placeholder="e.g. 201"
      />
    </mat-form-field>

    <mat-form-field appearance="outline" style="width: 100%;">
      <mat-label>{{ 'newRental.durationDays' | translate }}</mat-label>
      <input
        matInput
        type="number"
        formControlName="durationDays"
        aria-label="Duration in days"
        placeholder="e.g. 3"
      />
    </mat-form-field>

    <div style="display: flex; gap: 12px; margin-top: 16px;">
      <button
        mat-raised-button
        color="primary"
        type="submit"
        [disabled]="form.invalid"
        aria-label="Create rental">
        {{ 'newRental.create' | translate }}
      </button>
      <button
        mat-button
        type="button"
        (click)="onCancel()"
        aria-label="Cancel and go back to home">
        {{ 'newRental.cancel' | translate }}
      </button>
    </div>

  </form>
</main>
```

---

## Modificación del PageNotFoundComponent

Agregar los siguientes imports al archivo `page-not-found.component.ts` ubicado en `src/app/shared/presentation/views/page-not-found`:

```typescript
import { inject } from '@angular/core';
import { Router, RouterLink } from '@angular/router';
import { MatButtonModule } from '@angular/material/button';
import { TranslateModule } from '@ngx-translate/core';
```

Agregar en el array `imports` del decorator `@Component`:

```typescript
MatButtonModule, RouterLink, TranslateModule
```

Reemplazar el contenido de la clase `PageNotFoundComponent` con el siguiente código:

```typescript
/**
 * @summary Page not found view for unsupported navigation routes.
 * @author Elynor Palma
 */
private router: Router = inject(Router);
currentUrl: string = this.router.url;
```

Reemplazar el contenido del archivo `page-not-found.component.html`:

```html
<main aria-label="Page not found" style="padding: 48px; text-align: center;">
  <h1>404</h1>
  <p>{{ 'notFound.message' | translate }} <code>{{ currentUrl }}</code></p>
  <a
    mat-raised-button
    color="primary"
    routerLink="/home"
    aria-label="Return to home page">
    {{ 'notFound.back' | translate }}
  </a>
</main>
```

---

## Modificación del AppComponent

Agregar los siguientes imports al archivo `app.component.ts` ubicado en `src/app`:

```typescript
import { RouterOutlet } from '@angular/router';
import { ToolbarComponent } from './shared/presentation/components/toolbar/toolbar.component';
```

Agregar en el array `imports` del decorator `@Component`:

```typescript
RouterOutlet, ToolbarComponent
```

Reemplazar el contenido de la clase `AppComponent` con el comentario TSDoc:

```typescript
/**
 * @summary Root application component.
 * @author Elynor Palma
 */
```

Reemplazar el contenido del archivo `app.component.html`:

```html
<app-toolbar />
<router-outlet />
```

---

## Ejecución del proyecto

**Terminal 1 — json-server (nuevo Tab):**

```
cd server
json-server --watch db.json
```

**Terminal 2 — Angular:**

```
ng serve --port 4200
```

Abrir el navegador en: http://localhost:4200

---

## Preparación del entregable

Antes de generar el archivo `.zip`, eliminar la carpeta `node_modules`:

```
# Mac / Linux
rm -rf node_modules

# Windows
rmdir /s /q node_modules
```

Comprimir el proyecto y nombrar el archivo:

```
ea<nrc>u<codigo>.zip
```

Ejemplo: `ea7377u20241a972.zip`

---

## README.md

Reemplazar el contenido del archivo `README.md` en la raíz del proyecto:

```markdown
# Enterprise Fleet Manager

Web application for fleet management built for Enterprise Rent-A-Car.

## Description

Enterprise Fleet Manager is an Angular-based frontend application that provides
fleet utilization analytics, rental contract management, and incident tracking
for Enterprise Rent-A-Car's vehicle fleet. The application consumes a REST API
simulated with json-server and follows a domain-driven architecture with layered
and component-based design patterns.

## Features

- Fleet Utilization Analytics by vehicle type (ECONOMY, SUV, LUXURY)
- Daily Revenue Potential and Estimated Incident Cost indicators per vehicle type
- Next Urgent Incident display
- New Rental contract creation with automatic cleaning incident generation
- Internationalization support (EN / ES) with ngx-translate
- Responsive UI with Angular Material
- Page Not Found view for unsupported routes

## Tech Stack

- Angular 20+
- Angular Material
- @ngx-translate/core
- json-server 0.17.4 (fake REST API)
- TypeScript

## Author

**Elynor Palma**
Universidad Peruana de Ciencias Aplicadas (UPC)
Course: Desarrollo de Aplicaciones Open Source (1ASI0729)
```

---

## Referencias

- https://angular.dev/tools/cli/setup-local
- https://material.angular.dev/guide/theming
- https://ngx-translate.org/
- https://github.com/typicode/json-server/tree/v0
- https://angular.dev/guide/routing/common-router-tasks
- https://angular.dev/guide/routing/router-tutorial#adding-a-404-page
- https://angular.dev/guide/http
- https://angular.dev/api/common/DatePipe
- https://material.angular.io/components/card/overview
- https://material.angular.io/components/grid-list/overview
- https://material.angular.io/components/toolbar/overview
- https://tsdoc.org/
