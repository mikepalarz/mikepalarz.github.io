---
layout: post
title:  "Reacting to Sticky Broadcasts"
date:   2019-06-05 18:00:30 -0500
categories: android,rxjava
---
# Android Is a Reactive Playground
If you read my [previous post about learning RxJava](/2019/06/my-path-to-rx-java), you might recall a project I was going to work on as my first real dive into RxJava. That project is wrapped up and I'll be going over some highlights and what I learned in the process.

### The goal
The overall goal of the project was to listen to [sticky broadcasts](https://stackoverflow.com/questions/2584497/what-is-the-difference-between-sendstickybroadcast-and-sendbroadcast-in-android) sent by the Android framework and _react_ to them. Unfortunately, Google has been [limiting the types and number of system broadcasts](https://developer.android.com/guide/components/broadcasts#changes-system-broadcasts) that are sent by the framework. I didn't view this as a showstopper though since I only wanted a few broadcasts to listen to. I can also see how, in general, doing anything with sticky broadcasts can raise some skepticism. There's likely more than one reason, but  one reason is likely the fact that some of these broadcasts are sent _very_ frequently and this can lead to some messy situations such as memory leaks and superfluous amounts of overhead. For example, if you're trying to (for whatever reason) update your UI each time `ACTION_BATTERY_CHANGED` is sent, I wouldn't expect this to lead to a very positive outcome since the UI would be redrawn very frequently. I wouldn't be surprised if this were to eventually lead to an ANR, if not at least a very unpleasant experience for the end user.

With all of that in mind, I wanted to create a couple `Observables` to emit events each time a sticky broadcast is sent. Then, once the subscription was ended, the UI would be updated to show which broadcasts were received. I felt like it's a simple concept yet challenging enough to keep me interested.

### What I Learned
There were a few things that I learned from while working on this project:

* RxJava is certainly a _powerful_ tool, even for simple projects such as this.
* Surprisingly, how the Android framework works by itself is a crazy and (might I say) _reactive_ world! Even with the simple concept of sticky broadcasts, there's so much going on under the hood that we often don't think about or interact with. It was enlightening to see how much activity I was seeing, even for this simple scenario.

# The Implementation
Alright, so enough jabbering and let's get to the good stuff: _the code_. I'll be using a `BroadcastReceiver` to intercept each sticky broadcast that I'm interested in. I'll also be using [context-registered receivers](https://developer.android.com/guide/components/broadcasts#context-registered-receivers) for good practice and also because I wasn't interested in the [the broadcasts exempted from the API 26 restriction of implicit broadcasts](https://developer.android.com/guide/components/broadcast-exceptions.html) (they didn't seem as much fun to interact with). The first step was to define which broadcasts to listen to through some filters:

```
// Intent filters needed for broadcasts
private final IntentFilter mBatteryFilter = new IntentFilter(Intent.ACTION_BATTERY_CHANGED);
private final IntentFilter mAirplaneModeFilter = new IntentFilter(Intent.ACTION_AIRPLANE_MODE_CHANGED);
private final IntentFilter mTimeFilter = new IntentFilter(Intent.ACTION_TIME_TICK);
private final IntentFilter mScreenOnFilter = new IntentFilter(Intent.ACTION_SCREEN_ON);
private final IntentFilter mScreenOffFilter = new IntentFilter(Intent.ACTION_SCREEN_OFF);
```

So, we'll be reacting to each time...
* The battery level changes
* Airplane mode is turned on/off
* Time passes by (i.e., a minute passes by)
* The screen is turn on/off

With this, I can see items emitted from my `Observables` without interacting with the device (via `ACTION_BATTERY_CHANGED` and `ACTION_TIME_TICK`) as well as with interaction (via the remaining filters). 

Next up, I wanted to create `Observables` for the broadcasts that are being sent. I also wanted to have several `Observables` for experimentation and learning purposes. Therefore, I created a helper method to return me an `Observable` using an `IntentFilter`:

```
private Observable<String> broadcastObservable(IntentFilter filter) {
	Observable<String> observable = Observable.create(observer -> {
	    Log.i(TAG, Thread.currentThread().getName() + ": Creating observable");
	    final BroadcastReceiver receiver = new BroadcastReceiver() {
		@Override
		public void onReceive(Context context, Intent intent) {
		    String action = intent.getAction();
		    observer.onNext("Action: " + action);

		    if (action.equals(mAirplaneModeFilter.getAction(0))) {
			observer.onNext("Airplane mode on: " + intent.getBooleanExtra("state", false));
		    } else if (action.equals(mTimeFilter.getAction(0))) {
			observer.onNext("And time goes on...");
		    }                 }
	    };

	    getActivity().registerReceiver(receiver, filter);
	});

	return observable.subscribeOn(AndroidSchedulers.mainThread())
		.observeOn(AndroidSchedulers.mainThread());
}
```

I then create my `Observables` within `onCreate()`:

```
@Override
public void onCreate(@Nullable Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);

	mBatteryObservable = broadcastObservable(mBatteryFilter);
	mAirplaneModeObservable = broadcastObservable(mAirplaneModeFilter);
	mTimeObservable = broadcastObservable(mTimeFilter);
	mScreenOnObservable = broadcastObservable(mScreenOnFilter);
	mScreenOffObservable = broadcastObservable(mScreenOffFilter);
	mAllBroadcasts = Observable
		.merge(Arrays.asList(mBatteryObservable, mAirplaneModeObservable, mTimeObservable, mScreenOnObservable, mScreenOffObservable))
		.observeOn(AndroidSchedulers.mainThread())
		.subscribeOn(AndroidSchedulers.mainThread());

    }
```

Now for the trickiest part throughout this project. To make things interesting, I wanted to have a button within the UI that would toggle the subscription. If the button was clicked the first time, then the subscription would be established and we'd start listening to events emitted by the `Observables`. If the button was clicked again, the subscription would be ended and the UI would be updated with the broadcasts that were received. If the button was clicked again, then we'd start listening to broadcasts again and the process repeats. I'd also have a separate button to clear the broadcasts shown in the UI if desired. Here's a breakdown of the logic flow for how I accomplished this:

* A `boolean` would be used as a flag. Each time the button is clicked, the `boolean` is negated.
* If the `boolean` is true, we subscribe to all of the `Observables` and log the actions that occured.
	* The subscribe button's text is updated to "Stop observing" and the clear logs button is disabled.
* If the `boolean` is false, we unsubscribe from all of the `Observables` and update the UI with the logged broadcasts.
	* The subscribe button's text is updated to "Let's observe" and the clear logs button is enabled.

This was all accomplished programmatically as follows (prepare for some aesthetically pleasing lambda expressions);

```
mSubscribeButton.setOnClickListener((View view) -> {

    mSubscriptionToggle = !mSubscriptionToggle;

    if (mSubscriptionToggle) {
	mAllBroadcasts
		.doOnDispose(() -> {
		    Log.i(TAG, "Unsubscribed from all broadcasts");
		    mBroadcastsTextview.setText(mStringBuilder);
		    // Clearing the StringBuilder
		    mStringBuilder.setLength(0);
		    // Re-enabling the clear broadcasts button
		    mClearButton.setEnabled(true);
		    mSubscribeButton.setText(R.string.fragment_sticky_subscribe_button_before_subscribe_text);
		})
		.subscribe(
			// onNext()
			string -> {
			    Log.i(TAG, string);
			    mStringBuilder.append(string + "\n");
			    },
			// onError()
			(error) -> Log.e(TAG, "Error occurred: " + error),
			// onComplete() should never be called since these broadcasts will always
			// be sent. I.e., we have a never-ending stream of events.
			() -> Log.i(TAG, "onComplete()"),
			// onSubscribe()
			disposable -> {
			    Log.i(TAG, "Subscribed to all broadcasts");
			    mBroadcastsTextview.setText(R.string.fragment_sticky_textview_logging_broadcasts);
			    // We're listening to broadcasts, so let's not enable this button
			    mClearButton.setEnabled(false);
			    mSubscribeButton.setText(R.string.fragment_sticky_subscribe_button_during_subscription_text);
			    mDisposable.add(disposable);
			});
    } else {
	mDisposable.clear();
    }

})
```
And that's essentially the "meat and potatoes" of the app. Here's what it looks like in action:

<img src="/assets/posts/2019-06-05-reacting-to-sticky-broadcasts/rxsticky_example.gif" 
alt="RxSticky Example" width="480" height="960" border="10" />

Feel free to check out the [full source code](https://github.com/mikepalarz/RxSticky).
