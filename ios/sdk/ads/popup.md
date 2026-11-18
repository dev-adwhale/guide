# 4.7 팝업

{% hint style="info" %}
팝업 광고는 앱 내 화면 전환 시점에 다이얼로그 형태로 표시되는 광고입니다. 이 문서는 iOS 프로젝트에서 AdWhale Mediation SDK 를 사용하여 팝업 광고를 연동하는 방법을 설명합니다.
{% endhint %}

{% hint style="info" %}
iOS 는 **앱 내 화면 전환 팝업**만 제공합니다. iOS 앱에는 뒤로 가기 키로 앱을 종료하는 동작이 없고, 광고 네트워크(AdFit · Cauly)도 iOS 용 앱 종료 팝업을 제공하지 않습니다. (Android 는 `앱 종료` 팝업 / `앱 내 화면 전환` 팝업 페이지가 분리되어 있습니다)
{% endhint %}

### 1. 주요 특징

* 앱 내 화면 전환 시점에 노출하는 팝업형 광고
* 닫힘 사유를 콜백으로 전달 (`AdWhalePopupCloseReason` 7종)
* 로드 · 노출을 2단계로 통지 (재고 확정은 노출 시점)
* 로드 · 노출 · 클릭 · 닫힘 등 이벤트 기반 델리게이트 콜백 시스템으로 광고 상태 추적 가능

| 항목      | 내용                        |
| ------- | ------------------------- |
| 클래스     | `AdWhaleMediationPopupAd` |
| 지원 네트워크 | AdFit                     |
| 노출      | `show(from:)`             |

### 2. 기본 구현 샘플 코드

`AdWhaleMediationPopupAd` 클래스를 사용하여 팝업 광고를 로드하고 표시하는 기본적인 구현 방법입니다.

{% tabs %}
{% tab title="Swift" %}
```swift
final class PopupViewController: UIViewController, AdWhaleMediationPopupAdDelegate {

    // ★ 광고 인스턴스는 앱이 강하게 보유해야 합니다. (SDK 는 delegate 를 weak 로 참조)
    private var popupAd: AdWhaleMediationPopupAd?

    override func viewDidLoad() {
        super.viewDidLoad()

        // 1. 인스턴스 생성 (placementUid)
        let ad = AdWhaleMediationPopupAd(placementUid: "발급받은 PLACEMENT_UID 값")

        // 2. 델리게이트 등록
        ad.delegate = self

        popupAd = ad

        // 3. 로드
        ad.loadAd()
    }

    /// 사용자가 화면 전환을 시도할 때 호출
    func onPopupRequested() {
        // 4. 노출 (로드 완료 후)
        popupAd?.show(from: self)
    }

    // MARK: - AdWhaleMediationPopupAdDelegate

    func popupAdDidLoad(_ ad: AdWhaleMediationPopupAd) {
        // 로드 성공 (잠정 — 재고는 show 시점에 확정)
    }

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didFailToLoadWithError statusCode: Int, message: String) {
        // 로드 실패
    }

    func popupAdDidShow(_ ad: AdWhaleMediationPopupAd) {
        // 노출됨
    }

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didFailToShowWithError statusCode: Int, message: String) {
        // 노출 실패 · 재고 부재
    }

    func popupAdDidClick(_ ad: AdWhaleMediationPopupAd) {
        // 클릭
    }

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didCloseWithReason reason: AdWhalePopupCloseReason) {
        // 닫힘 (사유 포함)
    }

    deinit {
        // 5. 폐기
        popupAd?.destroy()
        popupAd = nil
    }
}
```
{% endtab %}

{% tab title="SwiftUI" %}
```swift
// 팝업 광고는 노출 시 UIViewController 가 필요하므로
// 광고 인스턴스를 보유하는 ObservableObject 를 두고 최상단 ViewController 를 찾아 넘깁니다.
@MainActor
final class PopupAdController: NSObject, ObservableObject, AdWhaleMediationPopupAdDelegate {

    @Published var showFallbackDialog = false

    // ★ 광고 인스턴스는 앱이 강하게 보유해야 합니다.
    private var popupAd: AdWhaleMediationPopupAd?

    func load() {
        // 1. 인스턴스 생성 (placementUid)
        let ad = AdWhaleMediationPopupAd(placementUid: "발급받은 PLACEMENT_UID 값")

        // 2. 델리게이트 등록
        ad.delegate = self

        popupAd = ad

        // 3. 로드
        ad.loadAd()
    }

    /// 4. 노출 (로드 완료 후)
    func show() {
        guard let top = Self.topViewController() else { return }
        popupAd?.show(from: top)
    }

    /// 5. 폐기
    func release() {
        popupAd?.destroy()
        popupAd = nil
    }

    // MARK: - AdWhaleMediationPopupAdDelegate

    func popupAdDidLoad(_ ad: AdWhaleMediationPopupAd) { }

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didFailToLoadWithError statusCode: Int, message: String) { }

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didFailToShowWithError statusCode: Int, message: String) {
        if statusCode == 300 {
            // 재고 부재 — 연동 오류가 아니다. 자체 다이얼로그로 대체
            showFallbackDialog = true
        }
    }

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didCloseWithReason reason: AdWhalePopupCloseReason) { }

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
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
@interface PopupViewController () <AdWhaleMediationPopupAdDelegate>
// ★ 광고 인스턴스는 앱이 강하게 보유해야 합니다.
@property (nonatomic, strong) AdWhaleMediationPopupAd *popupAd;
@end

@implementation PopupViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    // 1. 인스턴스 생성 (placementUid)
    self.popupAd = [[AdWhaleMediationPopupAd alloc]
                    initWithPlacementUid:@"발급받은 PLACEMENT_UID 값"];

    // 2. 델리게이트 등록
    [self.popupAd setAdWhaleMediationPopupAdDelegate:self];

    // 3. 로드
    [self.popupAd loadAd];
}

- (void)onPopupRequested {
    // 4. 노출 (로드 완료 후)
    [self.popupAd showFrom:self];
}

// 로드 성공 (잠정)
- (void)popupAdDidLoad:(AdWhaleMediationPopupAd *)ad { }

// 로드 실패
- (void)popupAd:(AdWhaleMediationPopupAd *)ad
didFailToLoadWithError:(NSInteger)statusCode message:(NSString *)message { }

// 노출됨
- (void)popupAdDidShow:(AdWhaleMediationPopupAd *)ad { }

// 노출 실패 · 재고 부재
- (void)popupAd:(AdWhaleMediationPopupAd *)ad
didFailToShowWithError:(NSInteger)statusCode message:(NSString *)message { }

// 클릭
- (void)popupAdDidClick:(AdWhaleMediationPopupAd *)ad { }

// 닫힘 (사유 포함)
- (void)popupAd:(AdWhaleMediationPopupAd *)ad
didCloseWithReason:(AdWhalePopupCloseReason)reason { }

- (void)dealloc {
    // 5. 폐기
    [self.popupAd destroy];
    self.popupAd = nil;
}

@end
```
{% endtab %}
{% endtabs %}

### 3. API 설명

**AdWhaleMediationPopupAd 클래스 API 설명**

{% tabs %}
{% tab title="Swift" %}
```swift
init(placementUid: String)
```

| 파라미터 타입  | 파라미터 값                |
| -------- | --------------------- |
| `String` | placementUid 값(발급 필요) |

```swift
weak var delegate: AdWhaleMediationPopupAdDelegate?
func setAdWhaleMediationPopupAdDelegate(_ delegate: AdWhaleMediationPopupAdDelegate?)
```

| 파라미터 타입                           | 파라미터 값                  |
| --------------------------------- | ----------------------- |
| `AdWhaleMediationPopupAdDelegate` | 팝업 미디에이션 광고 호출 콜백 델리게이트 |

```swift
func loadAd()   // 미디에이션 팝업 광고 로드
```

```swift
func show(from viewController: UIViewController)   // 광고 로드 후 노출할 때 호출
```

| 파라미터 타입            | 파라미터 값                                             |
| ------------------ | -------------------------------------------------- |
| `UIViewController` | 노출 기준 ViewController (`view.window != nil` 상태여야 함) |

```swift
func resume()    // 화면 복귀 시 호출 (현재 지원 네트워크에서는 별도 동작 없음)
```

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
@property (nonatomic, weak) id<AdWhaleMediationPopupAdDelegate> delegate;
- (void)setAdWhaleMediationPopupAdDelegate:(id<AdWhaleMediationPopupAdDelegate>)delegate;
```

| 파라미터 타입                               | 파라미터 값                  |
| ------------------------------------- | ----------------------- |
| `id<AdWhaleMediationPopupAdDelegate>` | 팝업 미디에이션 광고 호출 콜백 델리게이트 |

```objectivec
- (void)loadAd;   // 미디에이션 팝업 광고 로드
```

```objectivec
- (void)showFrom:(UIViewController *)viewController;   // 광고 로드 후 노출할 때 호출
```

| 파라미터 타입              | 파라미터 값                                             |
| -------------------- | -------------------------------------------------- |
| `UIViewController *` | 노출 기준 ViewController (`view.window != nil` 상태여야 함) |

```objectivec
- (void)resume;    // 화면 복귀 시 호출 (현재 지원 네트워크에서는 별도 동작 없음)
```

```objectivec
- (void)destroy;   // dealloc 시 호출 혹은 더 이상 광고를 요청하지 않고 싶을 때 호출
```
{% endtab %}
{% endtabs %}

**AdWhaleMediationPopupAdDelegate 프로토콜 API 설명**

| 델리게이트 메서드                                    | 호출 시점            |  필수 |
| -------------------------------------------- | ---------------- | :-: |
| `popupAdDidLoad(_:)`                         | 광고 로드 성공 (잠정)    |  ●  |
| `popupAd(_:didFailToLoadWithError:message:)` | 광고 로드 실패         |  ●  |
| `popupAdDidShow(_:)`                         | 광고 노출 성공         |     |
| `popupAd(_:didFailToShowWithError:message:)` | 광고 노출 실패 · 재고 부재 |     |
| `popupAdDidClick(_:)`                        | 광고 클릭            |     |
| `popupAd(_:didCloseWithReason:)`             | 광고 닫힘 (사유 포함)    |     |

{% tabs %}
{% tab title="Swift" %}
```swift
func popupAdDidLoad(_ ad: AdWhaleMediationPopupAd)   // 광고 요청 성공 시 (잠정)
```

```swift
func popupAd(_ ad: AdWhaleMediationPopupAd,
             didFailToLoadWithError statusCode: Int, message: String)   // 광고 요청 실패 시
```

| 파라미터 타입  | 파라미터 값                                                                                                                                                                                                                                                                                      |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Int`    | <p><code>200</code> = 연동 오류(placementUid 오설정 등)<br>또는<br><code>300</code> = 광고를 채우지 못함(워터폴 모두 소진)</p>                                                                                                                                                                                       |
| `String` | <p><strong><code>200</code></strong> — <code>SDK not initialized.</code> /<br><code>placementUid is empty.</code> /<br><code>Failed to create config request.</code><br><strong><code>300</code></strong> — <code>All popup mediations failed</code> (워터폴 모두 소진) 또는 광고 네트워크가 전달한 오류 메시지</p> |

```swift
@objc optional func popupAdDidShow(_ ad: AdWhaleMediationPopupAd)   // 광고 노출 후
```

```swift
@objc optional func popupAd(_ ad: AdWhaleMediationPopupAd,
                            didFailToShowWithError statusCode: Int, message: String)   // 광고 노출 실패 시
```

| 파라미터 타입  | 파라미터 값                                                                                                                                                                                                                                                                       |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Int`    | <p><code>200</code> = 연동 오류 (로드 전 <code>show(from:)</code> 호출, window 계층에 없는 ViewController 전달 등)<br>또는<br><code>300</code> = <strong>노출 시점에 광고 재고가 없음</strong> (팝업 전용)</p>                                                                                                  |
| `String` | <p><code>No transition popup ad is ready to show.</code> = 아직 로드되지 않은 상태에서 <code>show(from:)</code> 호출<br>또는<br><code>ViewController is not in window hierarchy.</code> = dismiss 된 ViewController 전달<br>또는<br>광고 네트워크가 전달한 실패 메시지 (<code>200</code> · <code>300</code>)</p> |

{% hint style="info" %}
**`didFailToShowWithError` 후처리**

* 노출할 광고가 준비되지 않은 상태에서 `show(from:)` 을 호출해도 이 콜백이 발생합니다.
* 앱 내 화면 전환 팝업은 노출에 실패해도 앱의 흐름은 계속 진행되어야 합니다. 이 콜백에서 원래 하려던 화면 이동을 이어서 수행하세요.
{% endhint %}

```swift
@objc optional func popupAdDidClick(_ ad: AdWhaleMediationPopupAd)   // 광고 클릭 시
```

```swift
@objc optional func popupAd(_ ad: AdWhaleMediationPopupAd,
                            didCloseWithReason reason: AdWhalePopupCloseReason)   // 광고 닫기 시
```

| 파라미터 타입                   | 파라미터 값                   |
| ------------------------- | ------------------------ |
| `AdWhalePopupCloseReason` | 닫힘 사유 (아래 `5. 닫힘 사유` 참고) |
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
// 필수
- (void)popupAdDidLoad:(AdWhaleMediationPopupAd *)ad;   // 광고 요청 성공 시 (잠정)
```

```objectivec
// 필수
- (void)popupAd:(AdWhaleMediationPopupAd *)ad
    didFailToLoadWithError:(NSInteger)statusCode
                   message:(NSString *)message;   // 광고 요청 실패 시
```

| 파라미터 타입      | 파라미터 값                                                                                                                                                                                                                                                                                      |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `NSInteger`  | <p><code>200</code> = 연동 오류(placementUid 오설정 등)<br>또는<br><code>300</code> = 광고를 채우지 못함(워터폴 모두 소진)</p>                                                                                                                                                                                       |
| `NSString *` | <p><strong><code>200</code></strong> — <code>SDK not initialized.</code> /<br><code>placementUid is empty.</code> /<br><code>Failed to create config request.</code><br><strong><code>300</code></strong> — <code>All popup mediations failed</code> (워터폴 모두 소진) 또는 광고 네트워크가 전달한 오류 메시지</p> |

```objectivec
@optional
- (void)popupAdDidShow:(AdWhaleMediationPopupAd *)ad;   // 광고 노출 후
```

```objectivec
@optional
- (void)popupAd:(AdWhaleMediationPopupAd *)ad
    didFailToShowWithError:(NSInteger)statusCode
                   message:(NSString *)message;   // 광고 노출 실패 시
```

| 파라미터 타입      | 파라미터 값                                                                                                                                                                                                                                                                     |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `NSInteger`  | <p><code>200</code> = 연동 오류 (로드 전 <code>showFrom:</code> 호출, window 계층에 없는 ViewController 전달 등)<br>또는<br><code>300</code> = <strong>노출 시점에 광고 재고가 없음</strong> (팝업 전용)</p>                                                                                                  |
| `NSString *` | <p><code>No transition popup ad is ready to show.</code> = 아직 로드되지 않은 상태에서 <code>showFrom:</code> 호출<br>또는<br><code>ViewController is not in window hierarchy.</code> = dismiss 된 ViewController 전달<br>또는<br>광고 네트워크가 전달한 실패 메시지 (<code>200</code> · <code>300</code>)</p> |

{% hint style="info" %}
**`didFailToShowWithError` 후처리**

* 노출할 광고가 준비되지 않은 상태에서 `showFrom:` 을 호출해도 이 콜백이 발생합니다.
* 앱 내 화면 전환 팝업은 노출에 실패해도 앱의 흐름은 계속 진행되어야 합니다. 이 콜백에서 원래 하려던 화면 이동을 이어서 수행하세요.
{% endhint %}

```objectivec
@optional
- (void)popupAdDidClick:(AdWhaleMediationPopupAd *)ad;   // 광고 클릭 시
```

```objectivec
@optional
- (void)popupAd:(AdWhaleMediationPopupAd *)ad
    didCloseWithReason:(enum AdWhalePopupCloseReason)reason;   // 광고 닫기 시
```

| 파라미터 타입                   | 파라미터 값                   |
| ------------------------- | ------------------------ |
| `AdWhalePopupCloseReason` | 닫힘 사유 (아래 `5. 닫힘 사유` 참고) |
{% endtab %}
{% endtabs %}

### 4. 로드 성공은 "잠정" 성공입니다

AdFit 팝업은 내부적으로 **로드와 표시가 한 번에** 이뤄지는 구조라 노출 없이 재고를 미리 확인할 수 없습니다. 따라서 SDK 는 **두 단계 모두 통지**합니다.

| 단계                    | 콜백                                                                | 의미                             |
| --------------------- | ----------------------------------------------------------------- | ------------------------------ |
| `loadAd()`            | `popupAdDidLoad(_:)`                                              | **잠정 성공.** 팝업 준비까지만 성공, 재고 미확인 |
| `show(from:)` → 노출 성공 | `popupAdDidShow(_:)`                                              | 실제 광고가 노출됨                     |
| `show(from:)` → 재고 없음 | `popupAd(_:didFailToShowWithError:message:)` **statusCode = 300** | 노출 시점에 확정된 광고 미충족              |

{% hint style="danger" %}
**팝업만 노출 실패에 300 이 올 수 있습니다.** 다른 애드폼의 노출 실패는 모두 200(연동 오류)입니다. 팝업의 300 은 "연동은 정상인데 광고 재고가 없었다" 는 뜻이므로 **코드 수정 대상이 아닙니다.**
{% endhint %}

### 5. 닫힘 사유

`popupAd(_:didCloseWithReason:)` 로 전달되는 `AdWhalePopupCloseReason` 값입니다.

| 값                  | 원시값 | 의미            |
| ------------------ | --- | ------------- |
| `.unknown`         | 0   | 알 수 없음        |
| `.primaryButton`   | 1   | 기본 버튼         |
| `.secondaryButton` | 2   | 보조 버튼 (예: 취소) |
| `.outsideTouch`    | 4   | 팝업 외부 터치      |
| `.backPress`       | 8   | 뒤로 가기         |
| `.todayDismiss`    | 16  | 오늘 하루 보지 않기   |
| `.ambiguousCancel` | 32  | 사유 불명 취소      |

{% hint style="info" %}
닫힘 사유는 광고 네트워크마다 다릅니다. 현재 팝업을 제공하는 **AdFit 은 닫기 버튼을 누르면 `.ambiguousCancel`, 오늘 하루 보지 않기를 누르면 `.todayDismiss`** 를 전달하며, `.primaryButton` 은 전달하지 않습니다. (Android 와 동일)

**사유와 관계없이, 팝업이 닫히면 원래 하려던 화면 전환을 이어서 수행하세요.** 사유는 통계·로그 용도로 활용하는 것을 권장합니다.
{% endhint %}

### 6. 앱 내 화면 전환 팝업 샘플 코드

앱 내 화면 전환 시점에 팝업을 노출하고, **팝업이 닫히면** 목표 화면으로 이동합니다. 노출에 실패한 경우에도 광고 없이 곧바로 이동합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
final class TransitionPopupViewController: UIViewController, AdWhaleMediationPopupAdDelegate {

    private var popupAd: AdWhaleMediationPopupAd?
    private var pendingDestination: String?

    override func viewDidLoad() {
        super.viewDidLoad()
        loadPopup()
    }

    private func loadPopup() {
        // 1. 인스턴스 생성 (placementUid)
        let ad = AdWhaleMediationPopupAd(placementUid: "발급받은 PLACEMENT_UID 값")

        // 2. 델리게이트 등록
        ad.delegate = self

        popupAd = ad

        // 3. 로드
        ad.loadAd()
    }

    /// 화면 전환을 시도할 때 호출
    func onTransitionRequested(to destination: String) {
        pendingDestination = destination
        // 4. 노출
        popupAd?.show(from: self)
    }

    // MARK: - AdWhaleMediationPopupAdDelegate

    func popupAdDidLoad(_ ad: AdWhaleMediationPopupAd) {
        print("전환 팝업 준비 완료")
    }

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didFailToLoadWithError statusCode: Int, message: String) {
        print("전환 팝업 로드 실패 (\(statusCode)): \(message)")
    }

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didFailToShowWithError statusCode: Int, message: String) {
        // 재고 부재(300) · 미로드(200) 등 노출 실패 — 광고 없이 곧바로 화면 전환
        print("전환 팝업 노출 실패 (\(statusCode)): \(message)")
        navigateToPendingDestination()
    }

    func popupAd(_ ad: AdWhaleMediationPopupAd, didCloseWithReason reason: AdWhalePopupCloseReason) {
        // 닫힘 사유와 관계없이 원래 하려던 화면 전환을 이어서 수행
        print("전환 팝업 닫힘: \(reason.rawValue)")
        navigateToPendingDestination()

        // 다음 전환을 위해 미리 로드
        loadPopup()
    }

    private func navigateToPendingDestination() {
        guard let destination = pendingDestination else { return }
        pendingDestination = nil
        // 화면 전환 처리
        print("이동: \(destination)")
    }

    deinit {
        // 5. 폐기
        popupAd?.destroy()
        popupAd = nil
    }
}
```
{% endtab %}

{% tab title="SwiftUI" %}
```swift
@MainActor
final class TransitionPopupAdController: NSObject, ObservableObject, AdWhaleMediationPopupAdDelegate {

    @Published var navigateTo: String?

    private var popupAd: AdWhaleMediationPopupAd?
    private var pendingDestination: String?

    func load() {
        // 1. 인스턴스 생성 (placementUid)
        let ad = AdWhaleMediationPopupAd(placementUid: "발급받은 PLACEMENT_UID 값")

        // 2. 델리게이트 등록
        ad.delegate = self

        popupAd = ad

        // 3. 로드
        ad.loadAd()
    }

    /// 4. 노출 — 화면 전환을 시도할 때 호출
    func requestTransition(to destination: String) {
        pendingDestination = destination
        guard let top = Self.topViewController() else {
            navigateTo = destination
            pendingDestination = nil
            return
        }
        popupAd?.show(from: top)
    }

    /// 5. 폐기
    func release() {
        popupAd?.destroy()
        popupAd = nil
    }

    // MARK: - AdWhaleMediationPopupAdDelegate

    func popupAdDidLoad(_ ad: AdWhaleMediationPopupAd) { }

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didFailToLoadWithError statusCode: Int, message: String) { }

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didFailToShowWithError statusCode: Int, message: String) {
        // 재고 부재(300) · 미로드(200) 등 노출 실패 — 광고 없이 곧바로 화면 전환
        navigateTo = pendingDestination
        pendingDestination = nil
    }

    func popupAd(_ ad: AdWhaleMediationPopupAd, didCloseWithReason reason: AdWhalePopupCloseReason) {
        // 닫힘 사유와 관계없이 원래 하려던 화면 전환을 이어서 수행
        navigateTo = pendingDestination
        pendingDestination = nil
        load()   // 다음 전환을 위해 미리 로드
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
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
#import "TransitionPopupViewController.h"
@import AdWhaleSDK;

@interface TransitionPopupViewController () <AdWhaleMediationPopupAdDelegate>
@property (nonatomic, strong) AdWhaleMediationPopupAd *popupAd;
@property (nonatomic, copy) NSString *pendingDestination;
@end

@implementation TransitionPopupViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    [self loadPopup];
}

- (void)loadPopup {
    // 1. 인스턴스 생성 (placementUid)
    self.popupAd = [[AdWhaleMediationPopupAd alloc]
                    initWithPlacementUid:@"발급받은 PLACEMENT_UID 값"];

    // 2. 델리게이트 등록
    [self.popupAd setAdWhaleMediationPopupAdDelegate:self];

    // 3. 로드
    [self.popupAd loadAd];
}

- (void)onTransitionRequested:(NSString *)destination {
    self.pendingDestination = destination;
    // 4. 노출
    [self.popupAd showFrom:self];
}

- (void)popupAdDidLoad:(AdWhaleMediationPopupAd *)ad {
    NSLog(@"전환 팝업 준비 완료");
}

- (void)popupAd:(AdWhaleMediationPopupAd *)ad
didFailToLoadWithError:(NSInteger)statusCode message:(NSString *)message {
    NSLog(@"전환 팝업 로드 실패 (%ld): %@", (long)statusCode, message);
}

- (void)popupAd:(AdWhaleMediationPopupAd *)ad
didFailToShowWithError:(NSInteger)statusCode message:(NSString *)message {
    // 재고 부재(300) · 미로드(200) 등 노출 실패 — 광고 없이 곧바로 화면 전환
    [self navigateToPendingDestination];
}

- (void)popupAd:(AdWhaleMediationPopupAd *)ad
didCloseWithReason:(AdWhalePopupCloseReason)reason {
    // 닫힘 사유와 관계없이 원래 하려던 화면 전환을 이어서 수행
    [self navigateToPendingDestination];
    [self loadPopup];   // 다음 전환을 위해 미리 로드
}

- (void)navigateToPendingDestination {
    if (self.pendingDestination == nil) { return; }
    NSLog(@"이동: %@", self.pendingDestination);
    self.pendingDestination = nil;
}

- (void)dealloc {
    // 5. 폐기
    [self.popupAd destroy];
    self.popupAd = nil;
}

@end
```
{% endtab %}
{% endtabs %}

### 7. 주의사항

**광고 로드 타이밍**

* `loadAd()` 는 SDK 초기화 완료(`initialize` 완료 콜백) 이후에 호출하세요. 초기화 전에 호출하면 `200`(`SDK not initialized.`)이 통지됩니다.
* 로드되지 않은 상태에서 `show(from:)` 을 호출하면 `200`(`No transition popup ad is ready to show.`)이 통지됩니다.
* 광고 네트워크가 성공·실패 어느 콜백도 돌려주지 않으면, SDK 가 네트워크당 지면별 설정 시간(**10\~30초**, 기본 10초)을 기다린 뒤 다음 순위 네트워크로 진행합니다. 이 만료는 별도 콜백으로 통지되지 않으며, 워터폴이 모두 소진된 시점에 실패 콜백이 1회 옵니다.

**로드 성공은 잠정 성공**

* AdFit 팝업은 로드와 표시가 한 번에 이뤄지므로, `popupAdDidLoad(_:)` 는 재고가 확정된 성공이 아닙니다.
* **노출 실패에 300 이 올 수 있는 유일한 애드폼**입니다. 300 은 재고 부재이며 코드 수정 대상이 아닙니다.

**플로우 대체 경로**

* **로드 성공 콜백만 믿고 화면 전환 플로우를 팝업에 의존하지 마세요.** 재고가 없으면 `didFailToShowWithError(300)` 이 오므로, 노출 실패 경로에서 곧바로 화면 전환을 이어서 수행해야 사용자가 다음 동작을 할 수 없는 상태가 되지 않습니다.

**닫힘 사유 처리**

* 닫힘 사유는 광고 네트워크마다 다릅니다(AdFit 은 `.ambiguousCancel` · `.todayDismiss`). 사유로 화면 전환 여부를 정하지 말고, 닫히면 원래 하려던 화면 전환을 이어서 수행하세요.

**델리게이트 등록**

* 델리게이트를 설정하지 않으면 로드·노출 실패가 어디에도 전달되지 않아 **"광고도 안 나오고 콜백도 없는"** 상태가 됩니다. SDK 는 이 경우 `통지할 리스너가 없습니다` 경고 로그를 남깁니다.
* 필수 콜백은 `popupAdDidLoad(_:)` 와 `popupAd(_:didFailToLoadWithError:message:)` 두 개입니다. 나머지 네 개는 optional 이지만, **`popupAd(_:didFailToShowWithError:message:)` 를 구현하지 않으면 재고 부재(`300`)를 알 수 없어 화면 전환 플로우가 막힙니다.**

**인스턴스 보유**

* SDK 는 델리게이트를 `weak` 로 참조합니다. 광고 인스턴스를 앱이 강하게 보유하지 않으면 콜백이 도착하기 전에 해제되어 "광고도 안 나오고 콜백도 없는" 상태가 됩니다.

**리소스 해제**

* `deinit` 또는 화면 종료 시 반드시 `destroy()` 를 호출하세요.

**에러 처리**

* `didFailToLoadWithError` 와 `didFailToShowWithError` 이벤트에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.

**테스트**

* 개발 환경에서는 테스트용 placement UID 를 사용하세요.
* 재고가 없는 상황(노출 실패 300)까지 포함해 테스트하세요.
