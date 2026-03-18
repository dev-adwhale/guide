# 보상형

{% hint style="info" %}
보상형 광고는 사용자가 광고를 시청한 후 보상을 받는 광고 형식입니다. 게임 내 아이템, 코인, 생명 등 다양한 보상을 제공하여 높은 참여도와 수익을 얻을 수 있습니다.
{% endhint %}

#### **1. 주요특징**

* 사용자 시청 완료 후 보상 제공
* 높은 참여도와 완료율
* 다양한 보상 타입 지원 (코인, 아이템 등)
* 로드·표시·닫힘·보상 지급 등 이벤트 기반 콜백

#### 2. 기본 구현 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleMediationRewardAd` 클래스를 사용하여 보상형 광고를 로드하고 표시하는 기본적인 구현 방법입니다.

{% tabs %}
{% tab title="Java" %}
```java
private AdWhaleMediationRewardAd rewardAd;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    // 1. 인스턴스 생성 (placementUid)
    rewardAd = new AdWhaleMediationRewardAd("발급받은 placement uid 값");

    // 2. 리스너 등록
    rewardAd.setAdWhaleMediationFullScreenContentCallback(new AdWhaleMediationFullScreenContentCallback() {
        @Override public void onFailedToShow(int statusCode, String message) {
            // 표시 실패
        }

        @Override public void onAdClicked() {
            // 클릭
        }

        @Override public void onAdDismissed() {
            // 닫힘
        }

        @Override public void onAdShowed() {
            // 표시됨
        }
    });

    // 3. 로드 (AdWhaleMediationRewardedAdLoadCallback)
    rewardAd.loadAd(new AdWhaleMediationRewardedAdLoadCallback() {
        @Override
        public void onAdLoaded(AdWhaleMediationRewardAd ad, String message) {
            // 로드 성공 → 표시 시 AdWhaleMediationUserEarnedRewardListener 전달
            ad.showAd(MainActivity.this, rewardItem -> {
                // 보상 지급 처리
            });
        }
        @Override
        public void onAdFailedToLoad(int statusCode, String message) { }
    });
}

@Override
protected void onDestroy() {
    if (rewardAd != null) {
        // 5. 폐기
        rewardAd.destroy();
        rewardAd = null;
    }
    super.onDestroy();
}
```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
class MainActivity : AppCompatActivity() {

    private var rewardAd: AdWhaleMediationRewardAd? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 1. 인스턴스 생성 (placementUid)
        rewardAd = AdWhaleMediationRewardAd(
            "발급받은 placement uid 값"
        )

        // 2. 리스너 등록 (FullScreenContentCallback)
        rewardAd?.setAdWhaleMediationFullScreenContentCallback(
            object : AdWhaleMediationFullScreenContentCallback {

                override fun onFailedToShow(statusCode: Int, message: String?) {
                    // 표시 실패
                }

                override fun onAdClicked() {
                    // 클릭
                }

                override fun onAdDismissed() {
                    // 닫힘
                }

                override fun onAdShowed() {
                    // 표시됨
                }
            }
        )

        // 3. 로드
        rewardAd?.loadAd(object : AdWhaleMediationRewardedAdLoadCallback {

            override fun onAdLoaded(
                ad: AdWhaleMediationRewardAd,
                message: String?
            ) {
                // 로드 성공 → 표시 시 보상 리스너 전달
                ad.showAd(this@MainActivity) { rewardItem ->
                    // 보상 지급 처리
                    // rewardItem 사용
                }
            }

            override fun onAdFailedToLoad(statusCode: Int, message: String?) {
                // 로드 실패
            }
        })
    }

    override fun onDestroy() {
        rewardAd?.destroy()
        rewardAd = null
        super.onDestroy()
    }
}
```
{% endtab %}

{% tab title="Compose" %}
```kotlin
@Composable
private fun RewardScreen(
    placementUid: String,
    placementName: String? = null,
    region: String? = null,
    gcoderLat: Double? = null,
    gcoderLng: Double? = null
) {
    var isInited by remember { mutableStateOf(false) }

    var rewardAd by remember { mutableStateOf<AdWhaleMediationRewardAd?>(null) }

    val context = LocalContext.current
    val activity = context as? ComponentActivity

    // 1. SDK 초기화
    LaunchedEffect(Unit) {
        AdWhaleMediationAds.init(
            context,
            object : AdWhaleMediationOnInitCompleteListener {
                override fun onInitComplete(statusCode: Int, message: String) {
                    Log.i("RewardScreen", "onInitComplete($statusCode, $message)")
                    isInited = true
                }
            }
        )
    }

    // 2. init 완료 후 광고 생성
    LaunchedEffect(isInited) {
        if (!isInited || activity == null) return@LaunchedEffect

        val ad = AdWhaleMediationRewardAd(placementUid).apply {

            if (region != null) setRegion(region)
            if (gcoderLat != null && gcoderLng != null) setGcoder(gcoderLat, gcoderLng)
            if (placementName != null) setPlacementName(placementName)

            // 3. FullScreen 콜백
            setAdWhaleMediationFullScreenContentCallback(
                object : AdWhaleMediationFullScreenContentCallback {

                    override fun onFailedToShow(statusCode: Int, message: String?) {
                        Log.e("RewardScreen", "onFailedToShow($statusCode, $message)")
                    }

                    override fun onAdClicked() {
                        Log.i("RewardScreen", "onAdClicked()")
                    }

                    override fun onAdDismissed() {
                        Log.i("RewardScreen", "onAdDismissed()")
                    }

                    override fun onAdShowed() {
                        Log.i("RewardScreen", "onAdShowed()")
                    }
                }
            )
        }

        rewardAd = ad

        // 4. 로드
        ad.loadAd(object : AdWhaleMediationRewardedAdLoadCallback {

            override fun onAdLoaded(
                ad: AdWhaleMediationRewardAd,
                message: String?
            ) {
                Log.i("RewardScreen", "onAdLoaded($message)")

                // 5. 표시 + 보상 리스너 전달
                ad.showAd(activity) { rewardItem ->
                    Log.i("RewardScreen", "UserEarnedReward: $rewardItem")

                    // 보상 지급 처리
                }
            }

            override fun onAdFailedToLoad(statusCode: Int, message: String?) {
                Log.e("RewardScreen", "onAdFailedToLoad($statusCode, $message)")
            }
        })
    }

    // 6. dispose 시 destroy
    DisposableEffect(Unit) {
        onDispose {
            rewardAd?.destroy()
            rewardAd = null
        }
    }

    // UI
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(20.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp)
    ) {
        Text(text = if (isInited) "SDK 초기화 완료" else "SDK 초기화 중...")

        Button(
            onClick = {
                activity?.let {
                    rewardAd?.showAd(it) { rewardItem ->
                        Log.i("RewardScreen", "UserEarnedReward: $rewardItem")
                    }
                }
            },
            enabled = isInited && rewardAd != null && activity != null
        ) {
            Text("RewardAd 표시")
        }

        Button(
            onClick = { rewardAd?.loadAd(object : AdWhaleMediationRewardedAdLoadCallback {
                override fun onAdLoaded(ad: AdWhaleMediationRewardAd, message: String?) {
                    Log.i("RewardScreen", "Reload success")
                }

                override fun onAdFailedToLoad(statusCode: Int, message: String?) {
                    Log.e("RewardScreen", "Reload fail: $statusCode")
                }
            }) },
            enabled = isInited && rewardAd != null
        ) {
            Text("다시 로드")
        }
    }
}
```
{% endtab %}
{% endtabs %}

#### 3. API 설명 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

{% tabs %}
{% tab title="Java" %}
**AdWhaleMediationRewardAd 클래스 API 설명**

```java
public AdWhaleMediationRewardAd(String placementUid)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```java
public void setAdWhaleMediationFullScreenContentCallback(AdWhaleMediationFullScreenContentCallback listener)
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationFullScreenContentCallback</td><td>보상형 미디에이션 광고 콜백 리스너</td></tr></tbody></table>

```java
public void loadAd(AdWhaleMediationRewardAdLoadCallback listener) // 미디에이션 보상형 광고로드
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardAdLoadCallback</td><td>보상형 미디에이션 광고 로드 콜백 리스너</td></tr></tbody></table>

```java
public void showAd(Activity activity, AdWhaleMediationUserEarnedRewardListener listener) // 미디에이션 보상형 광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationUserEarnedRewardListener</td><td>보상형 미디에이션 광고 보상 지급 리스너</td></tr></tbody></table>

```java
public void destroy() // onDestroy() 시 호출 혹은 더 이상 광고를 요청하지 않고 싶을 때 호출
```

\
**AdWhaleMediationRewardAdLoadCallback 클래스 API 설명**

```java
public void onAdLoaded(AdWhaleMediationRewardAd ad, String message) // 미디에이션 보상형 광고요청 성공 시
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardAd</td><td>AdWhaleMediationRewardAd 클래스. ad로 showAd 호출 가능</td></tr><tr><td>String</td><td>로드 성공 메시지</td></tr></tbody></table>

```java
public void onAdFailedToLoad(int statusCode, String message) // 미디에이션 보상형 광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

**AdWhaleMediationFullScreenContentCallback 클래스 API 설명**

```java
public void onAdShowed() // 미디에이션 보상형 광고표시 후
```

```java
public void onFailedToShow(int statusCode, String message) // 미디에이션 보상형 광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고표시 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```java
public void onAdDismissed() // 미디에이션 보상형 광고닫기 시
```

```java
public void onAdClicked() // 미디에이션 보상형 광고클릭 시
```

**AdWhaleMediationUserEarnedRewardListener 클래스 API 설명**

```java
public void onUserRewarded(AdWhaleMediationRewardItem rewardItem) // 미디에이션 보상형 광고 보상 지급 리스너
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardItem</td><td>리워드 지급 아이템. 타입·금액 등은 rewardItem에서 조회</td></tr></tbody></table>
{% endtab %}

{% tab title="Kotlin" %}
**AdWhaleMediationRewardAd 클래스 API 설명**

```kotlin
AdWhaleMediationRewardAd(placementUid : String)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```kotlin
fun setAdWhaleMediationFullScreenContentCallback(listener : AdWhaleMediationFullScreenContentCallback) : Unit
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationFullScreenContentCallback</td><td>보상형 미디에이션 광고 콜백 리스너</td></tr></tbody></table>

```kotlin
fun loadAd(listener : AdWhaleMediationRewardAdLoadCallback) : Unit // 미디에이션 보상형 광고로드
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardAdLoadCallback</td><td>보상형 미디에이션 광고 로드 콜백 리스너</td></tr></tbody></table>

```kotlin
fun showAd(activity : Activity, listener : AdWhaleMediationUserEarnedRewardListener) : Unit // 미디에이션 보상형 광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationUserEarnedRewardListener</td><td>보상형 미디에이션 광고 보상 지급 리스너</td></tr></tbody></table>

```kotlin
fun destroy() : Unit // onDestroy() 시 호출 혹은 더 이상 광고를 요청하지 않고 싶을 때 호출
```

\
**AdWhaleMediationRewardAdLoadCallback 클래스 API 설명**

```kotlin
fun onAdLoaded(ad : AdWhaleMediationRewardAd, message : String) : Unit // 미디에이션 보상형 광고요청 성공 시
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardAd</td><td>AdWhaleMediationRewardAd 클래스. ad로 showAd 호출 가능</td></tr><tr><td>String</td><td>로드 성공 메시지</td></tr></tbody></table>

```kotlin
fun onAdFailedToLoad(statusCode : Int, message : String) : Unit // 미디에이션 보상형 광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

**AdWhaleMediationFullScreenContentCallback 클래스 API 설명**

```kotlin
fun onAdShowed() : Unit // 미디에이션 보상형 광고표시 후
```

```kotlin
fun onFailedToShow(statusCode : Unit, message : String) : Unit // 미디에이션 보상형 광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고표시 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdDismissed() : Unit // 미디에이션 보상형 광고닫기 시
```

```kotlin
fun onAdClicked() : Unit // 미디에이션 보상형 광고클릭 시
```

**AdWhaleMediationUserEarnedRewardListener 클래스 API 설명**

```kotlin
fun onUserRewarded(rewardItem : AdWhaleMediationRewardItem) : Unit // 미디에이션 보상형 광고 보상 지급 리스너
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardItem</td><td>리워드 지급 아이템. 타입·금액 등은 rewardItem에서 조회</td></tr></tbody></table>
{% endtab %}

{% tab title="Compose" %}
**AdWhaleMediationRewardAd 클래스 API 설명**

```kotlin
AdWhaleMediationRewardAd(placementUid : String)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```kotlin
fun setAdWhaleMediationFullScreenContentCallback(listener : AdWhaleMediationFullScreenContentCallback) : Unit
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationFullScreenContentCallback</td><td>보상형 미디에이션 광고 콜백 리스너</td></tr></tbody></table>

```kotlin
fun loadAd(listener : AdWhaleMediationRewardAdLoadCallback) : Unit // 미디에이션 보상형 광고로드
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardAdLoadCallback</td><td>보상형 미디에이션 광고 로드 콜백 리스너</td></tr></tbody></table>

```kotlin
fun showAd(activity : Activity, listener : AdWhaleMediationUserEarnedRewardListener) : Unit // 미디에이션 보상형 광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationUserEarnedRewardListener</td><td>보상형 미디에이션 광고 보상 지급 리스너</td></tr></tbody></table>

```kotlin
fun destroy() : Unit // onDestroy() 시 호출 혹은 더 이상 광고를 요청하지 않고 싶을 때 호출
```

\
**AdWhaleMediationRewardAdLoadCallback 클래스 API 설명**

```kotlin
fun onAdLoaded(ad : AdWhaleMediationRewardAd, message : String) : Unit // 미디에이션 보상형 광고요청 성공 시
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardAd</td><td>AdWhaleMediationRewardAd 클래스. ad로 showAd 호출 가능</td></tr><tr><td>String</td><td>로드 성공 메시지</td></tr></tbody></table>

```kotlin
fun onAdFailedToLoad(statusCode : Int, message : String) : Unit // 미디에이션 보상형 광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

**AdWhaleMediationFullScreenContentCallback 클래스 API 설명**

```kotlin
fun onAdShowed() : Unit // 미디에이션 보상형 광고표시 후
```

```kotlin
fun onFailedToShow(statusCode : Unit, message : String) : Unit // 미디에이션 보상형 광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고표시 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdDismissed() : Unit // 미디에이션 보상형 광고닫기 시
```

```kotlin
fun onAdClicked() : Unit // 미디에이션 보상형 광고클릭 시
```

**AdWhaleMediationUserEarnedRewardListener 클래스 API 설명**

```kotlin
fun onUserRewarded(rewardItem : AdWhaleMediationRewardItem) : Unit // 미디에이션 보상형 광고 보상 지급 리스너
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardItem</td><td>리워드 지급 아이템. 타입·금액 등은 rewardItem에서 조회</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

#### 4. 옵션 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

{% tabs %}
{% tab title="Java" %}
```java
rewardAd.setRegion("서울시 강남구"); // 지역 타게팅 전용 API(옵션)
rewardAd.setGcoder(37.5665, 126.9780); // 지역 타게팅 전용 API(옵션)
rewardAd.setPlacementName("reward_main"); // 레벨플레이 placement name 연동 전용 API (옵션)
```

{% hint style="info" %}
ADwhale 에서는 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
rewardAd.setRegion("서울시 강남구") // 지역 타게팅 전용 API(옵션)
rewardAd.setGcoder(37.5665, 126.9780) // 지역 타게팅 전용 API(옵션)
rewardAd.setPlacementName("reward_main") // 레벨플레이 placement name 연동 전용 API (옵션)
```

{% hint style="info" %}
ADwhale 에서는 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}
{% endtab %}

{% tab title="Compose" %}
```kotlin
rewardAd.setRegion("서울시 강남구") // 지역 타게팅 전용 API(옵션)
rewardAd.setGcoder(37.5665, 126.9780) // 지역 타게팅 전용 API(옵션)
rewardAd.setPlacementName("reward_main") // 레벨플레이 placement name 연동 전용 API (옵션)
```

{% hint style="info" %}
ADwhale 에서는 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}
{% endtab %}
{% endtabs %}

#### 5. 보상형 광고 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

다음은 안드로이드에서 보상형 광고를 구현하는 완전한 예시입니다.

{% tabs %}
{% tab title="Java" %}
```java
import android.os.Bundle;
import android.util.Log;
import androidx.appcompat.app.AppCompatActivity;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationFullScreenContentCallback;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardAd;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardedAdLoadCallback;
import net.adwhale.sdk.utils.AdWhaleLog;

public class MainActivity extends AppCompatActivity {

    private AdWhaleMediationRewardAd rewardAd;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 1. 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None);

        // 2. 초기화 코드
        AdWhaleMediationAds.init(this, new AdWhaleMediationOnInitCompleteListener() {
            @Override
            public void onInitComplete(int statusCode, String message) {
                Log.i("MainActivity", "onInitComplete(" + statusCode + ", " + message + ")");
                createAndLoadRewardAd();
            }
        });
    }

    private void createAndLoadRewardAd() {
        // 3. 인스턴스 생성 (Activity, placementUid)
        rewardAd = new AdWhaleMediationRewardAd("발급받은 placement uid 값");
        rewardAd.setRegion("서울시 강남구"); // 지역 타게팅 전용 API(옵션)
        rewardAd.setGcoder(37.5, 126.9); // 지역 타게팅 전용 API(옵션)
        rewardAd.setPlacementName("reward-main"); // 레벨플레이 placement name 연동 전용 API (옵션)

        // 4. 풀스크린 콜백 (표시·닫힘·클릭·표시 실패)
        rewardAd.setAdWhaleMediationFullScreenContentCallback(new AdWhaleMediationFullScreenContentCallback() {
            @Override
            public void onAdShowed() {
                Log.i("MainActivity", "onAdShowed()");
            }

            @Override
            public void onAdDismissed() {
                Log.i("MainActivity", "onAdDismissed()");
            }

            @Override
            public void onAdClicked() {
                Log.i("MainActivity", "onAdClicked()");
            }

            @Override
            public void onFailedToShow(int statusCode, String message) {
                Log.e("MainActivity", "onFailedToShow(" + statusCode + ", " + message + ")");
            }
        });

        // 5. 로드
        rewardAd.loadAd(new AdWhaleMediationRewardedAdLoadCallback() {
            @Override
            public void onAdLoaded(AdWhaleMediationRewardAd ad, String message) {
                Log.i("MainActivity", "onAdLoaded(" + message + ")");
                if (ad != null) {
                    // 6. 표시 (시청 완료 후 onUserRewarded 호출)
                    ad.showAd(this@MainActivity) { rewardItem ->
                        Log.i("MainActivity", "onUserRewarded(" + rewardItem + ")");
                        // 코인/아이템 지급 등 처리
                    }
                }
            }

            @Override
            public void onAdFailedToLoad(int statusCode, String message) {
                Log.e("MainActivity", "onAdFailedToLoad(" + statusCode + ", " + message + ")");
            }
        });
    }

    @Override
    protected void onDestroy() {
        if (rewardAd != null) {
            // 7. 폐기
            rewardAd.destroy();
            rewardAd = null;
        }
        super.onDestroy();
    }
}
```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds
import net.adwhale.sdk.mediation.ads.AdWhaleMediationFullScreenContentCallback
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener
import net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardAd
import net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardedAdLoadCallback
import net.adwhale.sdk.utils.AdWhaleLog

class MainActivity : AppCompatActivity() {

    private var rewardAd: AdWhaleMediationRewardAd? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 1. 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)

        // 2. 초기화 코드
        AdWhaleMediationAds.init(this, object : AdWhaleMediationOnInitCompleteListener {
            override fun onInitComplete(statusCode: Int, message: String) {
                Log.i("MainActivity", "onInitComplete($statusCode, $message)")
                createAndLoadRewardAd()
            }
        })
    }

    private fun createAndLoadRewardAd() {
        // 3. 인스턴스 생성 (placementUid)
        val ad = AdWhaleMediationRewardAd("발급받은 placement uid 값").apply {
            setRegion("서울시 강남구")       // 지역 타게팅 전용 API(옵션)
            setGcoder(37.5, 126.9)          // 지역 타게팅 전용 API(옵션)
            setPlacementName("reward-main")  // 레벨플레이 placement name 연동 전용 API (옵션)

            // 4. 풀스크린 콜백 (표시·닫힘·클릭·표시 실패)
            setAdWhaleMediationFullScreenContentCallback(object : AdWhaleMediationFullScreenContentCallback {
                override fun onAdShowed() {
                    Log.i("MainActivity", "onAdShowed()")
                }

                override fun onAdDismissed() {
                    Log.i("MainActivity", "onAdDismissed()")
                }

                override fun onAdClicked() {
                    Log.i("MainActivity", "onAdClicked()")
                }

                override fun onFailedToShow(statusCode: Int, message: String) {
                    Log.e("MainActivity", "onFailedToShow($statusCode, $message)")
                }
            })
        }

        rewardAd = ad

        // 5. 로드
        ad.loadAd(object : AdWhaleMediationRewardedAdLoadCallback {
            override fun onAdLoaded(ad: AdWhaleMediationRewardAd, message: String) {
                Log.i("MainActivity", "onAdLoaded($message)")
                // 6. 표시 (시청 완료 후 onUserRewarded 호출)
                ad.showAd(this@MainActivity) { rewardItem ->
                    Log.i("MainActivity", "onUserRewarded($rewardItem)")
                    // 코인/아이템 지급 등 처리
                }
            }

            override fun onAdFailedToLoad(statusCode: Int, message: String) {
                Log.e("MainActivity", "onAdFailedToLoad($statusCode, $message)")
            }
        })
    }

    override fun onDestroy() {
        // 7. 폐기
        rewardAd?.destroy()
        rewardAd = null
        super.onDestroy()
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
import androidx.compose.foundation.layout.*
import androidx.compose.material3.Button
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import androidx.compose.ui.platform.LocalContext
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds
import net.adwhale.sdk.mediation.ads.AdWhaleMediationFullScreenContentCallback
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener
import net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardAd
import net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardedAdLoadCallback
import net.adwhale.sdk.utils.AdWhaleLog

class MainActivity : ComponentActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // 1. 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)

        setContent {
            RewardScreen(
                placementUid = "발급받은 placement uid 값",
                placementName = "reward-main",
                region = "서울시 강남구",
                gcoderLat = 37.5,
                gcoderLng = 126.9
            )
        }
    }
}

@Composable
private fun RewardScreen(
    placementUid: String,
    placementName: String? = null,
    region: String? = null,
    gcoderLat: Double? = null,
    gcoderLng: Double? = null
) {
    // init 완료 여부
    var isInited by remember { mutableStateOf(false) }

    // 광고 인스턴스
    var rewardAd by remember { mutableStateOf<AdWhaleMediationRewardAd?>(null) }

    // 2. SDK 초기화: Composable이 최초 구성될 때 한 번
    LaunchedEffect(Unit) {
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

    LaunchedEffect(isInited) {
        if (!isInited) return@LaunchedEffect

        // 3. 인스턴스 생성 (placementUid)
        val ad = AdWhaleMediationRewardAd(placementUid).apply {
            if (region != null) setRegion(region)                               // 지역 타게팅 전용 API(옵션)
            if (gcoderLat != null && gcoderLng != null) setGcoder(gcoderLat, gcoderLng) // 지역 타게팅 전용 API(옵션)
            if (placementName != null) setPlacementName(placementName)         // 레벨플레이 placement name 연동 전용 API(옵션)

            // 4. 풀스크린 콜백 (표시·닫힘·클릭·표시 실패)
            setAdWhaleMediationFullScreenContentCallback(object : AdWhaleMediationFullScreenContentCallback {
                override fun onAdShowed() { Log.i("MainActivity", "onAdShowed()") }
                override fun onAdDismissed() { Log.i("MainActivity", "onAdDismissed()") }
                override fun onAdClicked() { Log.i("MainActivity", "onAdClicked()") }
                override fun onFailedToShow(statusCode: Int, message: String) {
                    Log.e("MainActivity", "onFailedToShow($statusCode, $message)")
                }
            })
        }

        rewardAd = ad

        // 5. 로드
        ad.loadAd(object : AdWhaleMediationRewardedAdLoadCallback {
            override fun onAdLoaded(ad: AdWhaleMediationRewardAd, message: String) {
                Log.i("MainActivity", "onAdLoaded($message)")
                // 6. 표시 (시청 완료 후 onUserRewarded 호출)
                ad.showAd { rewardItem ->
                    Log.i("MainActivity", "onUserRewarded($rewardItem)")
                    // 코인/아이템 지급 등 처리
                }
            }

            override fun onAdFailedToLoad(statusCode: Int, message: String) {
                Log.e("MainActivity", "onAdFailedToLoad($statusCode, $message)")
            }
        })
    }

    // dispose에서 destroy
    DisposableEffect(Unit) {
        onDispose {
            rewardAd?.destroy()
            rewardAd = null
        }
    }

    // 간단 UI (수동 reload)
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(20.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp)
    ) {
        Text(text = if (isInited) "SDK 초기화 완료" else "SDK 초기화 중...")
        Button(
            onClick = {
                rewardAd?.loadAd(object : AdWhaleMediationRewardedAdLoadCallback {
                    override fun onAdLoaded(ad: AdWhaleMediationRewardAd, message: String) {
                        Log.i("MainActivity", "onAdLoaded($message)")
                        ad.showAd(this@MainActivity) { rewardItem ->
                            Log.i("MainActivity", "onUserRewarded($rewardItem)")
                            // 코인/아이템 지급 등 처리
                        }
                    }

                    override fun onAdFailedToLoad(statusCode: Int, message: String) {
                        Log.e("MainActivity", "onAdFailedToLoad($statusCode, $message)")
                    }
                })
            },
            enabled = isInited && rewardAd != null
        ) {
            Text("다시 로드")
        }
    }
}
```
{% endtab %}
{% endtabs %}

#### **6. 주의사항**

**보상 지급 시점**

* `onUserRewarded` 이벤트는 사용자가 광고를 완전히 시청했을 때만 호출됩니다.
* 광고를 중간에 닫으면 보상이 지급되지 않습니다.

**광고 로드 타이밍**

* 광고는 로드가 완료된 후에만 표시할 수 있습니다.
* `onAdLoaded` 이벤트가 발생한 후에 `showAd(activity, listener)`를 호출해야 합니다.

**보상 중복 지급 방지**

* `onUserRewarded` 이벤트는 한 번만 호출됩니다.
* 서버와 동기화하여 중복 지급을 방지하는 것을 권장합니다.

**리소스 해제**

* `onDestroy()`에서 반드시 `destroy()`를 호출하세요.

**에러 처리**

* `onAdFailedToLoad`와 `onFailedToShow` 이벤트에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.

**테스트**

* 개발 환경에서는 테스트용 placement UID를 사용하세요.
* 보상 지급 로직을 충분히 테스트하세요.
