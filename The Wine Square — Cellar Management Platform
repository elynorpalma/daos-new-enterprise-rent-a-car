# The Wine Square — Cellar Management Platform

Guía paso a paso para el desarrollo de la aplicación web siguiendo el patrón del curso DAOS (1ASI0729).

> `<nrc>` = NRC de tu sección | `<codigo>` = tu código en minúsculas
> Ejemplo: `ea20262u20241a972`

---

## 1. Crear el proyecto

> **MAC:** antecede `sudo` a los comandos `ng`. Contraseña: `d3v3l0p3rUPC`
> **Windows:** ubícate en `IdeaProjects/`

```
ng new ea<nrc>u<codigo>
```

- Stylesheet → **SCSS**
- SSR → **N**

**Solo MAC** — cambiar propietario:
```
cd ..
sudo chown -R alumnos ./ea<nrc>u<codigo>
ls -l
cd ea<nrc>u<codigo>
```

---

## 2. Instalar dependencias

```
ng add @angular/material
```
- Proceed → **Y**
- Paleta → **Rose/Red**

> The Wine Square usa tonos oscuros con borgoña/vino. Rose/Red es la más cercana.

```
npm install @ngx-translate/core @ngx-translate/http-loader --save
npm install -g json-server@0.17.4
```

---

## 3. Ejecutar el proyecto

```
ng serve --port 4200
```

---

## 4. Archivos de idioma

Crear carpetas `public/assets/i18n/` y dentro los archivos `en.json` y `es.json`:

### en.json

```json
{
  "toolbar": {
    "title": "The Wine Square Cellar Management Platform",
    "home": "Home",
    "new-preservation-item": "New Preservation Item"
  },
  "home": {
    "title": "Home",
    "content": "Engineered Products for Wine Cellars.",
    "my-wine-cellars": "My Wine Cellars"
  },
  "cellar-summary": {
    "total-bottles": "Total Bottles",
    "available-capacity": "Available Capacity",
    "empty": "Empty"
  },
  "new-preservation-item": {
    "title": "New Preservation Item",
    "subtitle": "Add Wine Bottles to Your Cellar.",
    "wine-type": "Wine Type",
    "wine": "Wine",
    "quantity": "Quantity",
    "create": "Create",
    "cancel": "Cancel",
    "error-capacity": "Quantity exceeds available capacity for this cellar."
  },
  "page-not-found": {
    "title": "Page Not Found",
    "message": "The route was not found:",
    "go-home": "Go to Home"
  }
}
```

### es.json

```json
{
  "toolbar": {
    "title": "The Wine Square Plataforma de Gestión de Bodegas",
    "home": "Inicio",
    "new-preservation-item": "Nuevo Elemento de Preservación"
  },
  "home": {
    "title": "Inicio",
    "content": "Productos de Ingeniería para Bodegas de Vino.",
    "my-wine-cellars": "Mis Bodegas de Vino"
  },
  "cellar-summary": {
    "total-bottles": "Total de Botellas",
    "available-capacity": "Capacidad Disponible",
    "empty": "Vacío"
  },
  "new-preservation-item": {
    "title": "Nuevo Elemento de Preservación",
    "subtitle": "Agregar Botellas de Vino a tu Bodega.",
    "wine-type": "Tipo de Vino",
    "wine": "Vino",
    "quantity": "Cantidad",
    "create": "Crear",
    "cancel": "Cancelar",
    "error-capacity": "La cantidad supera la capacidad disponible para esta bodega."
  },
  "page-not-found": {
    "title": "Página No Encontrada",
    "message": "La ruta no fue encontrada:",
    "go-home": "Ir al Inicio"
  }
}
```

---

## 5. Configuración del json-server

Crear carpeta `server/` en la raíz del proyecto. Dentro crear `db.json` (el del examen) y `routes.json`:

```
📂 ea<nrc>u<codigo>
  📂 server
    db.json
    routes.json
```

**routes.json:**
```json
{
  "/api/v1/*": "/$1"
}
```

En un nuevo tab del terminal ejecutar:
```
json-server --watch server/db.json --routes server/routes.json
```

Verificar:
- http://localhost:3000/api/v1/cellars
- http://localhost:3000/api/v1/preservation-items

---

## 6. Environments

```
ng generate environments
```

**environment.development.ts** y **environment.ts**:
```typescript
export const environment = {
  production: false, // true en environment.ts
  wineSquareProviderApiBaseUrl: 'http://localhost:3000/api/v1',
  wineSquareProviderCellarsEndpointPath: '/cellars',
  wineSquareProviderPreservationItemsEndpointPath: '/preservation-items',
  wineryProviderApiBaseUrl: 'https://api.sampleapis.com/wines'
};
```

---

## 7. app.config.ts

Reemplazar el contenido del archivo `app.config.ts`:

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
      const stored = localStorage.getItem('app.lang');
      const translate = inject(TranslateService);
      const lang = stored || translate.getBrowserLang() || 'en';
      return translate.use(lang);
    })
  ]
};
```

---

## 8. Estructura de carpetas

Crear en `src/app/`:

```
📂 src/app
  📂 shared
    📂 infrastructure
    📂 presentation
      📂 components
      📂 views
  📂 preservation              ← cellars + preservation items
    📂 application
    📂 domain
      📂 model
    📂 infrastructure
    📂 presentation
      📂 components
      📂 views
  📂 winery                    ← SampleAPI wines
    📂 application
    📂 domain
      📂 model
    📂 infrastructure
    📂 presentation
      📂 components
      📂 views
```

---

## 9. Clases base en shared/infrastructure

### BaseEntity

```
ng generate interface shared/infrastructure/base-entity
```

```typescript
/**
 * @summary Base interface for all domain entities.
 * @author Tu Nombre y Apellido
 */
export interface BaseEntity {
  id: number;
}
```

### BaseResponse

```
ng generate interface shared/infrastructure/base-response
```

```typescript
/**
 * @summary Base interfaces for API response and resource objects.
 * @author Tu Nombre y Apellido
 */
export interface BaseResponse {}

export interface BaseResource {
  id: number;
}
```

### BaseAssembler

```
ng generate interface shared/infrastructure/base-assembler
```

```typescript
/**
 * @summary Base assembler interface for mapping between resource and entity objects.
 * @author Tu Nombre y Apellido
 */
import { BaseResource, BaseResponse } from './base-response';
import { BaseEntity } from './base-entity';

export interface BaseAssembler<
  TEntity extends BaseEntity,
  TResource extends BaseResource,
  TResponse extends BaseResponse
> {
  toEntityFromResource(resource: TResource): TEntity;
  toResourceFromEntity(entity: TEntity): TResource;
  toEntitiesFromResponse(response: TResponse): TEntity[];
}
```

### BaseApi

```
ng generate class shared/infrastructure/base-api --skip-tests=true
```

```typescript
/**
 * @summary Abstract base class for API services.
 * @author Tu Nombre y Apellido
 */
export abstract class BaseApi {}
```

### BaseApiEndpoint

```
ng generate class shared/infrastructure/base-api-endpoint --skip-tests=true
```

```typescript
/**
 * @summary Abstract base class providing standard CRUD operations for API endpoints.
 * @author Tu Nombre y Apellido
 */
import { HttpClient, HttpErrorResponse } from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError, map } from 'rxjs/operators';
import { BaseEntity } from './base-entity';
import { BaseResource, BaseResponse } from './base-response';
import { BaseAssembler } from './base-assembler';

export abstract class BaseApiEndpoint<
  TEntity extends BaseEntity,
  TResource extends BaseResource,
  TResponse extends BaseResponse,
  TAssembler extends BaseAssembler<TEntity, TResource, TResponse>
> {
  constructor(
    protected http: HttpClient,
    protected endpointUrl: string,
    protected assembler: TAssembler
  ) {}

  getAll(): Observable<TEntity[]> {
    return this.http.get<TResponse | TResource[]>(this.endpointUrl).pipe(
      map(response => {
        if (Array.isArray(response)) {
          return response.map(resource => this.assembler.toEntityFromResource(resource));
        }
        return this.assembler.toEntitiesFromResponse(response as TResponse);
      }),
      catchError(this.handleError('Failed to fetch entities'))
    );
  }

  getById(id: number): Observable<TEntity> {
    return this.http.get<TResource>(`${this.endpointUrl}/${id}`).pipe(
      map(resource => this.assembler.toEntityFromResource(resource)),
      catchError(this.handleError('Failed to fetch entity'))
    );
  }

  create(entity: TEntity): Observable<TEntity> {
    const resource = this.assembler.toResourceFromEntity(entity);
    return this.http.post<TResource>(this.endpointUrl, resource).pipe(
      map(created => this.assembler.toEntityFromResource(created)),
      catchError(this.handleError('Failed to create entity'))
    );
  }

  protected handleError(operation: string) {
    return (error: HttpErrorResponse): Observable<never> => {
      let errorMessage = operation;
      if (error.status === 404) errorMessage = `${operation}: Resource not found`;
      else if (error.error instanceof ErrorEvent) errorMessage = `${operation}: ${error.error.message}`;
      else errorMessage = `${operation}: ${error.statusText || 'Unexpected error'}`;
      return throwError(() => new Error(errorMessage));
    };
  }
}
```

---

## 10. Domain Layer — Entities

### Cellar entity

```
ng generate class preservation/domain/model/cellar --type=entity --skip-tests=true
```

```typescript
/**
 * @summary Cellar entity representing a wine storage unit in the preservation bounded context.
 * @author Tu Nombre y Apellido
 */
import { BaseEntity } from '../../../shared/infrastructure/base-entity';

export class Cellar implements BaseEntity {
  private _id: number;
  private _name: string;
  private _wineType: string;
  private _capacity: number;

  constructor(cellar: { id: number; name: string; wineType: string; capacity: number }) {
    this._id = cellar.id;
    this._name = cellar.name;
    this._wineType = cellar.wineType;
    this._capacity = cellar.capacity;
  }

  get id() { return this._id; }
  set id(v: number) { this._id = v; }
  get name() { return this._name; }
  set name(v: string) { this._name = v; }
  get wineType() { return this._wineType; }
  set wineType(v: string) { this._wineType = v; }
  get capacity() { return this._capacity; }
  set capacity(v: number) { this._capacity = v; }
}
```

### PreservationItem entity

```
ng generate class preservation/domain/model/preservation-item --type=entity --skip-tests=true
```

```typescript
/**
 * @summary PreservationItem entity representing a wine bottle record in a cellar.
 * @author Tu Nombre y Apellido
 */
import { BaseEntity } from '../../../shared/infrastructure/base-entity';

export class PreservationItem implements BaseEntity {
  private _id: number;
  private _cellarId: number;
  private _wineType: string;
  private _wineId: number;
  private _wineName: string;
  private _quantity: number;
  private _registeredAt: string;

  constructor(item: {
    id: number; cellarId: number; wineType: string; wineId: number;
    wineName: string; quantity: number; registeredAt: string;
  }) {
    this._id = item.id;
    this._cellarId = item.cellarId;
    this._wineType = item.wineType;
    this._wineId = item.wineId;
    this._wineName = item.wineName;
    this._quantity = item.quantity;
    this._registeredAt = item.registeredAt;
  }

  get id() { return this._id; }
  set id(v: number) { this._id = v; }
  get cellarId() { return this._cellarId; }
  set cellarId(v: number) { this._cellarId = v; }
  get wineType() { return this._wineType; }
  set wineType(v: string) { this._wineType = v; }
  get wineId() { return this._wineId; }
  set wineId(v: number) { this._wineId = v; }
  get wineName() { return this._wineName; }
  set wineName(v: string) { this._wineName = v; }
  get quantity() { return this._quantity; }
  set quantity(v: number) { this._quantity = v; }
  get registeredAt() { return this._registeredAt; }
  set registeredAt(v: string) { this._registeredAt = v; }
}
```

### Wine entity (winery bounded context)

```
ng generate class winery/domain/model/wine --type=entity --skip-tests=true
```

```typescript
/**
 * @summary Wine entity representing a wine from the SampleAPI winery service.
 * @author Tu Nombre y Apellido
 */
import { BaseEntity } from '../../../shared/infrastructure/base-entity';

export class Wine implements BaseEntity {
  private _id: number;
  private _winery: string;
  private _wine: string;
  private _location: string;
  private _image: string;
  private _ratingAverage: string;
  private _ratingReviews: string;

  constructor(wine: {
    id: number; winery: string; wine: string; location: string;
    image: string; ratingAverage: string; ratingReviews: string;
  }) {
    this._id = wine.id;
    this._winery = wine.winery;
    this._wine = wine.wine;
    this._location = wine.location;
    this._image = wine.image;
    this._ratingAverage = wine.ratingAverage;
    this._ratingReviews = wine.ratingReviews;
  }

  get id() { return this._id; }
  set id(v: number) { this._id = v; }
  get winery() { return this._winery; }
  set winery(v: string) { this._winery = v; }
  get wine() { return this._wine; }
  set wine(v: string) { this._wine = v; }
  get location() { return this._location; }
  set location(v: string) { this._location = v; }
  get image() { return this._image; }
  set image(v: string) { this._image = v; }
  get ratingAverage() { return this._ratingAverage; }
  set ratingAverage(v: string) { this._ratingAverage = v; }
  get ratingReviews() { return this._ratingReviews; }
  set ratingReviews(v: string) { this._ratingReviews = v; }
}
```

---

## 11. Infrastructure Layer — Responses

### CellarsResponse

```
ng generate interface preservation/infrastructure/cellars-response
```

```typescript
/**
 * @summary Cellar response and resource interfaces for REST API deserialization.
 * @author Tu Nombre y Apellido
 */
import { BaseResource, BaseResponse } from '../../shared/infrastructure/base-response';

export interface CellarsResponse extends BaseResponse {
  cellars: CellarResource[];
}

export interface CellarResource extends BaseResource {
  id: number;
  name: string;
  wineType: string;
  capacity: number;
}
```

### PreservationItemsResponse

```
ng generate interface preservation/infrastructure/preservation-items-response
```

```typescript
/**
 * @summary PreservationItem response and resource interfaces for REST API deserialization.
 * @author Tu Nombre y Apellido
 */
import { BaseResource, BaseResponse } from '../../shared/infrastructure/base-response';

export interface PreservationItemsResponse extends BaseResponse {
  preservationItems: PreservationItemResource[];
}

export interface PreservationItemResource extends BaseResource {
  id: number;
  cellarId: number;
  wineType: string;
  wineId: number;
  wineName: string;
  quantity: number;
  registeredAt: string;
}
```

### WinesResponse

```
ng generate interface winery/infrastructure/wines-response
```

```typescript
/**
 * @summary Wine response and resource interfaces for SampleAPI deserialization.
 * @author Tu Nombre y Apellido
 */
import { BaseResource, BaseResponse } from '../../shared/infrastructure/base-response';

export interface WinesResponse extends BaseResponse {
  wines: WineResource[];
}

export interface WineResource extends BaseResource {
  id: number;
  winery: string;
  wine: string;
  location: string;
  image: string;
  rating: {
    average: string;
    reviews: string;
  };
}
```

---

## 12. Infrastructure Layer — Assemblers

### CellarAssembler

```
ng generate class preservation/infrastructure/cellar-assembler --skip-tests=true
```

```typescript
/**
 * @summary Assembler for mapping CellarResource to Cellar entity.
 * @author Tu Nombre y Apellido
 */
import { BaseAssembler } from '../../shared/infrastructure/base-assembler';
import { Cellar } from '../domain/model/cellar.entity';
import { CellarResource, CellarsResponse } from './cellars-response';

export class CellarAssembler implements BaseAssembler<Cellar, CellarResource, CellarsResponse> {
  toEntitiesFromResponse(response: CellarsResponse): Cellar[] {
    return response.cellars.map(r => this.toEntityFromResource(r));
  }

  toEntityFromResource(resource: CellarResource): Cellar {
    return new Cellar({
      id: resource.id,
      name: resource.name,
      wineType: resource.wineType,
      capacity: resource.capacity
    });
  }

  toResourceFromEntity(entity: Cellar): CellarResource {
    return {
      id: entity.id,
      name: entity.name,
      wineType: entity.wineType,
      capacity: entity.capacity
    } as CellarResource;
  }
}
```

### PreservationItemAssembler

```
ng generate class preservation/infrastructure/preservation-item-assembler --skip-tests=true
```

```typescript
/**
 * @summary Assembler for mapping PreservationItemResource to PreservationItem entity.
 * @author Tu Nombre y Apellido
 */
import { BaseAssembler } from '../../shared/infrastructure/base-assembler';
import { PreservationItem } from '../domain/model/preservation-item.entity';
import { PreservationItemResource, PreservationItemsResponse } from './preservation-items-response';

export class PreservationItemAssembler implements BaseAssembler<PreservationItem, PreservationItemResource, PreservationItemsResponse> {
  toEntitiesFromResponse(response: PreservationItemsResponse): PreservationItem[] {
    return response.preservationItems.map(r => this.toEntityFromResource(r));
  }

  toEntityFromResource(resource: PreservationItemResource): PreservationItem {
    return new PreservationItem({
      id: resource.id,
      cellarId: resource.cellarId,
      wineType: resource.wineType,
      wineId: resource.wineId,
      wineName: resource.wineName,
      quantity: resource.quantity,
      registeredAt: resource.registeredAt
    });
  }

  toResourceFromEntity(entity: PreservationItem): PreservationItemResource {
    return {
      id: entity.id,
      cellarId: entity.cellarId,
      wineType: entity.wineType,
      wineId: entity.wineId,
      wineName: entity.wineName,
      quantity: entity.quantity,
      registeredAt: entity.registeredAt
    } as PreservationItemResource;
  }
}
```

### WineAssembler

```
ng generate class winery/infrastructure/wine-assembler --skip-tests=true
```

```typescript
/**
 * @summary Assembler for mapping WineResource to Wine entity.
 * @author Tu Nombre y Apellido
 */
import { BaseAssembler } from '../../shared/infrastructure/base-assembler';
import { Wine } from '../domain/model/wine.entity';
import { WineResource, WinesResponse } from './wines-response';

export class WineAssembler implements BaseAssembler<Wine, WineResource, WinesResponse> {
  toEntitiesFromResponse(response: WinesResponse): Wine[] {
    return response.wines.map(r => this.toEntityFromResource(r));
  }

  toEntityFromResource(resource: WineResource): Wine {
    return new Wine({
      id: resource.id,
      winery: resource.winery,
      wine: resource.wine,
      location: resource.location,
      image: resource.image,
      ratingAverage: resource.rating?.average ?? '',
      ratingReviews: resource.rating?.reviews ?? ''
    });
  }

  toResourceFromEntity(entity: Wine): WineResource {
    return {
      id: entity.id,
      winery: entity.winery,
      wine: entity.wine,
      location: entity.location,
      image: entity.image,
      rating: {
        average: entity.ratingAverage,
        reviews: entity.ratingReviews
      }
    } as WineResource;
  }
}
```

---

## 13. Infrastructure Layer — API Endpoints

### CellarsApiEndpoint

```
ng generate class preservation/infrastructure/cellars-api-endpoint --skip-tests=true
```

```typescript
/**
 * @summary API endpoint for cellar data access.
 * @author Tu Nombre y Apellido
 */
import { HttpClient } from '@angular/common/http';
import { environment } from '../../../environments/environment';
import { BaseApiEndpoint } from '../../shared/infrastructure/base-api-endpoint';
import { Cellar } from '../domain/model/cellar.entity';
import { CellarResource, CellarsResponse } from './cellars-response';
import { CellarAssembler } from './cellar-assembler';

export class CellarsApiEndpoint extends BaseApiEndpoint<Cellar, CellarResource, CellarsResponse, CellarAssembler> {
  constructor(http: HttpClient) {
    super(
      http,
      `${environment.wineSquareProviderApiBaseUrl}${environment.wineSquareProviderCellarsEndpointPath}`,
      new CellarAssembler()
    );
  }
}
```

### PreservationItemsApiEndpoint

```
ng generate class preservation/infrastructure/preservation-items-api-endpoint --skip-tests=true
```

```typescript
/**
 * @summary API endpoint for preservation item data access.
 * @author Tu Nombre y Apellido
 */
import { HttpClient } from '@angular/common/http';
import { environment } from '../../../environments/environment';
import { BaseApiEndpoint } from '../../shared/infrastructure/base-api-endpoint';
import { PreservationItem } from '../domain/model/preservation-item.entity';
import { PreservationItemResource, PreservationItemsResponse } from './preservation-items-response';
import { PreservationItemAssembler } from './preservation-item-assembler';

export class PreservationItemsApiEndpoint extends BaseApiEndpoint<PreservationItem, PreservationItemResource, PreservationItemsResponse, PreservationItemAssembler> {
  constructor(http: HttpClient) {
    super(
      http,
      `${environment.wineSquareProviderApiBaseUrl}${environment.wineSquareProviderPreservationItemsEndpointPath}`,
      new PreservationItemAssembler()
    );
  }
}
```

### WinesApiEndpoint

```
ng generate class winery/infrastructure/wines-api-endpoint --skip-tests=true
```

```typescript
/**
 * @summary API endpoint for wine data access from SampleAPI.
 * @author Tu Nombre y Apellido
 */
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';
import { environment } from '../../../environments/environment';
import { Wine } from '../domain/model/wine.entity';
import { WineResource } from './wines-response';
import { WineAssembler } from './wine-assembler';

export class WinesApiEndpoint {
  private assembler = new WineAssembler();

  constructor(private http: HttpClient) {}

  getByType(wineType: string): Observable<Wine[]> {
    return this.http.get<WineResource[]>(`${environment.wineryProviderApiBaseUrl}/${wineType}`).pipe(
      map(resources => resources.map(r => this.assembler.toEntityFromResource(r)))
    );
  }
}
```

---

## 14. Infrastructure Layer — API Services

### PreservationApi Service

```
ng generate service preservation/infrastructure/preservation-api --skip-tests=true
```

Agregar imports:

```typescript
import { inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { BaseApi } from '../../shared/infrastructure/base-api';
import { Cellar } from '../domain/model/cellar.entity';
import { PreservationItem } from '../domain/model/preservation-item.entity';
import { CellarsApiEndpoint } from './cellars-api-endpoint';
import { PreservationItemsApiEndpoint } from './preservation-items-api-endpoint';
```

Reemplazar contenido de la clase `PreservationApiService`:

```typescript
/**
 * @summary API service for preservation bounded context data access.
 * @author Tu Nombre y Apellido
 */
private http: HttpClient = inject(HttpClient);
private cellarsEndpoint: CellarsApiEndpoint = new CellarsApiEndpoint(this.http);
private preservationItemsEndpoint: PreservationItemsApiEndpoint = new PreservationItemsApiEndpoint(this.http);

getCellars(): Observable<Cellar[]> {
  return this.cellarsEndpoint.getAll();
}

getPreservationItems(): Observable<PreservationItem[]> {
  return this.preservationItemsEndpoint.getAll();
}

createPreservationItem(item: PreservationItem): Observable<PreservationItem> {
  return this.preservationItemsEndpoint.create(item);
}
```

Agregar `extends BaseApi` a la clase.

### WineryApi Service

```
ng generate service winery/infrastructure/winery-api --skip-tests=true
```

Agregar imports:

```typescript
import { inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { BaseApi } from '../../shared/infrastructure/base-api';
import { Wine } from '../domain/model/wine.entity';
import { WinesApiEndpoint } from './wines-api-endpoint';
```

Reemplazar contenido de la clase `WineryApiService`:

```typescript
/**
 * @summary API service for winery bounded context data access from SampleAPI.
 * @author Tu Nombre y Apellido
 */
private http: HttpClient = inject(HttpClient);
private winesEndpoint: WinesApiEndpoint = new WinesApiEndpoint(this.http);

getWinesByType(wineType: string): Observable<Wine[]> {
  return this.winesEndpoint.getByType(wineType);
}
```

Agregar `extends BaseApi` a la clase.

---

## 15. Application Layer — View Models

Crear archivo `preservation/application/cellar-summary.ts`:

```typescript
/**
 * @summary View model representing a cellar with its preservation items summary.
 * @author Tu Nombre y Apellido
 */
import { PreservationItem } from '../domain/model/preservation-item.entity';

export interface CellarSummary {
  cellarId: number;
  cellarName: string;
  wineType: string;
  capacity: number;
  items: PreservationItem[];
  totalBottles: number;
  availableCapacity: number;
}
```

---

## 16. Application Layer — Store Services

### PreservationStore

```
ng generate service preservation/application/preservation-store --skip-tests=true
```

Agregar imports:

```typescript
import { computed, inject, Injectable, signal } from '@angular/core';
import { takeUntilDestroyed } from '@angular/core/rxjs-interop';
import { retry } from 'rxjs';
import { Cellar } from '../domain/model/cellar.entity';
import { PreservationItem } from '../domain/model/preservation-item.entity';
import { PreservationApiService } from '../infrastructure/preservation-api.service';
import { CellarSummary } from './cellar-summary';
```

Reemplazar contenido de la clase `PreservationStoreService`:

```typescript
/**
 * @summary Store service for preservation state management using Angular Signals.
 * @author Tu Nombre y Apellido
 */
private cellarsSignal = signal<Cellar[]>([]);
private itemsSignal = signal<PreservationItem[]>([]);
private loadingSignal = signal<boolean>(false);
private errorSignal = signal<string | null>(null);

private api: PreservationApiService = inject(PreservationApiService);

readonly cellars = this.cellarsSignal.asReadonly();
readonly items = this.itemsSignal.asReadonly();
readonly loading = this.loadingSignal.asReadonly();
readonly error = this.errorSignal.asReadonly();

readonly cellarSummaries = computed<CellarSummary[]>(() => {
  return this.cellars().map(cellar => {
    const cellarItems = this.items().filter(i => i.cellarId === cellar.id);
    const totalBottles = cellarItems.reduce((sum, i) => sum + i.quantity, 0);
    return {
      cellarId: cellar.id,
      cellarName: cellar.name,
      wineType: cellar.wineType,
      capacity: cellar.capacity,
      items: cellarItems,
      totalBottles,
      availableCapacity: cellar.capacity - totalBottles
    };
  });
});

constructor() {
  this.loadAll();
}

getCellarByWineType(wineType: string) {
  return computed(() => this.cellars().find(c => c.wineType === wineType));
}

addPreservationItem(input: {
  wineType: string; wineId: number; wineName: string; quantity: number;
}): boolean {
  const cellar = this.cellars().find(c => c.wineType === input.wineType);
  if (!cellar) {
    this.errorSignal.set('No cellar found for this wine type');
    return false;
  }

  const summary = this.cellarSummaries().find(s => s.cellarId === cellar.id);
  if (!summary || input.quantity > summary.availableCapacity) {
    this.errorSignal.set('Quantity exceeds available capacity for this cellar.');
    return false;
  }

  const item = new PreservationItem({
    id: 0,
    cellarId: cellar.id,
    wineType: input.wineType,
    wineId: input.wineId,
    wineName: input.wineName,
    quantity: input.quantity,
    registeredAt: new Date().toISOString()
  });

  this.loadingSignal.set(true);
  this.api.createPreservationItem(item).pipe(retry(3)).subscribe({
    next: created => {
      this.itemsSignal.update(items => [...items, created]);
      this.loadingSignal.set(false);
    },
    error: e => {
      this.errorSignal.set(e.message || 'Failed to create preservation item');
      this.loadingSignal.set(false);
    }
  });
  return true;
}

private loadAll(): void {
  this.loadingSignal.set(true);
  this.api.getCellars().pipe(takeUntilDestroyed(), retry(3)).subscribe({
    next: cellars => { this.cellarsSignal.set(cellars); this.loadingSignal.set(false); },
    error: e => { this.errorSignal.set(e.message); this.loadingSignal.set(false); }
  });
  this.api.getPreservationItems().pipe(takeUntilDestroyed(), retry(3)).subscribe({
    next: items => { this.itemsSignal.set(items); this.loadingSignal.set(false); },
    error: e => { this.errorSignal.set(e.message); this.loadingSignal.set(false); }
  });
}
```

### WineryStore

```
ng generate service winery/application/winery-store --skip-tests=true
```

Agregar imports:

```typescript
import { inject, Injectable, signal } from '@angular/core';
import { Observable } from 'rxjs';
import { Wine } from '../domain/model/wine.entity';
import { WineryApiService } from '../infrastructure/winery-api.service';
```

Reemplazar contenido de la clase `WineryStoreService`:

```typescript
/**
 * @summary Store service for winery state management using Angular Signals.
 * @author Tu Nombre y Apellido
 */
private winesSignal = signal<Wine[]>([]);
private loadingSignal = signal<boolean>(false);
private errorSignal = signal<string | null>(null);

private api: WineryApiService = inject(WineryApiService);

readonly wines = this.winesSignal.asReadonly();
readonly loading = this.loadingSignal.asReadonly();
readonly error = this.errorSignal.asReadonly();

loadByType(wineType: string): void {
  this.winesSignal.set([]);
  this.loadingSignal.set(true);
  this.api.getWinesByType(wineType).subscribe({
    next: wines => { this.winesSignal.set(wines); this.loadingSignal.set(false); },
    error: e => { this.errorSignal.set(e.message); this.loadingSignal.set(false); }
  });
}
```

---

## 17. Routing

### preservation.routes.ts

Crear archivo `preservation/presentation/views/preservation.routes.ts`:

```typescript
/**
 * @summary Routing configuration for the preservation bounded context.
 * @author Tu Nombre y Apellido
 */
import { Routes } from '@angular/router';

const newPreservationItem = () =>
  import('./new-preservation-item/new-preservation-item.component')
    .then(m => m.NewPreservationItemComponent);

export const preservationRoutes: Routes = [
  { path: 'items/new', loadComponent: newPreservationItem }
];
```

### app.routes.ts

Reemplazar el contenido del archivo `app.routes.ts`:

```typescript
/**
 * @summary Application routing configuration with semantic routes and child routes by bounded context.
 * @author Tu Nombre y Apellido
 */
import { Routes } from '@angular/router';

const pageNotFound = () =>
  import('./shared/presentation/views/page-not-found/page-not-found.component')
    .then(m => m.PageNotFoundComponent);

export const routes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full' },
  {
    path: 'home',
    loadComponent: () =>
      import('./shared/presentation/views/home/home.component')
        .then(m => m.HomeComponent)
  },
  {
    path: 'preservation',
    loadChildren: () =>
      import('./preservation/presentation/views/preservation.routes')
        .then(m => m.preservationRoutes)
  },
  { path: '**', loadComponent: pageNotFound }
];
```

---

## 18. Generar componentes

```
ng generate component shared/presentation/components/toolbar --skip-tests=true
ng generate component shared/presentation/components/language-switcher --skip-tests=true
ng generate component shared/presentation/components/layout --skip-tests=true
ng generate component shared/presentation/views/home --skip-tests=true
ng generate component shared/presentation/views/page-not-found --skip-tests=true
ng generate component preservation/presentation/components/cellar-summary --skip-tests=true
ng generate component preservation/presentation/views/new-preservation-item --skip-tests=true
```

---

## 19. Componentes

### LanguageSwitcherComponent

Imports a agregar: `TranslateService`, `MatButtonToggleModule`

**language-switcher.component.ts** — contenido de la clase:
```typescript
/**
 * @summary Language switcher component for toggling between EN and ES.
 * @author Tu Nombre y Apellido
 */
currentLang = 'en';
languages = ['en', 'es'];

constructor(private translate: TranslateService) {
  this.currentLang = translate.currentLang || 'en';
}

useLanguage(language: string): void {
  this.currentLang = language;
  this.translate.use(language);
  localStorage.setItem('app.lang', language);
}
```

**language-switcher.component.html:**
```html
<mat-button-toggle-group
  [value]="currentLang"
  appearance="standard"
  aria-label="Language selector"
  name="language">
  @for (language of languages; track language) {
    <mat-button-toggle [value]="language" [aria-label]="language" (click)="useLanguage(language)">
      {{ language.toUpperCase() }}
    </mat-button-toggle>
  }
</mat-button-toggle-group>
```

---

### LayoutComponent

Imports a agregar: `RouterOutlet`, `RouterLink`, `RouterLinkActive`, `MatToolbarModule`, `MatButtonModule`, `TranslateModule`, `LanguageSwitcherComponent`

**layout.component.ts** — contenido de la clase:
```typescript
/**
 * @summary Layout component wrapping the toolbar and router outlet.
 * @author Tu Nombre y Apellido
 */
options = [
  { link: '/home', label: 'toolbar.home' },
  { link: '/preservation/items/new', label: 'toolbar.new-preservation-item' }
];
```

**layout.component.html:**
```html
<mat-toolbar style="background-color: #6d1f2f; color: white;" role="navigation" aria-label="The Wine Square toolbar">
  <img
    src="https://img.logo.dev/thewinesquare.com?token=pk_X0HHj7aqT5KHkXiG-EzL0A"
    alt="The Wine Square logo"
    height="36"
    aria-hidden="true"
    style="margin-right: 8px; background: white; border-radius: 4px; padding: 2px;"
  />
  <span>{{ 'toolbar.title' | translate }}</span>

  <span style="flex: 1 1 auto;"></span>

  <nav aria-label="Main navigation" style="display: flex; gap: 8px;">
    @for (option of options; track option.label) {
      <a mat-button [routerLink]="option.link" routerLinkActive="active" style="color: white;">
        {{ option.label | translate }}
      </a>
    }
  </nav>

  <span style="margin-left: 16px;">
    <app-language-switcher />
  </span>
</mat-toolbar>

<router-outlet />
```

**layout.component.scss:**
```scss
mat-toolbar {
  position: sticky;
  top: 0;
  z-index: 100;
}
```

---

### HomeComponent

Imports a agregar: `Component, inject`, `TranslateModule`, `MatGridListModule`, `PreservationStoreService`, `CellarSummaryComponent`

**home.component.ts** — contenido de la clase:
```typescript
/**
 * @summary Home view displaying My Wine Cellars section with cellar summaries.
 * @author Tu Nombre y Apellido
 */
protected store = inject(PreservationStoreService);
```

**home.component.html:**
```html
<main aria-label="Home page" style="padding: 24px;">
  <h1>{{ 'home.title' | translate }}</h1>
  <p>{{ 'home.content' | translate }}</p>

  <section aria-labelledby="cellars-title">
    <h2 id="cellars-title">{{ 'home.my-wine-cellars' | translate }}</h2>
    <mat-grid-list cols="2" rowHeight="320px" gutterSize="16px">
      @for (summary of store.cellarSummaries(); track summary.cellarId) {
        <mat-grid-tile>
          <app-cellar-summary [summary]="summary" />
        </mat-grid-tile>
      }
    </mat-grid-list>
  </section>
</main>
```

---

### CellarSummaryComponent

Imports a agregar: `input, InputSignal`, `MatCardModule`, `TranslateModule`, `CellarSummary`

**cellar-summary.component.ts** — contenido de la clase:
```typescript
/**
 * @summary Component displaying a cellar card with its wine items and capacity stats.
 * @author Tu Nombre y Apellido
 */
summary: InputSignal<CellarSummary> = input.required<CellarSummary>();
```

**cellar-summary.component.html:**
```html
<mat-card style="width: 100%;" aria-label="Cellar summary card">
  <mat-card-header>
    <mat-card-title>{{ summary().cellarName }}</mat-card-title>
  </mat-card-header>
  <mat-card-content>
    @if (summary().items.length === 0) {
      <p>{{ 'cellar-summary.empty' | translate }}</p>
    } @else {
      @for (item of summary().items; track item.id) {
        <p><strong>{{ item.wineName }}</strong> — {{ item.quantity }}</p>
      }
    }
  </mat-card-content>
  <mat-card-footer style="padding: 8px 16px;">
    <p>
      <strong>{{ 'cellar-summary.total-bottles' | translate }}:</strong>
      {{ summary().totalBottles }}
    </p>
    <p>
      <strong>{{ 'cellar-summary.available-capacity' | translate }}:</strong>
      {{ summary().availableCapacity }}
    </p>
  </mat-card-footer>
</mat-card>
```

---

### NewPreservationItemComponent

Imports a agregar: `Component, inject, OnInit, signal, WritableSignal, effect`, `FormBuilder, FormGroup, ReactiveFormsModule, Validators`, `Router`, `MatFormFieldModule`, `MatInputModule`, `MatButtonModule`, `MatSelectModule`, `TranslateModule`, `PreservationStoreService`, `WineryStoreService`, `Wine`

**new-preservation-item.component.ts** — contenido de la clase:
```typescript
/**
 * @summary View component for creating a new preservation item with wine selection from SampleAPI.
 * @author Tu Nombre y Apellido
 */
private fb: FormBuilder = inject(FormBuilder);
private router: Router = inject(Router);
protected preservationStore: PreservationStoreService = inject(PreservationStoreService);
protected wineryStore: WineryStoreService = inject(WineryStoreService);

readonly wineTypes: string[] = ['reds', 'whites', 'sparkling', 'rose', 'dessert', 'port'];

form: FormGroup = this.fb.group({
  wineType: [null, Validators.required],
  wineId: [null, Validators.required],
  wineName: [''],
  quantity: [null, [Validators.required, Validators.min(1)]]
});

ngOnInit(): void {
  this.form.get('wineType')?.valueChanges.subscribe(wineType => {
    if (wineType) {
      this.wineryStore.loadByType(wineType);
      this.form.get('wineId')?.reset();
    }
  });

  this.form.get('wineId')?.valueChanges.subscribe(wineId => {
    const wine = this.wineryStore.wines().find(w => w.id === wineId);
    if (wine) this.form.get('wineName')?.setValue(wine.wine);
  });
}

onSubmit(): void {
  if (this.form.invalid) return;
  const { wineType, wineId, wineName, quantity } = this.form.value;
  const ok = this.preservationStore.addPreservationItem({ wineType, wineId, wineName, quantity });
  if (ok) this.router.navigate(['/home']);
}

onCancel(): void {
  this.router.navigate(['/home']);
}
```

**new-preservation-item.component.html:**
```html
<main aria-label="New preservation item form" style="padding: 24px; max-width: 500px;">
  <h1>{{ 'new-preservation-item.title' | translate }}</h1>
  <h2>{{ 'new-preservation-item.subtitle' | translate }}</h2>

  <form [formGroup]="form" (ngSubmit)="onSubmit()" aria-label="Create new preservation item">

    <mat-form-field appearance="outline" style="width: 100%;">
      <mat-label>{{ 'new-preservation-item.wine-type' | translate }}</mat-label>
      <mat-select formControlName="wineType" aria-label="Select wine type">
        @for (type of wineTypes; track type) {
          <mat-option [value]="type">{{ type }}</mat-option>
        }
      </mat-select>
    </mat-form-field>

    <mat-form-field appearance="outline" style="width: 100%;">
      <mat-label>{{ 'new-preservation-item.wine' | translate }}</mat-label>
      <mat-select formControlName="wineId" aria-label="Select wine" [disabled]="!form.get('wineType')?.value">
        @for (wine of wineryStore.wines(); track wine.id) {
          <mat-option [value]="wine.id">{{ wine.wine }}</mat-option>
        }
      </mat-select>
    </mat-form-field>

    <mat-form-field appearance="outline" style="width: 100%;">
      <mat-label>{{ 'new-preservation-item.quantity' | translate }}</mat-label>
      <input matInput type="number" formControlName="quantity" aria-label="Quantity" placeholder="e.g. 10" />
    </mat-form-field>

    @if (preservationStore.error()) {
      <p style="color: red;" role="alert">{{ preservationStore.error() }}</p>
    }

    <div style="display: flex; gap: 12px; margin-top: 16px;">
      <button mat-raised-button color="primary" type="submit" [disabled]="form.invalid" aria-label="Create preservation item">
        {{ 'new-preservation-item.create' | translate }}
      </button>
      <button mat-button type="button" (click)="onCancel()" aria-label="Cancel">
        {{ 'new-preservation-item.cancel' | translate }}
      </button>
    </div>

  </form>
</main>
```

---

### PageNotFoundComponent

Imports a agregar: `inject`, `Router, RouterLink`, `MatButtonModule`, `TranslateModule`

**page-not-found.component.ts** — contenido de la clase:
```typescript
/**
 * @summary Page not found view for unsupported navigation routes.
 * @author Tu Nombre y Apellido
 */
private router: Router = inject(Router);
currentUrl: string = this.router.url;
```

**page-not-found.component.html:**
```html
<main aria-label="Page not found" style="padding: 48px; text-align: center;">
  <h1>{{ 'page-not-found.title' | translate }}</h1>
  <p>{{ 'page-not-found.message' | translate }} <code>{{ currentUrl }}</code></p>
  <a mat-raised-button color="primary" routerLink="/home" aria-label="Return to home">
    {{ 'page-not-found.go-home' | translate }}
  </a>
</main>
```

---

### AppComponent

Imports a agregar: `LayoutComponent`

**app.component.ts** — contenido de la clase:
```typescript
/**
 * @summary Root application component.
 * @author Tu Nombre y Apellido
 */
```

**app.component.html:**
```html
<app-layout />
```

---

## 20. styles.scss

```scss
html, body {
  height: 100%;
  margin: 0;
  font-family: Roboto, "Helvetica Neue", sans-serif;
  background-color: #fafafa;
}
```

---

## 21. README.md

```markdown
# The Wine Square — Cellar Management Platform

Web application for wine cellar management built for The Wine Square.

## Description

Angular frontend providing wine cellar utilization analytics and preservation
item management. Consumes a local REST API (json-server) and the SampleAPI
winery service. Follows domain-driven architecture with layered and
component-based design patterns.

## Features

- Toolbar with Logo.dev logo, navigation and EN/ES language switcher
- Home view with My Wine Cellars grid showing cellar summaries
- Total Bottles and Available Capacity per cellar
- New Preservation Item form with dynamic wine selection from SampleAPI
- Automatic cellar assignment by wine type
- Validation: quantity must not exceed available capacity
- Page Not Found view with invalid path display
- Full i18n support (EN/ES)

## Tech Stack

- Angular 21+
- Angular Material
- @ngx-translate/core
- json-server 0.17.4 (fake REST API)
- SampleAPI (https://api.sampleapis.com/wines)
- TypeScript / Angular Signals

## Author

**Tu Nombre y Apellido**
Universidad Peruana de Ciencias Aplicadas (UPC)
Course: Desarrollo de Aplicaciones Open Source (1ASI0729)
```

---

## 22. Preparación del entregable

```
# Mac/Linux
rm -rf node_modules

# Windows
rmdir /s /q node_modules
```

Nombre del zip: `ea<nrc>u<codigo>.zip`
Ejemplo: `ea20262u20241a972.zip`

---

## Referencias

- https://angular.dev/guide/routing/common-router-tasks
- https://angular.dev/guide/http
- https://ngx-translate.org/
- https://material.angular.dev/guide/theming
- https://material.angular.io/components/card/overview
- https://material.angular.io/components/grid-list/overview
- https://material.angular.io/components/toolbar/overview
- https://github.com/typicode/json-server/tree/v0
- https://api.sampleapis.com/wines
- https://tsdoc.org/
