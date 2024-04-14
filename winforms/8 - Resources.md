### Resource Basics
- Resources are named pieces of data bound to an assembly at build time
- They are used to bundle data such as text, graphics, videos, into your apps for use at runtime
- They also serve as the building block for internalization to support no-compile deployment of localized resources

You do have the option to ship your images with your app and then load them when you need to, but not all installations have this image in the same place, let alone this image at all. And even if you *choose* to ship it with your app, a space-conscious user might choose to delete it, causing your app to fault. The safest way is to embed it as a resource.

```cs
this.BackgroundImage = new Bitmap(@"c:\windows\web\wallpaper\azul.jpg");
```

### Manifest Resources
- Resources are added to an assembly at compile time
- To embed a resource, add the file to the project -> right click -> Properties -> change Build Action to Embedded Resource
- Embedded Resource means that it becomes embedded in the assembly's set of manifest resources
- **Manifest** of an assembly is composed of a set of metadata that describes part of the assembly such as the name and data
-

### Loading Resources
- You can load embedded resources by loading it as a raw stream of bytes

```cs
public MainForm() {
        Assembly asm = this.GetType().Assembly; // get this type's assembly
        Stream stream = asm.GetManifestResourceStream("<Namespace>.<AnySubfolders>.<ResourceName>");
        // or
        Stream stream = asm.GetManifestResourceStream(GetType(), "<ResourceName>");
        BackgroundImage = new Bitmap(stream);
}
```

- You can also use a shortcut for types that can directly load files that are embedded resources

```cs
BackgroundImage = new Bitmap(GetType(), "<ResourceName>");
```

### Application Resources (.resx) Files
- Embedded resources don't come with their type information (jpg, png)
- Application resources persist resource type information by employing and XML schema called ResX
- Resources Editor allows you to add/edit resources by type; you can also drag directly to it
- All resource files added to a `.resx` file will be added to Resources folder in the project
- You can only delete resource metadata from `.resx` files; if you want to completely delete it, you must delete the metadata and then delete it from the project
- Project-wide resources are added in the Properties folder

### Resource Persistence
- After a resource is added to a project, you can specify the way it's associated with the project by either **linking** or **embedding**
- **linked at compile time** means that the data is stored in a separate file but is referenced from the resx file using a relative file path
- **embedded** causes the resource to be sucked into the `.resx` file for the duration of the development
- both types of persistence are ultimately compiled into an assembly since `.resx` files themselves have a build action that is set to Embedded Resource

### Resource Manager
- Supports random access to resources and is initialized with an embedded `.resources` file
- Using a resource manager associated with a specific type is labor-intensive, use strongly typed resource classes instead

### Strongly Typed Resource Classes
- Set `ResXFileCodeGenerator` as the value of the Custom Tool property of the `.resx` file to generate a `Resources.Designer.cs` file
- Resources added to the `.resx` file are exposed as readonly properties, and you can now access resources with a single line of code

```cs
BackgroundImage = Properties.Resources.azul.jpg;
```

### Resource Association
- Resource designer files are also generated for forms to allow you to import image resources as a local resource (separate from the project `.resx`)
- Local resources are embedded into the `.resx` files associated with the form