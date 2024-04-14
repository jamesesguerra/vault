## Multithreaded User Interfaces
- You need to support long-running operations by running them in a way that the app continues to interact with the user without freezing, while still allowing the user to see the progress of the operation and even cancel to cancel it

### Long-running operations
- The problem with the PI calculator application is that it only has a single thread of execution. This means that while it's calculating PI, it can't also be drawing the UI in response to system paint requests
- When the user switches the app to the background and then the foreground again, the system requests the main form to repaint its entire client area, and that means processing the Paint event. However, no events can be processed until the app can return from the click event on the calculate button, and so the user doesn't see any display of progress until all the digits have been calculated
- The same problem prevents the user from interacting with the text box or resizing the form

### Asynchronous Operations
- A **thread of execution** or a **thread** is a series of instructions and a call stack that operate independently of the other threads in the application
- Starting a new thread is an asynchronous operation in that the current thread continues executing independently of the new thread
- You start a new thread by creating a Thread object from the System.Threading namespace, passing a delegate as the constructor parameter, and invoking the Start method

```cs
void calcButton_Click(object sender, EventArgs e) {
        // set calculating UI here
        // start pi calculation on new thread
        Thread piThread = new(CalcPiThreadStart);
        piThread.Start((int) decimalPlacesNumericUpDown.Value);
}

void CalcPiThreadStart(object digits) {
        CalcPi((int) digits);
}
```