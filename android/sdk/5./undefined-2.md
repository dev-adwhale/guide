# 보상형

보상형 생성

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
public void loadAd(AdWhaleMediationRewardAdLoadCallback listener) // 미디에이션 보상형광고로드 시 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationRewardAdLoadCallback</p></td><td>보상형 미디에이션 광고 로드 콜백 리스너</td></tr></tbody></table>

```java
public void showAd(AdWhaleMediationUserEarnedRewardListener listener) // 미디에이션 보상형광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationUserEarnedRewardListener</p></td><td>보상형 미디에이션 광고 리워드 지급 콜백 리스너</td></tr></tbody></table>

```java
public void destroy() // onDestroy() 시 호출
```



**AdWhaleMediationFullScreenContentCallback 클래스 API 설명**

```java
public void onAdClicked() // 미디에이션 보상형광고클릭 시
```

```java
public void onAdDismissed() // 미디에이션 보상형광고닫기 시
```

```java
public void onFailedToShow(int statusCode, String message) // 미디에이션 보상형광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>광고표시 결과 코드</td></tr><tr><td>String</td><td>초기화 결과 메시지</td></tr></tbody></table>

```java
public void onAdShowed() // 미디에이션 보상형광고표시 후
```



**AdWhaleMediationRewardAdLoadCallback 클래스 API 설명**

```java
public void onAdLoaded(AdWhaleMediationRewardAd adWhaleMediationRewardAd, String message) // 미디에이션 보상형광고로드 성공 시
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>AdWhaleMediationRewardAd</td><td>AdWhaleMediationRewardAd 객체</td></tr><tr><td>String</td><td>광고로드 결과 메시지</td></tr></tbody></table>

```java
public void onFailedToShow(int statusCode, String message) // 미디에이션 보상형광고로드 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>광고표시 결과 코드</td></tr><tr><td>String</td><td>광고표시 결과 메시지</td></tr></tbody></table>



**AdWhaleMediationUserEarnedRewardListener 클래스 API 설명**

```java
public void onUserRewarded(AdWhaleMediationRewardItem rewardItem) // 미디에이션 보상형리워드지급 성공 시
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>AdWhaleMediationRewardtem</td><td>리워드 타입, 리워드 금액 값을 지니는 객체</td></tr></tbody></table>



**보상형 구현 샘플은 아래와 같습니다.**&#x20;

```java
import android.os.Bundle;
import android.util.Log;
import android.widget.Button;
import android.widget.EditText;

import androidx.appcompat.app.AppCompatActivity;

import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationFullScreenContentCallback;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardAd;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardedAdLoadCallback;

public class MainActivity extends AppCompatActivity {

    private AdWhaleMediationRewardAd adWhaleMediationRewardAd;

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

        adWhaleMediationRewardAd = new AdWhaleMediationRewardAd("발급받은 placement uid 값");
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

        adWhaleMediationRewardAd.loadAd(new AdWhaleMediationRewardedAdLoadCallback() {
            @Override
            public void onAdLoaded(AdWhaleMediationRewardAd adWhaleMediationRewardAd, String message) {
                Log.i(MainActivity.class.getSimpleName(), ".onAdLoaded(" + message + ")");
                
                if(adWhaleMediationRewardAd != null) {
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
import android.widget.Button;
import android.widget.EditText;

import androidx.appcompat.app.AppCompatActivity;

import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationFullScreenContentCallback;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardAd;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardedAdLoadCallback;

public class MainActivity : AppCompatActivity() {

    private lateinit var adWhaleMediationRewardAd : AdWhaleMediationRewardAd;

    override fun onCreate(savedInstanceState : Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        AdWhaleMediationAds.init(this, AdWhaleMediationOnInitCompleteListener {
            statusCode, message ->
                Log.i(MainActivity::class.simpleName, ".onInitComplete($statusCode, $message)")
        })

        adWhaleMediationRewardAd = AdWhaleMediationRewardAd("발급받은 placement uid 값")
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
        
        adWhaleMediationRewardAd.loadAd(object : AdWhaleMediationRewardedAdLoadCallback {
            
            override fun onAdLoaded(adWhaleMediationRewardAd : AdWhaleMediationRewardAd, message : String) {
                Log.i(MainActivity.class.getSimpleName(), ".onAdLoaded(${message})");
                adWhaleMediationRewardAd.showAd(adWhaleMediationRewardItem -> {
                    Log.i(MainActivity.class.getSimpleName(), ".onUserRewarded(${adWhaleMediationRewardItem.toString()})");
                });                 
            }

            override fun onAdFailedToLoad(statusCode : Int, message : String) {
                Log.i(MainActivity.class.getSimpleName(), ".onAdFailedToLoad(" + statusCode + ", " + message + ")");
            }
        })

    }
    
    override fun onDestroy() {
        super.onDestroy()
        adWhaleMediationRewardAd.destroy()
    }
}
```
{% endtab %}
{% endtabs %}





























