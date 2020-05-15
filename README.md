# Salesforce Marketing Cloud React Native 

Use this module to implement the Marketing Cloud MobilePush SDK for your [iOS](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-iOS/) and [Android](http://salesforce-marketingcloud.github.io/MarketingCloudSDK-Android/) applications.

## Release Notes

Release notes for the plugin can be found [here](CHANGELOG.md)

## Installation

#### 1. Add plugin to your application via [npm](https://www.npmjs.com/package/react-native-marketingcloudsdk)

```shell
npm install react-native-marketingcloudsdk --save
```

### Android Setup

#### 1. Add Marketing Cloud SDK repository & update minSdkVersion

`android/build.gradle`
```groovy
ext {
        // ...
        minSdkVersion = 21
// ...

allprojects {
    repositories {
        maven { url "https://salesforce-marketingcloud.github.io/MarketingCloudSDK-Android/repository" }
        //... Other repos
    }
}
```

#### 2. Provide FCM credentials

1. To enable push support for the Android platform you will need to include the google-services.json file.  Download the file from your Firebase console and place it into the `android/app` directory

2. Include the Google Services plugin in your build
`android/build.gradle`
```groovy
buildscript {
  repositories {
    google()  // Google's Maven repository
  }

  dependencies {
    // ...
    // Add the following line:
    classpath 'com.google.gms:google-services:4.2.0'
  }
}
```
3. Implement the SDK & apply the plugin
`android/app/build.gradle`
```groovy
dependencies {
    // ...
    implementation 'com.salesforce.marketingcloud:marketingcloudsdk:7.1.0'
    // ...

// Add the following line to the bottom of the file:
apply plugin: 'com.google.gms.google-services'
```

#### 3. Configure the SDK in your MainApplication.java class

```java
import android.util.Log;

import com.salesforce.marketingcloud.MarketingCloudConfig;
import com.salesforce.marketingcloud.MarketingCloudSdk;
import com.salesforce.marketingcloud.notifications.NotificationCustomizationOptions;

// ...

@Override
public void onCreate() {
    super.onCreate();

    MarketingCloudSdk.init(this,
            MarketingCloudConfig.builder()
                    .setApplicationId("{MC_APP_ID}")
                    .setAccessToken("{MC_ACCESS_TOKEN}")
                    .setSenderId("{FCM_SENDER_ID_FOR_MC_APP}")
                    .setMarketingCloudServerUrl("{MC_APP_SERVER_URL}")
                    .setNotificationCustomizationOptions(NotificationCustomizationOptions.create(R.drawable.ic_notification))
                    .setAnalyticsEnabled(true)
                    .build(this),
            initializationStatus -> Log.e("INIT", initializationStatus.toString()));

    // ... The rest of the onCreate method    
}
```

### iOS Setup

#### 1. Install pod for Marketing Cloud SDK

You will have to go into xcode, under the General tab of your project, make sure that you have your target set to iOS 10.0. Then, you'll want to go into `ios/Podfile` and make sure and update the platform to `platform: ios, '10.0'`. 

Once that is done, run `pod install`. *Note* - if you previously installed pods and run into issues you may have to run:
```
pod deintegrate
cd ..
rm -rf node_modules
npm install
cd ios
pod install
```

#### 2. Configure the SDK in your AppDelegate.m class

```objc
#import "MarketingCloudSDK/MarketingCloudSDK.h"

// ...

- (BOOL)application:(UIApplication *)application
    didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    MarketingCloudSDKConfigBuilder *mcsdkBuilder = [MarketingCloudSDKConfigBuilder new];
    [mcsdkBuilder sfmc_setApplicationId:@"{MC_APP_ID}"];
    [mcsdkBuilder sfmc_setAccessToken:@"{MC_ACCESS_TOKEN}"];
    [mcsdkBuilder sfmc_setAnalyticsEnabled:@(YES)];
    [mcsdkBuilder sfmc_setMarketingCloudServerUrl:@"{MC_APP_SERVER_URL}"];

    NSError *error = nil;
    BOOL success =
        [[MarketingCloudSDK sharedInstance] sfmc_configureWithDictionary:[mcsdkBuilder sfmc_build]
                                                                   error:&error];

    // ... The rest of the didFinishLaunchingWithOptions method  
}
```

#### 3. Enable Push

Follow [these instructions](./ios_push.md) to enable push for iOS.

## API Reference <a name="reference"></a>

* [isPushEnabled()](#isPushEnabled) ⇒ <code>Promise.&lt;boolean&gt;</code>
* [enablePush()](#enablePush)
* [disablePush()](#disablePush)
* [getSystemToken()](#getSystemToken) ⇒ <code>Promise.&lt;?string&gt;</code>
* [getAttributes()](#getAttributes) ⇒ <code>Promise.&lt;Object.&lt;string, string&gt;&gt;</code>
* [setAttribute(key, value)](#setAttribute)
* [clearAttribute(key)](#clearAttribute)
* [addTag(tag)](#addTag)
* [removeTag(tag)](#removeTag)
* [getTags()](#getTags) ⇒ <code>Promise.&lt;Array.&lt;string&gt;&gt;</code>
* [setContactKey(contactKey)](#setContactKey)
* [getContactKey()](#getContactKey) ⇒ <code>Promise.&lt;?string&gt;</code>
* [enableVerboseLogging()](#enableVerboseLogging)
* [disableVerboseLogging()](#disableVerboseLogging)
* [logSdkState()](#logSdkState)

<a name="isPushEnabled"></a>

### isPushEnabled() ⇒ <code>Promise.&lt;boolean&gt;</code>
The current state of the pushEnabled flag in the native Marketing Cloud
SDK.

**Kind**: function
**Returns**: <code>Promise.&lt;boolean&gt;</code> - A promise to the boolean representation of whether push is
    enabled.  
**See**

- [Android Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-Android/javadocs/6.0/reference/com/salesforce/marketingcloud/messages/push/PushMessageManager.html#isPushEnabled())
- [iOS Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-iOS/appledoc/Classes/MarketingCloudSDK.html#//api/name/sfmc_pushEnabled)

<a name="enablePush"></a>

### enablePush()
Enables push messaging in the native Marketing Cloud SDK.

**Kind**: function
**See**

- [Android Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-Android/javadocs/6.0/reference/com/salesforce/marketingcloud/messages/push/PushMessageManager.html#enablePush())
- [iOS Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-iOS/appledoc/Classes/MarketingCloudSDK.html#//api/name/sfmc_setPushEnabled:)

<a name="disablePush"></a>

### disablePush()
Disables push messaging in the native Marketing Cloud SDK.

**Kind**: function
**See**

- [Android Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-Android/javadocs/6.0/reference/com/salesforce/marketingcloud/messages/push/PushMessageManager.html#disablePush())
- [iOS Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-iOS/appledoc/Classes/MarketingCloudSDK.html#//api/name/sfmc_setPushEnabled:)

<a name="getSystemToken"></a>

### getSystemToken() ⇒ <code>Promise.&lt;?string&gt;</code>
Returns the token used by the Marketing Cloud to send push messages to
the device.

**Kind**: function
**Returns**: <code>Promise.&lt;?string&gt;</code> - A promise to the system token string.  
**See**

- [Android Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-Android/javadocs/6.0/reference/com/salesforce/marketingcloud/messages/push/PushMessageManager.html#getPushToken())
- [iOS Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-iOS/appledoc/Classes/MarketingCloudSDK.html#//api/name/sfmc_deviceToken)

<a name="getAttributes"></a>

### getAttributes() ⇒ <code>Promise.&lt;Object.&lt;string, string&gt;&gt;</code>
Returns the maps of attributes set in the registration.

**Kind**: function  
**Returns**: <code>Promise.&lt;Object.&lt;string, string&gt;&gt;</code> - A promise to the key/value map of attributes set
    in the registration.  
**See**

- [Android Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-Android/javadocs/6.0/reference/com/salesforce/marketingcloud/registration/RegistrationManager.html#getAttributes())
- [iOS Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-iOS/appledoc/Classes/MarketingCloudSDK.html#//api/name/sfmc_attributes)

<a name="setAttribute"></a>

### setAttribute(key, value)
Sets the value of an attribute in the registration.

**Kind**: function 
**See**

- [Android Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-Android/javadocs/6.0/reference/com/salesforce/marketingcloud/registration/RegistrationManager.Editor.html#setAttribute(java.lang.String,%20java.lang.String))
- [iOS Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-iOS/appledoc/Classes/MarketingCloudSDK.html#//api/name/sfmc_setAttributeNamed:value:)


| Param | Type | Description |
| --- | --- | --- |
| key | <code>string</code> | The name of the attribute to be set in the     registration. |
| value | <code>string</code> | The value of the `key` attribute to be set in     the registration. |

<a name="clearAttribute"></a>

### clearAttribute(key)
Clears the value of an attribute in the registration.

**Kind**: function
**See**

- [Android Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-Android/javadocs/6.0/reference/com/salesforce/marketingcloud/registration/RegistrationManager.Editor.html#clearAttribute(java.lang.String))
- [iOS Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-iOS/appledoc/Classes/MarketingCloudSDK.html#//api/name/sfmc_clearAttributeNamed:)


| Param | Type | Description |
| --- | --- | --- |
| key | <code>string</code> | The name of the attribute whose value should be     cleared from the registration. |

<a name="addTag"></a>

### addTag(tag)
**Kind**: function
**See**

- [Android Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-Android/javadocs/6.0/reference/com/salesforce/marketingcloud/registration/RegistrationManager.Editor.html#addTag(java.lang.String))
- [iOS Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-iOS/appledoc/Classes/MarketingCloudSDK.html#//api/name/sfmc_addTag:)


| Param | Type | Description |
| --- | --- | --- |
| tag | <code>string</code> | The tag to be added to the list of tags in the     registration. |

<a name="removeTag"></a>

### removeTag(tag)
**Kind**: function
**See**

- [Android Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-Android/javadocs/6.0/reference/com/salesforce/marketingcloud/registration/RegistrationManager.Editor.html#removeTag(java.lang.String))
- [iOS Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-iOS/appledoc/Classes/MarketingCloudSDK.html#//api/name/sfmc_removeTag:)


| Param | Type | Description |
| --- | --- | --- |
| tag | <code>string</code> | The tag to be removed from the list of tags in the     registration. |

<a name="getTags"></a>

### getTags() ⇒ <code>Promise.&lt;Array.&lt;string&gt;&gt;</code>
Returns the tags currently set on the device.

**Kind**: function 
**Returns**: <code>Promise.&lt;Array.&lt;string&gt;&gt;</code> - A promise to the array of tags currently set in the native SDK.  
**See**

- [Android Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-Android/javadocs/6.0/reference/com/salesforce/marketingcloud/registration/RegistrationManager.html#getTags())
- [iOS Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-iOS/appledoc/Classes/MarketingCloudSDK.html#//api/name/sfmc_tags)

<a name="setContactKey"></a>

### setContactKey(contactKey)
Sets the contact key for the device's user.

**Kind**: function
**See**

- [Android Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-Android/javadocs/6.0/reference/com/salesforce/marketingcloud/registration/RegistrationManager.Editor.html#setContactKey(java.lang.String))
- [iOS Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-iOS/appledoc/Classes/MarketingCloudSDK.html#//api/name/sfmc_setContactKey:)


| Param | Type | Description |
| --- | --- | --- |
| contactKey | <code>string</code> | The value to be set as the contact key of     the device's user. |

<a name="getContactKey"></a>

### getContactKey() ⇒ <code>Promise.&lt;?string&gt;</code>
Returns the contact key currently set on the device.

**Kind**: function
**Returns**: <code>Promise.&lt;?string&gt;</code> - A promise to the current contact key.  
**See**

- [Android Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-Android/javadocs/6.0/reference/com/salesforce/marketingcloud/registration/RegistrationManager.html#getContactKey())
- [iOS Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-iOS/appledoc/Classes/MarketingCloudSDK.html#//api/name/sfmc_contactKey)

<a name="enableVerboseLogging"></a>

### enableVerboseLogging()
Enables verbose logging within the native Marketing Cloud SDK.

**Kind**: function
**See**

- [Android Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-Android/javadocs/6.0/reference/com/salesforce/marketingcloud/MarketingCloudSdk.html#setLogLevel(int))
- [iOS Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-iOS/appledoc/Classes/MarketingCloudSDK.html#//api/name/sfmc_setDebugLoggingEnabled:)

<a name="disableVerboseLogging"></a>

### disableVerboseLogging()
Disables verbose logging within the native Marketing Cloud SDK.

**Kind**: function
**See**

- [Android Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-Android/javadocs/6.0/reference/com/salesforce/marketingcloud/MarketingCloudSdk.html#setLogLevel(int))
- [iOS Docs](https://salesforce-marketingcloud.github.io/MarketingCloudSDK-iOS/appledoc/Classes/MarketingCloudSDK.html#//api/name/sfmc_setDebugLoggingEnabled:)

<a name="logSdkState"></a>

### logSdkState()
Instructs the native SDK to log the SDK state to the native logging system (Logcat for
Android and Xcode/Console.app for iOS).  This content can help diagnose most issues within
the SDK and will be requested by the Marketing Cloud support team.

**Kind**: function
