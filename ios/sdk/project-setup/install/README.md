# 2.1 SDK 설치

프로젝트에 [패키지 종속 항목을 추가](https://developer.apple.com/documentation/xcode/adding-package-dependencies-to-your-app#Add-a-package-dependency)하려면 다음 단계를 진행합니다.

### Xcode 에서 추가

{% hint style="info" %}
1. Xcode에서 **File(파일) > Add Package Dependencies(패키지 종속 항목 추가)...**&#xB85C; 이동하여 AdWhale Swift 패키지를 설치합니다.
2. 표시되는 메시지에서 다음 Google 모바일 광고 Swift 패키지 GitHub 저장소를 검색합니다.
   *   AdWhale SDK Swift 패키지 저장소

       ```
       https://github.com/adwhale-sdk/adwhale-sdk-ios
       ```
3. **Dependency Rule** 을 'Up to Next Major Version' 으로 두고 **Add Package** 를 누릅니다.
4. 사용할 AdWhale Swift 패키지의 버전을 선택합니다. (최신 SDK 버전: 1.1.0)
{% endhint %}

### Package.swift 로 추가

```swift
dependencies: [
    .package(url: "https://github.com/adwhale-sdk/adwhale-sdk-ios.git", from: "1.1.0")
],
targets: [
    .target(
        name: "YourApp",
        dependencies: [
            .product(name: "AdWhaleSDK", package: "adwhale-sdk-ios"),
            .product(name: "AdWhaleAdMobAdapter", package: "adwhale-sdk-ios"),
            .product(name: "AdWhaleCaulyAdapter", package: "adwhale-sdk-ios"),
            .product(name: "AdWhaleAdFitAdapter", package: "adwhale-sdk-ios"),
            .product(name: "AdWhaleAdmizeAdapter", package: "adwhale-sdk-ios"),
            .product(name: "AdWhaleLevelPlayAdapter", package: "adwhale-sdk-ios"),
        ]
    )
]
```

{% hint style="info" %}
**파트너 SDK(GoogleMobileAds / CaulySDK / AdFitSDK / AdmizeSdk / LevelPlay)는 SDK 패키지가 의존성으로 선언하고 있어 SPM 이 자동으로 함께 가져옵니다.**

따라서 앱에서 별도로 추가할 필요가 없습니다. \
어댑터 바이너리에 파트너 SDK 가 포함되어 있는 것은 아니며, 버전은 SDK 패키지가 고정합니다.
{% endhint %}

{% hint style="warning" %}
설치 후 [Project Setting](../project-setting.md) 의 `-ObjC` 링커 플래그 설정을 반드시 함께 적용하세요.
{% endhint %}

### 애드몹 미디에이션 파트너 네트워크 추가 (선택)

AdMob 미디에이션 파트너 어댑터는 SDK 에 포함되지 않습니다. **앱이 직접 추가**합니다.

{% hint style="danger" %}
**레벨플레이 파트너 어댑터와 함께 사용할 수 없습니다.**

두 계열이 동일한 파트너 SDK(AppLovin, Vungle, Mintegral, Pangle, Unity Ads 등)를 서로 다른 버전으로 고정하므로, 한 프로젝트에는 **둘 중 한쪽만** 추가하세요.\
AdWhale 의 `AdWhaleAdMobAdapter` 와 `AdWhaleLevelPlayAdapter` 를 함께 쓰는 것은 문제없습니다. 제약은 **파트너 네트워크 어댑터**에만 해당합니다.
{% endhint %}

<table><thead><tr><th width="161.78515625">네트워크</th><th width="483.0546875">SPM 저장소</th><th>버전</th></tr></thead><tbody><tr><td>InMobi</td><td><code>https://github.com/googleads/googleads-mobile-ios-mediation-inmobi.git</code></td><td><code>11.5.000</code></td></tr><tr><td>AppLovin</td><td><code>https://github.com/googleads/googleads-mobile-ios-mediation-applovin.git</code></td><td><code>13.6.400</code> </td></tr><tr><td>Vungle(Liftoff)</td><td><code>https://github.com/googleads/googleads-mobile-ios-mediation-liftoffmonetize.git</code></td><td><code>7.7.800</code> </td></tr><tr><td>DT Exchange(Fyber)</td><td><code>https://github.com/googleads/googleads-mobile-ios-mediation-dtexchange.git</code></td><td><code>8.5.000</code> </td></tr><tr><td>Mintegral</td><td><code>https://github.com/googleads/googleads-mobile-ios-mediation-mintegral.git</code></td><td><code>8.1.700</code> </td></tr><tr><td>Pangle</td><td><code>https://github.com/googleads/googleads-mobile-ios-mediation-pangle.git</code></td><td><code>8.3.00800</code></td></tr><tr><td>Unity Ads</td><td><code>https://github.com/googleads/googleads-mobile-ios-mediation-unity.git</code></td><td><code>4.21.000</code> </td></tr><tr><td>Moloco</td><td><code>https://github.com/googleads/googleads-mobile-ios-mediation-moloco.git</code></td><td><code>4.11.100</code> </td></tr></tbody></table>

{% hint style="info" %}
**버전을 반드시 위 표대로 고정하세요.**\
AdWhale AdMob 어댑터는 `Google-Mobile-Ads-SDK 13.11.0` 기준으로 빌드되어 있습니다.\
파트너 어댑터가 다른 메이저 버전의 GMA 를 끌어오면 런타임에 광고가 로드되지 않습니다.
{% endhint %}

#### Cauly 커스텀 이벤트 사용 (선택)

AdMob 미디에이션 그룹에 Cauly 를 **커스텀 이벤트**로 추가해 사용할 수 있습니다. (배너 · 전면 · 보상형)

1.  앱에 `AdWhaleCaulyAdapter` 를 추가하고, 초기화 전에 등록합니다. (`AdWhaleAdMobAdapter` 는 이미 연동되어 있어야 합니다.)

    이미 Cauly 어댑터를 등록해 두었다면 추가 코드는 필요 없습니다.

    ```swift
    AdWhaleCaulyAdapterRegistrar.register()
    ```
2. AdMob 대시보드에서 해당 광고 단위의 미디에이션 그룹에 커스텀 이벤트를 추가하고 아래와 같이 입력합니다.

| 항목         | 입력값                      |
| ---------- | ------------------------ |
| Class Name | `AdWhaleAdMobCaulyEvent` |
| Parameter  | Cauly 앱 코드               |

{% hint style="warning" %}
**기존 SDK(1.0.x)에서 `AdWhaleSDK.CaulyBanner` 로 등록해 두었다면 `AdWhaleAdMobCaulyEvent` 로 바꿔 주세요.** 기존 이름으로는 Cauly 광고를 불러오지 못합니다.
{% endhint %}

{% hint style="info" %}
보상형의 보상 종류 · 수량은 AdMob 광고 단위에 설정한 값이 전달됩니다.
{% endhint %}

### 레벨플레이 미디에이션 파트너 네트워크 추가 (선택)

LevelPlay 미디에이션 파트너 어댑터는 SDK 에 포함되지 않습니다. **앱이 직접 추가**합니다.

{% hint style="danger" %}
**애드몹 파트너 어댑터와 함께 사용할 수 없습니다.**

두 계열이 동일한 파트너 SDK(AppLovin, Vungle, Mintegral, Pangle, Unity Ads 등)를 서로 다른 버전으로 고정하므로, 한 프로젝트에는 **둘 중 한쪽만** 추가하세요.\
AdWhale 의 `AdWhaleAdMobAdapter` 와 `AdWhaleLevelPlayAdapter` 를 함께 쓰는 것은 문제없습니다. 제약은 **파트너 네트워크 어댑터**에만 해당합니다.
{% endhint %}

<table data-search="true"><thead><tr><th width="161.55859375">네트워크</th><th width="467.8046875">SPM 저장소</th><th>버전</th></tr></thead><tbody><tr><td>AppLovin</td><td><code>https://github.com/ironsource-mobile/LevelPlay-AppLovin-Adapter-Swift-Package</code></td><td><code>5.9.0</code></td></tr><tr><td>Vungle(Liftoff)</td><td><code>https://github.com/ironsource-mobile/LevelPlay-Vungle-Adapter-Swift-Package</code></td><td><code>5.14.0</code></td></tr><tr><td>DT Exchange(Fyber)</td><td><code>https://github.com/ironsource-mobile/LevelPlay-Fyber-Adapter-Swift-Package</code></td><td><code>5.12.0</code></td></tr><tr><td>Mintegral</td><td><code>https://github.com/ironsource-mobile/LevelPlay-Mintegral-Adapter-Swift-Package</code></td><td><code>5.20.0</code></td></tr><tr><td>Pangle</td><td><code>https://github.com/ironsource-mobile/LevelPlay-Pangle-Adapter-Swift-Package</code></td><td><code>5.34.0</code></td></tr><tr><td>Unity Ads</td><td><code>https://github.com/ironsource-mobile/LevelPlay-UnityAds-Adapter-Swift-Package</code></td><td><code>5.12.0</code></td></tr></tbody></table>

{% hint style="info" %}
**버전을 반드시 위 표대로 고정하세요.**\
AdWhale LevelPlay 어댑터는 `LevelPlay 9.6.1` 기준으로 빌드되어 있습니다.\
파트너 어댑터는 LevelPlay SDK 를 정확한 버전으로 고정하므로, 표와 다른 버전을 쓰면 패키지 해석 단계에서 충돌해 **빌드가 되지 않습니다.**
{% endhint %}

### 최신 SDK Version : 1.1.0
