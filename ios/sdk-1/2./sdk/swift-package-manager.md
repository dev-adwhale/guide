# Swift Package Manager

프로젝트에 [패키지 종속 항목을 추가](https://developer.apple.com/documentation/xcode/adding-package-dependencies-to-your-app#Add-a-package-dependency)하려면 다음 단계를 진행합니다.

#### Xcode 에서 추가

{% hint style="info" %}
1. Xcode에서 **File(파일) > Add Package Dependencies(패키지 종속 항목 추가)...**&#xB85C; 이동하여 Cauly Swift 패키지를 설치합니다.
2. 표시되는 메시지에서 다음 Google 모바일 광고 Swift 패키지 GitHub 저장소를 검색합니다.
   *   AdWhale SDK Swift 패키지 저장소

       ```
       https://github.com/adwhale-sdk/adwhale-sdk-ios
       ```
3. **Dependency Rule** 을 'Up to Next Major Version' 으로 두고 **Add Package** 를 누릅니다.
4. 사용할 AdWhale Swift 패키지의 버전을 선택합니다. (최신 SDK 버전: 1.0.8)
{% endhint %}

#### Package.swift 로 추가

```swift
dependencies: [
    .package(url: "https://github.com/adwhale-sdk/adwhale-sdk-ios.git", from: "0.0.1")
],
targets: [
    .target(
        name: "YourApp",
        dependencies: [
            .product(name: "AdWhaleSDK", package: "adwhale-sdk-ios"),
            .product(name: "AdWhaleAdMobAdapter", package: "adwhale-sdk-ios"),
            .product(name: "AdWhaleCaulyAdapter", package: "adwhale-sdk-ios"),
        ]
    )
]
```

{% hint style="info" %}
파트너 SDK(GoogleMobileAds / CaulySDK / AdFitSDK / AdmizeSdk)는 XCFramework 빌드 시 바이너리에 정적 링크되어 있습니다. **앱에서 별도로 설치할 필요가 없습니다.**
{% endhint %}

{% hint style="warning" %}
설치 후 [Project Setting](../project-setting.md) 의 `-ObjC` 링커 플래그 설정을 반드시 함께 적용하세요.
{% endhint %}

#### 애드몹 미디에이션 파트너 네트워크 추가 (선택)

AdMob 미디에이션 파트너 어댑터는 SDK 에 포함되지 않습니다. **앱이 직접 추가**합니다.

<table><thead><tr><th width="161.78515625">네트워크</th><th width="483.0546875">SPM 저장소</th><th width="282.6484375">버전</th></tr></thead><tbody><tr><td><sup>InMobi</sup></td><td><sup><code>https://github.com/googleads/googleads-mobile-ios-mediation-inmobi.git</code></sup></td><td><sup><code>11.1.101</code></sup></td></tr><tr><td><sup>AppLovin</sup></td><td><sup><code>https://github.com/googleads/googleads-mobile-ios-mediation-applovin.git</code></sup></td><td><sup><code>13.5.100</code></sup> </td></tr><tr><td><sup>Vungle(Liftoff)</sup></td><td><sup><code>https://github.com/googleads/googleads-mobile-ios-mediation-liftoffmonetize.git</code></sup></td><td><sup><code>7.7.0</code></sup> </td></tr><tr><td><sup>DT Exchange</sup></td><td><sup><code>https://github.com/googleads/googleads-mobile-ios-mediation-dtexchange.git</code></sup></td><td><sup><code>8.4.401</code></sup> </td></tr><tr><td><sup>Mintegral</sup></td><td><sup><code>https://github.com/googleads/googleads-mobile-ios-mediation-mintegral.git</code></sup></td><td><sup><code>8.0.700</code></sup> </td></tr><tr><td><sup>Pangle</sup></td><td><sup><code>https://github.com/googleads/googleads-mobile-ios-mediation-pangle.git</code></sup></td><td><sup><code>7.9.600</code></sup></td></tr><tr><td><sup>Unity Ads</sup></td><td><sup><code>https://github.com/googleads/googleads-mobile-ios-mediation-unity.git</code></sup></td><td><sup><code>4.16.601</code></sup> </td></tr><tr><td><sup>Moloco</sup></td><td><sup><code>https://github.com/googleads/googleads-mobile-ios-mediation-moloco.git</code></sup></td><td><sup><code>4.3.2</code></sup> </td></tr></tbody></table>

{% hint style="info" %}
**버전을 반드시 위 표대로 고정하세요.**\
AdWhale AdMob 어댑터는 `Google-Mobile-Ads-SDK 13.0.0` 기준으로 빌드되어 있습니다.\
파트너 어댑터가 다른 메이저 버전의 GMA 를 끌어오면 런타임에 광고가 로드되지 않습니다.
{% endhint %}
