
Modules are another way of creating components wherein you specify its imports / dependencies in one place and every component grouped under that module will automatically have access to it. The upside is that you don't have to import dependencies in every single component, but the downside is that you have to make module files and define a module structure. 

You typically create an app module `app.module.ts` with a class you decorate with `NgModule`. You add a `declarations` array in its configuration and add components, directives etc. that you want to work together. 

By adding `AppComponent`, you have to make it a module as well by marking its `standalone` configuration as false. You also add a `bootstrap` configuration to tell Angular which component is the root which starts the application.  If you wanna add standalone components to the module to tell Angular about them,  you have to add them to an `imports` array in the configuration. 

**NOTE** - the standalone components that you add to `AppModule` are the "top-level" ones that are in the template of the `AppComponent`

- `declarations` - add non-standalone components
- `imports` - add standalone components and other modules
```ts
import { NgModule } from '@angular/core';

import { AppComponent } from "./app.component";
import { HeaderComponent } from "./header/header.component";

@NgModule({
	declarations: [AppComponent],
	bootstrap: [AppComponent],
	imports: [HeaderComponent]
})
export class AppModule { }
```

You then also have to change `main.ts` and how you bootstrap the root module. 
```ts
import { platformBrowserDynamic } from "@angular/platform-browser-dynamic";
import { AppModule } from "./app/app.module";

platformBrowserDynamic().bootstrapModule(AppModule);
```

and also import `BrowserModule` to get a collection of stuff that your app needs to run properly

### Shared Modules
If you wanna create submodules that contain a group of related components or other submodules, you can create another module file, import the components / submodules under it, and specify an `exports` array configuration to expose these dependencies to other modules you'll import the submodule in.

```ts
@NgModule({
	declarations: [CardComponent],
	exports: [CardComponent]
})
export class SharedModule { }
```

Each module's imported submodules in `imports` will import whatever is in the `exports` configuration of that submodule. It will make those dependencies available to whatever is in the parent module's `declarations` and `imports` arrays. But it's important to note that importing a dependency in the parent module won't make those dependencies available to the submodules, just to whatever standalone / non-standalone components it imports. 

Also, to get access to the bag of features that `BrowserModule` gives, you can import `CommonModule` since `BrowserModule` is only imported in the root module. 