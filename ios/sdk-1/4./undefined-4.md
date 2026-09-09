# 네이티브

{% hint style="info" %}
네이티브 광고는 앱의 UI 와 어울리는 형태로 노출되는 광고입니다. 광고 구성 요소(아이콘, 제목, 본문, 미디어, 버튼)를 앱 디자인에 맞춰 배치할 수 있어 자연스러운 사용자 경험을 제공합니다. AdWhale Mediation iOS SDK 의 네이티브 광고는 **템플릿 방식**과 **커스텀 바인딩 방식** 2가지 형태의 구현과 사용이 가능합니다.
{% endhint %}

**1. 주요특징**

* 앱 UI 와 어울리는 형태로 노출
* 템플릿(SMALL / MEDIUM / FULLSCREEN) 3종 지원
* 템플릿 색 · 폰트 커스터마이즈 지원 (`templateStyle`)
* 커스텀 바인딩으로 앱이 만든 뷰에 광고 요소 직접 배치 가능
* 자동 갱신 기능
* 이벤트 기반 델리게이트 콜백으로 광고 상태 추적

| 항목      | 내용                                 |
| ------- | ---------------------------------- |
| 클래스     | `AdWhaleMediationNativeAdView`     |
| 지원 네트워크 | AdMob, AdManager, Cauly, LevelPlay |
| 노출      | `show()`                           |

**2. 네이티브 광고 타입**

| 방식           | 메서드                 | 설명                                 |
| ------------ | ------------------- | ---------------------------------- |
| 템플릿 (고정 템플릿) | `loadAd(template:)` | SDK 가 제공하는 레이아웃 사용. 색 · 폰트만 커스터마이즈 |
| 커스텀 바인딩      | `loadAd(binder:)`   | 앱이 만든 뷰에 광고 요소를 바인딩                |

**템플릿 종류**

| `AdWhaleNativeTemplate` | 값                                  | 설명           |
| ----------------------- | ---------------------------------- | ------------ |
| `.small`                | `AdWhaleNativeTemplate.small`      | 소형 (리스트 셀 등) |
| `.medium`               | `AdWhaleNativeTemplate.medium`     | 중형 (미디어 포함)  |
| `.fullscreen`           | `AdWhaleNativeTemplate.fullscreen` | 전체 화면        |

{% hint style="info" %}
템플릿과 커스텀 바인딩 중 **한 번 선택한 방식이 자동 갱신에도 그대로 적용**됩니다. 방식을 바꾸려면 `loadAd(template:)` 또는 `loadAd(binder:)` 를 다시 호출하세요.
{% endhint %}

**3. 템플릿 네이티브 광고**

`AdWhaleMediationNativeAdView` 와 `loadAd(template:)` 을 사용하여 SDK 가 제공하는 레이아웃으로 네이티브 광고를 노출합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
import UIKit
import AdWhaleSDK

final class NativeViewController: UIViewController, AdWhaleMediationNativeAdDelegate {

    // 1. 인스턴스 생성
    private let nativeAdView = AdWhaleMediationNativeAdView()

    override func viewDidLoad() {
        super.viewDidLoad()

        nativeAdView.translatesAutoresizingMaskIntoConstraints = false

        // 2. 화면 계층에 추가 (★ loadAd() 보다 먼저)
        view.addSubview(nativeAdView)
        NSLayoutConstraint.activate([
            nativeAdView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 16),
            nativeAdView.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 16),
            nativeAdView.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -16),
            nativeAdView.heightAnchor.constraint(equalToConstant: 340),
        ])

        // 3. 지면 등록 (placementUid)
        nativeAdView.placementUid = "발급받은 PLACEMENT_UID 값"

        // 4. 델리게이트 등록
        nativeAdView.delegate = self

        // 5. (선택) 템플릿 스타일 설정
        nativeAdView.templateStyle = AdWhaleMediationNativeTemplateStyle.Builder()
            .setMainBackgroundColor(.white)
            .setPrimaryTextColor(.label)
            .setPrimaryTextFont(.boldSystemFont(ofSize: 15))
            .setCallToActionBackgroundColor(.systemBlue)
            .setCallToActionTextColor(.white)
            .build()

        // 6. 로드 (템플릿 지정)
        nativeAdView.loadAd(template: .medium)
    }

    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        nativeAdView.resume()   // 자동 갱신 재개
    }

    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        nativeAdView.pause()    // 자동 갱신 일시정지
    }

    deinit {
        // 8. 폐기
        nativeAdView.destroy()
    }

    // MARK: - AdWhaleMediationNativeAdDelegate

    func nativeAd(_ adView: AdWhaleMediationNativeAdView,
                  didLoadWith responseInfo: AdWhaleMediationResponseInfo) {
        // 7. 노출 (로드 완료 후)
        adView.show()
    }

    func nativeAd(_ adView: AdWhaleMediationNativeAdView,
                  didFailToLoadWithError statusCode: Int, message: String) {
        if statusCode == 300, message.contains("Previous ad is still showing.") {
            return         // 갱신만 실패, 광고는 노출 중
        }
        print("네이티브 로드 실패 (\(statusCode)): \(message)")
    }

    func nativeAd(_ adView: AdWhaleMediationNativeAdView,
                  didFailToShowWithError statusCode: Int, message: String) {
        print("네이티브 노출 실패 (\(statusCode)): \(message)")
    }

    func nativeAdDidClick(_ adView: AdWhaleMediationNativeAdView) {
        print("네이티브 클릭")
    }

    func nativeAdDidClose(_ adView: AdWhaleMediationNativeAdView) {
        print("네이티브 닫힘")
    }
}
```
{% endtab %}

{% tab title="SwiftUI" %}
```swift
import SwiftUI
import UIKit
import AdWhaleSDK

// 네이티브 광고는 UIView 이므로 UIViewRepresentable 로 감싸 사용합니다.
struct TemplateNativeRepresentable: UIViewRepresentable {

    let placementUid: String
    let template: AdWhaleNativeTemplate

    func makeCoordinator() -> Coordinator { Coordinator() }

    func makeUIView(context: Context) -> UIView {
        let container = UIView()

        // 1. 인스턴스 생성
        let native = AdWhaleMediationNativeAdView()

        // 2. 지면 등록 / 3. 델리게이트 등록
        native.placementUid = placementUid
        native.delegate = context.coordinator

        // 4. (선택) 템플릿 스타일 설정
        native.templateStyle = AdWhaleMediationNativeTemplateStyle.Builder()
            .setMainBackgroundColor(.white)
            .setPrimaryTextColor(.label)
            .setCallToActionBackgroundColor(.systemBlue)
            .setCallToActionTextColor(.white)
            .build()

        native.translatesAutoresizingMaskIntoConstraints = false
        container.addSubview(native)   // ★ loadAd() 보다 먼저
        NSLayoutConstraint.activate([
            native.topAnchor.constraint(equalTo: container.topAnchor),
            native.bottomAnchor.constraint(equalTo: container.bottomAnchor),
            native.leadingAnchor.constraint(equalTo: container.leadingAnchor),
            native.trailingAnchor.constraint(equalTo: container.trailingAnchor),
        ])
        context.coordinator.native = native

        // 5. 로드 — 뷰가 window 계층에 붙은 다음 프레임에 호출
        DispatchQueue.main.async { native.loadAd(template: template) }
        return container
    }

    func updateUIView(_ uiView: UIView, context: Context) { }

    // 7. 폐기
    static func dismantleUIView(_ uiView: UIView, coordinator: Coordinator) {
        coordinator.native?.destroy()
    }

    final class Coordinator: NSObject, AdWhaleMediationNativeAdDelegate {
        var native: AdWhaleMediationNativeAdView?

        func nativeAd(_ adView: AdWhaleMediationNativeAdView,
                      didLoadWith responseInfo: AdWhaleMediationResponseInfo) {
            adView.show()   // 6. 노출 (로드 완료 후)
        }

        func nativeAd(_ adView: AdWhaleMediationNativeAdView,
                      didFailToLoadWithError statusCode: Int, message: String) {
            if statusCode == 300, message.contains("Previous ad is still showing.") { return }
            print("네이티브 로드 실패 (\(statusCode)): \(message)")
        }

        func nativeAd(_ adView: AdWhaleMediationNativeAdView,
                      didFailToShowWithError statusCode: Int, message: String) {
            print("네이티브 노출 실패 (\(statusCode)): \(message)")
        }

        func nativeAdDidClick(_ adView: AdWhaleMediationNativeAdView) { }
        func nativeAdDidClose(_ adView: AdWhaleMediationNativeAdView) { }
    }
}

struct NativeScreen: View {
    var body: some View {
        VStack {
            TemplateNativeRepresentable(placementUid: "발급받은 PLACEMENT_UID 값",
                                        template: .medium)
                .frame(height: 340)
                .padding(.horizontal, 16)
            Spacer()
        }
    }
}
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
#import <AdWhaleSDK/AdWhaleSDK-Swift.h>

@interface NativeViewController () <AdWhaleMediationNativeAdDelegate>
@property (nonatomic, strong) AdWhaleMediationNativeAdView *nativeAdView;
@end

@implementation NativeViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    // 1. 인스턴스 생성
    self.nativeAdView = [[AdWhaleMediationNativeAdView alloc] init];
    self.nativeAdView.translatesAutoresizingMaskIntoConstraints = NO;

    // 2. 화면 계층에 추가 (★ loadAd 보다 먼저)
    [self.view addSubview:self.nativeAdView];
    [NSLayoutConstraint activateConstraints:@[
        [self.nativeAdView.topAnchor constraintEqualToAnchor:self.view.safeAreaLayoutGuide.topAnchor constant:16],
        [self.nativeAdView.leadingAnchor constraintEqualToAnchor:self.view.leadingAnchor constant:16],
        [self.nativeAdView.trailingAnchor constraintEqualToAnchor:self.view.trailingAnchor constant:-16],
        [self.nativeAdView.heightAnchor constraintEqualToConstant:340],
    ]];

    // 3. 지면 등록 (placementUid)
    self.nativeAdView.placementUid = @"발급받은 PLACEMENT_UID 값";

    // 4. 델리게이트 등록
    [self.nativeAdView setAdWhaleMediationNativeAdDelegate:self];

    // 5. (선택) 템플릿 스타일 설정
    AdWhaleMediationNativeTemplateStyleBuilder *builder =
        [[AdWhaleMediationNativeTemplateStyleBuilder alloc] init];
    [builder setMainBackgroundColor:UIColor.whiteColor];
    [builder setCallToActionBackgroundColor:UIColor.systemBlueColor];
    [builder setCallToActionTextColor:UIColor.whiteColor];
    self.nativeAdView.templateStyle = [builder build];

    // 6. 로드 (템플릿 지정)
    [self.nativeAdView loadAdWithTemplate:AdWhaleNativeTemplateMedium];
}

- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];
    [self.nativeAdView resume];
}

- (void)viewWillDisappear:(BOOL)animated {
    [super viewWillDisappear:animated];
    [self.nativeAdView pause];
}

- (void)dealloc {
    // 8. 폐기
    [self.nativeAdView destroy];
}

// 로드 성공
- (void)nativeAd:(AdWhaleMediationNativeAdView *)adView
     didLoadWith:(AdWhaleMediationResponseInfo *)responseInfo {
    // 7. 노출 (로드 완료 후)
    [adView show];
}

// 로드 실패
- (void)nativeAd:(AdWhaleMediationNativeAdView *)adView
didFailToLoadWithError:(NSInteger)statusCode message:(NSString *)message {
    if (statusCode == 300 && [message containsString:@"Previous ad is still showing."]) {
        return;
    }
    NSLog(@"네이티브 로드 실패 (%ld): %@", (long)statusCode, message);
}

// 노출 실패
- (void)nativeAd:(AdWhaleMediationNativeAdView *)adView
didFailToShowWithError:(NSInteger)statusCode message:(NSString *)message {
    NSLog(@"네이티브 노출 실패 (%ld): %@", (long)statusCode, message);
}

// 클릭 / 닫힘
- (void)nativeAdDidClick:(AdWhaleMediationNativeAdView *)adView { }
- (void)nativeAdDidClose:(AdWhaleMediationNativeAdView *)adView { }

@end
```
{% endtab %}
{% endtabs %}

**템플릿 스타일 설정**

`AdWhaleMediationNativeTemplateStyle.Builder` 로 템플릿의 색과 폰트를 커스터마이즈할 수 있습니다.

| Builder 메서드                                                                                           | 설명                                       |
| ----------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| `setMainBackgroundColor(_:)`                                                                          | 광고 전체 배경색                                |
| `setPrimaryTextFont(_:)` / `setPrimaryTextColor(_:)` / `setPrimaryTextBackgroundColor(_:)`            | 제목 텍스트                                   |
| `setSecondaryTextFont(_:)` / `setSecondaryTextColor(_:)` / `setSecondaryTextBackgroundColor(_:)`      | 본문 텍스트                                   |
| `setTertiaryTextFont(_:)` / `setTertiaryTextColor(_:)` / `setTertiaryTextBackgroundColor(_:)`         | 부가 텍스트                                   |
| `setCallToActionTextFont(_:)` / `setCallToActionTextColor(_:)` / `setCallToActionBackgroundColor(_:)` | CTA 버튼                                   |
| `build()`                                                                                             | `AdWhaleMediationNativeTemplateStyle` 생성 |

**4. 커스텀 네이티브 광고**

앱이 만든 뷰를 `AdWhaleMediationNativeAdBindingHelper` 로 전달해 광고 요소를 직접 배치합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
final class CustomNativeViewController: UIViewController, AdWhaleMediationNativeAdDelegate {

    private let nativeAdView = AdWhaleMediationNativeAdView()

    // 앱이 만든 광고 요소 뷰
    private let icon = UIImageView()
    private let title = UILabel()
    private let body = UILabel()
    private let cta = UIButton(type: .system)
    private let media = UIView()

    override func viewDidLoad() {
        super.viewDidLoad()

        // 1. 광고 뷰를 화면 계층에 추가 (★ loadAd() 보다 먼저)
        view.addSubview(nativeAdView)

        // 2. 광고 요소 뷰를 앱 레이아웃에 배치 (★ 크기가 0 이면 광고가 보이지 않음)
        layoutAdElements()

        // 3. 지면 등록 / 델리게이트 등록
        nativeAdView.placementUid = "발급받은 PLACEMENT_UID 값"
        nativeAdView.delegate = self

        // 4. 바인더 생성
        let binder = AdWhaleMediationNativeAdBindingHelper.Builder()
            .setIconView(icon)
            .setTitleView(title)
            .setBodyView(body)
            .setCallToActionView(cta)
            .setMediaView(media)
            .build()

        // 5. 로드 (바인더 지정)
        nativeAdView.loadAd(binder: binder)
    }

    private func layoutAdElements() { /* 앱 레이아웃 구성 */ }

    deinit {
        // 7. 폐기
        nativeAdView.destroy()
    }

    // MARK: - AdWhaleMediationNativeAdDelegate

    func nativeAd(_ adView: AdWhaleMediationNativeAdView,
                  didLoadWith responseInfo: AdWhaleMediationResponseInfo) {
        adView.show()   // 6. 노출
    }

    func nativeAd(_ adView: AdWhaleMediationNativeAdView,
                  didFailToLoadWithError statusCode: Int, message: String) {
        print("네이티브 로드 실패 (\(statusCode)): \(message)")
    }
}
```
{% endtab %}

{% tab title="SwiftUI" %}
```swift
struct CustomBindingNativeRepresentable: UIViewRepresentable {

    let placementUid: String

    func makeCoordinator() -> Coordinator { Coordinator() }

    func makeUIView(context: Context) -> UIView {
        let container = UIView()

        // 1. 광고 요소 뷰를 앱 레이아웃에 배치 (★ 크기가 0 이면 광고가 보이지 않음)
        let icon = UIImageView()
        let title = UILabel()
        let body = UILabel()
        let cta = UIButton(type: .system)
        let media = UIView()

        let stack = UIStackView(arrangedSubviews: [icon, title, body, media, cta])
        stack.axis = .vertical
        stack.spacing = 8
        stack.translatesAutoresizingMaskIntoConstraints = false

        // 2. 광고 뷰 생성 및 화면 계층에 추가
        let native = AdWhaleMediationNativeAdView()
        native.placementUid = placementUid
        native.delegate = context.coordinator
        native.translatesAutoresizingMaskIntoConstraints = false

        container.addSubview(native)
        native.addSubview(stack)
        NSLayoutConstraint.activate([
            native.topAnchor.constraint(equalTo: container.topAnchor),
            native.bottomAnchor.constraint(equalTo: container.bottomAnchor),
            native.leadingAnchor.constraint(equalTo: container.leadingAnchor),
            native.trailingAnchor.constraint(equalTo: container.trailingAnchor),
            stack.topAnchor.constraint(equalTo: native.topAnchor),
            stack.bottomAnchor.constraint(equalTo: native.bottomAnchor),
            stack.leadingAnchor.constraint(equalTo: native.leadingAnchor),
            stack.trailingAnchor.constraint(equalTo: native.trailingAnchor),
            icon.heightAnchor.constraint(equalToConstant: 48),
            media.heightAnchor.constraint(equalToConstant: 180),
        ])
        context.coordinator.native = native

        // 3. 바인더 생성
        let binder = AdWhaleMediationNativeAdBindingHelper.Builder()
            .setIconView(icon)
            .setTitleView(title)
            .setBodyView(body)
            .setCallToActionView(cta)
            .setMediaView(media)
            .build()

        // 4. 로드 — 뷰가 window 계층에 붙은 다음 프레임에 호출
        DispatchQueue.main.async { native.loadAd(binder: binder) }
        return container
    }

    func updateUIView(_ uiView: UIView, context: Context) { }

    static func dismantleUIView(_ uiView: UIView, coordinator: Coordinator) {
        coordinator.native?.destroy()
    }

    final class Coordinator: NSObject, AdWhaleMediationNativeAdDelegate {
        var native: AdWhaleMediationNativeAdView?

        func nativeAd(_ adView: AdWhaleMediationNativeAdView,
                      didLoadWith responseInfo: AdWhaleMediationResponseInfo) {
            adView.show()
        }

        func nativeAd(_ adView: AdWhaleMediationNativeAdView,
                      didFailToLoadWithError statusCode: Int, message: String) {
            print("네이티브 로드 실패 (\(statusCode)): \(message)")
        }
    }
}
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
- (void)viewDidLoad {
    [super viewDidLoad];

    // 1. 광고 뷰를 화면 계층에 추가 (★ loadAd 보다 먼저)
    self.nativeAdView = [[AdWhaleMediationNativeAdView alloc] init];
    [self.view addSubview:self.nativeAdView];

    // 2. 광고 요소 뷰를 앱 레이아웃에 배치
    [self layoutAdElements];

    // 3. 지면 등록 / 델리게이트 등록
    self.nativeAdView.placementUid = @"발급받은 PLACEMENT_UID 값";
    [self.nativeAdView setAdWhaleMediationNativeAdDelegate:self];

    // 4. 바인더 생성
    AdWhaleMediationNativeAdBindingHelperBuilder *builder =
        [[AdWhaleMediationNativeAdBindingHelperBuilder alloc] init];
    [builder setIconView:self.icon];
    [builder setTitleView:self.title];
    [builder setBodyView:self.body];
    [builder setCallToActionView:self.cta];
    [builder setMediaView:self.media];

    // 5. 로드 (바인더 지정)
    [self.nativeAdView loadAdWithBinder:[builder build]];
}

// 로드 성공 → 6. 노출
- (void)nativeAd:(AdWhaleMediationNativeAdView *)adView
     didLoadWith:(AdWhaleMediationResponseInfo *)responseInfo {
    [adView show];
}

- (void)dealloc {
    // 7. 폐기
    [self.nativeAdView destroy];
}
```
{% endtab %}
{% endtabs %}

**바인딩 요소**

| Builder 메서드               | 뷰 타입          | 광고 요소                                      |
| ------------------------- | ------------- | ------------------------------------------ |
| `setIconView(_:)`         | `UIImageView` | 아이콘                                        |
| `setTitleView(_:)`        | `UILabel`     | 제목                                         |
| `setBodyView(_:)`         | `UILabel`     | 본문                                         |
| `setCallToActionView(_:)` | `UIButton`    | CTA 버튼                                     |
| `setMediaView(_:)`        | `UIView`      | 이미지 · 동영상 미디어                              |
| `build()`                 | —             | `AdWhaleMediationNativeAdBindingHelper` 생성 |

{% hint style="warning" %}
바인딩할 뷰는 **미리 앱 레이아웃에 배치**해 두어야 합니다. 크기가 0 인 뷰를 넘기면 광고가 보이지 않습니다.
{% endhint %}

**5. API 설명**

**AdWhaleMediationNativeAdView 클래스 API 설명**

```swift
public init()   // UIView 기본 이니셜라이저
```

```swift
public var placementUid: String   // 지면 등록
```

| 파라미터 타입  | 파라미터 값                |
| -------- | --------------------- |
| `String` | placementUid 값(발급 필요) |

```swift
public weak var delegate: AdWhaleMediationNativeAdDelegate?   // 콜백 델리게이트 (Swift)
public func setAdWhaleMediationNativeAdDelegate(_ delegate: AdWhaleMediationNativeAdDelegate?)   // Objective-C
```

| 파라미터 타입                            | 파라미터 값                    |
| ---------------------------------- | ------------------------- |
| `AdWhaleMediationNativeAdDelegate` | 네이티브 미디에이션 광고 호출 콜백 델리게이트 |

```swift
public var templateStyle: AdWhaleMediationNativeTemplateStyle?   // 고정 템플릿의 색 · 폰트 커스터마이즈
```

| 파라미터 타입                               | 파라미터 값                       |
| ------------------------------------- | ---------------------------- |
| `AdWhaleMediationNativeTemplateStyle` | `Builder` 로 생성한 템플릿 스타일 (옵션) |

```swift
public func loadAd(template: AdWhaleNativeTemplate)   // 템플릿 방식으로 네이티브 광고 로드
```

| 파라미터 타입                 | 파라미터 값                                      |
| ----------------------- | ------------------------------------------- |
| `AdWhaleNativeTemplate` | 템플릿 종류 (`.small`, `.medium`, `.fullscreen`) |

```swift
public func loadAd(binder: AdWhaleMediationNativeAdBindingHelper)   // 커스텀 바인딩 방식으로 네이티브 광고 로드
```

| 파라미터 타입                                 | 파라미터 값                     |
| --------------------------------------- | -------------------------- |
| `AdWhaleMediationNativeAdBindingHelper` | `Builder` 로 생성한 앱 뷰 바인딩 정보 |

```swift
public func show()      // 광고 로드 후 노출할 때 호출
```

```swift
public func resume()    // 화면 복귀(viewWillAppear 등) 시 호출 필요 — 자동 갱신 재개
```

```swift
public func pause()     // 화면 이탈(viewWillDisappear 등) 시 호출 필요 — 자동 갱신 일시정지
```

```swift
public func stop()      // 갱신을 완전히 멈출 때 호출 (재개는 loadAd())
```

```swift
public func destroy()   // deinit 시 호출 필요 — 리소스 해제
```

**AdWhaleMediationNativeAdDelegate 프로토콜 API 설명**

| 델리게이트 메서드                                     | 호출 시점            |
| --------------------------------------------- | ---------------- |
| `nativeAd(_:didLoadWith:)`                    | 로드 성공            |
| `nativeAd(_:didFailToLoadWithError:message:)` | 로드 실패            |
| `nativeAd(_:didFailToShowWithError:message:)` | 노출 실패 (optional) |
| `nativeAdDidClick(_:)`                        | 클릭 (optional)    |
| `nativeAdDidClose(_:)`                        | 닫힘 (optional)    |

```swift
func nativeAd(_ adView: AdWhaleMediationNativeAdView,
              didLoadWith responseInfo: AdWhaleMediationResponseInfo)   // 광고요청 성공 시
```

| 파라미터 타입                        | 파라미터 값                                       |
| ------------------------------ | -------------------------------------------- |
| `AdWhaleMediationResponseInfo` | 낙찰된 광고의 응답 정보 (`adNetworkName`, `revenue` 등) |

```swift
func nativeAd(_ adView: AdWhaleMediationNativeAdView,
              didFailToLoadWithError statusCode: Int, message: String)   // 광고요청 실패 시
```

| 파라미터 타입  | 파라미터 값                                                                                                                                                                                                                                                     |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Int`    | <p><code>200</code> = 연동 오류(placementUid 오설정 등)<br>또는<br><code>300</code> = 광고를 채우지 못함(워터폴 모두 소진)</p>                                                                                                                                                      |
| `String` | <p><code>Internal error occurred...</code> = 연동 오류 메시지<br>또는<br><code>Mediation network error occurred...</code> = 최초 로드에서 광고를 채우지 못함<br>또는<br><code>Mediation network error occurred...Previous ad is still showing.</code> = 갱신 중 실패(이전 광고는 계속 노출 중)</p> |

```swift
@objc optional func nativeAd(_ adView: AdWhaleMediationNativeAdView,
                             didFailToShowWithError statusCode: Int, message: String)   // 광고 노출 실패 시
```

| 파라미터 타입  | 파라미터 값                             |
| -------- | ---------------------------------- |
| `Int`    | `200` = 연동 오류 (로드 전 `show()` 호출 등) |
| `String` | 노출 실패 메시지                          |

```swift
@objc optional func nativeAdDidClick(_ adView: AdWhaleMediationNativeAdView)   // 광고 클릭 시
@objc optional func nativeAdDidClose(_ adView: AdWhaleMediationNativeAdView)   // 광고 닫기 시
```

**6. 옵션 설정**

{% tabs %}
{% tab title="Swift" %}
```swift
nativeAdView.region = "서울시 강남구"                                // 지역 타게팅 전용 API(옵션)
nativeAdView.setGeocoder(latitude: 37.5665, longitude: 126.9780)   // 지역 타게팅 전용 API(옵션)
```

{% hint style="info" %}
AdWhale SDK 는 Cauly 네트워크를 지원하며, 광고 지역 타게팅을 위해 지역정보(`region`, `setGeocoder`)를 선택적으로 입력받고 있습니다.
{% endhint %}

```swift
nativeAdView.placementName = "native_main"   // 레벨플레이 placement name 연동 전용 API (옵션)
```

{% hint style="warning" %}
LevelPlay 콘솔에서 설정한 Placement 이름을 지정하면, 해당 Placement 에 설정된 설정이 적용된 광고가 노출됩니다. 설정하지 않으면 기본 Placement(Default Placement)가 사용됩니다.

***

네이티브 광고에는 **보상 조건, 제한 조건 등의 설정이 적용되지 않으며**, Placement 는 **광고 위치 구분 및 분석 용도**로만 사용됩니다.
{% endhint %}
{% endtab %}

{% tab title="SwiftUI" %}
```swift
// UIViewRepresentable 의 makeUIView 안에서 동일하게 설정합니다.
native.region = "서울시 강남구"                                // 지역 타게팅 전용 API(옵션)
native.setGeocoder(latitude: 37.5665, longitude: 126.9780)   // 지역 타게팅 전용 API(옵션)
native.placementName = "native_main"                         // 레벨플레이 placement name (옵션)
native.templateStyle = style                                 // 템플릿 스타일 (옵션)
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
nativeAdView.region = @"서울시 강남구";                              // 지역 타게팅 전용 API(옵션)
[nativeAdView setGeocoderWithLatitude:37.5665 longitude:126.9780];   // 지역 타게팅 전용 API(옵션)
nativeAdView.placementName = @"native_main";                         // 레벨플레이 placement name (옵션)
```
{% endtab %}
{% endtabs %}

| 프로퍼티 / 메서드                                      | 설명                                    |
| ----------------------------------------------- | ------------------------------------- |
| `templateStyle`                                 | 고정 템플릿의 색 · 폰트 커스터마이즈                 |
| `region`                                        | 지역 타겟팅 (Cauly 전용)                     |
| `placementName`                                 | LevelPlay placement 이름 (LevelPlay 전용) |
| `setGeocoder(latitude:longitude:)`              | 좌표 타겟팅 (Cauly 전용)                     |
| `resume()` / `pause()` / `stop()` / `destroy()` | 생명주기 제어                               |

**7. 주의사항**

**광고 로드 타이밍**

* `loadAd(...)` 는 SDK 초기화 완료(`initialize` 완료 콜백) 이후에 호출하는 것을 권장합니다.
* **`addSubview` → `loadAd(...)` 순서를 지키세요.** Cauly 는 responder chain 으로 부모 `UIViewController` 를 찾습니다.

**노출**

* 네이티브 광고는 로드 완료 후 `show()` 를 호출해야 노출됩니다. `nativeAd(_:didLoadWith:)` 에서 호출하세요.

**바인딩 뷰**

* 커스텀 바인딩에서 넘기는 뷰는 **미리 앱 레이아웃에 배치**해 두어야 합니다. 크기가 0 인 뷰를 넘기면 광고가 보이지 않습니다.

**라이프사이클**

* `viewWillAppear` / `viewWillDisappear` / `deinit` 에서 각각 `resume()` / `pause()` / `destroy()` 호출이 필요합니다.
* `pause()` 후 `resume()` 을 호출하지 않으면 자동 갱신이 재개되지 않습니다.

**방식 변경**

* 템플릿과 커스텀 바인딩 중 **한 번 선택한 방식이 자동 갱신에도 그대로 적용**됩니다. 방식을 바꾸려면 `loadAd(template:)` 또는 `loadAd(binder:)` 를 다시 호출하세요.

**에러 처리**

* 로드 실패 콜백에서 적절한 에러 처리를 구현하세요.
* 갱신 실패 메시지에 `Previous ad is still showing.` 이 붙어 있으면 광고는 계속 노출 중이므로 광고 영역을 숨기지 마세요.

**테스트**

* 개발 환경에서는 테스트용 placement UID 를 사용하세요.

{% hint style="danger" %}
**`addSubview` → `loadAd(...)` 순서를 지키세요.** Cauly 는 responder chain 으로 부모 `UIViewController` 를 찾습니다.
{% endhint %}

{% hint style="warning" %}
네이티브도 배너와 같이 `ad_reload_sec` 주기로 자동 갱신됩니다. 갱신 실패 메시지에 `Previous ad is still showing.` 이 붙어 있으면 광고는 계속 노출 중입니다.
{% endhint %}

{% hint style="info" %}
템플릿과 커스텀 바인딩 중 **한 번 선택한 방식이 자동 갱신에도 그대로 적용**됩니다. 방식을 바꾸려면 `loadAd(template:)` 또는 `loadAd(binder:)` 를 다시 호출하세요.
{% endhint %}
