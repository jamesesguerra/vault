Directives are just like components except they don't have templates. They enhance elements by manipulating their configuration (properties and attributes), behavior, and their styling.

### Attribute Directives
Attribute directives such as `ngModel` are added to elements like attributes, and they simply change that elements properties / dynamically add CSS classes. They don't change its markup whatsoever.

In this case, `ngModel` enhances an input element by giving it 2-way binding capabilities with its input-output combination.

```html
<input [(ngModel)]="text" />
```

### Structural Directives
Structural directives change the DOM in some way. An example of this is `ngIf` which changes the DOM according to the predicate set. Structural directives are also denoted by the `*` before their names.

```html
<p *ngIf="isAdmin">Only admins should see this.</p>
```

### Custom Directives
You can create a new directive by using the Angular CLI
```sh
ng g directive
```

To create one yourself, you export a class and decorate it with the `Directive` decorator, similar to components.
```ts
import { Directive } from '@angular/core';

@Directive({
	selector: 'a[appSafeLink]',
	standalone: true
})
export class SafeLinkDirective {

}
```

```html
<a appSafeLink href=""> To somewhere </a>
```

You can then start enhancing the behavior / properties of the element you attach the directive to by adding code such that you were interacting with the host element itself. In this manner, directives are just like things you can use to "attach" to host elements and tack on behavior to them.

```ts
@Directive({
	...
	host: {
		'(click)':  'onClick($event)'
	}
})
export class SafeLinkDirective {
	onClick(event: MouseEvent) {
		const wantsToLeave = window.confirm("Are you sure you want to leave the page?");

		if (wantsToLeave) {
			return;
		}

		e.preventDefault();
	} 
}
```

Directives can also take inputs with the same input decorators / methods as components.
```ts
export class SafeLinkDirective {
	@Input() queryParam = "myApp";

	ngOnInit() {
		console.log(this.queryParam); // docs-link
	}
}
```

```html
<a appSafeLink queryParam="docs-link" href=""> To somewhere </a>
```

**NOTE:** You can also inject services into directives just like components.