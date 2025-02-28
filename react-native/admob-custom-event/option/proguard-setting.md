# 난독화(proguard) 설정

{% hint style="danger" %}
**난독화 예외 설정이 없이 앱을 난독화 하시면 앱의 빌드가 실패 하거나, 앱 사용중 오류가 발생 할 수 있습니다.**
{% endhint %}

```
#================== AdWhale Mediation SDK Proguard for Release 적용 코드 시작 ==================
-keepclasseswithmembers class net.adwhale.sdk.mediation.ads.AdWhaleMediationAds {
    public static *** init(***);
    public static *** init(***, ***);
}

-keep interface net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener {*;}

-keep class net.adwhale.sdk.mediation.ads.ADWHALE_AD_SIZE {*;}
-keep class net.adwhale.sdk.mediation.ads.ADWHALE_RESULT_CODE {*;}
-keep class net.adwhale.sdk.impl.mediation.ReqMediationAdConfig {*;}
-keep class net.adwhale.sdk.impl.mediation.ResMediationAdConfig {*;}
-keep class net.adwhale.sdk.impl.mediation.ResMediation {*;}

-keepclasseswithmembers class net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAd {
    public <init>(...);
    public *** loadAd();
    public *** showAd();
    public *** setAdWhaleMediationInterstitialAdListener(***);
}

-keep interface net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAdListener {*;}

-keepclasseswithmembers class net.adwhale.sdk.mediation.ads.AdWhaleMediationAdView {
    public <init>(...);
    public *** loadAd();
    public *** destroy();
    public *** setAdWhaleMediationAdViewListener(***);
    public *** setAdwhaleAdSize(***);
    public *** setPlacementUid(***);
    public *** resume();
    public *** pause();
}

-keep interface net.adwhale.sdk.mediation.ads.AdWhaleMediationAdViewListener {*;}

-keepclasseswithmembers class net.adwhale.sdk.mediation.ads.AdWhaleMediationAdBannerView {
    public <init>(...);
    public *** loadAd();
    public *** show();
    public *** destroy();
    public *** setAdWhaleMediationAdBannerViewListener(***);
    public *** resume();
    public *** pause();
}

-keep interface net.adwhale.sdk.mediation.ads.AdWhaleMediationAdBannerViewListener {*;}
#================== AdWhale Mediation SDK Proguard for Release 적용 코드 끝 ==================

#================== AdWhale Cauly Adapter SDK Proguard for Release 적용 코드 시작 ==================

-keep class net.adwhale.sdk.cauly.adapter.CaulyAdBannerLoader {*;}

-keep class net.adwhale.sdk.cauly.adapter.CaulyAdBannerPreLoader {*;}

-keep class net.adwhale.sdk.cauly.adapter.CaulyAdInterstitialLoader {*;}

#================== AdWhale Cauly Adapter SDK Proguard for Release 적용 코드 끝 ==================

#================== AdWhale AdFit Adapter SDK Proguard for Release 적용 코드 시작 ==================

-keep class net.adwhale.sdk.adfit.adapter.AdFitAdBannerLoader {*;}

-keep class net.adwhale.sdk.adfit.adapter.AdFitAdBannerPreLoader {*;}

#================== AdWhale AdFit Adapter SDK Proguard for Release 적용 코드 끝 ==================

#================== AdWhale Techlabs Adapter SDK Proguard for Release 적용 코드 시작 ==================

-keep class net.adwhale.sdk.techlabs.adapter.TechlabsAdBannerLoader {*;}

-keep class net.adwhale.sdk.techlabs.adapter.TechlabsAdBannerPreLoader {*;}

-keep class net.adwhale.sdk.techlabs.adapter.TechlabsAdInterstitialLoader {*;}

#================== AdWhale Techlabs Adapter SDK Proguard for Release 적용 코드 끝 ==================
```
