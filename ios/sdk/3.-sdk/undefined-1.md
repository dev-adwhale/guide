# 타겟팅 설정

{% hint style="info" %}
* 광고 요청에 타겟팅 정보를 제공합니다.
* <mark style="background-color:red;">**구글 플레이스토어의 콘텐츠 설정과 SDK 설정이 반드시 동일하게 설정되어야 합니다.**</mark>
* Adwhale SDK는 Bidding 광고를 우선적으로 사용하며, Bidding 광고를 사용하기 위해서는 <mark style="background-color:red;">**아동대상 설정 FALSE, 광고 콘텐츠 필터링 T 이상 등급 설정이 필요합니다.**</mark>
{% endhint %}



### 1. 아동 대상 설정

* [아동 온라인 개인 정보 보호법(COPPA)](https://www.ftc.gov/business-guidance/privacy-security/childrens-privacy)에 따라 `tagForChildDirectedTreatment` 설정이 가능합니다.
* 광고를 요청할 때 콘텐츠의 아동 대상 서비스 취급 여부를 지정할 수 있습니다.
* 콘텐츠를 아동 대상으로 처리하도록 지정하면 해당 광고 요청에 대한 관심 기반 광고 및 리마케팅 광고가 사용 중지됩니다.

{% hint style="danger" %}
**아동용으로 광고 요청시 Bidding 네트워크에 광고 요청이 불가하므로 FALSE 설정이 필요합니다.**
{% endhint %}

{% tabs %}
{% tab title="Swift" %}
```swift
AdWhaleAds.sharedInstance.tagForChildDirectedTreatment(true)
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[[AdWhaleAds sharedInstance] tagForChildDirectedTreatment:@YES];
```
{% endtab %}
{% endtabs %}

| tagForChildDirectedTreatment 설정 | 설명                                      |
| ------------------------------- | --------------------------------------- |
| true                            | COPPA에 따라 콘텐츠를 아동 대상으로 처리하도록 지정하는 경우    |
| false                           | COPPA에 따라 콘텐츠를 아동 대상으로 처리하지 않도록 지정하는 경우 |
| 미설정                             | 광고 요청에서 COPPA에 따른 콘텐츠 취급 방법을 지정하지 않는 경우 |



### 2. 동의 연령 미만 사용자 설정 방법

* 유럽 경제 지역(EEA)에 거주하는 동의 연령 미만의 사용자를 대상으로 하는 서비스로 취급하도록 광고 요청에 표시할 수 있습니다.
* TFUA(동의 연령 미만의 유럽 사용자가 대상임을 나타내는 태그) 매개변수가 광고 요청에 포함되며, 모든 광고 요청에서 리마케팅을 포함한 개인 맞춤 광고가 사용 중지됩니다.
* `true` 로 설정하는 경우 IDFA 수집도 차단됩니다.
* [아동 대상 설정](undefined-1.md#id-1)과 동시에 true 로 설정하면 안 되며, 이 경우 아동 대상 설정이 우선 적용됩니다.

{% tabs %}
{% tab title="Swift" %}
```swift
AdWhaleAds.sharedInstance.tagForUnderAgeOfConsent(true)
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[[AdWhaleAds sharedInstance] tagForUnderAgeOfConsent:@YES];
```
{% endtab %}
{% endtabs %}

<table data-header-hidden><thead><tr><th width="362">tagForUnderAgeOfConsent 설정</th><th>설명</th></tr></thead><tbody><tr><td>tagForUnderAgeOfConsent 설정</td><td>설명</td></tr><tr><td>true</td><td>광고 요청이 EEA에 거주하는 동의 연령 미만의 사용자를 대상으로 처리하도록 지정하는 경우</td></tr><tr><td>미설정</td><td>광고 요청이 EEA에 거주하는 동의 연령 미만의 사용자 취급 방법을 지정하지 않는 경우</td></tr></tbody></table>



### 3. 광고 콘텐츠 필터링

* 광고 내 관련 혜택이 포함된 Google Play의 [부적절한 광고 정책](https://support.google.com/googleplay/android-developer/answer/9857753?hl=ko#zippy=,examples-of-common-violations)을 준수하려면 콘텐츠 자체가 Google Play 정책을 준수하더라도 앱에 표시되는 모든 광고 및 관련 혜택은 앱의 [콘텐츠 등급](https://support.google.com/googleplay/android-developer/answer/9898843?hl=ko)에 적합해야 합니다.
* 광고 콘텐츠 등급 한도가 설정된 경우 콘텐츠 등급이 설정된 한도 이하인 광고가 게재되며, 다음 중 하나로 설정해야합니다.
* 콘텐츠 등급 한도 설정에 대해 [각 광고 요청에 대한 콘텐츠 등급 한도 설정하기](https://support.google.com/admob/answer/10477886?hl=ko) 또는 [앱 또는 계정의 광고 콘텐츠 등급 한도 설정하기](https://support.google.com/admob/answer/7562142?hl=ko) 를 참고 부탁드립니다.

{% hint style="danger" %}
**청소년(Teen) 이하 등급 설정시 Bidding 네트워크에 광고 요청이 불가하므로 Teen 또는 MatureAudience 설정이 필요합니다.**
{% endhint %}

| 광고 콘텐츠 등급 한도 (Objective-C)                | 광고 콘텐츠 등급 한도 (Swift) | 설명                                    |
| ----------------------------------------- | -------------------- | ------------------------------------- |
| AdWhaleMaxAdContentRatingGeneral          | .general             | <p>일반 잠재고객<br>(가족, 아동을 포함한 모든 대상)</p> |
| AdWhaleMaxAdContentRatingParentalGuidance | .parentalGuidance    | 부모 지도 요망                              |
| AdWhaleMaxAdContentRatingTeen             | .teen                | 청소년                                   |
| AdWhaleMaxAdContentRatingMatureAudience   | .matureAudience      | 성인                                    |

{% tabs %}
{% tab title="Swift" %}
```swift
AdWhaleAds.sharedInstance.maxAdContentRating(.general)
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[[AdWhaleAds sharedInstance] maxAdContentRating:AdWhaleMaxAdContentRatingGeneral];
```
{% endtab %}
{% endtabs %}
