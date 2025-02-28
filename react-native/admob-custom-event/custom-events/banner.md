# 배너

{% hint style="info" %}
Custom event 추가 과정에서 작성한 Class Name에서 `loadBannerAd()`가 호출됩니다.

본 문서에서는 AdWhaleMediationAdapter에 해당 메서드가 있으며, AdWhaleMediationBanner의 loadAd()를 호출합니다.
{% endhint %}

### **AdWhaleMediation SDK API**

**AdWhaleMediationAdView 클래스 API 설명**

```java
public AdWhaleMediationAdView(Context context)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.content.Context</td><td><p>Android Main Activity 클래스나</p><p>Application 확장 클래스</p></td></tr></tbody></table>

```java
public void setPlacementUid(String placementUid)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td><p>placementUid 값(발급 필요)</p><p>Custom event 추가 과정 중 Parameter에 입력한 데이터</p></td></tr></tbody></table>

```java
public void setAdwhaleAdSize(ADWHALE_AD_SIZE adWhaleAdSize)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.ADWHALE_AD_SIZE</td><td>배너 광고 사이즈(사이즈 종류: BANNER320x50, BANNER320x100, BANNER300x250, BANNER250x250) </td></tr></tbody></table>

```java
public void setAdWhaleMediationAdViewListener(AdWhaleMediationAdViewListener listener)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationAdViewListener</td><td>배너 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```java
public void resume() // resume 콜백 시 호출필요
```

```java
public void pause() // pause 콜백 시 호출필요
```

```java
public void destroy() // destroy 콜백 시 호출필요
```



**AdWhaleMediationAdViewListener 클래스 API 설명**

```java
public void onAdLoaded() // 배너 광고요청 성공 시
```

```java
public void onAdLoadFailed(int statusCode, String message) // 배너 광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>초기화 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```java
public void onAdClicked() // 배너 클릭 시
```

***

### Custom Banner class 구현

프로젝트의 android 위치에 Custom banner class 파일을 생성합니다.

(예: android/app/src/main/java/com/example/rnadmob/AdWhaleMediationBanner.java)

```java
public class AdWhaleMediationBanner implements MediationBannerAd {
    final static String TAG = AdWhaleMediationBanner.class.getSimpleName();
    private final MediationBannerAdConfiguration mediationBannerAdConfiguration;
    private final MediationAdLoadCallback<MediationBannerAd, MediationBannerAdCallback> mediationAdLoadCallback;
    private MediationBannerAdCallback bannerAdCallback;

    private AdWhaleMediationAdBannerView adWhaleMediationAdView = null;

    /** Error raised when the custom event adapter cannot obtain the ad unit id. */
    public static final int ERROR_NO_AD_UNIT_ID = 101;
    /** Error raised when the custom event adapter cannot obtain the activity context. */
    public static final int ERROR_NO_ACTIVITY_CONTEXT = 103;
    public static final String SAMPLE_SDK_DOMAIN = "com.example.rnadmob";
    public static final String CUSTOM_EVENT_ERROR_DOMAIN = "com.google.ads.mediation.sample.customevent";

    private Activity currentActivity;

    private boolean load = false;

    public AdWhaleMediationBanner(MediationBannerAdConfiguration mediationBannerAdConfiguration, MediationAdLoadCallback<MediationBannerAd, MediationBannerAdCallback> mediationAdLoadCallback) {
        this.mediationBannerAdConfiguration = mediationBannerAdConfiguration;
        this.mediationAdLoadCallback = mediationAdLoadCallback;
    }

    /** Loads a banner ad from the third party ad network. */
    public void loadAd() {
        Log.d(TAG, "loadAd()");
        load = true;

        /** 미디에이션 서버로부터 파라미터 설정 값 획득 **/
        String serverParameter = mediationBannerAdConfiguration.getServerParameters().getString(MediationConfiguration.CUSTOM_EVENT_SERVER_PARAMETER_FIELD);
        if (TextUtils.isEmpty(serverParameter)) {
            mediationAdLoadCallback.onFailure(new AdError(ERROR_NO_AD_UNIT_ID, "Ad unit id is empty", CUSTOM_EVENT_ERROR_DOMAIN));
            return;
        }
        /** 미디에이션 서버로부터 파라미터 설정 값 획득 **/

        /** 현재 화면의 Activity Context 획득 **/
        Context context = mediationBannerAdConfiguration.getContext();

        if (context instanceof Activity) {
            currentActivity = (Activity) context;
        } else if (context instanceof ThemedReactContext) {
            currentActivity = ((ThemedReactContext) context).getCurrentActivity();
        }

        if (currentActivity == null) {
            Log.e(TAG, "Activity context is empty");
            mediationAdLoadCallback.onFailure(new AdError(ERROR_NO_ACTIVITY_CONTEXT, "Activity context is empty", CUSTOM_EVENT_ERROR_DOMAIN));
            return;

        }
        /** 현재 화면의 Activity Context 획득 **/

        /** Admob ad_size를 Adwhale ad_size로 변환 **/
        AdSize size = mediationBannerAdConfiguration.getAdSize();
        int widthInPixels = size.getWidthInPixels(context);
        int heightInPixels = size.getHeightInPixels(context);
        DisplayMetrics displayMetrics = Resources.getSystem().getDisplayMetrics();
        int widthInDp = Math.round(widthInPixels / displayMetrics.density);
        int heightInDp = Math.round(heightInPixels / displayMetrics.density);
        String adSizeString = widthInDp + "x" + heightInDp;

        ADWHALE_AD_SIZE adwhale_ad_size = ADWHALE_AD_SIZE.BANNER320x50;

        switch (adSizeString) {
            case "320x50":
                adwhale_ad_size = ADWHALE_AD_SIZE.BANNER320x50;
                break;

            case "320x100":
                adwhale_ad_size = ADWHALE_AD_SIZE.BANNER320x100;
                break;

            case "300x250":
                adwhale_ad_size = ADWHALE_AD_SIZE.BANNER300x250;
                break;

            case "250x250":
                adwhale_ad_size = ADWHALE_AD_SIZE.BANNER250x250;
                break;
        }
        /** Admob ad_size를 Adwhale ad_size로 변환 **/

        // Adwhale SDK 초기화 코드
        AdWhaleMediationAds.init(currentActivity, new AdWhaleMediationOnInitCompleteListener() {
            @Override
            public void onInitComplete(int statusCode, String message) {
                Log.i(TAG, ".onInitComplete(" + statusCode + ", " + message + ")");
            }
        });

        // 배너 뷰 생성
        adWhaleMediationAdView = new AdWhaleMediationAdBannerView(currentActivity);
        adWhaleMediationAdView.setPlacementUid(serverParameter);
        adWhaleMediationAdView.setAdwhaleAdSize(adwhale_ad_size);

        // 배너 뷰 콜백 리스너 등록
        adWhaleMediationAdView.setAdWhaleMediationAdBannerViewListener(new AdWhaleMediationAdBannerViewListener() {
            @Override
            public void onAdLoaded() {
                Log.i(TAG, ".onAdLoaded()");
                if(adWhaleMediationAdView != null) {
                    if (adWhaleMediationAdView.getDisplay() == null && !load) {
                        adWhaleMediationAdView.destroy();
                        adWhaleMediationAdView = null;
                        return;
                    }

                    Handler mainHandler = new Handler(Looper.getMainLooper());
                    mainHandler.post(new Runnable() {
                        @Override
                        public void run() {
                            // 광고 표시는 반드시 Main UI Thread에서 진행
                            adWhaleMediationAdView.show();

                            bannerAdCallback = mediationAdLoadCallback.onSuccess(AdWhaleMediationBanner.this);
                            bannerAdCallback.onAdOpened();
                            bannerAdCallback.reportAdImpression();

                            load = false;
                        }
                    });
                }
            }

            @Override
            public void onAdLoadFailed(int statusCode, String message) {
                Log.e(TAG, ".onAdLoadFailed(" + statusCode + ", " + message + ")");
                mediationAdLoadCallback.onFailure(new AdError(statusCode, message, SAMPLE_SDK_DOMAIN));
            }

            @Override
            public void onShowLandingScreen() {
                Log.i(TAG, ".onShowLandingScreen()");
            }

            @Override
            public void onCloseLandingScreen() {
                Log.i(TAG, ".onCloseLandingScreen()");
            }

            @Override
            public void onTimeout(String errorMessage) {
                Log.i(TAG, ".onTimeout()");
            }

            @Override
            public void onClickAd() {
                Log.i(TAG, ".onClickAd()");
                bannerAdCallback.reportAdClicked();
            }
        });


        Handler mainHandler = new Handler(Looper.getMainLooper());
        mainHandler.post(new Runnable() {
            @Override
            public void run() {
                // 광고 요청시 반드시 Main UI Thread에서 진행
                adWhaleMediationAdView.loadAd();
            }
        });
    }

    @NonNull
    @Override
    public View getView() {
        return adWhaleMediationAdView;
    }
}
```

### Sample Code

{% embed url="https://github.com/dev-adwhale/ADwhaleMediationSDK-Android/blob/main/Admob%20Custom%20Events/CustomEvent_ReactNative/AdWhaleMediationBanner.java" %}
