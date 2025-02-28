# 전면

### **전면 생성**

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

```java
public void showAd() // 미디에이션 전면광고로드 후 표시할 때 호출
```

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
import android.widget.Button;
import android.widget.EditText;

import androidx.appcompat.app.AppCompatActivity;

import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAd;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAdListener;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener;

public class MainActivity extends AppCompatActivity {

    private ConstraintLayout root;

<strong>    private AdWhaleMediationInterstitialAd adWhaleMediationInterstitialAd;
</strong>
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        AdWhaleMediationAds.init(this, new AdWhaleMediationOnInitCompleteListener() {
            @Override
            public void onInitComplete(int statusCode, String message) {
                Log.i(MainActivity.class.getSimpleName(), ".onInitComplete(" + statusCode + ", " + message + ");");
            }
        });

        adWhaleMediationInterstitialAd = new AdWhaleMediationInterstitialAd("발급받은 placement uid 값");
        adWhaleMediationInterstitialAd.setAdWhaleMediationInterstitialAdListener(new AdWhaleMediationInterstitialAdListener() {
            @Override
            public void onAdLoaded() {
                Log.i(MainActivity.class.getSimpleName(), ".onAdLoaded();");
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

        adWhaleMediationInterstitialAd.loadAd();

    }
    
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
import android.widget.Button;
import android.widget.EditText;

import androidx.appcompat.app.AppCompatActivity;

import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAd;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAdListener;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener;

public class MainActivity : AppCompatActivity() {

    private lateinit var root : ConstraintLayout;

    private lateinit var adWhaleMediationInterstitialAd : AdWhaleMediationInterstitialAd;

    overrider fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        AdWhaleMediationAds.init(this, AdWhaleMediationOnInitCompleteListener {
            statusCode, message ->
                Log.i(MainActivity::class.simpleName, ".onInitComplete($statusCode, $message)")
        })

        adWhaleMediationInterstitialAd = AdWhaleMediationInterstitialAd("발급받은 placement uid 값")
        adWhaleMediationInterstitialAd.setAdWhaleMediationInterstitialAdListener(
        
            object : AdWhaleMediationInterstitialAdListener {
            
            override fun onAdLoaded() {
                Log.i(MainActivity::class.simpleName, ".onAdLoaded()")
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

        adWhaleMediationInterstitialAd.loadAd()

    }
    
    override fun onDestroy() {
        super.onDestroy()
        adWhaleMediationInterstitialAd.destroy()
    }  
}
```
{% endtab %}
{% endtabs %}





