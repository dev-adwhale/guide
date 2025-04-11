# 보상형 전면

보상형 전면 생성

**AdWhaleMediationRewardAd 클래스 API 설명**

```java
public AdWhaleMediationRewardAd(String placementUid)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```java
public void setAdWhaleMediationFullScreenContentCallback(AdWhaleMediationFullScreenContentCallback listener)
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationFullScreenContentCallback</p></td><td>보상형 전면 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```java
public void loadAd(AdWhaleMediationRewardAdLoadCallback listener) // 미디에이션 보상형 전면 광고로드 시 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationRewardAdLoadCallback</p></td><td>보상형 전면 미디에이션 광고 로드 콜백 리스너</td></tr></tbody></table>

```java
public void showAd(AdWhaleMediationUserEarnedRewardListener listener) // 미디에이션 보상형 전면 광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationUserEarnedRewardListener</p></td><td>보상형 전면 미디에이션 광고 리워드 지급 콜백 리스너</td></tr></tbody></table>

```java
public void destroy() // onDestroy() 시 호출
```



**AdWhaleMediationFullScreenContentCallback 클래스 API 설명**

```java
public void onAdClicked() // 미디에이션 보상형 전면 광고클릭 시
```

```java
public void onAdDismissed() // 미디에이션 보상형 전면 광고닫기 시
```

```java
public void onFailedToShow(int statusCode, String message) // 미디에이션 보상형 전면 광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>광고표시 결과 코드</td></tr><tr><td>String</td><td>초기화 결과 메시지</td></tr></tbody></table>

```java
public void onAdShowed() // 미디에이션 보상형 전면 광고표시 후
```



**AdWhaleMediationRewardAdLoadCallback 클래스 API 설명**

```java
public void onAdLoaded(AdWhaleMediationRewardAd adWhaleMediationRewardAd, String message) // 미디에이션 보상형 전면 광고로드 성공 시
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>AdWhaleMediationRewardAd</td><td>AdWhaleMediationRewardAd 객체</td></tr><tr><td>String</td><td>광고로드 결과 메시지</td></tr></tbody></table>

```java
public void onFailedToShow(int statusCode, String message) // 미디에이션 보상형 전면 광고로드 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td>광고표시 결과 코드</td></tr><tr><td>String</td><td>광고표시 결과 메시지</td></tr></tbody></table>



**AdWhaleMediationUserEarnedRewardListener 클래스 API 설명**

```java
public void onUserRewarded(AdWhaleMediationRewardItem rewardItem) // 미디에이션 보상형 전면 리워드지급 성공 시
```

<table data-header-hidden><thead><tr><th width="356">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>AdWhaleMediationRewardtem</td><td>리워드 타입, 리워드 금액 값을 지니는 객체</td></tr></tbody></table>



**보상형 전면 구현 샘플은 아래와 같습니다.**&#x20;

```java

import android.util.Log;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;

import net.adwhale.sdk.mediation.ads.AdWhaleMediationFullScreenContentCallback;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardAd;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardItem;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardedAdLoadCallback;
import net.adwhale.sdk.mediation.ads.AdWhaleMediationUserEarnedRewardListener;

import java.lang.ref.WeakReference;

public class FlutterRewardAd extends FlutterAd.FlutterOverlayAd{

    private static final String TAG = "FlutterRewardAd";

    @NonNull
    private final AdInstanceManager manager;

    @NonNull
    private final String appCode;

    @Nullable
    private AdWhaleMediationRewardAd ad;
    @NonNull
    private final FlutterRewardAdLoader flutterRewardAdLoader;

    public FlutterRewardAd(
            int adId,
            @NonNull AdInstanceManager manager,
            @NonNull String appCode,
            @NonNull FlutterRewardAdLoader flutterRewardAdLoader) {
        super(adId);
        Log.d(TAG, "FlutterRewardAdLoader()");
        this.manager = manager;
        this.appCode = appCode;
        this.flutterRewardAdLoader = flutterRewardAdLoader;
    }
    @Override
    void load() {
        Log.d(TAG, "FlutterRewardAd load()");
        Log.d(TAG, "FlutterRewardAd load() appCode : " + appCode);
        ad = flutterRewardAdLoader.loadReward(appCode,
                new DelegatingRewardAdFullScreenContentCallback(this), new DelegatingRewardAdLoadCallback(this));
    }

    public void onRewardAdLoaded() {
        Log.d(TAG, "FlutterRewardAd onAdLoaded()");
        manager.onRewardAdLoaded(adId);
    }

    public void onRewardAdFailedToLoad(int errorCode, String errorMessage) {
        Log.d(TAG, "FlutterRewardAd onAdFailedToLoad()");
        manager.onRewardAdFailedToLoad(adId, errorCode, errorMessage);
    }

    public void onUserRewarded(int i, String s){
        Log.d(TAG, "FlutterRewardAd.onUserRewarded()");
        manager.onUserRewarded(adId, i,s);
    }

    public void onRewardAdClicked() {
        Log.d(TAG, "FlutterRewardAd onAdClicked()");
        manager.onRewardAdClicked(adId);
    }
    public void onRewardAdDismissed() {
        Log.d(TAG, "FlutterRewardAd onAdDismissed()");
        manager.onRewardAdDismissed(adId);
    }
    public void onRewardFailedToShow(int errorCode, String errorMessage) {
        Log.d(TAG, "FlutterRewardAd onFailedToShow()");
        manager.onRewardFailedToShow(adId, errorCode, errorMessage);
    }
    public void onRewardAdShowed() {
        Log.d(TAG, "FlutterRewardAd onAdShowed()");
        manager.onRewardAdShowed(adId);
    }

    @Override
    void show() {
        if (ad == null) {
            Log.e(TAG, "Error showing reward - the reward ad wasn't loaded yet.");
            return;
        }
        if (manager.getActivity() == null) {
            Log.e(TAG, "Tried to show reward before activity was bound to the plugin.");
            return;
        }
        ad.showAd(new DelegatingRewardAdUserEarnedCallback(this));
    }

    @Override
    void dispose() {
        ad = null;
    }

    private static final class DelegatingRewardAdUserEarnedCallback implements AdWhaleMediationUserEarnedRewardListener{

        private final WeakReference<FlutterRewardAd> delegate;

        DelegatingRewardAdUserEarnedCallback(FlutterRewardAd delegate) {
            this.delegate = new WeakReference<>(delegate);
            Log.d(TAG, "DelegatingRewardAdUserEarnedCallback()");
        }

        @Override
        public void onUserRewarded(AdWhaleMediationRewardItem adWhaleMediationRewardItem) {
            Log.d(TAG, "DelegatingRewardAdUserEarnedCallback.onUserRewarded()");
            Log.d(TAG, "DelegatingRewardAdUserEarnedCallback.onUserRewarded() amount : " + adWhaleMediationRewardItem.getRewardAmount());
            Log.d(TAG, "DelegatingRewardAdUserEarnedCallback.onUserRewarded() type : " + adWhaleMediationRewardItem.getRewardType());
            if(delegate.get() != null){
                delegate.get().onUserRewarded(adWhaleMediationRewardItem.getRewardAmount(), adWhaleMediationRewardItem.getRewardType());
            }
        }
    }
    private static final class DelegatingRewardAdFullScreenContentCallback implements AdWhaleMediationFullScreenContentCallback{

        private final WeakReference<FlutterRewardAd> delegate;

        DelegatingRewardAdFullScreenContentCallback(FlutterRewardAd delegate) {
            this.delegate = new WeakReference<>(delegate);
            Log.d(TAG, "DelegatingRewardAdFullScreenContentCallback()");
        }

        @Override
        public void onAdClicked() {
            Log.d(TAG, "DelegatingRewardAdFullScreenContentCallback.onAdClicked()");
            if(delegate.get() != null){
                delegate.get().onRewardAdClicked();
            }
        }

        @Override
        public void onAdDismissed() {
            Log.d(TAG, "DelegatingRewardAdFullScreenContentCallback.onAdDismissed()");
            if(delegate.get() != null){
                delegate.get().onRewardAdDismissed();
            }
        }

        @Override
        public void onFailedToShow(int i, String s) {
            Log.d(TAG, "DelegatingRewardAdFullScreenContentCallback.onFailedToShow()");
            if(delegate.get() != null){
                delegate.get().onRewardFailedToShow(i,s);
            }
        }

        @Override
        public void onAdShowed() {
            Log.d(TAG, "DelegatingRewardAdFullScreenContentCallback.onAdShowed()");
            if(delegate.get() != null){
                delegate.get().onRewardAdShowed();
            }
        }

    }
    private static final class DelegatingRewardAdLoadCallback implements AdWhaleMediationRewardedAdLoadCallback{

        private final WeakReference<FlutterRewardAd> delegate;

        DelegatingRewardAdLoadCallback(FlutterRewardAd delegate) {
            this.delegate = new WeakReference<>(delegate);
            Log.d(TAG, "DelegatingRewardAdAdLoadCallback()");
        }
        @Override
        public void onAdLoaded(AdWhaleMediationRewardAd adWhaleMediationRewardAd, String s) {
            Log.d(TAG, "DelegatingRewardAdLoadCallback.onAdLoaded()");
            if(delegate.get() != null){
                delegate.get().onRewardAdLoaded();
            }
        }

        @Override
        public void onAdFailedToLoad(int i, String s) {
            Log.d(TAG, "DelegatingRewardAdLoadCallback.onAdFailedToLoad()");
            if(delegate.get() != null){
                delegate.get().onRewardAdFailedToLoad(i, s);
            }
        }

    }

}
```

```java
public class FlutterRewardAdLoader {

    private static final String TAG = "FlutterInterstitialAdLoader";
    @NonNull
    private final Context context;

    public FlutterRewardAdLoader(@NonNull Context context) {
        this.context = context;
    }

    /**
     * Load an interstitial ad.
     */
    public AdWhaleMediationRewardAd loadReward(
            @NonNull String appCode,
            @NonNull AdWhaleMediationFullScreenContentCallback showCallback,
            @NonNull AdWhaleMediationRewardedAdLoadCallback loadCallback) {

        if(loadCallback == null) {
            Log.e(TAG, "loadCallback is null.");
        }
        if(showCallback == null) {
            Log.e(TAG, "loadCallback is null.");
        }
        if(!(showCallback instanceof AdWhaleMediationFullScreenContentCallback)){
            Log.e(TAG, "loadCallback is not type AdWhaleMediationFullScreenContentCallback.");
        }
        if(!(loadCallback instanceof AdWhaleMediationRewardedAdLoadCallback)){
            Log.e(TAG, "loadCallback is not type AdwhaleMediationInterstitialAdListener.");
        }

        AdWhaleMediationRewardAd rewardAd = new AdWhaleMediationRewardAd(appCode);
        rewardAd.setAdWhaleMediationFullScreenContentCallback(showCallback);
        rewardAd.loadAd(loadCallback);

        return rewardAd;
    }

}
```

### dart main 파일에서 보상형 구현

```dart
void _createReward() {
    debugPrint('_createReward()');
    _rewardAd = RewardAd(
      appCode: "보상형 광고 코드 입력",
      adRewardLoadCallback: RewardAdLoadCallback(
        onRewardAdLoaded: () {
          debugPrint('RewardAd onRewardAdLoaded');
          _rewardAd!.show();
        },
        onRewardAdFailedToLoad: (errorCode, errorMessage) {
          debugPrint('RewardAd onRewardAdFailedToLoad: errorCode: $errorCode, errorMessage: $errorMessage');
          _rewardAd = null;
        },
        onUserRewarded: (amount, type) {
          debugPrint('RewardAd onUserRewarded');
          debugPrint('RewardAd onUserRewarded: amount: $amount, type: $type');
        },
        onRewardAdClicked: () {
          debugPrint('RewardAd onRewardAdClicked');
        },
        onRewardAdShowed: () {
          debugPrint('RewardAd onRewardAdShowed');
          _rewardAd = null;
        },
        onRewardFailedToShow: (String errorCode, String errorMessage) {
          debugPrint('RewardAd onRewardFailedToShow: errorCode: $errorCode, errorMessage: $errorMessage');
        },
        onRewardAdDismissed: () {
          debugPrint('RewardAd onRewardAdDismissed');
        },
      ),
    );
    _rewardAd!.load();
  }
```
