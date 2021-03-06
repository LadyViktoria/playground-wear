# playground-wear

Couple of samples showcasing Android Wear APIs.  

This work was the primary preparation for **Developing for Android Wear** session in [ADD 2015] (http://www.androiddeveloperdays.com/2015/)

You may check the presentation [here] (https://speakerdeck.com/canelmas/developing-for-android-wear)

# TakeAways

Notes to self..

Code and notes were written a year ago. So there might be couple of out dated things.

## 1. Adding Wearable Features to Notifications

### 1.1. Wearable Features

* Simple notifications, with actions or not, are shared with the wearable
* No background (default set to green)
* Notification icon shown on the wearable is by default the app icon
* Implicit intents are triggered on the wearable and resolved on phone 
e.g. Show on Map action clicked on wearable asks app to resolve the intent
* Action triggered doesn't dismiss the notification on the wearable
* Notification dismissed on the phone also dismiss from wearable and vice versa
* By default each notification shared on wearable has 'Block App' action
* Once a wearable-only action is added, only these type of actions are displayed on wearable and **they're not displayed on phone.** 
* Phone actions are ignored on wearable if wearable-only action is added
* ContentIntent (intent when notification is clicked) also adds 'openOnPhone' action to wearable
* On wearable `BigContent` (BigStyle.bigText) is visible by default; on phone big view is visible when the notification is expanded
* `NotificationCompat.Builder.setLargeIcon()` appear as large background images on wearable and they don't look good (scaled up to fit the wearable screen)
* `WearableExtender.setBackground()` should have 400x400 for non scrolling, 640x400 for backgrounds that support parallax scrolling. 
These bitmaps should be in **drawable-nodpi**; Other non-bitmap resources for wearable notifications e.g. WearableExtender.setContentIcon(), 
should be in **drawable-hdpi**

### 1.2. Receiving Voice Input

* Voice Input should be added as notification action
* Use `RemoteInput.getResultsFromIntent()` instead of `Intent.getExtras()` to obtain the voice result. Voice input is stored as ClipData.
* Pre-defined text responses (max 5), in addition to voice input can be set via `RemoteInput.setChoices()`

### 1.3. Pages

* Extra pages are Notification instances, added via `WearableExtender().addPage(notification)` or `addPages(notification:Collections)`

### 1.4. Stacking Notifications

* `setGroup()` should be called on notifications in order to group them and display in a single page on wearable
* If user has a notification of type T listed (not dismissed yet) and receives another notification of the same type, instead of raising 
a new notification you may cancel listed one and raise a summary notification by setting `setGroup()` and `setGroupSummary(true)` to a notification.
* `InboxStyle` is useful for styling summary notification. See Styling with HTML markup and Styling with Spannables for more info.
* To detect notification dismissal, use either Notification Listener API (4.3) or set a DeleteIntent for the notification

## 2. Wearable Apps

* SDK tools version 23.0.0 or higher
* Android API 20 or higher
* Enable adb debugging (same 7 taps) and enable Debug over Bluetooth
* Add wearApp project(':<wear-module>')
* Include all the permissions declared in the manifest file of the wearable app module in the manifest file of the handheld app module.
* Both wearable and handheld app modules should have the same package name and version number
* Set up a Debugging Session i.e Enable Debugging over Bluetooth on Wear android companion app
* Connect the handheld to your machine over USB 
```
adb forward tcp:4444 localabstract:/adb-hub
adb connect localhost:4444
```

### 2.1. Correct Libraries

* **Notifications that appear only on wearable is only possible with an app running on the wearable
issuing these notifications**. Standard library is sufficient; remove support library dependency
* To sync data between wearable and handhelds with Wearable Data Layer API, play services dependency is required
* Wearable support library is required and recommended for UI widgets designed for wearable

### 2.2. Custom Layouts

* Create custom layout only when necessary. https://developer.android.com/design/wear/index.html

### 2.3. Designing for Wearable

* Different strengths and weaknesses, different use cases, different ergonomics
* Launched automatically based on user's context : time, location, physical activity and so on
* Fast, sharp and immediate - Glanceable
* All about suggest and demand
* Zero or low interaction - Small interactions; user input only when necessary.
 
### 2.4. Principles

* Focus on not stopping the user
* Time required for each action should be not more than 5 sec e.g. buy something to Jane, navigate to work
* Design for big gestures
* Think about stream cards first - Best experience is when the right content is there just when user needs it
* Well designed stream card carries one bit of information and potentially a few action
* Do one thing, really fast - Glanceability

### 2.5. App Structure

* No tapping an icon to launch Wear app
* Typical wear app adds a card to the stream at contextually relevant moment

#### 2.5.1. Contextual Cards in the Stream

##### 2.5.1.1. Bridged Notifications

* Delivered from handheld
* Wear specific features e.g extra pages, voice reply and other wear specific actions

##### 2.5.1.2. Contextual Notifications

* Triggered when an appropriate event occurs e.g. exercise card shown when user starts running
* Generated locally on wearable and appear at contextually relevant moments
* Standard Android Notifications or also custom layout with an activity inside a card
* Go Full Screen only when you can't do what you want on card and quickly exit back to the stream

#### 2.5.2. Full Screen UI App

* Custom Layouts
* 2D Picker - Card-Like UI component (GridViewPager)
* Minimize the number of cards
* Show the most popular card at the top
* Keep the cards extremely simple
* Optimize for speed over customization

### 2.6. Context Awareness

* https://developer.android.com/design/wear/context.html

### 2.7. Creating Custom Layouts

* Wearable UI Library is part of the Google Repository i.e. com.google.android.support:wearable:+
* Do not port functionality and the UI from a handheld app and expect a good experience
* Content for square may be cropped on round Wear devices

### 2.7.1. Layouts

* `WatchViewStub` or `BoxInsetLayout`
* `WatchViewStub` detects screen shape and inflates either round or square layout provided separately
* `BoxInsetLayout` lets you define a single layout for both square and round screens
* `BoxInsetLayout` padding only applies to square screens

### 2.7.2. Creating Cards

* Android Notifications are also displayed as cards
* To add cards to Android wear activities : `CardFragment` or CardFrame inside CardScrollView 


### 2.7.3. Creating Lists
* `WearableListView` optimized for wearable devices

### 2.7.4. 2D Picker

* Design Pattern for showing options in a list e.g. Google search results
* FragmentGridPageAdapter for providing pages to populate GridViewPager component

### 2.7.5. Confirmations

* DelayedConfirmationView
* ConfirmationActivity with `SUCCESS_ANIMATION`, `FAILURE_ANIMATION` or `OPEN_ON_PHONE_ANIMATION`

### 2.7.6. Exiting Full-Screen Activities

* By default left-to-right swipe
* To turn off default gesture :
```html
<style name="AppTheme" parent="Theme.DeviceDefault">
    <item name="android:windowSwipeToDismiss">false</item>
</style>
```
* Alternatively you can implement long press to dismiss pattern with DismissOverlayView

## 3. Sending and Syncing Data

* Wearable API available only with 4.3 (API 18) or higher
* Latest version of Google Play

## 3.1 Daya Layer API

* __Data Item__, data storage with automatic syncing between handheld and wearable
* __MessageApi__, send messages and for RPC. Start intent on the wearable from handheld. Great for one-way requests or
  request/response communication model
* __Asset__, for sending blobs of data
* __WearableListenerService__, to listen important data layer events in a service. Lifecycle is managed by the system; binding to
and unbinding the service is done by the system.
* __DataListener__, to listen important data layer events when an activity is in the foreground e.g listen for changes only when
the user is actively using the app
* __ChannelApi__, to transfer large data items, such as music and movie files, from handheld to a wearable device.
* Benefits of using Channel Api for data transfer :
    1. Reliable
    2. Transfer streamed data, such as music pulled from a network server or voice data from the microphone
    3. Saves disk space unlike DataApi class which creates a copy of th assets on the local device before synchronizing with
    connected devices
    
## 3.2. Syncing Data Items

* Data Items limited to 100KB; assets can be as large as desired

## 3.3. Transferring Assets

## 3.4. Sending and Receiving Messages

* Multiple wearable devices can be connected to a user's handheld. Find the node you're looking for.
* With Google Play Services 7.3.0 only one wearable device could be connected to a handheld at a time.