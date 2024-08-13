Classes make components together with the `Component` decorator from Angular. Decorators are used to add certain metadata and configuration to classes.

```ts
@Component({
	selector: 'app-root',
	standalone: true,
	templateUrl: './app.component.html',
	styleUrl: './app.component.css'
})
export class AppComponent {}
```

If you wanna use a component inside another component, you have to let Angular know about it. This works for standalone components.

```ts
import { HeaderComponent } from './header.component';

@Component({
	imports: [HeaderComponent]
})
```

### Data Binding
There are 2 main ways you can bind data from the component class to the template.

**String interpolation**
With `{{ }}`, you can access any public property of the component class.

```html
<p>{{ selectedUser.name }}</p>
```

**Property Binding**
You can bind values to a property with `[]`.

```html
<img [src]="'assets/users/' + selectedUser.avatar" alt="">
```

It's also important to note that when you use property binding, you're not actually binding to the HTML attribute of the element. Instead, you're telling Angular to bind it to the underlying property of the object. There's a difference between a **property** and an **attribute**.

```ts
var image = document.querySelector(img[foo=bar]);

console.log(image.getAttribute("foo")); // bar <- attribute
console.log(image.foo); // undefined <- property, what you're actually binding to
```

**Event Binding**
You can bind events to a property with `()`

```html
<button (click)="handleClick()">Click me</button>
```

### Changing State

**Change Detection**
Angular's change detection mechanism is powered by zone.js, which notifies Angular about user events, expired timers etc. When Angular receives a notification, it checks components for changes and updates the DOM accordingly. This makes it so that you can change state by simply updating the properties of the clas

```ts
this.selectedUser = DUMMY_USERS[0];
```

**Signals - trackable data containers** 
A signal is an object that stores a value (any type of value, including nested objects). These containers notify Angular when their values change, and it can update the part of the UI that needs changing, which can be very efficient. It doesn't have to check for changes in every component in a zone, components can just subscribe to signals and get updated when those signal values change.

---

## Communication between Components
A parent can bind to a property of a child component with the `@Input` decorator. You can also specify configuration inside the decorator.

```ts
@Input({ required: true }) user!: User; // when an input is required
@Input() user?: User; // telling ts that this might not be set and it's ok
@Input() user: User | undefined; // same thing
```

```html
<li>
	<app-user [user]="user" />
</li>
```

A child can notify a parent an event has happened with the `Output` decorator and `EventEmitter`. You can then listen to the event from the parent, and retrieve the value it emitted with the special variable `$event`.

```ts
@Output() select = new EventEmitter<User>();
```

```ts
<app-user (select)="onSelectUser($event)" />
```