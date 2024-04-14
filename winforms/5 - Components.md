## Components

### Components Defined
- Components allow you to drag a class from the toolbox onto a form and configure it using the properties window via the `IComponent` interface
- They don't expose a UI or handle keyboard and mouse input
- You can declaratively set its properties
- Their resources are managed since `IComponent` extends `IDisposable`
- If your component doesn't extend the `Component` class, it must implement the `IComposable` and `IDisposable` interfaces

### Component Resource Management
Code is generated to add components to a list that their container maintains; when the container shuts down, it notifies all the components to release any resources they're holding
```cs
public AlarmComponent(IContainer container) {
  container.Add(this); // add ourselves to our parent's container so we get notified when our parent goes away
}
```

When you add a component to a form, a control, or another component, code is generated to add itself to the parent's container. Parents can notify their components that they are to be disposed of as part of the `Dispose` method. Components can then dispose of their resources **immediately**
```cs
// alarm component form
void InitializeComponent() {
  components = new Container();
  alarmComponent = new AlarmComponent(components);
}
```

### Custom Functionality
You make components useful by creating public properties (need to rebuild solution so they pop-up in the properties window), events, and methods. You can also extend from other components so you won't have to repeat functionality in your own components.