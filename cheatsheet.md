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

### Material Component erstellen
```
ng generate @angular/material:<material-element> <component-name>
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


