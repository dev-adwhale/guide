# 보상형

보상형 광고 생성

{% tabs %}
{% tab title="Java" %}


**AdWhaleMediationRewardAd 클래스 API 설명**

```java
public AdWhaleMediationRewardAd(String placementUid)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```java
public void setAdWhaleMediationFullScreenContentCallback(AdWhaleMediationFullScreenContentCallback listener)
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationFullScreenContentCallback</p></td><td>보상형 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```java
public void loadAd(AdWhaleMediationRewardAdLoadCallback listener) // 미디에이션 보상형 전면 광고로드 시 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationRewardAdLoadCallback</p></td><td>보상형 미디에이션 광고 로드 콜백 리스너</td></tr></tbody></table>

```java
public void showAd(AdWhaleMediationUserEarnedRewardListener listener) // 미디에이션 보상형 전면 광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationUserEarnedRewardListener</p></td><td>보상형 미디에이션 광고 리워드 지급 콜백 리스너</td></tr></tbody></table>

```java
public void showAd(Activity activity, AdWhaleMediationUserEarnedRewardListener listener) // 미디에이션 보상형 전면 광고로드 후 표시할 때 호출
```

| 파라미터 타입                                                                              | 파라미터 값                     |
| ------------------------------------------------------------------------------------ | -------------------------- |
| android.app.Activity                                                                 | Android Activity 클래스       |
| <p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationUserEarnedRewardListener</p> | 보상형 미디에이션 광고 리워드 지급 콜백 리스너 |

<pre class="language-java"><code class="lang-java"><strong>public void destroy() // onDestroy() 시 호출
</strong></code></pre>



**AdWhaleMediationFullScreenContentCallback 클래스 API 설명**

```java
public void onAdClicked() // 미디에이션 보상형 광고클릭 시
```

```java
public void onAdDismissed() // 미디에이션 보상형 광고닫기 시
```

```java
public void onFailedToShow(int statusCode, String message) // 미디에이션 보상형 광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>광고표시 결과 코드</td></tr><tr><td>String</td><td>광고표시 결과 메시지</td></tr></tbody></table>

```java
public void onAdShowed() // 미디에이션 보상형 광고표시 후
```



**AdWhaleMediationRewardAdLoadCallback 클래스 API 설명**

```java
public void onAdLoaded(AdWhaleMediationRewardAd adWhaleMediationRewardAd, String message) // 미디에이션 보상형 광고로드 성공 시
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>AdWhaleMediationRewardAd</td><td>AdWhaleMediationRewardAd 객체</td></tr><tr><td>String</td><td>광고로드 결과 메시지</td></tr></tbody></table>

```java
public void onFailedToLoad(int statusCode, String message) // 미디에이션 보상형 광고로드 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>광고로드 결과 코드</td></tr><tr><td>String</td><td>광고로드 결과 메시지</td></tr></tbody></table>



**AdWhaleMediationUserEarnedRewardListener 클래스 API 설명**

```java
public void onUserRewarded(AdWhaleMediationRewardItem rewardItem) // 미디에이션 보상형 리워드지급 성공 시
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>AdWhaleMediationRewardtem</td><td>리워드 타입, 리워드 금액 값을 지니는 객체</td></tr></tbody></table>



**보상형 구현 샘플은 아래와 같습니다.**&#x20;

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

    private AdWhaleMediationRewardAd adWhaleMediationRewardAd;

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

        // 보상형광고 생성
        adWhaleMediationRewardAd = new AdWhaleMediationRewardAd("발급받은 placement uid 값");
        
        // 보상형광고 콜백 리스너 등록
        adWhaleMediationRewardAd.setAdWhaleMediationFullScreenContentCallback(new AdWhaleMediationFullScreenContentCallback() {
        
            @Override
            public void onAdClicked() {
                Log.i(MainActivity.class.getSimpleName(), ".onAdClicked();");
            }

            @Override
            public void onAdDismissed() {
                Log.i(MainActivity.class.getSimpleName(), ".onAdDismissed();");
            }
            
            @Override
            public void onFailedToShow(int statusCode, String message) {
                Log.i(MainActivity.class.getSimpleName(), ".onFailedToShow(" + statusCode + ", " + message + ");");
            }
                        
            @Override
            public void onAdShowed() {
                Log.e(MainActivity.class.getSimpleName(), ".onAdShowed();");
            }
        });

        // 보상형광고 로드
        adWhaleMediationRewardAd.loadAd(new AdWhaleMediationRewardedAdLoadCallback() {
            @Override
            public void onAdLoaded(AdWhaleMediationRewardAd adWhaleMediationRewardAd, String message) {
                Log.i(MainActivity.class.getSimpleName(), ".onAdLoaded(" + message + ")");
                
                if(adWhaleMediationRewardAd != null) {
                    // 보상형광고 표시
                    adWhaleMediationRewardAd.showAd(adWhaleMediationRewardItem -> {
                        Log.i(MainActivity.class.getSimpleName(), ".onUserRewarded(" + adWhaleMediationRewardItem.toString() + ")");
                    });                
                }         
            }

            @Override
            public void onAdFailedToLoad(int statusCode, String message) {
                Log.i(MainActivity.class.getSimpleName(), ".onAdFailedToLoad(" + statusCode + ", " + message + ")");
            }
        });           

    }
    
    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy 호출 필요
    @Override
    protected void onDestroy() {
        super.onDestroy();
        if(adWhaleMediationRewardAd != null) {
            adWhaleMediationRewardAd.destroy();
        }
    }    
}
```
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

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationFullScreenContentCallback</p></td><td>보상형 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```kotlin
fun loadAd(listener : AdWhaleMediationRewardAdLoadCallback) : Unit  // 미디에이션 보상형광고로드 시 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationRewardAdLoadCallback</p></td><td>보상형 미디에이션 광고 로드 콜백 리스너</td></tr></tbody></table>

```kotlin
fun showAd(listener : AdWhaleMediationUserEarnedRewardListener) : Unit // 미디에이션 보상형광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationUserEarnedRewardListener</p></td><td>보상형 미디에이션 광고 리워드 지급 콜백 리스너</td></tr></tbody></table>

```kotlin
fun showAd(activity : Activity, listener : AdWhaleMediationUserEarnedRewardListener) : Unit // 미디에이션 보상형광고로드 후 표시할 때 호출
```

| 파라미터 타입                                                                              | 파라미터 값                     |
| ------------------------------------------------------------------------------------ | -------------------------- |
| android.app.Activity                                                                 | Android Activity 클래스       |
| <p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationUserEarnedRewardListener</p> | 보상형 미디에이션 광고 리워드 지급 콜백 리스너 |

```kotlin
fun destroy() : Unit // onDestroy() 시 호출
```



**AdWhaleMediationFullScreenContentCallback 클래스 API 설명**

```kotlin
fun onAdClicked() : Unit // 미디에이션 보상형광고클릭 시
```

```kotlin
fun onAdDismissed() : Unit // 미디에이션 보상형광고닫기 시
```

```kotlin
fun onFailedToShow(statusCode : Int , message : String) : Unit // 미디에이션 보상형광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td>광고표시 결과 코드</td></tr><tr><td>String</td><td>초기화 결과 메시지</td></tr></tbody></table>

```kotlin
fun onAdShowed() : Unit // 미디에이션 보상형광고표시 후
```



**AdWhaleMediationRewardAdLoadCallback 클래스 API 설명**

```kotlin
fun onAdLoaded(adWhaleMediationRewardAd : AdWhaleMediationRewardAd, message : String) : Unit // 미디에이션 보상형광고로드 성공 시
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>AdWhaleMediationRewardAd</td><td>AdWhaleMediationRewardAd 객체</td></tr><tr><td>String</td><td>광고로드 결과 메시지</td></tr></tbody></table>

```kotlin
fun onFailedToShow(statusCode : Int, message : String) : Unit // 미디에이션 보상형로드 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td>광고표시 결과 코드</td></tr><tr><td>String</td><td>광고표시 결과 메시지</td></tr></tbody></table>



**AdWhaleMediationUserEarnedRewardListener 클래스 API 설명**

```kotlin
fun onUserRewarded(rewardItem : AdWhaleMediationRewardItem) : Unit // 미디에이션 보상형리워드지급 성공 시
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>AdWhaleMediationRewardtem</td><td>리워드 타입, 리워드 금액 값을 지니는 객체</td></tr></tbody></table>



**보상형 구현 샘플은 아래와 같습니다.**&#x20;

```kotlin
import android.os.Bundle;
import android.util.Log;

import androidx.appcompat.app.AppCompatActivity;

import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationFullScreenContentCallback;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardAd;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardedAdLoadCallback;
import net.adwhale.sdk.utils.AdWhaleLog;

public class MainActivity : AppCompatActivity() {

    private lateinit var adWhaleMediationRewardAd : AdWhaleMediationRewardAd;

    override fun onCreate(savedInstanceState : Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)
        
        // 초기화 코드
        AdWhaleMediationAds.init(this, AdWhaleMediationOnInitCompleteListener {
            statusCode, message ->
                Log.i(MainActivity::class.simpleName, ".onInitComplete($statusCode, $message)")
        })

        // 보상형광고 생성
        adWhaleMediationRewardAd = AdWhaleMediationRewardAd("발급받은 placement uid 값")
        
        // 보상형광고 콜백 리스너 등록
        adWhaleMediationRewardAd.setAdWhaleMediationFullScreenContentCallback(
        
            object : AdWhaleMediationFullScreenContentCallback {
            
            override fun onAdClicked() {
                Log.i(MainActivity::class.simpleName, ".onAdClicked()")
            }

            override fun onAdDismissed() {
                Log.i(MainActivity::class.simpleName, ".onAdDismissed()")
            }

            override fun onFailedToShow(statusCode : Int, message : String) {
                Log.i(MainActivity::class.simpleName, ".onFailedToShow($statusCode, $message)")
            }

            override fun onAdShowed() {
                Log.i(MainActivity::class.simpleName, ".onAdShowed()")
            }
        })
        
        // 보상형광고 로드
        adWhaleMediationRewardAd.loadAd(object : AdWhaleMediationRewardedAdLoadCallback {
            
            override fun onAdLoaded(adWhaleMediationRewardAd : AdWhaleMediationRewardAd, message : String) {
                Log.i(MainActivity.class.getSimpleName(), ".onAdLoaded(${message})");
                // 보상형광고 표시
                adWhaleMediationRewardAd.showAd(adWhaleMediationRewardItem -> {
                    Log.i(MainActivity.class.getSimpleName(), ".onUserRewarded(${adWhaleMediationRewardItem.toString()})");
                });                 
            }

            override fun onAdFailedToLoad(statusCode : Int, message : String) {
                Log.i(MainActivity.class.getSimpleName(), ".onAdFailedToLoad(" + statusCode + ", " + message + ")");
            }
        })

    }
    
    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy 호출 필요
    override fun onDestroy() {
        super.onDestroy()
        adWhaleMediationRewardAd.destroy()
    }
}
```
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

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationFullScreenContentCallback</p></td><td>보상형 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```kotlin
fun loadAd(listener : AdWhaleMediationRewardAdLoadCallback) : Unit  // 미디에이션 보상형광고로드 시 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationRewardAdLoadCallback</p></td><td>보상형 미디에이션 광고 로드 콜백 리스너</td></tr></tbody></table>

```kotlin
fun showAd(listener : AdWhaleMediationUserEarnedRewardListener) : Unit // 미디에이션 보상형광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationUserEarnedRewardListener</p></td><td>보상형 미디에이션 광고 리워드 지급 콜백 리스너</td></tr></tbody></table>

```kotlin
fun showAd(activity : Activity, listener : AdWhaleMediationUserEarnedRewardListener) : Unit // 미디에이션 보상형광고로드 후 표시할 때 호출
```

| 파라미터 타입                                                                              | 파라미터 값                     |
| ------------------------------------------------------------------------------------ | -------------------------- |
| android.app.Activity                                                                 | Android Activity 클래스       |
| <p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationUserEarnedRewardListener</p> | 보상형 미디에이션 광고 리워드 지급 콜백 리스너 |

```kotlin
fun destroy() : Unit // onDestroy() 시 호출
```



**AdWhaleMediationFullScreenContentCallback 클래스 API 설명**

```kotlin
fun onAdClicked() : Unit // 미디에이션 보상형광고클릭 시
```

```kotlin
fun onAdDismissed() : Unit // 미디에이션 보상형광고닫기 시
```

```kotlin
fun onFailedToShow(statusCode : Int , message : String) : Unit // 미디에이션 보상형광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td>광고표시 결과 코드</td></tr><tr><td>String</td><td>초기화 결과 메시지</td></tr></tbody></table>

```kotlin
fun onAdShowed() : Unit // 미디에이션 보상형광고표시 후
```



**AdWhaleMediationRewardAdLoadCallback 클래스 API 설명**

```kotlin
fun onAdLoaded(adWhaleMediationRewardAd : AdWhaleMediationRewardAd, message : String) : Unit // 미디에이션 보상형광고로드 성공 시
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>AdWhaleMediationRewardAd</td><td>AdWhaleMediationRewardAd 객체</td></tr><tr><td>String</td><td>광고로드 결과 메시지</td></tr></tbody></table>

```kotlin
fun onFailedToShow(statusCode : Int, message : String) : Unit // 미디에이션 보상형로드 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td>광고표시 결과 코드</td></tr><tr><td>String</td><td>광고표시 결과 메시지</td></tr></tbody></table>



**AdWhaleMediationUserEarnedRewardListener 클래스 API 설명**

```kotlin
fun onUserRewarded(rewardItem : AdWhaleMediationRewardItem) : Unit // 미디에이션 보상형리워드지급 성공 시
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>AdWhaleMediationRewardtem</td><td>리워드 타입, 리워드 금액 값을 지니는 객체</td></tr></tbody></table>



**보상형 구현 샘플은 아래와 같습니다.**&#x20;

```kotlin
import android.os.Bundle
import android.util.Log
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds
import net.adwhale.sdk.mediation.ads.AdWhaleMediationFullScreenContentCallback
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener
import net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardAd
import net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardedAdLoadCallback
import net.adwhale.sdk.utils.AdWhaleLog

class MainActivity : ComponentActivity() {

    private var adWhaleMediationRewardAd: AdWhaleMediationRewardAd? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Compose UI (샘플이므로 간단한 텍스트만 표시)
        setContent {
            MaterialTheme {
                RewardSampleScreen()
            }
        }

        // 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)

        // 초기화 코드
        AdWhaleMediationAds.init(
            /* context = */ this,
            /* listener = */ object : AdWhaleMediationOnInitCompleteListener {
                override fun onInitComplete(statusCode: Int, message: String) {
                    Log.i(
                        MainActivity::class.java.simpleName,
                        ".onInitComplete($statusCode, $message);"
                    )
                }
            }
        )

        // 보상형광고 생성
        adWhaleMediationRewardAd =
            AdWhaleMediationRewardAd("발급받은 placement uid 값")

        // 보상형광고 콜백 리스너 등록
        adWhaleMediationRewardAd?.setAdWhaleMediationFullScreenContentCallback(
            object : AdWhaleMediationFullScreenContentCallback {

                override fun onAdClicked() {
                    Log.i(
                        MainActivity::class.java.simpleName,
                        ".onAdClicked();"
                    )
                }

                override fun onAdDismissed() {
                    Log.i(
                        MainActivity::class.java.simpleName,
                        ".onAdDismissed();"
                    )
                }

                override fun onFailedToShow(statusCode: Int, message: String) {
                    Log.i(
                        MainActivity::class.java.simpleName,
                        ".onFailedToShow($statusCode, $message);"
                    )
                }

                override fun onAdShowed() {
                    Log.e(
                        MainActivity::class.java.simpleName,
                        ".onAdShowed();"
                    )
                }
            }
        )

        // 보상형광고 로드
        adWhaleMediationRewardAd?.loadAd(object : AdWhaleMediationRewardedAdLoadCallback {
            override fun onAdLoaded(
                adWhaleMediationRewardAd: AdWhaleMediationRewardAd,
                message: String
            ) {
                Log.i(
                    MainActivity::class.java.simpleName,
                    ".onAdLoaded($message)"
                )

                // 로드 완료 시 바로 표시 (Java 가이드와 동일한 흐름)
                this@MainActivity.adWhaleMediationRewardAd?.showAd { rewardItem ->
                    Log.i(
                        MainActivity::class.java.simpleName,
                        ".onUserRewarded(${rewardItem})"
                    )
                }
            }

            override fun onAdFailedToLoad(statusCode: Int, message: String) {
                Log.i(
                    MainActivity::class.java.simpleName,
                    ".onAdFailedToLoad($statusCode, $message)"
                )
            }
        })
    }

    // 라이프사이클 onDestroy 콜백 시 반드시 destroy 호출 필요
    override fun onDestroy() {
        adWhaleMediationRewardAd?.destroy()
        adWhaleMediationRewardAd = null
        super.onDestroy()
    }
}

@Composable
fun RewardSampleScreen() {
    // 샘플 UI – 실제 가이드에서는 "보상형 광고 샘플" 정도의 안내 텍스트만 보여줘도 충분
    Text(text = "AdWhale 보상형 광고 샘플 (Compose)")
}
```
{% endtab %}
{% endtabs %}





























