## Applications

- An **application** is anything with a `.exe` extension that can be started from the shell

### Application Lifetime
- A winforms application starts when the main method is run, but in order for it to fully initialize and start routing winforms events, you need to invoke `Application.Run()` in one of three ways

**1. Call Run with no arguments**
When run with no arguments, the app runs until explicitly told to stop, even when all its forms are closed. This puts the burden on some part of the app to call the `Application.Exit()` method. You typically do this when you need a secondary UI thread which can process events that drive a Winforms app.
```cs
static void Main() {
        Mainform form = new();
        form.Show();
        Application.Run();
}
```

**2. Call Run with a reference to the main form**
- When the Run method takes in an argument the form designated as the main form, it doesn't return until the main form closes
```cs
Mainform form = new();
Application.Run(form);
```

### Application Context
- You can create your own context to override events such as `OnMainFormClosed` to stop the app from exiting even if the main form has already closed

### Application Events
- During the lifetime of an application, several key application events such as Idle, ThreadExit, and ApplicationExit are fired; it's most common to subscribe to these events in the main function

```cs
Application.Idle += App_Idle; // no more events are waiting to be processed
Application.ThreadExit += App_ThreadExit; // notifies the UI thread when it's about to exit
Application.ApplicationExit += App_ApplicationExit; // fires when the last UI thread goes away
```

### UI Thread Exceptions
- An event is fired when a UI thread causes an exception to be thrown and shows a dialog where you can continue
- You can replace the default Winforms unhandled exception dialog by handling the app's thread exception event
- You can also make it so that users won't be involved in unhandled exceptions by making sure the thread exception event is never fired
```cs
static void Main() {
        Application.ThreadException += App_ThreadException;
}
```

### Command Line Arguments
- CLI args allow users to determine an app's initial state and behavior when launched
- You have to allow the main method to take in a string of args, but you can also handle them elsewhere by:
```cs
string[] args = Environment.GetCommandLineArgs();
```

**Multi SDI** - single process, multiple windows, no top level form (chrome, word)
**MDI** - forms within a form; excel