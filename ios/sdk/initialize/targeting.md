# 3.3 타겟팅 설정

{% hint style="info" %}
* 광고 요청에 아동 대상 여부 · 동의 연령 미만 사용자 여부 · 광고 콘텐츠 등급 한도를 지정할 수 있습니다.
* 타겟팅 설정 제어는 일부 광고 소스에만 적용되며, 타겟팅 설정을 지원하지 않는 경우 적용되지 않을 수 있습니다.
* <mark style="background-color:red;">**애플 앱스토어의 콘텐츠 설정과 SDK 설정이 반드시 동일하게 설정되어야 합니다.**</mark>
* AdWhale SDK는 Bidding 광고를 우선적으로 사용하며, Bidding 광고를 사용하기 위해서는 <mark style="background-color:red;">**아동 대상 설정 FALSE, 광고 콘텐츠 필터링 T 이상 등급 설정이 필요합니다.**</mark>
{% endhint %}

### 1. 아동 대상 설정

* [아동 온라인 개인 정보 보호법(COPPA)](https://www.ftc.gov/business-guidance/privacy-security/childrens-privacy)에 따라 아동 대상 취급 설정이 가능합니다.
* 광고를 요청할 때 콘텐츠의 아동 대상 서비스 취급 여부를 지정할 수 있습니다.
* 콘텐츠를 아동 대상으로 처리하도록 지정하면 해당 광고 요청에 대한 관심 기반 광고 및 리마케팅 광고가 사용 중지됩니다.

{% hint style="danger" %}
**아동용으로 광고 요청 시 Bidding 네트워크에 광고 요청이 불가하므로 FALSE 설정이 필요합니다.**
{% endhint %}

{% tabs %}
{% tab title="Swift" %}
```swift
AdWhaleMediationAds.setCoppa(true)

let applied = AdWhaleMediationAds.isCoppaApplied   // 현재 설정 조회
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[AdWhaleMediationAds setCoppa:YES];

BOOL applied = [AdWhaleMediationAds isCoppaApplied];   // 현재 설정 조회
```
{% endtab %}
{% endtabs %}

| setCoppa 설정   | 설명                                      |
| ------------- | --------------------------------------- |
| `true`        | COPPA에 따라 콘텐츠를 아동 대상으로 처리하도록 지정하는 경우    |
| `false` (기본값) | COPPA에 따라 콘텐츠를 아동 대상으로 처리하지 않도록 지정하는 경우 |

### 2. 동의 연령 미만 사용자 설정 방법

* 유럽 경제 지역(EEA)에 거주하는 동의 연령 미만의 사용자를 대상으로 하는 서비스로 취급하도록 광고 요청에 표시할 수 있습니다.
* TFUA(동의 연령 미만의 유럽 사용자가 대상임을 나타내는 태그) 매개변수가 광고 요청에 포함되며, 모든 광고 요청에서 리마케팅을 포함한 개인 맞춤 광고가 사용 중지됩니다.
* `.yes` 로 설정하는 경우 IDFA 수집도 차단됩니다.
* [아동 대상 설정](targeting.md#id-1)과 동시에 설정하면 안 되며, 이 경우 아동 대상 설정이 우선 적용됩니다.

{% tabs %}
{% tab title="Swift" %}
```swift
AdWhaleMediationAds.setTagForUnderAgeOfConsent(.yes)
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[AdWhaleMediationAds setTagForUnderAgeOfConsent:AdWhaleTagForUnderAgeOfConsentYes];
```
{% endtab %}
{% endtabs %}

| setTagForUnderAgeOfConsent 설정 (Objective-C) | 설정 (Swift)     | 설명                                                     |
| ------------------------------------------- | -------------- | ------------------------------------------------------ |
| `AdWhaleTagForUnderAgeOfConsentYes`         | `.yes`         | 광고 요청이 EEA에 거주하는 동의 연령 미만의 사용자를 대상으로 처리하도록 지정하는 경우     |
| `AdWhaleTagForUnderAgeOfConsentNo`          | `.no`          | 동의 연령 미만의 사용자를 대상으로 하지 않음을 명시하는 경우                     |
| `AdWhaleTagForUnderAgeOfConsentUnspecified` | `.unspecified` | 광고 요청이 EEA에 거주하는 동의 연령 미만의 사용자 취급 방법을 지정하지 않는 경우 (기본값) |

### 3. 광고 콘텐츠 필터링

* [App Store 심사 지침](https://developer.apple.com/app-store/review/guidelines/)에 따라 앱에 표시되는 광고는 앱의 연령 등급에 적합해야 합니다. App Store Connect에 설정한 연령 등급에 맞춰 광고 콘텐츠 등급 한도를 설정하세요.
* 광고 콘텐츠 등급 한도가 설정된 경우 콘텐츠 등급이 설정된 한도 이하인 광고가 게재되며, 다음 중 하나로 설정해야 합니다.
* 콘텐츠 등급 한도 설정에 대해 [각 광고 요청에 대한 콘텐츠 등급 한도 설정하기](https://support.google.com/admob/answer/10477886?hl=ko) 또는 [앱 또는 계정의 광고 콘텐츠 등급 한도 설정하기](https://support.google.com/admob/answer/7562142?hl=ko) 를 참고 부탁드립니다.

{% hint style="danger" %}
**부모 지도 요망(ParentalGuidance) 이하 등급으로 설정하면 Bidding 네트워크에 광고 요청이 불가하므로 Teen 또는 MatureAudience 로 설정하세요.**
{% endhint %}

| 광고 콘텐츠 등급 한도 (Objective-C)               | 광고 콘텐츠 등급 한도 (Swift) | 설명                                    |
| ---------------------------------------- | -------------------- | ------------------------------------- |
| `AdWhaleAdContentRatingGeneral`          | `.general`           | <p>일반 잠재고객<br>(가족, 아동을 포함한 모든 대상)</p> |
| `AdWhaleAdContentRatingParentalGuidance` | `.parentalGuidance`  | 부모 지도 요망                              |
| `AdWhaleAdContentRatingTeen`             | `.teen`              | 청소년                                   |
| `AdWhaleAdContentRatingMatureAudience`   | `.matureAudience`    | 성인                                    |

{% tabs %}
{% tab title="Swift" %}
```swift
AdWhaleMediationAds.setMaxAdContentRating(.teen)
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[AdWhaleMediationAds setMaxAdContentRating:AdWhaleAdContentRatingTeen];
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
아동 대상 설정 · 동의 연령 미만 사용자 설정 · 광고 콘텐츠 등급 한도는 `UserDefaults` 에 저장되어 앱을 재시작해도 유지됩니다. 값을 바꾸려면 해당 API 를 다시 호출하세요.

* 호출하면 이미 등록된 광고 네트워크 SDK 에 바로 적용되고, 저장된 값은 다음 실행 때 어댑터를 등록(`register()`)하는 시점에 적용됩니다.
* 설정을 처음 하거나 바꿀 때는 **광고를 로드하기 전에** 호출하세요. 이미 로드된 광고에는 바뀐 값이 반영되지 않습니다.
{% endhint %}
