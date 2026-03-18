# 앱 내 화면 전환

{% hint style="info" %}
앱전환광고(Transition Popup Ad)는 화면 전환 시점(예: 다른 화면으로 이동하기 직전)에 노출되는 팝업형 광고입니다. 액티비티/화면 전환 전 자연스러운 타이밍에 노출하여 전환률과 수익을 높일 수 있습니다.
{% endhint %}

#### **1. 주요특징**

* 화면 전환 시점에 노출되는 팝업형 광고
* 다양한 옵션 설정 지원 (placementName, region, gcoder)
* 로드, 표시, 닫힘 등 이벤트 기반 콜백 시스템으로 광고 상태 추적 가능

#### 2. 기본 구현 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleMediationTransitionPopupAd` 클래스를 사용하여 앱 전환 광고를 로드하고 표시하는 기본적인 구현 방법입니다.

{% tabs %}
{% tab title="Java" %}
```java
private AdWhaleMediationTransitionPopupAd transitionPopupAd;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    // 1. 인스턴스 생성 (placementUid)
    transitionPopupAd = new AdWhaleMediationTransitionPopupAd("발급받은 placement uid 값");

    // 2. 리스너 등록
    transitionPopupAd.setAdWhaleMediationTransitionPopupAdListener(new AdWhaleMediationTransitionPopupAdListener() {
        @Override public void onAdLoaded() { 
              // 로드 성공
            // 4. 표시 (로드 완료 후)
            transitionPopupAd.showAd(MainActivity.this, MainActivity.this.getSupportFragmentManager());
        }
        
        @Override public void onAdLoadFailed(int statusCode, String message) { 
            // 로드 실패 
        }
        
        @Override public void onAdShowed() { 
            // 표시됨
        }
        
        @Override public void onAdShowFailed(int statusCode, String message) { 
            // 표시 실패
        }
        
        @Override public void onAdClicked() { 
            // 클릭 
        }
        
        @Override public void onAdClosed(ADWHALE_POPUP_AD_CLOSE_REASON reason) { 
            // 닫힘 (사유 포함)
        }
    });

    // 3. 로드
    transitionPopupAd.loadAd();
}

@Override
protected void onResume() {
    if (transitionPopupAd != null)
        transitionPopupAd.resume(this); // 필수 호출
    super.onResume();
}

@Override
protected void onDestroy() {
    if (transitionPopupAd != null) {
        // 5. 폐기
        transitionPopupAd.destroy();
        transitionPopupAd = null;
    }
    super.onDestroy();
}
```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
@Composable
fun TransitionPopupAdSample() {

    val context = LocalContext.current
    val activity = context as? AppCompatActivity ?: return
    val lifecycleOwner = LocalLifecycleOwner.current

    // recomposition 방지
    val transitionPopupAd = remember {
        // 1. 인스턴스 생성
        AdWhaleMediationTransitionPopupAd("발급받은 placement uid 값")
    }

    DisposableEffect(lifecycleOwner) {

        // 2. 리스너 등록
        transitionPopupAd.setAdWhaleMediationExitPopupAdListener(
            object : AdWhaleMediationExitPopupAdListener {

                override fun onAdLoaded() {
                    // 로드 성공
                    // 4. 광고 표시
                    transitionPopupAd.showAd(
                        activity,
                        activity.supportFragmentManager
                    )
                }

                override fun onAdLoadFailed(statusCode: Int, message: String?) {
                    // 로드 실패
                }

                override fun onAdShowed() {
                    // 표시됨
                }

                override fun onAdShowFailed(statusCode: Int, message: String?) {
                    // 표시 실패
                }

                override fun onAdClicked() {
                    // 클릭
                }

                override fun onAdClosed(reason: ADWHALE_POPUP_AD_CLOSE_REASON?) {
                    // 닫힘
                }
            }
        )

        // 3. 로드
        transitionPopupAd.loadAd()

        // onResume 대체
        val observer = LifecycleEventObserver { _, event ->
            if (event == Lifecycle.Event.ON_RESUME) {
                transitionPopupAd.resume(activity)
            }
        }

        lifecycleOwner.lifecycle.addObserver(observer)

        onDispose {
            // observer 제거
            lifecycleOwner.lifecycle.removeObserver(observer)

            // 5. 폐기 (onDestroy 대체)
            transitionPopupAd.destroy()
        }
    }
}
```
{% endtab %}

{% tab title="Compose" %}
```kotlin
@Composable
fun ExitPopupAdSample() {

    val context = LocalContext.current
    val activity = context as Activity
    val fragmentManager = (activity as AppCompatActivity).supportFragmentManager

    var isLoaded by remember { mutableStateOf(false) }

    // recomposition 방지
    val exitPopupAd = remember {
        // 1. 인스턴스 생성 (placementUid)
        AdWhaleMediationExitPopupAd("발급받은 placement uid 값")
    }

    DisposableEffect(Unit) {

        // 2. 리스너 등록
        exitPopupAd.setAdWhaleMediationExitPopupAdListener(
            object : AdWhaleMediationExitPopupAdListener {

                override fun onAdLoaded() {
                    isLoaded = true
                }

                override fun onAdLoadFailed(statusCode: Int, message: String?) {
                    isLoaded = false
                }

                override fun onAdShowed() {
                    isLoaded = false
                }

                override fun onAdShowFailed(statusCode: Int, message: String?) {
                    isLoaded = false
                }

                override fun onAdClicked() {
                    isLoaded = false
                }

                override fun onAdClosed(reason: ADWHALE_POPUP_AD_CLOSE_REASON?) {
                    isLoaded = false
                }
            }
        )

        // 3. 로드
        exitPopupAd.loadAd()

        onDispose {
            // 5. 폐기
            exitPopupAd.destroy()
        }
    }

    // Compose 방식 Back 처리
    BackHandler {
        if (isLoaded) {
            // 4. 표시
            exitPopupAd.showAd(activity, fragmentManager)
        } else {
            activity.finish()
        }
    }
}
```
{% endtab %}
{% endtabs %}

#### 3. API 설명 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

{% tabs %}
{% tab title="Java" %}
**AdWhaleMediationTransitionPopupAd 클래스 API 설명**

```java
public AdWhaleMediationTransitionPopupAd(String placementUid)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```java
public void setAdWhaleMediationTransitionPopupAdListener(AdWhaleMediationTransitionPopupAdListener listener)
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationTransitionPopupAdListener</p></td><td>앱 전환 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```java
public void loadAd() // 미디에이션 앱 전환 광고로드
```

```java
public void showAd(Activity activity, FragmentManager fragmentManager) // 미디에이션 앱 전환 광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr><tr><td>androidx.fragment.app.FragmentManager</td><td>Android FragmentManager 클래스</td></tr></tbody></table>

```java
public void resume() // onResume() 시 호출
```

```java
public void destroy() // onDestroy() 시 호출 혹은 더 이상 광고를 요청하지 않고 싶을 때 호출
```

\
**AdWhaleMediationTransitionPopupAdListener 클래스 API 설명**

```java
public void onAdLoaded() // 미디에이션 앱 전환 광고요청 성공 시
```

```java
public void onAdLoadFailed(int statusCode, String message) // 미디에이션 앱 전환 광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```java
public void onAdShowed() // 미디에이션 앱 전환 광고표시 후
```

```java
public void onAdShowFailed(int statusCode, String message) // 미디에이션 앱 전환 광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고표시 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```java
public void onAdClosed(ADWHALE_POPUP_AD_CLOSE_REASON adwhaleExitPopupAdCloseReason) // 미디에이션 앱 전환 광고닫기. 닫힘 사유 전달
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>ADWHALE_POPUP_AD_CLOSE_REASON</p></td><td>닫힘 사유 (사유 종류: UNKNOWN, PRIMARY_BUTTON, SECONDARY_BUTTON, OUTSIDE_TOUCH, BACK_PRESS, TODAY_DISMISS, AMBIGUOUS_CANCEL)<br><strong>* 닫힘 사유 콜백 시 파라미터값은 광고 소스, 광고 네트워크 마다 조금씩 상이할 수 있습니다.</strong></td></tr></tbody></table>

```java
public void onAdClicked() // 미디에이션 앱 전환 광고클릭 시
```
{% endtab %}

{% tab title="Kotlin" %}
**AdWhaleMediationTransitionPopupAd 클래스 API 설명**

```kotlin
AdWhaleMediationTransitionPopupAd(placementUid : String)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```kotlin
fun setAdWhaleMediationTransitionPopupAdListener(listener : AdWhaleMediationTransitionPopupAdListener) : Unit
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationTransitionPopupAdListener</p></td><td>앱 전환 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```kotlin
fun loadAd() : Unit // 미디에이션 앱 전환 광고로드
```

```kotlin
fun showAd(activity : Activity, fragmentManager : FragmentManager) : Unit // 미디에이션 앱 전환 광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr><tr><td>androidx.fragment.app.FragmentManager</td><td>Android FragmentManager 클래스</td></tr></tbody></table>

```kotlin
fun resume() : Unit // onResume() 시 호출
```

```kotlin
fun destroy() : Unit // onDestroy() 시 호출 혹은 더 이상 광고를 요청하지 않고 싶을 때 호출
```

\
**AdWhaleMediationTransitionPopupAdListener 클래스 API 설명**

```kotlin
fun onAdLoaded() : Unit // 미디에이션 앱 전환 광고요청 성공 시
```

```kotlin
fun onAdLoadFailed(statusCode : Int, message : String) : Unit // 미디에이션 앱 전환 광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdShowed() : Unit // 미디에이션 앱 전환 광고표시 후
```

```kotlin
fun onAdShowFailed(statusCode : Unit, message : String) : Unit // 미디에이션 앱 전환 광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고표시 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdClosed(adwhaleExitPopupAdCloseReason : ADWHALE_POPUP_AD_CLOSE_REASON) // 미디에이션 앱 전환 광고닫기. 닫힘 사유 전달
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>ADWHALE_POPUP_AD_CLOSE_REASON</p></td><td>닫힘 사유 (사유 종류: UNKNOWN, PRIMARY_BUTTON, SECONDARY_BUTTON, OUTSIDE_TOUCH, BACK_PRESS, TODAY_DISMISS, AMBIGUOUS_CANCEL)<br><strong>* 닫힘 사유 콜백 시 파라미터값은 광고 소스, 광고 네트워크 마다 조금씩 상이할 수 있습니다.</strong></td></tr></tbody></table>

```kotlin
fun onAdClicked() : Unit // 미디에이션 앱 전환 광고클릭 시
```
{% endtab %}

{% tab title="Compose" %}
**AdWhaleMediationTransitionPopupAd 클래스 API 설명**

```kotlin
AdWhaleMediationTransitionPopupAd(placementUid : String)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```kotlin
fun setAdWhaleMediationTransitionPopupAdListener(listener : AdWhaleMediationTransitionPopupAdListener) : Unit
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationTransitionPopupAdListener</p></td><td>앱 전환 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```kotlin
fun loadAd() : Unit // 미디에이션 앱 전환 광고로드
```

```kotlin
fun showAd(activity : Activity, fragmentManager : FragmentManager) : Unit // 미디에이션 앱 전환 광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr><tr><td>androidx.fragment.app.FragmentManager</td><td>Android FragmentManager 클래스</td></tr></tbody></table>

```kotlin
fun resume() : Unit // onResume() 시 호출
```

```kotlin
fun destroy() : Unit // onDestroy() 시 호출 혹은 더 이상 광고를 요청하지 않고 싶을 때 호출
```

\
**AdWhaleMediationTransitionPopupAdListener 클래스 API 설명**

```kotlin
fun onAdLoaded() : Unit // 미디에이션 앱 전환 광고요청 성공 시
```

```kotlin
fun onAdLoadFailed(statusCode : Int, message : String) : Unit // 미디에이션 앱 전환 광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdShowed() : Unit // 미디에이션 앱 전환 광고표시 후
```

```kotlin
fun onAdShowFailed(statusCode : Unit, message : String) : Unit // 미디에이션 앱 전환 광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고표시 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdClosed(adwhaleExitPopupAdCloseReason : ADWHALE_POPUP_AD_CLOSE_REASON) // 미디에이션 앱 전환 광고닫기. 닫힘 사유 전달
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>ADWHALE_POPUP_AD_CLOSE_REASON</p></td><td>닫힘 사유 (사유 종류: UNKNOWN, PRIMARY_BUTTON, SECONDARY_BUTTON, OUTSIDE_TOUCH, BACK_PRESS, TODAY_DISMISS, AMBIGUOUS_CANCEL)<br><strong>* 닫힘 사유 콜백 시 파라미터값은 광고 소스, 광고 네트워크 마다 조금씩 상이할 수 있습니다.</strong></td></tr></tbody></table>

```kotlin
fun onAdClicked() : Unit // 미디에이션 앱 전환 광고클릭 시
```
{% endtab %}
{% endtabs %}

#### 4. 닫힘 사유 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

{% hint style="info" %}
&#x20;닫힘 사유 콜백 시 파라미터값은 광고 소스, 광고 네트워크 마다 조금씩 상이할 수 있습니다.
{% endhint %}

<table><thead><tr><th width="425.625">값</th><th>설명</th></tr></thead><tbody><tr><td><code>ADWHALE_POPUP_AD_CLOSE_REASON.UNKNOWN</code></td><td>알 수 없음</td></tr><tr><td><code>ADWHALE_POPUP_AD_CLOSE_REASON.PRIMARY_BUTTON</code></td><td>메인 버튼 터치</td></tr><tr><td><code>ADWHALE_POPUP_AD_CLOSE_REASON.SECONDARY_BUTTON</code></td><td>보조 버튼 터치</td></tr><tr><td><code>ADWHALE_POPUP_AD_CLOSE_REASON.OUTSIDE_TOUCH</code></td><td>영역 외 터치</td></tr><tr><td><code>ADWHALE_POPUP_AD_CLOSE_REASON.BACK_PRESS</code></td><td>백키 입력</td></tr><tr><td><code>ADWHALE_POPUP_AD_CLOSE_REASON.TODAY_DISMISS</code></td><td>당일 일회 노출 등으로 닫힘</td></tr><tr><td><code>ADWHALE_POPUP_AD_CLOSE_REASON.AMBIGUOUS_CANCEL</code></td><td>기타 취소</td></tr></tbody></table>

#### 5. 옵션 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

{% tabs %}
{% tab title="Java" %}
```java
exitPopupAd.setRegion("서울시 강남구"); // 지역 타게팅 전용 API(옵션)
exitPopupAd.setGcoder(37.5665, 126.9780); // 지역 타게팅 전용 API(옵션)
exitPopupAd.setPlacementName("app_transition_main"); // 레벨플레이 placement name 연동 전용 API (옵션)
```

{% hint style="info" %}
ADwhale 에서는 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
exitPopupAd.setRegion("서울시 강남구") // 지역 타게팅 전용 API(옵션)
exitPopupAd.setGcoder(37.5665, 126.9780) // 지역 타게팅 전용 API(옵션)
exitPopupAd.setPlacementName("app_transition_main") // 레벨플레이 placement name 연동 전용 API (옵션)
```

{% hint style="info" %}
ADwhale 에서는 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}
{% endtab %}

{% tab title="Compose" %}
```kotlin
exitPopupAd.setRegion("서울시 강남구") // 지역 타게팅 전용 API(옵션)
exitPopupAd.setGcoder(37.5665, 126.9780) // 지역 타게팅 전용 API(옵션)
exitPopupAd.setPlacementName("app_transition_main") // 레벨플레이 placement name 연동 전용 API (옵션)
```

{% hint style="info" %}
ADwhale 에서는 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}
{% endtab %}
{% endtabs %}

#### 6. 앱 전환 광고 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

다음은 안드로이드에서 앱 전환 광고를 구현하는 완전한 예시입니다.

{% tabs %}
{% tab title="Java" %}
```java
import android.os.Bundle;
import android.util.Log;
import android.view.KeyEvent;
import androidx.appcompat.app.AppCompatActivity;
import androidx.fragment.app.FragmentManager;
import net.adwhale.sdk.mediation.ads.ADWHALE_POPUP_AD_CLOSE_REASON;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationExitPopupAd;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationExitPopupAdListener;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener;
import net.adwhale.sdk.utils.AdWhaleLog;

public class MainActivity extends AppCompatActivity {

    private AdWhaleMediationExitPopupAd exitPopupAd;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None);

        AdWhaleMediationAds.init(this, new AdWhaleMediationOnInitCompleteListener() {
            @Override
            public void onInitComplete(int statusCode, String message) {
                Log.i("MainActivity", "onInitComplete(" + statusCode + ", " + message + ")");
                createAndLoadExitPopupAd();
            }
        });
    }

    private void createAndLoadExitPopupAd() {
        exitPopupAd = new AdWhaleMediationExitPopupAd("발급받은 placement uid 값");
        exitPopupAd.setCustomizeButtonText("취소", "종료");
        exitPopupAd.setCustomDescription("한 번 더 눌러 종료");
        exitPopupAd.setAdWhaleMediationExitPopupAdListener(new AdWhaleMediationExitPopupAdListener() {
            @Override
            public void onAdLoaded() {
                Log.i("MainActivity", "onAdLoaded()");
            }
            @Override
            public void onAdLoadFailed(int statusCode, String message) {
                Log.e("MainActivity", "onAdLoadFailed(" + statusCode + ", " + message + ")");
            }
            @Override
            public void onAdShowed() {
                Log.i("MainActivity", "onAdShowed()");
            }
            @Override
            public void onAdShowFailed(int statusCode, String message) {
                Log.e("MainActivity", "onAdShowFailed(" + statusCode + ", " + message + ")");
            }
            @Override
            public void onAdClicked() {
                Log.i("MainActivity", "onAdClicked()");
            }
            @Override
            public void onAdClosed(ADWHALE_POPUP_AD_CLOSE_REASON reason) {
                Log.i("MainActivity", "onAdClosed(" + reason.getCloseReasonTypeString() + ")");
            }
        });
        exitPopupAd.loadAd();
    }

    @Override
    protected void onResume() {
        super.onResume();
        if (exitPopupAd != null) exitPopupAd.resume(this);
    }

    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        if (keyCode == KeyEvent.KEYCODE_BACK) {
            if (exitPopupAd != null) {
                exitPopupAd.showAd(this, getSupportFragmentManager());
            }
            return true;
        }
        return super.onKeyDown(keyCode, event);
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (exitPopupAd != null) {
            exitPopupAd.destroy();
            exitPopupAd = null;
        }
    }
}
```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import net.adwhale.sdk.mediation.ads.ADWHALE_POPUP_AD_CLOSE_REASON
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds
import net.adwhale.sdk.mediation.ads.AdWhaleMediationTransitionPopupAd
import net.adwhale.sdk.mediation.ads.AdWhaleMediationTransitionPopupAdListener
import net.adwhale.sdk.utils.AdWhaleLog

class MainActivity : AppCompatActivity() {

    private var transitionPopupAd: AdWhaleMediationTransitionPopupAd? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)

        AdWhaleMediationAds.init(this) { statusCode, message ->
            Log.i("MainActivity", "onInitComplete($statusCode, $message)")
            loadTransitionPopupAd()
        }

        findViewById<View>(R.id.btn_next).setOnClickListener { showTransitionPopupAd() }
    }

    private fun loadTransitionPopupAd() {
        transitionPopupAd = AdWhaleMediationTransitionPopupAd("발급받은 placement uid 값").apply {
            setAdWhaleMediationTransitionPopupAdListener(object : AdWhaleMediationTransitionPopupAdListener {
                override fun onAdLoaded() {
                    Log.i("MainActivity", "onAdLoaded()")
                    showTransitionPopupAd()
                }
                override fun onAdLoadFailed(statusCode: Int, message: String) {
                    Log.e("MainActivity", "onAdLoadFailed($statusCode, $message)")
                }
                override fun onAdShowed() { Log.i("MainActivity", "onAdShowed()") }
                override fun onAdShowFailed(statusCode: Int, message: String) {
                    Log.e("MainActivity", "onAdShowFailed($statusCode, $message)")
                }
                override fun onAdClicked() { Log.i("MainActivity", "onAdClicked()") }
                override fun onAdClosed(reason: ADWHALE_POPUP_AD_CLOSE_REASON) {
                    Log.i("MainActivity", "onAdClosed(${reason.getCloseReasonTypeString()})")
                }
            })
            loadAd()
        }
    }

    private fun showTransitionPopupAd() {
        transitionPopupAd?.showAd(this, supportFragmentManager)
    }

    override fun onResume() {
        super.onResume()
        transitionPopupAd?.resume(this)
    }

    override fun onDestroy() {
        super.onDestroy()
        transitionPopupAd?.destroy()
        transitionPopupAd = null
    }
}
```
{% endtab %}

{% tab title="Compose" %}
```kotlin
import android.os.Bundle
import android.util.Log
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.padding
import androidx.compose.material3.Button
import androidx.compose.material3.Text
import androidx.compose.runtime.DisposableEffect
import androidx.compose.runtime.LaunchedEffect
import androidx.compose.runtime.getValue
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.remember
import androidx.compose.runtime.setValue
import androidx.compose.ui.Modifier
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.unit.dp
import net.adwhale.sdk.mediation.ads.ADWHALE_POPUP_AD_CLOSE_REASON
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener
import net.adwhale.sdk.mediation.ads.AdWhaleMediationTransitionPopupAd
import net.adwhale.sdk.mediation.ads.AdWhaleMediationTransitionPopupAdListener
import net.adwhale.sdk.utils.AdWhaleLog

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // 1. 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)

        setContent {
            TransitionPopupScreen(
                placementUid = "발급받은 placement uid 값",
                placementName = "transition_popup_main",
                region = "서울시 강남구",
                gcoderLat = 37.5665,
                gcoderLng = 126.9780
            )
        }
    }
}

@Composable
private fun TransitionPopupScreen(
    placementUid: String,
    placementName: String? = null,
    region: String? = null,
    gcoderLat: Double? = null,
    gcoderLng: Double? = null
) {
    // init 완료 여부
    var isInited by remember { mutableStateOf(false) }

    // 광고 인스턴스
    var transitionPopupAd by remember { mutableStateOf<AdWhaleMediationTransitionPopupAd?>(null) }

    // 2. SDK 초기화: Composable이 최초 구성될 때 한 번
    LaunchedEffect(Unit) {
        // 주의: init은 보통 Activity에서 1회만 하는 게 권장.
        AdWhaleMediationAds.init(
            LocalContext.current,
            object : AdWhaleMediationOnInitCompleteListener {
                override fun onInitComplete(statusCode: Int, message: String) {
                    Log.i("MainActivity", "onInitComplete($statusCode, $message)")
                    isInited = true
                }
            }
        )
    }

    val activity = LocalContext.current as? ComponentActivity

    LaunchedEffect(isInited) {
        if (!isInited || activity == null) return@LaunchedEffect

        // 3. 인스턴스 생성 (placementUid)
        val ad = AdWhaleMediationTransitionPopupAd(placementUid).apply {
            if (region != null) setRegion(region)                                         // 지역 타게팅 전용 API(옵션)
            if (gcoderLat != null && gcoderLng != null) setGcoder(gcoderLat, gcoderLng)   // 지역 타게팅 전용 API(옵션)
            if (placementName != null) setPlacementName(placementName)                    // 레벨플레이 placement name 연동 전용 API (옵션)

            // 4. 리스너 등록
            setAdWhaleMediationTransitionPopupAdListener(object : AdWhaleMediationTransitionPopupAdListener {
                override fun onAdLoaded() {
                    Log.i("MainActivity", "onAdLoaded()")
                }

                override fun onAdLoadFailed(statusCode: Int, message: String) {
                    Log.e("MainActivity", "onAdLoadFailed($statusCode, $message)")
                }

                override fun onAdShowed() {
                    Log.i("MainActivity", "onAdShowed()")
                }

                override fun onAdShowFailed(statusCode: Int, message: String) {
                    Log.e("MainActivity", "onAdShowFailed($statusCode, $message)")
                }

                override fun onAdClicked() {
                    Log.i("MainActivity", "onAdClicked()")
                }

                override fun onAdClosed(reason: ADWHALE_POPUP_AD_CLOSE_REASON) {
                    Log.i("MainActivity", "onAdClosed(${reason.getCloseReasonTypeString()})")
                }
            })
        }

        transitionPopupAd = ad

        // 5. 로드
        ad.loadAd()
    }

    // dispose에서 destroy
    DisposableEffect(Unit) {
        onDispose {
            // 7. 폐기
            transitionPopupAd?.destroy()
            transitionPopupAd = null
        }
    }

    // 간단 UI (표시 버튼 + 다시 로드 버튼)
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(20.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp)
    ) {
        Text(text = if (isInited) "SDK 초기화 완료" else "SDK 초기화 중...")

        Button(
            onClick = {
                activity?.let { act ->
                    transitionPopupAd?.showAd(act, act.supportFragmentManager)
                }
            },
            enabled = isInited && activity != null && transitionPopupAd != null
        ) {
            Text("TransitionPopupAd 표시")
        }

        Button(
            onClick = { transitionPopupAd?.loadAd() },
            enabled = isInited && transitionPopupAd != null
        ) {
            Text("다시 로드")
        }
    }
}
```
{% endtab %}
{% endtabs %}

#### **7. 주의사항**

**광고 로드 타이밍**

* 광고는 로드가 완료된 후에만 표시할 수 있습니다.
* `onAdLoaded()` 이후에만 `showAd(activity, fragmentManager)`를 호출하세요.

**광고 표시 조건**

* 화면 전환 직전, 메뉴 이동 직전 등 자연스러운 전환 시점에 노출하는 것을 권장합니다.
* 중복 표시: 이미 표시 중이거나 로드 중일 때 `showAd(activity)`는 무시될 수 있으므로, 콜백 상태를 활용해 제어하세요.
* **FragmentManager**: `showAd(Activity, FragmentManager)`에 전달하는 FragmentManager는 해당 Activity의 `getSupportFragmentManager()`(또는 `supportFragmentManager`)를 사용하세요.

**리스너 정리**

* 컴포넌트가 언마운트될 때 등록한 이벤트 리스너를 반드시 제거해야 합니다.

**리소스 해제**

* `onDestroy()`에서 반드시 `destroy()`를 호출하세요.

**에러 처리**

* `onAdFailedToLoad`와 `onAdShowFailed` 이벤트에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.

**테스트**

* 개발 환경에서는 테스트용 placement UID를 사용하세요.
* 실제 배포 전에 다양한 시나리오에서 테스트하세요.
