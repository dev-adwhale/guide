# Ad Network Gradle Builder

## 수익화 방식 설정

**운영대행**

**제휴매체**

## 미디에이션 네트워크 선택

**Admob**

**Unity(Levelplay)**

## 네트워크 선택 (체크하면 아래 Gradle 설정이 자동 생성)

전체 선택전체 해제전체 코드 복사현재: Groovy※ 검색 후 '전체 선택' 가능 • 버튼으로 DSL 전환

* [ ] AppLovin
* [ ] DT Exchange
* [ ] InMobi
* [ ] Liftoff Monetize
* [ ] Mintegral
* [ ] Pangle
* [ ] Unity Ads
* [ ] Moloco
* [ ] Ironsource

***

### 프로젝트 레벨 `build.gradle` (루트) repositories

이 블록만 복사

```
// 프로젝트 루트의 build.gradle
allprojects {
    repositories {
        google()
        mavenCentral()

        // Adwhale Mediation SDK Repository
        maven { url 'https://dev-adwhale.github.io/adwhale-sdk-android-maven/maven-repo' }
    }
}
```

### 앱 모듈 `app/build.gradle` dependencies (implementation)

이 블록만 복사

```
// 앱 모듈의 app/build.gradle
  android {
      ...
      compileOptions {
          sourceCompatibility JavaVersion.VERSION_11
          targetCompatibility JavaVersion.VERSION_11
      }
  }

dependencies {
    implementation 'net.adwhale.sdk.mediation:adwhale-mediation-sdk:2.7.2' // AdWhale Mediation SDK dependency
    implementation 'net.adwhale.sdk.admob.adapter:admob-sdk:24.3.0.2' // Admob SDK dependency
}
```

### `AndroidManifest.xml` 권한 설정

이 블록만 복사

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <!--SDK 권한 추가 시작-->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
    <uses-permission android:name="com.google.android.gms.permission.AD_ID"/>
    <!--SDK 권한 추가 끝-->

    <!--네트워크 보안 구성 추가(android:usesCleartextTraffic="true")
    android9(API 28)부터 강화된 네트워크 보안정책으로 인해
    application에 값(android:usesCleartextTraffic="true")을 삽입합니다.
    광고 이미지 url load, 트래킹요소, resource가 http 구성될 수 있으므로 해당옵션을 삽입합니다.-->
    <application
           ...
           android:usesCleartextTraffic="true"
           ...>

           ...

           <!--SDK 연동 키 설정1: PUBLISHER_UID 설정
           AndroidManifest.xml 파일에 에 태그를 추가합니다.-->
           <meta-data
               android:name="net.adwhale.sdk.mediation.PUBLISHER_UID"
               android:value="발급받은 PUBLISHER_UID 값" />

           <!--SDK 연동 키 설정2: Admob APP ID 설정
           AndroidManifest.xml 파일에 에 태그를 추가합니다.-->
           <meta-data
               android:name="com.google.android.gms.ads.APPLICATION_ID"
               android:value="애드웨일이 제공한 Admob APP ID 값" />

           ...

    </application>

</manifest>
```

### `proguard-rules.pro` 난독화 설정

이 블록만 복사

```
#================== AdWhale Mediation SDK Proguard for Release 적용 코드 시작 ==================

-keepclasseswithmembers class net.adwhale.sdk.mediation.ads.AdWhaleMediationAds {
    public static *** init(***);
    public static *** init(***, ***);
    public static *** setCoppa(***);
    public static *** setGdpr(***);
    public static *** getGdpr(***);
    public static *** requestGdprConsent(***, ***);
    public static *** isCoppaApplied();
    public static *** getGdprConsentStatus(***);
    public static *** resetGdprConsentStatus(***);
    public static *** getAdwhaleGDPR(***);
}

-keep interface net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener {*;}

-keepclasseswithmembers class net.adwhale.sdk.utils.AdWhaleLog {
    public static *** setLogLevel(***);
    public static *** getLogLevel();
}

-keep class net.adwhale.sdk.mediation.ads.ADWHALE_AD_SIZE {*;}
-keep class net.adwhale.sdk.mediation.ads.ADWHALE_RESULT_CODE {*;}
-keep class net.adwhale.sdk.mediation.ads.ADWHALE_NATIVE_TEMPLATE {*;}
-keep class net.adwhale.sdk.mediation.ads.GdprConsentStatus {*;}

-keep class net.adwhale.sdk.utils.AdWhaleLog$LogLevel{*;}
-keep class net.adwhale.sdk.impl.mediation.ReqMediationAdConfig {*;}
-keep class net.adwhale.sdk.impl.mediation.ResMediationAdConfig {*;}
-keep class net.adwhale.sdk.impl.mediation.ResMediation {*;}

-keepclasseswithmembers class net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAd {
    public <init>(...);
    public *** loadAd();
    public *** showAd();
    public *** showAd(***);
    public *** setRegion(***);
    public *** setGcoder(***, ***);
    public *** setPlacementName(***);
    public *** destroy();
    public *** setAdWhaleMediationInterstitialAdListener(***);
}

-keep interface net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAdListener {*;}

-keepclasseswithmembers class net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardAd {
    public <init>(...);
    public *** loadAd(***);
    public *** showAd(***);
    public *** showAd(***, ***);
    public *** setRegion(***);
    public *** setGcoder(***, ***);
    public *** setPlacementName(***);
    public *** destroy();
    public *** setAdWhaleMediationFullScreenContentCallback(***);
}

-keep class net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardItem {*;}
-keep interface net.adwhale.sdk.mediation.ads.AdWhaleMediationFullScreenContentCallback {*;}
-keep interface net.adwhale.sdk.mediation.ads.AdWhaleMediationRewardedAdLoadCallback {*;}
-keep interface net.adwhale.sdk.mediation.ads.AdWhaleMediationUserEarnedRewardListener {*;}

-keepclasseswithmembers class net.adwhale.sdk.mediation.ads.AdWhaleMediationNativeAdView {
    public <init>(...);
    public *** loadAdWithBinder(***);
    public *** loadAdWithTemplate(***);
    public *** show();
    public *** setDebugEnabled(***);
    public *** setAdWhaleMediationNativeAdViewListener(***);
    public *** setPlacementUid(***);
    public *** setRegion(***);
    public *** setGcoder(***, ***);
    public *** resume();
    public *** pause();
    public *** destroy();
}

-keep class net.adwhale.sdk.mediation.ads.AdWhaleNativeAdBinder {*;}
-keep class net.adwhale.sdk.mediation.ads.AdWhaleNativeAdBinder$Builder {*;}
-keep class net.adwhale.sdk.mediation.ads.AdWhaleMediationNativeTemplateStyle {*;}
-keep class net.adwhale.sdk.mediation.ads.AdWhaleMediationNativeTemplateStyle$Builder {*;}

-keep interface net.adwhale.sdk.mediation.ads.AdWhaleMediationNativeAdViewListener {*;}

-keepclasseswithmembers class net.adwhale.sdk.mediation.ads.AdWhaleMediationAdView {
    public <init>(...);
    public *** loadAd();
    public *** destroy();
    public *** setAdWhaleMediationAdViewListener(***);
    public *** setAdwhaleAdSize(***);
    public *** setPlacementUid(***);
    public *** setRegion(***);
    public *** setGcoder(***, ***);
    public *** setPlacementName(***);
    public *** setAdaptiveAnchorWidth(***);
    public *** resume();
    public *** pause();
}

-keep interface net.adwhale.sdk.mediation.ads.AdWhaleMediationAdViewListener {*;}

-keepclasseswithmembers class net.adwhale.sdk.mediation.ads.AdWhaleMediationAdBannerView {
    public <init>(...);
    public *** loadAd();
    public *** show();
    public *** setRegion(***);
    public *** setPlacementName(***);
    public *** destroy();
    public *** setAdWhaleMediationAdBannerViewListener(***);
    public *** setAdaptiveAnchorWidth(***);
    public *** resume();
    public *** pause();
}

-keep interface net.adwhale.sdk.mediation.ads.AdWhaleMediationAdBannerViewListener {*;}

-keepclasseswithmembers class net.adwhale.sdk.mediation.ads.AdWhaleMediationAppOpenAd {
    public <init>(...);
    public *** loadAd();
    public *** showAd(***);
    public *** setRegion(***);
    public *** setGcoder(***, ***);
    public *** setPlacementUid(***);
    public *** setPlacementName(***);
    public *** destroy();
    public *** setAdWhaleMediationAppOpenAdListener(***);
}

-keep interface net.adwhale.sdk.mediation.ads.AdWhaleMediationAppOpenAdListener {*;}

#================== AdWhale Mediation SDK Proguard for Release 적용 코드 끝 ==================

#================== AdWhale AdManager Adapter SDK Proguard for Release 적용 코드 시작 ==================

-keep class net.adwhale.sdk.admob.adapter.AdManagerAdBannerLoader {*;}

-keep class net.adwhale.sdk.admob.adapter.AdManagerAdBannerPreLoader {*;}

-keep class net.adwhale.sdk.admob.adapter.AdManagerAdInterstitialLoader {*;}

-keep class net.adwhale.sdk.admob.adapter.AdManagerAdNativeTemplateLoader {*;}

-keep class net.adwhale.sdk.admob.adapter.AdManagerAdNativeCustomBindingLoader {*;}

-keep class net.adwhale.sdk.admob.adapter.AdManagerAdRewardLoader {*;}

-keep class net.adwhale.sdk.admob.adapter.AdManagerAdRewardedInterstitialLoader {*;}
-keep class net.adwhale.sdk.admob.adapter.AdManagerAdAppOpeningLoader {*;}

#================== AdWhale AdManager Adapter SDK Proguard for Release 적용 코드 끝 ==================

#================== AdWhale Admob Adapter SDK Proguard for Release 적용 코드 시작 ==================

-keep class net.adwhale.sdk.admob.adapter.AdmobAdBannerLoader {*;}

-keep class net.adwhale.sdk.admob.adapter.AdmobAdBannerPreLoader {*;}

-keep class net.adwhale.sdk.admob.adapter.AdmobAdInterstitialLoader {*;}

-keep class net.adwhale.sdk.admob.adapter.AdmobAdNativeTemplateLoader {*;}

-keep class net.adwhale.sdk.admob.adapter.AdmobAdNativeCustomBindingLoader {*;}

-keep class net.adwhale.sdk.admob.adapter.AdmobAdRewardLoader {*;}

-keep class net.adwhale.sdk.admob.adapter.AdmobAdRewardedInterstitialLoader {*;}

-keep class net.adwhale.sdk.admob.adapter.AdmobAdAppOpeningLoader {*;}

-keep class net.adwhale.sdk.admob.adapter.custom.cauly.AdMobCaulyEvent {*;}

-keep class net.adwhale.sdk.admob.adapter.custom.cauly.CaulyMediationBannerAd {*;}

-keep class net.adwhale.sdk.admob.adapter.custom.cauly.CaulyMediationInterstitialAd {*;}

-keep class net.adwhale.sdk.admob.adapter.custom.cauly.CaulyMediationRewardAd {*;}

# AdWhale AdMob Adapter - Keep the public entry points for reflection
-keep public class net.adwhale.sdk.admob.adapter.AdMobPrivacyAdapter {
    public static net.adwhale.sdk.admob.adapter.AdMobPrivacyAdapter getInstance();
}

#================== AdWhale Admob Adapter SDK Proguard for Release 적용 코드 끝 ==================
```
