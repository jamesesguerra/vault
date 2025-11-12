## az functions
- event-driven, serverless compute option
- an event wakes the function and function runs task
- ideal when you’re only concerned about the code running your service and not the underlying infrastructure
- commonly used when the work can be completed quickly

## benefits
- **spend more time building app** instead of administrative tasks (installing updates etc.)
- **no infrastructure management**
- **scalability** 
- **only pay for what you use** - only charged for the time it takes to run your code

## stateless & stateful
- **stateless** - default; behave as if they’re restarted every time they respond to an event
- **stateful** - aka durable functions; a context is passed through the function to track prior activity