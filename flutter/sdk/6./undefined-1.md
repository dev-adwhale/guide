# 전면

#### **전면 생성** <a href="#undefined" id="undefined"></a>

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

\
**전면 구현 샘플은 아래와 같습니다.**&#x20;

```java
import android.util.Log;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;

import net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAd;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAdListener;

import java.lang.ref.WeakReference;

public class FlutterInterstitialAd extends FlutterAd.FlutterOverlayAd {
    private static final String TAG = "FlutterInterstitialAd";

    @NonNull
    private final AdInstanceManager manager;

    @NonNull
    private final String appCode;

    @Nullable
    private AdWhaleMediationInterstitialAd ad;
    @NonNull
    private final FlutterInterstitialAdLoader flutterInterstitialAdLoader;

    public FlutterInterstitialAd(
            int adId,
            @NonNull AdInstanceManager manager,
            @NonNull String appCode,
            @NonNull FlutterInterstitialAdLoader flutterInterstitialAdLoader) {
        super(adId);
        Log.d(TAG, "FlutterInterstitialAd()");
        this.manager = manager;
        this.appCode = appCode;
        this.flutterInterstitialAdLoader = flutterInterstitialAdLoader;
    }

    @Override
    void load() {
        Log.d(TAG, "FlutterInterstitialAd load()");
        Log.d(TAG, "FlutterInterstitialAd load() appCode : " + appCode);
        ad = flutterInterstitialAdLoader.loadInterstitial(appCode,
                new DelegatingInterstitialAdLoadCallback(this));
    }


    public void onInterstitialAdLoaded() {
        Log.d(TAG, "FlutterInterstitialAd onAdLoaded()");
        manager.onInterstitialAdLoaded(adId);
    }

    public void onInterstitialAdLoadFailed(int errorCode, String errorMessage) {
        Log.d(TAG, "FlutterInterstitialAd onAdLoadFailed()");
        manager.onInterstitialAdLoadFailed(adId, errorCode, errorMessage);
    }

    public void onInterstitialAdShowed() {
        Log.d(TAG, "FlutterInterstitialAd onAdShowed()");
        manager.onInterstitialAdShowed(adId);
    }
    public void onInterstitialAdClicked() {
        Log.d(TAG, "FlutterInterstitialAd onAdClicked()");
        manager.onInterstitialAdClicked(adId);
    }

    public void onInterstitialAdShowFailed(int errorCode, String errorMessage) {
        Log.d(TAG, "FlutterInterstitialAd onAdShowFailed()");
        manager.onInterstitialAdShowFailed(adId, errorCode, errorMessage);
    }

    public void onInterstitialAdClosed() {
        Log.d(TAG, "FlutterInterstitialAd onAdClosed()");
        manager.onInterstitialAdClosed(adId);
    }

    @Override
    void dispose() {
        ad = null;
    }

    @Override
    void show() {
        if (ad == null) {
            Log.e(TAG, "Error showing interstitial - the interstitial ad wasn't loaded yet.");
            return;
        }
        if (manager.getActivity() == null) {
            Log.e(TAG, "Tried to show interstitial before activity was bound to the plugin.");
            return;
        }
        ad.showAd();
    }


    /**
     * An InterstitialAdLoadCallback that just forwards events to a delegate.
     */
    private static final class DelegatingInterstitialAdLoadCallback implements AdWhaleMediationInterstitialAdListener {

        private final WeakReference<FlutterInterstitialAd> delegate;

        DelegatingInterstitialAdLoadCallback(FlutterInterstitialAd delegate) {
            this.delegate = new WeakReference<>(delegate);
            Log.d(TAG, "DelegatingInterstitialAdLoadCallback()");
        }

        @Override
        public void onAdLoaded() {
            Log.d(TAG, "DelegatingInterstitialAdLoadCallback onAdLoaded()");
            if(delegate.get() != null){
                delegate.get().onInterstitialAdLoaded();
            }
        }

        @Override
        public void onAdLoadFailed(int errorCode, String errorMessage) {
            Log.d(TAG, "DelegatingInterstitialAdLoadCallback onAdLoadFailed()");
            if(delegate.get() != null){
                delegate.get().onInterstitialAdLoadFailed(errorCode, errorMessage);
            }
        }

        @Override
        public void onAdShowed() {
            Log.d(TAG, "DelegatingInterstitialAdLoadCallback onAdShowed()");
            if(delegate.get() != null){
                delegate.get().onInterstitialAdShowed();
            }
        }

        @Override
        public void onAdClicked() {
            Log.d(TAG, "DelegatingInterstitialAdLoadCallback onAdClicked()");
            if(delegate.get() != null){
                delegate.get().onInterstitialAdClicked();
            }
        }

        @Override
        public void onAdShowFailed(int errorCode, String errorMessage) {
            Log.d(TAG, "DelegatingInterstitialAdLoadCallback onAdShowFailed()");
            if(delegate.get() != null){
                delegate.get().onInterstitialAdShowFailed(errorCode, errorMessage);
            }
        }

        @Override
        public void onAdClosed() {
            Log.d(TAG, "DelegatingInterstitialAdLoadCallback onAdClosed()");
            if(delegate.get() != null){
                delegate.get().onInterstitialAdClosed();
            }
        }
    }
}
```

```java
public class FlutterInterstitialAdLoader {
    private static final String TAG = "FlutterInterstitialAdLoader";
    @NonNull
    private final Context context;

    public FlutterInterstitialAdLoader(@NonNull Context context) {
        this.context = context;
    }

    /**
     * Load an interstitial ad.
     */
    public AdWhaleMediationInterstitialAd loadInterstitial(
            @NonNull String appCode,
            @NonNull AdWhaleMediationInterstitialAdListener loadCallback) {

        if(loadCallback == null) {
            Log.e(TAG, "loadCallback is null.");
        }
        if(!(loadCallback instanceof AdWhaleMediationInterstitialAdListener)){
            Log.e(TAG, "loadCallback is not type AdwhaleMediationInterstitialAdListener.");
        }
        AdWhaleMediationInterstitialAd interstitialAd = new AdWhaleMediationInterstitialAd(appCode);
        interstitialAd.setAdWhaleMediationInterstitialAdListener(loadCallback);
        interstitialAd.loadAd();

        return interstitialAd;
    }
}
```

### dart main 파일에서 전면 구현

```dart
void _createInterstitialAd() {
    debugPrint('_createInterstitialAd()');
    _interstitialAd = InterstitialAd(
      appCode: "전면 광고 코드 입력",
      adLoadCallback: InterstitialAdLoadCallback(
        onInterstitialAdLoaded: () {
          debugPrint('InterstitialAd onInterstitialAdLoaded');
          _interstitialAd!.show();
        },
        onInterstitialAdLoadFailed: (errorCode, errorMessage) {
          debugPrint('InterstitialAd onInterstitialAdLoadFailed: $errorMessage');
          _interstitialAd = null;
        },
        onInterstitialAdShowed: () {
          debugPrint('InterstitialAd onInterstitialAdShowed');
        },
        onInterstitialAdShowFailed: (errorCode, errorMessage) {
          debugPrint('InterstitialAd onInterstitialAdShowFailed: $errorMessage');
          _interstitialAd = null;
        },
        onInterstitialAdClosed: () {
          debugPrint('InterstitialAd onInterstitialAdClosed');
        },
        onInterstitialAdClicked: () {
          debugPrint('InterstitialAd onInterstitialAdClicked');
        },

      ),
    );
    _interstitialAd?.load();
  }
```
