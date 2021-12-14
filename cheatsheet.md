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
```javascript

1. Values weitergeben beim Component verwenden
<app-greeting
    vorname="Max"
    [nachname]="'Muster'"
>
</app-greeting>

2. Im Typescript
export class GreetingComponent {
    @Input('vorname') firstname | undefined;
    @Input('nachname') lastname | undefined;
}

3. Im HTML
Hello {{firstname}} {{lastname}}!
```
