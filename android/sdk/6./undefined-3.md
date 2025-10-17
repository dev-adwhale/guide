# 네이티브

네이티브 광고 생성

{% tabs %}
{% tab title="Java" %}


**AdWhaleMediationNativeAdView 클래스 API 설명**

```java
public AdWhaleMediationNativeAdView(Context context)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.content.Context</td><td>Android Main Activity 클래스</td></tr></tbody></table>

```java
public void setAdWhaleMediationNativeAdViewListener(AdWhaleMediationNativeAdViewListener listener)
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationNativeAdViewListener</p></td><td>네이티브 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```java
public void loadAdWithBinder(AdWhaleNativeAdBinder binder) // 미디에이션 커스텀 바인딩 네이티브 광고로드 시 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleNativeAdBinder</p></td><td>커스텀 바인딩 네이티브 광고 로드를 위한 바인더</td></tr></tbody></table>

```java
public void loadAdWithTemplate(ADWHALE_NATIVE_TEMPLATE template) // 미디에이션 고정 템플릿 네이티브 광고로드 시 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>ADWHALE_NATIVE_TEMPLATE</p></td><td>고정 템플릿 네이티브 광고 사이즈(사이즈 종류: SMALL, MEDIUM, FULLSCREEN) </td></tr></tbody></table>

```java
public void setTemplateStyle(AdWhaleMediationNativeTemplateStyle style) // 고정 템플릿 네이티브 광고 요소에 스타일 적
```

| 파라미터 타입                                                           | 파라미터 값                             |
| ----------------------------------------------------------------- | ---------------------------------- |
| net.adwhale.sdk.mediation.ads.AdWhaleMediationNativeTemplateStyle | (Optional) 고정 템플릿 네이티브 이용 시 스타일 적용 |

```java
public void setPlacementUid(String placementUid) // 지면 등록
```

| 파라미터 타입 | 파라미터 값                |
| ------- | --------------------- |
| String  | placementUid 값(발급 필요) |

```java
public void setDebugEnabled(boolean isDebugEnabled) // 광고검사기 활성/비활성
```

| 파라미터 타입 | 파라미터 값                     |
| ------- | -------------------------- |
| Boolean | 개발 모드에서 네이티브 광고 검사기 활성/비활성 |

```java
public void show() // 네이티브 광고 표시
```

<pre class="language-java"><code class="lang-java"><strong>public void resume() // resume 콜백 시 호출필요
</strong></code></pre>

```java
public void pause() // pause 콜백 시 호출필요
```

```java
public void destroy() // onDestroy 콜백 시 호출필요
```



**AdWhaleMediationNativeAdViewListener 클래스 API 설명**

```java
public void onNativeAdLoaded() // 미디에이션 네이티브 광고로드 성공 시
```

```java
public void onNativeAdFailedToLoad(int statusCode, String message) // 미디에이션 네이티브 광고로드 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>광고로드 결과 코드</td></tr><tr><td>String</td><td>광고로드 결과 메시지</td></tr></tbody></table>

```java
public void onNativeAdShowFailed(int statusCode, String message) // 미디에이션 네이티브 광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>광고표시 결과 코드</td></tr><tr><td>String</td><td>광고표시 결과 메시지</td></tr></tbody></table>

```java
public void onNativeAdClicked() // 미디에이션 네이티브 광고클릭 시
```

```java
public void onNativeAdClosed() // 미디에이션 보상형 광고닫기 시
```



**AdWhaleNativeAdBinder.Builder 클래스 API 설명**

사용자는 2가지 형태의 네이티브 광고를 호출 및 표시할 수 있습니다 :&#x20;

* 커스텀 바인딩 네이티브 광고와&#x20;
* 고정형 템플릿 네이티브 광고

커스텀 바인딩 네이티브 광고는 사용자가 직접 네이티브 광고 레이아웃을 정의하여 사용합니다.

커스텀 바인딩 네이티브 광고를 로드할 때(**loadAdWithBinder 메서드**) 함께 사용하는 API 입니다.

```java
public Builder(Context context, int layoutResId) // 커스텀 바인딩 네이티브 광고 로드를 위한 바인더 빌더
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.content.Context</td><td>Android Main Activity 클래스</td></tr><tr><td>Int</td><td>사용자가 직접 정의한 커스텀 네이티브 레이아웃 resoure id</td></tr></tbody></table>

```java
public AdWhaleNativeAdBinder.Builder setIconViewId(int IconViewResId) // 사용자가 직접 정의한 아이콘 뷰 설정
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>사용자가 직접 정의한 아이콘 뷰 resoure id</td></tr></tbody></table>

```java
public AdWhaleNativeAdBinder.Builder setTitleViewID(int titleViewResId) // 사용자가 직접 정의한 타이틀 뷰 설정
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>사용자가 직접 정의한 타이틀 뷰 resoure id</td></tr></tbody></table>

```java
public AdWhaleNativeAdBinder.Builder setBodyTextViewId(int bodyTextViewResId) // 사용자가 직접 정의한 바디 텍스트 설명 뷰 설정
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>사용자가 직접 정의한 바디 텍스트 설명 뷰 resoure id</td></tr></tbody></table>

```java
public AdWhaleNativeAdBinder.Builder setCallToActionViewId(int callToActionViewResId) // 사용자가 직접 정의한 CTA 버튼 설정
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>사용자가 직접 정의한 CTA 버튼 resoure id</td></tr></tbody></table>

```java
public AdWhaleNativeAdBinder.Builder setMediaViewGroupId(int mediaViewGroupResId) // 사용자가 직접 정의한 미디어 뷰 그룹 설정
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>사용자가 직접 정의한 미디어 뷰 그룹 resoure id</td></tr></tbody></table>

```java
public AdWhaleNativeAdBinder build() // AdWhaleNativeAdBinder 객체를 리턴
```



**AdWhaleMediationNativeTemplateStyle.Builder 클래스 주요 API 설명**

고정형 템플릿 네이티브 광고는 ADwhale에서 제공하는 사이즈 별 템플릿 (SMALL, MEDIUM, FULLSCREEN)을 사용합니다.

이 때, 사용자 앱의 look & feel 에 맞추어 템플릿의 요소들에 스타일을 변경 적용할 수 있습니다.

고정형 템플릿 네이티브 광고를 로드할 때(**loadAdWithTemplate 메서드**) 함께 사용하는 API 입니다.

```java
public Builder() // 고정형 템플릿 네이티브 광고 스타일 적용을 위한 옵션 빌더
```

```java
public AdWhaleMediationNativeTemplateStyle.Builder withMainBackgroundColor(ColorDrawable colorDrawable) // 고정형 템플릿 메인 색상 선택
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.graphics.drawable.ColorDrawable</td><td>컬러 색상 값</td></tr></tbody></table>

```java
public AdWhaleMediationNativeTemplateStyle.Builder withPrimaryTextTypefaceColor(int color) // 헤드라인 뷰 텍스트 색상 설정
public AdWhaleMediationNativeTemplateStyle.Builder withSecondaryTextTypefaceColor(int color) // 세컨더리 뷰 텍스트 색상 설정
public AdWhaleMediationNativeTemplateStyle.Builder withTertiaryTextTypefaceColor(int color) // 바디 텍스트 뷰 텍스트 색상 설정
public AdWhaleMediationNativeTemplateStyle.Builder withCallToActionTypefaceColor(int color) // CTA 버튼 텍스트 색상 설정
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>텍스트 색상 값</td></tr></tbody></table>

```java
public AdWhaleMediationNativeTemplateStyle.Builder withPrimaryTextSize(float size) // 헤드라인 뷰 텍스트 사이즈 설정
public AdWhaleMediationNativeTemplateStyle.Builder withSecondaryTextSize(float size) // 세컨더리 뷰 텍스트 사이즈 설정
public AdWhaleMediationNativeTemplateStyle.Builder withTertiaryTextSize(float size) // 바디 텍스트 뷰 텍스트 사이즈 설정
public AdWhaleMediationNativeTemplateStyle.Builder withCallToActionTextSize(float size) // CTA 버튼 텍스트 사이즈 설정
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Float</td><td>텍스트 사이즈 값</td></tr></tbody></table>

```java
public AdWhaleMediationNativeTemplateStyle.Builder withPrimaryTextTypeface(Typeface typeface) // 헤드라인 뷰 텍스트 폰트 설정
public AdWhaleMediationNativeTemplateStyle.Builder withSecondaryTextTypeface(Typeface typeface) // 세컨더리 뷰 텍스트 폰트 설정
public AdWhaleMediationNativeTemplateStyle.Builder withTertiaryTextTypeface(Typeface typeface) // 바디 텍스트 뷰 텍스트 폰트 설정
public AdWhaleMediationNativeTemplateStyle.Builder withCallToActionTextTypeface(Typeface typeface) // CTA 버튼 텍스트 폰트 설정
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.graphics.Typeface</td><td>텍스트 폰트 값</td></tr></tbody></table>

```java
public AdWhaleMediationNativeTemplateStyle.Builder withPrimaryTextBackgroundColor(ColorDrawable colorDrawable) // 헤드라인 뷰 텍스트 백그라운드 색상 설정
public AdWhaleMediationNativeTemplateStyle.Builder withSecondaryTextBackgroundColor(ColorDrawable colorDrawable) // 세컨더리 뷰 텍스트 백그라운드 색상 설정
public AdWhaleMediationNativeTemplateStyle.Builder withTertiaryTextBackgroundColor(ColorDrawable colorDrawable) // 바디 텍스트 뷰 텍스트 백그라운드 색상 설정
public AdWhaleMediationNativeTemplateStyle.Builder withCallToActionBackgroundColor(ColorDrawable colorDrawable) // CTA 버튼 백그라운드 색상 설정
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.graphics.drawable.ColorDrawable</td><td>컬러 색상 값</td></tr></tbody></table>

```java
public AdWhaleMediationNativeTemplateStyle build() // AdWhaleMediationNativeTemplateStyle 객체를 리턴
```



**네이티브 구현 샘플은 아래와 같습니다.**&#x20;

```java
import androidx.appcompat.app.AppCompatActivity;

import android.graphics.Color;
import android.graphics.Typeface;
import android.graphics.drawable.ColorDrawable;
import android.os.Bundle;
import android.util.Log;

import net.adwhale.sdk.mediation.ads.ADWHALE_NATIVE_TEMPLATE;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationNativeAdView;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationNativeAdViewListener;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationNativeTemplateStyle;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener;
import net.adwhale.sdk.mediation.ads.AdWhaleNativeAdBinder;
import net.adwhale.sdk.utils.AdWhaleLog;

public class MainActivity extends AppCompatActivity {

    private AdWhaleMediationNativeAdView adWhaleMediationNativeAdView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None);
        
        // 초기화 코드
        AdWhaleMediationAds.init(this, AdWhaleMediationOnInitCompleteListener {
            statusCode, message ->
                Log.i(MainActivity::class.simpleName, ".onInitComplete($statusCode, $message)")
        })

        // 네이티브 광고 생성
        adWhaleMediationNativeAdView = AdWhaleMediationNativeAdView(this)
        
        // 네이티브 광고 지면 설정
        adWhaleMediationNativeAdView.setPlacementUid("발급 받은 placement uid 값");
        
        // 네이티브 콜백 리스너 등록
        adWhaleMediationNativeAdView.setAdWhaleMediationNativeAdViewListener(new AdWhaleMediationNativeAdViewListener() {
            @Override
            public void onNativeAdLoaded() {
                Log.i(MainActivity.class.getSimpleName(), ".onAdClicked();");
                // 네이티브 광고 표시
                adWhaleMediationNativeAdView.show();
            }

            @Override
            public void onNativeAdFailedToLoad(int errorCode, String errorMessage) {
                Log.e(MainActivity.class.getSimpleName(), ".onNativeAdFailedToLoad(" + errorCode + ", " + errorMessage + ")");
            }

            @Override
            public void onNativeAdShowFailed(int errorCode, String errorMessage) {
                Log.e(MainActivity.class.getSimpleName(), ".onNativeAdShowFailed(" + errorCode + ", " + errorMessage + ")");
            }

            @Override
            public void onNativeAdClicked() {
                Log.i(MainActivity.class.getSimpleName(), ".onNativeAdClicked()");
            }

            @Override
            public void onNativeAdClosed() {
                Log.i(MainActivity.class.getSimpleName(), ".onNativeAdClosed()");
            }
        });


        // 네이티브 광고 로드 방법1(택1): 커스텀 바인딩으로 네이티브 광고 로드
        // custom native AdBinder 생성(custom_native_ad_layout.xml)
        AdWhaleNativeAdBinder adWhaleNativeAdBinder = new AdWhaleNativeAdBinder.Builder(this, R.layout.custom_native_ad_layout)
                .setIconViewId(R.id.view_icon)
                .setTitleViewId(R.id.view_title)
                .setBodyTextViewId(R.id.view_body)
                .setCallToActionViewId(R.id.button_cta)
                .setMediaViewGroupId(R.id.view_media)
                .build();
        
        // 커스텀 바인딩으로 광고 로드
        adWhaleMediationNativeAdView.loadAdWithBinder(adWhaleNativeAdBinder);
        
        
        // 네이티브 광고 로드 방법2(택1): ADwhale에서 제공하는 고정 템플릿으로 네이티브 광고 로드         
        // 고정 템플릿 요소들을 사용자 앱의 look&feel 에 맞게 스타일 적용
        AdWhaleMediationNativeTemplateStyle style = new AdWhaleMediationNativeTemplateStyle.Builder()
                .withMainBackgroundColor(new ColorDrawable(Color.parseColor("#2C2C2C")))
                .withPrimaryTextTypefaceColor(Color.WHITE)
                .withPrimaryTextTypeface(Typeface.DEFAULT_BOLD)
                .withPrimaryTextBackgroundColor(new ColorDrawable(Color.parseColor("#4CAF50")))
                .withPrimaryTextSize(15.0f)
                .build();
        adWhaleMediationNativeAdView.setTemplateStyle(style);
        
        // 고정 템플릿으로 광고 로드
        adWhaleMediationNativeAdView.loadAdWithTemplate(ADWHALE_NATIVE_TEMPLATE.SMALL); // ADWHALE_NATIVE_TEMPLATE.MEDIUM, ADWHALE_NATIVE_TEMPLATE.FULLSCREEN
        
    }

    // 라이프사이클 onResume 콜백 시 반드시 onResume 호출 필요
    @Override
    protected void onResume() {
        super.onResume();
        if (adWhaleMediationNativeAdView != null) {
            adWhaleMediationNativeAdView.resume();
        }
    }

    // 라이프사이클 onPause 콜백 시 반드시 onPause 호출 필요
    @Override
    protected void onPause() {
        super.onPause();
        if (adWhaleMediationNativeAdView != null) {
            adWhaleMediationNativeAdView.pause();
        }
    }
        
    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy 호출 필요
    @Override
    protected void onDestroy() {
        super.onDestroy();
        if(adWhaleMediationNativeAdView != null) {
            adWhaleMediationNativeAdView.destroy();
        }
    }    
}

```

> 사용자 지정 커스텀 네이티브 레이아웃 예시(custom\_native\_ad\_layout.xml)

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:padding="16dp"
    android:background="@android:color/white"
    android:elevation="4dp">

    <!-- 앱 아이콘 -->
    <ImageView
        android:id="@+id/view_icon"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:layout_marginEnd="12dp"
        android:scaleType="centerCrop"
        app:layout_constraintBottom_toBottomOf="@id/view_title"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="@id/view_title" />

    <!-- 제목 -->
    <TextView
        android:id="@+id/view_title"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:textSize="16sp"
        android:textStyle="bold"
        android:textColor="@android:color/black"
        android:ellipsize="end"
        android:maxLines="2"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@id/view_icon"
        app:layout_constraintTop_toTopOf="parent"
        tools:text="광고 제목입니다" />

    <!-- 설명 -->
    <TextView
        android:id="@+id/view_body"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="4dp"
        android:textSize="14sp"
        android:textColor="@android:color/darker_gray"
        android:ellipsize="end"
        android:lines="2"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="@id/view_title"
        app:layout_constraintTop_toBottomOf="@id/view_title"
        tools:text="광고 설명입니다. 이 앱을 다운로드하세요!" />

    <!-- CTA 버튼 -->
    <Button
        android:id="@+id/button_cta"
        android:layout_width="wrap_content"
        android:layout_height="36dp"
        android:layout_marginTop="8dp"
        android:textSize="12sp"
        android:backgroundTint="#009688"
        android:textColor="@android:color/white"
        android:text="설치하기"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toBottomOf="@id/view_body" />

    <!-- 미디어 뷰 -->
    <FrameLayout
        android:id="@+id/view_media"
        android:layout_width="match_parent"
        android:layout_height="300dp"
        android:layout_marginTop="12dp"
        app:layout_constraintTop_toBottomOf="@id/button_cta" />

</androidx.constraintlayout.widget.ConstraintLayout>

```
{% endtab %}

{% tab title="Kotlin" %}
**AdWhaleMediationNativeAdView 클래스 API 설명**

```kotlin
AdWhaleMediationNativeAdView(Context context)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.content.Context</td><td>Android Main Activity 클래스</td></tr></tbody></table>

```kotlin
fun setAdWhaleMediationNativeAdViewListener(listener : AdWhaleMediationNativeAdViewListener) : Unit
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationNativeAdViewListener</p></td><td>네이티브 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```kotlin
fun loadAdWithBinder(binder : AdWhaleNativeAdBinder) : Unit // 미디에이션 커스텀 바인딩 네이티브 광고로드 시 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleNativeAdBinder</p></td><td>커스텀 바인딩 네이티브 광고 로드를 위한 바인더</td></tr></tbody></table>

```kotlin
fun loadAdWithTemplate(template : ADWHALE_NATIVE_TEMPLATE) : Unit // 미디에이션 고정 템플릿 네이티브 광고로드 시 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>ADWHALE_NATIVE_TEMPLATE</p></td><td>고정 템플릿 네이티브 광고 사이즈(사이즈 종류: SMALL, MEDIUM, FULLSCREEN) </td></tr></tbody></table>

```kotlin
fun setTemplateStyle(style : AdWhaleMediationNativeTemplateStyle) : Unit // 고정 템플릿 네이티브 광고 요소에 스타일 적
```

| 파라미터 타입                                                           | 파라미터 값                             |
| ----------------------------------------------------------------- | ---------------------------------- |
| net.adwhale.sdk.mediation.ads.AdWhaleMediationNativeTemplateStyle | (Optional) 고정 템플릿 네이티브 이용 시 스타일 적용 |

```kotlin
fun setPlacementUid(placementUid : String) : Unit // 지면 등록
```

| 파라미터 타입 | 파라미터 값                |
| ------- | --------------------- |
| String  | placementUid 값(발급 필요) |

```kotlin
fun setDebugEnabled(isDebugEnabled : boolean) : Unit // 광고검사기 활성/비활성
```

| 파라미터 타입 | 파라미터 값                     |
| ------- | -------------------------- |
| Boolean | 개발 모드에서 네이티브 광고 검사기 활성/비활성 |

```kotlin
fun show() : Unit // 네이티브 광고 표시
```

<pre class="language-kotlin"><code class="lang-kotlin"><strong>fun resume() : Unit // resume 콜백 시 호출필요
</strong></code></pre>

```kotlin
fun pause() : Unit // pause 콜백 시 호출필요
```

```kotlin
fun destroy() : Unit // onDestroy 콜백 시 호출필요
```



**AdWhaleMediationNativeAdViewListener 클래스 API 설명**

```kotlin
fun onNativeAdLoaded() : Unit // 미디에이션 네이티브 광고로드 성공 시
```

```kotlin
fun onNativeAdFailedToLoad(statusCode : Int, message : String) : Unit // 미디에이션 네이티브 광고로드 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>광고로드 결과 코드</td></tr><tr><td>String</td><td>광고로드 결과 메시지</td></tr></tbody></table>

```kotlin
fun onNativeAdShowFailed(statusCode : Int, message : String) : Unit // 미디에이션 네이티브 광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>광고표시 결과 코드</td></tr><tr><td>String</td><td>광고표시 결과 메시지</td></tr></tbody></table>

```kotlin
fun onNativeAdClicked() : Unit // 미디에이션 네이티브 광고클릭 시
```

```kotlin
fun onNativeAdClosed() : Unit // 미디에이션 보상형 광고닫기 시
```



**AdWhaleNativeAdBinder.Builder 클래스 API 설명**

사용자는 2가지 형태의 네이티브 광고를 호출 및 표시할 수 있습니다 :&#x20;

* 커스텀 바인딩 네이티브 광고와&#x20;
* 고정형 템플릿 네이티브 광고

커스텀 바인딩 네이티브 광고는 사용자가 직접 네이티브 광고 레이아웃을 정의하여 사용합니다.

커스텀 바인딩 네이티브 광고를 로드할 때(**loadAdWithBinder 메서드**) 함께 사용하는 API 입니다.

```kotlin
Builder(context : Context, layoutResId : Int) // 커스텀 바인딩 네이티브 광고 로드를 위한 바인더 빌더
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.content.Context</td><td>Android Main Activity 클래스</td></tr><tr><td>Int</td><td>사용자가 직접 정의한 커스텀 네이티브 레이아웃 resoure id</td></tr></tbody></table>

```kotlin
fun setIconViewId(IconViewResId : Int) : AdWhaleNativeAdBinder.Builder // 사용자가 직접 정의한 아이콘 뷰 설정
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>사용자가 직접 정의한 아이콘 뷰 resoure id</td></tr></tbody></table>

```kotlin
fun setTitleViewID(titleViewResId : Int) : AdWhaleNativeAdBinder.Builder // 사용자가 직접 정의한 타이틀 뷰 설정
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>사용자가 직접 정의한 타이틀 뷰 resoure id</td></tr></tbody></table>

```kotlin
fun setBodyTextViewId(bodyTextViewResId : Int) : AdWhaleNativeAdBinder.Builder // 사용자가 직접 정의한 바디 텍스트 설명 뷰 설정
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>사용자가 직접 정의한 바디 텍스트 설명 뷰 resoure id</td></tr></tbody></table>

```kotlin
fun setCallToActionViewId(callToActionViewResId : Int) : AdWhaleNativeAdBinder.Builder // 사용자가 직접 정의한 CTA 버튼 설정
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>사용자가 직접 정의한 CTA 버튼 resoure id</td></tr></tbody></table>

```kotlin
fun setMediaViewGroupId(mediaViewGroupResId : Int) : AdWhaleNativeAdBinder.Builder // 사용자가 직접 정의한 미디어 뷰 그룹 설정
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>사용자가 직접 정의한 미디어 뷰 그룹 resoure id</td></tr></tbody></table>

```kotlin
fun build() : AdWhaleNativeAdBinder // AdWhaleNativeAdBinder 객체를 리턴
```



**AdWhaleMediationNativeTemplateStyle.Builder 클래스 주요 API 설명**

고정형 템플릿 네이티브 광고는 ADwhale에서 제공하는 사이즈 별 템플릿 (SMALL, MEDIUM, FULLSCREEN)을 사용합니다.

이 때, 사용자 앱의 look & feel 에 맞추어 템플릿의 요소들에 스타일을 변경 적용할 수 있습니다.

고정형 템플릿 네이티브 광고를 로드할 때(**loadAdWithTemplate 메서드**) 함께 사용하는 API 입니다.

```kotlin
Builder() // 고정형 템플릿 네이티브 광고 스타일 적용을 위한 옵션 빌더
```

```kotlin
fun withMainBackgroundColor(colorDrawable : ColorDrawable) : AdWhaleMediationNativeTemplateStyle.Builder // 고정형 템플릿 메인 색상 선택
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.graphics.drawable.ColorDrawable</td><td>컬러 색상 값</td></tr></tbody></table>

```kotlin
fun withPrimaryTextTypefaceColor(color : Int) : AdWhaleMediationNativeTemplateStyle.Builder // 헤드라인 뷰 텍스트 색상 설정
fun withSecondaryTextTypefaceColor(color : Int) : AdWhaleMediationNativeTemplateStyle.Builder // 세컨더리 뷰 텍스트 색상 설정
fun withTertiaryTextTypefaceColor(color : Int) : AdWhaleMediationNativeTemplateStyle.Builder // 바디 텍스트 뷰 텍스트 색상 설정
fun withCallToActionTypefaceColor(color : Int) : AdWhaleMediationNativeTemplateStyle.Builder // CTA 버튼 텍스트 색상 설정
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>텍스트 색상 값</td></tr></tbody></table>

```kotlin
fun withPrimaryTextSize(size : Float) : AdWhaleMediationNativeTemplateStyle.Builder // 헤드라인 뷰 텍스트 사이즈 설정
fun withSecondaryTextSize(size : Float) : AdWhaleMediationNativeTemplateStyle.Builder // 세컨더리 뷰 텍스트 사이즈 설정
fun withTertiaryTextSize(size : Float) : AdWhaleMediationNativeTemplateStyle.Builder // 바디 텍스트 뷰 텍스트 사이즈 설정
fun withCallToActionTextSize(size : Float) : AdWhaleMediationNativeTemplateStyle.Builder // CTA 버튼 텍스트 사이즈 설정
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Float</td><td>텍스트 사이즈 값</td></tr></tbody></table>

```kotlin
fun withPrimaryTextTypeface(typeface : Typeface) : AdWhaleMediationNativeTemplateStyle.Builder // 헤드라인 뷰 텍스트 폰트 설정
fun withSecondaryTextTypeface(typeface : Typeface) : AdWhaleMediationNativeTemplateStyle.Builder // 세컨더리 뷰 텍스트 폰트 설정
fun withTertiaryTextTypeface(typeface : Typeface) : AdWhaleMediationNativeTemplateStyle.Builder // 바디 텍스트 뷰 텍스트 폰트 설정
fun withCallToActionTextTypeface(typeface : Typeface) : AdWhaleMediationNativeTemplateStyle.Builder // CTA 버튼 텍스트 폰트 설정
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.graphics.Typeface</td><td>텍스트 폰트 값</td></tr></tbody></table>

```kotlin
fun withPrimaryTextBackgroundColor(colorDrawable : ColorDrawable) : AdWhaleMediationNativeTemplateStyle.Builder // 헤드라인 뷰 텍스트 백그라운드 색상 설정
fun withSecondaryTextBackgroundColor(colorDrawable : ColorDrawable) : AdWhaleMediationNativeTemplateStyle.Builder // 세컨더리 뷰 텍스트 백그라운드 색상 설정
fun withTertiaryTextBackgroundColor(colorDrawable : ColorDrawable) : AdWhaleMediationNativeTemplateStyle.Builder // 바디 텍스트 뷰 텍스트 백그라운드 색상 설정
fun withCallToActionBackgroundColor(colorDrawable : ColorDrawable) : AdWhaleMediationNativeTemplateStyle.Builder // CTA 버튼 백그라운드 색상 설정
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.graphics.drawable.ColorDrawable</td><td>컬러 색상 값</td></tr></tbody></table>

```kotlin
fun build() : AdWhaleMediationNativeTemplateStyle // AdWhaleMediationNativeTemplateStyle 객체를 리턴
```



**네이티브 구현 샘플은 아래와 같습니다.**&#x20;

```kotlin
import androidx.appcompat.app.AppCompatActivity;

import android.graphics.Color;
import android.graphics.Typeface;
import android.graphics.drawable.ColorDrawable;
import android.os.Bundle;
import android.util.Log;

import net.adwhale.sdk.mediation.ads.ADWHALE_NATIVE_TEMPLATE;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAds;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationNativeAdView;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationNativeAdViewListener;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationNativeTemplateStyle;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener;
import net.adwhale.sdk.mediation.ads.AdWhaleNativeAdBinder;
import net.adwhale.sdk.utils.AdWhaleLog;

public class MainActivity : AppCompatActivity() {

    private lateinit var adWhaleMediationNativeAdView : AdWhaleMediationNativeAdView;

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

        // 네이티브 광고 생성
        adWhaleMediationNativeAdView = new AdWhaleMediationNativeAdView(this);
        
        // 네이티브 광고 지면 설정
        adWhaleMediationNativeAdView!!.setPlacementUid("발급 받은 placement uid 값")
        
        // 네이티브 콜백 리스너 등록
        adWhaleMediationNativeAdView!!.setAdWhaleMediationNativeAdViewListener(object :
            AdWhaleMediationNativeAdViewListener {
            override fun onNativeAdLoaded() {
                Log.i(MainActivity::class.simpleName, ".onNativeAdLoaded()")
                // 네이티브 광고 표시
                adWhaleMediationNativeAdView.show()
            }

            override fun onNativeAdFailedToLoad(errorCode: Int, errorMessage: String?) {
                Log.e(MainActivity::class.simpleName, ".onNativeAdFailedToLoad(" + errorCode + ", " + errorMessage + ")")
            }

            override fun onNativeAdShowFailed(errorCode: Int, errorMessage: String?) {
                Log.e(MainActivity::class.simpleName, ".onNativeAdShowFailed(" + errorCode + ", " + errorMessage + ")")
            }

            override fun onNativeAdClicked() {
                Log.i(MainActivity::class.simpleName, ".onNativeAdClicked()")
            }

            override fun onNativeAdClosed() {
                Log.i(MainActivity::class.simpleName, ".onNativeAdClosed()")
            }
        })

        // 네이티브 광고 로드 방법1(택1): 커스텀 바인딩으로 네이티브 광고 로드
        // custom native AdBinder 생성(custom_native_ad_layout.xml)
        val adWhaleNativeAdBinder =
            AdWhaleNativeAdBinder.Builder(this, R.layout.custom_native_ad_layout)
                .setIconViewId(R.id.view_icon)
                .setTitleViewId(R.id.view_title)
                .setBodyTextViewId(R.id.view_body)
                .setCallToActionViewId(R.id.button_cta)
                .setMediaViewGroupId(R.id.view_media)
                .build()
        
        // 커스텀 바인딩으로 광고 로드
        adWhaleMediationNativeAdView!!.loadAdWithBinder(adWhaleNativeAdBinder)
        
        
        // 네이티브 광고 로드 방법2(택1): ADwhale에서 제공하는 고정 템플릿으로 네이티브 광고 로드         
        // 고정 템플릿 요소들을 사용자 앱의 look&feel 에 맞게 스타일 적용                
        val style = AdWhaleMediationNativeTemplateStyle.Builder()
            .withMainBackgroundColor(new ColorDrawable(Color.parseColor("#2C2C2C")))
            .withPrimaryTextTypefaceColor(Color.WHITE)
            .withPrimaryTextTypeface(Typeface.DEFAULT_BOLD)
            .withPrimaryTextBackgroundColor(new ColorDrawable(Color.parseColor("#4CAF50")))
            .withPrimaryTextSize(15.0f)
            .build()                
        adWhaleMediationNativeAdView!!.setTemplateStyle(style)
        
        // 고정 템플릿으로 광고 로드
        adWhaleMediationNativeAdView!!.loadAdWithTemplate(ADWHALE_NATIVE_TEMPLATE.SMALL) // ADWHALE_NATIVE_TEMPLATE.MEDIUM, ADWHALE_NATIVE_TEMPLATE.FULLSCREEN
        
    }

    }
    
    // 라이프사이클 onResume 콜백 시 반드시 onResume 호출 필요
    override fun onResume() {
        super.onResume()
        if (adWhaleMediationNativeAdView != null) {
            adWhaleMediationNativeAdView!!.resume()
        }
    }

    // 라이프사이클 onPause 콜백 시 반드시 onPause 호출 필요
    override fun onPause() {
        super.onPause()
        if (adWhaleMediationNativeAdView != null) {
            adWhaleMediationNativeAdView!!.pause()
        }
    }
        
    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy 호출 필요
    override fun onDestroy() {
        super.onDestroy()
        if (adWhaleMediationNativeAdView != null) {
            adWhaleMediationNativeAdView!!.destroy()
        }
    }
}
```
{% endtab %}
{% endtabs %}
