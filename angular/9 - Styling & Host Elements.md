Styles can be global via the `styles.scss` or scoped via the stylesheet file linked to a component. The styles inside a scoped stylesheet only apply to whatever Angular sees in the component template file as is. It doesn't consider what elements might be projected into it when the component is rendered.For those cases, you can either make it global, or change the configuration of the component to not scope the styles and apply them globally.

The advantage of this is that even though the styles are scoped globally, they're co-located to the component that they should be applied to.

```ts
@Component({
	...
	encapsulation: ViewEncapsulation.None
})
```

### Conditional Styling
You can conditionally add a class and therefore style an element by binding a predicate to the `class` property of an element together with the class name you want to possibly add.
```html
<div [class.status-online]="currentStatus == 'online'"></div>
```

You can also conditionally add more than 1 class by binding key value pairs of the form `<class-name>: <predicate>`
```html
<div [class]="{
	 'status-online': currentStatus == 'online',
	 'status-offline': currentStatus == 'offline'
}"></div>
```

### Host components
If you try to style elements you're extending, they also won't be styled simply by targeting them in the stylesheets file. You need to target the host element via the `:host` css selector. The host element is whatever you specify in the `selector` configuration of a component. You'll notice that you almost never actually add a component inside its own template. And so if you wanna style it you have to add the styling inside the component you add that component to. But by using `:host`, you can target the host component from its own stylesheet.

```css
:host { ... }

:host:hover { ... }
```


#### Adding classes to host components
You can add classes to host components in the template file straight away
```html
<app-control class="control"></app-control>
```

But if you have many of those components, it can seem repetitive. One workaround is to use the `host` component configuration to specify a class you want to add to every instance of that component.
```ts
@Component({
	host: {
		class: 'control'
	}
})
```

You wanna do this if you have a wrapper with a class that seems unnecessary. So instead of:
```html
<div class="wrapper">
	<article></article>
<div>
```
```css
.wrapper { ... }
```

You can just do:
```html
<article></article>
```
```css
:host { ... }
```

#### HostBinding & HostListener
An alternative to using the `host` configuration in the component decorator is to use the `HostBinding` decorator. However, this isn't preferred.

```ts
import { HostBinding } from '@angular/core';

export class ControlComponent {
	@HostBinding('class') className = 'control';
}
```

You can also bind a method to an event you wanna listen to on the host component with `HostListener`
```ts
import { HostListener } from '@angular/core';

export class ControlComponent {
	@HostListener('click') onClick() { ... }
}
```

Alternatively, you can also use the `host` configuration of the component decorator
```ts
@Component({
	host: {
		'(click)':  'onClick()'
	}
})
```

### Accessing the host element programmatically
You can access the host element programmatically by injecting the `ElementRef` wrapper.

```ts
import { ElementRef } from '@angular/core';

export class ControlComponent {
	contructor(private el: ElementRef) {
		console.log(this.el);
	}
}
```