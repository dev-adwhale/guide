# 전면

{% hint style="info" %}
전면 광고는 앱 화면 전체를 덮는 형태로 표시되는 광고입니다. 화면 전환 시점 등 자연스러운 지점에 노출하여 높은 주목도를 제공합니다.\
이 문서는 iOS 프로젝트에서 AdWhale Mediation SDK 를 사용하여 전면 광고를 연동하는 방법을 설명합니다.
{% endhint %}

#### 1. 주요 특징

* 화면 전체를 덮는 전면형 광고
* 로드와 노출 시점을 앱이 직접 제어 (`loadAd()` → `show(from:)`)
* 노출 전 취소 지원 (`cancelAd()`)
* 로드 · 노출 · 클릭 · 닫힘 등 이벤트 기반 델리게이트 콜백 시스템으로 광고 상태 추적 가능

| 항목      | 내용                                         |
| ------- | ------------------------------------------ |
| 항목      | 내용                                         |
| 클래스     | AdWhaleMediationInterstitialAd             |
| 지원 네트워크 | AdMob, AdManager, Admize, Cauly, Levelplay |
| 노출      | show(from:)                                |

#### 2. 기본 구현 샘플코드

`AdWhaleMediationInterstitialAd` 클래스를 사용하여 전면 광고를 로드하고 표시하는 기본적인 구현 방법입니다.

{% tabs %}
{% tab title="Swift" %}
```swift
final class InterstitialViewController: UIViewController, AdWhaleMediationInterstitialDelegate {

    // ★ 광고 인스턴스는 앱이 강하게 보유해야 합니다. (SDK 는 delegate 를 weak 로 참조)
    private var interstitialAd: AdWhaleMediationInterstitialAd?

    override func viewDidLoad() {
        super.viewDidLoad()

        // 1. 인스턴스 생성 (placementUid)
        let ad = AdWhaleMediationInterstitialAd(placementUid: "발급받은 PLACEMENT_UID 값")

        // 2. 델리게이트 등록
        ad.delegate = self
        interstitialAd = ad

        // 3. 로드
        ad.loadAd()
    }

    @IBAction func onShowTapped() {
        // 4. 노출 (로드 완료 후)
        interstitialAd?.show(from: self)
    }

    // MARK: - AdWhaleMediationInterstitialDelegate

    func interstitial(_ ad: AdWhaleMediationInterstitialAd,
                      didLoadWith responseInfo: AdWhaleMediationResponseInfo) {
        // 로드 성공
    }

    func interstitial(_ ad: AdWhaleMediationInterstitialAd,
                      didFailToLoadWithError statusCode: Int, message: String) {
        // 로드 실패
    }

    func interstitial(_ ad: AdWhaleMediationInterstitialAd,
                      didFailToShowWithError statusCode: Int, message: String) {
        // 노출 실패
    }

    func interstitialDidShow(_ ad: AdWhaleMediationInterstitialAd) {
        // 노출됨
    }

    func interstitialDidClick(_ ad: AdWhaleMediationInterstitialAd) {
        // 클릭
    }

    func interstitialDidClose(_ ad: AdWhaleMediationInterstitialAd) {
        // 닫힘
    }

    deinit {
        // 5. 폐기
        interstitialAd?.destroy()
        interstitialAd = nil
    }
}
```
{% endtab %}

{% tab title="SwiftUI" %}
```swift
// 전면 광고는 노출 시 UIViewController 가 필요하므로
// 광고 인스턴스를 보유하는 ObservableObject 를 두고 최상단 ViewController 를 찾아 넘깁니다.
@MainActor
final class InterstitialAdController: NSObject, ObservableObject, AdWhaleMediationInterstitialDelegate {

    @Published var isLoaded = false

    // ★ 광고 인스턴스는 앱이 강하게 보유해야 합니다.
    private var interstitialAd: AdWhaleMediationInterstitialAd?

    func load() {
        // 1. 인스턴스 생성 (placementUid)
        let ad = AdWhaleMediationInterstitialAd(placementUid: "발급받은 PLACEMENT_UID 값")

        // 2. 델리게이트 등록
        ad.delegate = self
        interstitialAd = ad

        // 3. 로드
        ad.loadAd()
    }

    /// 4. 노출 (로드 완료 후)
    func show() {
        guard let top = Self.topViewController() else { return }
        interstitialAd?.show(from: top)
    }

    /// 5. 폐기
    func release() {
        interstitialAd?.destroy()
        interstitialAd = nil
    }

    // MARK: - AdWhaleMediationInterstitialDelegate

    func interstitial(_ ad: AdWhaleMediationInterstitialAd,
                      didLoadWith responseInfo: AdWhaleMediationResponseInfo) {
        isLoaded = true
    }

    func interstitial(_ ad: AdWhaleMediationInterstitialAd,
                      didFailToLoadWithError statusCode: Int, message: String) {
        isLoaded = false
    }

    func interstitial(_ ad: AdWhaleMediationInterstitialAd,
                      didFailToShowWithError statusCode: Int, message: String) { }

    func interstitialDidClose(_ ad: AdWhaleMediationInterstitialAd) {
        isLoaded = false
        load()   // 닫힘 후 다음 광고 미리 로드
    }

    /// `show(from:)` 에 넘길 현재 최상단 UIViewController
    static func topViewController() -> UIViewController? {
        let scene = UIApplication.shared.connectedScenes
            .compactMap { $0 as? UIWindowScene }
            .first { $0.activationState == .foregroundActive }
        let keyWindow = scene?.windows.first(where: { $0.isKeyWindow }) ?? scene?.windows.first
        var top = keyWindow?.rootViewController
        while let presented = top?.presentedViewController { top = presented }
        return top
    }
}

struct InterstitialScreen: View {
    @StateObject private var controller = InterstitialAdController()

    var body: some View {
        Button("전면 광고 노출") { controller.show() }
            .disabled(!controller.isLoaded)
            .onAppear { controller.load() }
            .onDisappear { controller.release() }
    }
}
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
@interface InterstitialViewController () <AdWhaleMediationInterstitialDelegate>
// ★ 광고 인스턴스는 앱이 강하게 보유해야 합니다.
@property (nonatomic, strong) AdWhaleMediationInterstitialAd *interstitialAd;
@end

@implementation InterstitialViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    // 1. 인스턴스 생성 (placementUid)
    self.interstitialAd = [[AdWhaleMediationInterstitialAd alloc]
                           initWithPlacementUid:@"발급받은 PLACEMENT_UID 값"];

    // 2. 델리게이트 등록
    [self.interstitialAd setAdWhaleMediationInterstitialDelegate:self];

    // 3. 로드
    [self.interstitialAd loadAd];
}

- (IBAction)onShowTapped:(id)sender {
    // 4. 노출 (로드 완료 후)
    [self.interstitialAd showFrom:self];
}

// 로드 성공
- (void)interstitial:(AdWhaleMediationInterstitialAd *)ad
         didLoadWith:(AdWhaleMediationResponseInfo *)responseInfo { }

// 로드 실패
- (void)interstitial:(AdWhaleMediationInterstitialAd *)ad
didFailToLoadWithError:(NSInteger)statusCode message:(NSString *)message { }

// 노출 실패
- (void)interstitial:(AdWhaleMediationInterstitialAd *)ad
didFailToShowWithError:(NSInteger)statusCode message:(NSString *)message { }

// 노출됨
- (void)interstitialDidShow:(AdWhaleMediationInterstitialAd *)ad { }

// 클릭
- (void)interstitialDidClick:(AdWhaleMediationInterstitialAd *)ad { }

// 닫힘
- (void)interstitialDidClose:(AdWhaleMediationInterstitialAd *)ad { }

- (void)dealloc {
    // 5. 폐기
    [self.interstitialAd destroy];
    self.interstitialAd = nil;
}

@end
```
{% endtab %}
{% endtabs %}

#### 3. API 설명

**AdWhaleMediationInterstitialAd 클래스 API 설명**

```swift
public init(placementUid: String)
```

| 파라미터 타입 | 파라미터 값                |
| ------- | --------------------- |
| 파라미터 타입 | 파라미터 값                |
| String  | placementUid 값(발급 필요) |

```swift
public weak var delegate: AdWhaleMediationInterstitialDelegate?   // 콜백 델리게이트 (Swift)
```

| 파라미터 타입                               | 파라미터 값                  |
| ------------------------------------- | ----------------------- |
| 파라미터 타입                               | 파라미터 값                  |
| AdWhaleMediationInterstitialDelegate  | 전면 미디에이션 광고 호출 콜백 델리게이트 |

```swift
public func loadAd()   // 미디에이션 전면 광고 로드
```

```swift
public func show(from viewController: UIViewController)   // 광고 로드 후 노출할 때 호출
```

| 파라미터 타입          | 파라미터 값                                             |
| ---------------- | -------------------------------------------------- |
| UIViewController | 노출 기준 ViewController (`view.window != nil` 상태여야 함) |

```swift
public func cancelAd()   // 로드된 광고를 노출하지 않고 취소
```

```swift
public func destroy()   // deinit 시 호출 혹은 더 이상 광고를 요청하지 않고 싶을 때 호출
```

**AdWhaleMediationInterstitialDelegate 프로토콜 API 설명**

| 델리게이트 메서드                                        | 호출 시점 |
| ------------------------------------------------ | ----- |
| 델리게이트 메서드                                        | 호출 시점 |
| interstitial(\_:didLoadWith:)                    | 로드 성공 |
| interstitial(\_:didFailToLoadWithError:message:) | 로드 실패 |
| interstitialDidShow(\_:)                         | 노출 성공 |
| interstitial(\_:didFailToShowWithError:message:) | 노출 실패 |
| interstitialDidClick(\_:)                        | 클릭    |
| interstitialDidClose(\_:)                        | 닫힘    |

```swift
func interstitial(_ ad: AdWhaleMediationInterstitialAd,
                  didLoadWith responseInfo: AdWhaleMediationResponseInfo)   // 광고요청 성공 시
```

| 파라미터 타입                       | 파라미터 값                                       |
| ----------------------------- | -------------------------------------------- |
| 파라미터 타입                       | 파라미터 값                                       |
| AdWhaleMediationResponseInfo  | 낙찰된 광고의 응답 정보 (`adNetworkName`, `revenue` 등) |

```swift
func interstitial(_ ad: AdWhaleMediationInterstitialAd,
                  didFailToLoadWithError statusCode: Int, message: String)   // 광고요청 실패 시
```

| 파라미터 타입  | 파라미터 값                                                                                                                                       |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `Int`    | <p><code>200</code> = 연동 오류(placementUid 오설정 등)<br>또는<br><code>300</code> = 광고를 채우지 못함(워터폴 모두 소진)</p>                                        |
| `String` | <p><code>Internal error occurred...</code> = 연동 오류 메시지<br>또는<br><code>Mediation network error occurred...</code> = 광고를 채우지 못함(워터폴 모두 소진)</p> |

```swift
@objc optional func interstitialDidShow(_ ad: AdWhaleMediationInterstitialAd)   // 광고 노출 후
```

```swift
@objc optional func interstitial(_ ad: AdWhaleMediationInterstitialAd,
                                 didFailToShowWithError statusCode: Int, message: String)   // 광고 노출 실패 시
```

| 파라미터 타입  | 파라미터 값                                                                                                                                                                     |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Int`    | `200` = 연동 오류 (로드 전 `show()` 호출, window 계층에 없는 ViewController 전달 등)                                                                                                        |
| `String` | <p><code>Ad not loaded.</code> = 아직 로드되지 않은 상태에서 <code>show()</code> 호출<br>또는<br><code>ViewController is not in window hierarchy.</code> = dismiss 된 ViewController 전달</p> |

```swift
@objc optional func interstitialDidClick(_ ad: AdWhaleMediationInterstitialAd)   // 광고 클릭 시
```

```swift
@objc optional func interstitialDidClose(_ ad: AdWhaleMediationInterstitialAd)   // 광고 닫기 시
```

#### 4. 옵션 설정

{% tabs %}
{% tab title="Swift" %}
```swift
interstitialAd.region = "서울시 강남구"                                // 지역 타게팅 전용 API(옵션)
interstitialAd.setGeocoder(latitude: 37.5665, longitude: 126.9780)   // 지역 타게팅 전용 API(옵션)
```

{% hint style="info" %}
AdWhale SDK 는 Cauly 네트워크를 지원하며, 광고 지역 타게팅을 위해 지역정보(`region`, `setGeocoder`)를 선택적으로 입력받고 있습니다.
{% endhint %}

```swift
// 레벨플레이 placement name 연동 전용 API (옵션).
// placementName 값은 LevelPlay 콘솔에서 설정한 이름
interstitialAd.placementName = "app_open_main"
```

{% hint style="warning" %}
AdWhale SDK 는 LevelPlay 네트워크를 지원하며, 각 Placement 별로 광고 노출을 구분하고자 할 때 `placementName` 으로 설정할 수 있습니다.\
설정하지 않으면 기본 Placement(Default Placement)가 사용됩니다.
{% endhint %}
{% endtab %}

{% tab title="SwiftUI" %}
```swift
interstitialAd.region = "서울시 강남구"                                // 지역 타게팅 전용 API(옵션)
interstitialAd.setGeocoder(latitude: 37.5665, longitude: 126.9780)   // 지역 타게팅 전용 API(옵션)
```

{% hint style="info" %}
AdWhale SDK 는 Cauly 네트워크를 지원하며, 광고 지역 타게팅을 위해 지역정보(`region`, `setGeocoder`)를 선택적으로 입력받고 있습니다.
{% endhint %}

```swift
// 레벨플레이 placement name 연동 전용 API (옵션).
// placementName 값은 LevelPlay 콘솔에서 설정한 이름
interstitialAd.placementName = "app_open_main"
```

{% hint style="warning" %}
AdWhale SDK 는 LevelPlay 네트워크를 지원하며, 각 Placement 별로 광고 노출을 구분하고자 할 때 `placementName` 으로 설정할 수 있습니다.\
설정하지 않으면 기본 Placement(Default Placement)가 사용됩니다.
{% endhint %}
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
interstitialAd.region = @"서울시 강남구";                          // 지역 타게팅 전용 API(옵션)
[interstitialAd setGeocoderWithLatitude:37.5665 longitude:126.9780];  // 지역 타게팅 전용 API(옵션)
```

{% hint style="info" %}
AdWhale SDK 는 Cauly 네트워크를 지원하며, 광고 지역 타게팅을 위해 지역정보(`region`, `setGeocoder`)를 선택적으로 입력받고 있습니다.
{% endhint %}

```objective-c
// 레벨플레이 placement name 연동 전용 API (옵션).
// placementName 값은 LevelPlay 콘솔에서 설정한 이름
interstitialAd.placementName = @"app_open_main";                   // 레벨플레이 placement name (옵션)
```

{% hint style="warning" %}
AdWhale SDK 는 LevelPlay 네트워크를 지원하며, 각 Placement 별로 광고 노출을 구분하고자 할 때 `placementName` 으로 설정할 수 있습니다.\
설정하지 않으면 기본 Placement(Default Placement)가 사용됩니다.
{% endhint %}
{% endtab %}
{% endtabs %}

5.전면 광고 샘플코드

{% tabs %}
{% tab title="Swift" %}
```swift
import UIKit
import AdWhaleSDK

final class InterstitialViewController: UIViewController, AdWhaleMediationInterstitialDelegate {

    private var interstitialAd: AdWhaleMediationInterstitialAd?

    override func viewDidLoad() {
        super.viewDidLoad()
        loadInterstitial()
    }

    private func loadInterstitial() {
        // 1. 인스턴스 생성 (placementUid)
        let ad = AdWhaleMediationInterstitialAd(placementUid: "발급받은 PLACEMENT_UID 값")

        // 2. 옵션 설정 (선택)
        ad.region = "서울시 강남구"
        ad.setGeocoder(latitude: 37.5665, longitude: 126.9780)
        ad.placementName = "interstitial_main"

        // 3. 델리게이트 등록
        ad.delegate = self
        interstitialAd = ad

        // 4. 로드
        ad.loadAd()
    }

    @IBAction func onShowTapped() {
        // 5. 노출
        interstitialAd?.show(from: self)
    }

    // MARK: - AdWhaleMediationInterstitialDelegate

    func interstitial(_ ad: AdWhaleMediationInterstitialAd,
                      didLoadWith responseInfo: AdWhaleMediationResponseInfo) {
        print("전면 로드 성공 — network=\(responseInfo.adNetworkName ?? "-")")
    }

    func interstitial(_ ad: AdWhaleMediationInterstitialAd,
                      didFailToLoadWithError statusCode: Int, message: String) {
        // 200: 연동 오류(코드 수정 필요) / 300: 광고 미충족(정상)
        print("전면 로드 실패 (\(statusCode)): \(message)")
    }

    func interstitialDidShow(_ ad: AdWhaleMediationInterstitialAd) {
        print("전면 노출됨")
    }

    func interstitial(_ ad: AdWhaleMediationInterstitialAd,
                      didFailToShowWithError statusCode: Int, message: String) {
        print("전면 노출 실패 (\(statusCode)): \(message)")
    }

    func interstitialDidClick(_ ad: AdWhaleMediationInterstitialAd) {
        print("전면 클릭")
    }

    func interstitialDidClose(_ ad: AdWhaleMediationInterstitialAd) {
        // 닫힘 후 다음 광고 미리 로드
        loadInterstitial()
    }

    deinit {
        // 6. 폐기
        interstitialAd?.destroy()
        interstitialAd = nil
    }
}
```
{% endtab %}

{% tab title="SwiftUI" %}
```swift
import SwiftUI
import UIKit
import AdWhaleSDK

@MainActor
final class InterstitialAdController: NSObject, ObservableObject, AdWhaleMediationInterstitialDelegate {

    @Published var isLoaded = false

    private var interstitialAd: AdWhaleMediationInterstitialAd?

    func load() {
        // 1. 인스턴스 생성 (placementUid)
        let ad = AdWhaleMediationInterstitialAd(placementUid: "발급받은 PLACEMENT_UID 값")

        // 2. 옵션 설정 (선택)
        ad.placementName = "interstitial_main"

        // 3. 델리게이트 등록
        ad.delegate = self
        interstitialAd = ad

        // 4. 로드
        ad.loadAd()
    }

    /// 5. 노출
    func show() {
        guard let top = Self.topViewController() else { return }
        interstitialAd?.show(from: top)
    }

    /// 6. 폐기
    func release() {
        interstitialAd?.destroy()
        interstitialAd = nil
    }

    // MARK: - AdWhaleMediationInterstitialDelegate

    func interstitial(_ ad: AdWhaleMediationInterstitialAd,
                      didLoadWith responseInfo: AdWhaleMediationResponseInfo) {
        print("전면 로드 성공 — network=\(responseInfo.adNetworkName ?? "-")")
        isLoaded = true
    }

    func interstitial(_ ad: AdWhaleMediationInterstitialAd,
                      didFailToLoadWithError statusCode: Int, message: String) {
        print("전면 로드 실패 (\(statusCode)): \(message)")
        isLoaded = false
    }

    func interstitialDidShow(_ ad: AdWhaleMediationInterstitialAd) {
        isLoaded = false
    }

    func interstitial(_ ad: AdWhaleMediationInterstitialAd,
                      didFailToShowWithError statusCode: Int, message: String) {
        print("전면 노출 실패 (\(statusCode)): \(message)")
    }

    func interstitialDidClose(_ ad: AdWhaleMediationInterstitialAd) {
        load()   // 닫힘 후 다음 광고 미리 로드
    }

    static func topViewController() -> UIViewController? {
        let scene = UIApplication.shared.connectedScenes
            .compactMap { $0 as? UIWindowScene }
            .first { $0.activationState == .foregroundActive }
        let keyWindow = scene?.windows.first(where: { $0.isKeyWindow }) ?? scene?.windows.first
        var top = keyWindow?.rootViewController
        while let presented = top?.presentedViewController { top = presented }
        return top
    }
}

struct InterstitialScreen: View {
    @StateObject private var controller = InterstitialAdController()

    var body: some View {
        VStack(spacing: 16) {
            Button("전면 광고 노출") { controller.show() }
                .disabled(!controller.isLoaded)
        }
        .onAppear { controller.load() }
        .onDisappear { controller.release() }
    }
}
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
#import <AdWhaleSDK/AdWhaleSDK-Swift.h>

@interface InterstitialViewController () <AdWhaleMediationInterstitialDelegate>
@property (nonatomic, strong) AdWhaleMediationInterstitialAd *interstitialAd;
@end

@implementation InterstitialViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    [self loadInterstitial];
}

- (void)loadInterstitial {
    // 1. 인스턴스 생성 (placementUid)
    self.interstitialAd = [[AdWhaleMediationInterstitialAd alloc]
                           initWithPlacementUid:@"발급받은 PLACEMENT_UID 값"];

    // 2. 옵션 설정 (선택)
    self.interstitialAd.region = @"서울시 강남구";
    [self.interstitialAd setGeocoderWithLatitude:37.5665 longitude:126.9780];
    self.interstitialAd.placementName = @"interstitial_main";

    // 3. 델리게이트 등록
    [self.interstitialAd setAdWhaleMediationInterstitialDelegate:self];

    // 4. 로드
    [self.interstitialAd loadAd];
}

- (IBAction)onShowTapped:(id)sender {
    // 5. 노출
    [self.interstitialAd showFrom:self];
}

- (void)interstitial:(AdWhaleMediationInterstitialAd *)ad
         didLoadWith:(AdWhaleMediationResponseInfo *)responseInfo {
    NSLog(@"전면 로드 성공");
}

- (void)interstitial:(AdWhaleMediationInterstitialAd *)ad
didFailToLoadWithError:(NSInteger)statusCode message:(NSString *)message {
    NSLog(@"전면 로드 실패 (%ld): %@", (long)statusCode, message);
}

- (void)interstitial:(AdWhaleMediationInterstitialAd *)ad
didFailToShowWithError:(NSInteger)statusCode message:(NSString *)message {
    NSLog(@"전면 노출 실패 (%ld): %@", (long)statusCode, message);
}

- (void)interstitialDidClose:(AdWhaleMediationInterstitialAd *)ad {
    // 닫힘 후 다음 광고 미리 로드
    [self loadInterstitial];
}

- (void)dealloc {
    // 6. 폐기
    [self.interstitialAd destroy];
    self.interstitialAd = nil;
}

@end
```
{% endtab %}
{% endtabs %}

#### 6. 주의사항

**광고 로드 타이밍**

* `loadAd()` 는 SDK 초기화 완료(`initialize` 완료 콜백) 이후에 호출하는 것을 권장합니다.
* 광고는 로드가 완료된 후에만 노출할 수 있습니다. `interstitial(_:didLoadWith:)` 이후에만 `show(from:)` 을 호출하세요.

**광고 노출 조건**

* 화면 전환, 스테이지 종료 등 자연스러운 시점에 노출하는 것을 권장합니다.
* 중복 노출: 이미 노출 중이거나 로드 중일 때 `show(from:)` 은 무시될 수 있으므로, 콜백 상태를 활용해 제어하세요.
* `show(from:)` 에는 `view.window != nil` 상태의 ViewController 를 넘겨야 합니다.

**1회성 광고**

* 전면 광고는 **1회성**입니다. 한 번 노출한 뒤 다시 보여주려면 `loadAd()` 를 다시 호출해야 합니다.
* `interstitialDidClose` 에서 다음 광고를 미리 로드하는 패턴을 권장합니다.

**인스턴스 보유**

* SDK 는 델리게이트를 `weak` 로 참조합니다. 광고 인스턴스를 앱이 강하게 보유하지 않으면 콜백이 도착하기 전에 해제되어 “광고도 안 나오고 콜백도 없는” 상태가 됩니다.

**리소스 해제**

* `deinit` 또는 화면 종료 시 반드시 `destroy()` 를 호출하세요.

**에러 처리**

* `didFailToLoadWithError` 와 `didFailToShowWithError` 이벤트에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.

**테스트**

* 개발 환경에서는 테스트용 placement UID 를 사용하세요.
* 실제 배포 전에 다양한 시나리오에서 테스트하세요.

{% hint style="danger" %}
`show(from:)` 에는 `view.window != nil` 상태의 ViewController 를 넘기세요.\
dismiss 된 ViewController 를 넘기면 **200 (`ViewController is not in window hierarchy.`)** 이 통지됩니다.
{% endhint %}

{% hint style="warning" %}
로드되지 않은 상태에서 `show(from:)` 을 호출하면 **200 (`Ad not loaded.`)** 이 통지됩니다.\
로드 성공 콜백을 받은 뒤 노출하세요.
{% endhint %}

{% hint style="info" %}
전면 광고는 **1회성**입니다. 한 번 노출한 뒤 다시 보여주려면 `loadAd()` 를 다시 호출해야 합니다.\
`interstitialDidClose` 에서 다음 광고를 미리 로드하는 패턴을 권장합니다.
{% endhint %}
