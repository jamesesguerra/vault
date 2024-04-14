## Data Binding Basics

- There are many different data stores that an app can work with. Apps must provide the functionality to read data, present it, allow editing, and write any changes back to the data store.
- The concept of a **data source** simplifies things as it abstracts away different data stores and technologies so you can deal with different ones in a consistent way
- **Data binding** simplifies the integration of Winforms apps with data

### The Land Before Data Binding
- Logically, *data-binding* is the associate of object properties with control properties
- You have to write code that bidirectionally synchronizes the state of your controls and your object
- Implement the `INotifyPropertyChanged` interface on the object to notify the form when its properties are changed by something other than the controls from the form, so that it can be updated to the latest state
- Too much work, and you need intimate knowledge of the `RaceCarDriver` class

### Simple Data Binding and Item Data Sources
- The fundamental building block of Winforms data binding is the Binding object, which binds control properties to obejct properties
- **Simple binding** - binding a control's property to an object's property
- The bound `raceCarDriver` object is known as an *item data source* because its data is located in a single item
- Create a new `Binding` object and add it to the Binding collection of the control you want the object to be bound to
- Binding objects require 4 parameters: the name of the property on the control to set, the object that exposes the property, the name of the property which to bind, and whether to enable automatic formatting
- The simple data binding takes care of initialization of the control property's value, as well as synchronization between the control and the object, but this means you still need to implement the `INotifyPropertyChanged` interface on the object

### Simple Binding and List Data Sources
- A **list data source** is a collection of homogenous objects to be used as data in a binding operation
- The minimum implementation that is considered a list data source is a class that implements the `IList` interface
- You also add a Binding object to the `DataBinding` property of the control, but this time, you specify the list data source instead of the item data source
- You have to navigate through the bound data items if your control can only support item data sources (such as textboxes)
- The current item is managed by a binding manager by maintaining its position in the list data source with its Position property
- To implement the navigation, you acquire the BindingManager for the desired data source and use it to change the binding manager's position as appropriate
```cs
BindingManagerBase BindingManager {
        get => BindingContext[raceCarDrivers];
}

BindingManager.Position = 0;
BindingManager.Position++ / BindingManager.Position--;
```

### Complex Binding and List Data Sources
- Simple binding only works for item and list data sources that work with controls that can show only one object at a time, this is where complex binding comes in
- Whereas simple binding to list data sources binds a control property to a property on the current object of a list data source, **complex binding** binds a control property to an entire list data source
- Complex controls expose the `DataSource` property to consume list data sources, and `DisplayMember` to specify which property of the contained objects provides the value that's displayed by the list
- Controls that share the same list data source automatically share the same binding manager, and thefore share the same idea of which item is current
```cs
raceCarDriversListBox.DataSource = raceCarDrivers;
raceCarDriversListBox.DisplayMember = "Name";
```

#### DataGridView
- ListBoxes don't support adding / deleting racecar drivers, but we can use the DataGridView control instead, which has this functionality built in
```cs
raceCarDriversDataGridView.DataSource = raceCarDrivers;
```
- We can add buttons to add and remove racecar drivers from the list data source, but this wouldn't have any visual effect since a list data source of type `List<T>` doesn't provide notification of actions taken against its list items such as adding / deleting, and thus the binding engine doesn't see them
- To add support for adding via the DataGridView itself, add a default constructor to the object contained by the list data source
- List data sources therefore need to implement a communications protocol to let both simple and complex controls know about list and item changes, much like how the RaceCarDriver class implemented the `INotifyPropertyChanged` interface


### IBindingList
- IBindingList is a data binding infrastructure contract that extends IList with data-binding specific functionality for list data sources
- BindingList is a generic implementation of IBindingList that implements the list management and change notification functionalities of IBindingList
```cs
BindingList<RaceCarDriver> raceCarDrivers = new();
```

### BindingSource Component
- This component allows you to upgrade an already existing IList implementation to IBindingList in order to avoid the highly involved implementation of IBindingList ourselves; we want this since some item classes come already associated with their own collection classes
```cs
List<RaceCarDriver> raceCarDrivers = new(); // not an IBindingList
BindingSource raceCarDriverBS = new();

raceCarDriverBS.DataSource = raceCarDrivers;
```

- The change notification functionalities provided by the binding source will only work if you add and delete items via BindingSource and not via the original list
```cs
raceCarDriverBS.Add(new RaceCarDriver("M.Schumacher", 500)); // good
raceCarDrivers.Add(new RaceCarDriver("M.Schumacher", 500)); // bad
```