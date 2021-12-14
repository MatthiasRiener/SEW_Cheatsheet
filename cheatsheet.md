# Allgemeine Befehle
### Projekt anlegen
```
ng new <project-name>
```

### Projekt starten (Port 4200)
```
ng serve
```

### Component erstellen
```
ng generate component <component-name>
```

### Service erstellen
```
ng generate service <service-name>
```

### Class erstellen
```
ng generate class <class-name>
```

### Material Support hinzufügen
```
ng add @angular/material
```

### Material Component erstellen
```
ng generate @angular/material:<material-element> <component-name>

Andere Möglichkeiten:
- address-form
- navigation
- dashboard
- table
- tree
- drag-drop
```
Genauerer Beschreibung in den einzelnen Kapiteln

# Components Databinding

## @Input
1. Values weitergeben beim Component verwenden
```HTML
<app-greeting
    vorname="Max"
    [nachname]="'Muster'"
>
</app-greeting>
```
2. Im Typescript

```typescript
export class GreetingComponent {
    @Input('vorname') firstname | undefined;
    @Input('nachname') lastname | undefined;
}
```
3. Im HTML von der GreetingComponent
```HTML
<p>Hello {{firstname}} {{lastname}}!</p>
```

## @Output
* Übergeordnete Komponenten über Ereignisse zu informieren

1. Greeting Component
```js
import {EventEmitter} from '@angular/core';

export class GreetingComponent {
    @Output() loggedOut = new EventEmitter();

    logout() {
        this.loggedOut.emit();
    }
}
```
2. Event im Greeting auslösen
```HTML
<button (click)="logout()">Logout :(<button>
```

3. Event abfangen in Parentcomponent
```HTML
<app-greeting
(loggedOut)="handleLogout()"
>
</app-greeting>
```

## Local References
* Wird über Template an TS-File übergeben
* Syntax: #Bezeichner
```HTML
<input type="text" #nicknameInput>
<button (click)="login(nicknameInput)">Login</button>
Hello {{ nickname }}`!
```

```javascript
export class GreetingComponent {
    nickname;

    login(nicknameInput: HTMLInputElement) {
        this.nickname = nicknameInput.value;
    }
}
```

## ViewChild
* Zugriff auf HTML Elemente
  
```javascript
export class GreetingComponent {
    @ViewChild('nicknameInput', {static: true | false}) nicknameInput: ElementRef;
    nickname;

    login() {
        this.nickname = this.nicknameInput.nativeElement.value;
    }
}
```

```HTML
<input type="text" #nicknameInput>
<button (click)="login()">Logout</button>
```


# HTTP Service erstellen
* Dependency Injection!

## Setup
```
ng generate service <service-name>
```
## Konfiguration für HTTP Service
## Imports
Im service-name.ts
```js
import  {HttpClient} from '@angular/common/http';
```
Im constructor
```js
constructor(private http: HttpClient) {

}
```
Im app-module.ts
```js
import {HttpClientModule} from '@angular/common/http';

@NgModule({
    declaractions: [AppComponent],
    imports: [BrowserModule, FormsModule, HttpClientModule],
    providers: [],
    bootstrap: [AppComponent]
})
```

## Test Requests
Optionaler function return type
```js
getCustomers(): Observable<Customers[]> {
    return this.http.get<Customers[]>(`${this.baseUrl}/customers/getAllCustomers/`);
}
```
### GET
```js
getCustomers() {
    return this.http.get<Customers[]>(`${this.baseUrl}/customers/getAllCustomers/`);
}
```
### POST
```js
createCustomer(customer: Customer) {
    return this.http.post<Customer>(`${this.baseUrl}/customer`, customer);
}
```
### UPDATE
```js
updateCustomer(customer: Customer) {
    return this.http.put<Customer>(`${this.baseUrl}/customer`, customer);
}
```
### DELETE
 -> Info laut Julian i habs ned getestet ;)
```js
deleteCustomer(customer: Customer) {
    return this.http.delete<Customer>(`${this.baseUrl}/customer`, customer);
}
```


## Injecten und Verwendung von HTTP Service
### Injecten
```js
constructor(private httpService: HttpService) {

}
```

### Benutzen von HTTP Service
```js
loadCustomers() {
    this.httpService.getCustomers().subscribe((data: Customers[]) => {
      console.log("DO SAN DE CUSTOMER JAWOIIII!")
    }, (error) => {
        console.log("kane customer :(");
        console.error(error);
    });
}
```


# Routing
## Routing konfgurieren, falls noch nicht vorhanden
```
ng generate module app-routing --flat --module=app
```
Dann sollte app-routing.module.ts vorhanden sein
## SETUP
(in app-routing.module.ts oder app.module.ts)
### Import
```js
import { RouterModule, Routes } from '@angular/router';
```
### Routes definieren
```js
const routes: Routes = [
  { path: '', component: CustomerComponent },
  { path: 'customer-component', component: CustomerComponent },
  { path: 'reservationlist-component', component: ReservationListComponent },
];

```
```js
@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
```

## Navigieren
### Normal über Route
```html
<a [routerLink]="['customer-component']">Kundendaten</a>
<a routerLink="customer-component">Kundendaten</a>
<a  href="reservationlist-component">Reservierungsliste</a>

```

### Router Outlet einfügen bitte ahhhh
* In das Router-Outlet wird die aktuelle Component geladen
```html
<router-outlet></router-outlet>
```

### Nested Routes
```js
const routes: Routes = [
  { path: 'reservationlist-component', component: ReservationListComponent, children: [
      {path: 'r1sub1': component: SubComponent1},
      {path: 'r1sub2': component: SubComponent2},
  ] },
];
```
Wichtig: In den Parent Components gehört immer a router-outlet (also in dem Fall in der AppComponent und ReservationListComponent)
```html
<a [routerLink]="['reservationlist-component', 'r1sub1']">Sub</a>
Alternativ:
<a [routerLink]="['reservationlist-component/r1sub1']">Subcomponent</a>
Alternativ:
<a [routerLink]="reservationlist-component/r1sub1">Subcomponent</a>

<router-outlet></router-outlet>
```

## Übergabe von Parameter
```js
const appRoutes: Routes = [
    {path: 'user/:id/:name', component: UserComponent}
];
```
Auslesen von Params
```js
export class UserComponent implements OnInit {
    id: number;
    name: string;

    constructor(private route: ActivatedRoute) { }

    ngOnInit() {
        // this.id = this.route.snapshot.params['id'];
        // this.name = this.route.snapshot.params['name'];

        this.route.params.subscribe(
            (params: Params) => {
                this.id = params['id'];
                this.name = params['name'];
            }
        )
    }
}
```

## Query Parameter
```html
<a 
    [routerLink]="['/user', 1, 'Max']" 
    [queryParams]="{allowEdit: '1' }"
    fragment="editing"
>
    Bearbeite User
</a>

 localhost:4200/user/1/Max?allowEdit=1

```
Auslesen von QueryParams


```js
export class UserComponent implements OnInit {
    id: number;
    name: string;

    constructor(private route: ActivatedRoute) { }

    ngOnInit() {
        // this.id = this.route.snapshot.params['id'];
        // this.name = this.route.snapshot.params['name'];

        this.route.queryParams.subscribe(
            (queryParams: Params) => {
                this.allowEdit = queryParams['allowEdit'];
            }
        )
    }
}
```


## Navigieren mittels Programmlogik
```js
  constructor(private router: Router) {

  }

  navigateTo() {
    // greeting-component/subcomponent/1
    this.router.navigate(['greeting-component', 'subcomponent', '1'], 
        {queryParams: {"isEdit": '1'}})
  }
```
== 
```html
<a 
    [routerLink]="['greeting-component', 'subcomponent', '1']" 
    [queryParams]="{isEdit: '1' }"
>
```

# Forms

## Template Driven - TDF
### Setup
```js
import {FormsModule} from '@angular/forms';
// Auch in imports Array reinballern 
```

### Example
```html
<form (ngSubmit)="onSubmit(f)" #f="ngForm">
    <input type="text " class="form-control" ngModel #username="ngModel" id="username" name="username" required>

    <input type="email" class="form-control" ngModel name="email" required email #email="ngModel" min-length="3">
    <span>{{email.value}}</span>

    <button class="btn btn-primary" type="submit" [disabled]="!f.valid || !f.dirty">Submit</button>
</form>

Weitere Parameter:
- required
- min-length="3"
- max-length="3"
- email (email verifier)
- pattern="[0-9]{3}-[0-9]{3}-[0-9]{4}"


Input Types:
- number
- tel
```
### Auslesen der Daten bei Submit
```js
onSubmit(f: NgForm) {
    console.log(f.value['username'])
    console.log(f.value)
    // deep copy erstellen
    var obj = Object.assign({}, f.value);
}
```

## Reactive 
### Setup
```js
import {ReactiveFormsModule} from '@angular/forms';
// Auch in imports Array reinballern 
```

### Erstellen von am Form
```
ng generate @angular/material:address-form <component-name>
```


### Example
``` html
<form [formGroup]="addressForm" novalidate (ngSubmit)="onSubmit()">
  <mat-card class="shipping-card">
    <mat-card-content>
      
      <div class="row">
        <div class="col">
          <mat-form-field class="full-width">
            <input matInput placeholder="First name" formControlName="firstName">
            <mat-error *ngIf="addressForm.controls['firstName'].hasError('required')">
              First name is <strong>required</strong>
            </mat-error>
          </mat-form-field>
        </div>

      </div>
      <div class="row">
        <div class="col">
          <mat-form-field class="full-width">
            <textarea matInput placeholder="Address" formControlName="address"></textarea>
            <mat-error *ngIf="addressForm.controls['address'].hasError('required')">
              Address is <strong>required</strong>
            </mat-error>
          </mat-form-field>
        </div>
      </div>


      <div class="row">
        <div class="col">
          <mat-form-field class="full-width">
            <input matInput #postalCode maxlength="5" placeholder="Postal Code" type="number"
              formControlName="postalCode">
            <mat-hint align="end">{{postalCode.value.length}} / 5</mat-hint>
          </mat-form-field>
        </div>
      </div>

    </mat-card-content>
    <mat-card-actions>
      <button  [disabled]="!this.addressForm.valid"  mat-raised-button color="primary" type="submit">Edit</button>
    </mat-card-actions>
  </mat-card>
</form>
```

```js

export class ReactiveFormComponent implements OnInit{
  addressForm = this.fb.group({
    firstName: [null, Validators.required],
    address: [null, Validators.required],
    postalCode: [null, Validators.compose([
      Validators.required, Validators.minLength(5), Validators.maxLength(5)])
    ],
  });

  // values reinpatchen!
  ngOnInit(): void {
    this.addressForm.patchValue(
      data
    );

    // oder für einzelnes value
    this.addressForm.setValue(
      {
        firstName: "Gerry",
        address: "Gerry"
      }
    )
      
    // oder
    this.addressForm.controls['firstName'].setValue("Gerry");
  }

  constructor(private fb: FormBuilder, private httpService: HttpServiceService) {}


  // on submit
  onSubmit(): void {
    if (this.addressForm.valid) {
      console.log("Voi valid!");
      var res = Object.assign({}, this.addressForm.value);
      var username = this.addressForm.get('username')
    }
  }
}
```


# Direktive
```html
<p [style.backgroundColor]="testvar == 5 ? 'red' : 'blue'">Test</p>

<li  *ngFor="let u of users; let i=index">{{i}} - {{u}</li>}

<p [ngStyle]="[backgroundColor: getColor()]">Text</p>
```

# Classes
## Setup
```js
ng generate class <class-name>
```

## Example Class
```ts
export class Customer {
    id: number = 0;
    firstname: string = "";
    lastname: string = "";
    street: string = "";
    houseno: string = "";
    zip: string = "";
    city: string = "";
    password: string = "";
}
```

# Subject
```ts
export class SearchService {
    searchSource = new Subject<string>();
    constructor() {}

    setSearchString(message: string) {
        this.searchSource.next(message);
    }
}
```

```ts
export class Comp1 implements OnInit {
    constructor(private searchService: SearchService) {}

    ngOnInit() {

    }

    search(word: HTMLInputElement) {
        this.searchService
            .setSearchString(word.value);
    }
}
```

```ts
export class Comp2Component implements OnInit, OnDestroy{

    searchSubscription: Subscription;

    constructor(private searchService: SearchService){ }

    ngOnInit() {
        this.searchSubscription = this.searchService.searchSource.subscribe(
            (data:string)=>{
                console.log(data);
            }
        )
    )

    ngOnDestroy(){
        this.searchSubscription.unsubscribe();
    }
    
}
```

# Menu
## Setup
```js
ng generate @angular/material:navigation Menu
```
## Example
Bei mat-nav-list:
```html
<a routerLink="loginMask" mat-list-item >Anmeldung</a>
```
Bei Kommentar
```html
<router-outlet></router-outlet>
```


# List
## Setup
```js
ng generate @angular/material:table ReservationList
```

## Example
```html
<div class="mat-elevation-z8">
  <table mat-table class="full-width-table" matSort aria-label="Elements">

    <!-- Name Column -->
    <ng-container matColumnDef="start">
      <th mat-header-cell *matHeaderCellDef mat-sort-header>Datum</th>
      <td mat-cell *matCellDef="let row">{{row.start | date:'dd.MM.yyyy'}}</td>
    </ng-container>


    <tr mat-header-row *matHeaderRowDef="displayedColumns"></tr>
    <tr mat-row *matRowDef="let row; columns: displayedColumns;"></tr>

  </table>

  <mat-paginator #paginator
      [length]="dataSource?.data?.length"
      [pageIndex]="0"
      [pageSize]="10"
      [pageSizeOptions]="[5, 10, 20]">
  </mat-paginator>
</div>
```
## JS
```ts
export class ReservationListComponent implements AfterViewInit {
  @ViewChild(MatPaginator) paginator!: MatPaginator;
  @ViewChild(MatSort) sort!: MatSort;
  @ViewChild(MatTable) table!: MatTable<Reservation>;
  dataSource!: MatTableDataSource<Reservation>;

  /** Columns displayed in the table. Columns IDs can be added, removed, or reordered. */
  //displayedColumns = ['id', 'start', 'time'];
  displayedColumns = ['start'];

  constructor(private httpService: RestService) {
  }

  ngOnInit(): void {
    this.dataSource = new MatTableDataSource<Reservation>();
    this.refreshData();
  }

  ngAfterViewInit(): void {
    this.dataSource.sort = this.sort;
    this.dataSource.paginator = this.paginator;
  }

  refreshData() {
    this.httpService.getReservations(this.httpService.custid).subscribe((data) => {
      this.dataSource = new MatTableDataSource<Reservation>(data);
      this.table.dataSource = this.dataSource;
    });
  }
}
```






