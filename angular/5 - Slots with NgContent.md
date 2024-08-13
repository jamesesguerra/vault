You can reuse pieces of UI by using the `<ng-content>` component.

You first create / outsource the UI component you want to reuse, and then wrap it around the wrapped component to combine their UI into one.

```html
<!-- reusable card UI component -->
<div>
	<ng-content />
</div>
```

```html
<!-- wrapping the component -->
<app-card>
	...
</app-card>
```

### Multiple Slots
You can also output content in different parts of the UI with the `select` property of `ng-content`

```html
<button>
	Logout
	<span class="icon"> → </span>
</button>
```

```html
<span>
	<ng-content />
</span>
<ng-content select=".icon" />
```

Or you can also use a normal selector instead of a class selector

```html
<button>
	Logout
	<span ngProjectAs="icon"> → </span>
</button>
```

```html
<span>
	<ng-content />
</span>
<ng-content select="icon" />
```

### Fallback content
You can also add fallback content for slots that will possibly be empty by adding them inside the `ng-content` element like a slot as well
```html
<ng-content select="icon">
	->
</ng-content>
```

### Multi-selectors
You can select multiple elements in the `select` property of `ng-content` to select multiple components / elements.
```html
<!-- form control component -->
<label> {{ label }} </label>
<ng-content select="input, textarea" /> <!-- markup allowed in this slot is restricted to only input and textarea elements -->
```