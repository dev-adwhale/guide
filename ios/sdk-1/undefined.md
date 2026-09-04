# 들어가기 전에

### 기존 SDK에서 마이그레이션

기존에 사용하시던 **AdWhale iOS SDK 1.0.x**(`AdWhaleSDK` CocoaPods)와 이 문서의 SDK 는 **다른 제품**입니다. 광고를 붙이는 방식(클래스 이름과 동작이 전부 다르므로)이 바뀌었기 때문에 앱 코드를 수정해야 합니다.&#x20;

{% hint style="info" %}
가장 큰 변화는 **지면을 지정하는 방식**입니다. 기존에는 앱이 애드몹 광고단위 ID(`adUnitId`)를 직접 넣었지만, 이제는 애드웨일 전용 지면 ID(`placementUid`)만 넣고 **어떤 네트워크를 어떤 순서로 호출할지는 애드웨일 서버가 내려줍니다.**
{% endhint %}

### 1.무엇이 바뀌었나

#### 지면 운영이 애드몹 미디에이션 에서 애드웨일 자체 운영으로 넘어왔습니다.

기존에는 앱 코드에 광고 네트워크의 광고단위 ID(예: 애드몹 `adUnitId`) 를 직접 넣었습니다. 그래서 네트워크를 추가하거나 노출 순서를 바꾸려면 앱을 다시 배포해야 했습니다. 이제 앱에는 **애드웨일 전용 지면 ID 하나만** 넣습니다. 그 지면에서 어떤 네트워크를 어떤 순서로 시도할지는 **애드웨일 관리자에서 설정하고 서버가 앱에 내려줍니다.**

<table><thead><tr><th width="139.78515625"></th><th width="325.9921875">기존</th><th>현재</th></tr></thead><tbody><tr><td>앱 코드에 넣는 값</td><td>네트워크별 광고단위 ID(예: 애드몹 <code>adUnitId</code>)</td><td>애드웨일 전용 지면 ID(<code>placementUid</code>)</td></tr><tr><td>네트워크 추가</td><td>앱 재배포 필요</td><td>애드웨일 관리자에서 설정</td></tr><tr><td>노출 순서 변경</td><td>앱 재배포 필요</td><td>애드웨일 관리자에서 설정</td></tr></tbody></table>

{% hint style="info" %}
전환하고 나면 광고 운영 변경에 앱 업데이트가 필요하지 않습니다. 전환 작업의 가장 큰 이득입니다.
{% endhint %}

#### 필요한 네트워크만 담습니다

기존에는 SDK 하나에 지원하는 모든 네트워크가 포함되어 있었습니다. 이제는 **핵심 모듈에 사용할 네트워크만 골라 추가**합니다. 앱 용량이 줄고, 쓰지 않는 네트워크 때문에 앱 심사에서 설명해야 할 항목도 줄어듭니다. 대신 담은 네트워크를 코드에서 한 줄 등록해 주셔야 합니다.

#### 실패 원인을 구분해서 알려줍니다

기존에는 광고를 못 받으면 오류 객체 하나만 왔습니다. 그래서 **연동을 잘못한 것인지, 그냥 광고가 없는 것인지** 구분하기 어려웠습니다. 이제 실패 콜백이 두 가지로 나뉩니다.

<table><thead><tr><th width="145.265625">의미</th><th>대응</th></tr></thead><tbody><tr><td>연동 오류</td><td>코드나 설정을 고쳐야 합니다. 재시도해도 같은 실패가 반복됩니다.</td></tr><tr><td>광고 미충족</td><td>정상 상황입니다. 자체 광고로 대체하는 등 후처리가 필요합니다.</td></tr></tbody></table>

{% hint style="info" %}
구체적인 코드 값은 **에러코드 정책**에 있습니다.
{% endhint %}

항목별 세부변경내용입니다.

<table><thead><tr><th width="165.13671875">항목</th><th width="237.23046875">기존(1.0.x)</th><th>현재</th></tr></thead><tbody><tr><td>설치</td><td><ul><li>CocoaPods(pod 'AdWhaleSDK')</li><li>Swift Package Manager</li></ul></td><td><ul><li>Swift Package Manager</li></ul></td></tr><tr><td>패키지 구성</td><td>단일 pod(파트너 SDK 내장됨)</td><td>애드웨일 Core + 네트워크별 파트너 어댑터(선택)</td></tr><tr><td>지면 지정</td><td>애드몹 광고단위 ID(<code>adUnitId</code>)</td><td>애드웨일 전용 지면 ID(<code>placementUid</code>)</td></tr><tr><td>워터폴</td><td>애드몹 미디에이션에 위임</td><td>애드웨일 전용 서버가 제어</td></tr><tr><td>진입 클래스</td><td>AdWhaleAds.sharedInstance(싱글턴)</td><td>AdWhaleMediationAds(static)</td></tr><tr><td>초기화</td><td>intialize(appId:rootViewController:...)</td><td>initialize(publisherUid:completion:)</td></tr><tr><td>실패 콜백</td><td>Error 객체</td><td>(statusCode: Int, message: String)</td></tr><tr><td>rootViewController</td><td>전달 필요</td><td>전달 불필요(SDK 내부 처리)</td></tr><tr><td>어댑터 등록</td><td>X</td><td>코드상에 register() 호출 필요</td></tr></tbody></table>

{% hint style="info" %}
**두 SDK 를 동시에 탑재할 수 없습니다.**&#x20;

클래스 이름이 겹치지는 않지만 내부에 포함된 파트너 SDK 가 중복되어 링커 에러 또는 런타임 오작동이 발생합니다.&#x20;

기존 pod 을 제거한 뒤 SPM 으로 전환하세요.
{% endhint %}

### 2.전환시점

<table><thead><tr><th width="328.3671875">상황</th><th>권장 내용</th></tr></thead><tbody><tr><td>신규 프로젝트</td><td>이 SDK를 사용하세요.</td></tr><tr><td>운영 중인 앱</td><td>다음 정기 업데이트에 맞춰 한 번에 전환</td></tr><tr><td>Flutter / React Native 플러그인 사용 중인 앱</td><td>아직 전환하지 마세요. 각 플러그인이 기존 1.0.8을 사용합니다.</td></tr><tr><td>새 광고 포맷(팝업)이나 네트워크가 필요한 앱</td><td>이 SDK를 사용하세요.</td></tr></tbody></table>

{% hint style="info" %}
기존 1.0.x 는 계속 동작합니다. 서둘러 전환하실 필요는 없습니다. 다만 신규 광고 포맷(팝업)과 신규 네트워크(노출 fillrate를 높이기 위해 Levelplay·Cauly·AdFit·Admize 등 직접 추가 연동)는 이 SDK 에만 제공됩니다.
{% endhint %}

### 3.클래스 대응표

<table><thead><tr><th width="109.1015625">포맷</th><th width="306.23828125">기존</th><th>현재</th></tr></thead><tbody><tr><td>전역 설정</td><td>AdWhaleAds</td><td>AdWhaleMediationAds</td></tr><tr><td>배너</td><td>AdWhaleBannerAd</td><td>AdWhaleMediationAdView</td></tr><tr><td>전면</td><td>AdWhaleInterstitialAd</td><td>AdWhaleMediationInterstitialAd</td></tr><tr><td>보상형</td><td>AdWhaleRewardAd</td><td>AdWhaleMediationRewardAd</td></tr><tr><td>네이티브</td><td>AdWhaleNativeAdLoader + AdWhaleNativeAd</td><td>AdWhaleMediationNativeAdView(템플릿)<br>커스텀 바인딩도 지원</td></tr><tr><td>앱오프닝</td><td>AdWhaleAppOpenAd</td><td>AdWhaleMediationAppOpenAd</td></tr><tr><td>팝업</td><td>X</td><td>AdWhaleMediationPopupAd (신규)</td></tr></tbody></table>













