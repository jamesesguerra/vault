### Showing Forms
Forms can either be shown modelessly or modally:
- **modelessly** - no relationship between current and new form; means the current form can be closed and new form will be left behind
- **modally** - doesn't return control to current form until new form has been closed with either the `Close` method or by the setting `DialogResult` property
- You can still establish an owner-owned relationship between two forms by passing `this` to `form.Show(this)`

```cs
// modeless
NewForm newForm = new();
newForm.Show();

// modal 
newForm.ShowDialog();
```

### Form Lifetime
- A form comes into existence as soon as it's instantiated

![image](https://gist.github.com/assets/68677613/d1390ea4-12e4-45c6-96e1-a7f803ac365e)

#### Form Opening / Deactivation
- Form wakes up when constructor is executed -> calls the `InitializeComponent` method to create and initialize all controls
- You may add extra code / controls after the call to `InitializeComponent` in the constructor
- `Show` & `ShowDialog` is the form's cue to show itself and all child controls -> handle `Load` event to be notified before this happens
- `Activated` event fires when the form loads for the first time and is able to receive keyboard input; is fired again when you switch away and back to the app
- `Shown` event fires after `Activated` event
- `Deactivated` even fires when a user switches away from the app; can handle to pause any activity that can't continue without user involvement

#### Form Closing
- `this.Close()` to close form
- handle `FormClosing` event to give users the option to change their mind by setting `FormClosingEventArgs.Cancel` = true:

```cs
private void NewForm_FormClosing(object sender, FormClosingEventArgs e)
{
    DialogResult result = MessageBox.Show(
        "Abort your game?", "Game In Progress", MessageBoxButtons.YesNo
    );

    e.Cancel = (result == DialogResult.No);
}
```

#### Form Visibility
- Form activity depends on how its visibility
- toggle visibility with `this.Hide()` or `this.Visible = !this.Visible`
- `VisibleChanged` event fires

#### Form Notification
- use `Activate()` to notify the user when a long-running task has completed

#### Strips and Menus
- **menu strip** - many items across the top; can have items below
- **context menu strip** - 1 form / control can have at most 1 context menu strip; vertical at every level; assign to a form / control and then right-click to view
- **tool strip** - visual menu strip (icons only)
- **status strip** - provide app status and context, usually at the bottom

### MDI Apps
- Multiple Document Interface (MDI) apps contain a set of related windows in a single frame