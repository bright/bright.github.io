---
layout: post
title: 3D Touch - Adopting shortcut items to your app.
author: kwysocki
tags : [iOS, swift, 3d touch]
comments: true
---

With the beginning of the iPhone 6s, Apple has introduced a 3D Touch mechanism which is very cool thing. The 3D Touch is also available on
the newest iPhones 7. Nothing indicates that in the future Apple devices will run out of that feature so, here is a quick tutorial on how
to improve your app using the one of the three main features of 3D Touch.


![](https://static.pexels.com/photos/50603/iphone-6-apple-ios-iphone-50603.jpeg)

# Modify your Info.plist file

1. Add to your  `Info.plist` file a special key called `UIApplicationShortcutItems`. It should be an array.

2. Add items to `UIApplicationShortcutItems` array. Items should be a dictionary type.

3. Put info for each item.

  There are few values to set here.

      - UIApplicationShortcutItemType (required)
      - UIApplicationShortcutItemTitle (required)
      - UIApplicationShortcutItemSubtitle
      - UIApplicationShortcutItemIconType
      - UIApplicationShortcutItemIconFile
      - UIApplicationShortcutItemUserInfo


  I will focus on the values that I set in my example app. The `UIApplicationShortcutItemType` is a string that delivers an information to your app about what type of shortcut was pressed.
  `UIApplicationShortcutItemTitle` is what user sees when the shortcut is shown. `UIApplicationShortcutItemIconType` is a string that inform application what kind of ***system***  icon should be used for this shortcut.
  And the last one is `UIApplicationShortcutItemIconFile` defining the icon name that should be shown when the shortcut appears(instead of system icon).

  > Important thing here is that you can use system ***or*** your custom icons. Apple recommends that the custom icon should be square, single color, and 35x35 points.

  More about `UIApplicationShortcutItems` keys and description you can get from [Apple documentation](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW1).

  The configured `Info.plist` could look like that:

  ![](https://raw.githubusercontent.com/k8mil/k8mil.github.io/master/assets/posts/3dTouch/info-plist.png)

  and by code:

```xml
<key>UIApplicationShortcutItems</key>
<array>
	<dict>
		<key>UIApplicationShortcutItemType</key>
		<string>app.some_another_action</string>
		<key>UIApplicationShortcutItemTitle</key>
		<string>Title with custom icon</string>
		<key>UIApplicationShortcutItemIconFile</key>
		<string>apple-35</string>
	</dict>
	<dict>
		<key>UIApplicationShortcutItemType</key>
		<string>app.some_action</string>
		<key>UIApplicationShortcutItemIconType</key>
		<string>UIApplicationShortcutIconTypeAdd</string>
		<key>UIApplicationShortcutItemTitle</key>
		<string>Title with system icon</string>
	</dict>
</array>
```

***Final result***

Below you can see the result of above implementation.

![](https://raw.githubusercontent.com/k8mil/k8mil.github.io/master/assets/posts/3dTouch/custom_system_icon.gif)

# Application shortcut tap handling

Ok, now move to `AppDelegate` class and let's deal with the application's shortcut tap events.

#### 1.Override and implement `UIApplicationDelegate` method.

Let's focus on this method:
```swift
func application(_ application: UIApplication, performActionFor shortcutItem: UIApplicationShortcutItem, completionHandler: @escaping (Bool) -> Swift.Void)
```

That method is called every time when the application shortcut is pressed. You don't have to worry about your application state. If your app is in background mode it just wakes up your app and triggers `performActionFor` method. But if the application is terminated the app cycle will be `didLauchWithOptions` and then `performActionFor shortcutItem` will be called.
Of course, you can take the application shortcut in `didLauchWithOptions` method by:

```swift
if let shortcutItem = launchOptions?[UIApplicationLaunchOptionsShortcutItemKey] as? UIApplicationShortcutItem {
  //deal with it here
}
```

before it triggers the `performActionFor` method.

#### 2.Handling the `UIApplicationShortcutItem`

It's time to write some code here. So let's assume that your `AppDelegate` class includes these two methods:

```swift
func application(_ application: UIApplication, performActionFor shortcutItem: UIApplicationShortcutItem, completionHandler: @escaping (Bool) -> Swift.Void) {
    handleShortcut(shortcutItem)
}

private func handleShortcut(_ item: UIApplicationShortcutItem) {

}
```

Did you remember the `UIApplicationShortcutItemType`? Value for this key is very useful to identify what application shortcut was tapped.
But on the `Info.plist` file it occurs as a `String` type and I strongly advise against comparing the string in their pure form.
Very helpful here might be `Enum` type. So create the enum.

```swift
enum ApplicationShortcutTypes: String {
    case redApple = "show-red-apple"
    case greenApple = "show-green-apple"
}
```

I think comparing types using enum is a much better way to identify what type of shortcut was tapped.

Now get back to the `handleShortcut` method:

```swift
private func handleShortcut(_ item: UIApplicationShortcutItem) {
    guard let actionType = ApplicationShortcutTypes(rawValue: item.type) else {
        return
    }
    switch (actionType) {
    case .greenApple:
      showGreenAppleViewController()
    case .redApple:
      showRedAppleViewController()
    }
}
```

The effect of implementation will look like this:

![](https://raw.githubusercontent.com/k8mil/k8mil.github.io/master/assets/posts/3dTouch/working_app.gif)

As you can see above I create two quick actions with custom icon, title and subtitle.

# Dynamic shortcut items

It is worth to mention about dynamic shortcut items. Yes, UIApplicationShortcutItems are split between `static` and `dynamic`.

- static - that items are placed in `info.plist`
- dynamic - that items can be added and removed in code

To add dynamic shortcut item you just have to add it to `shortcutItems` array.

```
UIApplication.sharedApplication().shortcutItems?.append(UIMutableApplicationShortcutItem(type: "my-dynamic-shortcut", localizedTitle: "Dynamic shortcut"))
```

to remove:

```swift
if let shortcutItem = UIApplication.sharedApplication().shortcutItems?.filter({ $0.type == "my-dynamic-shortcut" }).first {
    guard let index = UIApplication.sharedApplication().shortcutItems?.indexOf(shortcutItem) else {
      return
    }
    UIApplication.sharedApplication().shortcutItems?.removeAtIndex(index)
}
```

# Conclusion

The 3D Touch is powerful feature and makes iOS apps very nice to use. Interesting fact is that from the iOS 10 all apps available on AppStore have predefined `Share App` shortcut. It allows us to share the applications without opening them. In my future posts I will describe more features that 3d touch gives us. So, thanks for reading and see you next time!

![](https://raw.githubusercontent.com/k8mil/k8mil.github.io/master/assets/posts/3dTouch/share_app.gif)

*This post is cross-posted with my personal [blog](https://wysockikamil.com/3dtouch-adopting-shortcut-items-to-your-app/)*


*This article is available at [a new location](https://brightinventions.pl/blog/3dtouch-adopting-shortcut-items-to-your-app)*
