
An application's routes is just an array of objects that encapsulate the path of the route, as well as the component to render when the URL is set to that path.

This array is then passed to the `provideRouter` method, which is then passed to the `providers` configuration of `bootstrapApplication`

```ts
import { provideRouter } from '@angular/router';

bootstrapApplication(AppComponent, {
	providers: [
		provideRouter([
			{
				path: 'tasks',
				component: TasksComponent
			}
		])
	]
}).catch((err) => console.error(err));
```

Because these can get lengthy, it's recommended to create a new `app.routes.ts` file to place the routes in.

```ts
import { Routes } from '@angular/router';

export const routes: Routes = [
	{
		path: 'tasks',
		component: TasksComponent
	}
]
```

It's also good practice to create a separate file to hold application config called `app.config.ts`, and initialize the routes there.

```ts
import { ApplicationConfig } from "@angular/core";
import { provideRouter } from "@angular/router";
import { routes } from "./app.routes";

export const appConfig: ApplicationConfig = {
	providers: [provideRouter(routes)]
}
```

You then tell Angular where to render the component by using `<router-outlet />`
```ts
import { RouterOutlet } from '@angular/router';
```

### Router links
You use the `routerLink` directive instead of `href` to specify links.

```ts
import { RouterLink } from '@angular/router';
```
```html
<a routerLink="/tasks"> Click me </a>
```

You can then style the currently active link by using the `routerLinkActive` directive.

```ts
import { RouterLinkActive } from '@angular/router';
```
```html
<a routerLink="/tasks" routerLinkActive="selected"> Click me </a>
```

### Dynamic Paths
Dynamic paths segments are denoted by a colon `:`.

```ts
{
	path: 'users/:userId'
}
```

```html
<a [routerLink]="'users/' + user.id"> Click me </a>

<!-- or -->

<a [routerLink]="['users/', user.id]"> Click me </a>
```

You can then grab the dynamic segment in different ways:

1. **Input-based**
```ts
// app.config.ts
import { withComponentInputBinding } from '@angular/router';

export const appConfig: ApplicationConfig = {
	providers: [provideRouter(routes, withComponentInputBinding())]
}
```
```ts
// component file

@Input() userId!: string; // variable name has to be same as the dynamic segment name
```

2. **ActivatedRoute (with Observables)**
- gives you access to inspect certain properties about the current route
- the difference between `ActivatedRoute` and the snapshot key is that the activated route exposes subjects you can subscribe to, whereas the snapshot isn't a reactive value
```ts
import { ActivatedRoute } from '@angular/router';

export class UserTasksComponent implements OnInit {
	constructor(private activatedRoute: ActivatedRoute) { }

	ngOnInit() {
		this.activatedRoute.paramMap.subscribe({
			next: paramMap => {
				console.log(this.paramMap.get('userId'));
			}
		})
	}
}
```

### Nested Routes
Child routes are routes that are tacked on to the main routes of your application.
```ts
{
	path: 'users/:userId',
	component: UserTaskComponent,
	children: [
		{
			path: 'tasks',
			component: TasksComponent // will be rendered if the url is users/:userId/tasks
		}
	]
}
```

You then add a `router-outlet` inside the component it's a child of.

From there, the links you make to that child component can be relative simply by not adding a `/` before the link.
```html
// navigates to /users/:userId/tasks/new from the component that renders it
<a routerLink="tasks/new"> Click me </a> 
```

If you wanna access the parent URL from the child, for example if you wanted to access some dynamic segment. You have to make changes to your application config

```ts
export const appConfig: ApplicationConfig = {

	providers: [provideRouter(routes, withComponentInputBinding(), withRouterConfig({
		paramsInheritanceStrategy: 'always'
	}))]
}
```

### Programmatic Navigation
If you wanted to move up a level and go to the route all the way up to the current route, `routerLink` exposes a special syntax to do this
```html
<a routerLink="../"> Cancel </a>
```

To navigate programmatically, you inject the `Router` and call the `navigate` method. You can also specify the `replaceUrl` to be true in order to disable going back to the current route (useful so form resubmission won't be possible).
```ts
import { Router } from '@angular/router';

export class NewTaskComponent {
	private router = inject(Router);

	onSubmit() {
		this.router.navigate(['/users', this.userId, 'tasks'], { replaceUrl: true })
	}
}
```

### Handling special paths

#### Not found
To create a catch-all route that gets called when the current route doesn't match any of the others, you can use `**`
```ts
{
	path: '**',
	component: NotFoundComponent
}
```

#### Redirects
You can redirect users to other paths by using the `redirectTo` option instead of specifying a component. In this case, you also wanna specify the `pathMatch` option to tell Angular whether to check if the current path should match the path with the redirect fully, or if it's merely the start that should be checked.

```ts
{ 
	path: '',
	redirectTo: '/home',
	pathMatch: 'full' // we want full because every route starts with an empty string
}
```

### Query Params
The advantage of using query params is that you can use the link to share the behavior of the page with others.

**NOTE:** You need to use `RouterLink` for this to work

```html
<a routerLink="./" [queryParams]="{ order: 'asc' }"> Sort Descending / Ascending </a>
```

You can then extract the query params the same way you would with extracting dynamic segments.
```ts
// via inputs
order = input<'asc' | 'desc'>();

// or subscribing to the activated route observables

ngOnInit() {
	this.activatedRoute.queryParams.subscribe({
		next: params => {
			this.order = params['order'];
		}
	})
}
```

### Route Guards
To "guard" certain pages from being accessed, you can add either the  `canMatch` or `canActivate` fields to the path configuration.

These fields accept an array of guards. Guards are just functions (in modern Angular, but classes in legacy) that return true or false depending on whether the user should be granted access to the page. But ideally, you want to redirect the user if they're not allowed to access the current page. 

```ts
const dummyCanMatch: CanMatchFn = (route, segments) => {
	const router = inject(Router);
	const shouldGetAccess = Math.random();
	return shouldGetAccess < 0.5 ? true : new RedirectCommand(router.parseUrl('/unauthorized'));
}

{
	path: 'users/:userId',
	component: UserTasksComponent,
	canMatch: [dummyCanMatch]
}
```