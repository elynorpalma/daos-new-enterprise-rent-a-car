# daos-new-enterprise-rent-a-car
# Proyecto Enterprise Fleet Manager

Guía para el desarrollo de la aplicación web **Enterprise Fleet Manager** usando Angular, Angular Material, json-server y ngx-translate.

---

## Creación del proyecto

> **En equipos MAC:** antecede `sudo` a los comandos `ng` e ingresa la contraseña del administrador.
>
> **En equipos Windows:** ubícate en la carpeta `IdeaProjects/` o una de tu preferencia.

### Crear el workspace y la aplicación inicial

Abre el terminal y ejecuta:

```bash
ng new ea<nrc>u<codigo>
```

> Reemplaza `<nrc>` con el NRC de tu sección y `<codigo>` con tu código de estudiante en minúsculas.  
> Ejemplo: `ea7377u20241a972`

Cuando el CLI muestre las opciones, selecciona:

- *Which stylesheet format would you like to use?*

```
SCSS   [ https://sass-lang.com/documentation/syntax#scss ]
```

- *Do you want to enable Server-Side Rendering (SSR)?*

```
N
```

### Instalar Angular Material

Ingresa a la carpeta del proyecto:

```bash
cd ea<nrc>u<codigo>
```

Ejecuta:

```bash
ng add @angular/material
```

Cuando pregunte:

- *Would you like to proceed?* → `Y`
- *Select a pair of starter prebuilt color palettes* → selecciona la que tenga mayor similitud con el design system del cliente (para Enterprise Rent-A-Car se recomienda **Rose/Red**)

### Instalar ngx-translate (i18n)

```bash
npm install @ngx-translate/core @ngx-translate/http-loader --save
```

### Instalar json-server

```bash
npm install -g json-server@0.17.4
```

---

## Configuración del proyecto

### Archivos de idioma (i18n)

Crea las carpetas `assets/i18n` dentro de `public/`:

```
📂 public
  📂 assets
    📂 i18n
```

Crea los archivos `en.json` y `es.json`:

#### en.json

```json
{
  "toolbar": {
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
    "message": "Page not found:",
    "back": "Go to Home"
  }
}
```

#### es.json

```json
{
  "toolbar": {
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
    "message": "Página no encontrada:",
    "back": "Ir a Inicio"
  }
}
```

### Configuración del json-server

Crea la carpeta `server/` en la raíz del proyecto y coloca dentro el archivo `db.json` proporcionado en el examen:

```
📂 ea<nrc>u<codigo>
  📂 server
    db.json
```

Para iniciar el fake API, abre una nueva pestaña del terminal y ejecuta:

```bash
cd server
json-server --watch db.json
```

Verifica los endpoints en el navegador:

- http://localhost:3000/vehicles
- http://localhost:3000/rentals
- http://localhost:3000/incidents

### Configuración de environments

Ejecuta en el terminal:

```bash
ng generate environments
```

Modifica `src/environments/environment.development.ts`:

```typescript
export const environment = {
  production: false,
  serverBasePath: 'http://localhost:3000'
};
```

Modifica `src/environments/environment.ts`:

```typescript
export const environment = {
  production: true,
  serverBasePath: 'http://localhost:3000'
};
```

### Configuración del appConfig

Modifica `src/app/app.config.ts`:

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
      translate.use('en');
    })
  ]
};
```

---

## Estructura del proyecto (Domain-Driven)

Crea la siguiente estructura de carpetas dentro de `src/app/`:

```
📂 src/app
  📂 shared
    📂 domain
    📂 infrastructure
    📂 presentation
      📂 components
      📂 views
  📂 masters                  ← vehicles
    📂 domain
      📂 model
    📂 application
    📂 infrastructure
    📂 presentation
      📂 components
      📂 views
  📂 operations               ← rentals + incidents
    📂 domain
      📂 model
    📂 application
    📂 infrastructure
    📂 presentation
      📂 components
      📂 views
```

---

## Modelos (Domain Layer)

### Vehicle

Crea el archivo `src/app/masters/domain/model/vehicle.entity.ts`:

```typescript
/**
 * @summary Vehicle entity representing a fleet unit.
 * @author Tu Nombre y Apellido
 */
export class Vehicle {
  id: number;
  make: string;
  model: string;
  mileageKm: number;
  dailyRate: number;
  vehicleType: 'ECONOMY' | 'SUV' | 'LUXURY';
  status: 'AVAILABLE' | 'RENTED' | 'MAINTENANCE';

  constructor() {
    this.id = 0;
    this.make = '';
    this.model = '';
    this.mileageKm = 0;
    this.dailyRate = 0;
    this.vehicleType = 'ECONOMY';
    this.status = 'AVAILABLE';
  }
}
```

### Rental

Crea `src/app/operations/domain/model/rental.entity.ts`:

```typescript
/**
 * @summary Rental entity representing a vehicle rental contract.
 * @author Tu Nombre y Apellido
 */
export class Rental {
  id?: number;
  vehicleId: number;
  clientId: number;
  startDate: string;
  endDate: string;
  durationDays: number;
  totalCost: number;
  status: 'ACTIVE' | 'COMPLETED' | 'CANCELED';

  constructor() {
    this.vehicleId = 0;
    this.clientId = 0;
    this.startDate = '';
    this.endDate = '';
    this.durationDays = 0;
    this.totalCost = 0;
    this.status = 'ACTIVE';
  }
}
```

### Incident

Crea `src/app/operations/domain/model/incident.entity.ts`:

```typescript
/**
 * @summary Incident entity representing a vehicle incident or maintenance event.
 * @author Tu Nombre y Apellido
 */
export class Incident {
  id?: number;
  vehicleId: number;
  rentalId: number | null;
  incidentType: 'DAMAGE' | 'BREAKDOWN' | 'CLEANING' | 'REPAIR';
  registeredAt: string;
  estimatedRepairCost: number;
  priority: 'HIGH' | 'NORMAL';

  constructor() {
    this.vehicleId = 0;
    this.rentalId = null;
    this.incidentType = 'CLEANING';
    this.registeredAt = '';
    this.estimatedRepairCost = 0;
    this.priority = 'NORMAL';
  }
}
```

---

## Infrastructure Layer

### Response interfaces

Crea `src/app/masters/infrastructure/vehicle.response.ts`:

```typescript
/**
 * @summary Vehicle response interface for API deserialization.
 * @author Tu Nombre y Apellido
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

Crea `src/app/operations/infrastructure/rental.response.ts`:

```typescript
/**
 * @summary Rental response interface for API deserialization.
 * @author Tu Nombre y Apellido
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

Crea `src/app/operations/infrastructure/incident.response.ts`:

```typescript
/**
 * @summary Incident response interface for API deserialization.
 * @author Tu Nombre y Apellido
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

### Assemblers

Crea `src/app/masters/infrastructure/vehicle.assembler.ts`:

```typescript
/**
 * @summary Assembler for converting VehicleResponse to Vehicle entity.
 * @author Tu Nombre y Apellido
 */
import { VehicleResponse } from './vehicle.response';
import { Vehicle } from '../domain/model/vehicle.entity';

export class VehicleAssembler {
  static toEntityFromResponse(response: VehicleResponse): Vehicle {
    const vehicle = new Vehicle();
    vehicle.id = response.id;
    vehicle.make = response.make;
    vehicle.model = response.model;
    vehicle.mileageKm = response.mileageKm;
    vehicle.dailyRate = response.dailyRate;
    vehicle.vehicleType = response.vehicleType as Vehicle['vehicleType'];
    vehicle.status = response.status as Vehicle['status'];
    return vehicle;
  }

  static toEntityFromResponseArray(responseArray: VehicleResponse[]): Vehicle[] {
    return responseArray.map(r => this.toEntityFromResponse(r));
  }
}
```

### API Services

Crea `src/app/masters/infrastructure/vehicle-api.service.ts`:

```typescript
/**
 * @summary API service for vehicle data access via HTTP.
 * @author Tu Nombre y Apellido
 */
import { inject, Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { map, Observable } from 'rxjs';
import { environment } from '../../../environments/environment';
import { Vehicle } from '../domain/model/vehicle.entity';
import { VehicleResponse } from './vehicle.response';
import { VehicleAssembler } from './vehicle.assembler';

@Injectable({ providedIn: 'root' })
export class VehicleApiService {
  private baseUrl = `${environment.serverBasePath}/vehicles`;
  private http = inject(HttpClient);

  getAll(): Observable<Vehicle[]> {
    return this.http.get<VehicleResponse[]>(this.baseUrl)
      .pipe(map(res => VehicleAssembler.toEntityFromResponseArray(res)));
  }
}
```

Crea `src/app/operations/infrastructure/rental-api.service.ts`:

```typescript
/**
 * @summary API service for rental data access via HTTP.
 * @author Tu Nombre y Apellido
 */
import { inject, Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { environment } from '../../../environments/environment';
import { Rental } from '../domain/model/rental.entity';

@Injectable({ providedIn: 'root' })
export class RentalApiService {
  private baseUrl = `${environment.serverBasePath}/rentals`;
  private http = inject(HttpClient);

  getAll(): Observable<Rental[]> {
    return this.http.get<Rental[]>(this.baseUrl);
  }

  create(rental: Rental): Observable<Rental> {
    return this.http.post<Rental>(this.baseUrl, rental);
  }
}
```

Crea `src/app/operations/infrastructure/incident-api.service.ts`:

```typescript
/**
 * @summary API service for incident data access via HTTP.
 * @author Tu Nombre y Apellido
 */
import { inject, Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { environment } from '../../../environments/environment';
import { Incident } from '../domain/model/incident.entity';

@Injectable({ providedIn: 'root' })
export class IncidentApiService {
  private baseUrl = `${environment.serverBasePath}/incidents`;
  private http = inject(HttpClient);

  getAll(): Observable<Incident[]> {
    return this.http.get<Incident[]>(this.baseUrl);
  }

  create(incident: Incident): Observable<Incident> {
    return this.http.post<Incident>(this.baseUrl, incident);
  }
}
```

---

## Application Layer (Stores con Signals)

### VehicleStore

Crea `src/app/masters/application/vehicle-store.service.ts`:

```typescript
/**
 * @summary Store service for vehicle state management using Angular Signals.
 * @author Tu Nombre y Apellido
 */
import { computed, inject, Injectable, signal } from '@angular/core';
import { Vehicle } from '../domain/model/vehicle.entity';
import { VehicleApiService } from '../infrastructure/vehicle-api.service';

@Injectable({ providedIn: 'root' })
export class VehicleStoreService {
  private vehiclesSignal = signal<Vehicle[]>([]);
  private api = inject(VehicleApiService);

  readonly vehicles = computed(() => this.vehiclesSignal());

  loadAll(): void {
    if (this.vehiclesSignal().length === 0) {
      this.api.getAll().subscribe(v => this.vehiclesSignal.set(v));
    }
  }
}
```

### OperationsStore

Crea `src/app/operations/application/operations-store.service.ts`:

```typescript
/**
 * @summary Store service for rentals and incidents state management using Angular Signals.
 * @author Tu Nombre y Apellido
 */
import { computed, inject, Injectable, signal } from '@angular/core';
import { Rental } from '../domain/model/rental.entity';
import { Incident } from '../domain/model/incident.entity';
import { RentalApiService } from '../infrastructure/rental-api.service';
import { IncidentApiService } from '../infrastructure/incident-api.service';

@Injectable({ providedIn: 'root' })
export class OperationsStoreService {
  private rentalsSignal = signal<Rental[]>([]);
  private incidentsSignal = signal<Incident[]>([]);
  private rentalApi = inject(RentalApiService);
  private incidentApi = inject(IncidentApiService);

  readonly rentals = computed(() => this.rentalsSignal());
  readonly incidents = computed(() => this.incidentsSignal());

  loadAll(): void {
    if (this.rentalsSignal().length === 0) {
      this.rentalApi.getAll().subscribe(r => this.rentalsSignal.set(r));
    }
    if (this.incidentsSignal().length === 0) {
      this.incidentApi.getAll().subscribe(i => this.incidentsSignal.set(i));
    }
  }

  createRental(rental: Rental) {
    return this.rentalApi.create(rental);
  }

  createIncident(incident: Incident) {
    return this.incidentApi.create(incident);
  }
}
```

---

## Routing

Modifica `src/app/app.routes.ts`:

```typescript
/**
 * @summary Application routing configuration with semantic and child routes.
 * @author Tu Nombre y Apellido
 */
import { Routes } from '@angular/router';

export const routes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full' },
  {
    path: 'home',
    loadComponent: () =>
      import('./shared/presentation/views/home/home.component').then(m => m.HomeComponent)
  },
  {
    path: 'operations',
    children: [
      {
        path: 'rentals/new',
        loadComponent: () =>
          import('./operations/presentation/views/new-rental/new-rental.component').then(m => m.NewRentalComponent)
      }
    ]
  },
  {
    path: '**',
    loadComponent: () =>
      import('./shared/presentation/views/page-not-found/page-not-found.component').then(m => m.PageNotFoundComponent)
  }
];
```

---

## Componentes

### Generar los componentes con Angular CLI

Ejecuta los siguientes comandos uno por uno en el terminal:

```bash
ng generate component shared/presentation/components/toolbar --skip-tests=true
ng generate component shared/presentation/components/language-switcher --skip-tests=true
ng generate component shared/presentation/views/home --skip-tests=true
ng generate component shared/presentation/views/page-not-found --skip-tests=true
ng generate component masters/presentation/components/vehicle-type-stats --skip-tests=true
ng generate component operations/presentation/components/next-urgent-incident --skip-tests=true
ng generate component operations/presentation/views/new-rental --skip-tests=true
```

### ToolbarComponent

`src/app/shared/presentation/components/toolbar/toolbar.component.ts`:

```typescript
/**
 * @summary Toolbar component with logo, navigation links, and language switcher.
 * @author Tu Nombre y Apellido
 */
import { Component } from '@angular/core';
import { RouterLink } from '@angular/router';
import { MatToolbarModule } from '@angular/material/toolbar';
import { MatButtonModule } from '@angular/material/button';
import { TranslateModule } from '@ngx-translate/core';
import { LanguageSwitcherComponent } from '../language-switcher/language-switcher.component';

@Component({
  selector: 'app-toolbar',
  standalone: true,
  imports: [MatToolbarModule, MatButtonModule, RouterLink, TranslateModule, LanguageSwitcherComponent],
  templateUrl: './toolbar.component.html',
  styleUrl: './toolbar.component.scss'
})
export class ToolbarComponent {}
```

`toolbar.component.html`:

```html
<mat-toolbar color="primary" role="navigation" aria-label="Enterprise Fleet Manager Navigation">
  <img
    src="https://logo.clearbit.com/enterprise.com"
    alt="Enterprise Rent-A-Car logo"
    height="36"
    style="margin-right: 8px"
  />
  <span>Enterprise Fleet Manager</span>

  <span style="flex: 1 1 auto;"></span>

  <a mat-button routerLink="/home" aria-label="Home">{{ 'toolbar.home' | translate }}</a>
  <a mat-button routerLink="/operations/rentals/new" aria-label="New Rental">{{ 'toolbar.newRental' | translate }}</a>

  <span style="margin-left: 16px;">
    <app-language-switcher />
  </span>
</mat-toolbar>
```

### LanguageSwitcherComponent

`language-switcher.component.ts`:

```typescript
/**
 * @summary Language switcher component for toggling between EN and ES.
 * @author Tu Nombre y Apellido
 */
import { Component } from '@angular/core';
import { TranslateService } from '@ngx-translate/core';
import { MatButtonToggleModule } from '@angular/material/button-toggle';

@Component({
  selector: 'app-language-switcher',
  standalone: true,
  imports: [MatButtonToggleModule],
  templateUrl: './language-switcher.component.html'
})
export class LanguageSwitcherComponent {
  currentLang = 'en';
  languages = ['en', 'es'];

  constructor(private translate: TranslateService) {
    this.currentLang = translate.currentLang || 'en';
  }

  useLanguage(lang: string): void {
    this.currentLang = lang;
    this.translate.use(lang);
  }
}
```

`language-switcher.component.html`:

```html
<mat-button-toggle-group
  [value]="currentLang"
  appearance="standard"
  aria-label="Language selector"
  name="language">
  @for (lang of languages; track lang) {
    <mat-button-toggle
      [value]="lang"
      [aria-label]="lang"
      (click)="useLanguage(lang)">
      {{ lang.toUpperCase() }}
    </mat-button-toggle>
  }
</mat-button-toggle-group>
```

### HomeComponent

`home.component.ts`:

```typescript
/**
 * @summary Home view displaying Fleet Utilization Analytics and Next Urgent Incident.
 * @author Tu Nombre y Apellido
 */
import { Component, inject, OnInit, Signal } from '@angular/core';
import { TranslateModule } from '@ngx-translate/core';
import { MatGridListModule } from '@angular/material/grid-list';
import { VehicleStoreService } from '../../../../masters/application/vehicle-store.service';
import { OperationsStoreService } from '../../../../operations/application/operations-store.service';
import { Vehicle } from '../../../../masters/domain/model/vehicle.entity';
import { Incident } from '../../../../operations/domain/model/incident.entity';
import { VehicleTypeStatsComponent } from '../../../../masters/presentation/components/vehicle-type-stats/vehicle-type-stats.component';
import { NextUrgentIncidentComponent } from '../../../../operations/presentation/components/next-urgent-incident/next-urgent-incident.component';

@Component({
  selector: 'app-home',
  standalone: true,
  imports: [TranslateModule, MatGridListModule, VehicleTypeStatsComponent, NextUrgentIncidentComponent],
  templateUrl: './home.component.html'
})
export class HomeComponent implements OnInit {
  private vehicleStore = inject(VehicleStoreService);
  private operationsStore = inject(OperationsStoreService);

  readonly vehicles: Signal<Vehicle[]> = this.vehicleStore.vehicles;
  readonly incidents: Signal<Incident[]> = this.operationsStore.incidents;

  readonly vehicleTypes = ['ECONOMY', 'SUV', 'LUXURY'] as const;

  ngOnInit(): void {
    this.vehicleStore.loadAll();
    this.operationsStore.loadAll();
  }

  getVehiclesByType(type: string): Vehicle[] {
    return this.vehicles().filter(v => v.vehicleType === type);
  }

  getIncidentsByVehicleType(type: string): Incident[] {
    const vehicleIds = this.getVehiclesByType(type).map(v => v.id);
    return this.incidents().filter(i => vehicleIds.includes(i.vehicleId));
  }

  get nextUrgentIncident(): Incident | null {
    const normal = this.incidents()
      .filter(i => i.priority === 'NORMAL')
      .sort((a, b) => new Date(b.registeredAt).getTime() - new Date(a.registeredAt).getTime());
    return normal[0] ?? null;
  }
}
```

`home.component.html`:

```html
<main aria-label="Home page">
  <h1>{{ 'home.title' | translate }}</h1>
  <p>{{ 'home.welcome' | translate }}</p>

  <section aria-labelledby="fleet-analytics-title">
    <h2 id="fleet-analytics-title">{{ 'home.fleetUtilization' | translate }}</h2>
    <mat-grid-list cols="3" rowHeight="220px" gutterSize="16px">
      @for (type of vehicleTypes; track type) {
        <mat-grid-tile>
          <app-vehicle-type-stats
            [vehicleType]="type"
            [vehicles]="getVehiclesByType(type)"
            [incidents]="getIncidentsByVehicleType(type)"
          />
        </mat-grid-tile>
      }
    </mat-grid-list>
  </section>

  <section aria-labelledby="next-incident-title">
    <h2 id="next-incident-title">{{ 'home.nextUrgentIncident' | translate }}</h2>
    @if (nextUrgentIncident) {
      <app-next-urgent-incident [incident]="nextUrgentIncident" />
    }
  </section>
</main>
```

### VehicleTypeStatsComponent

`vehicle-type-stats.component.ts`:

```typescript
/**
 * @summary Component displaying fleet statistics per vehicle type.
 * @author Tu Nombre y Apellido
 */
import { Component, input, InputSignal } from '@angular/core';
import { MatCardModule } from '@angular/material/card';
import { TranslateModule } from '@ngx-translate/core';
import { Vehicle } from '../../../domain/model/vehicle.entity';
import { Incident } from '../../../../operations/domain/model/incident.entity';

@Component({
  selector: 'app-vehicle-type-stats',
  standalone: true,
  imports: [MatCardModule, TranslateModule],
  templateUrl: './vehicle-type-stats.component.html'
})
export class VehicleTypeStatsComponent {
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
      return sum + i.estimatedRepairCost * factor;
    }, 0);
    return Math.round(total * 100) / 100;
  }

  get vehiclesRented(): number {
    return this.vehicles().filter(v => v.status === 'RENTED').length;
  }
}
```

`vehicle-type-stats.component.html`:

```html
<mat-card aria-label="Vehicle type stats card" style="width: 100%;">
  <mat-card-header>
    <mat-card-title>{{ vehicleType() }}</mat-card-title>
  </mat-card-header>
  <mat-card-content>
    <p><strong>{{ 'vehicleTypeStats.dailyRevenuePotential' | translate }}:</strong> {{ dailyRevenuePotential | currency }}</p>
    <p><strong>{{ 'vehicleTypeStats.estimatedIncidentCost' | translate }}:</strong> {{ estimatedIncidentCost | currency }}</p>
  </mat-card-content>
  <mat-card-footer>
    <p>{{ 'vehicleTypeStats.vehiclesRented' | translate }}: {{ vehiclesRented }}</p>
  </mat-card-footer>
</mat-card>
```

### NextUrgentIncidentComponent

`next-urgent-incident.component.ts`:

```typescript
/**
 * @summary Component displaying the most recent incident with NORMAL priority.
 * @author Tu Nombre y Apellido
 */
import { Component, input, InputSignal } from '@angular/core';
import { MatCardModule } from '@angular/material/card';
import { DatePipe } from '@angular/common';
import { Incident } from '../../../domain/model/incident.entity';

@Component({
  selector: 'app-next-urgent-incident',
  standalone: true,
  imports: [MatCardModule, DatePipe],
  templateUrl: './next-urgent-incident.component.html'
})
export class NextUrgentIncidentComponent {
  incident: InputSignal<Incident> = input.required<Incident>();
}
```

`next-urgent-incident.component.html`:

```html
<mat-card aria-label="Next urgent incident">
  <mat-card-header>
    <mat-card-title>{{ incident().incidentType }}</mat-card-title>
    <mat-card-subtitle>{{ incident().registeredAt | date:'medium' }}</mat-card-subtitle>
  </mat-card-header>
  <mat-card-content>
    <p><strong>Vehicle ID:</strong> {{ incident().vehicleId }}</p>
    <p><strong>Priority:</strong> {{ incident().priority }}</p>
    <p><strong>Estimated Cost:</strong> {{ incident().estimatedRepairCost | currency }}</p>
  </mat-card-content>
</mat-card>
```

### NewRentalComponent

`new-rental.component.ts`:

```typescript
/**
 * @summary View component for creating a new rental contract.
 * @author Tu Nombre y Apellido
 */
import { Component, inject, OnInit, signal } from '@angular/core';
import { FormBuilder, ReactiveFormsModule, Validators } from '@angular/forms';
import { Router } from '@angular/router';
import { MatFormFieldModule } from '@angular/material/form-field';
import { MatInputModule } from '@angular/material/input';
import { MatButtonModule } from '@angular/material/button';
import { MatSelectModule } from '@angular/material/select';
import { TranslateModule } from '@ngx-translate/core';
import { OperationsStoreService } from '../../../application/operations-store.service';
import { VehicleStoreService } from '../../../../masters/application/vehicle-store.service';
import { Rental } from '../../../domain/model/rental.entity';
import { Incident } from '../../../domain/model/incident.entity';
import { Vehicle } from '../../../../masters/domain/model/vehicle.entity';

@Component({
  selector: 'app-new-rental',
  standalone: true,
  imports: [ReactiveFormsModule, MatFormFieldModule, MatInputModule, MatButtonModule, MatSelectModule, TranslateModule],
  templateUrl: './new-rental.component.html'
})
export class NewRentalComponent implements OnInit {
  private fb = inject(FormBuilder);
  private router = inject(Router);
  private operationsStore = inject(OperationsStoreService);
  private vehicleStore = inject(VehicleStoreService);

  availableVehicles = signal<Vehicle[]>([]);

  form = this.fb.group({
    vehicleId: [0, Validators.required],
    clientId: [0, [Validators.required, Validators.min(1)]],
    durationDays: [1, [Validators.required, Validators.min(1)]]
  });

  ngOnInit(): void {
    this.vehicleStore.loadAll();
    this.operationsStore.loadAll();
    this.vehicleStore.vehicles;
    // Show only available vehicles (no active rental)
    this.vehicleStore.loadAll();
    setTimeout(() => {
      const rentedIds = this.operationsStore.rentals()
        .filter(r => r.status === 'ACTIVE')
        .map(r => r.vehicleId);
      this.availableVehicles.set(
        this.vehicleStore.vehicles().filter(v => !rentedIds.includes(v.id))
      );
    }, 500);
  }

  onSubmit(): void {
    if (this.form.invalid) return;

    const { vehicleId, clientId, durationDays } = this.form.value;
    const vehicle = this.vehicleStore.vehicles().find(v => v.id === vehicleId);
    if (!vehicle) return;

    const startDate = new Date();
    const endDate = new Date(startDate);
    endDate.setDate(endDate.getDate() + (durationDays ?? 1));

    const rental: Rental = {
      vehicleId: vehicleId!,
      clientId: clientId!,
      startDate: startDate.toISOString(),
      endDate: endDate.toISOString(),
      durationDays: durationDays!,
      totalCost: durationDays! * vehicle.dailyRate,
      status: 'ACTIVE'
    };

    this.operationsStore.createRental(rental).subscribe(created => {
      const incident: Incident = {
        vehicleId: vehicleId!,
        rentalId: created.id ?? null,
        incidentType: 'CLEANING',
        registeredAt: new Date().toISOString(),
        estimatedRepairCost: 50.00,
        priority: 'NORMAL'
      };
      this.operationsStore.createIncident(incident).subscribe(() => {
        this.router.navigate(['/home']);
      });
    });
  }

  onCancel(): void {
    this.router.navigate(['/home']);
  }
}
```

`new-rental.component.html`:

```html
<main aria-label="New rental form">
  <h1>{{ 'newRental.title' | translate }}</h1>
  <h2>{{ 'newRental.subtitle' | translate }}</h2>

  <form [formGroup]="form" (ngSubmit)="onSubmit()" aria-label="Create rental form">

    <mat-form-field appearance="outline">
      <mat-label>{{ 'newRental.vehicleId' | translate }}</mat-label>
      <mat-select formControlName="vehicleId" aria-label="Vehicle selection">
        @for (vehicle of availableVehicles(); track vehicle.id) {
          <mat-option [value]="vehicle.id">
            {{ vehicle.make }} {{ vehicle.model }} ({{ vehicle.vehicleType }})
          </mat-option>
        }
      </mat-select>
    </mat-form-field>

    <mat-form-field appearance="outline">
      <mat-label>{{ 'newRental.clientId' | translate }}</mat-label>
      <input matInput type="number" formControlName="clientId" aria-label="Client ID" />
    </mat-form-field>

    <mat-form-field appearance="outline">
      <mat-label>{{ 'newRental.durationDays' | translate }}</mat-label>
      <input matInput type="number" formControlName="durationDays" aria-label="Duration in days" />
    </mat-form-field>

    <div>
      <button mat-raised-button color="primary" type="submit" [disabled]="form.invalid" aria-label="Create rental">
        {{ 'newRental.create' | translate }}
      </button>
      <button mat-button type="button" (click)="onCancel()" aria-label="Cancel">
        {{ 'newRental.cancel' | translate }}
      </button>
    </div>
  </form>
</main>
```

### PageNotFoundComponent

`page-not-found.component.ts`:

```typescript
/**
 * @summary Page not found view for unsupported navigation routes.
 * @author Tu Nombre y Apellido
 */
import { Component, inject } from '@angular/core';
import { Router, RouterLink } from '@angular/router';
import { MatButtonModule } from '@angular/material/button';
import { TranslateModule } from '@ngx-translate/core';

@Component({
  selector: 'app-page-not-found',
  standalone: true,
  imports: [MatButtonModule, RouterLink, TranslateModule],
  templateUrl: './page-not-found.component.html'
})
export class PageNotFoundComponent {
  private router = inject(Router);
  currentUrl = this.router.url;
}
```

`page-not-found.component.html`:

```html
<main aria-label="Page not found">
  <h1>404</h1>
  <p>{{ 'notFound.message' | translate }} <code>{{ currentUrl }}</code></p>
  <a mat-raised-button color="primary" routerLink="/home" aria-label="Go to Home">
    {{ 'notFound.back' | translate }}
  </a>
</main>
```

---

## Modificación del AppComponent

`src/app/app.component.ts`:

```typescript
/**
 * @summary Root application component.
 * @author Tu Nombre y Apellido
 */
import { Component } from '@angular/core';
import { RouterOutlet } from '@angular/router';
import { ToolbarComponent } from './shared/presentation/components/toolbar/toolbar.component';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [RouterOutlet, ToolbarComponent],
  templateUrl: './app.component.html'
})
export class AppComponent {}
```

`src/app/app.component.html`:

```html
<app-toolbar />
<router-outlet />
```

---

## Ejecución del proyecto

**Terminal 1 — json-server:**

```bash
cd server
json-server --watch db.json
```

**Terminal 2 — Angular:**

```bash
ng serve --port 4200
```

Abre el navegador en: http://localhost:4200

---

## Preparación del entregable

Antes de empaquetar el `.zip`, elimina la carpeta `node_modules`:

```bash
rm -rf node_modules   # Mac/Linux
rmdir /s node_modules # Windows
```

Nombra el archivo:

```
ea<nrc>u<codigo>.zip
```

Ejemplo: `ea7377u20241a972.zip`

---

## README.md

```markdown
# Enterprise Fleet Manager

Web application for fleet management built for Enterprise Rent-A-Car.

## Description

Enterprise Fleet Manager is an Angular-based frontend application that provides
fleet utilization analytics, rental management, and incident tracking for
Enterprise Rent-A-Car's vehicle fleet.

## Features

- Fleet Utilization Analytics by vehicle type
- Next Urgent Incident display
- New Rental contract creation with automatic incident generation
- Internationalization (EN / ES)
- Responsive UI with Angular Material

## Tech Stack

- Angular 20+
- Angular Material
- ngx-translate
- json-server (fake REST API)
- TypeScript

## Author

**Your Name**  
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
- https://angular.dev/guide/http
- https://material.angular.io/components/card/overview
- https://material.angular.io/components/grid-list/overview
- https://material.angular.io/components/toolbar/overview
- https://tsdoc.org/
