# 배너

{% hint style="info" %}
&#x20;배너 광고는 앱 화면의 특정 영역에 표시되는 직사각형 광고입니다. 사용자가 앱을 사용하는 동안 지속적으로 노출되어 높은 노출 빈도를 제공합니다. ADwhale Mediation 배너 광고는 Programmatic 방식과 XML 방식 2가지 형태의 구현과 사용이 가능합니다.&#x20;
{% endhint %}

#### **1. 주요특징**

* 다양한 사이즈 지원 (320x50, 320x100, 300x250, 250x250, ADAPTIVE\_ANCHOR)
* 자동 로드 및 새로고침 기능
* 간단한 컴포넌트 기반 구현
* Programmatic / XML 방식 모두 지원
* 이벤트 기반 콜백으로 광고 상태 추적

#### 2. 기본 구현 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleMediationAdView` 클래스를 사용하여 배너 광고를 로드하고 표시하는 기본적인 구현 방법입니다.

{% tabs %}
{% tab title="Java" %}
```java
private AdWhaleMediationAdView adView;

@Override
protected void onCreate(Bundle savedInstanceState) {
    // 1. 뷰 생성
    adView = new AdWhaleMediationAdView(this);
    
    // 2. 설정
    adView.setPlacementUid("발급받은 placement uid 값");
    adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER320x50);
    
    // 3. 리스너 등록
    adView.setAdWhaleMediationAdViewListener(new AdWhaleMediationAdViewListener() {
        @Override public void onAdLoaded() { /* 로드 성공 */ }
        @Override public void onAdLoadFailed(int statusCode, String message) { /* 로드 실패 */ }
        @Override public void onAdClicked() { /* 클릭 */ }
    });
    
    // 4. 레이아웃에 추가 후, SDK 초기화 완료 콜백에서 loadAd() 호출
    root.addView(adView, params);
    adView.loadAd();
}

// 5. Activity 라이프사이클에서 resume / pause / destroy 호출
@Override
protected void onResume() {
    super.onResume();
    if (adView != null) adView.resume();
}
@Override
protected void onPause() {
    super.onPause();
    if (adView != null) adView.pause();
}
@Override
protected void onDestroy() {
    if (adView != null) {
        adView.destroy();
        adView = null;
    }
    super.onDestroy();
}
```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
private var adView: AdWhaleMediationAdView? = null

override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    // 예시: root는 실제 레이아웃의 컨테이너로 교체
    val root: ViewGroup = findViewById(R.id.root)
    val params = ViewGroup.LayoutParams(
        ViewGroup.LayoutParams.WRAP_CONTENT,
        ViewGroup.LayoutParams.WRAP_CONTENT
    )

    // 1. 뷰 생성
    adView = AdWhaleMediationAdView(this)

    // 2. 설정
    adView?.setPlacementUid("발급받은 placement uid 값")
    adView?.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER320x50)

    // 3. 리스너 등록
    adView?.setAdWhaleMediationAdViewListener(object : AdWhaleMediationAdViewListener {
        override fun onAdLoaded() { /* 로드 성공 */ }
        override fun onAdLoadFailed(statusCode: Int, message: String) { /* 로드 실패 */ }
        override fun onAdClicked() { /* 클릭 */ }
    })

    // 4. 레이아웃에 추가 후, SDK 초기화 완료 콜백에서 loadAd() 호출
    root.addView(adView, params)
    adView?.loadAd()
}

// 5. Activity 라이프사이클에서 resume / pause / destroy 호출
override fun onResume() {
    super.onResume()
    adView?.resume()
}

override fun onPause() {
    super.onPause()
    adView?.pause()
}

override fun onDestroy() {
    adView?.destroy()
    adView = null
    super.onDestroy()
}
```
{% endtab %}

{% tab title="Compose" %}
```kotlin
@Composable
fun AdWhaleBannerAd(
    modifier: Modifier = Modifier,
    placementUid: String = "발급받은 placement uid 값",
    adSize: ADWHALE_AD_SIZE = ADWHALE_AD_SIZE.BANNER320x50,
) {
    val context = LocalContext.current
    val lifecycleOwner = LocalLifecycleOwner.current

    // (Compose) adView 인스턴스를 remember로 유지
    val adView = remember {
        // 1. 뷰 생성
        AdWhaleMediationAdView(context).apply {
            // 2. 설정
            setPlacementUid(placementUid)
            setAdwhaleAdSize(adSize)

            // 3. 리스너 등록
            setAdWhaleMediationAdViewListener(object : AdWhaleMediationAdViewListener {
                override fun onAdLoaded() { /* 로드 성공 */ }
                override fun onAdLoadFailed(statusCode: Int, message: String) { /* 로드 실패 */ }
                override fun onAdClicked() { /* 클릭 */ }
            })
        }
    }

    // 4. 레이아웃에 추가 후, SDK 초기화 완료 콜백에서 loadAd() 호출
    // Compose에서는 AndroidView에 붙는 시점에 로드를 걸어도 되고,
    // "SDK 초기화 완료" 상태를 외부 state로 받아서 true일 때만 호출해도 됨.
    var didLoad by remember { mutableStateOf(false) }
    LaunchedEffect(adView) {
        if (!didLoad) {
            adView.loadAd()
            didLoad = true
        }
    }

    // 5. Activity 라이프사이클에서 resume / pause / destroy 호출
    // Compose에서는 Lifecycle을 구독해서 내부적으로 처리 가능
    DisposableEffect(lifecycleOwner, adView) {
        val observer = LifecycleEventObserver { _, event ->
            when (event) {
                Lifecycle.Event.ON_RESUME -> adView.resume()
                Lifecycle.Event.ON_PAUSE -> adView.pause()
                Lifecycle.Event.ON_DESTROY -> adView.destroy()
                else -> Unit
            }
        }
        lifecycleOwner.lifecycle.addObserver(observer)

        onDispose {
            lifecycleOwner.lifecycle.removeObserver(observer)
            // 만약 화면 교체 등으로 Composable이 먼저 dispose되는 케이스까지 커버하려면 여기서도 안전하게 destroy
            // (중복 destroy가 안전한지 보장되지 않으면, SDK 정책에 맞게 한쪽만 남겨도 됨)
            adView.destroy()
        }
    }

    AndroidView(
        modifier = modifier,
        factory = { adView }
    )
}

```
{% endtab %}
{% endtabs %}

#### 3. API 설명 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

{% tabs %}
{% tab title="Java" %}
**AdWhaleMediationAdView 클래스 API 설명**

```java
public AdWhaleMediationAdView(Context context)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.content.Context</td><td>Android Main Activity 클래스</td></tr></tbody></table>

```java
public void loadAd() // 미디에이션 배너광고로드
```

<pre class="language-java"><code class="lang-java"><strong>public void setPlacementUid(String placementUid) // 지면 등록
</strong></code></pre>

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```java
public void setAdwhaleAdSize(ADWHALE_AD_SIZE adWhaleAdSize) // 미디에이션 배너사이즈 설정
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.ADWHALE_AD_SIZE</td><td>배너 광고 사이즈(사이즈 종류: BANNER320x50, BANNER320x100, BANNER300x250, BANNER250x250, ADAPTIVE_ANCHOR) </td></tr></tbody></table>

```java
public void setAdWhaleMediationAdViewListener(AdWhaleMediationAdViewListener listener) // 리스너 등록
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationAdViewListener</p></td><td>배너 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```java
public void setAdaptiveAnchorWidth(int width) // 디바이스 width 입력. ADAPTIVE_ANCHOR 적응형 배너 적용시 사용.
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td>디바이스 width 입력<br>0을 입력할 경우 디바이스 전체 가로 길이 적용됨</td></tr></tbody></table>

```java
public void resume() // resume 콜백 시 호출필요
```

```java
public void pause() // pause 콜백 시 호출필요
```

```java
public void destroy() // onDestroy 콜백 시 호출필요
```



**AdWhaleMediationAdViewListener 클래스 API 설명**

```java
public void onAdLoaded() // 배너 광고요청 성공 시
```

```java
public void onAdLoadFailed(int statusCode, String message) // 미디에이션 배너광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>초기화 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```java
public void onAdClicked() // 배너 클릭 시
```
{% endtab %}

{% tab title="Kotlin" %}
**AdWhaleMediationAdView 클래스 API 설명**

```kotlin
AdWhaleMediationAdView(context : Context)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.content.Context</td><td>Android Main Activity 클래스</td></tr></tbody></table>

```kotlin
fun loadAd() : Unit // 미디에이션 배너광고 로드
```

<pre class="language-kotlin"><code class="lang-kotlin"><strong>fun setPlacementUid(placementUid : String) : Unit // 지면 등록
</strong></code></pre>

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```kotlin
fun setAdwhaleAdSize(adWhaleAdSize : ADWHALE_AD_SIZE) : Unit // 미디에이션 배너사이즈 설정
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.ADWHALE_AD_SIZE</td><td>배너 광고 사이즈(사이즈 종류: BANNER320x50, BANNER320x100, BANNER300x250, BANNER250x250, ADAPTIVE_ANCHOR) </td></tr></tbody></table>

```kotlin
fun setAdWhaleMediationAdViewListener(listener : Int) : Unit // 리스너 등
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>배너 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```kotlin
fun setAdaptiveAnchorWidth(width : Int) : Unit // 디바이스 width 입력. ADAPTIVE_ANCHOR 적응형 배너 적용시 사용.
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>디바이스 width 입력<br>0을 입력할 경우 디바이스 전체 가로 길이 적용됨</td></tr></tbody></table>

```kotlin
fun resume() : Unit // resume 콜백 시 호출필요
```

```kotlin
fun pause() : Unit // pause 콜백 시 호출필요
```

```kotlin
fun destroy() : Unit // destroy 콜백 시 호출필요
```



**AdWhaleMediationAdViewListener 클래스 API 설명**

```kotlin
fun onAdLoaded() : Unit // 배너 광고요청 성공 시
```

```kotlin
fun onAdLoadFailed(statusCode : Int, message : String) : Unit // 미디에이션 배너광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int </td><td><p>초기화 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdClicked() : Unit // 배너 클릭 시
```
{% endtab %}

{% tab title="Compose" %}
**AdWhaleMediationAdView 클래스 API 설명**

```kotlin
AdWhaleMediationAdView(context : Context)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.content.Context</td><td>Android Main Activity 클래스</td></tr></tbody></table>

```kotlin
fun loadAd() : Unit // 미디에이션 배너광고 로드
```

<pre class="language-kotlin"><code class="lang-kotlin"><strong>fun setPlacementUid(placementUid : String) : Unit // 지면 등록
</strong></code></pre>

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```kotlin
fun setAdwhaleAdSize(adWhaleAdSize : ADWHALE_AD_SIZE) : Unit // 미디에이션 배너사이즈 설정
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.ADWHALE_AD_SIZE</td><td>배너 광고 사이즈(사이즈 종류: BANNER320x50, BANNER320x100, BANNER300x250, BANNER250x250, ADAPTIVE_ANCHOR) </td></tr></tbody></table>

```kotlin
fun setAdWhaleMediationAdViewListener(listener : Int) : Unit // 리스너 등
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>배너 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```kotlin
fun setAdaptiveAnchorWidth(width : Int) : Unit // 디바이스 width 입력. ADAPTIVE_ANCHOR 적응형 배너 적용시 사용.
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>디바이스 width 입력<br>0을 입력할 경우 디바이스 전체 가로 길이 적용됨</td></tr></tbody></table>

```kotlin
fun resume() : Unit // resume 콜백 시 호출필요
```

```kotlin
fun pause() : Unit // pause 콜백 시 호출필요
```

```kotlin
fun destroy() : Unit // destroy 콜백 시 호출필요
```



**AdWhaleMediationAdViewListener 클래스 API 설명**

```kotlin
fun onAdLoaded() : Unit // 배너 광고요청 성공 시
```

```kotlin
fun onAdLoadFailed(statusCode : Int, message : String) : Unit // 미디에이션 배너광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int </td><td><p>초기화 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdClicked() : Unit // 배너 클릭 시
```
{% endtab %}
{% endtabs %}

#### 4. 배너 사이즈

배너 광고는 다음 사이즈를 지원합니다:

<table><thead><tr><th width="181.7578125">사이즈</th><th width="283.515625">값</th><th width="146.29296875">사이즈 별 높이</th><th>설명</th></tr></thead><tbody><tr><td>320x50</td><td><code>ADWHALE_AD_SIZE.BANNER320x50</code></td><td>50</td><td>표준 배너 (Banner)</td></tr><tr><td>320x100</td><td><code>ADWHALE_AD_SIZE.BANNER320x100</code></td><td>100</td><td>큰 배너 (Large Banner)</td></tr><tr><td>300x250</td><td><code>ADWHALE_AD_SIZE.BANNER300x250</code></td><td>250</td><td>중간 직사각형 (Medium Rectangle)</td></tr><tr><td>250x250</td><td><code>ADWHALE_AD_SIZE.BANNER250x250</code></td><td>250</td><td>정사각형 (Square)</td></tr><tr><td>ADAPTIVE_ANCHOR</td><td><code>ADWHALE_AD_SIZE.ADAPTIVE_ANCHOR</code></td><td>60(대략적인 높이)</td><td>적응형 앵커 배너</td></tr></tbody></table>

**사용예시**

{% tabs %}
{% tab title="Java" %}
```java
// 320x50 (표준 배너) 사용 예:
adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER320x50);

// 320x100 (큰 배너) 사용 예:
adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER320x100);

// 300x250 (중간 직사각형) 사용 예:
adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER300x250);

// 250x250 (정사각형) 사용 예:
adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER250x250);

// ADAPTIVE_ANCHOR (적응형 배너 (너비 지정)) 사용 예:
adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.ADAPTIVE_ANCHOR);
adView.setAdaptiveAnchorWidth(360); // 디바이스 너비 값 예시

// ADAPTIVE_ANCHOR (적응형 배너 (디바이스 전체 너비)) 사용 예:
adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.ADAPTIVE_ANCHOR);
adView.setAdaptiveAnchorWidth(0); // 0을 주거나, 아예 호출하지 않으면 디바이스 전체 너비를 자동으로 감지
```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
// 320x50 (표준 배너) 사용 예:
adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER320x50)

// 320x100 (큰 배너) 사용 예:
adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER320x100)

// 300x250 (중간 직사각형) 사용 예:
adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER300x250)

// 250x250 (정사각형) 사용 예:
adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER250x250)

// ADAPTIVE_ANCHOR (적응형 배너 (너비 지정)) 사용 예:
adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.ADAPTIVE_ANCHOR)
adView.setAdaptiveAnchorWidth(360) // 디바이스 너비 값 예시

// ADAPTIVE_ANCHOR (적응형 배너 (디바이스 전체 너비)) 사용 예:
adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.ADAPTIVE_ANCHOR)
adView.setAdaptiveAnchorWidth(0) // 0을 주거나, 아예 호출하지 않으면 디바이스 전체 너비를 자동으로 감지
```
{% endtab %}

{% tab title="Compose" %}
```kotlin
// 320x50 (표준 배너) 사용 예:
adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER320x50)

// 320x100 (큰 배너) 사용 예:
adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER320x100)

// 300x250 (중간 직사각형) 사용 예:
adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER300x250)

// 250x250 (정사각형) 사용 예:
adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER250x250)

// ADAPTIVE_ANCHOR (적응형 배너 (너비 지정)) 사용 예:
adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.ADAPTIVE_ANCHOR)
adView.setAdaptiveAnchorWidth(360) // 디바이스 너비 값 예시

// ADAPTIVE_ANCHOR (적응형 배너 (디바이스 전체 너비)) 사용 예:
adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.ADAPTIVE_ANCHOR)
adView.setAdaptiveAnchorWidth(0) // 0을 주거나, 아예 호출하지 않으면 디바이스 전체 너비를 자동으로 감지
```
{% endtab %}
{% endtabs %}

#### 5. 옵션 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

{% tabs %}
{% tab title="Java" %}
```java
adView.setRegion("서울시 강남구"); // 지역 타게팅 전용 API(옵션)
adView.setGcoder(37.5665, 126.9780); // 지역 타게팅 전용 API(옵션)
adView.setPlacementName("app_open_main"); // 레벨플레이 placement name 연동 전용 API (옵션)
```

{% hint style="info" %}
ADwhale 에서는 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
adView.setRegion("서울시 강남구") // 지역 타게팅 전용 API(옵션)
adView.setGcoder(37.5665, 126.9780) // 지역 타게팅 전용 API(옵션)
adView.setPlacementName("app_open_main") // 레벨플레이 placement name 연동 전용 API (옵션)
```

{% hint style="info" %}
ADwhale 에서는 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}
{% endtab %}

{% tab title="Compose" %}
```kotlin
adView.setRegion("서울시 강남구") // 지역 타게팅 전용 API(옵션)
adView.setGcoder(37.5665, 126.9780) // 지역 타게팅 전용 API(옵션)
adView.setPlacementName("app_open_main") // 레벨플레이 placement name 연동 전용 API (옵션)
```

{% hint style="info" %}
ADwhale 에서는 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}
{% endtab %}
{% endtabs %}

#### 6. 배너 광고 Programmatic 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

{% tabs %}
{% tab title="Java" %}
```java
import androidx.appcompat.app.AppCompatActivity;
import androidx.constraintlayout.widget.ConstraintLayout;

import android.os.Bundle;
import android.os.Handler;
import android.os.Looper;
import android.util.Log;
import android.widget.LinearLayout;
import android.widget.RelativeLayout;

import net.adwhale.sdk.mediation.ads.ADWHALE_AD_SIZE;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdView;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdViewListener;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener;
import net.adwhale.sdk.utils.AdWhaleLog;

public class MainActivity extends AppCompatActivity {

    private ConstraintLayout root;

    private AdWhaleMediationAdView adWhaleMediationAdView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        root = (ConstraintLayout) findViewById(R.id.root);      
    
        // 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None);
            
        // 배너광고 뷰 생성
        adWhaleMediationAdView = new AdWhaleMediationAdView(this);
        adWhaleMediationAdView.setPlacementUid("발급받은 placement uid 값");
        adWhaleMediationAdView.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER320x50);
        // 적응형 배너 뷰를 적용할 경우 
        // adWhaleMediationAdView.setAdwhaleAdSize(ADWHALE_AD_SIZE.ADAPTIVE_ANCHOR);
        // adWhaleMediationAdView.setAdaptiveAnchorWidth(360); // 디바이스 width 입력. 0을 입력할 경우 디바이스 전체 가로 길이 적용됨.
        
        
        // 배너광고 뷰 콜백 리스너 등록
        adWhaleMediationAdView.setAdWhaleMediationAdViewListener(new AdWhaleMediationAdViewListener() {
            @Override
            public void onAdLoaded() {
                Log.i(MainActivity.class.getSimpleName(), ".onAdLoaded();");
            }

            @Override
            public void onAdLoadFailed(int statusCode, String message) {
                Log.i(MainActivity.class.getSimpleName(), ".onAdLoadFailed(" + statusCode + ", " + message + ");");
            }
            
            @Override
            public void onAdClicked() {
                Log.i(MainActivity.class.getSimpleName(), ".onAdClicked();");
            }
        });
        
        // LayoutParams 적용 및 root view에 add
        RelativeLayout.LayoutParams params = new RelativeLayout.LayoutParams(
                LinearLayout.LayoutParams.WRAP_CONTENT, LinearLayout.LayoutParams.WRAP_CONTENT);
        params.addRule(RelativeLayout.ALIGN_PARENT_BOTTOM);
        params.addRule(RelativeLayout.CENTER_HORIZONTAL);
        root.addView(adWhaleMediationAdView, params);
                
        // 초기화 코드
        AdWhaleMediationAds.init(this, new AdWhaleMediationOnInitCompleteListener() {
            @Override
            public void onInitComplete(int statusCode, String message) {
                Log.i(MainActivity.class.getSimpleName(), ".onInitComplete(" + statusCode + ", " + message + ");");
                if(adWhaleMediationAdView != null) {
                    // 배너광고 뷰 로드
                    new Handler(Looper.getMainLooper()).postDelayed(() -> {
                        adWhaleMediationAdView.loadAd();
                    },0);                    
                }
            }
        });  
    }

    // 라이프사이클 onResume 콜백 시 반드시 onResume 호출 필요(미호출 시 불이익 발생)
    @Override
    protected void onResume() {
        super.onResume();
        if(adWhaleMediationAdView != null) {
            adWhaleMediationAdView.resume();        
        }
    }


    // 라이프사이클 onPause 콜백 시 반드시 onPause 호출 필요(미호출 시 불이익 발생)
    @Override
    protected void onPause() {
        super.onPause();
        if(adWhaleMediationAdView != null) {
            adWhaleMediationAdView.pause();
        }    
    }

    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy 호출 필요
    @Override
    protected void onDestroy() {
        super.onDestroy();
        if(adWhaleMediationAdView != null) {
            adWhaleMediationAdView.destroy();        
        }
    }
}
```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
import android.os.Bundle
import android.util.Log
import android.view.View
import android.widget.Button
import android.widget.EditText
import android.widget.LinearLayout
import android.widget.RadioGroup
import android.widget.RelativeLayout
import androidx.appcompat.app.AppCompatActivity
import androidx.constraintlayout.widget.ConstraintLayout
import net.adwhale.sdk.mediation.ads.ADWHALE_AD_SIZE
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdView
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdViewListener
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds

class MainActivity : AppCompatActivity() {

    private lateinit var root: ConstraintLayout
    private lateinit var adWhaleMediationAdView: AdWhaleMediationAdView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        root = findViewById<View>(R.id.root) as ConstraintLayout
               
        // 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)
         
        // 배너광고 뷰 생성
        adWhaleMediationAdView = AdWhaleMediationAdView(this)
        adWhaleMediationAdView.setPlacementUid("발급받은 placement uid 값")
        adWhaleMediationAdView.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER320x50)
        // 적응형 배너 뷰를 적용할 경우 
        // adWhaleMediationAdView.setAdwhaleAdSize(ADWHALE_AD_SIZE.ADAPTIVE_ANCHOR)
        // adWhaleMediationAdView.setAdaptiveAnchorWidth(360) // 디바이스 width 입력. 0을 입력할 경우 디바이스 전체 가로 길이 적용됨.
        
        // 배너광고 뷰 콜백 리스너 등록        
        adWhaleMediationAdView!!.adWhaleMediationAdViewListener =
            object : AdWhaleMediationAdViewListener {
                override fun onAdLoaded() {
                    Log.i(MainActivity::class.simpleName, ".onAdLoaded()")
                }

                override fun onAdLoadFailed(statusCode: Int, message: String) {
                    Log.i(MainActivity::class.simpleName, ".onAdLoadFailed($statusCode, $message)")
                }
                
                override fun onAdClicked() {
                    Log.i(MainActivity::class.simpleName, ".onAdClicked()")
                }
            }        

        // LayoutParams 적용 및 root view에 add        
        val params = RelativeLayout.LayoutParams(
            LinearLayout.LayoutParams.WRAP_CONTENT, LinearLayout.LayoutParams.WRAP_CONTENT
        )
        params.addRule(RelativeLayout.ALIGN_PARENT_BOTTOM)
        params.addRule(RelativeLayout.CENTER_HORIZONTAL)
        root!!.addView(adWhaleMediationAdView, params)    
        
        // 초기화 코드        
        AdWhaleMediationAds.init(this) { statusCode, message ->
            Log.i(MainActivity::class.simpleName, ".onInitComplete($statusCode, $message)")
            // 배너 뷰 로드
            Handler(Looper.getMainLooper()).postDelayed({
                adWhaleMediationAdView!!.loadAd()
            }, 0)         
        }        
    }

    // 라이프사이클 onResume 콜백 시 반드시 onResume 호출 필요(미호출 시 불이익 발생)
    override fun onResume() {
        super.onResume()
        adWhaleMediationAdView!!.resume()
    }
    
    // 라이프사이클 onPause 콜백 시 반드시 onPause 호출 필요(미호출 시 불이익 발생)
    override fun onPause() {
        super.onPause()
        adWhaleMediationAdView!!.pause()
    }
    
    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy 호출 필요
    override fun onDestroy() {
        super.onDestroy()
        adWhaleMediationAdView!!.destroy()
    }
}
```
{% endtab %}

{% tab title="Compose" %}
```kotlin
import android.os.Bundle
import android.os.Handler
import android.os.Looper
import android.util.Log
import android.widget.LinearLayout
import android.widget.RelativeLayout
import android.content.Context
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.viewinterop.AndroidView
import net.adwhale.sdk.mediation.ads.ADWHALE_AD_SIZE
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdView
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdViewListener
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener
import net.adwhale.sdk.utils.AdWhaleLog

class MainActivity : ComponentActivity() {

    private var adWhaleMediationAdView: AdWhaleMediationAdView? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)

        // 먼저 배너 View 생성 (Compose에서 재사용)
        adWhaleMediationAdView = createBannerView(this)

        // AdWhale 초기화
        AdWhaleMediationAds.init(this, object : AdWhaleMediationOnInitCompleteListener {
            override fun onInitComplete(statusCode: Int, message: String?) {
                Log.i(MainActivity::class.java.simpleName,
                    ".onInitComplete($statusCode, $message);")

                // 초기화 완료 후 배너 로드
                if (adWhaleMediationAdView != null) {
                    Handler(Looper.getMainLooper()).postDelayed({
                        adWhaleMediationAdView?.loadAd()
                    }, 0)
                }
            }
        })

        setContent {
            Box(
                modifier = Modifier.fillMaxSize()
            ) {
                // 화면 하단 중앙에 배너 배치
                AndroidView(
                    modifier = Modifier
                        .align(Alignment.BottomCenter),
                    factory = { context ->
                        // 이미 onCreate에서 만든 뷰 재사용
                        adWhaleMediationAdView ?: createBannerView(context).also {
                            adWhaleMediationAdView = it
                        }
                    }
                )
            }
        }
    }

    private fun createBannerView(context: Context): AdWhaleMediationAdView {
        return AdWhaleMediationAdView(context).apply {
            // placement uid 설정
            setPlacementUid("발급받은 placement uid 값")

            // 고정 배너 사이즈 320x50
            setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER320x50)

            // 적응형 배너를 쓸 경우:
            // setAdwhaleAdSize(ADWHALE_AD_SIZE.ADAPTIVE_ANCHOR)
            // setAdaptiveAnchorWidth(360) // 디바이스 width. 0이면 전체 가로 길이

            setAdWhaleMediationAdViewListener(object : AdWhaleMediationAdViewListener {
                override fun onAdLoaded() {
                    Log.i(MainActivity::class.java.simpleName, ".onAdLoaded();")
                }

                override fun onAdLoadFailed(statusCode: Int, message: String?) {
                    Log.i(
                        MainActivity::class.java.simpleName,
                        ".onAdLoadFailed($statusCode, $message);"
                    )
                }

                override fun onAdClicked() {
                    Log.i(MainActivity::class.java.simpleName, ".onAdClicked();")
                }
            })

            val params = RelativeLayout.LayoutParams(
                LinearLayout.LayoutParams.WRAP_CONTENT,
                LinearLayout.LayoutParams.WRAP_CONTENT
            ).apply {
                addRule(RelativeLayout.ALIGN_PARENT_BOTTOM)
                addRule(RelativeLayout.CENTER_HORIZONTAL)
            }
            layoutParams = params
        }
    }

    // 라이프사이클 onResume 콜백 시 반드시 onResume 호출 필요
    override fun onResume() {
        super.onResume()
        adWhaleMediationAdView?.resume()
    }

    // 라이프사이클 onPause 콜백 시 반드시 onPause 호출 필요
    override fun onPause() {
        super.onPause()
        adWhaleMediationAdView?.pause()
    }

    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy 호출 필요
    override fun onDestroy() {
        super.onDestroy()
        adWhaleMediationAdView?.destroy()
        adWhaleMediationAdView = null
    }
}
```
{% endtab %}
{% endtabs %}

#### 7. 배너 광고  XML 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

{% tabs %}
{% tab title="Java + XML" %}
XML 코드

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    tools:context=".MainActivity">

    <net.adwhale.sdk.mediation.ads.AdWhaleMediationAdView
        android:id="@+id/adWhaleMediationAdView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:placementUid="발급받은 placement uid 값"
        app:bannerSize="BANNER320x50"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

Java 코드

```java
import androidx.appcompat.app.AppCompatActivity;

import android.os.Bundle;
import android.os.Handler;
import android.os.Looper;
import android.util.Log;

import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdView;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdViewListener;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener;
import net.adwhale.sdk.utils.AdWhaleLog;

public class MainActivity extends AppCompatActivity {

    private AdWhaleMediationAdView adWhaleMediationAdView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None);
        
        // 배너 뷰 바인딩
        adWhaleMediationAdView = findViewById(R.id.adWhaleMediationAdView);
        
        // 배너 뷰 콜백 리스너 등록
        adWhaleMediationAdView.setAdWhaleMediationAdViewListener(new AdWhaleMediationAdViewListener() {
            @Override
            public void onAdLoaded() {
                Log.i(MainActivity.class.getSimpleName(), ".onAdLoaded();");
            }

            @Override
            public void onAdLoadFailed(int statusCode, String message) {
                Log.i(MainActivity.class.getSimpleName(), ".onAdLoadFailed(" + statusCode + ", " + message + ");");
            }
            
            @Override
            public void onAdClicked() {
                Log.i(MainActivity.class.getSimpleName(), ".onAdClicked();");
            }
        });
        
        // 초기화 코드
        AdWhaleMediationAds.init(this, new AdWhaleMediationOnInitCompleteListener() {
            @Override
            public void onInitComplete(int statusCode, String message) {
                Log.i(MainActivity.class.getSimpleName(), ".onInitComplete(" + statusCode + ", " + message + ");");
                if(adWhaleMediationAdView != null) {
                    // 배너 뷰 로드
                    new Handler(Looper.getMainLooper()).postDelayed(() -> {
                        adWhaleMediationAdView.loadAd();
                    },0);  
                }
            }
        });        
    }

    // 라이프사이클 onResume 콜백 시 반드시 onResume 호출 필요(미호출 시 불이익 발생)
    @Override
    protected void onResume() {
        super.onResume();
        if(adWhaleMediationAdView != null) {
            adWhaleMediationAdView.resume();        
        }
    }


    // 라이프사이클 onPause 콜백 시 반드시 onPause 호출 필요(미호출 시 불이익 발생)
    @Override
    protected void onPause() {
        super.onPause();
        if(adWhaleMediationAdView != null) {
            adWhaleMediationAdView.pause();
        }    
    }

    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy 호출 필요
    @Override
    protected void onDestroy() {
        super.onDestroy();
        if(adWhaleMediationAdView != null) {
            adWhaleMediationAdView.destroy();        
        }
    }
}
```
{% endtab %}

{% tab title="Kotlin + XML" %}
XML 코드

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    tools:context=".MainActivity">

    <net.adwhale.sdk.mediation.ads.AdWhaleMediationAdView
        android:id="@+id/adWhaleMediationAdView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:placementUid="발급받은 placement uid 값"
        app:bannerSize="BANNER320x50"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

Kotlin 코드

```kotlin
import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdView
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdViewListener
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds

class MainActivity : AppCompatActivity() {

    private lateinit var adWhaleMediationAdView: AdWhaleMediationAdView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 배너 뷰 바인딩
        adWhaleMediationAdView = findViewById(R.id.adWhaleMediationAdView)
        
        // 배너 뷰 콜백 리스너 등록        
        adWhaleMediationAdView!!.adWhaleMediationAdViewListener =
            object : AdWhaleMediationAdViewListener {
                override fun onAdLoaded() {
                    Log.i(MainActivity::class.simpleName, ".onAdLoaded()")
                }

                override fun onAdLoadFailed(statusCode: Int, message: String) {
                    Log.i(MainActivity::class.simpleName, ".onAdLoadFailed($statusCode, $message)")
                }
                
                override fun onAdClicked() {
                    Log.i(MainActivity::class.simpleName, ".onAdClicked()")
                }
            }

        // 초기화 코드            
        AdWhaleMediationAds.init(this) { statusCode, message ->
            Log.i(MainActivity::class.simpleName, ".onInitComplete($statusCode, $message)")
            // 배너 뷰 로드
            Handler(Looper.getMainLooper()).postDelayed({
                adWhaleMediationAdView!!.loadAd()
            }, 0)  
        }            
    }

    // 라이프사이클 onResume 콜백 시 반드시 onResume 호출 필요(미호출 시 불이익 발생)
    override fun onResume() {
        super.onResume()
        adWhaleMediationAdView!!.resume()
    }

    // 라이프사이클 onPause 콜백 시 반드시 onPause 호출 필요(미호출 시 불이익 발생)
    override fun onPause() {
        super.onPause()
        adWhaleMediationAdView!!.pause()
    }

    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy 호출 필요(미호출 시 불이익 발생)
    override fun onDestroy() {
        super.onDestroy()
        adWhaleMediationAdView!!.destroy()
    }
}
```
{% endtab %}
{% endtabs %}

#### **8. 주의사항**

**광고 로드 타이밍**

* `loadAd()`는 SDK 초기화 완료(`onInitComplete`) 이후에 호출하는 것을 권장합니다.

**라이프사이클**

* `onResume` / `onPause` / `onDestroy`에서 각각 `resume()` / `pause()` / `destroy()` 호출이 필요합니다. 미호출 시 동작에 불이익이 있을 수 있습니다.

**재생성**

* 배너 사이즈나 placement UID를 바꿀 때는 뷰를 `destroy()`한 뒤 새로 생성한 후 다시 설정·로드하세요.

**에러 처리**

* `onAdLoadFailed` 이벤트에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.

**성능 최적화**

* 배너 광고는 자동으로 새로고침되므로, 불필요한 리렌더링을 방지하세요.

**테스트**

* 개발 환경에서는 테스트용 placement UID를 사용하세요.
