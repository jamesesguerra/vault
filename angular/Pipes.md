**Pipes** transform the way data is displayed. They aren't unlocked for all templates, so you have to import the pipes you want to use.

```ts
import { DatePipe } from '@angular/common';

@Component({
	...
	imports: [DatePipe] 
})
```

You can also add parameters to tweak the pipe output
```html
<p>{{ currentDate | date }}</p>
<p>{{ currentDate | date:'medium' }}</p>
```

### Creating a custom pipe

1. Create the pipe file `<pipe-name>.pipe.ts` e.g. `temperature.pipe.ts`
2. Add the pipe class and its configuration with the `@Pipe` decorate from Angular
```ts
import { Pipe } from '@angular/core';

@Pipe({
	name: 'temp',
	standalone: true
})
export class TemperaturePipe { }
```

3. Add the pipe's mandatory `transform` method by implementing the `PipeTransform` interface 
```ts
export class TemperaturePipe implements PipeTransform {
	transform(value: string | number, ...args: any[]) {
		return value;
	}
}
```

4. Add the pipe's logic and optionally, pass arguments to it
```ts
export class TemperaturePipe implements PipeTransform {
	transform(value: string | number, inputType: string, outputType: string) {
		// ...some logic
		return value
	}
}
```

```html
<p>{{ temperature | temp:'cel':'fah' }}</p>
```

You can also use pipes in the `@for` block just as you would in string interpolation
```html
@for(temperature in temperatures | sort; track temperature) { }
```


### Caching
Pipe transform functions only run when their `value` input / reference changes. It does this to optimize the app since it could potentially run a lot if it's run every time anything in the template changes, and so it caches the value until its input changes.

To disable this caching, you can set its `pure` configuration to false so that it runs every time **anything** in the template changes.

```ts
@Pipe({
	name: 'sort',
	standalone: true,
	pure: false
})
```