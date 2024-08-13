### Getting a hold of form control values

#### 2-way binding
2-way binding is a combination of property and event binding. This is used to keep track of values entered by users and override them if needed. You use this feature by adding the directive `ngModel` to the inputs you want to bind to a property. Directives are just components without templates.

```html
<input [(ngModel)]="<property>" />
```

You also need to import the `FormsModule` to use `ngModel`. By importing this module, it automatically prevents the browser defaults of sending the form request to the server that served the website (like `e.preventDefault()` in React).

```ts
import { FormsModule } from '@angular/forms';
```

You can then listen to the submission of the form by listening to the `ngSubmit` event.

```html
<form (ngSubmit)="onSubmit()">
</form>
```

###### Custom 2-way binding
You can also set up custom 2-way binding for components you create yourself since it's essentially just a combination of using inputs to take in values, and outputs to notify changes with those values to whoever is interested. You have to coordinate the names of the inputs and outputs to match the form `<property>` for the input, and `<property>Change` for the output, for 2-way binding to work.

```ts
export class RectangleComponent {
	@Input({ required: true }) size!: { width: string; height: string; };
	@Output() sizeChange =  new EventEmitter<{ width: string; height: string; }>();
}
```

```html
<app-rectangle [(size)]="{ width: '100', height: '100' }" />
```

If you're using Angular version 17.2 >, you have the option of using more concise syntax using signals by using the `model` function instead.

```ts
import { model } from '@angular/core';

export class RectangleComponent {
	size = model.required<{ width: string; height: string; }>();

	onReset() {
		this.size.set({ width: '100', height: '100' });
	}
}
```
#### Template variables
Another way to extract form control values is to use template variables. You create a template variable by adding a `#<variable-name>` to get a reference to the form control and refer to the name inside the template *without* the hashtag. 

```html
<input type="text" #titleInput />
```

So for example, you can pass this element to your `onSubmit` function inside the template itself...
```html
<form (ngSubmit)="onSubmit(titleInput)">
	<input type="text" #titleInput />
</form>
```

and eventually do something to extract the value of the form control.
```ts
onSubmit(titleInput: HTMLInputElement) {
	const title = titleInput.value;
}
```

You can even extract the value right away and just pass that instead of passing the whole input element.
```html
<form (ngSubmit)="onSubmit(titleInput.value)"> ... </form>
```

### ViewChild
So that you don't have to rely on passing a template variable to an event handler just to manipulate / read its value in your component file, you can instead query the view (whatever is in the template) directly in the component using `ViewChild`. This acts similarly to `document.querySelector()` in that you can select elements via some selector to make them available in a code file. 

You can use class selectors to query a host component in the template by passing the class name to `ViewChild`.
```ts
@ViewChild(ButtonComponent) button?: ButtonComponent;
```

More importantly, you can pass in the name of the template variable to `ViewChild` to query template variables with that name. You also need to set its value as the generic `ElementRef` and specify the type of native element you expect to get from the query. And whenever you need to access the element, you need to first access the `nativeElement` property of the wrapper.
```ts
import { ElementRef } from '@angular/core';

@ViewChild("form") form?: ElementRef<HTMLFormElement>;

onSubmit() {
	this.form?.nativeElement.reset();
}
```

Note that we add a `?` to the properties to denote that their values might not be available right away since `ViewChild` queries can only get a meaningful value after the view is initialized ie only on or after `ngAfterViewInit`.

Querying multiple elements is also possible, just like `document.querySelectorAll()` using `ViewChildren`.

### ContentChild
`ContentChild` works just the same as `ViewChild` except you can query elements that are merely projected into the component template, but not actually in the template itself already.

- **view** - whatever is in the component template
- **content** - elements that are projected inside a component template

---

### Form classes
There are classes that are controlled by `ngModel` that tells you the state of a form ie whether it was touched or invalid. You can use these to conditionally show error messages / style the controls accordingly.

- `ng-pristine` / `ng-dirty` - tells you whether the form control has received input from the user
- `ng-valid` / `ng-invalid` - tells you whether the form control is valid depending on the validators added
- `ng-untouched` / `ng-touched` - tells you whether the form control was selected

## 2 main ways of handling forms

### Template-driven
Forms are set up in the template using template variables. They're easier to get started with but it can become tricky once you need to implement more complex logic.

1. Import the `FormsModule`
```ts
import { FormsModule } from '@angular/core';
```

2. Let Angular know about a form control by adding the `ngModel` directive and adding a `name` property to it. 
```html
<input type="text" name="email" ngModel />
```

3. Add a template variable to the form to make it possible to grab a reference to the underlying `HTMLFormElement`, but set its value to `"ngForm"` to create an `NgForm` behind the scenes. Do this also form the input controls but set them to `NgModel`.
```html
<form #form="ngForm">
	<input
		type="text"
		name="email"
		ngModel
		#email="ngModel"
	/>
</form>
```

4. Grab a reference to the element by either using `ViewChild` or by passing it to an `ngSubmit` event handler.
```html
<form #form="ngForm" (ngSubmit)="onSubmit(form)"></form>
```

5. Extract the value of the form control by accessing the `form` key of the `NgForm`
```ts
onSubmit(formData: NgForm) {
	const enteredEmail = formData.form.value.email;
}
```

6. (Optional) Add form validators using  attributes / directives.
```html
<input type="text" name="email" ngModel required email />
<input type="password" name="password" ngModel required minlength="6" />
```

7. Access the `NgForm`'s state to check if a form is invalid.
```ts
onSubmit(formData: NgForm) {
	if (formData.form.invalid) return;
	// ...
}
```

8. Add error messages when form is invalid.
```html
@if (password.touched && form.form.invalid) {
	<p> Form is invalid </p>
}
```

9. Reset the form if submission is successful
```ts
onSubmit(formData: NgForm) {
	if (formData.form.invalid) return;

	formData.form.reset();
}
```

10. (Optional) Subscribe to form value changes
```ts
constructor() {
	afterNextRender(() => {
		this.form.valueChanges?.subscribe(() => {
			next: (value: any) => {
				console.log(value); // do something here
			}
		})
	})
}
```

#### Setting form values programmatically
You can set form values programmatically using the `setValue` function. This works for setting the value of the whole form, as well as individual form controls.
```ts
this.form.form.setValue({
	"email": "programmatic@gmail.com",
	"password": "password123"
})

this.form.controls['email'].setValue("programmatic@gmail.com");
```

### Reactive
Reactive forms are set up in the TS file and are linked to the template. They're more verbose but easier to work with when it comes 
to complex logic. 

1. Instantiate a `FormGroup` instance and set it to a property. 
**NOTE:** A `FormGroup` is just the object that is stored in the `this.form.form` value of a template form's implementation using `NgForm`
```ts
import { FormGroup } from '@angular/forms';

export class LoginComponent {
	form = new FormGroup({});
}
```

2. Add controls to the form group by adding key-value pairs where the key is the form control name, and the value is a `FormControl` object.
```ts
import { FormControl } from '@angular/forms';

export class LoginComponent {
	form = new FormGroup({
		email: new FormControl('') // default value
	});
}
```

3. Let Angular know how the form group in the TS file is connected to a form in the template using directives.
```ts
import { ReactiveFormsModule } from '@angular/forms';

@Component({
	imports: [ReactiveFormsModule]
})
```

```html
<form [formGroup]="form"> // form group property name
	<input id="email" type="email" formControlName="email" /> // form control name under form group
	<input id="password" type="password" formControlName="password" />
</form>
```

4. Listen to the `ngSubmit` event and call an event handler. Except in the case of reactive forms, you don't need to pass a reference to the form / use `ViewChild` to grab a reference to it, because you already have access to it in the TS file in the first place.
```ts
<form [formGroup]="form" (ngSubmit)="onSubmit()">
```

5. Extract the value of the form controls.
```ts
onSubmit() {
	const email = this.form.value.email; // or
	const email = this.form.controls.email.value;
	this.form.reset();
}
```

6. Add form validators to the form setup. Validators are just functions.
```ts
import { Validators } from '@angular/forms';

form = new FormGroup({
	email: new FormControl('', {
		validators: [Validators.email, Validators.required, Validators.minLength(5)]
	})
})
```

7. Add a way to check for invalid form controls, preferably in the TS file because they tend to get long
```ts
export class LoginComponent {
	get emailIsInvalid() {
		return this.form.controls.email.touched &&
			   this.form.controls.email.dirty &&
			   this.form.controls.email.invalid;
	}
}
```


#### Building custom validators
A validator is just a function that takes in the control and returns whether the control is valid or not. You can also add custom async validators to, for example, check if an email is already registered.
```ts
import { AbstractControl } from '@angular/forms';

function mustContainQuestionMark(control: AbstractControl) {
	if (control.value.includes('?')) return null;
	return { doesNotContainQuestionMark: true };
}

form = new FormGroup({
	password: new FormControl('', {
		validators: [mustContainQuestionmark]
	})
})
```

You can also create custom validators that test multiple inputs at once. For this to work, the controls have to be grouped under the same form group. You can use the `get` method on a form control to get a child control.

```ts
equalValues(control: AbstractControl) {
	const password = control.get('password')?.value; 
	const confirmPassword = control.get('confirmPassword')?.value; 

	if (password == confirmPassword) return null;

	return { valuesNotEqual: true };
}

form = new FormGroup({
	passwords: new FormGroup({
		password: new FormControl(),
		confirmPassword: new FormControl()
	}, {
		validators: [equalValues]
	})
})
```

#### Nested form controls
```ts
form = new FormGroup({
	passwords: new FormGroup({
		password: new FormControl('', { validators: [Validators.required] }),
		confirmPassword: new FormControl('', { validators: [Validators.required] })
	}),
})
```

```html
<div formGroupName="password"> // shared parent element
	<input name="password" formControlName="password" />
	<input name="confirmPassword" formControlName="confirmPassword" />
</div>
```

### Form Arrays
You can use form arrays if you want to group form controls without having to name them explicitly or if you want to dynamically add / remove form controls from a group.

```ts
import { FormArray } from '@angular/forms';

new FormGroup({
	source: new FormArray([
		new FormControl(false),
		new FormControl(false),
		new FormControl(false),
	])
})
```

```html
<fieldset formArrayName="source">
	<input type="checkbox" formControlName="0" />
	<input type="checkbox" formControlName="1" />
	<input type="checkbox" formControlName="2" />
</fieldset>
```