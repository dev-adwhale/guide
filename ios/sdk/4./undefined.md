# 앱 오프닝

{% hint style="info" %}
* AdWhale SDK를 프로젝트에 추가 해야합니다.
* App Open Ad용으로 발급받은 Ad Unit ID를 사용합니다.
* 광고를 요청하기 전에 SDK 초기화를 진행합니다.
{% endhint %}

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
