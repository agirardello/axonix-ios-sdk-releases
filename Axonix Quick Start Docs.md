## Axonix SDK 7.0 BETA Release

Please note this is a BETA release and it could contain minor bugs, please feel free to raise issues [on Github](https://github.com/AxonixRTB/axonix-ios-sdk-releases/issues)

### Installation

There are two ways to install the Axonix SDK: using [CocoaPods][1] or by downloading and then
doing it manually. We highly suggest using CocoaPods because CocoaPods will take care of adding all of the necessary dependencies.

#### Using CocoaPods

Add **AxonixSDK** to your Podfile

```
pod "AxonixSDK", "~> 7.0"
```

We recommend using the `~> 7.0` version specifier so you will automatically get
small bug fix updates that don't break the API.


#### Manually

##### 1. Add the Axonix SDK files to your project


- Command-click the project in the Project Navigator and select Add Files to "&lt;Your Project Name&gt;"

![Add file to project](http://cl.ly/image/0S443z19341m/Resized%20Screen%202014-07-21%20at%2010.24.47%20am.png)



- Add the AxonixSDK directory with the following settings

![Adding the AxonixSDK with the correct settings](http://cl.ly/image/1B3p1j3b3g3i/Resized%20Screen%202014-07-21%20at%2010.23.55%20am.png)


After clicking the "Add" button the static framework **libAxonixSDK.a** and the headers
should be added to your project

![Updated Project Navigator with Axonix SDK](http://cl.ly/image/3N2b393i2I0b/Resized%20Screen%202014-07-21%20at%2010.28.00%20am.png)



Make sure **libAxonixSDK.a** is added to your application target


![Making sure the framework is added to the target](http://cl.ly/image/0o3y0U1K0G1d/Napkin%204%2021-07-14%2010.51.59%20am.png)





##### 2. Link Framework Dependencies

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


### Integrating with Your Code

#### 1. Setting up your App Delegate

You will need to start the Axonix service in your App Delegate. To do this, you'll first
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
@implementation AXNAppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    [Axonix startWithApplicationId:@"your-application-id-here"]; //Add this
}

@end
```

You should replace the `@"your-application-id-here"` string your a string containing
your actual application ID, found in the [Axonix Dashboard][2]. If you haven't created
an application in Axonix yet, you can follow [these steps][3] and you'll recieve your
Application ID when you're finished.


#### Creating an Ad in your Interface

**IMPORTANT:** After enabling an ad in the developer dashboard you may have wait up to an hour before
 it becomes available.

##### 1. Add a view to your interface

Add a generic view to your user interface. In this example we are using the **320x50**
ad as an example, so we are setting the width to **320**, the height to **50**, and positioning
it at the very top of the interface

![](http://cl.ly/image/1Z2a1V3H1i1I/Resized%20Screen%202014-07-21%20at%2011.31.41%20am.png)


Next, set the Custom Class of the view to `AxonixAdViewiPhone_320x50` in the Identity Inspector (⌥⌘3)


![](http://cl.ly/image/0O2M19211s2f/Resized%20Screen%202014-07-21%20at%2011.32.20%20am.png)

##### 2. Create an Outlet

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


### Requesting and Managing Ads

It’s very important to request ads properly. If you don’t follow the instructions below, you will have lower click through rates, which will lead to lower CPM’s and lower revenue for you.

#### Automatically request new ads

In your view controller’s `viewDidAppear:` method, you should start the ad refresh timer
by calling `resumeAdAutoRefresh`. If this is the first time you call this method, it will automatically call `getAd`.
Subsequent calls to this method will reschedule the auto refresh timers.

```
- (void)viewDidAppear:(BOOL)animated {
	[super viewDidAppear:animated];
	[self.adView resumeAdAutoRefresh];
}
```

#### Pause ad requests when your view controller disappears

When your view controller goes off screen, you should pause the ad refresh timers.
You do this by calling `pauseAdAutoRefresh`. If you don’t pause ads when your view controller
goes off screen, you will not only lower your CPMs and CTRs, you will impact your applications overall performance.

```
- (void)viewWillDisappear:(BOOL)animated {
	[super viewWillDisappear:animated];
	[self.adView pauseAdAutoRefresh];
}
```


#### Cleaning up your ad view

When the view controller that your ad view belongs to is deallocated, make sure you
cancel your ad

```
- (void)dealloc {
	[self.adView cancelAd];
}
```

[1]: http://guides.cocoapods.org/using/getting-started.html
[2]: https://developer.mobclix.com/account/applications
[3]: https://developer.mobclix.com/account/general/add_application
