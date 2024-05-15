### Reflection

Reflection is a mechanism that allows you to retrieve the metadata and structure of your code / types at runtime. The important thing to note here is that you can do this *at runtime*.

In this example, you can get the type of the object at runtime, and get its properties and values as well even if you don't know what type will be passed into it at runtime

```cs
class ObjectToTextConverter
{
    public string Convert(object obj)
    {
        Type type = obj.GetType();
        var properties = type
            .GetProperties()
            .Where(p => p.Name != "Equality Contract");

        return string.Join(", ", properties.Select(p => $"{p.Name} is {p.GetValue(obj)}"));
    }
}
```

### Attributes
Compiled C# code retains a lot of rich information about the code itself. Attributes are a way to add **metadata** to the richness. They add information about a type or method to this type's existing metadata. Tools to inspect your code (such as reflection) can access this metadata and change their behavior in response.

Attributes are just classes that derive from the `Attribute` class. The parameters you pass to when you use each attribute correspond to the attribute class's constructor

```cs
public 
```
