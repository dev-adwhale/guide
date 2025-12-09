# 앱 오프닝 광고

앱 오프닝 광고 생성

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



**앱 오픈 광고는 앱 상태가 백그라운드에서 포그라운드로 변경될 때 광고를 노출합니다.**

1. 앱오픈광고 요청하기 위해 AdWhaleMediationAppOpenAd생성자를 호출합니다.
2. &#x20;AdWhaleMediationAppOpenAd가 Application의 Foreground 상태에 진입하는 것을 감지하면 광고를 요청합니다.
3. 더 이상 광고를 요청하지 않으려면 AdWhaleMediationAppOpenAd의 Destroy를 호출합니다.

&#x20;

**앱 오픈 광고 구현 샘플은 아래와 같습니다.**

먼저 백그라운드/포어그라운드 감지를 위한 라이프사이클 등록이 필요합니다.

라이프사이클 등록/해제에 필요한 의존성을 app 레벨의 build.gradle에 추가 후 sync 합니다.

```
    // Lifecycle Process (ProcessLifecycleOwner 사용을 위해 필요)
    implementation 'androidx.lifecycle:lifecycle-process:2.6.2'
    implementation 'androidx.lifecycle:lifecycle-runtime:2.6.2'
```

<pre class="language-java"><code class="lang-java">import android.os.Bundle;
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

<strong>    private AdWhaleMediationAppOpenAd adWhaleMediationAppOpenAd;
</strong>
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
                if (adWhaleMediationAppOpenAd != null &#x26;&#x26; isAppInBackground) {
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
</code></pre>
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



**앱 오픈 광고는 앱 상태가 백그라운드에서 포그라운드로 변경될 때 광고를 노출합니다.**

1. 앱오픈광고 요청하기 위해 AdWhaleMediationAppOpenAd생성자를 호출합니다.
2. &#x20;AdWhaleMediationAppOpenAd가 Application의 Foreground 상태에 진입하는 것을 감지하면 광고를 요청합니다.
3. 더 이상 광고를 요청하지 않으려면 AdWhaleMediationAppOpenAd의 Destroy를 호출합니다.

&#x20;

**앱 오픈 광고 구현 샘플은 아래와 같습니다.**

먼저 백그라운드/포어그라운드 감지를 위한 라이프사이클 등록이 필요합니다.

라이프사이클 등록/해제에 필요한 의존성을 app 레벨의 build.gradle에 추가 후 sync 합니다.

```
    // Lifecycle Process (ProcessLifecycleOwner 사용을 위해 필요)
    implementation 'androidx.lifecycle:lifecycle-process:2.6.2'
    implementation 'androidx.lifecycle:lifecycle-runtime:2.6.2'
```

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



**앱 오픈 광고는 앱 상태가 백그라운드에서 포그라운드로 변경될 때 광고를 노출합니다.**

1. 앱오픈광고 요청하기 위해 AdWhaleMediationAppOpenAd생성자를 호출합니다.
2. &#x20;AdWhaleMediationAppOpenAd가 Application의 Foreground 상태에 진입하는 것을 감지하면 광고를 요청합니다.
3. 더 이상 광고를 요청하지 않으려면 AdWhaleMediationAppOpenAd의 Destroy를 호출합니다.

&#x20;

**앱 오픈 광고 구현 샘플은 아래와 같습니다.**

먼저 백그라운드/포어그라운드 감지를 위한 라이프사이클 등록이 필요합니다.

라이프사이클 등록/해제에 필요한 의존성을 app 레벨의 build.gradle에 추가 후 sync 합니다.

```
    // Lifecycle Process (ProcessLifecycleOwner 사용을 위해 필요)
    implementation 'androidx.lifecycle:lifecycle-process:2.6.2'
    implementation 'androidx.lifecycle:lifecycle-runtime:2.6.2'
```

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



