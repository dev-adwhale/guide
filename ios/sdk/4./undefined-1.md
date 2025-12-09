# 배너

{% hint style="info" %}
* AdWhale SDK를 프로젝트에 추가 해야합니다.
* Banner Ad용으로 발급받은 Ad Unit ID를 사용합니다.
* 광고를 요청하기 전에 SDK 초기화를 진행합니다.
{% endhint %}

| **AdWhaleAdSize(Swift)** | **AdWhaleAdSize(ObjC)**      | **Size (width \* height)** |
| ------------------------ | ---------------------------- | -------------------------- |
| .banner                  | AdWhaleAdSizeBanner          | 320 \*50                   |
| .largeBanner             | AdWhaleAdSizeLargeBanner     | 320 \* 100                 |
| .mediumRectangle         | AdWhaleAdSizeMediumRectangle | 300 \* 250                 |



## 1. Storyboard 방식

{% tabs %}
{% tab title="Swift" %}
```swift
import UIKit
import AdWhaleSDK

class ViewController: UIViewController {
    @IBOutlet var bannerView: AdWhaleBannerAd!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // BannerView Setting
        bannerView.setAdSize(.banner)
        bannerView.setAdUnitID("배너 광고 AD_UNIT_ID 입력")
        bannerView.setRootViewController(self)
        bannerView.setDelegate(self)
        
        // Banner Ad Request
        bannerView.load()
    }
}

// MARK: BannerAd Delegate
extension ViewController: AdWhaleBannerDelegate {
    func bannerViewDidReceiveAd(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewDidReceiveAd")
    }
    
    func bannerView(_ bannerView: AdWhaleSDK.AdWhaleBannerAd, didFailToReceiveAdWithError error: Error) {
        print("ViewController didFailToReceiveAdWithError: \(error.localizedDescription)")
    }
    
    func bannerViewDidRecordImpression(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewDidRecordImpression")
    }
    
    func bannerViewWillPresentScreen(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewWillPresentScreen")
    }
    
    func bannerViewWillDismissScreen(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewWillDismissScreen")
    }
    
    func bannerViewDidDismissScreen(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewDidDismissScreen")
    }
}
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
#import "ViewController.h"
@import AdWhaleSDK;

@interface ViewController () <AdWhaleBannerDelegate>

@property (nonatomic, strong) IBOutlet AdWhaleBannerAd *bannerView;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
    
    // BannerView Setting
    [_bannerView setAdSize:AdWhaleAdSizeBanner];
    [_bannerView setAdUnitID:@"배너 광고 AD_UNIT_ID 입력"];
    [_bannerView setRootViewController:self];
    [_bannerView setDelegate:self];
    
    // Banner Ad Request
    [self.bannerView load];
}

#pragma mark - BannerAd Delegate
- (void)bannerViewDidReceiveAd:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewDidReceiveAd");
}

- (void)bannerView:(AdWhaleBannerAd *)bannerView didFailToReceiveAdWithError:(NSError *)error {
    NSLog(@"ViewController didFailToReceiveAdWithError: %@", error.localizedDescription);
}

- (void)bannerViewDidRecordImpression:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewDidRecordImpression");
}

- (void)bannerViewWillPresentScreen:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewWillPresentScreen");
}

- (void)bannerViewWillDismissScreen:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewWillDismissScreen");
}

- (void)bannerViewDidDismissScreen:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewDidDismissScreen");
}

@end
```
{% endtab %}
{% endtabs %}



## 2. 프로그램 코드 방식

{% tabs %}
{% tab title="Swift" %}
```swift
import UIKit
import AdWhaleSDK

class ViewController: UIViewController {
    var bannerView: AdWhaleBannerAd?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        let frame = CGRect(x: 0,
                           y: 500,
                           width: UIScreen.main.bounds.width,
                           height: 50)
        
        bannerView = AdWhaleBannerAd(frame: frame)
        view.addSubview(bannerView)
        
        // BannerView Setting
        bannerView?.setAdSize(.banner)
        bannerView?.setAdUnitID("배너 광고 AD_UNIT_ID 입력")
        bannerView?.setRootViewController(self)
        bannerView?.setDelegate(self)
        
        // Banner Ad Request
        bannerView.load()
    }
}

// MARK: BannerAd Delegate
extension ViewController: AdWhaleBannerDelegate {
    func bannerViewDidReceiveAd(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewDidReceiveAd")
    }
    
    func bannerView(_ bannerView: AdWhaleSDK.AdWhaleBannerAd, didFailToReceiveAdWithError error: Error) {
        print("ViewController didFailToReceiveAdWithError: \(error.localizedDescription)")
    }
    
    func bannerViewDidRecordImpression(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewDidRecordImpression")
    }
    
    func bannerViewWillPresentScreen(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewWillPresentScreen")
    }
    
    func bannerViewWillDismissScreen(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewWillDismissScreen")
    }
    
    func bannerViewDidDismissScreen(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewDidDismissScreen")
    }
}
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
#import "ViewController.h"
@import AdWhaleSDK;

@interface ViewController () <AdWhaleBannerDelegate>

@property (nonatomic, strong) AdWhaleBannerAd *bannerView;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
    
    // BannerView Create
    CGRect frame = CGRectMake(0,
                              500,
                              UIScreen.mainScreen.bounds.size.width,
                              50);
    self.bannerView = [[AdWhaleBannerAd alloc] initWithFrame:frame];
    [self.view addSubview:self.bannerView];
    
    // BannerView Setting
    [_bannerView setAdSize:AdWhaleAdSizeBanner];
    [_bannerView setAdUnitID:@"배너 광고 AD_UNIT_ID 입력"];
    [_bannerView setRootViewController:self];
    [_bannerView setDelegate:self];
    
    // Banner Ad Request
    [self.bannerView load];
}

#pragma mark - BannerAd Delegate
- (void)bannerViewDidReceiveAd:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewDidReceiveAd");
}

- (void)bannerView:(AdWhaleBannerAd *)bannerView didFailToReceiveAdWithError:(NSError *)error {
    NSLog(@"ViewController didFailToReceiveAdWithError: %@", error.localizedDescription);
}

- (void)bannerViewDidRecordImpression:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewDidRecordImpression");
}

- (void)bannerViewWillPresentScreen:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewWillPresentScreen");
}

- (void)bannerViewWillDismissScreen:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewWillDismissScreen");
}

- (void)bannerViewDidDismissScreen:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewDidDismissScreen");
}

@end
```
{% endtab %}
{% endtabs %}
