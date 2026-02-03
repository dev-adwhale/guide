# 앱 오프닝 광고

{% hint style="info" %}
앱오프닝 광고는 앱이 실행될 때 또는 백그라운드에서 포그라운드로 전환될 때 표시되는 전면 광고입니다. 이 문서는 안드로이드 프로젝트에서 AdWhale Mediation SDK를 사용하여 앱오프닝 광고를 연동하는 방법을 설명합니다.
{% endhint %}

#### **1. 주요특징**

* 앱 실행 시 또는 포그라운드 전환 시 자동 표시 가능
* 화면 전체를 덮는 전면형 광고
* 다양한 옵션 설정 지원 (placementName, region, gcoder)
* 로드, 표시, 닫힘 등 이벤트 기반 콜백 시스템으로 광고 상태 추적 가능

#### 2. 기본 구현 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleMediationAppOpenAd` 클래스를 사용하여 앱 오프닝 광고를 로드하고 표시하는 기본적인 구현 방법입니다.

{% tabs %}
{% tab title="Java" %}
```java
private AdWhaleMediationAppOpenAd appOpenAd;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    // 1. 인스턴스 생성 (Activity, placementUid)
    appOpenAd = new AdWhaleMediationAppOpenAd(this, "발급받은 placement uid 값");

    // 2. 리스너 등록
    appOpenAd.setAdWhaleMediationAppOpenAdListener(new AdWhaleMediationAppOpenAdListener() {
        @Override public void onAdLoaded() {
            // 로드 성공
            // 4. 표시 (로드 완료 후)
            appOpenAd.showAd(MainActivity.this);
        }

        @Override public void onAdFailedToLoad(int statusCode, String message) {
            // 로드 실패
        }

        @Override public void onAdFailedToShow(int statusCode, String message) {
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

    // 3. 로드
    appOpenAd.loadAd();
}

@Override
protected void onDestroy() {
    if (appOpenAd != null) {
        // 5. 폐기
        appOpenAd.destroy();
        appOpenAd = null;
    }
    super.onDestroy();
}
```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
private var appOpenAd: AdWhaleMediationAppOpenAd? = null

override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    // 1. 인스턴스 생성 (Activity, placementUid)
    appOpenAd = AdWhaleMediationAppOpenAd(this, "발급받은 placement uid 값")

    // 2. 리스너 등록
    appOpenAd?.setAdWhaleMediationAppOpenAdListener(
        object : AdWhaleMediationAppOpenAdListener {

            override fun onAdLoaded() {
                // 로드 성공
                // 4. 표시 (로드 완료 후)
                appOpenAd?.showAd(this@MainActivity)
            }

            override fun onAdFailedToLoad(statusCode: Int, message: String?) {
                // 로드 실패
            }

            override fun onAdFailedToShow(statusCode: Int, message: String?) {
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
    appOpenAd?.loadAd()
}

override fun onDestroy() {
    // 5. 폐기
    appOpenAd?.destroy()
    appOpenAd = null
    super.onDestroy()
}
```
{% endtab %}

{% tab title="Compose" %}
```kotlin
@Composable
fun AppOpenAdSample() {
    val context = LocalContext.current
    val activity = context as Activity

    // recomposition 방지
    val appOpenAd = remember {
        // 1. 인스턴스 생성 (Activity, placementUid)
        AdWhaleMediationAppOpenAd(activity, "발급받은 placement uid 값")
    }

    DisposableEffect(Unit) {
        // 2. 리스너 등록
        appOpenAd.setAdWhaleMediationAppOpenAdListener(
            object : AdWhaleMediationAppOpenAdListener {

                override fun onAdLoaded() {
                    // 로드 성공
                    // 4. 표시 (로드 완료 후)
                    appOpenAd.showAd(activity)
                }

                override fun onAdFailedToLoad(statusCode: Int, message: String?) {
                    // 로드 실패
                }

                override fun onAdFailedToShow(statusCode: Int, message: String?) {
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
        appOpenAd.loadAd()

        onDispose {
            // 5. 폐기
            appOpenAd?.destroy() 
        }
    }
}

```
{% endtab %}
{% endtabs %}

#### 3. API 설명 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

{% tabs %}
{% tab title="Java" %}
**AdWhaleMediationAppOpenAd 클래스 API 설명**

```java
public AdWhaleMediationAppOpenAd(Activity activity, String placementUid)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```java
public void setAdWhaleMediationAppOpenAdListener(AdWhaleMediationAppOpenAdListener listener)
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationAppOpenAdListener</p></td><td>앱 오프닝 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```java
public void loadAd() // 미디에이션 앱 오프닝 광고로드
```

```java
public void showAd(Activity activity) // 미디에이션 앱 오프닝 광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr></tbody></table>

```java
public void destroy() // onDestroy() 시 호출 혹은 더 이상 광고를 요청하지 않고 싶을 때 호출
```

\
**AdWhaleMediationAppOpenAdListener 클래스 API 설명**

```java
public void onAdLoaded() // 미디에이션 앱 오프닝 광고요청 성공 시
```

```java
public void onAdLoadFailed(int statusCode, String message) // 미디에이션 앱 오프닝 광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```java
public void onAdShowed() // 미디에이션 앱 오프닝 광고표시 후
```

```java
public void onAdShowFailed(int statusCode, String message) // 미디에이션 앱 오프닝 광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고표시 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```java
public void onAdDismissed() // 미디에이션 앱 오프닝 광고닫기 시
```

```java
public void onAdClicked() // 미디에이션 앱 오프닝 광고클릭 시
```
{% endtab %}

{% tab title="Kotlin" %}
**AdWhaleMediationAppOpenAd 클래스 API 설명**

```kotlin
AdWhaleMediationAppOpenAd(activity : Activity, placementUid : String)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```kotlin
fun setAdWhaleMediationAppOpenAdListener(listener : AdWhaleMediationAppOpenAdListener) : Unit
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationAppOpenAdListener</p></td><td>앱 오프닝 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```kotlin
fun loadAd() : Unit // 미디에이션 앱 오프닝 광고로드
```

```kotlin
fun showAd(activity : Activity) : Unit // 미디에이션 앱 오프닝 광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr></tbody></table>

```kotlin
fun destroy() : Unit // onDestroy() 시 호출 혹은 더 이상 광고를 요청하지 않고 싶을 때 호출
```

\
**AdWhaleMediationAppOpenAdListener 클래스 API 설명**

```kotlin
fun onAdLoaded() : Unit // 미디에이션 앱 오프닝 광고요청 성공 시
```

```kotlin
fun onAdLoadFailed(statusCode : Int, message : String) : Unit // 미디에이션 앱 오프닝 광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdShowed() : Unit // 미디에이션 앱 오프닝 광고표시 후
```

```kotlin
fun onAdShowFailed(statusCode : Unit, message : String) : Unit // 미디에이션 앱 오프닝 광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고표시 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdDismissed() : Unit // 미디에이션 앱 오프닝 광고닫기 시
```

```kotlin
fun onAdClicked() : Unit // 미디에이션 앱 오프닝 광고클릭 시
```
{% endtab %}

{% tab title="Compose" %}


**AdWhaleMediationAppOpenAd 클래스 API 설명**

```kotlin
AdWhaleMediationAppOpenAd(activity : Activity, placementUid : String)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```kotlin
fun setAdWhaleMediationAppOpenAdListener(listener : AdWhaleMediationAppOpenAdListener) : Unit
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationAppOpenAdListener</p></td><td>앱 오프닝 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```kotlin
fun loadAd() : Unit // 미디에이션 앱 오프닝 광고로드
```

```kotlin
fun showAd(activity : Activity) : Unit // 미디에이션 앱 오프닝 광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr></tbody></table>

```kotlin
fun destroy() : Unit // onDestroy() 시 호출 혹은 더 이상 광고를 요청하지 않고 싶을 때 호출
```

\
**AdWhaleMediationAppOpenAdListener 클래스 API 설명**

```kotlin
fun onAdLoaded() : Unit // 미디에이션 앱 오프닝 광고요청 성공 시
```

```kotlin
fun onAdLoadFailed(statusCode : Int, message : String) : Unit // 미디에이션 앱 오프닝 광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdShowed() : Unit // 미디에이션 앱 오프닝 광고표시 후
```

```kotlin
fun onAdShowFailed(statusCode : Unit, message : String) : Unit // 미디에이션 앱 오프닝 광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고표시 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdDismissed() : Unit // 미디에이션 앱 오프닝 광고닫기 시
```

```kotlin
fun onAdClicked() : Unit // 미디에이션 앱 오프닝 광고클릭 시
```
{% endtab %}
{% endtabs %}

#### 4. 옵션 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

{% tabs %}
{% tab title="Java" %}
```java
appOpenAd.setRegion("서울시 강남구"); // 지역 타게팅 전용 API(옵션)
appOpenAd.setGcoder(37.5665, 126.9780); // 지역 타게팅 전용 API(옵션)
appOpenAd.setPlacementName("app_open_main"); // 레벨플레이 placement name 연동 전용 API (옵션)
```

{% hint style="info" %}
ADwhale 에서는 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
appOpenAd.setRegion("서울시 강남구") // 지역 타게팅 전용 API(옵션)
appOpenAd.setGcoder(37.5665, 126.9780) // 지역 타게팅 전용 API(옵션)
appOpenAd.setPlacementName("app_open_main") // 레벨플레이 placement name 연동 전용 API (옵션)
```

{% hint style="info" %}
ADwhale 에서는 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}
{% endtab %}

{% tab title="Compose" %}
```kotlin
appOpenAd.setRegion("서울시 강남구") // 지역 타게팅 전용 API(옵션)
appOpenAd.setGcoder(37.5665, 126.9780) // 지역 타게팅 전용 API(옵션)
appOpenAd.setPlacementName("app_open_main") // 레벨플레이 placement name 연동 전용 API (옵션)
```

{% hint style="info" %}
ADwhale 에서는 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}
{% endtab %}
{% endtabs %}

#### 5. 앱 오프닝 광고 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

다음은 안드로이드에서 앱오프닝 광고를 구현하는 완전한 예시입니다.

{% tabs %}
{% tab title="Java" %}
```java
import android.os.Bundle;
import android.util.Log;
import androidx.appcompat.app.AppCompatActivity;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAppOpenAd;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAppOpenAdListener;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener;
import net.adwhale.sdk.utils.AdWhaleLog;

public class MainActivity extends AppCompatActivity {

    private AdWhaleMediationAppOpenAd appOpenAd;

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
                createAndLoadAppOpenAd();
            }
        });
    }

    private void createAndLoadAppOpenAd() {
        // 3. 인스턴스 생성 (Activity, placementUid)
        appOpenAd = new AdWhaleMediationAppOpenAd(this, "발급받은 placement uid 값");
        appOpenAd.setRegion("서울시 강남구"); // 지역 타게팅 전용 API(옵션)
        appOpenAd.setGcoder(37.5665, 126.9780); // 지역 타게팅 전용 API(옵션)
        appOpenAd.setPlacementName("app_open_main"); // 레벨플레이 placement name 연동 전용 API (옵션)

        // 4. 리스너 등록
        appOpenAd.setAdWhaleMediationAppOpenAdListener(new AdWhaleMediationAppOpenAdListener() {
            @Override
            public void onAdLoaded() {
                Log.i("MainActivity", "onAdLoaded()");
                // 원하는 시점에 표시 (예: 앱 첫 화면 로드 후)
                // 6. 표시 (로드 완료 후)
                appOpenAd.showAd(MainActivity.this);
            }

            @Override
            public void onAdFailedToLoad(int statusCode, String message) {
                Log.e("MainActivity", "onAdFailedToLoad(" + statusCode + ", " + message + ")");
            }

            @Override
            public void onAdFailedToShow(int statusCode, String message) {
                Log.e("MainActivity", "onAdFailedToShow(" + statusCode + ", " + message + ")");
            }

            @Override
            public void onAdClicked() {
                Log.i("MainActivity", "onAdClicked()");
            }

            @Override
            public void onAdDismissed() {
                Log.i("MainActivity", "onAdDismissed()");
            }

            @Override
            public void onAdShowed() {
                Log.i("MainActivity", "onAdShowed()");
            }
        });

        // 5. 로드
        appOpenAd.loadAd();
    }

    /** 표시 버튼 등으로 수동 표시할 경우 */
    private void showAppOpenAd() {
        if (appOpenAd != null) {
            appOpenAd.showAd(this);
        }
    }

    @Override
    protected void onDestroy() {
        if (appOpenAd != null) {
            // 7. 폐기
            appOpenAd.destroy();
            appOpenAd = null;
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
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAppOpenAd
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAppOpenAdListener
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener
import net.adwhale.sdk.utils.AdWhaleLog

class MainActivity : AppCompatActivity() {

    private var appOpenAd: AdWhaleMediationAppOpenAd? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 1. 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)

        // 2. 초기화 코드
        AdWhaleMediationAds.init(this, object : AdWhaleMediationOnInitCompleteListener {
            override fun onInitComplete(statusCode: Int, message: String) {
                Log.i("MainActivity", "onInitComplete($statusCode, $message)")
                createAndLoadAppOpenAd()
            }
        })
    }

    private fun createAndLoadAppOpenAd() {
        // 3. 인스턴스 생성 (Activity, placementUid)
        val ad = AdWhaleMediationAppOpenAd(this, "발급받은 placement uid 값").apply {
            setRegion("서울시 강남구")            // 지역 타게팅 전용 API(옵션)
            setGcoder(37.5665, 126.9780)       // 지역 타게팅 전용 API(옵션)
            setPlacementName("app_open_main")  // 레벨플레이 placement name 연동 전용 API (옵션)
        
            // 4. 리스너 등록
            setAdWhaleMediationAppOpenAdListener(object : AdWhaleMediationAppOpenAdListener {
                override fun onAdLoaded() {
                    Log.i("MainActivity", "onAdLoaded()")
                    // 6. 표시 (로드 완료 후)
                    showAd(this@MainActivity)
                }

                override fun onAdFailedToLoad(statusCode: Int, message: String) {
                    Log.e("MainActivity", "onAdFailedToLoad($statusCode, $message)")
                }

                override fun onAdFailedToShow(statusCode: Int, message: String) {
                    Log.e("MainActivity", "onAdFailedToShow($statusCode, $message)")
                }

                override fun onAdClicked() {
                    Log.i("MainActivity", "onAdClicked()")
                }

                override fun onAdDismissed() {
                    Log.i("MainActivity", "onAdDismissed()")
                }

                override fun onAdShowed() {
                    Log.i("MainActivity", "onAdShowed()")
                }
            })
        }

        appOpenAd = ad

        // 5. 로드
        ad.loadAd()
    }

    /** 표시 버튼 등으로 수동 표시할 경우 */
    private fun showAppOpenAd() {
        appOpenAd?.showAd(this)
    }

    override fun onDestroy() {
        // 7. 폐기
        appOpenAd?.destroy()
        appOpenAd = null
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
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAppOpenAd
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAppOpenAdListener
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener
import net.adwhale.sdk.utils.AdWhaleLog

class MainActivity : ComponentActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // 1. 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)

        setContent {
            AppOpenScreen(
                placementUid = "발급받은 placement uid 값",
                placementName = "app_open_main",
                region = "서울시 강남구",
                gcoderLat = 37.5665,
                gcoderLng = 126.9780
            )
        }
    }
}

@Composable
private fun AppOpenScreen(
    placementUid: String,
    placementName: String? = null,
    region: String? = null,
    gcoderLat: Double? = null,
    gcoderLng: Double? = null
) {
    // init 완료 여부
    var isInited by remember { mutableStateOf(false) }

    // 광고 인스턴스
    var appOpenAd by remember { mutableStateOf<AdWhaleMediationAppOpenAd?>(null) }

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

        // 3. 인스턴스 생성 (Activity, placementUid)
        val ad = AdWhaleMediationAppOpenAd(activity, placementUid).apply {
            if (region != null) setRegion(region)                                         // 지역 타게팅 전용 API(옵션)
            if (gcoderLat != null && gcoderLng != null) setGcoder(gcoderLat, gcoderLng)   // 지역 타게팅 전용 API(옵션)
            if (placementName != null) setPlacementName(placementName)                    // 레벨플레이 placement name 연동 전용 API (옵션)

            // 4. 리스너 등록
            setAdWhaleMediationAppOpenAdListener(object : AdWhaleMediationAppOpenAdListener {
                override fun onAdLoaded() {
                    Log.i("MainActivity", "onAdLoaded()")
                    // 6. 표시 (로드 완료 후)
                    showAd(activity)
                }

                override fun onAdFailedToLoad(statusCode: Int, message: String) {
                    Log.e("MainActivity", "onAdFailedToLoad($statusCode, $message)")
                }

                override fun onAdFailedToShow(statusCode: Int, message: String) {
                    Log.e("MainActivity", "onAdFailedToShow($statusCode, $message)")
                }

                override fun onAdClicked() {
                    Log.i("MainActivity", "onAdClicked()")
                }

                override fun onAdDismissed() {
                    Log.i("MainActivity", "onAdDismissed()")
                }

                override fun onAdShowed() {
                    Log.i("MainActivity", "onAdShowed()")
                }
            })
        }

        appOpenAd = ad
        
        // 5. 로드
        ad.loadAd()
    }

    // dispose에서 destroy
    DisposableEffect(Unit) {
        onDispose {
            // 7. 폐기
            appOpenAd?.destroy()
            appOpenAd = null
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
            onClick = { activity?.let { appOpenAd?.showAd(it) } },
            enabled = isInited && activity != null && appOpenAd != null
        ) {
            Text("AppOpenAd 표시")
        }
        Button(
            onClick = { appOpenAd?.loadAd() },
            enabled = isInited && appOpenAd != null
        ) {
            Text("다시 로드")
        }
    }
}

```
{% endtab %}
{% endtabs %}

#### 5. 앱 시작 시 (및 포어그라운드 진입시) 자동 표시 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

{% hint style="info" %}
**다음 샘플코드는 앱 상태가 백그라운드에서 포그라운드로 변경될 때 광고를 노출합니다.**

* 앱오픈광고 요청하기 위해 AdWhaleMediationAppOpenAd생성자를 호출합니다.
* &#x20;AdWhaleMediationAppOpenAd가 Application의 Foreground 상태에 진입하는 것을 감지하면 광고를 요청합니다.
* 더 이상 광고를 요청하지 않으려면 AdWhaleMediationAppOpenAd의 Destroy를 호출합니다.&#x20;
{% endhint %}

먼저 백그라운드/포어그라운드 감지를 위한 라이프사이클 등록이 필요합니다.

라이프사이클 등록/해제에 필요한 의존성을 app 레벨의 build.gradle에 추가 후 sync 합니다.

```
    // Lifecycle Process (ProcessLifecycleOwner 사용을 위해 필요)
    implementation 'androidx.lifecycle:lifecycle-process:2.6.2'
    implementation 'androidx.lifecycle:lifecycle-runtime:2.6.2'
```

{% tabs %}
{% tab title="Java" %}
```java
import android.os.Bundle;
import android.util.Log;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import androidx.lifecycle.DefaultLifecycleObserver;
import androidx.lifecycle.LifecycleOwner;
import androidx.lifecycle.ProcessLifecycleOwner;

import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAppOpenAd;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAppOpenAdListener;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener;
import net.adwhale.sdk.utils.AdWhaleLog;

public class MainActivity extends AppCompatActivity implements AdWhaleMediationAppOpenAdListener {

    private static final String TAG = "AppOpenLifecycleTest";
    
    // 라이프사이클 관리 (사용자가 직접 처리)
    private DefaultLifecycleObserver appLifecycleObserver;
    
    // 백그라운드/포어그라운드 상태체크 플래그값 (사용자가 직접 처리)
    private boolean isAppInBackground = false;

    private AdWhaleMediationAppOpenAd adWhaleMediationAppOpenAd;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 앱 라이프사이클 관리 Observer 등록: 백그라운드/포어그라운드 감지를 위해 등록 필요
        appLifecycleObserver = new DefaultLifecycleObserver() {
            @Override
            public void onStart(@NonNull LifecycleOwner owner) {
                Log.d(TAG, "App moved to foreground");
                
                // 사용자가 직접 처리: 포어그라운드로 전환 시 광고 표시
                // 필요에 따라 조건 체크 (예: 백그라운드 시간, 광고 간격 등)를 추가할 수 있음
                if (adWhaleMediationAppOpenAd != null && isAppInBackground) {
                    if (adWhaleMediationAppOpenAd != null) {
                        adWhaleMediationAppOpenAd.showAd(MainActivity.this);
                    }
                }
                // 사용자가 직접 처리: 백그라운드/포어그라운드 상태체크 플래그값 업데이트
                isAppInBackground = false;
            }

            @Override
            public void onStop(@NonNull LifecycleOwner owner) {
                Log.d(TAG, "App moved to background");
                
                // 사용자가 직접 처리: 백그라운드/포어그라운드 상태체크 플래그값 업데이트
                isAppInBackground = true;
            }
        };
        
       // ProcessLifecycleOwner에 Observer 등록
        ProcessLifecycleOwner.get().getLifecycle().addObserver(appLifecycleObserver);
                         
        // 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None);
        
        // 초기화 코드
        AdWhaleMediationAds.init(this, new AdWhaleMediationOnInitCompleteListener() {
            @Override
            public void onInitComplete(int statusCode, String message) {
                Log.i(TAG, ".onInitComplete(" + statusCode + ", " + message + ");");
                
                // 앱 오프닝 광고 null 체크: 이미 load하거나 생성된 앱 오프닝 광고가 있다면 onDestroy()가 있는 release() 호출
                if (adWhaleMediationAppOpenAd != null) {
                    release();
                }
                                
                // 앱 오프닝 광고 생성
                adWhaleMediationAppOpenAd = new AdWhaleMediationAppOpenAd(MainActivity.this, "발급받은 placement uid 값");
        
                // 앱 오프닝 광고 콜백 리스너 등록
                adWhaleMediationAppOpenAd.setAdWhaleMediationAppOpenAdListener(MainActivity.this);
                
                // 앱 오프닝 광고 로드
                adWhaleMediationAppOpenAd.loadAd();
            }
        });
    }

    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy()가 있는 release() 호출 필요
    @Override
    protected void onDestroy() {
        super.onDestroy();
        release();
    }

    private void release() {
        // ProcessLifecycleOwner Observer 해제
        if (appLifecycleObserver != null) {
            ProcessLifecycleOwner.get().getLifecycle().removeObserver(appLifecycleObserver);
            appLifecycleObserver = null;
        }
        
        if (adWhaleMediationAppOpenAd != null) {
            // 앱 종료 시 또는 더 이상 앱오프닝 광고를 사용하지 않을 때 호출
            adWhaleMediationAppOpenAd.destroy();
            adWhaleMediationAppOpenAd = null;
        }
    }

    // 앱 오프닝 광고 콜백 리스너: 광고 로드 성공 시 콜백됨
    @Override
    public void onAdLoaded() {
        Log.i(TAG, ".onAdLoaded();");
    }

    // 앱 오프닝 광고 콜백 리스너: 광고 로드 실패 시 콜백됨
    @Override
    public void onAdFailedToLoad(int statusCode, String message) {
        Log.e(TAG, ".onAdFailedToLoad(" + statusCode + ", " + message + ");");
    }

    // 앱 오프닝 광고 콜백 리스너: 광고 표시 실패 시 콜백됨
    @Override
    public void onAdFailedToShow(int statusCode, String message) {
        Log.e(TAG, ".onAdFailedToShow(" + statusCode + ", " + message + ");");
    }

    // 앱 오프닝 광고 콜백 리스너: 광고 클릭 시 콜백됨
    @Override
    public void onAdClicked() {
        Log.i(TAG, ".onAdClicked();");
    }

    // 앱 오프닝 광고 콜백 리스너: 광고 닫기 시 콜백됨
    @Override
    public void onAdDismissed() {
        Log.i(TAG, ".onAdDismissed();");
    }

    // 앱 오프닝 광고 콜백 리스너: 광고 표시 성공 시 콜백됨
    @Override
    public void onAdShowed() {
        Log.i(TAG, ".onAdShowed();");
    }
}
```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
import android.os.Bundle
import android.util.Log
import androidx.annotation.NonNull
import androidx.appcompat.app.AppCompatActivity
import androidx.lifecycle.DefaultLifecycleObserver
import androidx.lifecycle.LifecycleOwner
import androidx.lifecycle.ProcessLifecycleOwner
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAppOpenAd
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAppOpenAdListener
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener
import net.adwhale.sdk.utils.AdWhaleLog

class MainActivity : AppCompatActivity(), AdWhaleMediationAppOpenAdListener {

    companion object {
        private const val TAG = "AppOpenLifecycleTest"
    }

    // 라이프사이클 관리 (사용자가 직접 처리)
    private var appLifecycleObserver: DefaultLifecycleObserver? = null

    // 백그라운드/포어그라운드 상태체크 플래그값 (사용자가 직접 처리)
    private var isAppInBackground: Boolean = false

    private var adWhaleMediationAppOpenAd: AdWhaleMediationAppOpenAd? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main) // 기존 XML 레이아웃 사용하는 버전

        // 앱 라이프사이클 관리 Observer 등록: 백그라운드/포어그라운드 감지를 위해 등록 필요
        appLifecycleObserver = object : DefaultLifecycleObserver {
            override fun onStart(@NonNull owner: LifecycleOwner) {
                Log.d(TAG, "App moved to foreground")

                // 사용자가 직접 처리: 포어그라운드로 전환 시 광고 표시
                // 필요에 따라 조건 체크 (예: 백그라운드 시간, 광고 간격 등)를 추가할 수 있음
                if (adWhaleMediationAppOpenAd != null && isAppInBackground) {
                    adWhaleMediationAppOpenAd?.showAd(this@MainActivity)
                }
                // 사용자가 직접 처리: 백그라운드/포어그라운드 상태체크 플래그값 업데이트
                isAppInBackground = false
            }

            override fun onStop(@NonNull owner: LifecycleOwner) {
                Log.d(TAG, "App moved to background")

                // 사용자가 직접 처리: 백그라운드/포어그라운드 상태체크 플래그값 업데이트
                isAppInBackground = true
            }
        }

        // ProcessLifecycleOwner에 Observer 등록
        ProcessLifecycleOwner.get().lifecycle.addObserver(appLifecycleObserver!!)

        // 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)

        // 초기화 코드
        AdWhaleMediationAds.init(
            /* context = */ this,
            /* listener = */ object : AdWhaleMediationOnInitCompleteListener {
                override fun onInitComplete(statusCode: Int, message: String) {
                    Log.i(TAG, ".onInitComplete($statusCode, $message);")

                    // 앱 오프닝 광고 null 체크: 이미 load하거나 생성된 앱 오프닝 광고가 있다면 onDestroy()가 있는 release() 호출
                    if (adWhaleMediationAppOpenAd != null) {
                        release()
                    }

                    // 앱 오프닝 광고 생성
                    adWhaleMediationAppOpenAd =
                        AdWhaleMediationAppOpenAd(this@MainActivity, "발급받은 placement uid 값")

                    // 앱 오프닝 광고 콜백 리스너 등록
                    adWhaleMediationAppOpenAd?.setAdWhaleMediationAppOpenAdListener(this@MainActivity)

                    // 앱 오프닝 광고 로드
                    adWhaleMediationAppOpenAd?.loadAd()
                }
            }
        )
    }

    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy()가 있는 release() 호출 필요
    override fun onDestroy() {
        super.onDestroy()
        release()
    }

    private fun release() {
        // ProcessLifecycleOwner Observer 해제
        appLifecycleObserver?.let { observer ->
            ProcessLifecycleOwner.get().lifecycle.removeObserver(observer)
            appLifecycleObserver = null
        }

        if (adWhaleMediationAppOpenAd != null) {
            // 앱 종료 시 또는 더 이상 앱오프닝 광고를 사용하지 않을 때 호출
            adWhaleMediationAppOpenAd?.destroy()
            adWhaleMediationAppOpenAd = null
        }
    }

    // 앱 오프닝 광고 콜백 리스너: 광고 로드 성공 시 콜백됨
    override fun onAdLoaded() {
        Log.i(TAG, ".onAdLoaded();")
    }

    // 앱 오프닝 광고 콜백 리스너: 광고 로드 실패 시 콜백됨
    override fun onAdFailedToLoad(statusCode: Int, message: String) {
        Log.e(TAG, ".onAdFailedToLoad($statusCode, $message);")
    }

    // 앱 오프닝 광고 콜백 리스너: 광고 표시 실패 시 콜백됨
    override fun onAdFailedToShow(statusCode: Int, message: String) {
        Log.e(TAG, ".onAdFailedToShow($statusCode, $message);")
    }

    // 앱 오프닝 광고 콜백 리스너: 광고 클릭 시 콜백됨
    override fun onAdClicked() {
        Log.i(TAG, ".onAdClicked();")
    }

    // 앱 오프닝 광고 콜백 리스너: 광고 닫기 시 콜백됨
    override fun onAdDismissed() {
        Log.i(TAG, ".onAdDismissed();")
    }

    // 앱 오프닝 광고 콜백 리스너: 광고 표시 성공 시 콜백됨
    override fun onAdShowed() {
        Log.i(TAG, ".onAdShowed();")
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
import androidx.annotation.NonNull
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.lifecycle.DefaultLifecycleObserver
import androidx.lifecycle.LifecycleOwner
import androidx.lifecycle.ProcessLifecycleOwner
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAppOpenAd
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAppOpenAdListener
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener
import net.adwhale.sdk.utils.AdWhaleLog

class MainActivity : ComponentActivity(), AdWhaleMediationAppOpenAdListener {

    companion object {
        private const val TAG = "AppOpenLifecycleTest"
    }

    // 라이프사이클 관리 (사용자가 직접 처리)
    private var appLifecycleObserver: DefaultLifecycleObserver? = null

    // 백그라운드/포어그라운드 상태체크 플래그값 (사용자가 직접 처리)
    private var isAppInBackground: Boolean = false

    private var adWhaleMediationAppOpenAd: AdWhaleMediationAppOpenAd? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Compose UI
        setContent {
            MaterialTheme {
                AppOpenSampleScreen()
            }
        }

        // 앱 라이프사이클 관리 Observer 등록: 백그라운드/포어그라운드 감지를 위해 등록 필요
        appLifecycleObserver = object : DefaultLifecycleObserver {
            override fun onStart(@NonNull owner: LifecycleOwner) {
                Log.d(TAG, "App moved to foreground")

                // 사용자가 직접 처리: 포어그라운드로 전환 시 광고 표시
                if (adWhaleMediationAppOpenAd != null && isAppInBackground) {
                    adWhaleMediationAppOpenAd?.showAd(this@MainActivity)
                }
                // 상태 플래그 업데이트
                isAppInBackground = false
            }

            override fun onStop(@NonNull owner: LifecycleOwner) {
                Log.d(TAG, "App moved to background")
                isAppInBackground = true
            }
        }

        // ProcessLifecycleOwner에 Observer 등록
        ProcessLifecycleOwner.get().lifecycle.addObserver(appLifecycleObserver!!)

        // 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)

        // 초기화 코드
        AdWhaleMediationAds.init(
            /* context = */ this,
            /* listener = */ object : AdWhaleMediationOnInitCompleteListener {
                override fun onInitComplete(statusCode: Int, message: String) {
                    Log.i(TAG, ".onInitComplete($statusCode, $message);")

                    // 앱 오프닝 광고 null 체크: 이미 load하거나 생성된 앱 오프닝 광고가 있다면 release() 호출
                    if (adWhaleMediationAppOpenAd != null) {
                        release()
                    }

                    // 앱 오프닝 광고 생성
                    adWhaleMediationAppOpenAd =
                        AdWhaleMediationAppOpenAd(this@MainActivity, "발급받은 placement uid 값")

                    // 앱 오프닝 광고 콜백 리스너 등록
                    adWhaleMediationAppOpenAd?.setAdWhaleMediationAppOpenAdListener(this@MainActivity)

                    // 앱 오프닝 광고 로드
                    adWhaleMediationAppOpenAd?.loadAd()
                }
            }
        )
    }

    // 라이프사이클 onDestroy 콜백 시 반드시 release() 호출 필요
    override fun onDestroy() {
        release()
        super.onDestroy()
    }

    private fun release() {
        // ProcessLifecycleOwner Observer 해제
        appLifecycleObserver?.let { observer ->
            // main thread 보장 (혹시 init 콜백 스레드에서 호출될 수 있으므로)
            runOnUiThread {
                ProcessLifecycleOwner.get().lifecycle.removeObserver(observer)
                appLifecycleObserver = null
            }
        }

        if (adWhaleMediationAppOpenAd != null) {
            adWhaleMediationAppOpenAd?.destroy()
            adWhaleMediationAppOpenAd = null
        }
    }

    // 앱 오프닝 광고 콜백 리스너: 광고 로드 성공 시 콜백됨
    override fun onAdLoaded() {
        Log.i(TAG, ".onAdLoaded();")
    }

    // 앱 오프닝 광고 콜백 리스너: 광고 로드 실패 시 콜백됨
    override fun onAdFailedToLoad(statusCode: Int, message: String) {
        Log.e(TAG, ".onAdFailedToLoad($statusCode, $message);")
    }

    // 앱 오프닝 광고 콜백 리스너: 광고 표시 실패 시 콜백됨
    override fun onAdFailedToShow(statusCode: Int, message: String) {
        Log.e(TAG, ".onAdFailedToShow($statusCode, $message);")
    }

    // 앱 오프닝 광고 콜백 리스너: 광고 클릭 시 콜백됨
    override fun onAdClicked() {
        Log.i(TAG, ".onAdClicked();")
    }

    // 앱 오프닝 광고 콜백 리스너: 광고 닫기 시 콜백됨
    override fun onAdDismissed() {
        Log.i(TAG, ".onAdDismissed();")
    }

    // 앱 오프닝 광고 콜백 리스너: 광고 표시 성공 시 콜백됨
    override fun onAdShowed() {
        Log.i(TAG, ".onAdShowed();")
    }
}

@Composable
fun AppOpenSampleScreen() {
    Box(
        modifier = Modifier.fillMaxSize(),
        contentAlignment = Alignment.Center
    ) {
        Text(text = "AdWhale 앱 오프닝 광고 샘플 (Compose)")
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

* 앱 콜드 스타트 직후 또는 포어그라운드 복귀 시 등 자연스러운 시점에 노출하는 것을 권장합니다.
* 중복 표시: 이미 표시 중이거나 로드 중일 때 `showAd(activity)`는 무시될 수 있으므로, 콜백 상태를 활용해 제어하세요.

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
