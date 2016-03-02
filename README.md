Access deltaDNA's analytics and SmartAds platforms from Unity.

## Installation

Our analytics SDK is written entirely in Unity with no native code requirements.  Out of the box it runs on any platform that Unity supports.  The easiest way to get started is to download the `deltadna-sdk-*.unitypackage` from this repository, and import into your Unity project.

## Quick Start

For all the information on how to use the analytics SDK, refer to our documentation [portal](http://docs.deltadna.com/advanced-integration/unity-sdk/).  *Beware*, the API changes for starting the SDK and using event builders since beta 4 are outdated on the platform docs.

Checkout the example in `Assets\DeltaDNA\Example` to see how to use the SDK.  At a minimum you will want to set the Client Version and start the SDK from a custom `MonoBehaviour`.

```csharp
DDNA.Instance.ClientVersion = "1.0.0";
DDNA.Instance.StartSDK("YOUR_ENVIRONMENT_KEY",
                       "YOUR_COLLECT_URL",
                       "YOUR_ENGAGE_URL");
```

On the first run this will create new user id and send a `newPlayer` event. On every call it will send a `gameStarted` and `clientDevice` event.

#### Custom Events

You can easily record custom events by using the `GameEvent` class.  Create a `GameEvent` with the name of your event schema.  Call `AddParam` to add custom event parameters to the event.  For example:

```csharp
GameEvent gameEvent = new GameEvent("myEvent")
    .AddParam("option", "sword")
    .AddParam("action", "sell");

DDNA.Instance.RecordEvent(gameEvent);
```

## SmartAds

Integrating SmartAds into your Unity project requires native code extensions which we supply separately.  More information on how to access our SmartAds platform is [here](http://docs.deltadna.com/advanced-integration/smart-ads/).  To add the Unity extensions download and import the `deltadna-smartads-*.unitypackage`.  We support iOS and Android platforms.

### Usage

The quickest way to learn how to use SmartAds is to checkout out the example scene in `Assets\DeltaDNAAds\Example`.  The `AdsDemo` class shows how to use both interstitial and rewarded ads.  Support for SmartAds is enabled by calling `RegisterForAds`.  This *must* be called after starting the analytics SDK.  The `DDNASmartAds` class defines a number of events which you can register callbacks with to be notified when an ad has opened or closed.

Start the analytics SDK.

```csharp
DDNA.Instance.ClientVersion = "1.0.0";
DDNA.Instance.StartSDK("YOUR_ENVIRONMENT_KEY",
                       "YOUR_COLLECT_URL",
                       "YOUR_ENGAGE_URL");
```

Register for ads.

```csharp
DDNASmartAds.Instance.RegisterForAds();
```

You can test if an interstitial ad is ready to be displayed with `DDNASmartAds.Instance.IsInterstitialAdAvailable()`.

Show an interstitial ad by calling `DDNASmartAds.Instance.ShowInterstitialAd()`.

You can test if a rewarded ad is ready to be displayed with `DDNASmartAds.Instance.IsRewardedAdAvailable()`.

Show a rewarded ad by calling `DDNASmartAds.Instance.ShowRewardedAd()`.

#### Events

Callbacks can be added to the following events to be notified when an ad has opened or closed.

* `OnDidRegisterForInterstitialAds` - Called when you have successfully enabled interstitial ads for your game.
* `OnDidFailToRegisterForInterstitialAds` - Called if interstitial ads are unavailable for some reason.  A string parameter reports a possible error.
* `OnInterstitialAdOpened` - Called when an interstitial ad is shown on screen.
* `OnInterstitialAdFailedToOpen` - Called if an interstitial ad fails to show.
* `OnInterstitialAdClosed` - Called when the user has closed an interstitial ad.
* `OnDidRegisterForRewardedAds` - Called when you have successfully enabled rewarded ads for your game.
* `OnDidFailToRegisterForRewardedAds` - Called if rewarded ads are unavailable for some reason.  A string parameter reports a possible error.
* `OnRewardedAdOpened` - Called when a rewarded ad is shown on screen.
* `OnRewardedAdFailedToOpen` - Called if a rewarded ad fails to show.
* `OnRewardedAdClosed` - Called when the user had closed a rewarded ad.  A boolean parameter indicates if the user had watched enough of the ad to be rewarded.

#### Ad Points

You can add control of which kinds players see ads by using *Ad Points*.  Show an ad with `ShowInterstitialAd("pointInGameToShowAnAd")` or `ShowRewardedAd("anotherPointInGameToShowAnAd")`, and register the Ad Point in Portal.  The SDK will ask if the segment this player is in should be shown the ad or not.  It's worth using Ad Points when you first integrate, if the Ad Point is not registered, it will be ignored and the ad shown.

### iOS Integration

We use [CocoaPods](https://cocoapods.org/) to handle the 3rd party ad network dependencies.  A post process build hook adds a template Podfile and command script to the XCode project generated by Unity.  You should customise this Podfile to download the dependencies you require, by default it will pull down all the ad networks deltaDNA supports.  The process is the same as for the native [iOS SmartAds SDK](https://github.com/deltaDNA/ios-smartads-sdk) and more details on customising the Podfile can be found there.

Once Unity has generated the XCode project, run the `pods.command` file, which will install the pods and open the XCode project for you.  Feel free to change this script to suit your workflow.

### Android Integration

We provide a Python script to help manage the 3rd party ad network dependencies.  In `Assets\DeltaDNAAds\Editor\Android`, edit `config.json` to include the networks you wish to integrate.  Then from the command line run `download.py`.  This will download and copy the dependent AARs and Jar files into the `Assets\DeltaDNAAds\Plugins\Android` folder.  Unity will pick these up when you build the APK.

The SDK already pre-packages some dependencies for Google Play Services under `Assets\DeltaDNA\Plugins\Android` for push notifications (as well as SmartAds). If you would like to use your own version of Play Services, then you should remove the dependencies (ie play-services-base-7.8.0.aar, play-services-gcm-7.8.0.aar, etc) in order to avoid duplicate class definition errors during the build stage. Please note that we cannot guarantee other versions of Google Play Services than 7.8.0 to work correctly with our SDK.

*Note* if the download script will not run properly, please contact deltaDNA to get a hold of the dependencies.

## License

The analytics sources under Assets/DeltaDNA are available under the Apache 2.0 license.  The SmartAds sources under Assets/DeltaDNAAds are available under deltaDNA's commercial license.
