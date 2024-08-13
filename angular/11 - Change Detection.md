**Change Detection with zones**  
Angular's change detection mechanism is powered by zone.js, which notifies Angular about user events, expired timers etc. When Angular receives a notification, it checks components for changes and updates the DOM accordingly. This makes it so that you can change state by simply updating the properties of the class

```ts
this.selectedUser = DUMMY_USERS[0];
```

**Signals - trackable data containers** 
A signal is an object that stores a value (any type of value, including nested objects). These containers notify Angular when their values change, and it can update the part of the UI that needs changing, which can be very efficient. It doesn't have to check for changes in every component in a zone, components can just subscribe to signals and get updated when those signal values change.

### Change Detection Optimizations
1. Avoid calling functions in your template. The exceptions to these are event bindings and signal reads.
2. Avoid having expensive operations in getters. 
3. Opt out of zone.js change detection when you aren't changing data, for example, in a timer that will expire. This is because once the timer expires, zone.js will notify Angular something might have changed and it'll check every component again. But sometimes you're sure that nothing needs to be changed when that timer expires.
```ts
zone = inject(NgZone);

constructor() {
	this.zone.runOutsideAngular(() => {
		setTimeout(() => {
			console.log('hello');
		}, 4000);
	});
}
```

### The OnPush strategy
Using the OnPush change detection strategy can optimize your app even more because it makes it so that the component using this strategy will only be checked for changes if it / any of its child components' inputs change / when an event occurs in those components / when a signal in those components changes.

```ts
@Component({
	changeDetection: ChangeDetectionStrategy.OnPush
})
```

#### Triggering change detection manually
If you use the OnPush strategy for a component and that component uses a service that doesn't use signals. It won't get checked for changes if another component changes something using the service. This is because, again, an OnPush component will only get checked for changes if an input changes, an event is triggered, or a signal value changes. In those cases, you can choose to trigger the change detection cycle manually using RxJS.

```ts
// service
import { BehaviorSubject }

export class MessagesService {
	messages$ = new BehaviorSubject<sting[]>([]);

	onAddMessage(message) {
		this.messages$.next(message);
	}
}
```

```ts
// component
export class MessagesListComponent implements OnInit {
	private cdRef = inject(ChangeDetectorRef);

	ngOnInit() {
		this.messagesService.messages$.subscribe(() => {
			this.cdRef.markForCheck();
		})
	}
}
```

An alternative to doing all this setup is simply using the `async` pipe, which apart from subscribing and unsubscribing to an observable, also triggers the change detection ref. 
