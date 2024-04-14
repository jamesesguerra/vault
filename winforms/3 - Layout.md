## Layout Basics
> Fundamental elements are **positioning, sizing, and ordering**

### Position and Size
- **Position** - is in respect to the upper-left corner of the parent; which is of type `Point`
- **Size** - size of the control; which is of the type `Size`
```cs
myButton.Location = new Point(0, 0);
myButton.Size = new Size(50, 50);
```

### Margin and Padding
- **Padding** - space inside a control; of type `Padding`
- **Margin** - spacing between adjacent controls; of type `Padding`; unfortunately only for snap lines in designer
```cs
myButton.Padding = new Padding(10); // sets top, right, bottom, left to padding 10
myButton.Margin = new Padding(5, 5, 5, 5); // (left, top, right, bottom)
```

### Z-Order
- There's a vertical stack (a 3rd dimension) of controls in a container, with controls higher in the stack being rendered on top of controls lower in the stack
- **Z-Order**
  - determines the position of a control in the vertical stack;
  - lowest z-order is 0 -- means it's the highest in the stack
  - controls added first are rendered last -- means they are on top of controls lower in the stack

### Tab Order
- Controls the sequence in which a user can navigate / tab through controls at runtime
- A control is registered to be able to be tabbed if its `TabStop` property is `true`
- Order is determined by the `TabIndex` property which starts at 0
- First control in the tab order receives focus when form is opened

---
### Dynamic Layout

#### Anchoring
- Preserves the distance between the edge of a control and the adjacent edge of a control's container
- Control is resized if user anchors two opposing edges
- Maintain proper sizing by setting the form's `MinimumSize` property

#### Docking
- **Docking** is a way to identify which edge we want a control to "stick" itself to
- Controls lower in the z-order stack have higher docking priority = docking order is the reverse of z-order
- `ToolStripContainer` control allows you to drag and drop toolstrips without worrying about the docking; `GripStyle` has to be set to `Visible`

#### Automatic Resizing
- Steps taken to ensure that controls that are resized/moved are still visible in the container.
-  `AutoSize` - when set to `True`, specifies that a control will resize automatically to fit its contents
-  `AutoSizeMode` - governs how the control resizes

#### Automatic Scaling
- Maintain the same proportions, or scale, across multiple dpi settings
- Need to tell the form to scale by setting `AutoScale` to true and specify scale factor with `AutoScaleMode`
- Safest is to leave `AutoScaleMode` to its default which is font

---
### Container Controls
#### Split Layout
- use `SplitContainer` to configure vertical / horizontal splitting

#### Grouping
- encapsulates a reusable portion of the UI that you can drag around as 1 unit
- `GroupBox` - has a label and frame
- `Panel` - no label and frame; acts as a subform (form within a form)
- `TabPage` - hosts a container control in each of its `TabPage` controls