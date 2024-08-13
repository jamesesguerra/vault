An **interface** is another custom type that defines an object's interface or boundary by listing the methods and properties that an object must have without supplying any behavior for them. They are **fully** abstract classes that define a specific role in the system.

### Interface Examples
Interfaces in the real world can be seen everywhere. For example, the steering wheel, pedals, shifter of a car or the keys of a piano. Different cars and pianos have the same interface. Users can use different cars and pianos the same way even if they aren't all the same under the hood since they all have the same interface.

They make code more flexible since they allow you to substitute unrelated objects (objects not from the same inheritance hierarchy) to fulfill a specific role in the system.

### Defining Interfaces
Say we are building a web service and are deciding on what database to use. Interfaces come in handy since there are many DB options to choose from that each produce the same result / have the same responsibility. You may not know yet which DB you'll end up using / if you'll end up changing in the future, and so to preserve as much flexibility around this decision, you simply **define what the role must do**.

Members of an interface are `public` and `abstract` automatically since implementing the interface is like signing a contract that states you'll promise to do a certain thing and make it available for the outside world to interact with.

```cs
public interface IDBConnection {
	DataTable Select(string query);
}

public class MySQL : IDBConnection {
	public DataTable Select(string query) {
		// provide mysql-specific select implementation here
	}
}
```

You can then create variables of the interface type and assign it anything that implements the interface. The rest of the program doesn't care which implementation of `IDBConnection` is being used and will be oblivious if you choose to switch the database class.

The difference here is that if we choose to use a concrete class , there is nothing stopping the rest of the program, or the outside world, from accessing methods that are specific to that concrete class and isn't something that's listed on the interface. By using the interface, you can be certain that all other method calls will still be valid even if you change databases.

```cs
IDBConnection dbConnection = new MySQL(); ✅
MySQL dbConnection = new MySQL(); ❌
```

![[abstract-vs-interfaces 1.png]]