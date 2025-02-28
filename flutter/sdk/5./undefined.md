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

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.ADWHALE_AD_SIZE</td><td>배너 광고 사이즈(사이즈 종류: BANNER320x50, BANNER320x100, BANNER300x250, BANNER250x250) </td></tr></tbody></table>

```java
public void setAdWhaleMediationAdViewListener(AdWhaleMediationAdViewListener listener) // 리스너 등록
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationAdViewListener</p></td><td>배너 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

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

```java

import android.content.Context;
import android.util.Log;
import android.widget.LinearLayout;
import android.widget.RelativeLayout;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;

import net.adwhale.sdk.mediation.ads.ADWHALE_AD_SIZE;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdView;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdViewListener;

import io.flutter.plugin.platform.PlatformView;

public class FlutterBannerAd extends FlutterAd {
    private static final String TAG = "FlutterBannerAd";
    @NonNull
    private final Context context;
    @NonNull
    private final RelativeLayout rootView;
    @NonNull
    private final AdInstanceManager manager;
    @NonNull
    private final FlutterAdInfo adInfo;
    @NonNull
    private final BannerAdCreator bannerAdCreator;
    @Nullable
    private AdWhaleMediationAdView adWhaleMediationAdView;

    public FlutterBannerAd(
            int adId,
            @NonNull Context context,
            @NonNull AdInstanceManager manager,
            @NonNull FlutterAdInfo adInfo,
            @NonNull BannerAdCreator bannerAdCreator) {
        super(adId);
        this.context = context;
        this.manager = manager;
        this.adInfo = adInfo;
        this.bannerAdCreator = bannerAdCreator;
        this.rootView = new RelativeLayout(context);
    }

    @Override
    void load() {
        clearView();
        adWhaleMediationAdView = bannerAdCreator.createAdView();
        if (adWhaleMediationAdView == null) {
            Log.e(TAG, "createAdView() = null");
            return;
        }

        adWhaleMediationAdView.setPlacementUid(adInfo.getAppCode());
        Log.d(TAG, "FlutterBannerAd load()");
        Log.d(TAG, "FlutterBannerAd load() appCode : " + adInfo.getAppCode());

        ADWHALE_AD_SIZE adSize;
        int width = adInfo.getBannerSizeWidth();
        int height = adInfo.getBannerSizeHeight();
        if (width == 320 && height == 50) {
            adSize = ADWHALE_AD_SIZE.BANNER320x50;
        } else if (width == 320 && height == 100) {
            adSize = ADWHALE_AD_SIZE.BANNER320x100;
        } else if (width == 300 && height == 250) {
            adSize = ADWHALE_AD_SIZE.BANNER300x250;
        } else if (width == 250 && height == 250) {
            adSize = ADWHALE_AD_SIZE.BANNER250x250;
        } else {
            adSize = ADWHALE_AD_SIZE.BANNER320x50;
        }
        adWhaleMediationAdView.setAdwhaleAdSize(adSize);
        adWhaleMediationAdView.setAdWhaleMediationAdViewListener(new FlutterBannerAdListener(adId, manager));

        RelativeLayout.LayoutParams params = new RelativeLayout.LayoutParams(
                LinearLayout.LayoutParams.WRAP_CONTENT, LinearLayout.LayoutParams.WRAP_CONTENT);
        params.addRule(RelativeLayout.ALIGN_PARENT_BOTTOM);
        params.addRule(RelativeLayout.CENTER_HORIZONTAL);
        rootView.addView(adWhaleMediationAdView, params);

        if (adWhaleMediationAdView != null) {
            adWhaleMediationAdView.loadAd();
        }
        rootView.invalidate();
        rootView.requestLayout();
    }

    @Nullable
    @Override
    PlatformView getPlatformView() {
        if (adWhaleMediationAdView == null) {
            return null;
        }
        return new FlutterPlatformView(rootView);
    }

    @Override
    void dispose() {
        clearView();
    }

    void clearView() {
        if (adWhaleMediationAdView != null) {
            adWhaleMediationAdView.destroy();
            rootView.removeView(adWhaleMediationAdView);
            adWhaleMediationAdView = null;
        }
    }
}
```
{% endtab %}
{% endtabs %}

**배너 리스너 구현**

```java
public class FlutterBannerAdListener implements AdWhaleMediationAdViewListener {
    protected final int adId;
    @NonNull
    protected final AdInstanceManager manager;
    private static final String TAG = "FlutterBannerAd";
    FlutterBannerAdListener(int adId, @NonNull AdInstanceManager manager) {
        this.adId = adId;
        this.manager = manager;
    }

    @Override
    public void onAdLoaded() {
        Log.d(TAG, "FlutterBannerAd onAdLoaded");
        manager.onAdLoaded(adId);
    }

    @Override
    public void onAdLoadFailed(int i, String s) {
        Log.d(TAG, "FlutterBannerAd onAdLoadFailed");
        manager.onAdLoadFailed(adId, i, s);
    }

    @Override
    public void onAdClicked() {
        Log.d(TAG, "FlutterBannerAd onAdClicked");
        manager.onAdClicked(adId);
    }

}
```

### dart main 파일에서 배너 구현

```dart
void _createBannerWithSize(int width, int height) {
    debugPrint('Creating BannerAd with size ${width}x${height}');
    _banner = BannerAd(
      listener: BannerAdListener(
        onReceiveAd: (ad) {
          debugPrint('BannerAdListener onReceiveAd for size ${width}x${height} received');
          setState(() {}); // 광고 로드 성공 시 UI 갱신
        },
        onFailedToReceiveAd: (ad, errorCode, errorMessage) {
          debugPrint(
              'BannerAdListener onFailedToReceiveAd for size ${width}x${height}: errorCode: $errorCode, errorMessage: $errorMessage');
          ad.dispose();
          setState(() {
            _banner = null;
          });
        },
        onCloseLandingScreen: (ad) {
          debugPrint('BannerAdListener onCloseLandingScreen for size ${width}x${height}');
        },
        onShowLandingScreen: (ad) {
          debugPrint('BannerAdListener onShowLandingScreen for size ${width}x${height}');
        },
      ),
      // AdInfo에 배너 사이즈 정보를 전달합니다.
      adInfo: AdInfo("AU1718694072940", BannerHeightEnum.adaptive, width, height),
    )..load();
    debugPrint('BannerAd.load() called for size ${width}x${height}');
  }
```
