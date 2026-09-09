# 보상형

{% hint style="info" %}
보상형 광고는 사용자가 광고를 끝까지 시청하면 보상(아이템, 포인트 등)을 지급하는 전체 화면 광고입니다. 이 문서는 iOS 프로젝트에서 AdWhale Mediation SDK 를 사용하여 보상형 광고를 연동하는 방법을 설명합니다.
{% endhint %}

**1. 주요특징**

* 시청 완료 시 보상을 지급하는 전체 화면 광고
* 로드 · 전체화면 · 보상 지급 델리게이트가 **역할별로 분리**되어 있음
* 서버 사이드 검증(SSV) 지원 — `setUserId()`, `setCustomData()`
* 다양한 옵션 설정 지원 (`placementName`, `region`, `setGeocoder`)
* 로드 · 노출 · 클릭 · 닫힘 · 보상 지급 등 이벤트 기반 델리게이트 콜백 시스템으로 광고 상태 추적 가능

| 항목      | 내용                                  |
| ------- | ----------------------------------- |
| 클래스     | `AdWhaleMediationRewardAd`          |
| 지원 네트워크 | AdMob, AdManager, Admize, LevelPlay |
| 노출      | `show(from:rewardDelegate:)`        |

보상형은 델리게이트가 **세 개**로 나뉩니다.

| Delegate                               | 역할           | 설정 방법                                 |
| -------------------------------------- | ------------ | ------------------------------------- |
| `AdWhaleMediationRewardAdLoadDelegate` | 로드 결과        | `loadAd(loadDelegate:)` **파라미터**      |
| `AdWhaleRewardedFullScreenDelegate`    | 노출 · 클릭 · 닫힘 | `fullScreenDelegate` 프로퍼티             |
| `AdWhaleUserEarnedRewardDelegate`      | 보상 지급        | `show(from:rewardDelegate:)` **파라미터** |

**2. 기본 구현 샘플코드**

`AdWhaleMediationRewardAd` 클래스를 사용하여 보상형 광고를 로드하고 표시하는 기본적인 구현 방법입니다.

{% tabs %}
{% tab title="Swift" %}
```swift
final class RewardViewController: UIViewController,
                                  AdWhaleMediationRewardAdLoadDelegate,
                                  AdWhaleRewardedFullScreenDelegate,
                                  AdWhaleUserEarnedRewardDelegate {

    // ★ 광고 인스턴스는 앱이 강하게 보유해야 합니다. (SDK 는 delegate 를 weak 로 참조)
    private var rewardAd: AdWhaleMediationRewardAd?

    override func viewDidLoad() {
        super.viewDidLoad()

        // 1. 인스턴스 생성 (placementUid)
        let ad = AdWhaleMediationRewardAd(placementUid: "발급받은 PLACEMENT_UID 값")

        // 2. 전체화면 델리게이트 등록 (노출 · 클릭 · 닫힘)
        ad.fullScreenDelegate = self
        rewardAd = ad

        // 3. 로드 — 로드 델리게이트는 파라미터로 전달 (필수)
        ad.loadAd(loadDelegate: self)
    }

    @IBAction func onShowTapped() {
        // 4. 노출 — 보상 델리게이트는 파라미터로 전달 (필수)
        rewardAd?.show(from: self, rewardDelegate: self)
    }

    // MARK: - AdWhaleMediationRewardAdLoadDelegate

    func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                    didLoadWith responseInfo: AdWhaleMediationResponseInfo) {
        // 로드 성공
    }

    func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                    didFailToLoadWithError statusCode: Int, message: String) {
        // 로드 실패
    }

    // MARK: - AdWhaleRewardedFullScreenDelegate

    func rewardedAdDidShow(_ ad: AdWhaleMediationRewardAd) { }      // 노출됨
    func rewardedAdDidClick(_ ad: AdWhaleMediationRewardAd) { }     // 클릭
    func rewardedAdDidDismiss(_ ad: AdWhaleMediationRewardAd) { }   // 닫힘

    func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                    didFailToShowWithError statusCode: Int, message: String) {
        // 노출 실패
    }

    // MARK: - AdWhaleUserEarnedRewardDelegate

    func userDidEarnReward(_ rewardItem: AdWhaleMediationRewardItem) {
        // 보상 지급 — 보상 처리는 반드시 이 콜백에서만
    }

    deinit {
        // 5. 폐기
        rewardAd?.destroy()
        rewardAd = nil
    }
}
```
{% endtab %}

{% tab title="SwiftUI" %}
```swift
// 보상형 광고는 노출 시 UIViewController 가 필요하므로
// 광고 인스턴스를 보유하는 ObservableObject 를 두고 최상단 ViewController 를 찾아 넘깁니다.
@MainActor
final class RewardAdController: NSObject, ObservableObject,
                                AdWhaleMediationRewardAdLoadDelegate,
                                AdWhaleRewardedFullScreenDelegate,
                                AdWhaleUserEarnedRewardDelegate {

    @Published var isLoaded = false
    @Published var earnedReward: String?

    // ★ 광고 인스턴스는 앱이 강하게 보유해야 합니다.
    private var rewardAd: AdWhaleMediationRewardAd?

    func load() {
        // 1. 인스턴스 생성 (placementUid)
        let ad = AdWhaleMediationRewardAd(placementUid: "발급받은 PLACEMENT_UID 값")

        // 2. 전체화면 델리게이트 등록
        ad.fullScreenDelegate = self
        rewardAd = ad

        // 3. 로드 (로드 델리게이트는 파라미터)
        ad.loadAd(loadDelegate: self)
    }

    /// 4. 노출 (보상 델리게이트는 파라미터)
    func show() {
        guard let top = Self.topViewController() else { return }
        rewardAd?.show(from: top, rewardDelegate: self)
    }

    /// 5. 폐기
    func release() {
        rewardAd?.destroy()
        rewardAd = nil
    }

    // MARK: - 로드

    func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                    didLoadWith responseInfo: AdWhaleMediationResponseInfo) {
        isLoaded = true
    }

    func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                    didFailToLoadWithError statusCode: Int, message: String) {
        isLoaded = false
    }

    // MARK: - 전체 화면

    func rewardedAdDidDismiss(_ ad: AdWhaleMediationRewardAd) {
        isLoaded = false
        load()   // 다음 광고 미리 로드
    }

    func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                    didFailToShowWithError statusCode: Int, message: String) { }

    // MARK: - 보상

    func userDidEarnReward(_ rewardItem: AdWhaleMediationRewardItem) {
        earnedReward = "\(rewardItem.rewardType) x \(rewardItem.rewardAmount)"
    }

    /// `show(from:rewardDelegate:)` 에 넘길 현재 최상단 UIViewController
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

struct RewardScreen: View {
    @StateObject private var controller = RewardAdController()

    var body: some View {
        VStack(spacing: 16) {
            Button("보상형 광고 보기") { controller.show() }
                .disabled(!controller.isLoaded)

            if let reward = controller.earnedReward {
                Text("보상 지급: \(reward)")
            }
        }
        .onAppear { controller.load() }
        .onDisappear { controller.release() }
    }
}
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
@interface RewardViewController () <AdWhaleMediationRewardAdLoadDelegate,
                                    AdWhaleRewardedFullScreenDelegate,
                                    AdWhaleUserEarnedRewardDelegate>
// ★ 광고 인스턴스는 앱이 강하게 보유해야 합니다.
@property (nonatomic, strong) AdWhaleMediationRewardAd *rewardAd;
@end

@implementation RewardViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    // 1. 인스턴스 생성 (placementUid)
    self.rewardAd = [[AdWhaleMediationRewardAd alloc]
                     initWithPlacementUid:@"발급받은 PLACEMENT_UID 값"];

    // 2. 전체화면 델리게이트 등록
    [self.rewardAd setAdWhaleRewardedFullScreenDelegate:self];

    // 3. 로드 (로드 델리게이트는 파라미터)
    [self.rewardAd loadAdWithLoadDelegate:self];
}

- (IBAction)onShowTapped:(id)sender {
    // 4. 노출 (보상 델리게이트는 파라미터)
    [self.rewardAd showFrom:self rewardDelegate:self];
}

// 로드 성공
- (void)rewardedAd:(AdWhaleMediationRewardAd *)ad
       didLoadWith:(AdWhaleMediationResponseInfo *)responseInfo { }

// 로드 실패
- (void)rewardedAd:(AdWhaleMediationRewardAd *)ad
didFailToLoadWithError:(NSInteger)statusCode message:(NSString *)message { }

// 노출됨 / 클릭 / 닫힘
- (void)rewardedAdDidShow:(AdWhaleMediationRewardAd *)ad { }
- (void)rewardedAdDidClick:(AdWhaleMediationRewardAd *)ad { }
- (void)rewardedAdDidDismiss:(AdWhaleMediationRewardAd *)ad { }

// 노출 실패
- (void)rewardedAd:(AdWhaleMediationRewardAd *)ad
didFailToShowWithError:(NSInteger)statusCode message:(NSString *)message { }

// 보상 지급
- (void)userDidEarnReward:(AdWhaleMediationRewardItem *)rewardItem { }

- (void)dealloc {
    // 5. 폐기
    [self.rewardAd destroy];
    self.rewardAd = nil;
}

@end
```
{% endtab %}
{% endtabs %}

**3. API 설명**

**AdWhaleMediationRewardAd 클래스 API 설명**

```swift
public init(placementUid: String)
```

| 파라미터 타입  | 파라미터 값                |
| -------- | --------------------- |
| `String` | placementUid 값(발급 필요) |

```swift
public weak var fullScreenDelegate: AdWhaleRewardedFullScreenDelegate?   // 전체화면 델리게이트 (Swift)
public func setAdWhaleRewardedFullScreenDelegate(_ delegate: AdWhaleRewardedFullScreenDelegate?)   // Objective-C
```

| 파라미터 타입                             | 파라미터 값                      |
| ----------------------------------- | --------------------------- |
| `AdWhaleRewardedFullScreenDelegate` | 노출 · 클릭 · 닫힘 콜백 델리게이트 (옵셔널) |

```swift
public func loadAd(loadDelegate: AdWhaleMediationRewardAdLoadDelegate)   // 미디에이션 보상형 광고 로드
```

| 파라미터 타입                                | 파라미터 값                                  |
| -------------------------------------- | --------------------------------------- |
| `AdWhaleMediationRewardAdLoadDelegate` | 로드 결과 콜백 델리게이트 (**필수** — nil 을 넘길 수 없음) |

```swift
public func show(from viewController: UIViewController,
                 rewardDelegate: AdWhaleUserEarnedRewardDelegate)   // 광고 로드 후 노출할 때 호출
```

| 파라미터 타입                           | 파라미터 값                                             |
| --------------------------------- | -------------------------------------------------- |
| `UIViewController`                | 노출 기준 ViewController (`view.window != nil` 상태여야 함) |
| `AdWhaleUserEarnedRewardDelegate` | 보상 지급 콜백 델리게이트 (**필수** — nil 을 넘길 수 없음)            |

```swift
public func setUserId(_ userId: String)   // SSV 전용 — 서버 사이드 검증 사용자 식별자
```

| 파라미터 타입  | 파라미터 값                    |
| -------- | ------------------------- |
| `String` | SSV 콜백의 `user_id` 로 전달될 값 |

```swift
public func setCustomData(_ customData: [String: String])   // SSV 전용 — 부가 데이터
```

| 파라미터 타입            | 파라미터 값                                        |
| ------------------ | --------------------------------------------- |
| `[String: String]` | JSON 문자열로 직렬화되어 SSV 콜백의 `custom_data` 로 전달될 값 |

```swift
public func destroy()   // deinit 시 호출 혹은 더 이상 광고를 요청하지 않고 싶을 때 호출
```

**AdWhaleMediationRewardAdLoadDelegate 프로토콜 API 설명**

| 델리게이트 메서드                                       | 호출 시점 |
| ----------------------------------------------- | ----- |
| `rewardedAd(_:didLoadWith:)`                    | 로드 성공 |
| `rewardedAd(_:didFailToLoadWithError:message:)` | 로드 실패 |

```swift
func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                didLoadWith responseInfo: AdWhaleMediationResponseInfo)   // 광고요청 성공 시
```

| 파라미터 타입                        | 파라미터 값                                       |
| ------------------------------ | -------------------------------------------- |
| `AdWhaleMediationResponseInfo` | 낙찰된 광고의 응답 정보 (`adNetworkName`, `revenue` 등) |

```swift
func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                didFailToLoadWithError statusCode: Int, message: String)   // 광고요청 실패 시
```

| 파라미터 타입  | 파라미터 값                                                                                                                                       |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `Int`    | <p><code>200</code> = 연동 오류(placementUid 오설정 등)<br>또는<br><code>300</code> = 광고를 채우지 못함(워터폴 모두 소진)</p>                                        |
| `String` | <p><code>Internal error occurred...</code> = 연동 오류 메시지<br>또는<br><code>Mediation network error occurred...</code> = 광고를 채우지 못함(워터폴 모두 소진)</p> |

**AdWhaleRewardedFullScreenDelegate 프로토콜 API 설명** (전부 optional)

| 델리게이트 메서드                                       | 호출 시점 |
| ----------------------------------------------- | ----- |
| `rewardedAdDidShow(_:)`                         | 노출 성공 |
| `rewardedAd(_:didFailToShowWithError:message:)` | 노출 실패 |
| `rewardedAdDidClick(_:)`                        | 클릭    |
| `rewardedAdDidDismiss(_:)`                      | 닫힘    |

```swift
@objc optional func rewardedAdDidShow(_ ad: AdWhaleMediationRewardAd)      // 광고 노출 후
@objc optional func rewardedAdDidClick(_ ad: AdWhaleMediationRewardAd)     // 광고 클릭 시
@objc optional func rewardedAdDidDismiss(_ ad: AdWhaleMediationRewardAd)   // 광고 닫기 시
```

```swift
@objc optional func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                               didFailToShowWithError statusCode: Int, message: String)   // 광고 노출 실패 시
```

| 파라미터 타입  | 파라미터 값                                                                                                                                                                     |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Int`    | `200` = 연동 오류 (로드 전 `show()` 호출, window 계층에 없는 ViewController 전달 등)                                                                                                        |
| `String` | <p><code>Ad not loaded.</code> = 아직 로드되지 않은 상태에서 <code>show()</code> 호출<br>또는<br><code>ViewController is not in window hierarchy.</code> = dismiss 된 ViewController 전달</p> |

**AdWhaleUserEarnedRewardDelegate 프로토콜 API 설명**

| 델리게이트 메서드               | 호출 시점       |
| ----------------------- | ----------- |
| `userDidEarnReward(_:)` | 보상 지급 조건 충족 |

```swift
func userDidEarnReward(_ rewardItem: AdWhaleMediationRewardItem)   // 보상 지급 시
```

| 파라미터 타입                      | 파라미터 값                                                |
| ---------------------------- | ----------------------------------------------------- |
| `AdWhaleMediationRewardItem` | 보상 정보 — `rewardType`(`String`), `rewardAmount`(`Int`) |

**4. 옵션 설정**

{% tabs %}
{% tab title="Swift" %}
```swift
rewardAd.region = "서울시 강남구"                                // 지역 타게팅 전용 API(옵션)
rewardAd.setGeocoder(latitude: 37.5665, longitude: 126.9780)   // 지역 타게팅 전용 API(옵션)
```

{% hint style="info" %}
AdWhale SDK 는 Cauly 네트워크를 지원하며, 광고 지역 타게팅을 위해 지역정보(`region`, `setGeocoder`)를 선택적으로 입력받고 있습니다.
{% endhint %}

```swift
// 레벨플레이 placement name 연동 전용 API (옵션).
// placementName 값은 LevelPlay 콘솔에서 설정한 이름
rewardAd.placementName = "reward_main"
```

{% hint style="warning" %}
LevelPlay 콘솔에서 설정한 Placement 이름을 지정하면, 해당 Placement 에 설정된 **보상 금액, 노출 제한 등의 설정이 적용된 광고**가 노출됩니다. 설정하지 않으면 기본 Placement(Default Placement)가 사용됩니다.
{% endhint %}

```swift
rewardAd.destroy()   // 리소스 해제
```
{% endtab %}

{% tab title="SwiftUI" %}
```swift
// 광고 인스턴스를 보유한 ObservableObject 안에서 동일하게 설정합니다.
rewardAd.region = "서울시 강남구"                                // 지역 타게팅 전용 API(옵션)
rewardAd.setGeocoder(latitude: 37.5665, longitude: 126.9780)   // 지역 타게팅 전용 API(옵션)
rewardAd.placementName = "reward_main"                         // 레벨플레이 placement name (옵션)
rewardAd.destroy()                                             // 리소스 해제
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
rewardAd.region = @"서울시 강남구";                              // 지역 타게팅 전용 API(옵션)
[rewardAd setGeocoderWithLatitude:37.5665 longitude:126.9780];   // 지역 타게팅 전용 API(옵션)
rewardAd.placementName = @"reward_main";                         // 레벨플레이 placement name (옵션)
[rewardAd destroy];                                              // 리소스 해제
```
{% endtab %}
{% endtabs %}

| 프로퍼티 / 메서드                            | 설명                                    |
| ------------------------------------- | ------------------------------------- |
| `region`                              | 지역 타겟팅 (Cauly 전용)                     |
| `placementName`                       | LevelPlay placement 이름 (LevelPlay 전용) |
| `setGeocoder(latitude:longitude:)`    | 좌표 타겟팅 (Cauly 전용)                     |
| `setUserId(_:)` / `setCustomData(_:)` | SSV(서버 사이드 검증) 전용                     |
| `destroy()`                           | 리소스 해제                                |

**5. 보상형 SSV(서버 사이드 검증)**

서버에서 보상 지급을 검증할 때 사용합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
rewardAd.setUserId("user-1234")
rewardAd.setCustomData(["orderId": "A-99", "stage": "3"])
```
{% endtab %}

{% tab title="SwiftUI" %}
```swift
// 광고 인스턴스를 보유한 ObservableObject 안에서 동일하게 설정합니다.
rewardAd.setUserId("user-1234")
rewardAd.setCustomData(["orderId": "A-99", "stage": "3"])
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
[rewardAd setUserId:@"user-1234"];
[rewardAd setCustomData:@{@"orderId": @"A-99", @"stage": @"3"}];
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
**AdMob 전용 기능입니다.** 로드된 네트워크가 AdMob 인 경우에만 SSV 콜백에 반영되며, 그 외 네트워크에서는 무시됩니다. `customData` 는 JSON 문자열로 직렬화되어 SSV 콜백의 `custom_data` 로 전달됩니다.
{% endhint %}

{% hint style="warning" %}
값은 객체 생성 이후 **`show()` 직전까지** 언제든 설정할 수 있고, 여러 번 호출하면 마지막 값이 적용됩니다. `show()` **이후** 호출은 반영되지 않습니다.
{% endhint %}

**6. 보상형 광고 샘플코드**

다음은 iOS 에서 보상형 광고를 구현하는 완전한 예시입니다.

{% tabs %}
{% tab title="Swift" %}
```swift
import UIKit
import AdWhaleSDK

final class RewardViewController: UIViewController {

    private var rewardAd: AdWhaleMediationRewardAd?

    override func viewDidLoad() {
        super.viewDidLoad()
        loadReward()
    }

    private func loadReward() {
        // 1. 인스턴스 생성 (placementUid)
        let ad = AdWhaleMediationRewardAd(placementUid: "발급받은 PLACEMENT_UID 값")

        // 2. 옵션 설정 (선택)
        ad.region = "서울시 강남구"
        ad.setGeocoder(latitude: 37.5665, longitude: 126.9780)
        ad.placementName = "reward_main"

        // 3. SSV 설정 (선택, AdMob 전용)
        ad.setUserId("user-1234")
        ad.setCustomData(["orderId": "A-99"])

        // 4. 전체화면 델리게이트 등록
        ad.fullScreenDelegate = self
        rewardAd = ad

        // 5. 로드
        ad.loadAd(loadDelegate: self)
    }

    @IBAction func onShowTapped() {
        // 6. 노출
        rewardAd?.show(from: self, rewardDelegate: self)
    }

    deinit {
        // 7. 폐기
        rewardAd?.destroy()
        rewardAd = nil
    }
}

// MARK: - 로드

extension RewardViewController: AdWhaleMediationRewardAdLoadDelegate {
    func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                    didLoadWith responseInfo: AdWhaleMediationResponseInfo) {
        print("보상형 로드 성공 — network=\(responseInfo.adNetworkName ?? "-")")
    }

    func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                    didFailToLoadWithError statusCode: Int, message: String) {
        print("보상형 로드 실패 (\(statusCode)): \(message)")
    }
}

// MARK: - 전체 화면

extension RewardViewController: AdWhaleRewardedFullScreenDelegate {
    func rewardedAdDidShow(_ ad: AdWhaleMediationRewardAd) {
        print("보상형 노출됨")
    }

    func rewardedAdDidClick(_ ad: AdWhaleMediationRewardAd) {
        print("보상형 클릭")
    }

    func rewardedAdDidDismiss(_ ad: AdWhaleMediationRewardAd) {
        loadReward()   // 다음 광고 미리 로드
    }

    func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                    didFailToShowWithError statusCode: Int, message: String) {
        print("보상형 노출 실패 (\(statusCode)): \(message)")
    }
}

// MARK: - 보상

extension RewardViewController: AdWhaleUserEarnedRewardDelegate {
    func userDidEarnReward(_ rewardItem: AdWhaleMediationRewardItem) {
        print("보상 지급: \(rewardItem.rewardType) x \(rewardItem.rewardAmount)")
        // 서버에 보상 반영
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
final class RewardAdController: NSObject, ObservableObject,
                                AdWhaleMediationRewardAdLoadDelegate,
                                AdWhaleRewardedFullScreenDelegate,
                                AdWhaleUserEarnedRewardDelegate {

    @Published var isLoaded = false
    @Published var earnedReward: String?

    private var rewardAd: AdWhaleMediationRewardAd?

    func load() {
        // 1. 인스턴스 생성 (placementUid)
        let ad = AdWhaleMediationRewardAd(placementUid: "발급받은 PLACEMENT_UID 값")

        // 2. 옵션 설정 (선택)
        ad.placementName = "reward_main"

        // 3. SSV 설정 (선택, AdMob 전용)
        ad.setUserId("user-1234")
        ad.setCustomData(["orderId": "A-99"])

        // 4. 전체화면 델리게이트 등록
        ad.fullScreenDelegate = self
        rewardAd = ad

        // 5. 로드
        ad.loadAd(loadDelegate: self)
    }

    /// 6. 노출
    func show() {
        guard let top = Self.topViewController() else { return }
        rewardAd?.show(from: top, rewardDelegate: self)
    }

    /// 7. 폐기
    func release() {
        rewardAd?.destroy()
        rewardAd = nil
    }

    // MARK: - 로드

    func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                    didLoadWith responseInfo: AdWhaleMediationResponseInfo) {
        print("보상형 로드 성공 — network=\(responseInfo.adNetworkName ?? "-")")
        isLoaded = true
    }

    func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                    didFailToLoadWithError statusCode: Int, message: String) {
        print("보상형 로드 실패 (\(statusCode)): \(message)")
        isLoaded = false
    }

    // MARK: - 전체 화면

    func rewardedAdDidShow(_ ad: AdWhaleMediationRewardAd) {
        isLoaded = false
    }

    func rewardedAdDidDismiss(_ ad: AdWhaleMediationRewardAd) {
        load()   // 다음 광고 미리 로드
    }

    func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                    didFailToShowWithError statusCode: Int, message: String) {
        print("보상형 노출 실패 (\(statusCode)): \(message)")
    }

    // MARK: - 보상

    func userDidEarnReward(_ rewardItem: AdWhaleMediationRewardItem) {
        print("보상 지급: \(rewardItem.rewardType) x \(rewardItem.rewardAmount)")
        earnedReward = "\(rewardItem.rewardType) x \(rewardItem.rewardAmount)"
        // 서버에 보상 반영
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

struct RewardScreen: View {
    @StateObject private var controller = RewardAdController()

    var body: some View {
        VStack(spacing: 16) {
            Button("보상형 광고 보기") { controller.show() }
                .disabled(!controller.isLoaded)

            if let reward = controller.earnedReward {
                Text("보상 지급: \(reward)")
            }
        }
        .onAppear { controller.load() }
        .onDisappear { controller.release() }
    }
}
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
#import <AdWhaleSDK/AdWhaleSDK-Swift.h>

@interface RewardViewController () <AdWhaleMediationRewardAdLoadDelegate,
                                    AdWhaleRewardedFullScreenDelegate,
                                    AdWhaleUserEarnedRewardDelegate>
@property (nonatomic, strong) AdWhaleMediationRewardAd *rewardAd;
@end

@implementation RewardViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    [self loadReward];
}

- (void)loadReward {
    // 1. 인스턴스 생성 (placementUid)
    self.rewardAd = [[AdWhaleMediationRewardAd alloc]
                     initWithPlacementUid:@"발급받은 PLACEMENT_UID 값"];

    // 2. 옵션 설정 (선택)
    self.rewardAd.region = @"서울시 강남구";
    [self.rewardAd setGeocoderWithLatitude:37.5665 longitude:126.9780];
    self.rewardAd.placementName = @"reward_main";

    // 3. SSV 설정 (선택, AdMob 전용)
    [self.rewardAd setUserId:@"user-1234"];
    [self.rewardAd setCustomData:@{@"orderId": @"A-99"}];

    // 4. 전체화면 델리게이트 등록
    [self.rewardAd setAdWhaleRewardedFullScreenDelegate:self];

    // 5. 로드
    [self.rewardAd loadAdWithLoadDelegate:self];
}

- (IBAction)onShowTapped:(id)sender {
    // 6. 노출
    [self.rewardAd showFrom:self rewardDelegate:self];
}

- (void)rewardedAd:(AdWhaleMediationRewardAd *)ad
       didLoadWith:(AdWhaleMediationResponseInfo *)responseInfo {
    NSLog(@"보상형 로드 성공");
}

- (void)rewardedAd:(AdWhaleMediationRewardAd *)ad
didFailToLoadWithError:(NSInteger)statusCode message:(NSString *)message {
    NSLog(@"보상형 로드 실패 (%ld): %@", (long)statusCode, message);
}

- (void)rewardedAdDidDismiss:(AdWhaleMediationRewardAd *)ad {
    [self loadReward];   // 다음 광고 미리 로드
}

- (void)rewardedAd:(AdWhaleMediationRewardAd *)ad
didFailToShowWithError:(NSInteger)statusCode message:(NSString *)message {
    NSLog(@"보상형 노출 실패 (%ld): %@", (long)statusCode, message);
}

- (void)userDidEarnReward:(AdWhaleMediationRewardItem *)rewardItem {
    NSLog(@"보상 지급: %@ x %ld", rewardItem.rewardType, (long)rewardItem.rewardAmount);
    // 서버에 보상 반영
}

- (void)dealloc {
    // 7. 폐기
    [self.rewardAd destroy];
    self.rewardAd = nil;
}

@end
```
{% endtab %}
{% endtabs %}

**7. 주의사항**

**광고 로드 타이밍**

* `loadAd(loadDelegate:)` 는 SDK 초기화 완료(`initialize` 완료 콜백) 이후에 호출하는 것을 권장합니다.
* 광고는 로드가 완료된 후에만 노출할 수 있습니다. `rewardedAd(_:didLoadWith:)` 이후에만 `show(from:rewardDelegate:)` 를 호출하세요.

**보상 처리**

* **보상 지급은 `userDidEarnReward` 에서만 처리하세요.** `rewardedAdDidDismiss` 는 사용자가 시청을 완료하지 않고 닫은 경우에도 호출됩니다.
* 서버에서 검증이 필요하면 SSV(`setUserId` / `setCustomData`)를 사용하세요.

**델리게이트 필수 여부**

* `loadDelegate` 와 `rewardDelegate` 는 **필수 파라미터**입니다. nil 을 넘길 수 없습니다.
* `fullScreenDelegate` 는 옵셔널이라 설정하지 않으면 노출 실패가 전달되지 않습니다.

**1회성 광고**

* 보상형도 1회성입니다. `rewardedAdDidDismiss` 에서 다음 광고를 미리 로드하는 패턴을 권장합니다.

**인스턴스 보유**

* SDK 는 델리게이트를 `weak` 로 참조합니다. 광고 인스턴스를 앱이 강하게 보유하지 않으면 콜백이 도착하기 전에 해제되어 "광고도 안 나오고 콜백도 없는" 상태가 됩니다.

**리소스 해제**

* `deinit` 또는 화면 종료 시 반드시 `destroy()` 를 호출하세요.

**에러 처리**

* `didFailToLoadWithError` 와 `didFailToShowWithError` 이벤트에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.

**테스트**

* 개발 환경에서는 테스트용 placement UID 를 사용하세요.
* 실제 배포 전에 다양한 시나리오에서 테스트하세요.

{% hint style="danger" %}
**보상 지급은 `userDidEarnReward` 에서만 처리하세요.** `rewardedAdDidDismiss` 는 사용자가 시청을 완료하지 않고 닫은 경우에도 호출됩니다.
{% endhint %}

{% hint style="warning" %}
`loadDelegate` 와 `rewardDelegate` 는 **필수 파라미터**입니다. nil 을 넘길 수 없습니다. 반면 `fullScreenDelegate` 는 옵셔널이라 설정하지 않으면 노출 실패가 전달되지 않습니다.
{% endhint %}

{% hint style="info" %}
보상형도 1회성입니다. `rewardedAdDidDismiss` 에서 다음 광고를 미리 로드하는 패턴을 권장합니다.
{% endhint %}
