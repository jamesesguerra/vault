The `[ApiController]` is an attribute you can opt in to so that you can use common conventions in your API controller to make everything consistent throughout your app.

Without this attribute, you have to:
- explicitly add the attribute `[FromBody]` so that the model gets bound from the JSON body and not form values
- return a `400 BAD REQUEST` if the values provided failed parameter validation 
- return a problem details object when returning an error

What this attribute buys you is:
- automatic `400` responses
- automatic binding from JSON body instead of form values
- automatic problem details objects by returning an error 