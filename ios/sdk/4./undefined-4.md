# 네이티브

{% hint style="info" %}
* AdWhale SDK를 프로젝트에 추가 해야합니다.
* Native Ad용으로 발급받은 Ad Unit ID를 사용합니다.
* 광고를 요청하기 전에 SDK 초기화를 진행합니다.
{% endhint %}



## 1. 레이아웃 설정

Native Ad의 경우, 광고에 사용 될 레이아웃을 직접 구성해야 합니다. 필수 구성 요소들은 다음과 같습니다.

* **Title** : `UILabel`
* **Body** : `UILabel`
* **Call To Action** : `UIButton`
* **Profile Name** : `UILabel`
* **Profile Icon Image** : `UIImageView`
* **Media View** : `AdWhaleMediaView`

{% hint style="info" %}
이 요소들은 반드시 포함하여 구성하여 주시고, 광고 컨텐츠를 덮는 View가 없어야 합니다.&#x20;

또한 텍스트 변경, 이미지 변경, 터치 시 액션 변경 등 광고 컨텐츠에 관련된 부분을 가공하거나 변경하지 않도록 주의 부탁드립니다.
{% endhint %}

{% tabs %}
{% tab title="Swift" %}
```swift
import UIKit
import AdWhaleSDK

class NativeAdView: AdWhaleNativeAdView {

    @IBOutlet var titleLabel: UILabel!
    @IBOutlet var bodyLabel: UILabel!
    @IBOutlet var callToActionButton: UIButton!
    @IBOutlet var profileNameLabel: UILabel!
    @IBOutlet var profileIconView: UIImageView!
    @IBOutlet var adWhaleMediaView: AdWhaleMediaView!
    
    override func adTitleLabel() -> UILabel {
        return titleLabel
    }
    
    override func adBodyLabel() -> UILabel {
        return bodyLabel
    }
    
    override func adCallToActionButton() -> UIButton {
        return callToActionButton
    }
    
    override func adProfileNameLabel() -> UILabel {
        return profileNameLabel
    }
    
    override func adProfileIconView() -> UIImageView {
        return profileIconView
    }
    
    override func adMediaView() -> AdWhaleMediaView {
        return adWhaleMediaView
    }

}
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
#import "NativeAdView.h"
@import AdWhaleSDK;

@interface NativeAdView () <AdWhaleRenderable>

@property (strong, nonatomic) IBOutlet UILabel *titleLabel;
@property (strong, nonatomic) IBOutlet UILabel *bodyLabel;
@property (strong, nonatomic) IBOutlet UIButton *callToActionButton;
@property (strong, nonatomic) IBOutlet UILabel *profileNameLabel;
@property (strong, nonatomic) IBOutlet UIImageView *profileIconView;
@property (strong, nonatomic) IBOutlet AdWhaleMediaView *mediaView;

@end

@implementation NativeAdView

- (UILabel * _Nonnull)adBodyLabel {
    return _bodyLabel;
}

- (UIButton * _Nonnull)adCallToActionButton {
    return _callToActionButton;
}

- (AdWhaleMediaView * _Nonnull)adMediaView {
    return _mediaView;
}

- (UIImageView * _Nonnull)adProfileIconView {
    return _profileIconView;
}

- (UILabel * _Nonnull)adProfileNameLabel {
    return _profileNameLabel;
}

- (UILabel * _Nonnull)adTitleLabel {
    return _titleLabel;
}

@end

```
{% endtab %}
{% endtabs %}



## 2. 구현

{% tabs %}
{% tab title="Swift" %}
```swift
import UIKit
import AdWhaleSDK

class ViewController: UIViewController {
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        AdWhaleNativeAdLoader.sharedInstance.initialize(adUnitId: "네이티브 광고 AD_UNIT_ID 입력", rootViewController: self)
        AdWhaleNativeAdLoader.sharedInstance.delegate = self
        
        AdWhaleNativeAdLoader.sharedInstance.loadAd()
    }
    
    func setNativeAdView() {
        let nibView = Bundle.main.loadNibNamed("NativeAdView", owner: nil)?.first
        guard let nativeAdView = nibView as? NativeAdView else {
            print("NativeAdView is nil")
            return
        }
        
        nativeAdView.frame = CGRect(x: 10,
                                    y: (Int(UIScreen.main.bounds.height) - 460),
                                    width: (Int(UIScreen.main.bounds.width) - 20),
                                    height: 380)
        self.view.addSubview(nativeAdView)
        AdWhaleNativeAdLoader.sharedInstance.bind(nativeAdView)
    }
}

// MARK: NativeAd Delegate
extension ViewController: AdWhaleNativeAdLoaderDelegate, AdWhaleNativeAdDelegate {
    func nativeAdLoaderDidReceiveAd(_ nativeAd: AdWhaleSDK.AdWhaleNativeAd) {
        print("Received native ad: \(nativeAd)")
        
        nativeAd.delegate = self
        
        setNativeAdView()
    }
    
    func nativeAdLoaderDidFailToReceiveAd(_ nativeAd: AdWhaleSDK.AdWhaleNativeAdLoader, error: Error) {
        print("adLoader didFailToAdWithError \(error.localizedDescription)")
    }
    
    func nativeAdDidClickAd(_ nativeAd: AdWhaleSDK.AdWhaleNativeAd) {
        print("nativeAdDidClickAd")
    }
    
    func nativeAdDidImpression(_ nativeAd: AdWhaleSDK.AdWhaleNativeAd) {
        print("nativeAdDidImpression")
    }
    
    func nativeAdWillPresentScreen(_ nativeAd: AdWhaleSDK.AdWhaleNativeAd) {
        print("nativeAdWillPresentScreen")
    }
    
    func nativeAdWillDismissScreen(_ nativeAd: AdWhaleSDK.AdWhaleNativeAd) {
        print("nativeAdWillDismissScreen")
    }
    
    func nativeAdDidDismissScreen(_ nativeAd: AdWhaleSDK.AdWhaleNativeAd) {
        print("nativeAdDidDismissScreen")
    }
    
    func nativeAdWillLeaveApplication(_ nativeAd: AdWhaleSDK.AdWhaleNativeAd) {
        print("nativeAdWillLeaveApplication")
    }
}
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
#import "ViewController.h"
@import AdWhaleSDK;

@interface ViewController () <AdWhaleNativeAdLoaderDelegate, AdWhaleNativeAdDelegate>

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
    
    // NativeAdLoader Setting
    AdWhaleNativeAdLoader *adLoader = [AdWhaleNativeAdLoader sharedInstance];
    [adLoader initializeWithAdUnitId:@"네이티브 광고 AD_UNIT_ID 입력" rootViewController:self];
    adLoader.delegate = self;
    
    // Native Ad Request
    [adLoader loadAd];
}

- (void)setNativeAdView {
    NativeAdView *nativeAdView = [[[NSBundle mainBundle] loadNibNamed:@"NativeAdView" owner:nil options:nil] firstObject];
    
    nativeAdView.frame = CGRectMake(10,
                                    [UIScreen mainScreen].bounds.size.height - 430,
                                    [UIScreen mainScreen].bounds.size.width - 20,
                                    350);
    AdWhaleNativeAdView *adView = [[AdWhaleNativeAdLoader sharedInstance] bindView:nativeAdView];
    [self.view addSubview:adView];
}

#pragma mark - NativeAd Delegate

- (void)nativeAdLoaderDidReceiveAd:(AdWhaleNativeAd *)nativeAd {
    NSLog(@"Received native ad: %@", nativeAd);
    
    nativeAd.delegate = self;
    
    [self setNativeAdView];
}

- (void)nativeAdLoaderDidFailToReceiveAd:(AdWhaleNativeAdLoader *)nativeAdLoader error:(NSError *)error {
    NSLog(@"adLoader didFailToAdWithError %@", error.localizedDescription);
}

- (void)nativeAdDidClickAd:(AdWhaleNativeAd *)nativeAd {
    NSLog(@"nativeAdDidClickAd");
}

- (void)nativeAdDidImpression:(AdWhaleNativeAd *)nativeAd {
    NSLog(@"nativeAdDidImpression");
}

- (void)nativeAdWillPresentScreen:(AdWhaleNativeAd *)nativeAd {
    NSLog(@"nativeAdWillPresentScreen");
}

- (void)nativeAdWillDismissScreen:(AdWhaleNativeAd *)nativeAd {
    NSLog(@"nativeAdWillDismissScreen");
}

- (void)nativeAdDidDismissScreen:(AdWhaleNativeAd *)nativeAd {
    NSLog(@"nativeAdDidDismissScreen");
}

- (void)nativeAdWillLeaveApplication:(AdWhaleNativeAd *)nativeAd {
    NSLog(@"nativeAdWillLeaveApplication");
}

@end
```
{% endtab %}
{% endtabs %}
