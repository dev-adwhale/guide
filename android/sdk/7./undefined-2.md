# 전면

{% hint style="warning" %}
`targetSdkVersion 35` 환경의 Android 15 기기에서 **전면 광고 일부 화면 잘림 이슈**를 수정했습니다.

Android 15 이상을 타겟팅하는 경우 **2.5.8 이상**을 적용합니다.
{% endhint %}

전면 생성

{% tabs %}
{% tab title="Java" %}
**AdWhaleMediationInterstitialAd 클래스 API 설명**

```java
public AdWhaleMediationInterstitialAd(String placementUid)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```java
public void setAdWhaleMediationInterstitialAdListener(AdWhaleMediationInterstitialAdListener listener)
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationInterstitialAdListener</p></td><td>전면 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```java
public void loadAd() // 미디에이션 전면광고로드
```

<pre class="language-java"><code class="lang-java"><strong>public void showAd() // 미디에이션 전면광고로드 후 표시할 때 호출
</strong></code></pre>

```java
public void showAd(Activity activity) // 미디에이션 전면광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr></tbody></table>

```java
public void destroy() // onDestroy() 시 호출
```

\
**AdWhaleMediationInterstitialAdListener 클래스 API 설명**

```java
public void onAdLoaded() // 미디에이션 전면광고요청 성공 시
```

```java
public void onAdLoadFailed(int statusCode, String message) // 미디에이션 전면광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```java
public void onAdShowed() // 미디에이션 전면광고표시 후
```

```java
public void onAdShowFailed(int statusCode, String message) // 미디에이션 전면광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고표시 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```java
public void onAdClosed() // 미디에이션 전면광고닫기 시
```

```java
public void onAdClicked() // 미디에이션 전면광고클릭 시
```

**전면 구현 샘플은 아래와 같습니다.**&#x20;

<pre class="language-java"><code class="lang-java">import android.os.Bundle;
import android.util.Log;

import androidx.appcompat.app.AppCompatActivity;
import androidx.constraintlayout.widget.ConstraintLayout;

import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAd;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAdListener;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener;
import net.adwhale.sdk.utils.AdWhaleLog;

public class MainActivity extends AppCompatActivity {

    private ConstraintLayout root;

<strong>    private AdWhaleMediationInterstitialAd adWhaleMediationInterstitialAd;
</strong>
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        // 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None);
        
        // 초기화 코드
        AdWhaleMediationAds.init(this, new AdWhaleMediationOnInitCompleteListener() {
            @Override
            public void onInitComplete(int statusCode, String message) {
                Log.i(MainActivity.class.getSimpleName(), ".onInitComplete(" + statusCode + ", " + message + ");");
            }
        });

        // 전면광고 생성
        adWhaleMediationInterstitialAd = new AdWhaleMediationInterstitialAd("발급받은 placement uid 값");
        
        // 전면광고 콜백 리스너 등록
        adWhaleMediationInterstitialAd.setAdWhaleMediationInterstitialAdListener(new AdWhaleMediationInterstitialAdListener() {
            @Override
            public void onAdLoaded() {
                Log.i(MainActivity.class.getSimpleName(), ".onAdLoaded();");
                // 전면광고 표시
                adWhaleMediationInterstitialAd.showAd();
            }

            @Override
            public void onAdLoadFailed(int statusCode, String message) {
                Log.e(MainActivity.class.getSimpleName(), ".onAdLoadFailed(" + statusCode + ", " + message + ");");
            }

            @Override
            public void onAdShowed() {
                Log.i(MainActivity.class.getSimpleName(), ".onAdShowed();");
            }

            @Override
            public void onAdShowFailed(int statusCode, String message) {
                Log.i(MainActivity.class.getSimpleName(), ".onAdShowFailed(" + statusCode + ", " + message + ");");
            }

            @Override
            public void onAdClosed() {
                Log.i(MainActivity.class.getSimpleName(), ".onAdClosed();");
            }
            
            @Override
            public void onAdClicked() {
                Log.i(MainActivity.class.getSimpleName(), ".onAdClicked();");
            }            
        });

        // 전면광고 로드
        adWhaleMediationInterstitialAd.loadAd();

    }
    
    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy 호출 필요
    @Override
    protected void onDestroy() {
        super.onDestroy();
        if(adWhaleMediationInterstitialAd != null) {
            adWhaleMediationInterstitialAd.destroy();        
        }
    }    
}
</code></pre>
{% endtab %}

{% tab title="Kotlin" %}
**AdWhaleMediationInterstitialAd 클래스 API 설명**

```kotlin
AdWhaleMediationInterstitialAd(placementUid : String)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```kotlin
fun setAdWhaleMediationInterstitialAdListener(listener : AdWhaleMediationInterstitialAdListener) : Unit
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAdListener</td><td>전면 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```kotlin
fun loadAd() : Unit // 미디에이션 전면광고로드
```

```kotlin
fun showAd() : Unit // 미디에이션 전면광고로드 후 표시할 때 호출
```

```kotlin
fun showAd(activity : Activity) : Unit // 미디에이션 전면광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr></tbody></table>

```kotlin
fun destroy() : Unit // onDestroy() 시 호출
```





**AdWhaleMediationInterstitialAdListener 클래스 API 설명**

```kotlin
fun onAdLoaded() : Unit // 미디에이션 전면광고요청 성공 시
```

```kotlin
fun onAdLoadFailed(statusCode : Int, message : String) : Unit // 미디에이션 전면광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td><p>초기화 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdShowed() : Unit // 미디에이션 전면광고표시 후
```

```kotlin
fun onAdShowFailed(statusCode : Int, message : String) : Unit // 미디에이션 전면광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td><p>초기화 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdClosed() : Unit // 미디에이션 전면광고닫기 시
```

```kotlin
fun onAdClicked() : Unit // 미디에이션 전면광고클릭 시
```

**전면 구현 샘플은 아래와 같습니다.**&#x20;

```kotlin
import android.os.Bundle;
import android.util.Log;

import androidx.appcompat.app.AppCompatActivity;
import androidx.constraintlayout.widget.ConstraintLayout;

import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAd;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAdListener;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener;
import net.adwhale.sdk.utils.AdWhaleLog;

public class MainActivity : AppCompatActivity() {

    private lateinit var root : ConstraintLayout;

    private lateinit var adWhaleMediationInterstitialAd : AdWhaleMediationInterstitialAd;

    overrider fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)
        
        // 초기화 코드
        AdWhaleMediationAds.init(this, AdWhaleMediationOnInitCompleteListener {
            statusCode, message ->
                Log.i(MainActivity::class.simpleName, ".onInitComplete($statusCode, $message)")
        })

        // 전면광고 생성
        adWhaleMediationInterstitialAd = AdWhaleMediationInterstitialAd("발급받은 placement uid 값")

        // 전면광고 콜백 리스너 등록
        adWhaleMediationInterstitialAd.setAdWhaleMediationInterstitialAdListener(
        
            object : AdWhaleMediationInterstitialAdListener {
            
            override fun onAdLoaded() {
                Log.i(MainActivity::class.simpleName, ".onAdLoaded()")
                // 전면광고 표시
                adWhaleMediationInterstitialAd.showAd()
            }

            override fun onAdLoadFailed(statusCode : Int, message : String) {
                Log.e(MainActivity::class.simpleName, ".onAdLoadFailed($statusCode, $message)")
            }

            override fun onAdShowed() {
                Log.i(MainActivity::class.simpleName, ".onAdShowed()")
            }

            override fun onAdShowFailed(statusCode : Int, message : String) {
                Log.i(MainActivity::class.simpleName, ".onAdShowFailed($statusCode, $message)")
            }

            override fun onAdClosed() {
                Log.i(MainActivity::class.simpleName, ".onAdClosed()")
            }

            override fun onAdClicked() {
                Log.i(MainActivity::class.simpleName, ".onAdClicked()")
            }                        
        });

        // 전면광고 로드
        adWhaleMediationInterstitialAd.loadAd()

    }
    
    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy 호출 필요
    override fun onDestroy() {
        super.onDestroy()
        adWhaleMediationInterstitialAd.destroy()
    }  
}
```
{% endtab %}

{% tab title="Compose" %}
**AdWhaleMediationInterstitialAd 클래스 API 설명**

```kotlin
AdWhaleMediationInterstitialAd(placementUid : String)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```kotlin
fun setAdWhaleMediationInterstitialAdListener(listener : AdWhaleMediationInterstitialAdListener) : Unit
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAdListener</td><td>전면 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```kotlin
fun loadAd() : Unit // 미디에이션 전면광고로드
```

```kotlin
fun showAd() : Unit // 미디에이션 전면광고로드 후 표시할 때 호출
```

```kotlin
fun showAd(activity : Activity) : Unit // 미디에이션 전면광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr></tbody></table>

```kotlin
fun destroy() : Unit // onDestroy() 시 호출
```





**AdWhaleMediationInterstitialAdListener 클래스 API 설명**

```kotlin
fun onAdLoaded() : Unit // 미디에이션 전면광고요청 성공 시
```

```kotlin
fun onAdLoadFailed(statusCode : Int, message : String) : Unit // 미디에이션 전면광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td><p>초기화 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdShowed() : Unit // 미디에이션 전면광고표시 후
```

```kotlin
fun onAdShowFailed(statusCode : Int, message : String) : Unit // 미디에이션 전면광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td><p>초기화 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdClosed() : Unit // 미디에이션 전면광고닫기 시
```

```kotlin
fun onAdClicked() : Unit // 미디에이션 전면광고클릭 시
```

**전면 구현 샘플은 아래와 같습니다.**&#x20;

```kotlin
import android.os.Bundle
import android.util.Log
import android.widget.Toast
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.material3.Button
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.OutlinedTextField
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.runtime.getValue
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.remember
import androidx.compose.runtime.setValue
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds
import net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAd
import net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAdListener
import net.adwhale.sdk.utils.AdWhaleLog

class MainActivity : ComponentActivity() {


    // Java 가이드와 동일하게 멤버 필드로 유지
    private var adWhaleMediationInterstitialAd: AdWhaleMediationInterstitialAd? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Java 가이드와 동일: init + 로그 레벨 설정
        AdWhaleMediationAds.init(this) { statusCode, message ->
            AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.Verbose)
            Log.i(MainActivity::class.java.simpleName, ".onInitComplete($statusCode, $message)")
        }

        val defaultPlacementUid = "발급받은 placement uid 값"

        setContent {
            MaterialTheme {
                var placementUid by remember { mutableStateOf(defaultPlacementUid) }

                InterstitialScreen(
                    placementUid = placementUid,
                    onPlacementUidChange = { placementUid = it },
                    onRequestAd = { requestInterstitialAd(placementUid) },
                    onShowAd = { showInterstitialAd() }
                )
            }
        }
    }

    /**
     * Java 가이드의 onCreate 내부 로직과 동일한 흐름:
     * - AdWhaleMediationInterstitialAd 생성
     * - Listener 설정
     * - loadAd() 호출
     */
    private fun requestInterstitialAd(placementUid: String) {
        if (placementUid.isBlank()) {
            Toast.makeText(applicationContext, "placementUid 를 입력하세요.", Toast.LENGTH_SHORT)
                .show()
            return
        }

        // 기존 객체 정리
        adWhaleMediationInterstitialAd?.destroy()
        adWhaleMediationInterstitialAd = null

        // Java 가이드와 동일: 생성자에 placementUid 전달
        val interstitialAd = AdWhaleMediationInterstitialAd(placementUid)
        adWhaleMediationInterstitialAd = interstitialAd

        // Java 가이드와 동일한 리스너 구성
        interstitialAd.setAdWhaleMediationInterstitialAdListener(object :
            AdWhaleMediationInterstitialAdListener {

            @Override
            override fun onAdLoaded() {
                Log.i(MainActivity::class.java.simpleName, ".onAdLoaded()")
                Toast.makeText(applicationContext, ".onAdLoaded()", Toast.LENGTH_SHORT).show()
            }

            @Override
            override fun onAdLoadFailed(statusCode: Int, message: String) {
                Log.e(MainActivity::class.java.simpleName, ".onAdLoadFailed($statusCode, $message)")
                Toast.makeText(
                    applicationContext,
                    ".onAdLoadFailed(statusCode:$statusCode, message:$message)",
                    Toast.LENGTH_SHORT
                ).show()
            }

            @Override
            override fun onAdShowed() {
                Log.i(MainActivity::class.java.simpleName, ".onAdShowed()")
                Toast.makeText(applicationContext, ".onAdShowed()", Toast.LENGTH_SHORT).show()
            }

            @Override
            override fun onAdShowFailed(statusCode: Int, message: String) {
                Log.e(MainActivity::class.java.simpleName, ".onAdShowFailed($statusCode, $message)")
                Toast.makeText(
                    applicationContext,
                    ".onAdShowFailed(statusCode:$statusCode, message:$message)",
                    Toast.LENGTH_SHORT
                ).show()
            }

            @Override
            override fun onAdClosed() {
                Log.i(MainActivity::class.java.simpleName, ".onAdClosed()")
                Toast.makeText(applicationContext, ".onAdClosed()", Toast.LENGTH_SHORT).show()
            }

            @Override
            override fun onAdClicked() {
                Log.i(MainActivity::class.java.simpleName, ".onAdClicked()")
                // 필요시 Toast 추가 가능
            }
        })

        // Java 가이드 동일: loadAd() 호출
        interstitialAd.loadAd()
    }

    /**
     * Java 가이드와 동일한 showAd() 흐름
     */
    private fun showInterstitialAd() {
        val interstitialAd = adWhaleMediationInterstitialAd
        if (interstitialAd == null) {
            Toast.makeText(applicationContext, "광고가 아직 로드되지 않았습니다.", Toast.LENGTH_SHORT).show()
            return
        }
        interstitialAd.showAd()
    }

    /**
     * 라이프사이클 onDestroy 콜백 시 반드시 destroy() 호출 필요 (Java 가이드 동일)
     */
    override fun onDestroy() {
        if (adWhaleMediationInterstitialAd != null) {
            adWhaleMediationInterstitialAd?.destroy()
            adWhaleMediationInterstitialAd = null
        }
        super.onDestroy()
    }
}

/**
 * 기존 activity_programmatic_interstitial_main.xml 을 Compose 로 옮긴 UI
 */
@Composable
fun InterstitialScreen(
    placementUid: String,
    onPlacementUidChange: (String) -> Unit,
    onRequestAd: () -> Unit,
    onShowAd: () -> Unit
) {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(20.dp),
        horizontalAlignment = Alignment.Start
    ) {
        Text(
            text = "1. 테스트 전면광고 Placement Uid 입력:",
            style = MaterialTheme.typography.titleMedium
        )

        Spacer(modifier = Modifier.height(12.dp))

        OutlinedTextField(
            value = placementUid,
            onValueChange = onPlacementUidChange,
            modifier = Modifier.fillMaxWidth(),
            label = { Text("placementUid 입력") }
        )

        Spacer(modifier = Modifier.height(24.dp))

        Button(
            onClick = onRequestAd,
            modifier = Modifier.align(Alignment.CenterHorizontally)
        ) {
            Text(text = "테스트 전면광고 요청")
        }

        Spacer(modifier = Modifier.height(16.dp))

        Button(
            onClick = onShowAd,
            modifier = Modifier.align(Alignment.CenterHorizontally)
        ) {
            Text(text = "테스트 전면광고 표시")
        }
    }
}
```
{% endtab %}
{% endtabs %}





