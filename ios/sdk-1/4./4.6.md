# 팝업

{% hint style="info" %}
팝업 광고는 앱 종료 시점이나 앱 내 화면 전환 시점에 다이얼로그 형태로 표시되는 광고입니다. 이 문서는 iOS 프로젝트에서 AdWhale Mediation SDK 를 사용하여 팝업 광고를 연동하는 방법을 설명합니다.
{% endhint %}

{% hint style="info" %}
iOS 광고 네트워크(AdFit)는 **종료 / 전환 구분 없이 단일 팝업 형식**만 제공하므로, AdWhale iOS SDK 도 팝업을 하나의 `AdWhaleMediationPopupAd` 로 통합했습니다. (Android 는 `앱 종료` 팝업 / `앱 내 화면 전환` 팝업 페이지가 분리되어 있습니다) 두 용도의 구현 차이는 **호출 시점과 닫힘 사유 처리**뿐이며, 아래 7 · 8 절에서 각각 다룹니다.
{% endhint %}

**1. 주요특징**

* 앱 종료 시점 · 앱 내 화면 전환 시점에 노출하는 팝업형 광고
* 버튼 텍스트 · 설명 문구 커스터마이즈 지원 (`setCustomizeButtonText`, `setCustomDescription`)
* 닫힘 사유를 콜백으로 전달 (`AdWhalePopupCloseReason` 7종)
* 로드 · 노출을 2단계로 통지 (재고 확정은 노출 시점)
* 다양한 옵션 설정 지원 (`placementName`, `region`, `setGeocoder`)
* 로드 · 노출 · 클릭 · 닫힘 등 이벤트 기반 델리게이트 콜백 시스템으로 광고 상태 추적 가능

| 항목      | 내용                        |
| ------- | ------------------------- |
| 클래스     | `AdWhaleMediationPopupAd` |
| 지원 네트워크 | AdFit                     |
| 노출      | `show(from:)`             |

**2. 기본 구현 샘플코드**

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

        // 3. (선택) 버튼 · 설명 텍스트 커스터마이즈
        ad.setCustomizeButtonText(primaryButtonText: "종료", secondaryButtonText: "취소")
        ad.setCustomDescription("앱을 종료하시겠습니까?")

        popupAd = ad

        // 4. 로드
        ad.loadAd()
    }

    /// 사용자가 종료(또는 화면 전환)를 시도할 때 호출
    func onPopupRequested() {
        // 5. 노출 (로드 완료 후)
        popupAd?.show(from: self)
    }

    // MARK: - AdWhaleMediationPopupAdDelegate

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didLoadWith responseInfo: AdWhaleMediationResponseInfo) {
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
        // 6. 폐기
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

        // 3. (선택) 버튼 · 설명 텍스트 커스터마이즈
        ad.setCustomizeButtonText(primaryButtonText: "종료", secondaryButtonText: "취소")
        ad.setCustomDescription("앱을 종료하시겠습니까?")

        popupAd = ad

        // 4. 로드
        ad.loadAd()
    }

    /// 5. 노출 (로드 완료 후)
    func show() {
        guard let top = Self.topViewController() else { return }
        popupAd?.show(from: top)
    }

    /// 6. 폐기
    func release() {
        popupAd?.destroy()
        popupAd = nil
    }

    // MARK: - AdWhaleMediationPopupAdDelegate

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didLoadWith responseInfo: AdWhaleMediationResponseInfo) { }

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

    // 3. (선택) 버튼 · 설명 텍스트 커스터마이즈
    [self.popupAd setCustomizeButtonTextWithPrimaryButtonText:@"종료" secondaryButtonText:@"취소"];
    [self.popupAd setCustomDescription:@"앱을 종료하시겠습니까?"];

    // 4. 로드
    [self.popupAd loadAd];
}

- (void)onPopupRequested {
    // 5. 노출 (로드 완료 후)
    [self.popupAd showFrom:self];
}

// 로드 성공 (잠정)
- (void)popupAd:(AdWhaleMediationPopupAd *)ad
    didLoadWith:(AdWhaleMediationResponseInfo *)responseInfo { }

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
    // 6. 폐기
    [self.popupAd destroy];
    self.popupAd = nil;
}

@end
```
{% endtab %}
{% endtabs %}

**3. API 설명**

**AdWhaleMediationPopupAd 클래스 API 설명**

```swift
public init(placementUid: String)
```

| 파라미터 타입  | 파라미터 값                |
| -------- | --------------------- |
| `String` | placementUid 값(발급 필요) |

```swift
public weak var delegate: AdWhaleMediationPopupAdDelegate?   // 콜백 델리게이트 (Swift)
public func setAdWhaleMediationPopupAdDelegate(_ delegate: AdWhaleMediationPopupAdDelegate?)   // Objective-C
```

| 파라미터 타입                           | 파라미터 값                  |
| --------------------------------- | ----------------------- |
| `AdWhaleMediationPopupAdDelegate` | 팝업 미디에이션 광고 호출 콜백 델리게이트 |

```swift
public func setCustomizeButtonText(primaryButtonText: String, secondaryButtonText: String)   // 버튼 텍스트 커스터마이즈 (옵션)
```

| 파라미터 타입  | 파라미터 값                        |
| -------- | ----------------------------- |
| `String` | 기본 버튼 텍스트 (예: `"종료"`, `"이동"`) |
| `String` | 보조 버튼 텍스트 (예: `"취소"`)         |

```swift
public func setCustomDescription(_ descriptionText: String)   // 설명 문구 커스터마이즈 (옵션)
```

| 파라미터 타입  | 파라미터 값           |
| -------- | ---------------- |
| `String` | 팝업 상단에 표시할 설명 문구 |

```swift
public func loadAd()   // 미디에이션 팝업 광고 로드
```

```swift
public func show(from viewController: UIViewController)   // 광고 로드 후 노출할 때 호출
```

| 파라미터 타입            | 파라미터 값                                             |
| ------------------ | -------------------------------------------------- |
| `UIViewController` | 노출 기준 ViewController (`view.window != nil` 상태여야 함) |

```swift
public func resume()    // 팝업 재노출 준비
```

```swift
public func destroy()   // deinit 시 호출 혹은 더 이상 광고를 요청하지 않고 싶을 때 호출
```

**AdWhaleMediationPopupAdDelegate 프로토콜 API 설명**

| 델리게이트 메서드                                    | 호출 시점                    |
| -------------------------------------------- | ------------------------ |
| `popupAd(_:didLoadWith:)`                    | 로드 성공 (잠정)               |
| `popupAd(_:didFailToLoadWithError:message:)` | 로드 실패                    |
| `popupAdDidShow(_:)`                         | 노출 성공 (optional)         |
| `popupAd(_:didFailToShowWithError:message:)` | 노출 실패 · 재고 부재 (optional) |
| `popupAdDidClick(_:)`                        | 클릭 (optional)            |
| `popupAd(_:didCloseWithReason:)`             | 닫힘 (사유 포함, optional)     |

```swift
func popupAd(_ ad: AdWhaleMediationPopupAd,
             didLoadWith responseInfo: AdWhaleMediationResponseInfo)   // 광고요청 성공 시 (잠정)
```

| 파라미터 타입                        | 파라미터 값                                       |
| ------------------------------ | -------------------------------------------- |
| `AdWhaleMediationResponseInfo` | 낙찰된 광고의 응답 정보 (`adNetworkName`, `revenue` 등) |

```swift
func popupAd(_ ad: AdWhaleMediationPopupAd,
             didFailToLoadWithError statusCode: Int, message: String)   // 광고요청 실패 시
```

| 파라미터 타입  | 파라미터 값                                                                                                                                       |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `Int`    | <p><code>200</code> = 연동 오류(placementUid 오설정 등)<br>또는<br><code>300</code> = 광고를 채우지 못함(워터폴 모두 소진)</p>                                        |
| `String` | <p><code>Internal error occurred...</code> = 연동 오류 메시지<br>또는<br><code>Mediation network error occurred...</code> = 광고를 채우지 못함(워터폴 모두 소진)</p> |

```swift
@objc optional func popupAdDidShow(_ ad: AdWhaleMediationPopupAd)   // 광고 노출 후
```

```swift
@objc optional func popupAd(_ ad: AdWhaleMediationPopupAd,
                            didFailToShowWithError statusCode: Int, message: String)   // 광고 노출 실패 시
```

| 파라미터 타입  | 파라미터 값                                                                                                                                |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `Int`    | <p><code>200</code> = 연동 오류 (로드 전 <code>show()</code> 호출 등)<br>또는<br><code>300</code> = <strong>노출 시점에 광고 재고가 없음</strong> (팝업 전용)</p> |
| `String` | <p><code>Ad not ready</code> = 아직 로드되지 않은 상태에서 <code>show()</code> 호출<br>또는<br>재고 부재 메시지</p>                                          |

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

**4. 로드 성공은 "잠정" 성공입니다**

AdFit 팝업은 내부적으로 **로드와 표시가 한 번에** 이뤄지는 구조라 노출 없이 재고를 미리 확인할 수 없습니다. 따라서 SDK 는 **두 단계 모두 통지**합니다.

| 단계                    | 콜백                                                                | 의미                             |
| --------------------- | ----------------------------------------------------------------- | ------------------------------ |
| `loadAd()`            | `popupAd(_:didLoadWith:)`                                         | **잠정 성공.** 팝업 준비까지만 성공, 재고 미확인 |
| `show(from:)` → 노출 성공 | `popupAdDidShow(_:)`                                              | 실제 광고가 노출됨                     |
| `show(from:)` → 재고 없음 | `popupAd(_:didFailToShowWithError:message:)` **statusCode = 300** | 노출 시점에 확정된 광고 미충족              |

{% hint style="danger" %}
**팝업만 노출 실패에 300 이 올 수 있습니다.** 다른 애드폼의 노출 실패는 모두 200(연동 오류)입니다. 팝업의 300 은 "연동은 정상인데 광고 재고가 없었다" 는 뜻이므로 **코드 수정 대상이 아닙니다.**
{% endhint %}

**5. 닫힘 사유**

`popupAd(_:didCloseWithReason:)` 로 전달되는 `AdWhalePopupCloseReason` 값입니다.

| 값                  | 원시값 | 의미                 |
| ------------------ | --- | ------------------ |
| `.unknown`         | 0   | 알 수 없음             |
| `.primaryButton`   | 1   | 기본 버튼 (예: 종료 · 이동) |
| `.secondaryButton` | 2   | 보조 버튼 (예: 취소)      |
| `.outsideTouch`    | 4   | 팝업 외부 터치           |
| `.backPress`       | 8   | 뒤로 가기              |
| `.todayDismiss`    | 16  | 오늘 하루 보지 않기        |
| `.ambiguousCancel` | 32  | 사유 불명 취소           |

{% hint style="info" %}
**앱 종료 팝업**은 `.primaryButton` 에서 종료 처리를, **앱 내 화면 전환 팝업**은 `.primaryButton` 에서 화면 전환을 수행하고, 나머지 사유에서는 현재 화면을 유지하는 패턴을 권장합니다.
{% endhint %}

**6. 옵션 설정**

{% tabs %}
{% tab title="Swift" %}
```swift
popupAd.region = "서울시 강남구"                                // 지역 타게팅 전용 API(옵션)
popupAd.setGeocoder(latitude: 37.5665, longitude: 126.9780)   // 지역 타게팅 전용 API(옵션)
```

{% hint style="info" %}
AdWhale SDK 는 Cauly 네트워크를 지원하며, 광고 지역 타게팅을 위해 지역정보(`region`, `setGeocoder`)를 선택적으로 입력받고 있습니다. 팝업은 현재 AdFit 만 지원하므로 이 값은 전달되지 않습니다.
{% endhint %}

```swift
popupAd.placementName = "popup_exit"   // 레벨플레이 placement name 연동 전용 API (옵션)
```

```swift
// 버튼 · 설명 텍스트 커스터마이즈 (옵션)
popupAd.setCustomizeButtonText(primaryButtonText: "종료", secondaryButtonText: "취소")
popupAd.setCustomDescription("앱을 종료하시겠습니까?")
```

```swift
popupAd.destroy()   // 리소스 해제
```
{% endtab %}

{% tab title="SwiftUI" %}
```swift
// 광고 인스턴스를 보유한 ObservableObject 안에서 동일하게 설정합니다.
popupAd.region = "서울시 강남구"                                // 지역 타게팅 전용 API(옵션)
popupAd.setGeocoder(latitude: 37.5665, longitude: 126.9780)   // 지역 타게팅 전용 API(옵션)
popupAd.placementName = "popup_exit"                          // 레벨플레이 placement name (옵션)

popupAd.setCustomizeButtonText(primaryButtonText: "종료", secondaryButtonText: "취소")
popupAd.setCustomDescription("앱을 종료하시겠습니까?")

popupAd.destroy()   // 리소스 해제
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
popupAd.region = @"서울시 강남구";                              // 지역 타게팅 전용 API(옵션)
[popupAd setGeocoderWithLatitude:37.5665 longitude:126.9780];   // 지역 타게팅 전용 API(옵션)
popupAd.placementName = @"popup_exit";                          // 레벨플레이 placement name (옵션)

[popupAd setCustomizeButtonTextWithPrimaryButtonText:@"종료" secondaryButtonText:@"취소"];
[popupAd setCustomDescription:@"앱을 종료하시겠습니까?"];

[popupAd destroy];   // 리소스 해제
```
{% endtab %}
{% endtabs %}

| 프로퍼티 / 메서드                                                       | 설명                                    |
| ---------------------------------------------------------------- | ------------------------------------- |
| `setCustomizeButtonText(primaryButtonText:secondaryButtonText:)` | 기본 · 보조 버튼 텍스트                        |
| `setCustomDescription(_:)`                                       | 설명 문구                                 |
| `region`                                                         | 지역 타겟팅 (Cauly 전용)                     |
| `placementName`                                                  | LevelPlay placement 이름 (LevelPlay 전용) |
| `setGeocoder(latitude:longitude:)`                               | 좌표 타겟팅 (Cauly 전용)                     |
| `resume()` / `destroy()`                                         | 재노출 준비 / 리소스 해제                       |

**7. 앱 종료 팝업 샘플코드**

사용자가 앱을 종료하려는 시점에 팝업을 노출하고, `.primaryButton` 에서 종료 처리를 수행합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
import UIKit
import AdWhaleSDK

final class ExitPopupViewController: UIViewController, AdWhaleMediationPopupAdDelegate {

    private var popupAd: AdWhaleMediationPopupAd?

    override func viewDidLoad() {
        super.viewDidLoad()

        // 1. 인스턴스 생성 (placementUid)
        let ad = AdWhaleMediationPopupAd(placementUid: "발급받은 PLACEMENT_UID 값")

        // 2. 델리게이트 등록
        ad.delegate = self

        // 3. 종료 팝업 문구 설정
        ad.setCustomizeButtonText(primaryButtonText: "종료", secondaryButtonText: "취소")
        ad.setCustomDescription("앱을 종료하시겠습니까?")

        popupAd = ad

        // 4. 로드
        ad.loadAd()
    }

    /// 사용자가 종료를 시도할 때 호출
    func onExitRequested() {
        // 5. 노출
        popupAd?.show(from: self)
    }

    // MARK: - AdWhaleMediationPopupAdDelegate

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didLoadWith responseInfo: AdWhaleMediationResponseInfo) {
        // 잠정 성공 — 실제 재고는 show 시점에 확정된다
        print("팝업 준비 완료")
    }

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didFailToLoadWithError statusCode: Int, message: String) {
        print("팝업 로드 실패 (\(statusCode)): \(message)")
    }

    func popupAdDidShow(_ ad: AdWhaleMediationPopupAd) {
        print("팝업 노출됨")
    }

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didFailToShowWithError statusCode: Int, message: String) {
        if statusCode == 300 {
            // 재고 부재 — 연동 오류가 아니다. 자체 종료 확인 다이얼로그로 대체
            showPlainExitDialog()
        } else {
            print("팝업 노출 실패 (\(statusCode)): \(message)")
        }
    }

    func popupAdDidClick(_ ad: AdWhaleMediationPopupAd) {
        print("팝업 클릭")
    }

    func popupAd(_ ad: AdWhaleMediationPopupAd, didCloseWithReason reason: AdWhalePopupCloseReason) {
        switch reason {
        case .primaryButton:
            exit(0)                 // 종료 선택
        default:
            break                   // 취소 · 외부 터치 등 → 그대로 유지
        }
    }

    private func showPlainExitDialog() { /* 자체 다이얼로그 */ }

    deinit {
        // 6. 폐기
        popupAd?.destroy()
        popupAd = nil
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
final class ExitPopupAdController: NSObject, ObservableObject, AdWhaleMediationPopupAdDelegate {

    @Published var showPlainExitDialog = false

    private var popupAd: AdWhaleMediationPopupAd?

    func load() {
        // 1. 인스턴스 생성 (placementUid)
        let ad = AdWhaleMediationPopupAd(placementUid: "발급받은 PLACEMENT_UID 값")

        // 2. 델리게이트 등록
        ad.delegate = self

        // 3. 종료 팝업 문구 설정
        ad.setCustomizeButtonText(primaryButtonText: "종료", secondaryButtonText: "취소")
        ad.setCustomDescription("앱을 종료하시겠습니까?")

        popupAd = ad

        // 4. 로드
        ad.loadAd()
    }

    /// 5. 노출 — 사용자가 종료를 시도할 때 호출
    func requestExit() {
        guard let top = Self.topViewController() else {
            showPlainExitDialog = true
            return
        }
        popupAd?.show(from: top)
    }

    /// 6. 폐기
    func release() {
        popupAd?.destroy()
        popupAd = nil
    }

    // MARK: - AdWhaleMediationPopupAdDelegate

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didLoadWith responseInfo: AdWhaleMediationResponseInfo) {
        print("팝업 준비 완료")
    }

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didFailToLoadWithError statusCode: Int, message: String) {
        print("팝업 로드 실패 (\(statusCode)): \(message)")
    }

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didFailToShowWithError statusCode: Int, message: String) {
        if statusCode == 300 {
            // 재고 부재 — 자체 종료 확인 다이얼로그로 대체
            showPlainExitDialog = true
        } else {
            print("팝업 노출 실패 (\(statusCode)): \(message)")
        }
    }

    func popupAd(_ ad: AdWhaleMediationPopupAd, didCloseWithReason reason: AdWhalePopupCloseReason) {
        switch reason {
        case .primaryButton:
            exit(0)                 // 종료 선택
        default:
            break                   // 취소 · 외부 터치 등 → 그대로 유지
        }
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

struct ExitPopupScreen: View {
    @StateObject private var controller = ExitPopupAdController()

    var body: some View {
        Button("종료") { controller.requestExit() }
            .onAppear { controller.load() }
            .onDisappear { controller.release() }
            .alert("앱을 종료하시겠습니까?", isPresented: $controller.showPlainExitDialog) {
                Button("종료", role: .destructive) { exit(0) }
                Button("취소", role: .cancel) { }
            }
    }
}
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
#import <AdWhaleSDK/AdWhaleSDK-Swift.h>

@interface ExitPopupViewController () <AdWhaleMediationPopupAdDelegate>
@property (nonatomic, strong) AdWhaleMediationPopupAd *popupAd;
@end

@implementation ExitPopupViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    // 1. 인스턴스 생성 (placementUid)
    self.popupAd = [[AdWhaleMediationPopupAd alloc]
                    initWithPlacementUid:@"발급받은 PLACEMENT_UID 값"];

    // 2. 델리게이트 등록
    [self.popupAd setAdWhaleMediationPopupAdDelegate:self];

    // 3. 종료 팝업 문구 설정
    [self.popupAd setCustomizeButtonTextWithPrimaryButtonText:@"종료" secondaryButtonText:@"취소"];
    [self.popupAd setCustomDescription:@"앱을 종료하시겠습니까?"];

    // 4. 로드
    [self.popupAd loadAd];
}

- (void)onExitRequested {
    // 5. 노출
    [self.popupAd showFrom:self];
}

- (void)popupAd:(AdWhaleMediationPopupAd *)ad
    didLoadWith:(AdWhaleMediationResponseInfo *)responseInfo {
    NSLog(@"팝업 준비 완료");
}

- (void)popupAd:(AdWhaleMediationPopupAd *)ad
didFailToShowWithError:(NSInteger)statusCode message:(NSString *)message {
    if (statusCode == 300) {
        [self showPlainExitDialog];
    }
}

- (void)popupAd:(AdWhaleMediationPopupAd *)ad
didCloseWithReason:(AdWhalePopupCloseReason)reason {
    if (reason == AdWhalePopupCloseReasonPrimaryButton) {
        exit(0);
    }
}

- (void)dealloc {
    // 6. 폐기
    [self.popupAd destroy];
    self.popupAd = nil;
}

@end
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
iOS 에서는 `exit(0)` 로 앱을 강제 종료하는 것이 App Store 심사 지침상 권장되지 않습니다. 종료 대신 홈 화면 복귀 안내 · 화면 전환 등 앱 정책에 맞는 처리를 검토하세요.
{% endhint %}

**8. 앱 내 화면 전환 팝업 샘플코드**

앱 내 화면 전환 시점에 팝업을 노출하고, `.primaryButton` 에서 목표 화면으로 이동합니다. 구현은 종료 팝업과 동일하며 **버튼 문구와 닫힘 사유 처리만 다릅니다.**

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

        // 3. 전환 팝업 문구 설정
        ad.setCustomizeButtonText(primaryButtonText: "이동", secondaryButtonText: "취소")
        ad.setCustomDescription("다음 화면으로 이동합니다.")

        popupAd = ad

        // 4. 로드
        ad.loadAd()
    }

    /// 화면 전환을 시도할 때 호출
    func onTransitionRequested(to destination: String) {
        pendingDestination = destination
        // 5. 노출
        popupAd?.show(from: self)
    }

    // MARK: - AdWhaleMediationPopupAdDelegate

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didLoadWith responseInfo: AdWhaleMediationResponseInfo) {
        print("전환 팝업 준비 완료")
    }

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didFailToLoadWithError statusCode: Int, message: String) {
        print("전환 팝업 로드 실패 (\(statusCode)): \(message)")
    }

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didFailToShowWithError statusCode: Int, message: String) {
        if statusCode == 300 {
            // 재고 부재 — 광고 없이 곧바로 화면 전환
            navigateToPendingDestination()
        } else {
            print("전환 팝업 노출 실패 (\(statusCode)): \(message)")
        }
    }

    func popupAd(_ ad: AdWhaleMediationPopupAd, didCloseWithReason reason: AdWhalePopupCloseReason) {
        switch reason {
        case .primaryButton:
            navigateToPendingDestination()   // 이동 선택
        default:
            pendingDestination = nil         // 취소 · 외부 터치 등 → 현재 화면 유지
        }

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
        // 6. 폐기
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

        // 3. 전환 팝업 문구 설정
        ad.setCustomizeButtonText(primaryButtonText: "이동", secondaryButtonText: "취소")
        ad.setCustomDescription("다음 화면으로 이동합니다.")

        popupAd = ad

        // 4. 로드
        ad.loadAd()
    }

    /// 5. 노출 — 화면 전환을 시도할 때 호출
    func requestTransition(to destination: String) {
        pendingDestination = destination
        guard let top = Self.topViewController() else {
            navigateTo = destination
            pendingDestination = nil
            return
        }
        popupAd?.show(from: top)
    }

    /// 6. 폐기
    func release() {
        popupAd?.destroy()
        popupAd = nil
    }

    // MARK: - AdWhaleMediationPopupAdDelegate

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didLoadWith responseInfo: AdWhaleMediationResponseInfo) { }

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didFailToLoadWithError statusCode: Int, message: String) { }

    func popupAd(_ ad: AdWhaleMediationPopupAd,
                 didFailToShowWithError statusCode: Int, message: String) {
        if statusCode == 300 {
            // 재고 부재 — 광고 없이 곧바로 화면 전환
            navigateTo = pendingDestination
        }
        pendingDestination = nil
    }

    func popupAd(_ ad: AdWhaleMediationPopupAd, didCloseWithReason reason: AdWhalePopupCloseReason) {
        if reason == .primaryButton {
            navigateTo = pendingDestination
        }
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

    // 3. 전환 팝업 문구 설정
    [self.popupAd setCustomizeButtonTextWithPrimaryButtonText:@"이동" secondaryButtonText:@"취소"];
    [self.popupAd setCustomDescription:@"다음 화면으로 이동합니다."];

    // 4. 로드
    [self.popupAd loadAd];
}

- (void)onTransitionRequested:(NSString *)destination {
    self.pendingDestination = destination;
    // 5. 노출
    [self.popupAd showFrom:self];
}

- (void)popupAd:(AdWhaleMediationPopupAd *)ad
didFailToShowWithError:(NSInteger)statusCode message:(NSString *)message {
    if (statusCode == 300) {
        [self navigateToPendingDestination];   // 재고 부재 — 곧바로 화면 전환
    }
}

- (void)popupAd:(AdWhaleMediationPopupAd *)ad
didCloseWithReason:(AdWhalePopupCloseReason)reason {
    if (reason == AdWhalePopupCloseReasonPrimaryButton) {
        [self navigateToPendingDestination];
    } else {
        self.pendingDestination = nil;
    }
    [self loadPopup];   // 다음 전환을 위해 미리 로드
}

- (void)navigateToPendingDestination {
    if (self.pendingDestination == nil) { return; }
    NSLog(@"이동: %@", self.pendingDestination);
    self.pendingDestination = nil;
}

- (void)dealloc {
    // 6. 폐기
    [self.popupAd destroy];
    self.popupAd = nil;
}

@end
```
{% endtab %}
{% endtabs %}

**9. 주의사항**

**광고 로드 타이밍**

* `loadAd()` 는 SDK 초기화 완료(`initialize` 완료 콜백) 이후에 호출하는 것을 권장합니다.
* 로드되지 않은 상태에서 `show(from:)` 을 호출하면 **200 (`Ad not ready`)** 이 통지됩니다.

**로드 성공은 잠정 성공**

* AdFit 팝업은 로드와 표시가 한 번에 이뤄지므로, `popupAd(_:didLoadWith:)` 는 재고가 확정된 성공이 아닙니다.
* **노출 실패에 300 이 올 수 있는 유일한 애드폼**입니다. 300 은 재고 부재이며 코드 수정 대상이 아닙니다.

**플로우 대체 경로**

* **로드 성공 콜백만 믿고 종료 · 전환 플로우를 팝업에 의존하지 마세요.** 재고가 없으면 `didFailToShowWithError(300)` 이 오므로, 그 경로에서 자체 다이얼로그 또는 곧바로 화면 전환으로 대체해야 사용자가 다음 동작을 할 수 없는 상태가 되지 않습니다.

**닫힘 사유 처리**

* `popupAd(_:didCloseWithReason:)` 의 사유를 구분해 처리하세요. `.primaryButton` 외의 사유에서는 현재 화면을 유지하는 것이 일반적입니다.

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

{% hint style="danger" %}
**로드 성공 콜백만 믿고 종료 플로우를 팝업에 의존하지 마세요.** 재고가 없으면 `didFailToShowWithError(300)` 이 오므로, 그 경로에서 자체 종료 다이얼로그로 대체해야 사용자가 앱을 종료할 수 없는 상태가 되지 않습니다.
{% endhint %}

{% hint style="warning" %}
로드되지 않은 상태에서 `show(from:)` 을 호출하면 **200 (`Ad not ready`)** 이 통지됩니다.
{% endhint %}
