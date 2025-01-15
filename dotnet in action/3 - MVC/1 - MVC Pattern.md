The MVC design pattern is a common pattern for designing UIs. Fundamentally, it aims to separate the management and manipulation of data from its visual representation.
#### 3 components
Each component of this pattern is responsible for one thing, which, when combined, generates a UI.
- **model** - the data that needs to be displayed
- **view** - the template that displays the data provided by the model
- **controller** - (optionally) updates the model and provides data for display to the view
#### Process
In terms of Razor pages, the process goes something like:
1. the controller (Razor pages handler in the code-behind file) receives the request
2. depending on the request, the controller either fetches the data from the application model using injected services, or updates the data that makes up the model
3. the controller selects a view to display and passes a representation of the model-- *the view model* to it
4. the view uses the data contained in the model to render its UI

In this way, the controller serves as the entry point for the interaction. The user communicates with the controller to start an action, and in web applications, this action takes the form of an HTTP request.

Depending on the nature of the request, the controller may take a variety of actions, but the key point is that the actions are taken using the application model, and injected services are the functions that allow you to interact with it. The model contains all the business logic for the application, so it's able to provide requested data or perform actions.

##### Advantages
- the model representing the application data is decoupled from the final representation of the data--the view
- the controller is responsible for then deciding whether the response should generate an HTML view, an error page, or a page redirect
- model being independent from the view improves testability, which ensures that the model is easily testable, without any dependencies on UI constructs


