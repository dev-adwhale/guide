# 전면

{% hint style="info" %}
전면 광고는 앱 화면 전체를 덮는 전면형 광고입니다. 게임 레벨 완료, 콘텐츠 전환 등 자연스러운 타이밍에 노출하여 높은 참여도를 얻을 수 있습니다.
{% endhint %}

#### **1. 주요특징**

* 화면 전체를 덮는 전면형 광고
* 사용자 액션 후 자연스러운 타이밍에 노출
* 이벤트 기반 콜백 시스템으로 광고 상태 추적 가능
* 다양한 옵션 설정 지원 (placementName, region, gcoder)

#### 2. 기본 구현 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleMediationInterstitialAd` 클래스를 사용하여 전면 광고를 로드하고 표시하는 기본적인 구현 방법입니다.

{% tabs %}
{% tab title="Java" %}
```java
private AdWhaleMediationInterstitialAd interstitialAd;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    // 1. 인스턴스 생성 (Activity, placementUid)
    interstitialAd = new AdWhaleMediationInterstitialAd(this, "발급받은 placement uid 값");

    // 2. 리스너 등록
    interstitialAd.setAdWhaleMediationInterstitialAdListener(new AdWhaleMediationInterstitialAdListener() {
        @Override public void onAdLoaded() {
            // 로드 성공
            // 4. 표시 (로드 완료 후)
            interstitialAd.showAd(MainActivity.this);
        }

        @Override public void onAdLoadFailed(int statusCode, String message) {
            // 로드 실패
        }

        @Override public void onAdShowFailed(int statusCode, String message) {
            // 표시 실패
        }

        @Override public void onAdClicked() {
            // 클릭
        }

        @Override public void onAdClosed() {
            // 닫힘
        }

        @Override public void onAdShowed() {
            // 표시됨
        }
    });

    // 3. 로드
    interstitialAd.loadAd();
}

@Override
protected void onDestroy() {
    if (interstitialAd != null) {
        // 5. 폐기
        interstitialAd.destroy();
        interstitialAd = null;
    }
    super.onDestroy();
}
```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
class MainActivity : AppCompatActivity() {

    private var interstitialAd: AdWhaleMediationInterstitialAd? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 1. 인스턴스 생성 (Activity, placementUid)
        interstitialAd = AdWhaleMediationInterstitialAd(
            this,
            "발급받은 placement uid 값"
        )

        // 2. 리스너 등록
        interstitialAd?.setAdWhaleMediationInterstitialAdListener(
            object : AdWhaleMediationInterstitialAdListener {

                override fun onAdLoaded() {
                    // 로드 성공
                    interstitialAd?.showAd(this@MainActivity)
                }

                override fun onAdLoadFailed(statusCode: Int, message: String?) {
                    // 로드 실패
                }

                override fun onAdShowFailed(statusCode: Int, message: String?) {
                    // 표시 실패
                }

                override fun onAdClicked() {
                    // 클릭
                }

                override fun onAdClosed() {
                    // 닫힘
                }

                override fun onAdShowed() {
                    // 표시됨
                }
            }
        )

        // 로드
        interstitialAd?.loadAd()
    }

    override fun onDestroy() {
        interstitialAd?.destroy()
        interstitialAd = null
        super.onDestroy()
    }
}
```
{% endtab %}

{% tab title="Compose" %}
```kotlin
@Composable
private fun InterstitialScreen(
    placementUid: String,
    placementName: String? = null,
    region: String? = null,
    gcoderLat: Double? = null,
    gcoderLng: Double? = null
) {
    // init 완료 여부
    var isInited by remember { mutableStateOf(false) }

    // 광고 인스턴스
    var interstitialAd by remember { mutableStateOf<AdWhaleMediationInterstitialAd?>(null) }

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

    val activity = LocalContext.current as? ComponentActivity

    LaunchedEffect(isInited) {
        if (!isInited || activity == null) return@LaunchedEffect

        // 3. 인스턴스 생성 (placementUid)
        val ad = AdWhaleMediationInterstitialAd(placementUid).apply {
            if (region != null) setRegion(region)                                         // 지역 타게팅 전용 API(옵션)
            if (gcoderLat != null && gcoderLng != null) setGcoder(gcoderLat, gcoderLng)   // 지역 타게팅 전용 API(옵션)
            if (placementName != null) setPlacementName(placementName)                   // 레벨플레이 placement name 연동 전용 API (옵션)

            // 4. 리스너 등록
            setAdWhaleMediationInterstitialAdListener(object : AdWhaleMediationInterstitialAdListener {
                override fun onAdLoaded() {
                    Log.i("MainActivity", "onAdLoaded()")
                    // 6. 표시 (로드 완료 후)
                    showAd(activity)
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

                override fun onAdClosed() {
                    Log.i("MainActivity", "onAdClosed()")
                }

                override fun onAdClicked() {
                    Log.i("MainActivity", "onAdClicked()")
                }
            })
        }

        interstitialAd = ad

        // 5. 로드
        ad.loadAd()
    }

    // dispose에서 destroy
    DisposableEffect(Unit) {
        onDispose {
            // 7. 폐기
            interstitialAd?.destroy()
            interstitialAd = null
        }
    }

    // 간단 UI (수동 show 버튼)
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(20.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp)
    ) {
        Text(text = if (isInited) "SDK 초기화 완료" else "SDK 초기화 중...")
        Button(
            onClick = { activity?.let { interstitialAd?.showAd(it) } },
            enabled = isInited && activity != null && interstitialAd != null
        ) {
            Text("InterstitialAd 표시")
        }
        Button(
            onClick = { interstitialAd?.loadAd() },
            enabled = isInited && interstitialAd != null
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
**AdWhaleMediationInterstitialAd 클래스 API 설명**

```java
public AdWhaleMediationInterstitialAd(Activity activity, String placementUid)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```java
public void setAdWhaleMediationInterstitialAdListener(AdWhaleMediationInterstitialAdListener listener)
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationInterstitialAdListener</p></td><td>전면 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```java
public void loadAd() // 미디에이션 전면 광고로드
```

```java
public void showAd(Activity activity) // 미디에이션 전면 광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr></tbody></table>

```java
public void destroy() // onDestroy() 시 호출 혹은 더 이상 광고를 요청하지 않고 싶을 때 호출
```

\
**AdWhaleMediationInterstitialAdListener 클래스 API 설명**

```java
public void onAdLoaded() // 미디에이션 전면 광고요청 성공 시
```

```java
public void onAdLoadFailed(int statusCode, String message) // 미디에이션 전면 광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```java
public void onAdShowed() // 미디에이션 전면 광고표시 후
```

```java
public void onAdShowFailed(int statusCode, String message) // 미디에이션 전면 광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고표시 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```java
public void onAdClosed() // 미디에이션 전면 광고닫기 시
```

```java
public void onAdClicked() // 미디에이션 전면 광고클릭 시
```
{% endtab %}

{% tab title="Kotlin" %}
**AdWhaleMediationInterstitialAd 클래스 API 설명**

```kotlin
AdWhaleMediationInterstitialAd(activity : Activity, placementUid : String)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```kotlin
fun setAdWhaleMediationInterstitialAdListener(listener : AdWhaleMediationInterstitialAdListener) : Unit
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAdListener</td><td>전면 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```kotlin
fun loadAd() : Unit // 미디에이션 전면 광고로드
```

```kotlin
fun showAd(activity : Activity) : Unit // 미디에이션 전면 광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr></tbody></table>

```kotlin
fun destroy() : Unit // onDestroy() 시 호출 혹은 더 이상 광고를 요청하지 않고 싶을 때 호출
```

\
**AdWhaleMediationInterstitialAdListener 클래스 API 설명**

```kotlin
fun onAdLoaded() : Unit // 미디에이션 전면 광고요청 성공 시
```

```kotlin
fun onAdLoadFailed(statusCode : Int, message : String) : Unit // 미디에이션 전면 광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdShowed() : Unit // 미디에이션 전면 광고표시 후
```

```kotlin
fun onAdShowFailed(statusCode : Unit, message : String) : Unit // 미디에이션 전면 광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고표시 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdClosed() : Unit // 미디에이션 전면 광고닫기 시
```

```kotlin
fun onAdClicked() : Unit // 미디에이션 전면 광고클릭 시
```
{% endtab %}

{% tab title="Compose" %}


**AdWhaleMediationInterstitialAd 클래스 API 설명**

```kotlin
AdWhaleMediationInterstitialAd(activity : Activity, placementUid : String)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```kotlin
fun setAdWhaleMediationInterstitialAdListener(listener : AdWhaleMediationInterstitialAdListener) : Unit
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationInterstitialAdListener</p></td><td>전면 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```kotlin
fun loadAd() : Unit // 미디에이션 전면 광고로드
```

```kotlin
fun showAd(activity : Activity) : Unit // 미디에이션 전면 광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr></tbody></table>

```kotlin
fun destroy() : Unit // onDestroy() 시 호출 혹은 더 이상 광고를 요청하지 않고 싶을 때 호출
```

\
**AdWhaleMediationInterstitialAdListener 클래스 API 설명**

```kotlin
fun onAdLoaded() : Unit // 미디에이션 전면 광고요청 성공 시
```

```kotlin
fun onAdLoadFailed(statusCode : Int, message : String) : Unit // 미디에이션 전면 광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdShowed() : Unit // 미디에이션 전면 광고표시 후
```

```kotlin
fun onAdShowFailed(statusCode : Unit, message : String) : Unit // 미디에이션 전면 광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고표시 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdClosed() : Unit // 미디에이션 전면 광고닫기 시
```

```kotlin
fun onAdClicked() : Unit // 미디에이션 전면 광고클릭 시
```
{% endtab %}
{% endtabs %}

#### 4. 옵션 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

{% tabs %}
{% tab title="Java" %}
```java
interstitialAd.setRegion("서울시 강남구"); // 지역 타게팅 전용 API(옵션)
interstitialAd.setGcoder(37.5665, 126.9780); // 지역 타게팅 전용 API(옵션)
interstitialAd.setPlacementName("interstitial_main"); // 레벨플레이 placement name 연동 전용 API (옵션)
```

{% hint style="info" %}
ADwhale 에서는 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
interstitialAd.setRegion("서울시 강남구") // 지역 타게팅 전용 API(옵션)
interstitialAd.setGcoder(37.5665, 126.9780) // 지역 타게팅 전용 API(옵션)
interstitialAd.setPlacementName("interstitial_main") // 레벨플레이 placement name 연동 전용 API (옵션)
```

{% hint style="info" %}
ADwhale 에서는 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}
{% endtab %}

{% tab title="Compose" %}
```kotlin
interstitialAd.setRegion("서울시 강남구") // 지역 타게팅 전용 API(옵션)
interstitialAd.setGcoder(37.5665, 126.9780) // 지역 타게팅 전용 API(옵션)
interstitialAd.setPlacementName("interstitial_main") // 레벨플레이 placement name 연동 전용 API (옵션)
```

{% hint style="info" %}
ADwhale 에서는 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}
{% endtab %}
{% endtabs %}

#### 5. 전면 광고 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

다음은 안드로이드에서 전면 광고를 구현하는 완전한 예시입니다.

{% tabs %}
{% tab title="Java" %}
```java
import android.os.Bundle;
import android.util.Log;
import androidx.appcompat.app.AppCompatActivity;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAd;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAdListener;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener;
import net.adwhale.sdk.utils.AdWhaleLog;

public class MainActivity extends AppCompatActivity {

    private AdWhaleMediationInterstitialAd interstitialAd;

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
                createAndLoadInterstitialAd();
            }
        });
    }

    private void createAndLoadInterstitialAd() {
        // 3. 인스턴스 생성 (placementUid)
        interstitialAd = new AdWhaleMediationInterstitialAd("발급받은 placement uid 값");
        interstitialAd.setRegion("서울시 서초구"); // 지역 타게팅 전용 API(옵션)
        interstitialAd.setGcoder(37.49, 127.02); // 지역 타게팅 전용 API(옵션)
        interstitialAd.setPlacementName("interstitial-main"); // 레벨플레이 placement name 연동 전용 API (옵션)

        // 4. 리스너 등록
        interstitialAd.setAdWhaleMediationInterstitialAdListener(new AdWhaleMediationInterstitialAdListener() {
            @Override
            public void onAdLoaded() {
                Log.i("MainActivity", "onAdLoaded()");
                // 원하는 시점에 표시 (예: 사용자 액션 후)
                interstitialAd.showAd(MainActivity.this);
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
            public void onAdClosed() {
                Log.i("MainActivity", "onAdClosed()");
            }

            @Override
            public void onAdClicked() {
                Log.i("MainActivity", "onAdClicked()");
            }
        });

        // 5. 로드
        interstitialAd.loadAd();
    }

    /** 표시 버튼 등으로 수동 표시할 경우 */
    private void showInterstitialAd() {
        if (interstitialAd != null) {
            interstitialAd.showAd(this);
        }
    }

    @Override
    protected void onDestroy() {
        if (interstitialAd != null) {
            // 6. 폐기
            interstitialAd.destroy();
            interstitialAd = null;
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
import net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAd
import net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAdListener
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener
import net.adwhale.sdk.utils.AdWhaleLog

class MainActivity : AppCompatActivity() {

    private var interstitialAd: AdWhaleMediationInterstitialAd? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 1. 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)

        // 2. 초기화 코드
        AdWhaleMediationAds.init(this, object : AdWhaleMediationOnInitCompleteListener {
            override fun onInitComplete(statusCode: Int, message: String) {
                Log.i("MainActivity", "onInitComplete($statusCode, $message)")
                createAndLoadInterstitialAd()
            }
        })
    }

    private fun createAndLoadInterstitialAd() {
        // 3. 인스턴스 생성 (placementUid)
        val ad = AdWhaleMediationInterstitialAd("발급받은 placement uid 값").apply {
            setRegion("서울시 서초구")          // 지역 타게팅 전용 API(옵션)
            setGcoder(37.49, 127.02)           // 지역 타게팅 전용 API(옵션)
            setPlacementName("interstitial-main") // 레벨플레이 placement name 연동 전용 API (옵션)

            // 4. 리스너 등록
            setAdWhaleMediationInterstitialAdListener(object : AdWhaleMediationInterstitialAdListener {
                override fun onAdLoaded() {
                    Log.i("MainActivity", "onAdLoaded()")
                    // 원하는 시점에 표시 (예: 사용자 액션 후)
                    showAd(this@MainActivity)
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

                override fun onAdClosed() {
                    Log.i("MainActivity", "onAdClosed()")
                }

                override fun onAdClicked() {
                    Log.i("MainActivity", "onAdClicked()")
                }
            })
        }

        interstitialAd = ad

        // 5. 로드
        ad.loadAd()
    }

    /** 표시 버튼 등으로 수동 표시할 경우 */
    private fun showInterstitialAd() {
        interstitialAd?.showAd(this)
    }

    override fun onDestroy() {
        // 6. 폐기
        interstitialAd?.destroy()
        interstitialAd = null
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
import net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAd
import net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAdListener
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener
import net.adwhale.sdk.utils.AdWhaleLog

class MainActivity : ComponentActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // 1. 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)

        setContent {
            InterstitialScreen(
                placementUid = "발급받은 placement uid 값",
                placementName = "interstitial-main",
                region = "서울시 서초구",
                gcoderLat = 37.49,
                gcoderLng = 127.02
            )
        }
    }
}

@Composable
private fun InterstitialScreen(
    placementUid: String,
    placementName: String? = null,
    region: String? = null,
    gcoderLat: Double? = null,
    gcoderLng: Double? = null
) {
    // init 완료 여부
    var isInited by remember { mutableStateOf(false) }

    // 광고 인스턴스
    var interstitialAd by remember { mutableStateOf<AdWhaleMediationInterstitialAd?>(null) }

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

    val activity = LocalContext.current as? ComponentActivity

    LaunchedEffect(isInited) {
        if (!isInited || activity == null) return@LaunchedEffect

        // 3. 인스턴스 생성 (placementUid)
        val ad = AdWhaleMediationInterstitialAd(placementUid).apply {
            if (region != null) setRegion(region)                                         // 지역 타게팅 전용 API(옵션)
            if (gcoderLat != null && gcoderLng != null) setGcoder(gcoderLat, gcoderLng)   // 지역 타게팅 전용 API(옵션)
            if (placementName != null) setPlacementName(placementName)                   // 레벨플레이 placement name 연동 전용 API (옵션)

            // 4. 리스너 등록
            setAdWhaleMediationInterstitialAdListener(object : AdWhaleMediationInterstitialAdListener {
                override fun onAdLoaded() {
                    Log.i("MainActivity", "onAdLoaded()")
                    // 6. 표시 (로드 완료 후)
                    showAd(activity)
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

                override fun onAdClosed() {
                    Log.i("MainActivity", "onAdClosed()")
                }

                override fun onAdClicked() {
                    Log.i("MainActivity", "onAdClicked()")
                }
            })
        }

        interstitialAd = ad

        // 5. 로드
        ad.loadAd()
    }

    // dispose에서 destroy
    DisposableEffect(Unit) {
        onDispose {
            // 7. 폐기
            interstitialAd?.destroy()
            interstitialAd = null
        }
    }

    // 간단 UI (수동 show 버튼)
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(20.dp),
        verticalArrangement = Arrangement.spacedBy(12.dp)
    ) {
        Text(text = if (isInited) "SDK 초기화 완료" else "SDK 초기화 중...")
        Button(
            onClick = { activity?.let { interstitialAd?.showAd(it) } },
            enabled = isInited && activity != null && interstitialAd != null
        ) {
            Text("InterstitialAd 표시")
        }
        Button(
            onClick = { interstitialAd?.loadAd() },
            enabled = isInited && interstitialAd != null
        ) {
            Text("다시 로드")
        }
    }
}
```
{% endtab %}
{% endtabs %}

#### **6. 주의사항**

**광고 로드 타이밍**

* 광고는 로드가 완료된 후에만 표시할 수 있습니다.
* `onAdLoaded()` 이후에만 `showAd(activity)`를 호출하세요.

**광고 표시 조건**

* 레벨 완료, 화면 전환 등 사용자 액션 이후 자연스러운 타이밍에 노출하는 것을 권장합니다. 과도한 노출은 이용 경험을 해칠 수 있습니다.
* 중복 표시: 이미 표시 중이거나 로드 중일 때 `showAd(activity)`는 무시될 수 있으므로, 콜백 상태를 활용해 제어하세요.

**리스너 정리**

* 컴포넌트가 언마운트될 때 등록한 이벤트 리스너를 반드시 제거해야 합니다.

**리소스 해제**

* `onDestroy()`에서 반드시 `destroy()`를 호출하세요.

**에러 처리**

* `onAdLoadFailed`와 `onAdShowFailed` 이벤트에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.

**테스트**

* 개발 환경에서는 테스트용 placement UID를 사용하세요.
* 실제 배포 전에 다양한 시나리오에서 테스트하세요.
