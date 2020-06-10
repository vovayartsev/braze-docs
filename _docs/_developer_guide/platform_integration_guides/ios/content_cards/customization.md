---
nav_title: Customization
platform: iOS
page_order: 1

---

# Content Cards View Controller Integration

Content Cards can be integrated with two view controller contexts: Navigation or Modal.

## Navigation Context

Example of pushing a `ABKContentCardsTableViewController` instance into a navigation controller:

{% tabs %}
{% tab OBJECTIVE-C %}

```objc
ABKContentCardsTableViewController *contentCards = [ABKContentCardsTableViewController getNavigationFeedViewController];
contentCards.title = "Content Cards Title";
contentCards.disableUnreadIndicator = YES;
[self.navigationController pushViewController:contentCards animated:YES];
```

{% endtab %}
{% tab swift %}

```swift
let contentCards = ABKContentCardsTableViewController()
contentCards.title = "Content Cards Title"
contentCards.disableUnreadIndicator = true
navigationController?.pushViewController(contentCards, animated: true)
```

{% endtab %}
{% endtabs %}

{% alert note %}
To customize the navigation bar's title, set the title property of the `ABKContentCardsTableViewController` instance's `navigationItem`.
{% endalert %}

## Modal Context

This modal is used to present the view controller in a modal view, with a navigation bar on top and a Done button on the right side of the bar.

{% tabs %}
{% tab OBJECTIVE-C %}

```objc
ABKContentCardsViewController *contentCards = [[ABKContentCardsViewController alloc] init];
contentCards.contentCardsViewController.title = "Content Cards Title";
contentCards.contentCardsViewController.disableUnreadIndicator = YES;
[self.navigationController presentViewController:contentCards animated:YES completion:nil];
```

{% endtab %}
{% tab swift %}

```swift
let contentCards = ABKContentCardsViewController()
contentCards.contentCardsViewController.title = "Content Cards Title"
contentCards.contentCardsViewController.disableUnreadIndicator = true
self.present(contentCards, animated: true, completion: nil)
```

{% endtab %}
{% endtabs %}

For examples of these view controllers, check out our [Content Cards sample app](https://github.com/Appboy/appboy-ios-sdk/tree/master/Samples/ContentCards/BrazeContentCardsSampleApp).

{% alert note %}
To customize the header, set the title property of the `navigationItem` belonging to the `ABKContentCardsTableViewController` instance embedded in the parent `ABKContentCardsViewController` instance.
{% endalert %}

## Overriding Default Images

{% alert important %}
__Note that integration of `SDWebImage` is required if you plan on using our Braze UI for displaying images__ within iOS In-App Messages, News Feed, or Content Cards.
{% endalert %}

Braze allows clients to replace existing default images with their own custom images. To accomplish this, create a new `png` file with the custom image and add it to the app’s image bundle. Then, rename the file with the image’s name (see below) to override the default image in our library. Images available for override in Content Cards include:

- Placeholder image: `appboy_cc_noimage_lrg`.
- Pinned icon image: `appboy_cc_icon_pinned`.

Because Content Cards have a maximum size of **2kb** (including images, links, and all content) make sure to check the size before sending. Exceeding this amount will prevent the card from sending.

{% alert note %}
Be sure to upload the `@2x` and `@3x` versions of the images as well to accommodate different phone sizes.
{% endalert %}

{% alert note %}
Note that overriding default images is currently not supported in our Xamarin iOS integration.
{% endalert %}

## Customizing the Content Cards Feed

You can create your own Content Cards interface by extending `ABKContentCardsTableViewController` to customize all UI elements and Content Cards behavior. Alternatively, you can create a completely custom view controller and subscribe for data updates. In the latter case, you would need to log all view events, dismissed events, and clicks manually.


## Dark Mode and Custom Themes

A few options are available to you to customize the behavior of Content Cards. First if you would like to disable automatically adopting the OS setting for dark mode you can set enableDarkTheme to NO, see this example.


{% tabs %}
{% tab OBJECTIVE-C %}

```objc
ABKContentCardsViewController *contentCards = [[ABKContentCardsViewController alloc] init];
contentCards.contentCardsViewController.enableDarkTheme = NO;
contentCards.contentCardsViewController.title = "Content Cards Title"
contentCards.contentCardsViewController.disableUnreadIndicator = YES;
[self.navigationController pushViewController:contentCards animated:YES];
```

{% endtab %}
{% tab swift %}

```swift
let contentCards = ABKContentCardsViewController()
  contentCards.contentCardsViewController.enableDarkTheme = false
contentCards.contentCardsViewController.title = "Content Cards Title"
contentCards.contentCardsViewController.disableUnreadIndicator = true
self.present(contentCards, animated: true, completion: nil)
```

{% endtab %}
{% endtabs %}

Second, if you already have theme that expects darker colors, you can force dark or light mode using `overrideUserInterfaceStyle` on the viewController. In this example replace `StopwatchInAppThemeSettingsKey` with the logic for your theme.

{% tabs %}
{% tab OBJECTIVE-C %}

```objc
ABKContentCardsViewController *contentCards = [[ABKContentCardsViewController alloc] init];
contentCards.contentCardsViewController.title = "Content Cards Title"
contentCards.contentCardsViewController.disableUnreadIndicator = YES;
if (@available(iOS 13.0, *)) {
	if ([[NSUserDefaults standardUserDefaults] integerForKey:StopwatchInAppThemeSettingsKey] == StopwatchInAppThemeDark) {
		// Force dark mode regardless of user settings in OS.
		contentCards.contentCardsViewController.viewController.overrideUserInterfaceStyle = UIUserInterfaceStyleDark;
	} else if ([[NSUserDefaults standardUserDefaults] integerForKey:StopwatchInAppThemeSettingsKey] == StopwatchInAppThemeLight) {
		// Force light mode regardless of user settings in OS.
		contentCards.contentCardsViewController.viewController.overrideUserInterfaceStyle = UIUserInterfaceStyleLight;
	} else {
		// Default theme behavior will leave the OS settings alone.
		// This value will respect the system UI style of dark or light mode
		contentCards.contentCardsViewController.viewController.overrideUserInterfaceStyle = UIUserInterfaceStyleUnspecified;
	}
}

[self.navigationController pushViewController:contentCards animated:YES];
```

{% endtab %}
{% tab swift %}

```swift
let contentCards = ABKContentCardsViewController()
contentCards.contentCardsViewController.title = "Content Cards Title"
contentCards.contentCardsViewController.disableUnreadIndicator = true
iif #available(iOS 13.0, *) {
	if (UserDefaults.standard.integer(forKey:StopwatchInAppThemeSettingsKey) == StopwatchInAppThemeDark) {
		// Force dark mode regardless of user settings in OS.
		contentCards.contentCardsViewController.viewController.overrideUserInterfaceStyle = .dark
	} else if (UserDefaults.standard.integer(forKey:StopwatchInAppThemeSettingsKey) == StopwatchInAppThemeLight) {
		// Force light mode regardless of user settings in OS.
		contentCards.contentCardsViewController.viewController.overrideUserInterfaceStyle = .light
	} else {
		// Default theme behavior will leave the OS settings alone.
		// This value will respect the system UI style of dark or light mode
		contentCards.contentCardsViewController.viewController.overrideUserInterfaceStyle = .unspecified
	}
}

self.present(contentCards, animated: true, completion: nil)
```

{% endtab %}
{% endtabs %}


