### Drawing to the Screen
- No matter what drawing you're doing, the underlying abstraction is the `Graphics` class from the `System.Drawing` namespace
- 1 way to obtain a `Graphics` object is using the `CreateGraphics` method 
```cs
usin System.Drawing;

Graphics g = CreateGraphics();
g.FillEllipse(Brushes.DarkBlue, ClientRectangle);
```

### Handling the Paint Event
- You can handle the paint event to draw when the form is resized (paint event fires when controls need repainting, which is probably when the form is resized)
- Paint event has its own `Graphics` object for us
- You may want to force the paint event to happen since Windows prioritizes it last for optimization
- `Invalidate()` simply flags the region as requiring painting, but does not necessarily paint
- `Update()` forces any currently invalid regions to repaint, but does not flag anything
- `Refresh()` wraps the two operations together, first flagging the control's region as invalid and then immediately requesting the region be updated (repainted)
```cs
Invalidate(true); // request paint event and redraw form and child controls
Invalidate(fales); // redraw the form only
Update(); // force paint event to happen now
Refres(); // Invalidate(true) + Update
```
- You can also request that the whole form be repainted on a resize
```cs
SetStyle(ControlStyles.ResizeRedraw, true);
```

### Colors
- Brushes are for filling the interior of a shape, pens are used to draw the edge of a shape
- Create colors with `Colors.FromArgb()`, from the `Colors` and `SystemColors` classes

### Brushes
- **Solid brushes**
```cs
Brush whiteBrush = Brushes.White; // using managed brush with built-in colors
var whiteBrush2 = new SolidBrush(Color.White); // make your own
```