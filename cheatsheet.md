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