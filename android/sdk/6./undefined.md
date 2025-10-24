# 배너

{% hint style="info" %}
&#x20;ADwhale Mediation 배너 광고는 Programmatic 방식과 XML 방식 2가지 형태의 구현과 사용이 가능합니다.&#x20;
{% endhint %}

### **배너뷰 생성**

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

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td>디바이스 width 입력</td></tr></tbody></table>

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



**배너 구현 샘플은 아래와 같습니다.**&#x20;

**2가지 방식으로 배너 구현을 지원합니다(Programmatic 방식, XML 방식).**

> Programmatic 방식

<pre class="language-java"><code class="lang-java">import androidx.appcompat.app.AppCompatActivity;
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

<strong>    private AdWhaleMediationAdView adWhaleMediationAdView;
</strong>
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
        // adWhaleMediationAdView.setAdaptiveAnchorWidth(360); // 디바이스 width 입력
        
        
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
</code></pre>

> XML 방식

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

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>디바이스 width 입력</td></tr></tbody></table>

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



**배너 구현 샘플은 아래와 같습니다.**&#x20;

**2가지 방식으로 배너 구현을 지원합니다(Programmatic 방식, XML 방식).**

> Programmatic 방식

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
        // adWhaleMediationAdView.setAdaptiveAnchorWidth(360) // 디바이스 width 입력
        
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

> XML 방식

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

