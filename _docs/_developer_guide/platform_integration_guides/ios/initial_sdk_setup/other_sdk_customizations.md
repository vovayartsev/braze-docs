---
nav_title: Other SDK Customizations
platform: iOS
description: "This document covers Log Level, IDFA Collection, and other customizations"
page_order: 3

---

# Other SDK Customizations

## Braze Log Level

The default LogLevel for the Braze iOS SDK is `8`. This level suppresses most logging so that no sensitive information is logged in a production released application.

To enable verbose logging for debugging, add a dictionary named `Appboy` to your `Info.plist` file. Inside the `Appboy` Dictionary, add the `LogLevel` String subentry and set the value to `0`.

LogLevel `0` is only intended to be used in development environments and should not be set in a released application.

Example `Info.plist` contents:

```
<key>Appboy</key>
<dict>
	<key>LogLevel</key>
	<string>0</string>
</dict>
```

### Description of Log Levels

| LogLevel | Description |
|----------|-------------|
| 0        | All log information will be logged to the iOS console  |
| 8        | Default, minimal logging.                              |

## Optional IDFA Collection

IDFA Collection is optional within the Braze SDK and disabled by default. IDFA Collection is required if you intend to utilize our [install attribution integrations][21]. However, we may develop additional features in the future which would benefit from the collection of your IDFA. If you opt to store your IDFA, we will store it free of charge so you may take advantage of these options immediately upon release without additional development work.

As a result, we recommend continuing to collect the IDFA if you meet any of the following criteria:

- You are using advertising elsewhere in the app
- You are attributing app installation to a previously served advertisement
- You are attributing an action within the application to a previously served advertisement

## iOS 14 AppTrackingTransparency
Beginning with iOS 14 access to the advertising identifier is now on a per app basis. In addition to this delegate, your application will need to prompt for this authorization. For more information you can see this [Braze iOS 14 guide](https://www.braze.com/docs/developer_guide/platform_integration_guides/ios/ios_14/#idfa-and-app-tracking-transparency), [Apple's Overview](https://developer.apple.com/app-store/user-privacy-and-data-use/), and [Apple's Developer Documentation](https://developer.apple.com/documentation/apptrackingtransparency)

## Implementing IDFA Collection

Follow these steps to implement IDFA Collection:

1. Create a class that conforms to the [`ABKIDFADelegate`][29] protocol. For a contextual example, see [`IDFADelegate`][30].

{% tabs %}
{% tab OBJECTIVE-C %}

```objc
#import "IDFADelegate.h"
#import <AdSupport/ASIdentifierManager.h>
#import <AppTrackingTransparency/AppTrackingTransparency.h>

@implementation IDFADelegate

- (NSString *)advertisingIdentifierString {
  return [[[ASIdentifierManager sharedManager] advertisingIdentifier] UUIDString];
}

- (BOOL)isAdvertisingTrackingEnabledOrATTAuthorized {
  if (@available(iOS 14, *)) {
    return [ATTrackingManager trackingAuthorizationStatus] == ATTrackingManagerAuthorizationStatusAuthorized;
  }
  return [[ASIdentifierManager sharedManager] isAdvertisingTrackingEnabled];
}


@end
```

{% endtab %}
{% tab swift %}

```swift
import Appboy_iOS_SDK
import AdSupport
import AppTrackingTransparency

class IDFADelegate: NSObject, ABKIDFADelegate {

   func advertisingIdentifierString() -> String {
    return ASIdentifierManager.shared().advertisingIdentifier.uuidString
  }

  func isAdvertisingTrackingEnabledOrATTAuthorized() -> Bool {
    if #available(iOS 14, *) {
      return ATTrackingManager.trackingAuthorizationStatus ==  ATTrackingManager.AuthorizationStatus.authorized
    }
    return ASIdentifierManager.shared().isAdvertisingTrackingEnabled
  }

}

```
{% endtab %}
{% endtabs %}

2. Set the delegate during Braze initialization. In the `appboyOptions` dictionary passed to `startWithApiKey:inApplication:withAppboyOptions:`, set the `ABKIDFADelegateKey` key to an instance of your `ABKIDFADelegate` conforming class.

### Approximate iOS SDK Size {#ios-sdk-size}

The approximate iOS SDK framework file size is 30MB and the approximate .ipa (addition to app file) size is between 1MB and 2MB.

Braze measures the size of our iOS SDK by observing the SDK's effect on `.ipa` size, per [Apple's recommendations on app sizing][31]. If you are calculating the iOS SDK's size addition to your application, we recommend following the steps under ["Getting an App Size Report"][31] to compare the size difference in your `.ipa` before and after integrating the Braze iOS SDK. When comparing sizes from the App Thinning Size Report, we also recommend looking at app sizes for thinned `.ipa` files, as universal `.ipa` files will be larger than the binaries downloaded from the App Store and installed onto user devices.

> If you are integrating via CocoaPods with `use_frameworks!`, set `Enable Bitcode = NO` in target's Build Settings for accurate sizing.


[1]: http://cocoapods.org/
[2]: https://www.ruby-lang.org/en/installation/
[3]: http://guides.cocoapods.org/using/getting-started.html "CocoaPods Installation Directions"
[4]: http://guides.cocoapods.org/syntax/podfile.html
[5]: https://github.com/Appboy/appboy-ios-sdk/blob/master/AppboyKit/headers/AppboyKitLibrary/Appboy.h#L32
[6]: https://dashboard-01.braze.com/app_settings/app_settings/ "App Settings"
[7]: https://github.com/Appboy/appboy-ios-sdk/blob/master/Example/Stopwatch/AppDelegate.m
[8]: {{site.baseurl}}/developer_guide/platform_integration_guides/unity/ios/sdk_integration/#manual-sdk-integration
[12]: #appboy-podfiles-for-non-64-bit-apps
[13]: https://github.com/Appboy/appboy-ios-sdk/blob/master/HelloSwift/Podfile
[14]: https://github.com/Appboy/appboy-ios-sdk/blob/master/Example/Podfile "Example Podfile"
[15]: {% image_buster /assets/img_archive/podsworkspace.png %}
[17]: http://guides.cocoapods.org/using/getting-started.html#updating-cocoapods
[19]: https://developer.apple.com/library/ios/documentation/swift/conceptual/buildingcocoaapps/MixandMatch.html
[21]: {{site.baseurl}}/partners/advertising_technologies/attribution/adjust/
[25]: http://guides.cocoapods.org/using/troubleshooting.html "CocoaPods Troubleshooting Guide"
[27]: https://github.com/Appboy/appboy-ios-sdk/blob/master/CHANGELOG.md "iOS Changelog"
[28]: #apple-watch-sdk
[29]: https://github.com/Appboy/appboy-ios-sdk/blob/master/AppboyKit/headers/AppboyKitLibrary/ABKIDFADelegate.h
[30]: https://github.com/Appboy/appboy-ios-sdk/blob/master/Example/Stopwatch/Sources/Utils/IDFADelegate.m
[31]: https://developer.apple.com/library/content/qa/qa1795/_index.html
