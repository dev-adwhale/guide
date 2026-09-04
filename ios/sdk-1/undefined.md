# 들어가기 전에

### 1. 기존 SDK에서 마이그레이션

***

기존에 사용하시던 **애드웨일 iOS SDK 1.0.x**(`AdWhaleSDK` CocoaPods)와 이 문서의 SDK 는 **다른 제품**입니다. 광고를 붙이는 방식(클래스 이름과 동작이 전부 다르므로)이 바뀌었기 때문에 앱 코드를 수정해야 합니다.&#x20;

{% hint style="info" %}
가장 큰 변화는 **지면을 지정하는 방식**입니다. 기존에는 앱이 애드몹 광고단위 ID(`adUnitId`)를 직접 넣었지만, 이제는 애드웨일 전용 지면 ID(`placementUid`)만 넣고 **어떤 네트워크를 어떤 순서로 호출할지는 애드웨일 서버가 내려줍니다.**
{% endhint %}

***

### 2. 변경사항들

***

#### 광고 네트워크 추가 혹은 노출 순서 변경을 위해 앱을 다시 배포할 필요가 없어졌습니다.

기존에는 앱 코드에 광고 네트워크의 광고단위 ID(예: 애드몹 `adUnitId`) 를 직접 넣었습니다. 그래서 네트워크를 추가하거나 노출 순서를 바꾸려면 앱을 다시 배포해야 했습니다. 이제 앱에는 **애드웨일 전용 지면 ID 하나만** 넣습니다. 그 지면에서 어떤 네트워크를 어떤 순서로 시도할지는 **애드웨일 관리자에서 설정하고 서버가 앱에 내려줍니다.**

<table><thead><tr><th width="139.78515625"></th><th width="325.9921875">기존</th><th>현재</th></tr></thead><tbody><tr><td>앱 코드에 넣는 값</td><td>네트워크별 광고단위 ID(예: 애드몹 <code>adUnitId</code>)</td><td>애드웨일 전용 지면 ID(<code>placementUid</code>)</td></tr><tr><td>네트워크 추가</td><td>앱 재배포 필요</td><td>애드웨일 관리자에서 설정</td></tr><tr><td>노출 순서 변경</td><td>앱 재배포 필요</td><td>애드웨일 관리자에서 설정</td></tr></tbody></table>

{% hint style="info" %}
전환하고 나면 광고 운영 변경에 앱 업데이트가 필요하지 않습니다. 전환 작업의 가장 큰 이득입니다.
{% endhint %}

#### 필요한 네트워크만 담습니다.

기존에는 SDK 하나에 지원하는 모든 네트워크가 포함되어 있었습니다. 이제는 **핵심 모듈에 사용할 네트워크만 골라 추가**합니다. 앱 용량이 줄고, 쓰지 않는 네트워크 때문에 앱 심사에서 설명해야 할 항목도 줄어듭니다. 담을 광고 네트워크를 코드에서 한 줄 등록해 주기만 하면 완료됩니다.

#### 실패 원인을 구분해서 알려줍니다.

기존에는 광고를 못 받으면 오류 객체 하나만 왔습니다. 그래서 **연동을 잘못한 것인지, 그냥 광고가 없는 것인지** 구분하기 어려웠습니다. 이제 실패 콜백이 두 가지로 나뉩니다.

<table><thead><tr><th width="145.265625">의미</th><th>대응</th></tr></thead><tbody><tr><td>연동 오류</td><td>코드나 설정을 고쳐야 합니다. 재시도해도 같은 실패가 반복됩니다.</td></tr><tr><td>광고 미충족</td><td>정상 상황입니다. 자체 광고로 대체하는 등 후처리가 필요합니다.</td></tr></tbody></table>

{% hint style="info" %}
구체적인 코드 값은 **에러코드 정책**에 있습니다.
{% endhint %}

***

### 3.전환시점

***

<table><thead><tr><th width="328.3671875">상황</th><th>권장 내용</th></tr></thead><tbody><tr><td>신규 프로젝트</td><td>이 SDK를 사용하세요.</td></tr><tr><td>운영 중인 앱</td><td>다음 정기 업데이트에 맞춰 한 번에 전환</td></tr><tr><td>Flutter / React Native 플러그인 사용 중인 앱</td><td>아직 전환하지 마세요. 각 플러그인이 기존 1.0.8을 사용합니다.</td></tr><tr><td>새 광고 포맷(팝업)이나 네트워크가 필요한 앱</td><td>이 SDK를 사용하세요.</td></tr></tbody></table>

{% hint style="info" %}
기존 1.0.x 는 계속 동작합니다. 서둘러 전환하실 필요는 없습니다. 다만 신규 광고 포맷(팝업)과 신규 네트워크(노출 fillrate를 높이기 위해 Levelplay·Cauly·AdFit·Admize 등 직접 추가 연동)는 이 SDK 에만 제공됩니다.
{% endhint %}

***

### 4.구현 레벨에서의 세부변경사항

***

구현 레벨에서의 자세한 변경사항은 아래 표를 참고하여 주시기 바랍니다.

#### **주요 변경표**

<table><thead><tr><th width="165.13671875">항목</th><th width="237.23046875">기존(1.0.x)</th><th>현재</th></tr></thead><tbody><tr><td>설치</td><td><ul><li>CocoaPods(pod 'AdWhaleSDK')</li><li>Swift Package Manager</li></ul></td><td><ul><li>Swift Package Manager</li></ul></td></tr><tr><td>패키지 구성</td><td>단일 pod(파트너 SDK 내장됨)</td><td>애드웨일 Core + 네트워크별 파트너 어댑터(선택)</td></tr><tr><td>지면 지정</td><td>애드몹 광고단위 ID(<code>adUnitId</code>)</td><td>애드웨일 전용 지면 ID(<code>placementUid</code>)</td></tr><tr><td>워터폴</td><td>애드몹 미디에이션에 위임</td><td>애드웨일 전용 서버가 제어</td></tr><tr><td>진입 클래스</td><td>AdWhaleAds.sharedInstance(싱글턴)</td><td>AdWhaleMediationAds(static)</td></tr><tr><td>초기화</td><td>intialize(appId:rootViewController:...)</td><td>initialize(publisherUid:completion:)</td></tr><tr><td>실패 콜백</td><td>Error 객체</td><td>(statusCode: Int, message: String)</td></tr><tr><td>rootViewController</td><td>전달 필요</td><td>전달 불필요(SDK 내부 처리)</td></tr><tr><td>어댑터 등록</td><td>X</td><td>코드상에 register() 호출 필요</td></tr></tbody></table>

{% hint style="info" %}
**두 SDK 를 동시에 탑재할 수 없습니다.**&#x20;

클래스 이름이 겹치지는 않지만 내부에 포함된 파트너 SDK 가 중복되어 링커 에러 또는 런타임 오작동이 발생합니다.&#x20;

기존 pod 을 제거한 뒤 SPM 으로 전환하세요.
{% endhint %}

#### **클래스 변경표**

<table><thead><tr><th width="109.1015625">포맷</th><th width="306.23828125">기존</th><th>현재</th></tr></thead><tbody><tr><td>전역 설정</td><td>AdWhaleAds</td><td>AdWhaleMediationAds</td></tr><tr><td>배너</td><td>AdWhaleBannerAd</td><td>AdWhaleMediationAdView</td></tr><tr><td>전면</td><td>AdWhaleInterstitialAd</td><td>AdWhaleMediationInterstitialAd</td></tr><tr><td>보상형</td><td>AdWhaleRewardAd</td><td>AdWhaleMediationRewardAd</td></tr><tr><td>네이티브</td><td>AdWhaleNativeAdLoader + AdWhaleNativeAd</td><td>AdWhaleMediationNativeAdView(템플릿)<br>커스텀 바인딩도 지원</td></tr><tr><td>앱오프닝</td><td>AdWhaleAppOpenAd</td><td>AdWhaleMediationAppOpenAd</td></tr><tr><td>팝업</td><td>X</td><td>AdWhaleMediationPopupAd (신규)</td></tr></tbody></table>

#### **열겨형/모델 변경표**

| 기존                        | 현재                         |
| ------------------------- | -------------------------- |
| AdWhaleAdSize             | AdWhaleBannerSize          |
| AdWhaleMaxAdContentRating | AdWhaleAdContentRating     |
| GdprConsentStatus         | AdWhaleGdprConsentStatus   |
| AdWhaleReward             | AdWhaleMediationRewardItem |
| AdWhaleGdprType           | 제거                         |
| AdWhaleLogLevel           | 동일                         |

***

### 5.구현 대응 핵심 가이드

***

#### 초기화 대응

{% hint style="info" %}
기존에는 애드몹 앱 ID를 초기화 파라미터로 넘겼습니다. 이제는 애드웨일 PublisherUID 를 넘기고, 애드몹 앱 ID는 Info.plist에 둡니다. 그리고 사용할 어댑터를 register()로 등록해야 해당 어댑터 광고 네트워크를 사용할 수 있습니다.
{% endhint %}

{% hint style="warning" %}
**`register()` 를 호출하지 않으면 해당 네트워크의 광고 노출은 조용히 건너뛰어집니다.** 크래시나 에러 없이 광고만 나오지 않으므로 알아채기 어렵습니다. 로그에 `No banner adapter registered for network: …` 가 보이면 이 경우입니다.
{% endhint %}

{% tabs %}
{% tab title="Swift" %}
**기존(1.0.x)**

```swift
AdWhaleAds.sharedInstance.initialize(
    appId: "ca-app-pub-…~…",
    rootViewController: self,
    useGdpr: true,
    testDevices: nil
) {
    // 초기화 완료
}
```

***

**현재**

```swift
import AdWhaleSDK
import AdWhaleAdMobAdapter      // 사용할 어댑터만 import
import AdWhaleCaulyAdapter

AdWhaleAdMobAdapter.register()  // ★ 초기화 전에 등록
AdWhaleCaulyAdapter.register()

AdWhaleMediationAds.initialize(publisherUid: "발급받은 애드웨일 PUBLISHER_UID") { statusCode, message in
    // 초기화 완료
}
```

{% hint style="info" %}
Publisher UID 는 `Info.plist` 에 넣어 파라미터를 생략할 수도 있습니다. 자세한 내용은 [2-3. SDK 연동 키 설정](https://dillinger.io/02-03-integration-keys.md) 을 참고하세요.
{% endhint %}
{% endtab %}

{% tab title="Objective-C" %}
**기존(1.0.x)**

```objective-c
[[AdWhaleAds sharedInstance] initializeWithAppId:@"ca-app-pub-…~…"
                              rootViewController:self
                                         useGdpr:YES
                                     testDevices:nil
                               completionHandler:^{ }];
```

***

**현재**

```swift
@import AdWhaleSDK;
@import AdWhaleAdMobAdapter;      // 사용할 어댑터만 import

[AdWhaleAdMobAdapter register];   // ★ 초기화 전에 등록

[AdWhaleMediationAds initializeWithPublisherUid:@"발급받은 애드웨일 PUBLISHER_UID"
                                     completion:^(NSInteger statusCode, NSString *message) { }];
```
{% endtab %}
{% endtabs %}

#### 배너 대응

| 기존 AdWhaleBannerDelegate                    | 현재 AdWhaleMediationAdViewDelegate          |
| ------------------------------------------- | ------------------------------------------ |
| bannerViewDidReceiveAd(\_:)                 | adViewDidReceiveAd(\_:)                    |
| bannerView(\_:didFailToReceiveAdWithError:) | adView(\_:didFailToLoadWithError:message:) |
| bannerViewDidRecordClick(\_:)               | adViewDidClick(\_:) **(optional)**         |
| bannerViewDidRecordImpression(\_:)          | 제거                                         |
| bannerViewWillPresentScreen(\_:)            | bannerDidShowLandingScreen(\_:)            |
| bannerViewDidDismissScreen(\_:)             | bannerDidCloseLandingScreen(\_:)           |

#### 전면 대응

| 기존 AdWhaleInterstitialDelegate                  | 현재 AdWhaleMediationInterstitialDelegate          |
| ----------------------------------------------- | ------------------------------------------------ |
| adDidReceiveInterstitialAd(\_:)                 | interstitial(\_:didLoadWith:)                    |
| adDidFailToReceiveInterstitialAdWithError(\_:)  | interstitial(\_:didFailToLoadWithError:message:) |
| adWillPresentInterstitialAd(\_:)                | interstitialDidShow(\_:)                         |
| ad(\_:didFailToPresentInterstitialAdWithError:) | interstitial(\_:didFailToShowWithError:message:) |
| adDidDismissInterstitialAd(\_:)                 | interstitialDidClose(\_:)                        |
| X                                               | interstitialDidClick(\_:) **(신규)**               |

#### 보상형 대응

기존에는 Delegate 하나가 로드, 노출, 보상을 모두 받았습니다. 이제 3개의 콜백으로 분리되었습니다.

<table><thead><tr><th width="345.14453125">기존 AdWhaleRewardDelegate</th><th>현재</th></tr></thead><tbody><tr><td>adDidReceiveRewardAd(_:)</td><td>AdWhaleMediationRewardAdLoadDelegate.rewardedAd(_:didLoadWith:)</td></tr><tr><td>adDidFailToReceiveRewardAdWithError(_:)</td><td>AdWhaleMediationRewardAdLoadDelegate.rewardedAd(_:didFailToLoadWithError:message:)</td></tr><tr><td>adWillPresentRewardAd(_:)</td><td>AdWhaleRewardedFullScreenDelegate</td></tr><tr><td>adDidEarnReward(_:)</td><td>AdWhaleUserEarnedRewardDelegate.userDidEarnReward(_:)</td></tr></tbody></table>

#### 네이티브 대응

| 기존 AdWhaleNativeAdLoaderDelegate            | 현재 AdWhaleMediationNativeAdDelegate          |
| ------------------------------------------- | -------------------------------------------- |
| nativeAdLoaderDidReceiveAd(\_:)             | nativeAd(\_:didLoadWith:)                    |
| nativeAdLoaderDidFailToReceiveAd(\_:error:) | nativeAd(\_:didFailToLoadWithError:message:) |

{% hint style="info" %}
기존에는 `AdWhaleNativeAdLoader` 로 로드한 뒤 `bind()` 로 직접 뷰를 구성해야 했습니다. 이제 **고정 템플릿**을 쓰면 뷰 구성이 필요 없습니다. 기존처럼 직접 바인딩하려면 커스텀 바인딩을 사용하세요. ([4-5. 네이티브](https://dillinger.io/04-05-native.md))&#x20;
{% endhint %}

#### 앱오프닝 대응

<table><thead><tr><th width="334.0546875">기존 AdWhaleAppOpenAdDelegate</th><th>현재 AdWhaleMediationAppOpenAdDelegate</th></tr></thead><tbody><tr><td>adDidReceiveAppOpenAd(_:)</td><td>appOpenAd(_:didLoadWith:)</td></tr><tr><td>adDidFailToReceiveAppOpenAd(error:)</td><td>appOpenAd(_:didFailToLoadWithError:message:)</td></tr><tr><td>adWillPresentAppOpenAd()</td><td>appOpenAdDidShow(_:)</td></tr><tr><td>adDidFailToPresentAppOpenAd(error:)</td><td>appOpenAd(_:didFailToShowWithError:message:)</td></tr><tr><td>adDidDismissAppOpenAd()</td><td>appOpenAdDidDismiss(_:)</td></tr></tbody></table>

***

### 6.마이그레이션 체크리스트

***

* [ ] **애드웨일 전용 지면 ID 발급** - 애드웨일 담당자에게 지면별 ID 를 요청하세요. 이게 없으면 광고가 나오지 않습니다.&#x20;
* [ ] **기존 SDK 제거** - `Podfile`  에서 `pod 'AdWhaleSDK'` 및 관련 `source` 제거 → `pod install`
* [ ] **새 SDK 설치** - SPM 으로 `AdWhaleSDK` + **사용할 어댑터만** 추가 ([2-1-1](https://dillinger.io/02-01-01-spm.md))
* [ ] **애드웨일 전용 지면 ID로 교체** - `Info.plist` 에 Publisher UID · `GADApplicationIdentifier` 설정 ([2-3](https://dillinger.io/02-03-integration-keys.md))
* [ ] **애드웨일 전용 지면 ID로 교체** - `adUnitId` → **`placementUid`** 로 교체
* [ ] **프로젝트 설정** - 앱 타겟 **Other Linker Flags** 에 `-ObjC` 추가 ([2-2](https://dillinger.io/02-02-project-setting.md))
* [ ] **프로젝트 설정** - SKAdNetwork ID 목록 갱신 ([2-5](https://dillinger.io/02-05-skadnetwork.md))
* [ ] **초기화 코드 수정** - 초기화 전에 어댑터 **`register()`** 호출
* [ ] **광고 코드 수정** - 실패 콜백을 `Error` → `(statusCode, message)` 로 변경
* [ ] **광고 코드 수정** - `rootViewController` 전달 코드 제거
* [ ] **광고 코드 수정** - (보상형 사용시) 보상형 델리게이트 3개 분리 반영
* [ ] **실기기 확인** - 실기기에서 포맷별 노출 · 콜백 확인

{% hint style="info" %}
`placementUid` 는 기존 `adUnitId` 와 **다른 값**입니다. 기존 애드몹 광고단위 ID 를 그대로 넣으면 서버가 설정을 찾지 못해 `statusCode 200` 으로 실패합니다. 애드웨일 담당자에게 지면별 Placement UID 를 발급받으세요.
{% endhint %}

***

### 7. 자주 겪는 오해

***

#### 기존 광고단위 ID 를 지면 ID 자리에 넣으면 안 됩니다

두 값은 완전히 다릅니다. 기존에 쓰던 애드몹 광고단위 ID 를 그대로 넣으면 서버가 해당 지면 설정을 찾지 못해 **연동 오류로 실패**합니다. 애드웨일 담당자에게 발급받은 지면 ID 를 사용하세요.

#### 네트워크 등록을 빼먹으면 조용히 실패합니다 <a href="#id-107" id="id-107"></a>

SDK 에 네트워크를 추가했더라도 코드에서 등록하지 않으면 그 네트워크는 **건너뛰어집니다.** 오류도 크래시도 없이 광고만 나오지 않으므로 알아채기 어렵습니다.

광고가 전혀 나오지 않는데 실패 콜백도 오지 않는다면 이 경우를 먼저 확인하세요. 등록 방법은 [3. SDK 초기화](https://dillinger.io/03-initialize.md) 에 있습니다.

#### 광고가 나오지 않는 것이 항상 오류는 아닙니다 <a href="#id-113" id="id-113"></a>

광고 재고가 없어 채워지지 않는 것은 정상 동작입니다. 실패 코드가 “광고 미충족” 이면 연동은 정상이므로 코드를 고칠 필요가 없습니다.

#### 지면 설정을 바꿨는데 반영이 안 됩니다 <a href="#id-117" id="id-117"></a>

설정은 앱이 광고를 요청할 때 서버에서 받아옵니다. 앱을 완전히 종료한 뒤 다시 실행해 보세요.

***

