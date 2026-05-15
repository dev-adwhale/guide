# 어댑터

{% hint style="info" %}
[Custom event 추가](https://support.google.com/admob/answer/13407144?hl=ko\&visit_id=638568873946009774-580584833\&rd=1) 안내에 따라 AdMob UI에서 설정을 완료합니다.
{% endhint %}

| 설정 항목      | 설명                                                                                                    |
| ---------- | ----------------------------------------------------------------------------------------------------- |
| 매핑 이름      | 광고 소스를 정의하는 고유한 이름입니다.                                                                                |
| Class Name | <p>Custom event 어댑터를 구현하는 클래스의 정규화된 이름입니다.</p><p>(예: com.example.rnadmob.AdWhaleMediationAdapter)</p> |
| Parameter  | <p>Custom event 어댑터에 전달되는 문자열 인수입니다.</p><p>발급받은 PlacementUid를 입력합니다.</p>                              |

### Custom event class 구현

프로젝트의 android 위치에 Custom event class 파일을 생성합니다.

(예: android/app/src/main/java/com/example/rnadmob/AdWhaleMediationAdapter.java)

```java
public class AdWhaleMediationAdapter extends Adapter {

    protected static final String TAG = AdWhaleMediationAdapter.class.getSimpleName();

    private AdWhaleMediationBanner banner;

    // 어댑터 초기화
    @Override
    public void initialize(@NonNull Context context, @NonNull InitializationCompleteCallback initializationCompleteCallback, @NonNull List<MediationConfiguration> list) {
        initializationCompleteCallback.onInitializationSucceeded();
    }

    // 배너 광고 요청
    @Override
    public void loadBannerAd(@NonNull MediationBannerAdConfiguration mediationBannerAdConfiguration, @NonNull MediationAdLoadCallback<MediationBannerAd, MediationBannerAdCallback> callback) {
        banner = new AdWhaleMediationBanner(mediationBannerAdConfiguration, callback);
        banner.loadAd();
    }
    
    // AdWhaleMediation SDK 버전
    @NonNull
    @Override
    public VersionInfo getSDKVersionInfo() {
        String versionString = "1.0.5";
        String[] splits = versionString.split("\\.");

        if (splits.length >= 3) {
            int major = Integer.parseInt(splits[0]);
            int minor = Integer.parseInt(splits[1]);
            int micro = Integer.parseInt(splits[2]);
            return new VersionInfo(major, minor, micro);
        }

        return new VersionInfo(0, 0, 0);
    }

    // Custom event adapter 버전
    @NonNull
    @Override
    public VersionInfo getVersionInfo() {
        String versionString = "1.0.5.0";
        String[] splits = versionString.split("\\.");

        if (splits.length >= 4) {
            int major = Integer.parseInt(splits[0]);
            int minor = Integer.parseInt(splits[1]);
            int micro = Integer.parseInt(splits[2]) * 100 + Integer.parseInt(splits[3]);
            return new VersionInfo(major, minor, micro);
        }

        return new VersionInfo(0, 0, 0);
    }
}

```

### Sample Code

{% embed url="https://github.com/dev-adwhale/ADwhaleMediationSDK-Android/blob/main/Admob%20Custom%20Events/CustomEvent_ReactNative/AdWhaleMediationAdapter.java" %}
