# 보상형

{% hint style="info" %}
보상형 광고는 사용자가 광고를 시청한 후 보상을 지급하는 앱 화면 전체를 덮는 형태로 표기되는 광고입니다.\
게임 내 아이템, 코인, 생명 등 다양한 보상을 제공하여 높은 참여도와 수익을 얻을 수 있습니다.

이 문서는 iOS 프로젝트에서 AdWhale Mediation SDK 를 사용하여 보상형 광고를 연동하는 방법을 설명합니다.
{% endhint %}

#### **1. 주요특징**

* 시청 완료 시 보상을 지급하는 전체 화면 광고
* 로드 · 노출 · 보상 지급 델리게이트가 **역할별로 분리**
* 서버 사이드 검증(SSV) 지원 (`setUserId()`, `setCustomData()`)
* 로드 · 노출 · 클릭 · 닫힘 · 보상 지급 등 이벤트 기반 델리게이트 콜백 시스템으로 광고 상태 추적 가능

| 항목      | 내용                                  |
| ------- | ----------------------------------- |
| 클래스     | `AdWhaleMediationRewardAd`          |
| 지원 네트워크 | AdMob, AdManager, Admize, LevelPlay |
| 노출      | `show(from:rewardDelegate:)`        |

#### **2. 기본 구현 샘플코드**

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

#### **3. API 설명**

보상형은 델리게이트가 **세 개**로 나뉘며, 두 개는 프로퍼티가 아니라 **파라미터**로 넘깁니다.

| Delegate                               | 역할           | 설정 방법                                 |
| -------------------------------------- | ------------ | ------------------------------------- |
| `AdWhaleMediationRewardAdLoadDelegate` | 로드 결과        | `loadAd(loadDelegate:)` **파라미터**      |
| `AdWhaleRewardedFullScreenDelegate`    | 노출 · 클릭 · 닫힘 | `fullScreenDelegate` 프로퍼티             |
| `AdWhaleUserEarnedRewardDelegate`      | 보상 지급        | `show(from:rewardDelegate:)` **파라미터** |

**AdWhaleMediationRewardAd 클래스 API 설명**

{% tabs %}
{% tab title="Swift" %}
```swift
init(placementUid: String)
```

| 파라미터 타입  | 파라미터 값                |
| -------- | --------------------- |
| `String` | placementUid 값(발급 필요) |

```swift
weak var loadDelegate: AdWhaleMediationRewardAdLoadDelegate?      // loadAd(loadDelegate:) 가 설정
weak var fullScreenDelegate: AdWhaleRewardedFullScreenDelegate?   // 앱이 직접 설정
func setAdWhaleRewardedFullScreenDelegate(_ delegate: AdWhaleRewardedFullScreenDelegate?)
```

| 파라미터 타입                             | 파라미터 값                |
| ----------------------------------- | --------------------- |
| `AdWhaleRewardedFullScreenDelegate` | 노출 · 클릭 · 닫힘 콜백 델리게이트 |

```swift
func loadAd(loadDelegate: AdWhaleMediationRewardAdLoadDelegate)   // 미디에이션 보상형 광고 로드
```

| 파라미터 타입                                | 파라미터 값         |
| -------------------------------------- | -------------- |
| `AdWhaleMediationRewardAdLoadDelegate` | 로드 결과 콜백 델리게이트 |

<pre class="language-swift"><code class="lang-swift"><strong>func show(from viewController: UIViewController,
</strong>          rewardDelegate: AdWhaleUserEarnedRewardDelegate)   // 광고 로드 후 노출할 때 호출
</code></pre>

| 파라미터 타입                           | 파라미터 값                                             |
| --------------------------------- | -------------------------------------------------- |
| `UIViewController`                | 노출 기준 ViewController (`view.window != nil` 상태여야 함) |
| `AdWhaleUserEarnedRewardDelegate` | 보상 지급 콜백 델리게이트                                     |

```swift
func destroy()   // deinit 시 호출 혹은 더 이상 광고를 요청하지 않고 싶을 때 호출
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
- (instancetype)initWithPlacementUid:(NSString *)placementUid;
```

| 파라미터 타입      | 파라미터 값                |
| ------------ | --------------------- |
| `NSString *` | placementUid 값(발급 필요) |

```objectivec
@property (nonatomic, weak) id<AdWhaleMediationRewardAdLoadDelegate> loadDelegate;      // loadAdWithLoadDelegate: 가 설정
@property (nonatomic, weak) id<AdWhaleRewardedFullScreenDelegate> fullScreenDelegate;   // 앱이 직접 설정
- (void)setAdWhaleRewardedFullScreenDelegate:(id<AdWhaleRewardedFullScreenDelegate>)delegate;
```

| 파라미터 타입                                 | 파라미터 값                |
| --------------------------------------- | --------------------- |
| `id<AdWhaleRewardedFullScreenDelegate>` | 노출 · 클릭 · 닫힘 콜백 델리게이트 |

```objectivec
- (void)loadAdWithLoadDelegate:(id<AdWhaleMediationRewardAdLoadDelegate>)loadDelegate;   // 미디에이션 보상형 광고 로드
```

| 파라미터 타입                                    | 파라미터 값         |
| ------------------------------------------ | -------------- |
| `id<AdWhaleMediationRewardAdLoadDelegate>` | 로드 결과 콜백 델리게이트 |

```objectivec
- (void)showFrom:(UIViewController *)viewController
  rewardDelegate:(id<AdWhaleUserEarnedRewardDelegate>)rewardDelegate;   // 광고 로드 후 노출할 때 호출
```

| 파라미터 타입                               | 파라미터 값                                             |
| ------------------------------------- | -------------------------------------------------- |
| `UIViewController *`                  | 노출 기준 ViewController (`view.window != nil` 상태여야 함) |
| `id<AdWhaleUserEarnedRewardDelegate>` | 보상 지급 콜백 델리게이트                                     |

```objectivec
- (void)destroy;   // dealloc 시 호출 혹은 더 이상 광고를 요청하지 않고 싶을 때 호출
```
{% endtab %}
{% endtabs %}

**AdWhaleMediationRewardAdLoadDelegate 프로토콜 API 설명**

| 델리게이트 메서드                                       | 호출 시점    |  필수 |
| ----------------------------------------------- | -------- | :-: |
| `rewardedAd(_:didLoadWith:)`                    | 광고 로드 성공 |  ●  |
| `rewardedAd(_:didFailToLoadWithError:message:)` | 광고 로드 실패 |  ●  |

**AdWhaleRewardedFullScreenDelegate 프로토콜 API 설명**

| 델리게이트 메서드                                       | 호출 시점    |  필수 |
| ----------------------------------------------- | -------- | :-: |
| `rewardedAdDidShow(_:)`                         | 광고 노출 성공 |     |
| `rewardedAd(_:didFailToShowWithError:message:)` | 광고 노출 실패 |     |
| `rewardedAdDidClick(_:)`                        | 광고 클릭    |     |
| `rewardedAdDidDismiss(_:)`                      | 광고 닫힘    |     |

**AdWhaleUserEarnedRewardDelegate 프로토콜 API 설명**

| 델리게이트 메서드               | 호출 시점       |  필수 |
| ----------------------- | ----------- | :-: |
| `userDidEarnReward(_:)` | 보상 지급 조건 충족 |  ●  |

{% tabs %}
{% tab title="Swift" %}
```swift
// AdWhaleMediationRewardAdLoadDelegate — 필수
func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                didLoadWith responseInfo: AdWhaleMediationResponseInfo)   // 광고 요청 성공 시
```

| 파라미터 타입                        | 파라미터 값                                       |
| ------------------------------ | -------------------------------------------- |
| `AdWhaleMediationResponseInfo` | 낙찰된 광고의 응답 정보 (`adNetworkName`, `revenue` 등) |

```swift
// AdWhaleMediationRewardAdLoadDelegate — 필수
func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                didFailToLoadWithError statusCode: Int, message: String)   // 광고 요청 실패 시
```

| 파라미터 타입  | 파라미터 값                                                                                                                                                                                                                                                                                           |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Int`    | <p><code>200</code> = 연동 오류(placementUid 오설정 등)<br>또는<br><code>300</code> = 광고를 채우지 못함(워터폴 모두 소진)</p>                                                                                                                                                                                            |
| `String` | <p><strong><code>200</code></strong> — <code>SDK not initialized.</code> / <br><code>placementUid is empty.</code> / <br><code>Failed to create config request.</code><br><strong><code>300</code></strong> — <code>All rewarded mediations failed</code> (워터폴 모두 소진) 또는 광고 네트워크가 전달한 오류 메시지</p> |

```swift
// AdWhaleRewardedFullScreenDelegate — 전부 optional
@objc optional func rewardedAdDidShow(_ ad: AdWhaleMediationRewardAd)      // 광고 노출 후
@objc optional func rewardedAdDidClick(_ ad: AdWhaleMediationRewardAd)     // 광고 클릭 시
@objc optional func rewardedAdDidDismiss(_ ad: AdWhaleMediationRewardAd)   // 광고 닫기 시
```

```swift
// AdWhaleRewardedFullScreenDelegate — optional
@objc optional func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                               didFailToShowWithError statusCode: Int, message: String)   // 광고 노출 실패 시
```

| 파라미터 타입  | 파라미터 값                                                                                                                                                                                         |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Int`    | `200` = 연동 오류 (로드 전 `show(from:rewardDelegate:)` 호출, window 계층에 없는 ViewController 전달 등)                                                                                                        |
| `String` | <p><code>Ad not loaded.</code> = 아직 로드되지 않은 상태에서 <code>show(from:rewardDelegate:)</code> 호출<br>또는<br><code>ViewController is not in window hierarchy.</code> = dismiss 된 ViewController 전달</p> |

```swift
// AdWhaleUserEarnedRewardDelegate — 필수
func userDidEarnReward(_ rewardItem: AdWhaleMediationRewardItem)   // 보상 지급 시
```

| 파라미터 타입                      | 파라미터 값                                          |
| ---------------------------- | ----------------------------------------------- |
| `AdWhaleMediationRewardItem` | <p>리워드 지급 아이템. <br>타입·금액 등은 rewardItem에서 조회</p> |
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
// AdWhaleMediationRewardAdLoadDelegate — 필수
- (void)rewardedAd:(AdWhaleMediationRewardAd *)ad
       didLoadWith:(AdWhaleMediationResponseInfo *)responseInfo;   // 광고 요청 성공 시
```

| 파라미터 타입                          | 파라미터 값                                       |
| -------------------------------- | -------------------------------------------- |
| `AdWhaleMediationResponseInfo *` | 낙찰된 광고의 응답 정보 (`adNetworkName`, `revenue` 등) |

```objectivec
// AdWhaleMediationRewardAdLoadDelegate — 필수
- (void)rewardedAd:(AdWhaleMediationRewardAd *)ad
    didFailToLoadWithError:(NSInteger)statusCode
                   message:(NSString *)message;   // 광고 요청 실패 시
```

| 파라미터 타입      | 파라미터 값                                                                                                                                                                                                                                                                                           |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `NSInteger`  | <p><code>200</code> = 연동 오류(placementUid 오설정 등)<br>또는<br><code>300</code> = 광고를 채우지 못함(워터폴 모두 소진)</p>                                                                                                                                                                                            |
| `NSString *` | <p><strong><code>200</code></strong> — <code>SDK not initialized.</code> / <br><code>placementUid is empty.</code> / <br><code>Failed to create config request.</code><br><strong><code>300</code></strong> — <code>All rewarded mediations failed</code> (워터폴 모두 소진) 또는 광고 네트워크가 전달한 오류 메시지</p> |

```objectivec
// AdWhaleRewardedFullScreenDelegate — 전부 @optional
@optional
- (void)rewardedAdDidShow:(AdWhaleMediationRewardAd *)ad;      // 광고 노출 후
- (void)rewardedAdDidClick:(AdWhaleMediationRewardAd *)ad;     // 광고 클릭 시
- (void)rewardedAdDidDismiss:(AdWhaleMediationRewardAd *)ad;   // 광고 닫기 시
```

```objectivec
// AdWhaleRewardedFullScreenDelegate — @optional
@optional
- (void)rewardedAd:(AdWhaleMediationRewardAd *)ad
    didFailToShowWithError:(NSInteger)statusCode
                   message:(NSString *)message;   // 광고 노출 실패 시
```

| 파라미터 타입      | 파라미터 값                                                                                                                                                                                       |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `NSInteger`  | `200` = 연동 오류 (로드 전 `showFrom:rewardDelegate:` 호출, window 계층에 없는 ViewController 전달 등)                                                                                                        |
| `NSString *` | <p><code>Ad not loaded.</code> = 아직 로드되지 않은 상태에서 <code>showFrom:rewardDelegate:</code> 호출<br>또는<br><code>ViewController is not in window hierarchy.</code> = dismiss 된 ViewController 전달</p> |

```objectivec
// AdWhaleUserEarnedRewardDelegate — 필수
- (void)userDidEarnReward:(AdWhaleMediationRewardItem *)rewardItem;   // 보상 지급 시
```

| 파라미터 타입                        | 파라미터 값                               |
| ------------------------------ | ------------------------------------ |
| `AdWhaleMediationRewardItem *` | 리워드 지급 아이템. 타입·금액 등은 rewardItem에서 조회 |
{% endtab %}
{% endtabs %}

#### **4. 옵션 설정**

{% tabs %}
{% tab title="Swift" %}
```swift
// 레벨플레이 placement name 연동 전용 API (옵션).
// placementName 값은 LevelPlay 콘솔에서 설정한 이름
rewardAd.placementName = "reward_main"
```

{% hint style="info" %}
AdWhale SDK 는 LevelPlay 네트워크를 지원하며, 각 Placement 별로 광고 노출을 구분하고자 할 때 `placementName` 으로 설정할 수 있습니다.
{% endhint %}

{% hint style="warning" %}
LevelPlay 콘솔에서 설정한 Placement 이름을 지정하면, 해당 Placement 에 설정된 **보상 금액, 노출 제한 등의 설정이 적용된 광고**가 노출됩니다. \
설정하지 않으면 기본 Placement(Default Placement)가 사용됩니다.
{% endhint %}
{% endtab %}

{% tab title="SwiftUI" %}
```swift
// 광고 인스턴스를 보유한 ObservableObject 안에서 동일하게 설정합니다.
// placementName 값은 LevelPlay 콘솔에서 설정한 이름
rewardAd.placementName = "reward_main"
```

{% hint style="info" %}
AdWhale SDK 는 LevelPlay 네트워크를 지원하며, 각 Placement 별로 광고 노출을 구분하고자 할 때 `placementName` 으로 설정할 수 있습니다.
{% endhint %}

{% hint style="warning" %}
LevelPlay 콘솔에서 설정한 Placement 이름을 지정하면, 해당 Placement 에 설정된 **보상 금액, 노출 제한 등의 설정이 적용된 광고**가 노출됩니다. 설정하지 않으면 기본 Placement(Default Placement)가 사용됩니다.
{% endhint %}
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
// 레벨플레이 placement name 연동 전용 API (옵션).
// placementName 값은 LevelPlay 콘솔에서 설정한 이름
rewardAd.placementName = @"reward_main";
```

{% hint style="info" %}
AdWhale SDK 는 LevelPlay 네트워크를 지원하며, 각 Placement 별로 광고 노출을 구분하고자 할 때 `placementName` 으로 설정할 수 있습니다.
{% endhint %}

{% hint style="warning" %}
LevelPlay 콘솔에서 설정한 Placement 이름을 지정하면, 해당 Placement 에 설정된 **보상 금액, 노출 제한 등의 설정이 적용된 광고**가 노출됩니다. 설정하지 않으면 기본 Placement(Default Placement)가 사용됩니다.
{% endhint %}
{% endtab %}
{% endtabs %}

| 프로퍼티 / 메서드                            | 설명             | 적용 네트워크          |
| ------------------------------------- | -------------- | ---------------- |
| `placementName`                       | Placement 이름   | LevelPlay        |
| `setUserId(_:)` / `setCustomData(_:)` | SSV(서버 사이드 검증) | AdMob, AdManager |

#### **5. 보상형 SSV(서버 사이드 검증)**

보상형 SSV 는 서버에서 리워드 검증 및 지급을 제공하기 위한 기능입니다. 광고 네트워크가 퍼블리셔 서버로 직접 postback(콜백)을 보내며, AdWhale 은 이를 위해 `setUserId` / `setCustomData` 두 개의 API 를 제공합니다.

{% hint style="warning" %}
**지원 범위: AdMob / AdManager 만 지원합니다.**

보상형(Rewarded) · 보상형 전면(RewardedInterstitial) 광고에서 AdMob(SSV, Server-Side Verification) 으로만 동작합니다. 그 외 네트워크에서 두 메서드는 **무시(no-op)** 되며 아무 동작도 하지 않습니다.
{% endhint %}

{% tabs %}
{% tab title="Swift" %}
```swift
rewardAd.setUserId("test_user_id")                                   // 앱 사용자 id
rewardAd.setCustomData(["custom_test_key": "custom_test_value",
                        "os": "iOS"])                                // custom data (JSON 직렬화 문자열 처리)
```
{% endtab %}

{% tab title="SwiftUI" %}
```swift
// 광고 인스턴스를 보유한 ObservableObject 안에서 동일하게 설정합니다.
rewardAd.setUserId("test_user_id")                                   // 앱 사용자 id
rewardAd.setCustomData(["custom_test_key": "custom_test_value",
                        "os": "iOS"])                                // custom data (JSON 직렬화 문자열 처리)
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
[rewardAd setUserId:@"test_user_id"];                                // 앱 사용자 id
[rewardAd setCustomData:@{@"custom_test_key": @"custom_test_value",
                          @"os": @"iOS"}];                           // custom data (JSON 직렬화 문자열 처리)
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
**호출 타이밍**

* `AdWhaleMediationRewardAd(placementUid:)` 이후 `show(from:rewardDelegate:)` 직전까지 **언제든** 호출할 수 있습니다.
* 여러 번 호출하면 **마지막 값이 적용**됩니다.
* `show(from:rewardDelegate:)` 이후 호출은 반영되지 않습니다.
{% endhint %}

{% hint style="danger" %}
**대시보드 사전 설정 (필수)**

대시보드의 SSV callback URL 등록이 선행되지 않으면 postback 이 발생하지 않습니다. **AdMob / AdManager 대시보드에서 해당 ad unit 별로 SSV callback URL 을 먼저 등록**해야 합니다.

* AdMob 공식 가이드: [https://developers.google.com/admob/ios/ssv](https://developers.google.com/admob/ios/ssv)
{% endhint %}

{% hint style="info" %}
**customData JSON 직렬화 주의**

AdMob SSV 의 `custom_data` 는 **단일 문자열** 필드입니다. AdWhale 은 전달받은 `[String: String]` 을 JSON 으로 직렬화해 싣습니다.

* 퍼블리셔 서버는 postback 의 `custom_data` 값을 **JSON 으로 파싱**해야 key/value 를 얻을 수 있습니다.
* AdMob 이 `custom_data` 길이 상한을 공식적으로 명시하진 않지만, 실무상 **200자 정도를 넘기면 잘림**이 보고된 사례가 있으니 길이에 유의하세요.
{% endhint %}

#### **6. 보상형 광고 샘플코드**

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
        ad.placementName = "reward_main"

        // 3. SSV 설정 (선택, AdMob · AdManager 전용)
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

        // 3. SSV 설정 (선택, AdMob · AdManager 전용)
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
#import "RewardViewController.h"
@import AdWhaleSDK;

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
    self.rewardAd.placementName = @"reward_main";

    // 3. SSV 설정 (선택, AdMob · AdManager 전용)
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

#### **7. 주의사항**

**보상 지급 시점**

* `userDidEarnReward` 는 사용자가 광고를 완전히 시청했을 때만 호출됩니다.
* 광고를 중간에 닫으면 보상이 지급되지 않습니다.
* **보상 지급은 `userDidEarnReward` 에서만 처리하세요.** \
  `rewardedAdDidDismiss` 는 사용자가 시청을 완료하지 않고 닫은 경우에도 호출됩니다.

**광고 로드 타이밍**

* `loadAd(loadDelegate:)` 는 SDK 초기화 완료(`initialize` 완료 콜백) 이후에 호출하세요. \
  초기화 전에 호출하면 `200`(`SDK not initialized.`)이 통지됩니다.
* 광고는 로드가 완료된 후에만 노출할 수 있습니다. \
  `rewardedAd(_:didLoadWith:)` 이후에만 `show(from:rewardDelegate:)` 를 호출하세요.
* 광고 네트워크가 성공·실패 어느 콜백도 돌려주지 않으면, SDK 가 네트워크당 **10초**를 기다린 뒤 다음 순위 네트워크로 진행합니다. \
  이 만료는 별도 콜백으로 통지되지 않으며, 워터폴이 모두 소진된 시점에 실패 콜백이 1회 옵니다.

**보상 중복 지급 방지**

* `userDidEarnReward` 는 노출 1회당 한 번만 호출됩니다.
* 서버와 동기화하여 중복 지급을 방지하는 것을 권장합니다.

**1회성 광고**

* 보상형 광고는 **1회성**입니다. 한 번 노출한 뒤 다시 보여주려면 `loadAd(loadDelegate:)` 를 다시 호출해야 합니다.
* `rewardedAdDidDismiss` 에서 다음 광고를 미리 로드하는 패턴을 권장합니다.

**델리게이트 등록**

* `loadDelegate` 와 `rewardDelegate` 는 **필수 파라미터**입니다. nil 을 넘길 수 없습니다.
* `fullScreenDelegate` 는 설정하지 않으면 노출·클릭·닫힘과 노출 실패가 전달되지 않습니다. \
  SDK 는 이 경우 `통지할 리스너가 없습니다` 경고 로그를 남깁니다.

**인스턴스 보유**

* SDK 는 세 델리게이트를 모두 `weak` 로 참조합니다.&#x20;
* 광고 인스턴스를 앱이 강하게 보유하지 않으면 콜백이 도착하기 전에 해제되어 **"광고도 안 나오고 콜백도 없는"** 상태가 됩니다.
* `show(from:rewardDelegate:)` 에 넘긴 보상 델리게이트도 `weak` 로 잡히므로 시청이 끝날 때까지 보유해야 합니다. \
  보유하지 않으면 보상 콜백이 도착하기 전에 해제되어 **광고는 봤지만 보상이 지급되지 않는** 상태가 됩니다.

**리소스 해제**

* `deinit` 또는 화면 종료 시 반드시 `destroy()` 를 호출하세요.

**에러 처리**

* `didFailToLoadWithError` 와 `didFailToShowWithError` 에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.

**노출 실패 후처리**

*   노출할 광고가 준비되지 않은 상태에서 `show(from:rewardDelegate:)` 를 호출해도 `rewardedAd(_:didFailToShowWithError:message:)` 가 통지됩니다.

    ```
    statusCode : 200
    message    : "Ad not loaded."
    ```
*   보상형은 사용자가 **"광고 보고 보상 받기"** 를 누른 직후이므로, 아무 반응이 없으면 보상이 지급되지 않은 것으로 오해할 수 있습니다. 이 콜백에서 반드시 안내를 노출하세요.

    ```swift
    func rewardedAd(_ ad: AdWhaleMediationRewardAd,
                    didFailToShowWithError statusCode: Int, message: String) {
        hideLoading()
        showAlert("잠시 후 다시 시도해 주세요.")
    }
    ```
* 노출 실패한 경우 **보상은 지급되지 않습니다.** `userDidEarnReward` 만을 보상 지급의 기준으로 사용하세요.

**테스트**

* 개발 환경에서는 테스트용 placement UID 를 사용하세요.
* 보상 지급 로직을 충분히 테스트하세요.

{% hint style="warning" %}
`show(from:rewardDelegate:)` 에는 `view.window != nil` 상태의 ViewController 를 넘겨야 합니다. dismiss 된 ViewController 를 넘기면 `200`(`ViewController is not in window hierarchy.`)이 통지됩니다.
{% endhint %}
