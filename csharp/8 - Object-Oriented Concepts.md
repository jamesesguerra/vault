
### Dealing With Large Programs
- We use OOP to deal with large programs by dividing the state and code into **objects**, each responsible for doing one thing
- Each object works in coordination with other objects to solve the overall problem
- We also use OOP to represent complex concepts
- It's important to use the right type for everything you create; if the right type doesn't exist, create if first

### Objects
- Objects are tiny programs that work together to become 1 big program
- Objects have variables to store what state the object is in, and methods to also allow other objects to make requests of it
- They are responsible for and good at one thing only, if other people want to communicate with them, it needs to pass them a message, and if it cannot complete a task on its own, it sends a message to other objects
- Some objects don't need to store the state its in and only have methods 
- We use them to represent more complex concepts if the 14 simple types aren't enough

#### Interfaces
- The only way you should interact with objects is through its interfaces
- Write programs not in terms of manipulating the data but in terms of instructing the object to do something

### Padding in Pong Example
- State
	- x and y positions (can encapsulate this into a general `position` which contains both values)
	- speed
- Operations
	- move up
	- move down
	- draw
	- get position
- Interface
	- move up 
	- move down
	- draw
	- get position

### Big Idea
- OOP is more about messaging than objects 
- It's also about the idea of **late binding**; late binding is the idea that we don't know what an object will do when we send it a message, it's up to it what kind of thing it's going to do based on the state of the system
- Not so much about classes and interfaces but making systems scalable and easy to modify in the face of inevitable change

### Links
1. https://blog.sigma-star.io/2024/01/people-dont-understand-oop/