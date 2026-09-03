# 앱 오프닝

{% hint style="info" %}
* AdWhale SDK를 프로젝트에 추가 해야합니다.
* App Open Ad용으로 발급받은 Ad Unit ID를 사용합니다.
* 광고를 요청하기 전에 SDK 초기화를 진행합니다.
{% endhint %}

### AppOpenAd 설정

#### 1. 광고 단위 설정

앱 오프닝 광고용으로 발급받은 `ad unit ID`를 사용하여 광고 단위를 설정하세요.

{% tabs %}
{% tab title="Swift" %}
```swift
AdWhaleAppOpenAd.shared.adUnitId = "앱 오프닝 광고 AD_UNIT_ID 입력"
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
AdWhaleAppOpenAd.shared.adUnitId = @"앱 오프닝 광고 AD_UNIT_ID 입력";
```
{% endtab %}
{% endtabs %}

#### 2. Delegate 설정

앱 오프닝 광고의 이벤트는 **Delegate를 통해 전달**됩니다.

{% tabs %}
{% tab title="Swift" %}
```swift
AdWhaleAppOpenAd.shared.appOpenAdDelegate = self
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
AdWhaleAppOpenAd.shared.appOpenAdDelegate = self;
```
{% endtab %}
{% endtabs %}

#### 3. 광고 요청

Delegate 및 Ad Unit ID 설정이 완료되면 `loadAd()`를 호출하여 **App Open 광고를 요청** 합니다.\
광고 요청 결과는 `adDidReceiveAppOpenAd` / `adDidFailToReceiveAppOpenAd` 콜백으로 전달됩니다.

{% tabs %}
{% tab title="Swift" %}
```swift
AdWhaleAppOpenAd.shared.loadAd()
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[AdWhaleAppOpenAd.shared loadAd];
```
{% endtab %}
{% endtabs %}

#### 4. 광고 표시

광고가 로드된 상태라면 `showAdIfAvailable()`를 호출하여 **전면 광고를 표시**할 수 있습니다.

* 이때 지정한 ViewController를 기준으로 전면 광고를 표시합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
AdWhaleAppOpenAd.shared.showAdIfAvailable(self)
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[AdWhaleAppOpenAd.shared showAdIfAvailable:self];
```
{% endtab %}
{% endtabs %}

### AppOpenAd Delegate 설명

App Open 광고의 상태 변화는 Delegate를 통해 전달됩니다.

| Delegate 메서드                | 설명                             |
| --------------------------- | ------------------------------ |
| adDidReceiveAppOpenAd       | 앱 오프닝 광고가 성공적으로 로드되었을 때 호출됩니다. |
| adDidFailToReceiveAppOpenAd | 앱 오프닝 광고 로드에 실패했을 때 호출됩니다.     |
| adWillPresentAppOpenAd      | 앱 오프닝 광고가 화면에 노출되었을 때 호출됩니다.   |
| adDidFailToPresentAppOpenAd | 앱 오프닝 광고 노출에 실패했을 때 호출됩니다.     |
| adDidDismissAppOpenAd       | 앱 오프닝 광고가 종료되었을 때 호출됩니다.       |

{% hint style="info" %}
**앱 오프닝 광고는 앱 상태가 백그라운드에서 포그라운드로 변경될 때 광고를 노출합니다.**

App Open 광고를 사용하기 위해 `AdWhaleAppOpenAd.shared`에 **Ad Unit ID와 Delegate를 설정**한 뒤 `loadAd()`를 호출하여 광고를 요청합니다.

포그라운드 전환(또는 앱 실행) 시점은 앱의 라이프사이클 이벤트를 기준으로 **앱 구현에 따라 처리되며**,\
광고 로드가 완료되면 `showAdIfAvailable()`를 호출해 광고를 표시합니다.
{% endhint %}

### 앱 오프닝 광고 구현 샘플

{% tabs %}
{% tab title="Swift" %}
```swift
import UIKit
import AdWhaleSDK

class SplashViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        // Do any additional setup after loading the view.
        AdWhaleAppOpenAd.shared.adUnitId = "앱 오프닝 광고 AD_UNIT_ID 입력"
        AdWhaleAppOpenAd.shared.appOpenAdDelegate = self
        
        // AppOpen 광고 요청
        AdWhaleAppOpenAd.shared.loadAd()
    }
    
    // Main 화면으로 이동
    func startMainScreen() {
        let mainStoryBoard = UIStoryboard(name: "Main", bundle: nil)
        let mainViewController = mainStoryBoard.instantiateViewController(withIdentifier: "MainViewController")
        present(mainViewController, animated: true) {
            self.dismiss(animated: false) {
                let keyWindow = UIApplication.shared.windows.first(where: { $0.isKeyWindow })
                keyWindow?.rootViewController = mainViewController
            }
        }
    }
}

// MARK: AppOpenAd Delegate
extension SplashViewController: AdWhaleAppOpenAdDelegate {
    func adDidReceiveAppOpenAd(_ ad: AdWhaleAppOpenAd) {
        // 광고 로드가 완료되면 메소드가 실행됩니다.
        print("adDidReceiveAppOpenAd")
        
        // AppOpen 광고 표시
        AdWhaleAppOpenAd.shared.showAdIfAvailable(self)
    }
    
    func adDidFailToReceiveAppOpenAd(error: Error) {
        // 광고 로드가 실패하고 발생한 오류를 포함합니다.
        print("adDidFailToReceiveAppOpenAd sample error: \(error.localizedDescription)")
        startMainScreen()
    }
    
    func adWillPresentAppOpenAd() {
        // 광고가 전체 화면 콘텐츠를 표시할 때 호출됩니다.
        print("adWillPresentAppOpenAd")
    }
    
    func adDidDismissAppOpenAd() {
        // 전체 화면 콘텐츠를 닫을 때 호출됩니다.
        print("adDidDismissAppOpenAd")
        AdWhaleAppOpenAd.shared.loadAd()
        startMainScreen()
    }
    
    func adDidFailToPresentAppOpenAd(error: Error) {
        // 전체 화면 콘텐츠 표시가 실패하고 발생한 오류를 포합합니다.
        print("adDidFailToPresentAppOpenAd error: \(error)")
        AdWhaleAppOpenAd.shared.loadAd()
        startMainScreen()
    }
}

```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
#import "SplashViewController.h"
@import AdWhaleSDK;

@interface SplashViewController () <AdWhaleAppOpenAdDelegate>

@end

@implementation SplashViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
    AdWhaleAppOpenAd.shared.adUnitId = @"앱 오프닝 광고 AD_UNIT_ID 입력";
    AdWhaleAppOpenAd.shared.appOpenAdDelegate = self;
    
    // AppOpen 광고 요청
    [[AdWhaleAppOpenAd shared] loadAd];
}

// Main 화면으로 이동
- (void)startMainScreen {
    UIStoryboard *mainStoryBoard = [UIStoryboard storyboardWithName:@"Main" bundle:nil];
    UIViewController *mainViewController = [mainStoryBoard instantiateViewControllerWithIdentifier:@"MainViewController"];
    [self presentViewController:mainViewController animated:YES completion:^{
        [self dismissViewControllerAnimated:NO completion:^{
            UIWindow *keyWindow = [[UIApplication sharedApplication].windows firstObject];
            keyWindow.rootViewController = mainViewController;
        }];
    }];
}

#pragma mark - AppOpenAd Delegate

- (void)adDidReceiveAppOpenAd:(AdWhaleAppOpenAd *)ad {
    // 광고 로드가 완료되면 메소드가 실행됩니다.
    NSLog(@"adDidReceiveAppOpenAd");
    
    // AppOpen 광고 표시
    [AdWhaleAppOpenAd.shared showAdIfAvailable:self];
}

- (void)adDidFailToReceiveAppOpenAdWithError:(NSError * _Nonnull)error {
    // 광고 로드가 실패하고 발생한 오류를 포함합니다.
    NSLog(@"adDidFailToReceiveAppOpenAd sample error: %@", error.localizedDescription);
    [self startMainScreen];
}

- (void)adWillPresentAppOpenAd {
    // 광고가 전체 화면 콘텐츠를 표시할 때 호출됩니다.
    NSLog(@"adWillPresentAppOpenAd");
}

- (void)adDidDismissAppOpenAd {
    // 전체 화면 콘텐츠를 닫을 때 호출됩니다.
    NSLog(@"adDidDismissAppOpenAd");
    [AdWhaleAppOpenAd.shared loadAd];
    [self startMainScreen];
}

- (void)adDidFailToPresentAppOpenAdWithError:(NSError * _Nonnull)error {
    // 전체 화면 콘텐츠 표시가 실패하고 발생한 오류를 포합합니다.
    NSLog(@"adDidFailToPresentAppOpenAd error: %@", error);
    [AdWhaleAppOpenAd.shared loadAd];
    [self startMainScreen];
}
```
{% endtab %}
{% endtabs %}
