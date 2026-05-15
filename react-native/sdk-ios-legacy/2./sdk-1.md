# SDK 연동 키 설정

{% hint style="info" %}
Info.plist 파일에 GADApplicationIdentifier 키와 발급된 AdMob APP ID를 추가합니다.
{% endhint %}

{% hint style="danger" %}
<mark style="background-color:red;">GADApplicationIdentifier</mark> 를 추가하지 않으면 다음 에러가 발생하며 앱이 비정상 종료됩니다.

<mark style="background-color:red;">The Google Mobile Ads SDK was initialized incorrectly.</mark>

***

\<key>GADApplicationIdentifier\</key>

\<string>발급받은 애드몹 APPLICATION\_ID\</string>
{% endhint %}

{% hint style="info" %}
* 각 광고 마다 발급 받은 AD\_UNIT\_ID가 필요합니다.
* 발급 받은 AD\_UNIT\_ID는 구현부에 입력하게 됩니다.
{% endhint %}
