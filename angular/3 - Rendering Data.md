## Rendering Lists
The track expression is used to specify the unique identifier of each list object so that Angular can re-render the list efficiently if there are any updates.

Angular v17 > uses a `for` block for rendering lists. Angular <= v16 uses a **structural directive** -- something you can add to elements which change the DOM where those elements are used. 

```ts
@for (user of users; track user.id) {
	<li>{{ user.name }}</li>
}

import { NgFor } from '@angular/common';

<li *ngFor="let user of users">{{ user.name }}</li>
```

## Conditional Rendering

```ts
@if (selectedUser) {
	<p>{{ selectedUser.name }}</p>
} @else {
	<p>Select a user to see their tasks</p>
}

import { NgIf } from '@angular/common';

<p *ngIf="selectedUser; else fallback">{{ selectedUser.name }}</p>
<ng-template #fallback>
	<p>Select a user to see their tasks</p>
</ng-template>
```

You can also conditionally add classes to elements using `[class.<class-name>`

```html
<button [class.active]="selected">
	{{ user.name }}
</button>
```