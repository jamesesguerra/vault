## Dialogs
> Dialogs are defined by their use. If a form is an app's main window, it's a window and not a dialog. If a form pops up in response to a user request, and stops all other user interactions with the app, it's a dialog.

### Standard Dialogs
- Common dialogs that come with Windows

### Data Exchange
- If you need a form client to change a property of a control, don't make it public. Instead use properties that get and set the form control's properties:

```cs
NewForm newForm = new();
newForm.LabelName.Text = "I'm a new form"; // don't... if you decide to change the control from a textbox to a label then you have to change all the lines that reference it being a textbox

public string LabelName
{
    get { return _label.Text; }
    set { _label.Text = value; }
}
```

### Handling OK and Cancel
- Add `Close()` in each button's click event handler
- After a form is closed, `ShowDialog()` returns a `DialogResult` value that specifies the condition under which the form was closed.