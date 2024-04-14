## Design-Time Integration: The Properties Window

> Forms are built for a user-driven mode of execution called the run-time; components and controls are designed and built to support an additional mode of execution called *design time*

### Design Time
- Design time comprises Visual Studio, the Winforms Designer, and .NET
- It supports the creation and management of design-time components as developers drag and rearrange them

### Design-Time Components
- `IComponent` is the fundamental building block since it models the point of integration between a class and the design time
- Your classes will generally inherit from `Component`, `Control`, or `UserControl`, which all directly/indirectly implement the `IComponent` interface -- therefore, they are considered *design-time components*

### Hosts, Containers, and Sites
- When a component is dragged to the design surface, it's instantiated by the Winforms Designer and also grabs a reference to its `IComponent` interface
- The Winforms Designer has an internal *designer host* which manages design-time objects and provides design-time services
- The designer host also stores a list of references to all design-time components on the form, including the form itself
- Similarly, contained components can also access the designer host at design time via the `ISite` interface, but can directly be accessed from the component via its `Container` property

### DesignMode
- If you need to change the behavior of a component in design time, you can check whether it's running in a designer or not by accessing the component's `DesignMode` property

### Properties Window Integration
- You can customize a component's description, behavior, default values in the properties window by applying window-influencing attributes to its properties and events
- If you provide default values, it won't be serialized in `InitializeComponent`, only when if their configured values differ from the defaults do they get added
- You can instruct the designer to walk into a component and serialize a property that is a custom type by setting its `DesignerSerializationVisibility` to `Content`

### `ISupportInitialize`
- Implement if you want the component's properties to be validated all at the same time at the end