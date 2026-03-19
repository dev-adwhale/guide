# 네이티브(수정)

{% hint style="info" %}
네이티브 광고는 앱의 콘텐츠와 자연스럽게 어우러지는 광고 형식입니다. 앱의 디자인과 일치하도록 커스터마이징할 수 있어 사용자 경험을 해치지 않으면서 높은 참여도를 얻을 수 있습니다.
{% endhint %}

#### **1. 주요특징**

* 앱 콘텐츠와 자연스럽게 통합
* **커스텀 바인딩**과 **고정 템플릿** 두 가지 방식 지원
* 템플릿 타입: SMALL, MEDIUM, FULLSCREEN
* 스타일(색상·폰트·크기 등) 커스터마이징 가능

#### 2. 네이티브 광고 타입 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

네이티브 광고는 두 가지 방식으로 구현할 수 있습니다:

{% hint style="info" %}
**템플릿 네이티브 광고**&#x20;

```java
// SMALL 네이티브 템플릿 적용 예시코드
AdWhaleMediationNativeAdView nativeAdView = new AdWhaleMediationNativeAdView(this);
nativeAdView.loadAdWithTemplate(ADWHALE_NATIVE_TEMPLATE.SMALL);
```

* SDK에서 제공하는 템플릿 사용
* `SMALL`, `MEDIUM`, `FULLSCREEN` 타입 지원
* 스타일 커스터마이징 가능
{% endhint %}

{% hint style="info" %}
**커스텀 바인딩 네이티브 광고**

```java
// 커스텀 바인딩 네이티브 적용 예시코드
AdWhaleMediationNativeAdView nativeAdView = new AdWhaleMediationNativeAdView(this);
AdWhaleNativeAdBinder binder = 
    new AdWhaleNativeAdBinder.Builder(this, R.layout.custom_native_ad_layout)
        .setIconViewId(R.id.view_icon)
        .setTitleViewId(R.id.view_title)
        .setBodyTextViewId(R.id.view_body)
        .setCallToActionViewId(R.id.button_cta)
        .setMediaViewGroupId(R.id.view_media)
        .build();
nativeAdView.loadAdWithBinder(binder);
```

* 자체 레이아웃 사용
* 완전한 디자인 제어
* Android `AdWhaleNativeBinder`를 통합 커스텀 바인딩
{% endhint %}

#### 3. 템플릿 네이티브 광고 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

템플릿 네이티브 광고는 다음 세 가지 타입을 지원합니다:

<table><thead><tr><th width="139.9375">타입</th><th>값</th><th>설명</th></tr></thead><tbody><tr><td>SMALL</td><td><code>ADWHALE_NATIVE_TEMPLATE.small</code></td><td>작은 크기의 네이티브 광고</td></tr><tr><td>MEDIUM</td><td><code>ADWHALE_NATIVE_TEMPLATE.medium</code></td><td>중간 크기의 네이티브 광고</td></tr><tr><td>FULLSCREEN</td><td><code>ADWHALE_NATIVE_TEMPLATE.fullscreen</code></td><td>전체 화면 네이티브 광고</td></tr></tbody></table>

**템플릿 네이티브 광고 기본 구현 샘플코드**

{% tabs %}
{% tab title="Java" %}
```java
public class MainActivity extends AppCompatActivity {

    private AdWhaleMediationNativeAdView nativeAdView;
    private FrameLayout nativeAdContainer;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        nativeAdContainer = findViewById(R.id.nativeAdContainer);

        // 1. 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None);

        // 2. 초기화 코드
        AdWhaleMediationAds.init(this, new AdWhaleMediationOnInitCompleteListener() {
            @Override
            public void onInitComplete(int statusCode, String message) {
                Log.i("MainActivity", "onInitComplete(" + statusCode + ", " + message + ")");
                createAndLoadNativeTemplateAd();
            }
        });
    }

    private void createAndLoadNativeTemplateAd() {

        // 3. 인스턴스 생성
        nativeAdView = new AdWhaleMediationNativeAdView(this);
        nativeAdView.setPlacementUid("발급받은 placement uid 값");

        // 4. 리스너 등록
        nativeAdView.setAdWhaleMediationNativeAdViewListener(
                new AdWhaleMediationNativeAdViewListener() {

            @Override
            public void onNativeAdLoaded() {
                Log.i("MainActivity", "onNativeAdLoaded()");
                // 8. 광고 표시
                nativeAdView.show();
            }

            @Override
            public void onNativeAdFailedToLoad(int errorCode, String errorMessage) {
                Log.e("MainActivity",
                        "onNativeAdFailedToLoad(" + errorCode + ", " + errorMessage + ")");
            }

            @Override
            public void onNativeAdShowFailed(int errorCode, String errorMessage) {
                Log.e("MainActivity",
                        "onNativeAdShowFailed(" + errorCode + ", " + errorMessage + ")");
            }

            @Override
            public void onNativeAdClicked() {
                Log.i("MainActivity", "onNativeAdClicked()");
            }

            @Override
            public void onNativeAdClosed() {
                Log.i("MainActivity", "onNativeAdClosed()");
            }
        });

        // 5. 스타일 적용 (선택)
        AdWhaleMediationNativeTemplateStyle style =
                new AdWhaleMediationNativeTemplateStyle.Builder()
                        .withMainBackgroundColor(
                                new ColorDrawable(Color.parseColor("#2C2C2C")))
                        // 헤드라인(Primary)
                        .withPrimaryTextTypefaceColor(Color.WHITE)
                        .withPrimaryTextTypeface(Typeface.DEFAULT_BOLD)
                        .withPrimaryTextBackgroundColor(
                                new ColorDrawable(Color.parseColor("#4CAF50")))
                        .withPrimaryTextSize(15.0f)

                        // 세컨더리(Secondary)
                        .withSecondaryTextTypefaceColor(Color.parseColor("#CCCCCC"))
                        .withSecondaryTextTypeface(Typeface.DEFAULT)
                        .withSecondaryTextBackgroundColor(
                                new ColorDrawable(Color.TRANSPARENT))
                        .withSecondaryTextSize(13.0f)

                        // 바디(Tertiary)
                        .withTertiaryTextTypefaceColor(Color.parseColor("#AAAAAA"))
                        .withTertiaryTextTypeface(Typeface.DEFAULT)
                        .withTertiaryTextBackgroundColor(
                                new ColorDrawable(Color.TRANSPARENT))
                        .withTertiaryTextSize(12.0f)

                        // CTA 버튼
                        .withCallToActionTypefaceColor(Color.WHITE)
                        .withCallToActionTextTypeface(Typeface.DEFAULT_BOLD)
                        .withCallToActionTextSize(14.0f)
                        .withCallToActionBackgroundColor(
                                new ColorDrawable(Color.parseColor("#4CAF50")))
                        .build();

        nativeAdView.setTemplateStyle(style);

        // 6. addView 전에 기존 뷰 제거 (중복 방지)
        nativeAdContainer.removeAllViews();

        // LayoutParams 명시적으로 지정 (권장)
        FrameLayout.LayoutParams params =
                new FrameLayout.LayoutParams(
                        FrameLayout.LayoutParams.MATCH_PARENT,
                        FrameLayout.LayoutParams.WRAP_CONTENT
                );

        nativeAdContainer.addView(nativeAdView, params);

        // 7. 광고 로드
        nativeAdView.loadAdWithTemplate(ADWHALE_NATIVE_TEMPLATE.SMALL);
    }

    // 라이프사이클 onDestroy 콜백 시 반드시 onDestroy()가 있는 release() 호출 필요
    @Override
    protected void onDestroy() {
        if (nativeAdView != null) {
            nativeAdView.destroy();
            nativeAdView = null;
        }
        super.onDestroy();
    }
}
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    android:id="@+id/nativeContainer"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
class MainActivity : AppCompatActivity() {

    private var nativeAdView: AdWhaleMediationNativeAdView? = null
    private lateinit var nativeAdContainer: FrameLayout

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        nativeAdContainer = findViewById(R.id.nativeAdContainer)

        // 1. 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)

        // 2. 초기화 코드
        AdWhaleMediationAds.init(this, object : AdWhaleMediationOnInitCompleteListener {
            override fun onInitComplete(statusCode: Int, message: String) {
                Log.i("MainActivity", "onInitComplete($statusCode, $message)")
                createAndLoadNativeTemplateAd()
            }
        })
    }

    private fun createAndLoadNativeTemplateAd() {

        // 3. 인스턴스 생성
        nativeAdView = AdWhaleMediationNativeAdView(this).apply {

            setPlacementUid("발급받은 placement uid 값")

            // 4. 리스너 등록
            setAdWhaleMediationNativeAdViewListener(
                object : AdWhaleMediationNativeAdViewListener {

                    override fun onNativeAdLoaded() {
                        Log.i("MainActivity", "onNativeAdLoaded()")
                        // 8. 광고 표시
                        show()
                    }

                    override fun onNativeAdFailedToLoad(
                        errorCode: Int,
                        errorMessage: String?
                    ) {
                        Log.e(
                            "MainActivity",
                            "onNativeAdFailedToLoad($errorCode, $errorMessage)"
                        )
                    }

                    override fun onNativeAdShowFailed(
                        errorCode: Int,
                        errorMessage: String?
                    ) {
                        Log.e(
                            "MainActivity",
                            "onNativeAdShowFailed($errorCode, $errorMessage)"
                        )
                    }

                    override fun onNativeAdClicked() {
                        Log.i("MainActivity", "onNativeAdClicked()")
                    }

                    override fun onNativeAdClosed() {
                        Log.i("MainActivity", "onNativeAdClosed()")
                    }
                }
            )

            // 5. 스타일 적용 (선택)
            val style = AdWhaleMediationNativeTemplateStyle.Builder()
                .withMainBackgroundColor(
                    ColorDrawable(Color.parseColor("#2C2C2C"))
                )
                // Primary
                .withPrimaryTextTypefaceColor(Color.WHITE)
                .withPrimaryTextTypeface(Typeface.DEFAULT_BOLD)
                .withPrimaryTextBackgroundColor(
                    ColorDrawable(Color.parseColor("#4CAF50"))
                )
                .withPrimaryTextSize(15.0f)
                // Secondary
                .withSecondaryTextTypefaceColor(Color.parseColor("#CCCCCC"))
                .withSecondaryTextTypeface(Typeface.DEFAULT)
                .withSecondaryTextBackgroundColor(ColorDrawable(Color.TRANSPARENT))
                .withSecondaryTextSize(13.0f)
                // Tertiary
                .withTertiaryTextTypefaceColor(Color.parseColor("#AAAAAA"))
                .withTertiaryTextTypeface(Typeface.DEFAULT)
                .withTertiaryTextBackgroundColor(ColorDrawable(Color.TRANSPARENT))
                .withTertiaryTextSize(12.0f)
                // CTA
                .withCallToActionTypefaceColor(Color.WHITE)
                .withCallToActionTextTypeface(Typeface.DEFAULT_BOLD)
                .withCallToActionTextSize(14.0f)
                .withCallToActionBackgroundColor(
                    ColorDrawable(Color.parseColor("#4CAF50"))
                )
                .build()

            setTemplateStyle(style)
        }

        // 6. addView 전에 기존 뷰 제거 (중복 방지)
        nativeAdContainer.removeAllViews()

        val params = FrameLayout.LayoutParams(
            FrameLayout.LayoutParams.MATCH_PARENT,
            FrameLayout.LayoutParams.WRAP_CONTENT
        )

        nativeAdContainer.addView(nativeAdView, params)

        // 7. 광고 로드
        nativeAdView?.loadAdWithTemplate(ADWHALE_NATIVE_TEMPLATE.SMALL)
    }

    // 라이프사이클 onDestroy 콜백 시 반드시 release() 호출 필요
    override fun onDestroy() {
        nativeAdView?.destroy()
        nativeAdView = null
        super.onDestroy()
    }
}
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    android:id="@+id/nativeContainer"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```
{% endtab %}

{% tab title="Compose" %}
```kotlin
@Composable
fun NativeTemplateScreen(
    placementUid: String
) {
    val context = LocalContext.current
    val activity = context as? ComponentActivity ?: return

    var nativeAdView by remember { mutableStateOf<AdWhaleMediationNativeAdView?>(null) }
    var isInited by remember { mutableStateOf(false) }

    // 1. 로거 설정
    LaunchedEffect(Unit) {
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)
    }

    // 2. 초기화 코드
    LaunchedEffect(Unit) {
        AdWhaleMediationAds.init(activity, object : AdWhaleMediationOnInitCompleteListener {
            override fun onInitComplete(statusCode: Int, message: String) {
                Log.i("NativeTemplateScreen", "onInitComplete($statusCode, $message)")
                isInited = true
            }
        })
    }

    if (!isInited) return

    AndroidView(
        factory = {

            // 3. 인스턴스 생성
            AdWhaleMediationNativeAdView(it).apply {

                setPlacementUid(placementUid)

                // 4. 리스너 등록
                setAdWhaleMediationNativeAdViewListener(
                    object : AdWhaleMediationNativeAdViewListener {

                        override fun onNativeAdLoaded() {
                            Log.i("NativeTemplateScreen", "onNativeAdLoaded()")
                            // 8. 광고 표시
                            show()
                        }

                        override fun onNativeAdFailedToLoad(
                            errorCode: Int,
                            errorMessage: String?
                        ) {
                            Log.e(
                                "NativeTemplateScreen",
                                "onNativeAdFailedToLoad($errorCode, $errorMessage)"
                            )
                        }

                        override fun onNativeAdShowFailed(
                            errorCode: Int,
                            errorMessage: String?
                        ) {
                            Log.e(
                                "NativeTemplateScreen",
                                "onNativeAdShowFailed($errorCode, $errorMessage)"
                            )
                        }

                        override fun onNativeAdClicked() {
                            Log.i("NativeTemplateScreen", "onNativeAdClicked()")
                        }

                        override fun onNativeAdClosed() {
                            Log.i("NativeTemplateScreen", "onNativeAdClosed()")
                        }
                    }
                )

                // 5. 스타일 적용 (선택)
                val style = AdWhaleMediationNativeTemplateStyle.Builder()
                    .withMainBackgroundColor(
                        ColorDrawable(Color.parseColor("#2C2C2C"))
                    )
                    // Primary
                    .withPrimaryTextTypefaceColor(Color.WHITE)
                    .withPrimaryTextTypeface(Typeface.DEFAULT_BOLD)
                    .withPrimaryTextBackgroundColor(
                        ColorDrawable(Color.parseColor("#4CAF50"))
                    )
                    .withPrimaryTextSize(15.0f)
                    // Secondary
                    .withSecondaryTextTypefaceColor(Color.parseColor("#CCCCCC"))
                    .withSecondaryTextTypeface(Typeface.DEFAULT)
                    .withSecondaryTextBackgroundColor(
                        ColorDrawable(Color.TRANSPARENT)
                    )
                    .withSecondaryTextSize(13.0f)
                    // Tertiary
                    .withTertiaryTextTypefaceColor(Color.parseColor("#AAAAAA"))
                    .withTertiaryTextTypeface(Typeface.DEFAULT)
                    .withTertiaryTextBackgroundColor(
                        ColorDrawable(Color.TRANSPARENT)
                    )
                    .withTertiaryTextSize(12.0f)
                    // CTA
                    .withCallToActionTypefaceColor(Color.WHITE)
                    .withCallToActionTextTypeface(Typeface.DEFAULT_BOLD)
                    .withCallToActionTextSize(14.0f)
                    .withCallToActionBackgroundColor(
                        ColorDrawable(Color.parseColor("#4CAF50"))
                    )
                    .build()

                setTemplateStyle(style)

                nativeAdView = this

                // 7. 광고 로드
                loadAdWithTemplate(ADWHALE_NATIVE_TEMPLATE.SMALL)
            }
        }
    )

    // 라이프사이클 dispose 시 destroy 호출
    DisposableEffect(Unit) {
        onDispose {
            nativeAdView?.destroy()
            nativeAdView = null
        }
    }
}
```
{% endtab %}
{% endtabs %}

**템플릿 스타일 옵션 샘플코드(Builder 전체)**

{% tabs %}
{% tab title="Java" %}
```java
AdWhaleMediationNativeTemplateStyle style =
        new AdWhaleMediationNativeTemplateStyle.Builder()
                .withMainBackgroundColor(
                        new ColorDrawable(Color.parseColor("#F5F5F5")))
                .withPrimaryTextTypefaceColor(Color.parseColor("#1A1A1A"))
                .withPrimaryTextTypeface(Typeface.DEFAULT_BOLD)
                .withPrimaryTextBackgroundColor(
                        new ColorDrawable(Color.parseColor("#E8F5E8")))
                .withPrimaryTextSize(20f)

                .withSecondaryTextTypefaceColor(Color.parseColor("#2E7D32"))
                .withSecondaryTextTypeface(Typeface.DEFAULT)
                .withSecondaryTextBackgroundColor(
                        new ColorDrawable(Color.parseColor("#F1F8E9")))
                .withSecondaryTextSize(16f)

                .withTertiaryTextTypefaceColor(Color.parseColor("#424242"))
                .withTertiaryTextTypeface(Typeface.DEFAULT)
                .withTertiaryTextBackgroundColor(
                        new ColorDrawable(Color.parseColor("#FAFAFA")))
                .withTertiaryTextSize(14f)

                .withCallToActionTypefaceColor(Color.WHITE)
                .withCallToActionTextTypeface(Typeface.DEFAULT_BOLD)
                .withCallToActionTextSize(18f)
                .withCallToActionBackgroundColor(
                        new ColorDrawable(Color.parseColor("#FF6B35")))
                .build();
```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
val style = AdWhaleMediationNativeTemplateStyle.Builder()
    .withMainBackgroundColor(ColorDrawable(Color.parseColor("#F5F5F5")))
    .withPrimaryTextTypefaceColor(Color.parseColor("#1A1A1A"))
    .withPrimaryTextTypeface(Typeface.DEFAULT_BOLD)
    .withPrimaryTextBackgroundColor(ColorDrawable(Color.parseColor("#E8F5E8")))
    .withPrimaryTextSize(20f)

    .withSecondaryTextTypefaceColor(Color.parseColor("#2E7D32"))
    .withSecondaryTextTypeface(Typeface.DEFAULT)
    .withSecondaryTextBackgroundColor(ColorDrawable(Color.parseColor("#F1F8E9")))
    .withSecondaryTextSize(16f)

    .withTertiaryTextTypefaceColor(Color.parseColor("#424242"))
    .withTertiaryTextTypeface(Typeface.DEFAULT)
    .withTertiaryTextBackgroundColor(ColorDrawable(Color.parseColor("#FAFAFA")))
    .withTertiaryTextSize(14f)

    .withCallToActionTypefaceColor(Color.WHITE)
    .withCallToActionTextTypeface(Typeface.DEFAULT_BOLD)
    .withCallToActionTextSize(18f)
    .withCallToActionBackgroundColor(ColorDrawable(Color.parseColor("#FF6B35")))
    .build()
```
{% endtab %}
{% endtabs %}

#### 4. 커스텀 네이티브 광고 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

커스텀 네이티브 광고는 사용자가 직접 정의한 커스텀 레이아웃을 사용하여 광고를 표시합니다.

**커스텀 네이티브 광고 기본 구현 샘플코드**

{% tabs %}
{% tab title="Java" %}
```java
public class MainActivity extends AppCompatActivity {

    private AdWhaleMediationNativeAdView nativeAdView;
    private FrameLayout nativeAdContainer;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        nativeAdContainer = findViewById(R.id.nativeAdContainer);

        // 1. 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None);

        // 2. 초기화 코드
        AdWhaleMediationAds.init(this, new AdWhaleMediationOnInitCompleteListener() {
            @Override
            public void onInitComplete(int statusCode, String message) {
                Log.i("MainActivity", "onInitComplete(" + statusCode + ", " + message + ")");
                createAndLoadNativeBinderAd();
            }
        });
    }

    private void createAndLoadNativeBinderAd() {

        // 3. 인스턴스 생성
        nativeAdView = new AdWhaleMediationNativeAdView(this);
        nativeAdView.setPlacementUid("발급받은 placement uid 값");

        // 4. 리스너 등록
        nativeAdView.setAdWhaleMediationNativeAdViewListener(
                new AdWhaleMediationNativeAdViewListener() {

                    @Override
                    public void onNativeAdLoaded() {
                        Log.i("MainActivity", "onNativeAdLoaded()");
                        // 8. 광고 표시
                        nativeAdView.show();
                    }

                    @Override
                    public void onNativeAdFailedToLoad(int errorCode, String errorMessage) {
                        Log.e("MainActivity",
                                "onNativeAdFailedToLoad(" + errorCode + ", " + errorMessage + ")");
                    }

                    @Override
                    public void onNativeAdShowFailed(int errorCode, String errorMessage) {
                        Log.e("MainActivity",
                                "onNativeAdShowFailed(" + errorCode + ", " + errorMessage + ")");
                    }

                    @Override
                    public void onNativeAdClicked() {
                        Log.i("MainActivity", "onNativeAdClicked()");
                    }

                    @Override
                    public void onNativeAdClosed() {
                        Log.i("MainActivity", "onNativeAdClosed()");
                    }
                });

        // 5. 커스텀 바인딩용 Binder 생성
        AdWhaleNativeAdBinder binder =
                new AdWhaleNativeAdBinder.Builder(
                        this,
                        R.layout.custom_native_ad_main_layout)
                        .setIconViewId(R.id.main_view_icon)
                        .setTitleViewId(R.id.main_view_title)
                        .setBodyTextViewId(R.id.main_view_body)
                        .setCallToActionViewId(R.id.main_button_cta)
                        .setMediaViewGroupId(R.id.main_view_media)
                        .build();

        // 6. addView 전에 기존 뷰 제거 (중복 방지)
        nativeAdContainer.removeAllViews();

        // LayoutParams 명시적으로 지정 (권장)
        FrameLayout.LayoutParams params =
                new FrameLayout.LayoutParams(
                        FrameLayout.LayoutParams.MATCH_PARENT,
                        FrameLayout.LayoutParams.WRAP_CONTENT
                );

        nativeAdContainer.addView(nativeAdView, params);

        // 7. 광고 로드
        nativeAdView.loadAdWithBinder(binder);
    }

    // 라이프사이클 onDestroy 콜백 시 반드시 destroy() 호출
    @Override
    protected void onDestroy() {
        if (nativeAdView != null) {
            nativeAdView.destroy();
            nativeAdView = null;
        }
        super.onDestroy();
    }
}
```

**커스텀 레이아웃 파일 생성**

`android/app/src/main/res/layout/custom_native_ad_main_layout.xml` 파일을 생성합니다:

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
        android:id="@+id/main_view_icon"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:layout_marginEnd="12dp"
        android:scaleType="centerCrop"
        app:layout_constraintBottom_toBottomOf="@id/main_view_title"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="@id/main_view_title" />

    <!-- 제목 -->
    <TextView
        android:id="@+id/main_view_title"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:textSize="16sp"
        android:textStyle="bold"
        android:textColor="@android:color/black"
        android:ellipsize="end"
        android:maxLines="2"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@id/main_view_icon"
        app:layout_constraintTop_toTopOf="parent"
        tools:text="광고 제목입니다" />

    <!-- 설명 -->
    <TextView
        android:id="@+id/main_view_body"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="4dp"
        android:textSize="14sp"
        android:textColor="@android:color/darker_gray"
        android:ellipsize="end"
        android:lines="2"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="@id/main_view_title"
        app:layout_constraintTop_toBottomOf="@id/main_view_title"
        tools:text="광고 설명입니다. 이 앱을 다운로드하세요!" />

    <!-- CTA 버튼 -->
    <Button
        android:id="@+id/main_button_cta"
        android:layout_width="wrap_content"
        android:layout_height="36dp"
        android:layout_marginTop="8dp"
        android:textSize="12sp"
        android:backgroundTint="#009688"
        android:textColor="@android:color/white"
        android:text="설치하기"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toBottomOf="@id/main_view_body" />

    <!-- 미디어 뷰 -->
    <FrameLayout
        android:id="@+id/main_view_media"
        android:layout_width="match_parent"
        android:layout_height="300dp"
        android:layout_marginTop="12dp"
        app:layout_constraintTop_toBottomOf="@id/main_button_cta" />

</androidx.constraintlayout.widget.ConstraintLayout>
```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
class MainActivity : AppCompatActivity() {

    private var nativeAdView: AdWhaleMediationNativeAdView? = null
    private lateinit var nativeAdContainer: FrameLayout

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        nativeAdContainer = findViewById(R.id.nativeAdContainer)

        // 1. 로거 설정
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)

        // 2. 초기화 코드
        AdWhaleMediationAds.init(this, object : AdWhaleMediationOnInitCompleteListener {
            override fun onInitComplete(statusCode: Int, message: String) {
                Log.i("MainActivity", "onInitComplete($statusCode, $message)")
                createAndLoadNativeBinderAd()
            }
        })
    }

    private fun createAndLoadNativeBinderAd() {

        // 3. 인스턴스 생성
        nativeAdView = AdWhaleMediationNativeAdView(this).apply {

            setPlacementUid("발급받은 placement uid 값")

            // 4. 리스너 등록
            setAdWhaleMediationNativeAdViewListener(
                object : AdWhaleMediationNativeAdViewListener {

                    override fun onNativeAdLoaded() {
                        Log.i("MainActivity", "onNativeAdLoaded()")
                        // 8. 광고 표시
                        show()
                    }

                    override fun onNativeAdFailedToLoad(
                        errorCode: Int,
                        errorMessage: String?
                    ) {
                        Log.e(
                            "MainActivity",
                            "onNativeAdFailedToLoad($errorCode, $errorMessage)"
                        )
                    }

                    override fun onNativeAdShowFailed(
                        errorCode: Int,
                        errorMessage: String?
                    ) {
                        Log.e(
                            "MainActivity",
                            "onNativeAdShowFailed($errorCode, $errorMessage)"
                        )
                    }

                    override fun onNativeAdClicked() {
                        Log.i("MainActivity", "onNativeAdClicked()")
                    }

                    override fun onNativeAdClosed() {
                        Log.i("MainActivity", "onNativeAdClosed()")
                    }
                }
            )
        }

        // 5. 커스텀 바인딩용 Binder 생성
        val binder = AdWhaleNativeAdBinder.Builder(
            this,
            R.layout.custom_native_ad_main_layout
        )
            .setIconViewId(R.id.main_view_icon)
            .setTitleViewId(R.id.main_view_title)
            .setBodyTextViewId(R.id.main_view_body)
            .setCallToActionViewId(R.id.main_button_cta)
            .setMediaViewGroupId(R.id.main_view_media)
            .build()

        // 6. addView 전에 기존 뷰 제거 (중복 방지)
        nativeAdContainer.removeAllViews()

        val params = FrameLayout.LayoutParams(
            FrameLayout.LayoutParams.MATCH_PARENT,
            FrameLayout.LayoutParams.WRAP_CONTENT
        )

        nativeAdContainer.addView(nativeAdView, params)

        // 7. 광고 로드
        nativeAdView?.loadAdWithBinder(binder)
    }

    // 라이프사이클 onDestroy 콜백 시 반드시 destroy() 호출
    override fun onDestroy() {
        nativeAdView?.destroy()
        nativeAdView = null
        super.onDestroy()
    }
}
```

**커스텀 레이아웃 파일 생성**

`android/app/src/main/res/layout/custom_native_ad_main_layout.xml` 파일을 생성합니다:

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
        android:id="@+id/main_view_icon"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:layout_marginEnd="12dp"
        android:scaleType="centerCrop"
        app:layout_constraintBottom_toBottomOf="@id/main_view_title"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="@id/main_view_title" />

    <!-- 제목 -->
    <TextView
        android:id="@+id/main_view_title"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:textSize="16sp"
        android:textStyle="bold"
        android:textColor="@android:color/black"
        android:ellipsize="end"
        android:maxLines="2"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@id/main_view_icon"
        app:layout_constraintTop_toTopOf="parent"
        tools:text="광고 제목입니다" />

    <!-- 설명 -->
    <TextView
        android:id="@+id/main_view_body"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="4dp"
        android:textSize="14sp"
        android:textColor="@android:color/darker_gray"
        android:ellipsize="end"
        android:lines="2"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="@id/main_view_title"
        app:layout_constraintTop_toBottomOf="@id/main_view_title"
        tools:text="광고 설명입니다. 이 앱을 다운로드하세요!" />

    <!-- CTA 버튼 -->
    <Button
        android:id="@+id/main_button_cta"
        android:layout_width="wrap_content"
        android:layout_height="36dp"
        android:layout_marginTop="8dp"
        android:textSize="12sp"
        android:backgroundTint="#009688"
        android:textColor="@android:color/white"
        android:text="설치하기"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toBottomOf="@id/main_view_body" />

    <!-- 미디어 뷰 -->
    <FrameLayout
        android:id="@+id/main_view_media"
        android:layout_width="match_parent"
        android:layout_height="300dp"
        android:layout_marginTop="12dp"
        app:layout_constraintTop_toBottomOf="@id/main_button_cta" />

</androidx.constraintlayout.widget.ConstraintLayout>
```
{% endtab %}

{% tab title="Compose" %}
```kotlin
@Composable
fun NativeCustomBindingScreen(
    placementUid: String
) {
    val context = LocalContext.current
    val activity = context as? ComponentActivity ?: return

    var nativeAdView by remember { mutableStateOf<AdWhaleMediationNativeAdView?>(null) }
    var isInited by remember { mutableStateOf(false) }

    // 1. 로거 설정
    LaunchedEffect(Unit) {
        AdWhaleLog.setLogLevel(AdWhaleLog.LogLevel.None)
    }

    // 2. 초기화 코드
    LaunchedEffect(Unit) {
        AdWhaleMediationAds.init(activity, object : AdWhaleMediationOnInitCompleteListener {
            override fun onInitComplete(statusCode: Int, message: String) {
                Log.i("NativeCustomBindingScreen", "onInitComplete($statusCode, $message)")
                isInited = true
            }
        })
    }

    if (!isInited) return

    AndroidView(
        factory = { ctx ->

            // 3. 인스턴스 생성
            AdWhaleMediationNativeAdView(ctx).apply {

                setPlacementUid(placementUid)

                // 4. 리스너 등록
                setAdWhaleMediationNativeAdViewListener(
                    object : AdWhaleMediationNativeAdViewListener {

                        override fun onNativeAdLoaded() {
                            Log.i("NativeCustomBindingScreen", "onNativeAdLoaded()")
                            // 8. 광고 표시
                            show()
                        }

                        override fun onNativeAdFailedToLoad(
                            errorCode: Int,
                            errorMessage: String?
                        ) {
                            Log.e(
                                "NativeCustomBindingScreen",
                                "onNativeAdFailedToLoad($errorCode, $errorMessage)"
                            )
                        }

                        override fun onNativeAdShowFailed(
                            errorCode: Int,
                            errorMessage: String?
                        ) {
                            Log.e(
                                "NativeCustomBindingScreen",
                                "onNativeAdShowFailed($errorCode, $errorMessage)"
                            )
                        }

                        override fun onNativeAdClicked() {
                            Log.i("NativeCustomBindingScreen", "onNativeAdClicked()")
                        }

                        override fun onNativeAdClosed() {
                            Log.i("NativeCustomBindingScreen", "onNativeAdClosed()")
                        }
                    }
                )

                // 5. 커스텀 바인딩용 Binder 생성
                val binder = AdWhaleNativeAdBinder.Builder(
                    ctx,
                    R.layout.custom_native_ad_main_layout
                )
                    .setIconViewId(R.id.main_view_icon)
                    .setTitleViewId(R.id.main_view_title)
                    .setBodyTextViewId(R.id.main_view_body)
                    .setCallToActionViewId(R.id.main_button_cta)
                    .setMediaViewGroupId(R.id.main_view_media)
                    .build()

                nativeAdView = this

                // 7. 광고 로드
                loadAdWithBinder(binder)
            }
        }
    )

    // 라이프사이클 dispose 시 destroy 호출
    DisposableEffect(Unit) {
        onDispose {
            nativeAdView?.destroy()
            nativeAdView = null
        }
    }
}
```
{% endtab %}
{% endtabs %}

#### 5. 옵션 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

{% tabs %}
{% tab title="Java" %}
```java
nativeAdView.setRegion("서울시 강남구"); // 지역 타게팅 전용 API(옵션)
nativeAdView.setGcoder(37.5665, 126.9780); // 지역 타게팅 전용 API(옵션)
```

{% hint style="info" %}
AdWhale SDK는 Cauly 네트워크를 지원하며, 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}

```java
nativeAdView.setPlacementName("native_main");
```

{% hint style="info" %}
AdWhale SDK는 LevelPlay 네트워크를 지원하며, 각 Placement 별로 광고 노출을 구분하고자 할 때, `setPlacementName()` API를 통해 설정할 수 있습니다.
{% endhint %}

{% hint style="warning" %}
LevelPlay 콘솔에서 설정한 Placement 이름을 지정하면, 해당 Placement에 설정된 **보상 금액, 노출 제한 등의 설정이 적용된 광고**가 노출됩니다.\
설정하지 않으면 기본 Placement(Default Placement)가 사용됩니다.

***

배너 광고에는 **보상 조건, 제한 조건 등의 설정이 적용되지 않으며**, Placement는 **광고 위치 구분 및 분석 용도**로만 사용됩니다.
{% endhint %}
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
nativeAdView.setRegion("서울시 강남구") // 지역 타게팅 전용 API(옵션)
nativeAdView.setGcoder(37.5665, 126.9780) // 지역 타게팅 전용 API(옵션)
```

{% hint style="info" %}
AdWhale SDK는 Cauly 네트워크를 지원하며, 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}

```kotlin
nativeAdView.setPlacementName("native_main")
```

{% hint style="info" %}
AdWhale SDK는 LevelPlay 네트워크를 지원하며, 각 Placement 별로 광고 노출을 구분하고자 할 때, `setPlacementName()` API를 통해 설정할 수 있습니다.
{% endhint %}

{% hint style="warning" %}
LevelPlay 콘솔에서 설정한 Placement 이름을 지정하면, 해당 Placement에 설정된 **보상 금액, 노출 제한 등의 설정이 적용된 광고**가 노출됩니다.\
설정하지 않으면 기본 Placement(Default Placement)가 사용됩니다.

***

배너 광고에는 **보상 조건, 제한 조건 등의 설정이 적용되지 않으며**, Placement는 **광고 위치 구분 및 분석 용도**로만 사용됩니다.
{% endhint %}
{% endtab %}

{% tab title="Compose" %}
```kotlin
nativeAdView.setRegion("서울시 강남구") // 지역 타게팅 전용 API(옵션)
nativeAdView.setGcoder(37.5665, 126.9780) // 지역 타게팅 전용 API(옵션)
```

{% hint style="info" %}
AdWhale SDK는 Cauly 네트워크를 지원하며, 광고 지역 타게팅을 위해 지역정보(Region, Gcoder)를 선택적으로 입력받고 있습니다. setRegion() 과 setGcoder() 관련 자세한 구현 방법은 아래 링크를 참고하세요.

[https://adwhale.gitbook.io/sdk-android-appendix/adwhale](https://adwhale.gitbook.io/sdk-android-appendix/adwhale)
{% endhint %}

```kotlin
nativeAdView.setPlacementName("native_main")
```

{% hint style="info" %}
AdWhale SDK는 LevelPlay 네트워크를 지원하며, 각 Placement 별로 광고 노출을 구분하고자 할 때, `setPlacementName()` API를 통해 설정할 수 있습니다.
{% endhint %}

{% hint style="warning" %}
LevelPlay 콘솔에서 설정한 Placement 이름을 지정하면, 해당 Placement에 설정된 **보상 금액, 노출 제한 등의 설정이 적용된 광고**가 노출됩니다.\
설정하지 않으면 기본 Placement(Default Placement)가 사용됩니다.

***

배너 광고에는 **보상 조건, 제한 조건 등의 설정이 적용되지 않으며**, Placement는 **광고 위치 구분 및 분석 용도**로만 사용됩니다.
{% endhint %}
{% endtab %}
{% endtabs %}

#### **6. 주의사항**

**광고 로드 타이밍**

* `loadAd()`를 호출한 후 `onLoaded` 이벤트가 발생해야 `showAd()`를 호출할 수 있습니다.
* 광고가 로드되기 전에 `showAd()`를 호출하면 표시되지 않습니다.

**FULLSCREEN 템플릿**

* FULLSCREEN 템플릿은 모달이나 별도 화면에서 사용하는 것을 권장합니다.
* 일반 뷰에 표시하면 레이아웃 문제가 발생할 수 있습니다.

**스타일 커스터마이징**

* 템플릿 스타일은 광고 로드 전에 설정해야 합니다.
* 스타일을 변경하려면 컴포넌트를 재생성하세요.

**에러 처리**

* `onLoadFailed`와 `onShowFailed` 이벤트에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.

**테스트**

* 개발 환경에서는 테스트용 placement UID를 사용하세요.
* 다양한 템플릿 타입과 스타일을 테스트하세요.
