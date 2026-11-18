# 네이티브

{% hint style="info" %}
* AdWhale SDK를 프로젝트에 추가 해야합니다.
* Native Ad용으로 발급받은 Ad Unit ID를 사용합니다.
* 광고를 요청하기 전에 SDK 초기화를 진행합니다.
{% endhint %}

### NativeAdView 설정

#### 1. 레이아웃 설정

Native Ad의 경우, 광고에 사용 될 레이아웃을 직접 구성해야 합니다. 필수 구성 요소들은 다음과 같습니다.

* **Title** : `UILabel`
* **Body** : `UILabel`
* **Call To Action** : `UIButton`
* **Profile Name** : `UILabel`
* **Profile Icon Image** : `UIImageView`
* **Media View** : `AdWhaleMediaView`

{% hint style="info" %}
위 요소들은 **반드시 모두 포함**되어야 합니다.

광고 컨텐츠를 덮는 View가 존재해서는 안 됩니다.

텍스트, 이미지, 터치 액션 등 **광고 컨텐츠를 가공하거나 변경해서는 안 됩니다.**
{% endhint %}

#### 2. NativeAdView 클래스 구현

광고 레이아웃은 `AdWhaleNativeAdView`를 상속받아 구현하며,\
각 광고 구성 요소를 SDK에 전달하기 위해 **필수 메서드를 오버라이드**해야 합니다.

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

### NativeAdLoader 설정

#### 1. 광고 객체 생성

네이티브 광고를 사용하기 위해 `AdWhaleNativeAdLoader` 객체를 생성합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
let nativeAdLoader = AdWhaleNativeAdLoader()
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
AdWhaleNativeAdLoader *nativeAdLoader = [[AdWhaleNativeAdLoader alloc] init];
```
{% endtab %}
{% endtabs %}

#### 2. 광고 객체 설정(초기화)

네이티브 광고용으로 발급받은 **Ad Unit ID**와 **RootViewController**를 설정(초기화)합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
nativeAdLoader.initialize(adUnitId: "네이티브 광고 AD_UNIT_ID 입력", rootViewController: self)
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[self.nativeAdLoader initializeWithAdUnitId:@"네이티브 광고 AD_UNIT_ID 입력"
                         rootViewController:self];
```
{% endtab %}
{% endtabs %}

#### 3. Delegate 설정

네이티브 광고의 이벤트는 **Delegate를 통해 전달**됩니다.

* Loader Delegate: 로드 성공/실패 콜백 수신
* NativeAd Delegate: 노출/클릭/화면 전환 등 이벤트 수신

{% tabs %}
{% tab title="Swift" %}
```swift
nativeAdLoader.delegate = self
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
self.nativeAdLoader.delegate = self;
```
{% endtab %}
{% endtabs %}

#### 4. 광고 요청

Delegate 설정이 완료되면 `load()`를 호출하여 네이티브 광고를 요청합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
nativeAdLoader.loadAd()
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[self.nativeAdLoader loadAd];
```
{% endtab %}
{% endtabs %}

#### 5. 광고 표시(바인딩)

광고가 로드된 이후, **직접 구성한 NativeAdView 레이아웃**을 생성한 뒤 `bind`/`bindView`로 바인딩하여 화면에 표시합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
nativeAdLoader.bind(nativeAdView)
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
AdWhaleNativeAdView *adView = [self.nativeAdLoader bindView:nativeAdView];
[self.nativeAdPlaceholder addSubview:adView];
```
{% endtab %}
{% endtabs %}



### NativeAd Delegate 설명

네이티브 광고의 상태 변화는 Delegate를 통해 전달됩니다.

| Delegate 메서드                     | 설명                                                |
| -------------------------------- | ------------------------------------------------- |
| nativeAdLoaderDidReceiveAd       | 네이티브 광고가 성공적으로 로드되었을 때 호출됩니다.                     |
| nativeAdLoaderDidFailToReceiveAd | 네이티브 광고 로드에 실패했을 때 호출됩니다.                         |
| nativeAdDidImpression            | 네이티브 광고가 화면에 노출되었을 때 호출됩니다.                       |
| nativeAdDidClickAd               | 네이티브 광고를 클릭했을 때 호출됩니다.                            |
| nativeAdWillPresentScreen        | 네이티브 광고 클릭으로 전체 화면이 표시되기 직전에 호출됩니다.               |
| nativeAdWillDismissScreen        | 네이티브 광고로 열린 화면이 닫히기 직전에 호출됩니다.                    |
| nativeAdDidDismissScreen         | 네이티브 배너 광고로 열린 화면이 완전히 닫힌 후 호출됩니다.                |
| nativeAdWillLeaveApplication     | 광고 클릭으로 인해 외부 앱 또는 브라우저로 이동하여 앱이 비활성화되기 직전 호출됩니다. |

### 네이티브 광고 구현 샘플

{% tabs %}
{% tab title="Swift" %}
```swift
import UIKit
import AdWhaleSDK

class ViewController: UIViewController {
    var nativeAdLoader: AdWhaleNativeAdLoader?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // NativeAdLoader Setting
        nativeAdLoader = AdWhaleNativeAdLoader()
        nativeAdLoader?.initialize(adUnitId: "네이티브 광고 AD_UNIT_ID 입력", rootViewController: self)
        nativeAdLoader?.delegate = self
        
        // Native Ad Request
        nativeAdLoader?.loadAd()
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
        nativeAdLoader?.bind(nativeAdView)
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

@property (nonatomic, strong) AdWhaleNativeAdLoader *nativeAdLoader;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
    
    // NativeAdLoader Setting
    nativeAdLoader = [[AdWhaleNativeAdLoader alloc] init];
    [nativeAdLoader initializeWithAdUnitId:@"네이티브 광고 AD_UNIT_ID 입력" rootViewController:self];
    nativeAdLoader.delegate = self;
    
    // Native Ad Request
    [nativeAdLoader loadAd];
}

- (void)setNativeAdView {
    NativeAdView *nativeAdView = [[[NSBundle mainBundle] loadNibNamed:@"NativeAdView" owner:nil options:nil] firstObject];
    
    nativeAdView.frame = CGRectMake(10,
                                    [UIScreen mainScreen].bounds.size.height - 430,
                                    [UIScreen mainScreen].bounds.size.width - 20,
                                    350);
    AdWhaleNativeAdView *adView = [nativeAdLoader bindView:nativeAdView];
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
