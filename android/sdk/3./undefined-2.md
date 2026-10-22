# 프리로드 배너

{% hint style="info" %}
&#x20;프리로드 배너는 광고를 **미리 로드해 두었다가 원하는 시점에 `show()`로 노출**하는 배너 광고입니다. 화면 진입 전에 광고를 준비해 두면, 화면이 열리는 즉시 공백 없이 광고를 보여줄 수 있습니다.

일반 배너(`AdWhaleMediationAdView`)는 로드가 끝나면 바로 노출되지만, 프리로드 배너(`AdWhaleMediationAdBannerView`)는 **`show()`를 호출해야 노출**됩니다. Programmatic 방식과 XML 방식 두 가지로 구현할 수 있습니다.
{% endhint %}

#### **1. 주요특징**

* 로드와 노출 분리: `loadAd()`로 미리 받아두고 `show()`로 원하는 시점에 노출
* 다양한 사이즈 지원 (320x50, 320x100, 300x250, 250x250, ADAPTIVE\_ANCHOR)
* 최초 `show()` 이후 자동 로드 및 새로고침 기능
* Programmatic / XML 방식 모두 지원
* 이벤트 기반 콜백으로 광고 상태 추적

#### 일반 배너와의 차이

<table><thead><tr><th width="140.6015625">구분</th><th>일반 배너</th><th>프리로드 배너</th></tr></thead><tbody><tr><td>클래스</td><td><code>AdWhaleMediationAdView</code></td><td><code>AdWhaleMediationAdBannerView</code></td></tr><tr><td>리스너</td><td><code>AdWhaleMediationAdViewListener</code></td><td><code>AdWhaleMediationAdBannerViewListener</code></td></tr><tr><td>리스너 등록</td><td><code>setAdWhaleMediationAdViewListener()</code></td><td><code>setAdWhaleMediationAdBannerViewListener()</code></td></tr><tr><td>노출 시점</td><td>로드 성공 즉시</td><td><code>show()</code> 호출 시</td></tr><tr><td>갱신 광고 노출</td><td>자동</td><td>최초 <code>show()</code> 이후 자동</td></tr><tr><td>클릭 콜백</td><td><code>onAdClicked()</code></td><td><code>onClickAd()</code></td></tr></tbody></table>

#### 2. 기본 구현 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleMediationAdBannerView` 클래스를 사용하여 배너 광고를 로드하고 표시하는 기본적인 구현 방법입니다.

{% tabs %}
{% tab title="Java" %}
```java
private AdWhaleMediationAdBannerView adView;

@Override
protected void onCreate(Bundle savedInstanceState) {
    // 1. 뷰 생성
    adView = new AdWhaleMediationAdBannerView(this);
    
    // 2. 설정
    adView.setPlacementUid("발급받은 placement uid 값");
    adView.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER320x50);
    
    // 3. 리스너 등록
    adView.setAdWhaleMediationAdBannerViewListener(new AdWhaleMediationAdBannerViewListener() {
        @Override public void onAdLoaded() { /* 로드 성공 */ 
            // 5. onAdLoaded() 이후 show() 호출
            adView.show();
        }
        @Override public void onAdLoadFailed(int statusCode, String message) { /* 로드 실패 */ }
        @Override public void onClickAd() { /* 클릭 */ }
        @Override public void onAdShowFailed(int statusCode, String message) { /* 노출 실패 */ }
        @Override public void onShowLandingScreen() { /* 랜딩 화면 열림 */ }
        @Override public void onCloseLandingScreen() { /* 랜딩 화면 닫힘 */ }
        @Override public void onTimeout(String errorMessage) { /* 네트워크 타임아웃 */ }
    });
    
    // 4. 레이아웃에 추가 후, SDK 초기화 완료 콜백에서 loadAd() 호출
    root.addView(adView, params);
    adView.loadAd();
}

// 6. Activity 라이프사이클에서 resume / pause / destroy 호출
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
private var adView: AdWhaleMediationAdBannerView? = null

override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    // 예시: root는 실제 레이아웃의 컨테이너로 교체
    val root: ViewGroup = findViewById(R.id.root)
    val params = ViewGroup.LayoutParams(
        ViewGroup.LayoutParams.WRAP_CONTENT,
        ViewGroup.LayoutParams.WRAP_CONTENT
    )

    // 1. 뷰 생성
    adView = AdWhaleMediationAdBannerView(this)

    // 2. 설정
    adView?.setPlacementUid("발급받은 placement uid 값")
    adView?.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER320x50)

    // 3. 리스너 등록
    adView?.setAdWhaleMediationAdBannerViewListener(object : AdWhaleMediationAdBannerViewListener {
        override fun onAdLoaded() { /* 로드 성공 */
            // 5. onAdLoaded() 이후 show() 호출
            adView?.show()
        }
        override fun onAdLoadFailed(statusCode: Int, message: String) { /* 로드 실패 */ }
        override fun onClickAd() { /* 클릭 */ }
        override fun onAdShowFailed(statusCode: Int, message: String) { /* 노출 실패 */ }
        override fun onShowLandingScreen() { /* 랜딩 화면 열림 */ }
        override fun onCloseLandingScreen() { /* 랜딩 화면 닫힘 */ }
        override fun onTimeout(errorMessage: String) { /* 네트워크 타임아웃 */ }
    })

    // 4. 레이아웃에 추가 후, SDK 초기화 완료 콜백에서 loadAd() 호출
    root.addView(adView!!, params)
    adView?.loadAd()
}

// 6. Activity 라이프사이클에서 resume / pause / destroy 호출
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
        AdWhaleMediationAdBannerView(context).apply {
            // 2. 설정
            setPlacementUid(placementUid)
            setAdwhaleAdSize(adSize)

            // 3. 리스너 등록
            setAdWhaleMediationAdBannerViewListener(object : AdWhaleMediationAdBannerViewListener {
                override fun onAdLoaded() { /* 로드 성공 */
                    // 5. onAdLoaded() 이후 show() 호출
                    this@apply.show()
                }
                override fun onAdLoadFailed(statusCode: Int, message: String) { /* 로드 실패 */ }
                override fun onClickAd() { /* 클릭 */ }
                override fun onAdShowFailed(statusCode: Int, message: String) { /* 노출 실패 */ }
                override fun onShowLandingScreen() { /* 랜딩 화면 열림 */ }
                override fun onCloseLandingScreen() { /* 랜딩 화면 닫힘 */ }
                override fun onTimeout(errorMessage: String) { /* 네트워크 타임아웃 */ }
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

    // 6. Activity 라이프사이클에서 resume / pause / destroy 호출
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
**AdWhaleMediationAdBannerView 클래스 API 설명**

```java
public AdWhaleMediationAdBannerView(Context context)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.content.Context</td><td>Android Main Activity 클래스</td></tr></tbody></table>

```java
public void loadAd() // 미디에이션 배너광고 로드 (노출하지 않음)
```

```java
public void show() // 로드된 배너광고 노출. onAdLoaded() 이후 호출
```

<pre class="language-java"><code class="lang-java"><strong>public void setPlacementUid(String placementUid) // 지면 등록
</strong></code></pre>

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```java
public void setAdwhaleAdSize(ADWHALE_AD_SIZE adWhaleAdSize) // 미디에이션 배너사이즈 설정
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.ADWHALE_AD_SIZE</td><td>배너 광고 사이즈(사이즈 종류: BANNER320x50, BANNER320x100, BANNER300x250, BANNER250x250, ADAPTIVE_ANCHOR)</td></tr></tbody></table>

```java
public void setAdWhaleMediationAdBannerViewListener(AdWhaleMediationAdBannerViewListener listener) // 리스너 등록
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationAdBannerViewListener</p></td><td>프리로드 배너 미디에이션 광고 호출 콜백 리스너. <strong>loadAd() 전에 등록 필수</strong></td></tr></tbody></table>

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

**AdWhaleMediationAdBannerViewListener 클래스 API 설명**

```java
public void onAdLoaded() // 배너 광고요청 성공 시. 이 시점부터 show() 호출 가능
```

```java
public void onAdLoadFailed(int statusCode, String message) // 미디에이션 배너광고요청 실패 시
```

{% hint style="info" %}
**onAdLoadFailed 후처리 가이드**

* onAdLoadFailed는 **워터폴이 모두 소진됐을 때 1회만** 발생합니다. 개별 광고 네트워크의 실패로는 발생하지 않습니다.
* 기존에 노출 중인 광고가 있다면 그 광고 노출은 유지되지만, 자동 갱신은 멈춥니다. 광고 갱신을 재개하려면 `loadAd()` 를 다시 호출한 뒤 `show()` 하세요.
* **`onAdLoadFailed` 에서 무조건 광고 영역을 숨기지 마세요.** 이 콜백은 갱신 실패에서도 발생하며, 그때는 이전 광고가 그대로 노출 중입니다. 광고 영역을 `GONE` 으로 만들면 **노출 중인 광고를 스스로 가리게 됩니다.**
{% endhint %}

<table data-header-hidden><thead><tr><th width="132.26953125">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p><mark style="color:red;"><code>200</code></mark> = 연동 오류(placementUid · 배너 사이즈 미설정 등)</p><p>또는</p><p><mark style="color:red;"><code>300</code></mark> = 광고를 채우지 못함(워터폴 모두 소진)</p></td></tr><tr><td>String</td><td><p><mark style="color:red;"><code>placementUid is required.</code></mark> / <mark style="color:red;"><code>ADWHALE_AD_SIZE is required.</code></mark> = 연동 오류 메시지</p><p>또는</p><p><mark style="color:red;"><code>No ad mediation network found.</code></mark> = 노출 전 광고를 채우지 못함(워터폴 모두 소진)<br>또는<br><mark style="color:red;"><code>No ad mediation network found. Previous ad is still showing.</code></mark> = 갱신 중 광고를 채우지 못함(워터폴 모두 소진, 이전 광고 노출 유지)</p></td></tr></tbody></table>

```java
public void onAdShowFailed(int statusCode, String message) // 배너광고 노출 실패 시
```

<table data-header-hidden><thead><tr><th width="132.26953125">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p><mark style="color:red;"><code>200</code></mark> = 로드 전에 show() 호출</p><p>또는</p><p>그 외 = 광고 네트워크 노출 단계 실패</p></td></tr><tr><td>String</td><td><p><mark style="color:red;"><code>Ad is not loaded yet.</code></mark> = 로드 전에 show() 호출</p><p>또는</p><p>광고 네트워크가 전달한 실패 메시지</p></td></tr></tbody></table>

```java
public void onShowLandingScreen() // 배너 클릭으로 랜딩 화면이 열릴 때
```

```java
public void onCloseLandingScreen() // 랜딩 화면이 닫힐 때
```

```java
public void onTimeout(String errorMessage) // 광고 네트워크 응답 시간 초과 시. SDK가 다음 네트워크로 진행하므로 별도 처리 불필요
```

```java
public void onClickAd() // 배너 클릭 시
```
{% endtab %}

{% tab title="Kotlin" %}
**AdWhaleMediationAdBannerView 클래스 API 설명**

```kotlin
AdWhaleMediationAdBannerView(context : Context)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.content.Context</td><td>Android Main Activity 클래스</td></tr></tbody></table>

```kotlin
fun loadAd() : Unit // 미디에이션 배너광고 로드 (노출하지 않음)
```

```kotlin
fun show() : Unit // 로드된 배너광고 노출. onAdLoaded() 이후 호출
```

<pre class="language-kotlin"><code class="lang-kotlin"><strong>fun setPlacementUid(placementUid : String) : Unit // 지면 등록
</strong></code></pre>

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```kotlin
fun setAdwhaleAdSize(adWhaleAdSize : ADWHALE_AD_SIZE) : Unit // 미디에이션 배너사이즈 설정
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.ADWHALE_AD_SIZE</td><td>배너 광고 사이즈(사이즈 종류: BANNER320x50, BANNER320x100, BANNER300x250, BANNER250x250, ADAPTIVE_ANCHOR)</td></tr></tbody></table>

```kotlin
fun setAdWhaleMediationAdBannerViewListener(listener : AdWhaleMediationAdBannerViewListener) : Unit // 리스너 등록
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationAdBannerViewListener</p></td><td>프리로드 배너 미디에이션 광고 호출 콜백 리스너. <strong>loadAd() 전에 등록 필수</strong></td></tr></tbody></table>

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

**AdWhaleMediationAdBannerViewListener 클래스 API 설명**

```kotlin
fun onAdLoaded() : Unit // 배너 광고요청 성공 시. 이 시점부터 show() 호출 가능
```

```kotlin
fun onAdLoadFailed(statusCode : Int, message : String) : Unit // 미디에이션 배너광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td><p>실패 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>실패 메시지</p><p>(<mark style="color:red;">"placementUid is required." 등 연동 오류, "No ad mediation network found." 또는 "No ad mediation network found. Previous ad is still showing."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdShowFailed(statusCode : Int, message : String) : Unit // 배너광고 노출 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td><p>실패 코드</p><p>(<mark style="color:red;">200</mark> = 로드 전에 show() 호출, 그 외 = 광고 네트워크 노출 실패)</p></td></tr><tr><td>String</td><td><p>실패 메시지</p><p>(<mark style="color:red;">"Ad is not loaded yet."</mark> 또는 광고 네트워크가 전달한 메시지)</p></td></tr></tbody></table>

```kotlin
fun onShowLandingScreen() : Unit // 배너 클릭으로 랜딩 화면이 열릴 때
```

```kotlin
fun onCloseLandingScreen() : Unit // 랜딩 화면이 닫힐 때
```

```kotlin
fun onTimeout(errorMessage : String) : Unit // 광고 네트워크 응답 시간 초과 시. SDK가 다음 네트워크로 진행하므로 별도 처리 불필요
```

```kotlin
fun onClickAd() : Unit // 배너 클릭 시
```
{% endtab %}

{% tab title="Compose" %}
**AdWhaleMediationAdBannerView 클래스 API 설명**

```kotlin
AdWhaleMediationAdBannerView(context : Context)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.content.Context</td><td>Android Main Activity 클래스</td></tr></tbody></table>

```kotlin
fun loadAd() : Unit // 미디에이션 배너광고 로드 (노출하지 않음)
```

```kotlin
fun show() : Unit // 로드된 배너광고 노출. onAdLoaded() 이후 호출
```

<pre class="language-kotlin"><code class="lang-kotlin"><strong>fun setPlacementUid(placementUid : String) : Unit // 지면 등록
</strong></code></pre>

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```kotlin
fun setAdwhaleAdSize(adWhaleAdSize : ADWHALE_AD_SIZE) : Unit // 미디에이션 배너사이즈 설정
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.ADWHALE_AD_SIZE</td><td>배너 광고 사이즈(사이즈 종류: BANNER320x50, BANNER320x100, BANNER300x250, BANNER250x250, ADAPTIVE_ANCHOR)</td></tr></tbody></table>

```kotlin
fun setAdWhaleMediationAdBannerViewListener(listener : AdWhaleMediationAdBannerViewListener) : Unit // 리스너 등록
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationAdBannerViewListener</p></td><td>프리로드 배너 미디에이션 광고 호출 콜백 리스너. <strong>loadAd() 전에 등록 필수</strong></td></tr></tbody></table>

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

**AdWhaleMediationAdBannerViewListener 클래스 API 설명**

```kotlin
fun onAdLoaded() : Unit // 배너 광고요청 성공 시. 이 시점부터 show() 호출 가능
```

```kotlin
fun onAdLoadFailed(statusCode : Int, message : String) : Unit // 미디에이션 배너광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td><p>실패 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>실패 메시지</p><p>(<mark style="color:red;">"placementUid is required." 등 연동 오류, "No ad mediation network found." 또는 "No ad mediation network found. Previous ad is still showing."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdShowFailed(statusCode : Int, message : String) : Unit // 배너광고 노출 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td><p>실패 코드</p><p>(<mark style="color:red;">200</mark> = 로드 전에 show() 호출, 그 외 = 광고 네트워크 노출 실패)</p></td></tr><tr><td>String</td><td><p>실패 메시지</p><p>(<mark style="color:red;">"Ad is not loaded yet."</mark> 또는 광고 네트워크가 전달한 메시지)</p></td></tr></tbody></table>

```kotlin
fun onShowLandingScreen() : Unit // 배너 클릭으로 랜딩 화면이 열릴 때
```

```kotlin
fun onCloseLandingScreen() : Unit // 랜딩 화면이 닫힐 때
```

```kotlin
fun onTimeout(errorMessage : String) : Unit // 광고 네트워크 응답 시간 초과 시. SDK가 다음 네트워크로 진행하므로 별도 처리 불필요
```

```kotlin
fun onClickAd() : Unit // 배너 클릭 시
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
```

{% hint style="info" %}
AdWhale SDK는 Cauly 네트워크를 지원하며, 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}

```java
adView.setPlacementName("banner_main");
```

{% hint style="info" %}
AdWhale SDK는 LevelPlay 네트워크를 지원하며, 각 Placement 별로 광고 노출을 구분하고자 할 때, `setPlacementName()` API를 통해 설정할 수 있습니다.
{% endhint %}

{% hint style="warning" %}
LevelPlay 콘솔에서 설정한 Placement 이름을 지정하면, 해당 Placement에 설정된 **보상 금액, 노출 제한 등의 설정이 적용된 광고**가 노출됩니다.\
설정하지 않으면 기본 Placement(Default Placement)가 사용됩니다.

***

배너 광고에는 **보상 조건, 제한 조건 등의 설정이 적용되지 않으며**, Placement는 **광고 위치 구분 및 분석 용도**로만 사용됩니다.
{% endhint %}
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
adView.setRegion("서울시 강남구") // 지역 타게팅 전용 API(옵션)
adView.setGcoder(37.5665, 126.9780) // 지역 타게팅 전용 API(옵션)
```

{% hint style="info" %}
AdWhale SDK는 Cauly 네트워크를 지원하며, 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}

```kotlin
adView.setPlacementName("banner_main")
```

{% hint style="info" %}
AdWhale SDK는 LevelPlay 네트워크를 지원하며, 각 Placement 별로 광고 노출을 구분하고자 할 때, `setPlacementName()` API를 통해 설정할 수 있습니다.
{% endhint %}

{% hint style="warning" %}
LevelPlay 콘솔에서 설정한 Placement 이름을 지정하면, 해당 Placement에 설정된 **보상 금액, 노출 제한 등의 설정이 적용된 광고**가 노출됩니다.\
설정하지 않으면 기본 Placement(Default Placement)가 사용됩니다.

***

배너 광고에는 **보상 조건, 제한 조건 등의 설정이 적용되지 않으며**, Placement는 **광고 위치 구분 및 분석 용도**로만 사용됩니다.
{% endhint %}
{% endtab %}

{% tab title="Compose" %}
```kotlin
adView.setRegion("서울시 강남구") // 지역 타게팅 전용 API(옵션)
adView.setGcoder(37.5665, 126.9780) // 지역 타게팅 전용 API(옵션)
```

{% hint style="info" %}
AdWhale SDK는 Cauly 네트워크를 지원하며, 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}

```kotlin
adView.setPlacementName("banner_main")
```

{% hint style="info" %}
AdWhale SDK는 LevelPlay 네트워크를 지원하며, 각 Placement 별로 광고 노출을 구분하고자 할 때, `setPlacementName()` API를 통해 설정할 수 있습니다.
{% endhint %}

{% hint style="warning" %}
LevelPlay 콘솔에서 설정한 Placement 이름을 지정하면, 해당 Placement에 설정된 **보상 금액, 노출 제한 등의 설정이 적용된 광고**가 노출됩니다.\
설정하지 않으면 기본 Placement(Default Placement)가 사용됩니다.

***

배너 광고에는 **보상 조건, 제한 조건 등의 설정이 적용되지 않으며**, Placement는 **광고 위치 구분 및 분석 용도**로만 사용됩니다.
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
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdBannerView;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdBannerViewListener;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener;
import net.adwhale.sdk.utils.AdWhaleLog;

public class MainActivity extends AppCompatActivity {

    private ConstraintLayout root;

    private AdWhaleMediationAdBannerView adWhaleMediationAdBannerView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        root = (ConstraintLayout) findViewById(R.id.root);      
    
        // 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None);
            
        // 배너광고 뷰 생성
        adWhaleMediationAdBannerView = new AdWhaleMediationAdBannerView(this);
        adWhaleMediationAdBannerView.setPlacementUid("발급받은 placement uid 값");
        adWhaleMediationAdBannerView.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER320x50);
        // 적응형 배너 뷰를 적용할 경우 
        // adWhaleMediationAdBannerView.setAdwhaleAdSize(ADWHALE_AD_SIZE.ADAPTIVE_ANCHOR);
        // adWhaleMediationAdBannerView.setAdaptiveAnchorWidth(360); // 디바이스 width 입력. 0을 입력할 경우 디바이스 전체 가로 길이 적용됨.
        
        
        // 배너광고 뷰 콜백 리스너 등록
        adWhaleMediationAdBannerView.setAdWhaleMediationAdBannerViewListener(new AdWhaleMediationAdBannerViewListener() {
            @Override
            public void onAdLoaded() {
                Log.i(MainActivity.class.getSimpleName(), ".onAdLoaded();");
                // 로드 완료 후 배너광고 뷰 노출
                adWhaleMediationAdBannerView.show();
            }

            @Override
            public void onAdLoadFailed(int statusCode, String message) {
                Log.i(MainActivity.class.getSimpleName(), ".onAdLoadFailed(" + statusCode + ", " + message + ");");
            }
            
            @Override
            public void onClickAd() {
                Log.i(MainActivity.class.getSimpleName(), ".onClickAd();");
            }

            @Override
            public void onAdShowFailed(int statusCode, String message) {
                Log.i(MainActivity.class.getSimpleName(), ".onAdShowFailed(" + statusCode + ", " + message + ");");
            }

            @Override
            public void onShowLandingScreen() {
                Log.i(MainActivity.class.getSimpleName(), ".onShowLandingScreen();");
            }

            @Override
            public void onCloseLandingScreen() {
                Log.i(MainActivity.class.getSimpleName(), ".onCloseLandingScreen();");
            }

            @Override
            public void onTimeout(String errorMessage) {
                Log.i(MainActivity.class.getSimpleName(), ".onTimeout(" + errorMessage + ");");
            }
        });
        
        // LayoutParams 적용 및 root view에 add
        RelativeLayout.LayoutParams params = new RelativeLayout.LayoutParams(
                LinearLayout.LayoutParams.WRAP_CONTENT, LinearLayout.LayoutParams.WRAP_CONTENT);
        params.addRule(RelativeLayout.ALIGN_PARENT_BOTTOM);
        params.addRule(RelativeLayout.CENTER_HORIZONTAL);
        root.addView(adWhaleMediationAdBannerView, params);
                
        // 초기화 코드
        AdWhaleMediationAds.init(this, new AdWhaleMediationOnInitCompleteListener() {
            @Override
            public void onInitComplete(int statusCode, String message) {
                Log.i(MainActivity.class.getSimpleName(), ".onInitComplete(" + statusCode + ", " + message + ");");
                if(adWhaleMediationAdBannerView != null) {
                    // 배너광고 뷰 로드
                    new Handler(Looper.getMainLooper()).postDelayed(() -> {
                        adWhaleMediationAdBannerView.loadAd();
                    },0);                    
                }
            }
        });  
    }

    // 라이프사이클 onResume 콜백 시 반드시 onResume 호출 필요(미호출 시 불이익 발생)
    @Override
    protected void onResume() {
        super.onResume();
        if(adWhaleMediationAdBannerView != null) {
            adWhaleMediationAdBannerView.resume();        
        }
    }


    // 라이프사이클 onPause 콜백 시 반드시 onPause 호출 필요(미호출 시 불이익 발생)
    @Override
    protected void onPause() {
        super.onPause();
        if(adWhaleMediationAdBannerView != null) {
            adWhaleMediationAdBannerView.pause();
        }    
    }

    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy 호출 필요
    @Override
    protected void onDestroy() {
        super.onDestroy();
        if(adWhaleMediationAdBannerView != null) {
            adWhaleMediationAdBannerView.destroy();        
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
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdBannerView
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdBannerViewListener
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds

class MainActivity : AppCompatActivity() {

    private lateinit var root: ConstraintLayout
    private lateinit var adWhaleMediationAdBannerView: AdWhaleMediationAdBannerView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        root = findViewById<View>(R.id.root) as ConstraintLayout
               
        // 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)
         
        // 배너광고 뷰 생성
        adWhaleMediationAdBannerView = AdWhaleMediationAdBannerView(this)
        adWhaleMediationAdBannerView.setPlacementUid("발급받은 placement uid 값")
        adWhaleMediationAdBannerView.setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER320x50)
        // 적응형 배너 뷰를 적용할 경우 
        // adWhaleMediationAdBannerView.setAdwhaleAdSize(ADWHALE_AD_SIZE.ADAPTIVE_ANCHOR)
        // adWhaleMediationAdBannerView.setAdaptiveAnchorWidth(360) // 디바이스 width 입력. 0을 입력할 경우 디바이스 전체 가로 길이 적용됨.
        
        // 배너광고 뷰 콜백 리스너 등록        
        adWhaleMediationAdBannerView!!.adWhaleMediationAdBannerViewListener =
            object : AdWhaleMediationAdBannerViewListener {
                override fun onAdLoaded() {
                    Log.i(MainActivity::class.simpleName, ".onAdLoaded()")
                    // 로드 완료 후 배너광고 뷰 노출
                    adWhaleMediationAdBannerView!!.show()
                }

                override fun onAdLoadFailed(statusCode: Int, message: String) {
                    Log.i(MainActivity::class.simpleName, ".onAdLoadFailed($statusCode, $message)")
                }
                
                override fun onClickAd() {
                    Log.i(MainActivity::class.simpleName, ".onClickAd()")
                }

                override fun onAdShowFailed(statusCode: Int, message: String) {
                    Log.i(MainActivity::class.simpleName, ".onAdShowFailed($statusCode, $message)")
                }

                override fun onShowLandingScreen() {
                    Log.i(MainActivity::class.simpleName, ".onShowLandingScreen()")
                }

                override fun onCloseLandingScreen() {
                    Log.i(MainActivity::class.simpleName, ".onCloseLandingScreen()")
                }

                override fun onTimeout(errorMessage: String) {
                    Log.i(MainActivity::class.simpleName, ".onTimeout($errorMessage)")
                }
            }        

        // LayoutParams 적용 및 root view에 add        
        val params = RelativeLayout.LayoutParams(
            LinearLayout.LayoutParams.WRAP_CONTENT, LinearLayout.LayoutParams.WRAP_CONTENT
        )
        params.addRule(RelativeLayout.ALIGN_PARENT_BOTTOM)
        params.addRule(RelativeLayout.CENTER_HORIZONTAL)
        root!!.addView(adWhaleMediationAdBannerView, params)    
        
        // 초기화 코드        
        AdWhaleMediationAds.init(this) { statusCode, message ->
            Log.i(MainActivity::class.simpleName, ".onInitComplete($statusCode, $message)")
            // 배너 뷰 로드
            Handler(Looper.getMainLooper()).postDelayed({
                adWhaleMediationAdBannerView!!.loadAd()
            }, 0)         
        }        
    }

    // 라이프사이클 onResume 콜백 시 반드시 onResume 호출 필요(미호출 시 불이익 발생)
    override fun onResume() {
        super.onResume()
        adWhaleMediationAdBannerView!!.resume()
    }
    
    // 라이프사이클 onPause 콜백 시 반드시 onPause 호출 필요(미호출 시 불이익 발생)
    override fun onPause() {
        super.onPause()
        adWhaleMediationAdBannerView!!.pause()
    }
    
    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy 호출 필요
    override fun onDestroy() {
        super.onDestroy()
        adWhaleMediationAdBannerView!!.destroy()
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
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdBannerView
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdBannerViewListener
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener
import net.adwhale.sdk.utils.AdWhaleLog

class MainActivity : ComponentActivity() {

    private var adWhaleMediationAdBannerView: AdWhaleMediationAdBannerView? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)

        // 먼저 배너 View 생성 (Compose에서 재사용)
        adWhaleMediationAdBannerView = createBannerView(this)

        // AdWhale 초기화
        AdWhaleMediationAds.init(this, object : AdWhaleMediationOnInitCompleteListener {
            override fun onInitComplete(statusCode: Int, message: String?) {
                Log.i(MainActivity::class.java.simpleName,
                    ".onInitComplete($statusCode, $message);")

                // 초기화 완료 후 배너 로드
                if (adWhaleMediationAdBannerView != null) {
                    Handler(Looper.getMainLooper()).postDelayed({
                        adWhaleMediationAdBannerView?.loadAd()
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
                        adWhaleMediationAdBannerView ?: createBannerView(context).also {
                            adWhaleMediationAdBannerView = it
                        }
                    }
                )
            }
        }
    }

    private fun createBannerView(context: Context): AdWhaleMediationAdBannerView {
        return AdWhaleMediationAdBannerView(context).apply {
            // placement uid 설정
            setPlacementUid("발급받은 placement uid 값")

            // 고정 배너 사이즈 320x50
            setAdwhaleAdSize(ADWHALE_AD_SIZE.BANNER320x50)

            // 적응형 배너를 쓸 경우:
            // setAdwhaleAdSize(ADWHALE_AD_SIZE.ADAPTIVE_ANCHOR)
            // setAdaptiveAnchorWidth(360) // 디바이스 width. 0이면 전체 가로 길이

            setAdWhaleMediationAdBannerViewListener(object : AdWhaleMediationAdBannerViewListener {
                override fun onAdLoaded() {
                    Log.i(MainActivity::class.java.simpleName, ".onAdLoaded();")
                    // 로드 완료 후 배너 노출
                    this@apply.show()
                }

                override fun onAdLoadFailed(statusCode: Int, message: String?) {
                    Log.i(
                        MainActivity::class.java.simpleName,
                        ".onAdLoadFailed($statusCode, $message);"
                    )
                }

                override fun onClickAd() {
                    Log.i(MainActivity::class.java.simpleName, ".onClickAd();")
                }

                override fun onAdShowFailed(statusCode: Int, message: String?) {
                    Log.i(
                        MainActivity::class.java.simpleName,
                        ".onAdShowFailed($statusCode, $message);"
                    )
                }

                override fun onShowLandingScreen() {
                    Log.i(MainActivity::class.java.simpleName, ".onShowLandingScreen();")
                }

                override fun onCloseLandingScreen() {
                    Log.i(MainActivity::class.java.simpleName, ".onCloseLandingScreen();")
                }

                override fun onTimeout(errorMessage: String?) {
                    Log.i(MainActivity::class.java.simpleName, ".onTimeout($errorMessage);")
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
        adWhaleMediationAdBannerView?.resume()
    }

    // 라이프사이클 onPause 콜백 시 반드시 onPause 호출 필요
    override fun onPause() {
        super.onPause()
        adWhaleMediationAdBannerView?.pause()
    }

    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy 호출 필요
    override fun onDestroy() {
        super.onDestroy()
        adWhaleMediationAdBannerView?.destroy()
        adWhaleMediationAdBannerView = null
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

    <net.adwhale.sdk.mediation.ads.AdWhaleMediationAdBannerView
        android:id="@+id/adWhaleMediationAdBannerView"
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

import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdBannerView;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdBannerViewListener;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener;
import net.adwhale.sdk.utils.AdWhaleLog;

public class MainActivity extends AppCompatActivity {

    private AdWhaleMediationAdBannerView adWhaleMediationAdBannerView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None);
        
        // 배너 뷰 바인딩
        adWhaleMediationAdBannerView = findViewById(R.id.adWhaleMediationAdBannerView);
        
        // 배너 뷰 콜백 리스너 등록
        adWhaleMediationAdBannerView.setAdWhaleMediationAdBannerViewListener(new AdWhaleMediationAdBannerViewListener() {
            @Override
            public void onAdLoaded() {
                Log.i(MainActivity.class.getSimpleName(), ".onAdLoaded();");
                // 로드 완료 후 배너 뷰 노출
                adWhaleMediationAdBannerView.show();
            }

            @Override
            public void onAdLoadFailed(int statusCode, String message) {
                Log.i(MainActivity.class.getSimpleName(), ".onAdLoadFailed(" + statusCode + ", " + message + ");");
            }
            
            @Override
            public void onClickAd() {
                Log.i(MainActivity.class.getSimpleName(), ".onClickAd();");
            }

            @Override
            public void onAdShowFailed(int statusCode, String message) {
                Log.i(MainActivity.class.getSimpleName(), ".onAdShowFailed(" + statusCode + ", " + message + ");");
            }

            @Override
            public void onShowLandingScreen() {
                Log.i(MainActivity.class.getSimpleName(), ".onShowLandingScreen();");
            }

            @Override
            public void onCloseLandingScreen() {
                Log.i(MainActivity.class.getSimpleName(), ".onCloseLandingScreen();");
            }

            @Override
            public void onTimeout(String errorMessage) {
                Log.i(MainActivity.class.getSimpleName(), ".onTimeout(" + errorMessage + ");");
            }
        });
        
        // 초기화 코드
        AdWhaleMediationAds.init(this, new AdWhaleMediationOnInitCompleteListener() {
            @Override
            public void onInitComplete(int statusCode, String message) {
                Log.i(MainActivity.class.getSimpleName(), ".onInitComplete(" + statusCode + ", " + message + ");");
                if(adWhaleMediationAdBannerView != null) {
                    // 배너 뷰 로드
                    new Handler(Looper.getMainLooper()).postDelayed(() -> {
                        adWhaleMediationAdBannerView.loadAd();
                    },0);  
                }
            }
        });        
    }

    // 라이프사이클 onResume 콜백 시 반드시 onResume 호출 필요(미호출 시 불이익 발생)
    @Override
    protected void onResume() {
        super.onResume();
        if(adWhaleMediationAdBannerView != null) {
            adWhaleMediationAdBannerView.resume();        
        }
    }


    // 라이프사이클 onPause 콜백 시 반드시 onPause 호출 필요(미호출 시 불이익 발생)
    @Override
    protected void onPause() {
        super.onPause();
        if(adWhaleMediationAdBannerView != null) {
            adWhaleMediationAdBannerView.pause();
        }    
    }

    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy 호출 필요
    @Override
    protected void onDestroy() {
        super.onDestroy();
        if(adWhaleMediationAdBannerView != null) {
            adWhaleMediationAdBannerView.destroy();        
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

    <net.adwhale.sdk.mediation.ads.AdWhaleMediationAdBannerView
        android:id="@+id/adWhaleMediationAdBannerView"
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
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdBannerView
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdBannerViewListener
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds

class MainActivity : AppCompatActivity() {

    private lateinit var adWhaleMediationAdBannerView: AdWhaleMediationAdBannerView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 배너 뷰 바인딩
        adWhaleMediationAdBannerView = findViewById(R.id.adWhaleMediationAdBannerView)
        
        // 배너 뷰 콜백 리스너 등록        
        adWhaleMediationAdBannerView!!.adWhaleMediationAdBannerViewListener =
            object : AdWhaleMediationAdBannerViewListener {
                override fun onAdLoaded() {
                    Log.i(MainActivity::class.simpleName, ".onAdLoaded()")
                    // 로드 완료 후 배너 뷰 노출
                    adWhaleMediationAdBannerView!!.show()
                }

                override fun onAdLoadFailed(statusCode: Int, message: String) {
                    Log.i(MainActivity::class.simpleName, ".onAdLoadFailed($statusCode, $message)")
                }
                
                override fun onClickAd() {
                    Log.i(MainActivity::class.simpleName, ".onClickAd()")
                }

                override fun onAdShowFailed(statusCode: Int, message: String) {
                    Log.i(MainActivity::class.simpleName, ".onAdShowFailed($statusCode, $message)")
                }

                override fun onShowLandingScreen() {
                    Log.i(MainActivity::class.simpleName, ".onShowLandingScreen()")
                }

                override fun onCloseLandingScreen() {
                    Log.i(MainActivity::class.simpleName, ".onCloseLandingScreen()")
                }

                override fun onTimeout(errorMessage: String) {
                    Log.i(MainActivity::class.simpleName, ".onTimeout($errorMessage)")
                }
            }

        // 초기화 코드            
        AdWhaleMediationAds.init(this) { statusCode, message ->
            Log.i(MainActivity::class.simpleName, ".onInitComplete($statusCode, $message)")
            // 배너 뷰 로드
            Handler(Looper.getMainLooper()).postDelayed({
                adWhaleMediationAdBannerView!!.loadAd()
            }, 0)  
        }            
    }

    // 라이프사이클 onResume 콜백 시 반드시 onResume 호출 필요(미호출 시 불이익 발생)
    override fun onResume() {
        super.onResume()
        adWhaleMediationAdBannerView!!.resume()
    }

    // 라이프사이클 onPause 콜백 시 반드시 onPause 호출 필요(미호출 시 불이익 발생)
    override fun onPause() {
        super.onPause()
        adWhaleMediationAdBannerView!!.pause()
    }

    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy 호출 필요(미호출 시 불이익 발생)
    override fun onDestroy() {
        super.onDestroy()
        adWhaleMediationAdBannerView!!.destroy()
    }
}
```
{% endtab %}
{% endtabs %}

#### **8. 주의사항**

**노출(show) 호출**

* 콜백 리스너는 `loadAd()` 호출 전에 `setAdWhaleMediationAdBannerViewListener()`로 등록하세요. \
  리스너가 없으면 `loadAd()`는 아무 콜백 없이 종료됩니다.
* 일반 배너용 `setAdWhaleMediationAdViewListener()`로 등록한 리스너는 프리로드 배너에서 동작하지 않습니다.
* `show()`는 `onAdLoaded()` 이후에 호출하세요. \
  로드 전에 호출하면 노출되지 않고 `onAdShowFailed(200, "Ad is not loaded yet.")`가 호출됩니다.
* 최초 `show()` 이후에는 새로 로드된 광고가 자동으로 교체 노출됩니다. 매번 `show()`를 호출할 필요는 없습니다.
* `loadAd()`를 다시 호출하면 자동 교체 노출이 해제됩니다. 새 광고를 노출하려면 `onAdLoaded()` 이후 `show()`를 다시 호출하세요.

**광고 로드 타이밍**

* `loadAd()`는 SDK 초기화 완료(`onInitComplete`) 이후에 호출하는 것을 권장합니다.

**라이프사이클**

* `onResume` / `onPause` / `onDestroy`에서 각각 `resume()` / `pause()` / `destroy()` 호출이 필요합니다. 미호출 시 동작에 불이익이 있을 수 있습니다.

**재생성**

* 배너 사이즈나 placement UID를 바꿀 때는 뷰를 `destroy()`한 뒤 새로 생성한 후 다시 설정·로드하세요.

**에러 처리**

* `onAdLoadFailed` 이벤트에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.

**onAdLoadFailed 후처리 가이드**

* onAdLoadFailed는 **워터폴이 모두 소진됐을 때 1회만** 발생합니다. 개별 광고 네트워크의 실패로는 발생하지 않습니다.
* 기존에 노출 중인 광고가 있다면 그 광고 노출은 유지되지만, 자동 갱신은 멈춥니다. 광고 갱신을 재개하려면 `loadAd()`를 다시 호출한 뒤 `onAdLoaded()`에서 `show()`를 호출하세요.
* **`onAdLoadFailed` 에서 무조건 광고 영역을 숨기지 마세요.** 이 콜백은 갱신 실패에서도 발생하며, 그때는 이전 광고가 그대로 노출 중입니다. 광고 영역을 `GONE` 으로 만들면 **노출 중인 광고를 스스로 가리게 됩니다.**

**성능 최적화**

* 프리로드 배너 광고는 최초 `show()` 이후 자동으로 새로고침되므로, 불필요한 리렌더링을 방지하세요.

**테스트**

* 개발 환경에서는 테스트용 placement UID를 사용하세요.
