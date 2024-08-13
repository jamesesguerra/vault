Services is a class with "injectable" code you can use from different components. It manages data and state that is used by multiple components.

```ts
export class TasksService {
	// methods to manage data
}
```

When you want to use a service in a component, you don't instantiate it and use it from there like this. 
```ts
const tasksService = new TasksService();
tasksService.getTasks();
```

This is because if you use this service in 2 different components, they'll be using 2 different services and will operate on different sets of data. The solution to this is **dependency injection**, where you just ask for a service in the constructor, and Angular will create 1 instance of that service and share it with all other components that request it. 

```ts
constructor(private tasksService: TasksService) { }
```

You then also have to tell Angular to look for your code in your component by using the `Injectable` decorator
```ts
@Injectable({
	providedIn: 'root'
})
export class Tasks
```

### DI
In Angular, DI works by requesting services from Angular instead of creating them yourselves. You also have to let Angular know about the code that they need to inject, with the `Injectable` decorator. If you don't add this, Angular will look for a provider for that service from a hierarchy of providers.

Using the root `EnvironmentInjector`:
```ts
@Injectable({
	providedIn: 'root'
})
```

Another way you can can let Angular know about it is through the `bootstrapApplication` method, giving it some configuration about providers. However with this setup, Angular won't be able to tree-shake services.

```ts
bootstrapApplication(
	AppComponent, { providers: [TasksService] }).catch((err) => console.error(err));
```

You can also provide services via the `ElementInjector` in which case only the host element and all other child components can access the service. It's also important to note that only components and directives can use the `ElementInjector`, and so you can't use this to provide a service for another service.

```ts
@Component({
	// ...
	providers: []
})
```

Using the `ElementInjector` also means that you'll have 2 different services if you create 2 different components who request a service from this injector. This is different from using the `Injectable` decorator and providing the service in root wherein you'll always have 1 instance of the service for the whole application.