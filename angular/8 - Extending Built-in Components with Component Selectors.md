Rendering a component on the page makes it so that both the component element and its markup are added to the DOM. This might be unnecessary if, for example, you create a shared button component and its markup will end up being like

```html
<app-button>
	<button> Send </button>
</app-button>
```

You can choose to extend the existing button element with your own custom markup by using attribute selectors.

You aren't restricted to using component names in the `selector` configuration of a component. You can also target other things like attributes and classes.

```ts
@Component({
	selector: 'button[appButton]' // attribute selector for buttons with an attribute appButton
})
export class ButtonComponent { } 
```

**NOTE:** Make sure to also import the component using the attribute selector within the component you're going to use it in 

