# 4.3 프리로드 배너

{% hint style="info" %}
프리로드 배너는 광고를 **미리 로드해 두었다가 원하는 시점에 `show()` 로 노출**하는 배너 광고입니다. 화면이 열리기 전에 광고를 준비해 두면, 화면이 열리는 즉시 공백 없이 광고를 보여줄 수 있습니다.

일반 배너(`AdWhaleMediationAdView`)는 로드가 끝나면 바로 노출되지만, 프리로드 배너(`AdWhaleMediationAdBannerView`)는 **`show()` 를 호출해야 노출**됩니다. 코드(Programmatic) 방식과 Storyboard 방식 두 가지로 구현할 수 있습니다.
{% endhint %}

### 1. 주요 특징

* 로드와 노출 분리: `loadAd()` 로 미리 받아 두고 `show()` 로 원하는 시점에 노출
* 다양한 사이즈 지원 (320x50, 320x100, 300x250, 250x250, ADAPTIVE\_ANCHOR)
* 최초 `show()` 이후 자동 갱신 기능
* 코드(Programmatic) / Storyboard 방식 모두 지원
* 이벤트 기반 델리게이트 콜백으로 광고 상태 추적

<table><thead><tr><th width="239.484375">항목</th><th>내용</th></tr></thead><tbody><tr><td>클래스</td><td><code>AdWhaleMediationAdBannerView</code></td></tr><tr><td>지원 네트워크</td><td>AdMob, AdManager, AdFit, Admize, Cauly, LevelPlay</td></tr><tr><td>노출</td><td>로드 성공 후 <code>show()</code> 호출 시 노출</td></tr></tbody></table>

**일반 배너와의 차이**

<table><thead><tr><th width="129.859375">구분</th><th>일반 배너</th><th>프리로드 배너</th></tr></thead><tbody><tr><td>클래스</td><td><code>AdWhaleMediationAdView</code></td><td><code>AdWhaleMediationAdBannerView</code></td></tr><tr><td>델리게이트</td><td><code>AdWhaleMediationAdViewDelegate</code></td><td><code>AdWhaleMediationAdBannerViewDelegate</code></td></tr><tr><td>노출 시점</td><td>로드 성공 즉시</td><td><code>show()</code> 호출 시</td></tr><tr><td>갱신 광고 노출</td><td>자동</td><td>최초 <code>show()</code> 이후 자동</td></tr><tr><td>클릭 콜백</td><td><code>adViewDidClick(_:)</code></td><td><code>bannerDidClick(_:)</code></td></tr></tbody></table>

### 2. 기본 구현 샘플 코드

`AdWhaleMediationAdBannerView` 클래스를 사용하여 배너 광고를 미리 로드하고 노출하는 기본적인 구현 방법입니다.

{% tabs %}
{% tab title="Swift" %}
```swift
final class PreloadBannerViewController: UIViewController, AdWhaleMediationAdBannerViewDelegate {

    // 1. 인스턴스 생성
    private let bannerView = AdWhaleMediationAdBannerView()

    override func viewDidLoad() {
        super.viewDidLoad()

        // 2. 화면 계층에 추가 (★ loadAd() 보다 먼저)
        bannerView.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(bannerView)
        NSLayoutConstraint.activate([
            bannerView.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            bannerView.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor),
        ])

        // 3. 지면 등록 (placementUid)
        bannerView.placementUid = "발급받은 PLACEMENT_UID 값"

        // 4. 배너 사이즈 설정
        bannerView.bannerSize = .banner320x50

        // 5. 델리게이트 등록
        bannerView.delegate = self

        // 6. 미리 로드 (이 시점에는 노출되지 않음)
        bannerView.loadAd()
    }

    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        bannerView.resume()   // 자동 갱신 재개
    }

    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        bannerView.pause()    // 자동 갱신 일시정지
    }

    // MARK: - AdWhaleMediationAdBannerViewDelegate

    func bannerDidReceiveAd(_ bannerView: AdWhaleMediationAdBannerView) {
        // 7. 로드 성공 이후 show() 호출 — 노출 시점을 늦추려면 원하는 시점에 호출
        bannerView.show()
    }

    func banner(_ bannerView: AdWhaleMediationAdBannerView,
                didFailToLoadWithError statusCode: Int, message: String) {
        // 로드 실패
    }

    func banner(_ bannerView: AdWhaleMediationAdBannerView,
                didFailToShowWithError statusCode: Int, message: String) {
        // 노출 실패
    }

    func bannerDidClick(_ bannerView: AdWhaleMediationAdBannerView) {
        // 클릭
    }

    deinit {
        // 8. 폐기
        bannerView.destroy()
    }
}
```
{% endtab %}

{% tab title="SwiftUI" %}
```swift
// 배너는 UIView 이므로 UIViewRepresentable 로 감싸 사용합니다.
struct PreloadBannerRepresentable: UIViewRepresentable {

    let placementUid: String

    func makeCoordinator() -> Coordinator { Coordinator() }

    func makeUIView(context: Context) -> UIView {
        let container = UIView()

        // 1. 인스턴스 생성
        let banner = AdWhaleMediationAdBannerView()

        // 2. 지면 등록 / 3. 사이즈 설정 / 4. 델리게이트 등록
        banner.placementUid = placementUid
        banner.bannerSize = .banner320x50
        banner.delegate = context.coordinator

        banner.translatesAutoresizingMaskIntoConstraints = false
        container.addSubview(banner)   // ★ loadAd() 보다 먼저
        NSLayoutConstraint.activate([
            banner.centerXAnchor.constraint(equalTo: container.centerXAnchor),
            banner.centerYAnchor.constraint(equalTo: container.centerYAnchor),
        ])
        context.coordinator.banner = banner

        // 5. 미리 로드 — 뷰가 window 계층에 붙은 다음 프레임에 호출
        DispatchQueue.main.async { banner.loadAd() }
        return container
    }

    func updateUIView(_ uiView: UIView, context: Context) { }

    final class Coordinator: NSObject, AdWhaleMediationAdBannerViewDelegate {
        var banner: AdWhaleMediationAdBannerView?

        func bannerDidReceiveAd(_ bannerView: AdWhaleMediationAdBannerView) {
            // 6. 로드 성공 이후 show() 호출
            bannerView.show()
        }

        func banner(_ bannerView: AdWhaleMediationAdBannerView,
                    didFailToLoadWithError statusCode: Int, message: String) { }

        func banner(_ bannerView: AdWhaleMediationAdBannerView,
                    didFailToShowWithError statusCode: Int, message: String) { }

        func bannerDidClick(_ bannerView: AdWhaleMediationAdBannerView) { }
    }

    // 7. 폐기
    static func dismantleUIView(_ uiView: UIView, coordinator: Coordinator) {
        coordinator.banner?.destroy()
    }
}

struct ContentView: View {
    var body: some View {
        VStack {
            Spacer()
            PreloadBannerRepresentable(placementUid: "발급받은 PLACEMENT_UID 값")
                .frame(width: 320, height: 50)
        }
    }
}
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
@interface PreloadBannerViewController () <AdWhaleMediationAdBannerViewDelegate>
@property (nonatomic, strong) AdWhaleMediationAdBannerView *bannerView;
@end

@implementation PreloadBannerViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    // 1. 인스턴스 생성
    self.bannerView = [[AdWhaleMediationAdBannerView alloc] init];

    // 2. 화면 계층에 추가 (★ loadAd 보다 먼저)
    self.bannerView.translatesAutoresizingMaskIntoConstraints = NO;
    [self.view addSubview:self.bannerView];
    [NSLayoutConstraint activateConstraints:@[
        [self.bannerView.centerXAnchor constraintEqualToAnchor:self.view.centerXAnchor],
        [self.bannerView.bottomAnchor constraintEqualToAnchor:self.view.safeAreaLayoutGuide.bottomAnchor],
    ]];

    // 3. 지면 등록 (placementUid)
    self.bannerView.placementUid = @"발급받은 PLACEMENT_UID 값";

    // 4. 배너 사이즈 설정
    self.bannerView.bannerSize = AdWhaleBannerSizeBanner320x50;

    // 5. 델리게이트 등록
    [self.bannerView setAdWhaleMediationAdBannerViewDelegate:self];

    // 6. 미리 로드 (이 시점에는 노출되지 않음)
    [self.bannerView loadAd];
}

- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];
    [self.bannerView resume];
}

- (void)viewWillDisappear:(BOOL)animated {
    [super viewWillDisappear:animated];
    [self.bannerView pause];
}

// 로드 성공
- (void)bannerDidReceiveAd:(AdWhaleMediationAdBannerView *)bannerView {
    // 7. 로드 성공 이후 show 호출 — 노출 시점을 늦추려면 원하는 시점에 호출
    [bannerView show];
}

// 로드 실패
- (void)banner:(AdWhaleMediationAdBannerView *)bannerView
didFailToLoadWithError:(NSInteger)statusCode message:(NSString *)message { }

// 노출 실패
- (void)banner:(AdWhaleMediationAdBannerView *)bannerView
didFailToShowWithError:(NSInteger)statusCode message:(NSString *)message { }

// 클릭
- (void)bannerDidClick:(AdWhaleMediationAdBannerView *)bannerView { }

- (void)dealloc {
    // 8. 폐기
    [self.bannerView destroy];
}

@end
```
{% endtab %}
{% endtabs %}

{% hint style="danger" %}
**호출 순서가 중요합니다.** `addSubview(...)` → `loadAd()` → (로드 성공 후) `show()` 순서를 지키세요.\
일부 네트워크는 responder chain 으로 부모 `UIViewController` 를 찾기 때문에 화면에 붙기 전에 로드하면 그 네트워크가 실패합니다.
{% endhint %}

### 3. API 설명

**AdWhaleMediationAdBannerView 클래스 API 설명**

{% tabs %}
{% tab title="Swift" %}
```swift
init()   // UIView 기본 이니셜라이저
```

```swift
var placementUid: String   // 지면 등록
```

| 파라미터 타입  | 파라미터 값                |
| -------- | --------------------- |
| `String` | placementUid 값(발급 필요) |

```swift
var bannerSize: AdWhaleBannerSize   // 미디에이션 배너 사이즈 설정
```

| 파라미터 타입             | 파라미터 값                                                                                                       |
| ------------------- | ------------------------------------------------------------------------------------------------------------ |
| `AdWhaleBannerSize` | 배너 광고 사이즈 (사이즈 종류: `.banner320x50`, `.banner320x100`, `.banner300x250`, `.banner250x250`, `.adaptiveAnchor`) |

```swift
weak var delegate: AdWhaleMediationAdBannerViewDelegate?
func setAdWhaleMediationAdBannerViewDelegate(_ delegate: AdWhaleMediationAdBannerViewDelegate?)
```

| 파라미터 타입                                | 파라미터 값                       |
| -------------------------------------- | ---------------------------- |
| `AdWhaleMediationAdBannerViewDelegate` | 프리로드 배너 미디에이션 광고 호출 콜백 델리게이트 |

```swift
var adaptiveAnchorWidth: Int   // 배너 width 입력. ADAPTIVE_ANCHOR 적응형 배너 적용 시
```

| 파라미터 타입 | 파라미터 값                                             |
| ------- | -------------------------------------------------- |
| `Int`   | <p>배너 너비(pt) 입력<br>0 을 입력할 경우 앱 창 전체 가로 길이 적용됨</p> |

```swift
func loadAd()    // 미디에이션 배너 광고 로드 (노출하지 않음)
```

```swift
func show()      // 로드된 배너 광고 노출. bannerDidReceiveAd(_:) 이후 호출
```

```swift
func resume()    // 화면 복귀(viewWillAppear 등) 시 호출 필요 — 자동 갱신 재개
```

```swift
func pause()     // 화면 이탈(viewWillDisappear 등) 시 호출 필요 — 자동 갱신 일시정지
```

```swift
func stop()      // 갱신을 완전히 멈출 때 호출 (재개는 loadAd())
```

```swift
func destroy()   // deinit 시 호출 필요 — 리소스 해제
```

```swift
override var intrinsicContentSize: CGSize   // 배너 고유 크기 제공 (읽기 전용)
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
- (instancetype)initWithFrame:(CGRect)frame;   // UIView 기본 이니셜라이저
```

```objectivec
@property (nonatomic, copy) NSString *placementUid;   // 지면 등록
```

| 파라미터 타입      | 파라미터 값                |
| ------------ | --------------------- |
| `NSString *` | placementUid 값(발급 필요) |

```objectivec
@property (nonatomic) AdWhaleBannerSize bannerSize;   // 미디에이션 배너 사이즈 설정
```

| 파라미터 타입             | 파라미터 값                                                                                                                                                                                       |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `AdWhaleBannerSize` | 배너 광고 사이즈 (사이즈 종류: `AdWhaleBannerSizeBanner320x50`, `AdWhaleBannerSizeBanner320x100`, `AdWhaleBannerSizeBanner300x250`, `AdWhaleBannerSizeBanner250x250`, `AdWhaleBannerSizeAdaptiveAnchor`) |

```objectivec
@property (nonatomic, weak) id<AdWhaleMediationAdBannerViewDelegate> delegate;
- (void)setAdWhaleMediationAdBannerViewDelegate:(id<AdWhaleMediationAdBannerViewDelegate>)delegate;
```

| 파라미터 타입                                    | 파라미터 값                       |
| ------------------------------------------ | ---------------------------- |
| `id<AdWhaleMediationAdBannerViewDelegate>` | 프리로드 배너 미디에이션 광고 호출 콜백 델리게이트 |

```objectivec
@property (nonatomic) NSInteger adaptiveAnchorWidth;   // 배너 width 입력. ADAPTIVE_ANCHOR 적응형 배너 적용 시
```

| 파라미터 타입     | 파라미터 값                                             |
| ----------- | -------------------------------------------------- |
| `NSInteger` | <p>배너 너비(pt) 입력<br>0 을 입력할 경우 앱 창 전체 가로 길이 적용됨</p> |

```objectivec
- (void)loadAd;    // 미디에이션 배너 광고 로드 (노출하지 않음)
```

```objectivec
- (void)show;      // 로드된 배너 광고 노출. bannerDidReceiveAd: 이후 호출
```

```objectivec
- (void)resume;    // 화면 복귀(viewWillAppear 등) 시 호출 필요 — 자동 갱신 재개
```

```objectivec
- (void)pause;     // 화면 이탈(viewWillDisappear 등) 시 호출 필요 — 자동 갱신 일시정지
```

```objectivec
- (void)stop;      // 갱신을 완전히 멈출 때 호출 (재개는 loadAd)
```

```objectivec
- (void)destroy;   // dealloc 시 호출 필요 — 리소스 해제
```

```objectivec
@property (nonatomic, readonly) CGSize intrinsicContentSize;   // 배너 고유 크기 제공
```
{% endtab %}
{% endtabs %}

**AdWhaleMediationAdBannerViewDelegate 프로토콜 API 설명**

| 델리게이트 메서드                                   | 호출 시점                            |  필수 |
| ------------------------------------------- | -------------------------------- | :-: |
| `bannerDidReceiveAd(_:)`                    | 광고 로드 성공 (이 시점부터 `show()` 호출 가능) |  ●  |
| `banner(_:didFailToLoadWithError:message:)` | 광고 로드 실패                         |  ●  |
| `banner(_:didFailToShowWithError:message:)` | 광고 노출 실패                         |     |
| `bannerDidClick(_:)`                        | 광고 클릭                            |     |
| `bannerDidShowLandingScreen(_:)`            | 광고 클릭으로 랜딩 화면이 열릴 때              |     |
| `bannerDidCloseLandingScreen(_:)`           | 랜딩 화면이 닫힐 때                      |     |

{% tabs %}
{% tab title="Swift" %}
```swift
func bannerDidReceiveAd(_ bannerView: AdWhaleMediationAdBannerView)   // 광고 요청 성공 시. 이 시점부터 show() 호출 가능
```

```swift
func banner(_ bannerView: AdWhaleMediationAdBannerView,
            didFailToLoadWithError statusCode: Int, message: String)   // 광고 요청 실패 시
```

{% hint style="info" %}
**`didFailToLoadWithError` 후처리**

* 이 콜백은 **워터폴이 모두 소진됐을 때 1회만** 발생합니다. 개별 광고 네트워크의 실패로는 발생하지 않습니다.
* 기존에 노출 중인 광고가 있다면 그 광고 노출은 유지되지만, 자동 갱신은 멈춥니다. 광고 갱신을 재개하려면 `loadAd()` 를 다시 호출한 뒤 `bannerDidReceiveAd(_:)` 에서 `show()` 를 호출하세요.
* **이 콜백에서 무조건 광고 영역을 숨기지 마세요.** 갱신 실패에서도 발생하며, 그때는 이전 광고가 그대로 노출 중입니다. 광고 영역을 `isHidden = true` 로 만들면 **노출 중인 광고를 스스로 가리게 됩니다.**
{% endhint %}

| 파라미터 타입  | 파라미터 값                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Int`    | <p><code>200</code> = 연동 오류(placementUid 오설정 등)<br>또는<br><code>300</code> = 광고를 채우지 못함(워터폴 모두 소진)</p>                                                                                                                                                                                                                                                                                                                                                                                      |
| `String` | <p><strong><code>200</code></strong> — <code>SDK not initialized.</code> /<br><code>placementUid is empty.</code> /<br><code>Failed to create config request.</code> /<br><code>Banner view has been destroyed. Create a new instance before calling loadAd() again.</code><br><strong><code>300</code></strong> — <code>All banner mediations failed</code> (워터폴 모두 소진) 또는 광고 네트워크가 전달한 오류 메시지<br>갱신 실패이고 이전 광고가 계속 노출 중이면 메시지 끝에 <code>Previous ad is still showing.</code> 이 덧붙습니다.</p> |

```swift
@objc optional func banner(_ bannerView: AdWhaleMediationAdBannerView,
                           didFailToShowWithError statusCode: Int, message: String)   // 광고 노출 실패 시
```

| 파라미터 타입  | 파라미터 값                                                                                                |
| -------- | ----------------------------------------------------------------------------------------------------- |
| `Int`    | `200` = 로드 전에 `show()` 호출 또는 광고 네트워크 노출 단계 실패                                                         |
| `String` | <p><code>Ad is not loaded yet.</code> = 로드 전에 <code>show()</code> 호출<br>또는<br>광고 네트워크가 전달한 실패 메시지</p> |

```swift
@objc optional func bannerDidClick(_ bannerView: AdWhaleMediationAdBannerView)                // 광고 클릭 시
@objc optional func bannerDidShowLandingScreen(_ bannerView: AdWhaleMediationAdBannerView)    // 광고 클릭으로 랜딩 화면이 열릴 때
@objc optional func bannerDidCloseLandingScreen(_ bannerView: AdWhaleMediationAdBannerView)   // 랜딩 화면이 닫힐 때
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
// 필수
- (void)bannerDidReceiveAd:(AdWhaleMediationAdBannerView *)bannerView;   // 광고 요청 성공 시. 이 시점부터 show 호출 가능
```

```objectivec
// 필수
- (void)banner:(AdWhaleMediationAdBannerView *)bannerView
    didFailToLoadWithError:(NSInteger)statusCode
                   message:(NSString *)message;   // 광고 요청 실패 시
```

{% hint style="info" %}
**`didFailToLoadWithError` 후처리**

* 이 콜백은 **워터폴이 모두 소진됐을 때 1회만** 발생합니다. 개별 광고 네트워크의 실패로는 발생하지 않습니다.
* 기존에 노출 중인 광고가 있다면 그 광고 노출은 유지되지만, 자동 갱신은 멈춥니다. 광고 갱신을 재개하려면 `loadAd` 를 다시 호출한 뒤 `bannerDidReceiveAd:` 에서 `show` 를 호출하세요.
* **이 콜백에서 무조건 광고 영역을 숨기지 마세요.** 갱신 실패에서도 발생하며, 그때는 이전 광고가 그대로 노출 중입니다. 광고 영역을 `hidden = YES` 로 만들면 **노출 중인 광고를 스스로 가리게 됩니다.**
{% endhint %}

| 파라미터 타입      | 파라미터 값                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `NSInteger`  | <p><code>200</code> = 연동 오류(placementUid 오설정 등)<br>또는<br><code>300</code> = 광고를 채우지 못함(워터폴 모두 소진)</p>                                                                                                                                                                                                                                                                                                                                                                             |
| `NSString *` | <p><strong><code>200</code></strong> — <code>SDK not initialized.</code> / <code>placementUid is empty.</code> / <code>Failed to create config request.</code> / <code>Banner view has been destroyed. Create a new instance before calling loadAd() again.</code><br><strong><code>300</code></strong> — <code>All banner mediations failed</code> (워터폴 모두 소진) 또는 광고 네트워크가 전달한 오류 메시지<br>갱신 실패이고 이전 광고가 계속 노출 중이면 메시지 끝에 <code>Previous ad is still showing.</code> 이 덧붙습니다.</p> |

```objectivec
@optional
- (void)banner:(AdWhaleMediationAdBannerView *)bannerView
    didFailToShowWithError:(NSInteger)statusCode
                   message:(NSString *)message;   // 광고 노출 실패 시
```

| 파라미터 타입      | 파라미터 값                                                                                              |
| ------------ | --------------------------------------------------------------------------------------------------- |
| `NSInteger`  | `200` = 로드 전에 `show` 호출 또는 광고 네트워크 노출 단계 실패                                                         |
| `NSString *` | <p><code>Ad is not loaded yet.</code> = 로드 전에 <code>show</code> 호출<br>또는<br>광고 네트워크가 전달한 실패 메시지</p> |

```objectivec
@optional
- (void)bannerDidClick:(AdWhaleMediationAdBannerView *)bannerView;                // 광고 클릭 시
- (void)bannerDidShowLandingScreen:(AdWhaleMediationAdBannerView *)bannerView;    // 광고 클릭으로 랜딩 화면이 열릴 때
- (void)bannerDidCloseLandingScreen:(AdWhaleMediationAdBannerView *)bannerView;   // 랜딩 화면이 닫힐 때
```
{% endtab %}
{% endtabs %}

### 4. 배너 사이즈

배너 광고는 다음 사이즈를 지원합니다:

| 사이즈              | 값                                  | 사이즈 별 높이           | 설명                         |
| ---------------- | ---------------------------------- | ------------------ | -------------------------- |
| 320x50           | `AdWhaleBannerSize.banner320x50`   | 50                 | 표준 배너 (Banner)             |
| 320x100          | `AdWhaleBannerSize.banner320x100`  | 100                | 큰 배너 (Large Banner)        |
| 300x250          | `AdWhaleBannerSize.banner300x250`  | 250                | 중간 직사각형 (Medium Rectangle) |
| 250x250          | `AdWhaleBannerSize.banner250x250`  | 250                | 정사각형 (Square)              |
| ADAPTIVE\_ANCHOR | `AdWhaleBannerSize.adaptiveAnchor` | 약 50\~150(광고마다 다름) | 적응형 앵커 배너                  |

**사용예시**

{% tabs %}
{% tab title="Swift" %}
```swift
// 320x50 (표준 배너) 사용 예:
bannerView.bannerSize = .banner320x50

// 320x100 (큰 배너) 사용 예:
bannerView.bannerSize = .banner320x100

// 300x250 (중간 직사각형) 사용 예:
bannerView.bannerSize = .banner300x250

// 250x250 (정사각형) 사용 예:
bannerView.bannerSize = .banner250x250

// ADAPTIVE_ANCHOR (적응형 배너 — 너비 지정) 사용 예:
bannerView.bannerSize = .adaptiveAnchor
bannerView.adaptiveAnchorWidth = 360   // 포인트 단위

// ADAPTIVE_ANCHOR (적응형 배너 — 앱 창 전체 너비) 사용 예:
bannerView.bannerSize = .adaptiveAnchor
bannerView.adaptiveAnchorWidth = 0     // 0 을 주거나, 아예 설정하지 않으면 앱 창 전체 너비를 자동으로 감지
```
{% endtab %}

{% tab title="SwiftUI" %}
```swift
// UIViewRepresentable 내부에서 동일하게 설정합니다.
banner.bannerSize = .banner320x50        // 320x50 (표준 배너)
banner.bannerSize = .banner320x100       // 320x100 (큰 배너)
banner.bannerSize = .banner300x250       // 300x250 (중간 직사각형)
banner.bannerSize = .banner250x250       // 250x250 (정사각형)

banner.bannerSize = .adaptiveAnchor      // 적응형 앵커 배너
banner.adaptiveAnchorWidth = 360         // 너비 지정 (포인트 단위)
banner.adaptiveAnchorWidth = 0           // 0 을 주거나, 아예 설정하지 않으면 앱 창 전체 너비
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
// 320x50 (표준 배너) 사용 예:
bannerView.bannerSize = AdWhaleBannerSizeBanner320x50;

// 320x100 (큰 배너) 사용 예:
bannerView.bannerSize = AdWhaleBannerSizeBanner320x100;

// 300x250 (중간 직사각형) 사용 예:
bannerView.bannerSize = AdWhaleBannerSizeBanner300x250;

// 250x250 (정사각형) 사용 예:
bannerView.bannerSize = AdWhaleBannerSizeBanner250x250;

// ADAPTIVE_ANCHOR (적응형 배너 — 너비 지정) 사용 예:
bannerView.bannerSize = AdWhaleBannerSizeAdaptiveAnchor;
bannerView.adaptiveAnchorWidth = 360;   // 포인트 단위

// ADAPTIVE_ANCHOR (적응형 배너 — 앱 창 전체 너비) 사용 예:
bannerView.bannerSize = AdWhaleBannerSizeAdaptiveAnchor;
bannerView.adaptiveAnchorWidth = 0;     // 0 을 주거나, 아예 설정하지 않으면 앱 창 전체 너비를 자동으로 감지
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
**ADAPTIVE\_ANCHOR 의 높이는 고정값이 아닙니다.**

* 광고 네트워크가 너비에 맞춰 높이를 정하므로 약 50\~150 포인트 사이에서 광고마다 달라질 수 있고, 자동 갱신으로 광고가 바뀔 때도 달라질 수 있습니다.
* 배너 뷰는 실제 광고 높이로 크기가 바뀝니다. 배너를 담는 영역의 높이를 50 으로 고정하지 말고, 배너 뷰의 크기를 따라가도록 배치하세요.
* 너비를 지정하지 않으면 기기 화면이 아니라 **앱 창 너비**를 기준으로 합니다. (iPad 분할 화면, 폴더블 기기 등)
{% endhint %}

### 5. 옵션 설정

{% tabs %}
{% tab title="Swift" %}
```swift
bannerView.region = "서울시 강남구"                                // 지역 타겟팅 전용 API(옵션)
bannerView.setGeocoder(latitude: 37.5665, longitude: 126.9780)   // 지역 타겟팅 전용 API(옵션)
```

{% hint style="info" %}
AdWhale SDK 는 Cauly 네트워크를 지원하며, 광고 지역 타겟팅을 위해 지역정보(`region`, `setGeocoder`)를 선택적으로 입력받고 있습니다.
{% endhint %}

```swift
// 레벨플레이 placement name 연동 전용 API (옵션).
// placementName 값은 LevelPlay 콘솔에서 설정한 이름
bannerView.placementName = "banner_main"
```

{% hint style="warning" %}
AdWhale SDK 는 LevelPlay 네트워크를 지원하며, 각 Placement 별로 광고 노출을 구분하고자 할 때 `placementName` 으로 설정할 수 있습니다. 설정하지 않으면 기본 Placement(Default Placement)가 사용됩니다.

***

배너 광고에는 **보상 조건, 제한 조건 등의 설정이 적용되지 않으며**, Placement 는 **광고 위치 구분 및 분석 용도**로만 사용됩니다.
{% endhint %}
{% endtab %}

{% tab title="SwiftUI" %}
```swift
// UIViewRepresentable 의 makeUIView 안에서 loadAd() 호출 전에 설정합니다.
banner.region = "서울시 강남구"                                // 지역 타겟팅 전용 API(옵션)
banner.setGeocoder(latitude: 37.5665, longitude: 126.9780)   // 지역 타겟팅 전용 API(옵션)
```

{% hint style="info" %}
AdWhale SDK 는 Cauly 네트워크를 지원하며, 광고 지역 타겟팅을 위해 지역정보(`region`, `setGeocoder`)를 선택적으로 입력받고 있습니다.
{% endhint %}

```swift
// 레벨플레이 placement name 연동 전용 API (옵션).
// placementName 값은 LevelPlay 콘솔에서 설정한 이름
banner.placementName = "banner_main"
```

{% hint style="warning" %}
AdWhale SDK 는 LevelPlay 네트워크를 지원하며, 각 Placement 별로 광고 노출을 구분하고자 할 때 `placementName` 으로 설정할 수 있습니다. 설정하지 않으면 기본 Placement(Default Placement)가 사용됩니다.

***

배너 광고에는 **보상 조건, 제한 조건 등의 설정이 적용되지 않으며**, Placement 는 **광고 위치 구분 및 분석 용도**로만 사용됩니다.
{% endhint %}
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
bannerView.region = @"서울시 강남구";                              // 지역 타겟팅 전용 API(옵션)
[bannerView setGeocoderWithLatitude:37.5665 longitude:126.9780];   // 지역 타겟팅 전용 API(옵션)
```

{% hint style="info" %}
AdWhale SDK 는 Cauly 네트워크를 지원하며, 광고 지역 타겟팅을 위해 지역정보(`region`, `setGeocoderWithLatitude:longitude:`)를 선택적으로 입력받고 있습니다.
{% endhint %}

```objectivec
// 레벨플레이 placement name 연동 전용 API (옵션).
// placementName 값은 LevelPlay 콘솔에서 설정한 이름
bannerView.placementName = @"banner_main";
```

{% hint style="warning" %}
AdWhale SDK 는 LevelPlay 네트워크를 지원하며, 각 Placement 별로 광고 노출을 구분하고자 할 때 `placementName` 으로 설정할 수 있습니다. 설정하지 않으면 기본 Placement(Default Placement)가 사용됩니다.

***

배너 광고에는 **보상 조건, 제한 조건 등의 설정이 적용되지 않으며**, Placement 는 **광고 위치 구분 및 분석 용도**로만 사용됩니다.
{% endhint %}
{% endtab %}
{% endtabs %}

| 프로퍼티 / 메서드                         | 설명           | 적용 네트워크   |
| ---------------------------------- | ------------ | --------- |
| `region`                           | 지역 타겟팅       | Cauly     |
| `setGeocoder(latitude:longitude:)` | 좌표 타겟팅       | Cauly     |
| `placementName`                    | Placement 이름 | LevelPlay |

{% hint style="info" %}
옵션은 해당 네트워크가 낙찰됐을 때만 사용됩니다. 설정해 두어도 다른 네트워크의 광고에는 영향을 주지 않습니다.
{% endhint %}

### 6. 프리로드 배너 Programmatic 샘플 코드

다음은 코드로 프리로드 배너 광고를 구현하는 완전한 예시입니다.

{% tabs %}
{% tab title="Swift" %}
```swift
import UIKit
import AdWhaleSDK

final class PreloadBannerViewController: UIViewController, AdWhaleMediationAdBannerViewDelegate {

    private let bannerView = AdWhaleMediationAdBannerView()

    override func viewDidLoad() {
        super.viewDidLoad()

        // 1. 화면 계층에 추가 (★ loadAd() 보다 먼저)
        bannerView.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(bannerView)
        NSLayoutConstraint.activate([
            bannerView.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            bannerView.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor),
        ])

        // 2. 지면 등록 (placementUid)
        bannerView.placementUid = "발급받은 PLACEMENT_UID 값"

        // 3. 배너 사이즈 설정
        bannerView.bannerSize = .banner320x50

        // 4. 델리게이트 등록
        bannerView.delegate = self

        // 5. 미리 로드 (이 시점에는 노출되지 않음)
        bannerView.loadAd()
    }

    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        bannerView.resume()
    }

    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        bannerView.pause()
    }

    // MARK: - AdWhaleMediationAdBannerViewDelegate

    func bannerDidReceiveAd(_ bannerView: AdWhaleMediationAdBannerView) {
        print("프리로드 배너 로드 성공")
        // 6. 로드 완료 후 노출
        bannerView.show()
    }

    func banner(_ bannerView: AdWhaleMediationAdBannerView,
                didFailToLoadWithError statusCode: Int, message: String) {
        if statusCode == 300, message.contains("Previous ad is still showing.") {
            // 이번 갱신만 실패했고 광고는 계속 노출 중 → 아무것도 하지 않는다
            return
        }
        print("프리로드 배너 로드 실패 (\(statusCode)): \(message)")
    }

    func banner(_ bannerView: AdWhaleMediationAdBannerView,
                didFailToShowWithError statusCode: Int, message: String) {
        print("프리로드 배너 노출 실패 (\(statusCode)): \(message)")
    }

    func bannerDidClick(_ bannerView: AdWhaleMediationAdBannerView) {
        print("프리로드 배너 클릭")
    }

    deinit {
        // 7. 폐기
        bannerView.destroy()
    }
}
```
{% endtab %}

{% tab title="SwiftUI" %}
```swift
import SwiftUI
import UIKit
import AdWhaleSDK

struct PreloadBannerRepresentable: UIViewRepresentable {

    let placementUid: String
    let bannerSize: AdWhaleBannerSize

    func makeCoordinator() -> Coordinator { Coordinator() }

    func makeUIView(context: Context) -> UIView {
        let container = UIView()

        // 1. 인스턴스 생성
        let banner = AdWhaleMediationAdBannerView()

        // 2. 지면 등록 / 3. 사이즈 설정 / 4. 델리게이트 등록
        banner.placementUid = placementUid
        banner.bannerSize = bannerSize
        banner.placementName = "banner_main"
        banner.delegate = context.coordinator

        // 5. 화면 계층에 추가 (★ loadAd() 보다 먼저)
        banner.translatesAutoresizingMaskIntoConstraints = false
        container.addSubview(banner)
        NSLayoutConstraint.activate([
            banner.centerXAnchor.constraint(equalTo: container.centerXAnchor),
            banner.centerYAnchor.constraint(equalTo: container.centerYAnchor),
        ])
        context.coordinator.banner = banner

        // 6. 미리 로드 — 뷰가 window 계층에 붙은 다음 프레임에 호출
        DispatchQueue.main.async { banner.loadAd() }
        return container
    }

    func updateUIView(_ uiView: UIView, context: Context) { }

    final class Coordinator: NSObject, AdWhaleMediationAdBannerViewDelegate {
        var banner: AdWhaleMediationAdBannerView?

        func bannerDidReceiveAd(_ bannerView: AdWhaleMediationAdBannerView) {
            print("프리로드 배너 로드 성공")
            // 7. 로드 완료 후 노출
            bannerView.show()
        }

        func banner(_ bannerView: AdWhaleMediationAdBannerView,
                    didFailToLoadWithError statusCode: Int, message: String) {
            if statusCode == 300, message.contains("Previous ad is still showing.") { return }
            print("프리로드 배너 로드 실패 (\(statusCode)): \(message)")
        }

        func banner(_ bannerView: AdWhaleMediationAdBannerView,
                    didFailToShowWithError statusCode: Int, message: String) {
            print("프리로드 배너 노출 실패 (\(statusCode)): \(message)")
        }

        func bannerDidClick(_ bannerView: AdWhaleMediationAdBannerView) {
            print("프리로드 배너 클릭")
        }
    }

    // 8. 폐기
    static func dismantleUIView(_ uiView: UIView, coordinator: Coordinator) {
        coordinator.banner?.destroy()
    }
}

struct PreloadBannerScreen: View {
    var body: some View {
        VStack {
            Spacer()
            PreloadBannerRepresentable(placementUid: "발급받은 PLACEMENT_UID 값",
                                       bannerSize: .banner320x50)
                .frame(width: 320, height: 50)
        }
    }
}
```

{% hint style="warning" %}
SwiftUI 는 `viewWillAppear` / `viewWillDisappear` 가 없습니다. 자동 갱신 제어(`resume()` / `pause()`)가 필요하면 `Coordinator` 에 배너 참조를 보관해 두고 `.onAppear` / `.onDisappear` 또는 `scenePhase` 변화에서 호출하세요.
{% endhint %}
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
#import "PreloadBannerViewController.h"
@import AdWhaleSDK;

@interface PreloadBannerViewController () <AdWhaleMediationAdBannerViewDelegate>
@property (nonatomic, strong) AdWhaleMediationAdBannerView *bannerView;
@end

@implementation PreloadBannerViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    // 1. 인스턴스 생성
    self.bannerView = [[AdWhaleMediationAdBannerView alloc] init];

    // 2. 화면 계층에 추가 (★ loadAd 보다 먼저)
    self.bannerView.translatesAutoresizingMaskIntoConstraints = NO;
    [self.view addSubview:self.bannerView];
    [NSLayoutConstraint activateConstraints:@[
        [self.bannerView.centerXAnchor constraintEqualToAnchor:self.view.centerXAnchor],
        [self.bannerView.bottomAnchor constraintEqualToAnchor:self.view.safeAreaLayoutGuide.bottomAnchor],
    ]];

    // 3. 지면 등록 (placementUid)
    self.bannerView.placementUid = @"발급받은 PLACEMENT_UID 값";

    // 4. 배너 사이즈 설정
    self.bannerView.bannerSize = AdWhaleBannerSizeBanner320x50;

    // 5. 델리게이트 등록
    [self.bannerView setAdWhaleMediationAdBannerViewDelegate:self];

    // 6. 미리 로드 (이 시점에는 노출되지 않음)
    [self.bannerView loadAd];
}

- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];
    [self.bannerView resume];
}

- (void)viewWillDisappear:(BOOL)animated {
    [super viewWillDisappear:animated];
    [self.bannerView pause];
}

- (void)dealloc {
    // 8. 폐기
    [self.bannerView destroy];
}

- (void)bannerDidReceiveAd:(AdWhaleMediationAdBannerView *)bannerView {
    NSLog(@"프리로드 배너 로드 성공");
    // 7. 로드 완료 후 노출
    [bannerView show];
}

- (void)banner:(AdWhaleMediationAdBannerView *)bannerView
didFailToLoadWithError:(NSInteger)statusCode message:(NSString *)message {
    if (statusCode == 300 && [message containsString:@"Previous ad is still showing."]) {
        return;
    }
    NSLog(@"프리로드 배너 로드 실패 (%ld): %@", (long)statusCode, message);
}

- (void)banner:(AdWhaleMediationAdBannerView *)bannerView
didFailToShowWithError:(NSInteger)statusCode message:(NSString *)message {
    NSLog(@"프리로드 배너 노출 실패 (%ld): %@", (long)statusCode, message);
}

- (void)bannerDidClick:(AdWhaleMediationAdBannerView *)bannerView {
    NSLog(@"프리로드 배너 클릭");
}

@end
```
{% endtab %}
{% endtabs %}

### 7. 프리로드 배너 Storyboard 샘플 코드

Storyboard(또는 XIB)에 `UIView` 를 배치하고 **Custom Class** 를 `AdWhaleMediationAdBannerView` 로 지정한 뒤, `@IBOutlet` 으로 연결해 코드에서 설정 · 로드 · 노출합니다.

{% tabs %}
{% tab title="Swift" %}
**Storyboard 설정**

{% hint style="info" %}
1. Storyboard 에 UIView 를 배치하고 위치 제약(예: 화면 하단 중앙)을 지정합니다.
2. Identity Inspector → Custom Class → Class는 AdWhaleMediationAdBannerView, Module은 AdWhaleSDK
3. 크기 제약은 배너 사이즈에 맞춰 지정합니다. (예: 320 x 50)
4. Assistant Editor 에서 @IBOutlet 으로 연결합니다.
{% endhint %}

**Swift 코드**

```swift
import UIKit
import AdWhaleSDK

final class StoryboardPreloadBannerViewController: UIViewController, AdWhaleMediationAdBannerViewDelegate {

    // Storyboard 의 Custom Class 뷰와 연결
    @IBOutlet weak var bannerView: AdWhaleMediationAdBannerView!

    override func viewDidLoad() {
        super.viewDidLoad()

        // Storyboard 뷰는 이미 화면 계층에 붙어 있으므로 바로 설정 · 로드 가능

        // 1. 지면 등록 (placementUid)
        bannerView.placementUid = "발급받은 PLACEMENT_UID 값"

        // 2. 배너 사이즈 설정
        bannerView.bannerSize = .banner320x50

        // 3. 델리게이트 등록
        bannerView.delegate = self

        // 4. 미리 로드 (이 시점에는 노출되지 않음)
        bannerView.loadAd()
    }

    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        bannerView.resume()
    }

    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        bannerView.pause()
    }

    // MARK: - AdWhaleMediationAdBannerViewDelegate

    func bannerDidReceiveAd(_ bannerView: AdWhaleMediationAdBannerView) {
        print("프리로드 배너 로드 성공")
        // 5. 로드 완료 후 노출
        bannerView.show()
    }

    func banner(_ bannerView: AdWhaleMediationAdBannerView,
                didFailToLoadWithError statusCode: Int, message: String) {
        if statusCode == 300, message.contains("Previous ad is still showing.") {
            // 이번 갱신만 실패했고 광고는 계속 노출 중 → 아무것도 하지 않는다
            return
        }
        print("프리로드 배너 로드 실패 (\(statusCode)): \(message)")
    }

    func banner(_ bannerView: AdWhaleMediationAdBannerView,
                didFailToShowWithError statusCode: Int, message: String) {
        print("프리로드 배너 노출 실패 (\(statusCode)): \(message)")
    }

    func bannerDidClick(_ bannerView: AdWhaleMediationAdBannerView) {
        print("프리로드 배너 클릭")
    }

    deinit {
        // 6. 폐기
        bannerView?.destroy()
    }
}
```
{% endtab %}

{% tab title="Objective-C" %}
**Storyboard 설정**

{% hint style="info" %}
1. Storyboard 에 UIView 를 배치하고 위치 · 크기 제약을 지정합니다.
2. Identity Inspector → Custom Class → Class는 AdWhaleMediationAdBannerView, Module은 AdWhaleSDK
3. @property (IBOutlet) 으로 연결합니다.
{% endhint %}

**Objective-C 코드**

```objective-c
#import "StoryboardPreloadBannerViewController.h"
@import AdWhaleSDK;

@interface StoryboardPreloadBannerViewController () <AdWhaleMediationAdBannerViewDelegate>
@property (nonatomic, weak) IBOutlet AdWhaleMediationAdBannerView *bannerView;
@end

@implementation StoryboardPreloadBannerViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    // Storyboard 뷰는 이미 화면 계층에 붙어 있으므로 바로 설정 · 로드 가능

    // 1. 지면 등록 (placementUid)
    self.bannerView.placementUid = @"발급받은 PLACEMENT_UID 값";

    // 2. 배너 사이즈 설정
    self.bannerView.bannerSize = AdWhaleBannerSizeBanner320x50;

    // 3. 델리게이트 등록
    [self.bannerView setAdWhaleMediationAdBannerViewDelegate:self];

    // 4. 미리 로드 (이 시점에는 노출되지 않음)
    [self.bannerView loadAd];
}

- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];
    [self.bannerView resume];
}

- (void)viewWillDisappear:(BOOL)animated {
    [super viewWillDisappear:animated];
    [self.bannerView pause];
}

- (void)bannerDidReceiveAd:(AdWhaleMediationAdBannerView *)bannerView {
    NSLog(@"프리로드 배너 로드 성공");
    // 5. 로드 완료 후 노출
    [bannerView show];
}

- (void)banner:(AdWhaleMediationAdBannerView *)bannerView
didFailToLoadWithError:(NSInteger)statusCode message:(NSString *)message {
    if (statusCode == 300 && [message containsString:@"Previous ad is still showing."]) {
        return;
    }
    NSLog(@"프리로드 배너 로드 실패 (%ld): %@", (long)statusCode, message);
}

- (void)banner:(AdWhaleMediationAdBannerView *)bannerView
didFailToShowWithError:(NSInteger)statusCode message:(NSString *)message {
    NSLog(@"프리로드 배너 노출 실패 (%ld): %@", (long)statusCode, message);
}

- (void)bannerDidClick:(AdWhaleMediationAdBannerView *)bannerView {
    NSLog(@"프리로드 배너 클릭");
}

- (void)dealloc {
    // 6. 폐기
    [self.bannerView destroy];
}

@end
```
{% endtab %}
{% endtabs %}

### 8. 주의사항

**노출(show) 호출**

* `loadAd()` 만 호출하면 광고가 노출되지 않습니다. **로드 성공(`bannerDidReceiveAd(_:)`) 이후 `show()` 를 호출**하세요.
* 로드되기 전에 `show()` 를 호출하면 노출되지 않고 `banner(_:didFailToShowWithError:message:)` 로 `200`(`Ad is not loaded yet.`)이 통지됩니다.
* 최초 `show()` 이후에는 자동 갱신으로 받은 광고가 바로 교체 노출됩니다. 매번 `show()` 를 호출할 필요는 없습니다.
* `loadAd()` 를 다시 호출하면 자동 교체 노출이 해제됩니다. 새 광고를 노출하려면 `bannerDidReceiveAd(_:)` 이후 `show()` 를 다시 호출하세요.
* `show()` 를 호출하기 전에는 자동 갱신이 보류되어, 미리 받아둔 광고가 다른 광고로 바뀌지 않습니다. 미리 받아둔 광고는 **최대 50분** 보관되며, 그 뒤에 `show()` 를 호출하면 새 광고를 받아 노출합니다.
* `show()` 는 메인 스레드가 아닌 곳에서 호출해도 메인 스레드에서 처리됩니다.

**광고 로드 타이밍**

* `loadAd()` 는 SDK 초기화 완료(`initialize` 완료 콜백) 이후에 호출하세요.\
  초기화 전에 호출하면 `200`(`SDK not initialized.`)이 통지됩니다.
* **`addSubview` → `loadAd()` 순서를 지키세요.**\
  화면 계층에 붙기 전에 로드하면 일부 네트워크는 부모 화면을 찾지 못해 광고를 받지 못하고, 다음 순위 네트워크로 넘어갑니다.
* **미리 로드한 배너 뷰를 다른 화면으로 옮겨 노출하지 마세요.**\
  일부 네트워크는 로드할 때의 화면에 묶이므로, 배너를 노출할 화면에서 배너 뷰를 만들어 로드하세요.
* 광고 네트워크가 성공·실패 어느 콜백도 돌려주지 않으면, SDK 가 네트워크당 지면별 설정 시간(**10\~30초**, 기본 10초)을 기다린 뒤 다음 순위 네트워크로 진행합니다.\
  이 만료는 별도 콜백으로 통지되지 않으며, 워터폴이 모두 소진된 시점에 실패 콜백이 1회 옵니다.

**라이프사이클**

* 프리로드 배너는 최초 `show()` 이후 설정 응답의 `ad_reload_sec` 주기로 자동 갱신됩니다.\
  갱신은 SDK 내부 타이머가 돌리므로 앱이 `loadAd()` 를 반복 호출할 필요가 없습니다.
* `viewWillAppear` / `viewWillDisappear` / `deinit` 에서 각각 `resume()` / `pause()` / `destroy()` 호출이 필요합니다.\
  미호출 시 동작에 불이익이 있을 수 있습니다.
* `pause()` 후 `resume()` 을 호출하지 않으면 자동 갱신이 재개되지 않습니다.

**재생성**

* 배너 사이즈나 placement UID 를 바꿀 때는 뷰를 `destroy()` 한 뒤 새로 생성한 후 다시 설정 · 로드하세요.

**레이아웃**

* 배너 뷰는 고유 크기(`intrinsicContentSize`)를 제공합니다. 위치 제약만 걸어도 크기가 잡히지만, 명시적으로 크기 제약을 주는 것을 권장합니다.

**델리게이트 등록**

* 델리게이트를 설정하지 않으면 로드 실패가 어디에도 전달되지 않아 **"광고도 안 나오고 콜백도 없는"** 상태가 됩니다.
* SDK 는 이 경우 `통지할 리스너가 없습니다` 경고 로그를 남깁니다.

**에러 처리**

* `banner(_:didFailToLoadWithError:message:)` 에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.

**로드 실패 후처리**

* 로드 실패 콜백은 **워터폴이 모두 소진됐을 때 1회만** 발생합니다.\
  개별 광고 네트워크의 실패로는 발생하지 않습니다.
* 기존에 노출 중인 광고가 있다면 그 광고 노출은 유지되지만, 자동 갱신은 멈춥니다.\
  광고 갱신을 재개하려면 `loadAd()` 를 다시 호출한 뒤 `bannerDidReceiveAd(_:)` 에서 `show()` 를 호출하세요.
* **로드 실패 콜백에서 무조건 광고 영역을 숨기지 마세요.**\
  갱신 실패에서도 발생하며, 그때는 이전 광고가 그대로 노출 중입니다.\
  메시지 끝에 `Previous ad is still showing.` 이 붙어 있을 때 광고 영역을 숨기면 **노출 중인 광고를 스스로 가리게** 됩니다.

**성능 최적화**

* 프리로드 배너는 최초 `show()` 이후 자동으로 갱신되므로, 불필요한 뷰 재생성을 방지하세요.\
  (SwiftUI 는 `UIViewRepresentable` 의 `makeUIView` 가 다시 호출되지 않도록 상태를 관리하세요)

**테스트**

* 개발 환경에서는 테스트용 placement UID 를 사용하세요.
* 실제 배포 전에 다양한 사이즈와 화면 전환 시나리오에서 테스트하세요.

{% hint style="warning" %}
**SDK 1.1.0 동작 변경**

1.1.0 이전에는 프리로드 배너가 `loadAd()` 만으로 노출되었습니다. 1.1.0 부터는 `show()` 를 호출해야 노출되므로, `loadAd()` 만 호출하던 코드는 `show()` 호출을 추가해 주세요.
{% endhint %}
