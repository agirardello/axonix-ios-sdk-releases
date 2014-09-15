# Axonix SDK 7.0 Release

Please feel free to raise issues [on Github](https://github.com/AxonixRTB/axonix-ios-sdk-releases/issues)

__Table of contents__

<!-- MarkdownTOC depth=3 autolink=true bracket=round -->

- [Introduction](#introduction)
- [Installation](#installation)
  - [Install Using CocoaPods (RECOMMENDED)](#install-using-cocoapods-recommended)
  - [Install Manually (Not recommended)](#install-manually-not-recommended)
    - [Add the Axonix SDK files to your project (only for Manual installation)](#add-the-axonix-sdk-files-to-your-project-only-for-manual-installation)
    - [Link Framework Dependencies (only for Manual installation)](#link-framework-dependencies-only-for-manual-installation)
- [Integrate Axonix Ads with Your Code](#integrate-axonix-ads-with-your-code)
  - [Setup your App Delegate](#setup-your-app-delegate)
  - [Adding a Banner Ad in your Interface](#adding-a-banner-ad-in-your-interface)
    - [Add a view to your interface](#add-a-view-to-your-interface)
    - [Create and link an Outlet](#create-and-link-an-outlet)
    - [Register a delegate](#register-a-delegate)
    - [Request and Manage Ads](#request-and-manage-ads)
    - [Clean up your ad view](#clean-up-your-ad-view)
  - [Adding a Fullscreen Ad to your Interface](#adding-a-fullscreen-ad-to-your-interface)
    - [Create a full screen delegate and full screen view variable](#create-a-full-screen-delegate-and-full-screen-view-variable)
    - [Create an AxonixFullScreenAdViewController instance](#create-an-axonixfullscreenadviewcontroller-instance)
    - [Implement the delegate methods to handle ad flow](#implement-the-delegate-methods-to-handle-ad-flow)
    - [Request an ad (without pre-fetching)](#request-an-ad-without-pre-fetching)
    - [Request an ad with pre-fetching and display it later](#request-an-ad-with-pre-fetching-and-display-it-later)
- [Using the Axonix Demo App](#using-the-axonix-demo-app)

<!-- /MarkdownTOC -->

----

# Introduction

This is the Axonix SDK. You can include this in your applications to display ads.

Requirements:

* This SDK requires iOS SDK 6.1 or greater
* You will need an account in [axonix.com](http://axonix.com)
 * You will need to register an application there and register the ad sizes that you want to display
 * This will provide you with an `application-id` that you will input below

----

# Installation

There are two ways to install the Axonix SDK:

* Using [CocoaPods][1] (RECOMMENDED)
* Downloading and then doing it manually.

We __highly suggest__ using CocoaPods because CocoaPods will take care of adding all of the necessary dependencies, and will provide an easy path for updates.

## Install Using CocoaPods (RECOMMENDED)

If you aren't already using CocoaPods but would like to, check out the [CocoaPods Getting Started Guide](http://guides.cocoapods.org/using/getting-started.html)

Add **AxonixSDK** to your Podfile. 

```ruby
pod "AxonixSDK", "~> 7.0"
```

We recommend using the `~> 7.0` version specifier so you will automatically get
small bug fix updates that don't break the API.

Run `pod install` (and later, `pod update` to update to future new versions).

That's it!

----


## Install Manually (Not recommended)

### Add the Axonix SDK files to your project (only for Manual installation)

- Command-click the project in the Project Navigator and select Add Files to "&lt;Your Project Name&gt;"

![Add file to project](http://cl.ly/image/0S443z19341m/Resized%20Screen%202014-07-21%20at%2010.24.47%20am.png)

- Add the AxonixSDK directory with the following settings

![Adding the AxonixSDK with the correct settings](http://cl.ly/image/1B3p1j3b3g3i/Resized%20Screen%202014-07-21%20at%2010.23.55%20am.png)

After clicking the "Add" button the static framework **libAxonixSDK.a** and the headers
should be added to your project

![Updated Project Navigator with Axonix SDK](http://cl.ly/image/3N2b393i2I0b/Resized%20Screen%202014-07-21%20at%2010.28.00%20am.png)

Make sure **libAxonixSDK.a** is added to your application target

![Making sure the framework is added to the target](http://cl.ly/image/0o3y0U1K0G1d/Napkin%204%2021-07-14%2010.51.59%20am.png)

### Link Framework Dependencies (only for Manual installation)

The AxonixSDK depends on certain System Frameworks, so before you can start using
the Axonix classes you will need to add them as dependencies to your app. First,
go to the Build Phases tab of your application target settings

![Edit the Linked Binaries in the Build Phase of your app target](http://cl.ly/image/0N2E1B3Z2614/Resized%20Screen%202014-07-21%20at%2010.29.41%20am.png)


Click the "+" button under the framework list in the "Link Binary with Libraries" section


![Click the plus button to add a new framework to the list of Linked Binaries](http://cl.ly/image/3H3t03090C3k/Resized%20Screen%202014-07-21%20at%2010.29.45%20am.png)


Search for the AddressBook.framework, select and then add it


![Search for the AddressBook.framework, select and Add it](http://cl.ly/image/2a2m2q1x3v3l/Resized%20Screen%202014-07-21%20at%2010.30.01%20am.png)


Do the same thing for the rest of the framework dependencies

  - AudioToolbox.framework
  - AVFoundation.framework
  - CoreMotion.framework
  - EventKit.framework
  - MediaPlayer.framework
  - MessageUI.framework
  - QuartzCore.framework
  - SystemConfiguration.framwork
  - AdSupport.framework
  - AddressBook.framework

After you're finished your "Link Binary With Libraries" section should look like this


![Link Binary with Libraries](http://cl.ly/image/201T2z3D390l/Resized%20Screen%202014-07-21%20at%2011.27.08%20am.png)

----

# Integrate Axonix Ads with Your Code

## Setup your App Delegate

You will need to __start the Axonix service__ in your App Delegate. To do this, you'll first
need to import the `Axonix.h` header into your App Delegate implementation file. If you installed
the AxonixSDK using CocoaPods, the import will look like this:

```
#import <AxonixSDK/Axonix.h>
```

If you installed AxonixSDK manually, the header will have to be imported differently

```
#import "Axonix.h"
```

After successfully importing the `Axonix.h` header into your App Delegate, all you
have to do is to call `[Axonix startWithApplicationId:@"your-application-id-here"]`.
You can put this call in the `application:didFinishLaunchingWithOptions:` method in
your App Delegate:

(Only the commented lines need to be added)

```
@implementation MyAppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    [Axonix startWithApplicationId:@"your-application-id-here"]; //Add this
}

@end
```

You should replace the `@"your-application-id-here"` string with a string containing
your actual application ID, found in the [Axonix Dashboard][2]. If you haven't created
an application in Axonix yet, you can follow [these steps][3] and you will recieve your
Application ID when you're finished.

> Important: Created applications and/or new or modified ad sizes are __not__ available immediately. They are
> first validated by our algorithms and then propagated and enabled. This process can take up to 1 hour. Please
> wait after application creation or after disabling test mode or adding sizes.


## Adding a Banner Ad in your Interface

> **IMPORTANT:** After enabling an ad in the developer dashboard you may have wait up to an hour before
> it becomes available.

### Add a view to your interface

Add a generic view to your user interface. In this example we are using the **320x50**
ad as an example, so we are setting the width to **320**, the height to **50**, and positioning
it at the very top of the interface

![](http://cl.ly/image/1Z2a1V3H1i1I/Resized%20Screen%202014-07-21%20at%2011.31.41%20am.png)


Next, set the _Custom Class_ of the view to `AxonixAdViewiPhone_320x50` in the Identity Inspector (⌥⌘3)


![](http://cl.ly/image/0O2M19211s2f/Resized%20Screen%202014-07-21%20at%2011.32.20%20am.png)

### Create and link an Outlet

To get access to the ad view in the View Controller, you'll need to add an outlet to the controller and link
it to the view in the interface

(Again, only the commented lines need adding)

```
#import "AXNViewController.h"
#import "AxonixAdView.h"  //Add this

@interface AXNViewController ()
@property (strong, nonatomic) IBOutlet AxonixAdView *adView;  //Add this
@end

@implementation AXNViewController

- (void)viewDidLoad
{
    [super viewDidLoad];
	// Do any additional setup after loading the view, typically from a nib.
}

@end
```

And then link the outlet to the view

![](http://cl.ly/image/3K1O13473v1j/AttachAdViewOutlet.gif)


### Register a delegate

Click on the view that you just created, and on the Connections inspector view.

You will see it has an Outlet called `delegate`. You can link this to your View
Controller (not the View itself) and have it respond to the
AxonixAdViewDelegate events.

To do so, add `<AxonixAdViewDelegate>` to the declaration of your ViewController,
as in the following example:

```
#import <UIKit/UIKit.h>
#import <AxonixAds.h>

@interface AXNViewController : UIViewController<AxonixAdViewDelegate> // here

@end
```

Now, declare the methods you want to listen to, so that it will look similar
to:

```
#import "AXNViewController.h"
#import <AxonixAds.h>

@interface AXNViewController ()
...
@end

@implementation DetailViewController

- (void)viewDidLoad
{
...
}

- (void)didReceiveMemoryWarning
{
...
}

// Called if ad fails to load

- (void)adView:(AxonixAdView*)adView didFailLoadWithError:(NSError*)error {
	NSLog(@"Banner Ad Failed: %@ (Error: %@).", NSStringFromCGSize(adView.frame.size), error);

	UIAlertView* alertView = [[UIAlertView alloc] initWithTitle:@"Error" message:[NSString stringWithFormat:@"Failed to load a banner ad.  Please try again. Detail Error: %@", error.userInfo.description] delegate:self cancelButtonTitle:@"OK" otherButtonTitles:nil];
	[alertView show];
}

// Called if the user clicks on the ad

- (void)adViewWillTouchThrough:(AxonixAdView*)adView {
	NSLog(@"Banner ad was clicked");
}

// Called after the ad was clicked, displayed and closed

- (void)adViewDidFinishTouchThrough:(AxonixAdView*)adView {
	NSLog(@"Banner ad was clicked and returned");
}


// Called when ad finishes loading (i.e. starts being displayed)

- (void)adViewDidFinishLoad:(AxonixAdView*)adView {
	NSLog(@"Banner Ad Loaded: %@.", NSStringFromCGSize(adView.frame.size));

}
```


### Request and Manage Ads

It’s very important to request ads properly. If you don’t follow the instructions below, you will have lower click through rates, which will lead to lower CPM’s and lower revenue for you.

#### Automatically request new ads

In your view controller's `viewDidAppear:` method, you can start requesting new ads by calling the `resumeAdAutoRefresh` method on the ad view. 

```
- (void)viewDidAppear:(BOOL)animated {
	[super viewDidAppear:animated];
	[self.adView resumeAdAutoRefresh];
}
```

This method will request the initial ad to display and also schedule a refresh timer to request new ads on a certain interval. If you'd like to manage requesting ads manually, you can use the `getAd` method instead of `resumeAdAutoRefresh`. Just note that everytime you call `getAd` a new ad is fetched and displayed in the ad view.


#### Pause ad requests when your view controller disappears

When your view controller goes off screen, you should pause the ad refresh timers.
You do this by calling `pauseAdAutoRefresh`. If you don’t pause ads when your view controller
goes off screen, you will not only lower your CPMs and CTRs, you will impact your applications overall performance.

```
- (void)viewDidDisappear:(BOOL)animated {
	[super viewDidDisappear:animated];
	[self.adView pauseAdAutoRefresh];
}
```


### Clean up your ad view

When the view controller that your ad view belongs to is deallocated, make sure you
cancel your ad

```
- (void)dealloc {
	[self.adView cancelAd];
}
```

## Adding a Fullscreen Ad to your Interface

Full Screen ads differ in a few key ways from regular unit ads:

* They do not auto-refresh
* They cover the entire screen and take the user away from your game or application
* Can be prefetched or displayed immediately at an appropriate point for your application or games lifecycle.

The flow for generating a Full Screen ad is a bit different. You can use any standard UIViewController with a delegate that conforms to the `AxonixFullScreenAdDelegate` protocol.

### Add a Full Screen ad to a view controller

Full screen ads are displayed on top of another view, and linked to it. All you need do is to
implement the `AxonixFullScreenAdDelegate` protocol and create a property to hold a strong reference to the full screen ad view controller. In the example below we are adding a full screen ad view controller to the `AxonixDemoFullScreenAdvertising` view controller:

#### AxonixDemoFullScreenAdvertising.h

```
#import <UIKit/UIKit.h>

@interface AxonixDemoFullScreenAdvertising : UIViewController
@end
```

#### AxonixDemoFullScreenAdvertising.m

```
#import "AxonixDemoFullScreenAdvertising.h"
#import <AxonixFullScreenAdViewController.h>

@interface AxonixDemoFullScreenAdvertising () <AxonixFullScreenAdDelegate>

@property (strong, nonatomic) AxonixFullScreenAdViewController *fullScreenAdViewController;

@end

@implementation AxonixDemoFullScreenAdvertising
- (void)viewDidLoad {
	[super viewDidLoad];
	
    self.fullScreenAdViewController = [[AxonixFullScreenAdViewController alloc] init];
	self.fullScreenAdViewController.delegate = self;
}
@end
```

Notice how we set the delegate of the `fullScreenAdViewController` to `self` which means that we need to adopt the `AxonixFullScreenAdDelegate` protocol in this class. All the methods of the `AxonixFullScreenAdDelegate` protocol are optional, but implementing them allows you to be notified of events in the lifecycle of the ad, like whether or not the ad is loaded successfully, whether the ad is dismissed or closed by the user, etc.

To handle all these, implement the following in your delegate. In our simple
example, the `AxonixDemoFullScreenAdvertising` view controller is the delegate so we just need to add the following to `AxonixDemoFullScreenAdvertising.m`:

```
// Called if ad fails to load

- (void)fullScreenAdViewController:(AxonixFullScreenAdViewController*)fullScreenAdViewController didFailToLoadWithError:(NSError*)error {
	NSLog(@"Failed to load full screen ad");

	UIAlertView* alertView = [[UIAlertView alloc] initWithTitle:@"Error" message:[NSString stringWithFormat:@"Failed to load a full screen ad.  Please try again. Detail Error: %@", error.userInfo.description] delegate:nil cancelButtonTitle:@"OK" otherButtonTitles:nil];
	[alertView show];
}

// Called when ad finishes loading (i.e. starts being displayed)

- (void)fullScreenAdViewControllerDidFinishLoad:(AxonixFullScreenAdViewController*)fullScreenAdViewController {
	NSLog(@"Full screen ad was loaded");

}

// Called when about to show ad

- (void)fullScreenAdViewControllerWillPresentAd:(AxonixFullScreenAdViewController*)fullScreenAdViewController {
	NSLog(@"Full screen ad will be presented");
}

// Called when the ad is closed / dismissed

- (void)fullScreenAdViewControllerDidDismissAd:(AxonixFullScreenAdViewController*)fullScreenAdViewController {
	NSLog(@"Full screen ad was dismissed");

}
```

### Request an ad (without pre-fetching)

In our example, we've setup the full screen ad view controller and it's delegate, but we still need to request the ad from Axonix before it can be displayed.

There's two ways to request ads: pre-fetching (where the actual ad is loaded
before displaying it, to avoid load delays), or direct.

Direct is the easiest way, you can load and display it with just one simple call. Add a
call similar to this in your view controller:

```
[self.fullScreenAdViewController requestAndDisplayAdFromViewController:self];
```

This will issue the call and then the delegate methods will be called according to what happened:

* `fullScreenAdViewController:didFailToLoadWithError:`
* `fullScreenAdViewControllerDidFinishLoad:`
* `fullScreenAdViewControllerWillPresentAd:`
* `fullScreenAdViewControllerDidDismissAd:`

### Request an ad with pre-fetching and display it later

You can request an ad but not display it yet, by invoking

```
[self.fullScreenAdViewController requestAd];
```

As in the previous case, this will trigger delegate methods:

* `fullScreenAdViewController:didFailToLoadWithError:`
* `fullScreenAdViewControllerDidFinishLoad:`

You can use those methods to detect whether the ad was actually loaded. You can also
use the `hasAd` property (BOOL) to know if an ad is pre-fetched.

When your application is ready to display the full screen ad, for example after the player
has finished a level, you can invoke:

```
[self.fullScreenAdViewController displayRequestedAdFromViewController:self];
```

Do not invoke it if the `hasAd` property returns false.

This will display the ad. You can use the delegate methods to move to the next action in
your application:

* `fullScreenAdViewControllerWillPresentAd:`
* `fullScreenAdViewControllerDidDismissAd:`


# Using the Axonix Demo App

Simply download it, edit the AppDelegate.m file and replace "your-application-id-here" with
the application ID you received. Then from the [Developer Dashboard at axonix.com][2]
register Ad Sizes 320x50 and Fullscreen. You will have to wait up to 1 hour until this is
validated and propagated across the network. After that, ads will show.

[1]: http://guides.cocoapods.org/using/getting-started.html
[2]: https://developer.axonix.com/account/applications
[3]: https://developer.axonix.com/account/general/add_application
