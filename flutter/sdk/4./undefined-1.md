# 배너

{% tabs %}
{% tab title="Dart" %}
```dart
AdWhaleBannerAd({
    required AdWithViewListener listener,
    required this.adInfo,
  }) : super(listener: listener);
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>AdWithViewListener listener</td><td>BannerAdListener </td></tr><tr><td>AdInfo adInfo</td><td>발급받은 placement, BannerHeightEnum</td></tr></tbody></table>

BannerAdListener

| 리스너 구성               | 설명       |
| -------------------- | -------- |
| onReceiveAd          | 광고 로드시   |
| onFailedToReceiveAd  | 광고 로드 실패 |
| onShowLandingScreen  | 광고 화면 랜딩 |
| onCloseLandingScreen | 광고 화면 종료 |

```dart
//AdInfo에 들어가는 BannerHeightEnum
//ex)BannerHeightEnum.BANNER320x50
//적응형 배너 사용시 ADAPTIVE_ANCHOR
enum BannerHeightEnum {
  BANNER320x50,
  BANNER320x100,
  BANNER300x250,
  BANNER250x250,
  ADAPTIVE_ANCHOR,
}
```
{% endtab %}
{% endtabs %}

### 배너 구현 예제

```dart
void _createBanner(BannerHeightEnum bannerSize) {
    print('FlutterCreating AdWhaleBannerAd with enum $bannerSize');
    _adwhaleBannerAd = AdWhaleBannerAd(
      listener: BannerAdListener(
        onReceiveAd: (ad) {
          print('FlutterBannerAd onReceiveAd for size $bannerSize received');
          setState(() {}); // 광고 로드 성공 시 UI 갱신
        },
        onFailedToReceiveAd: (ad, errorCode, errorMessage) {
          print('AdWhaleBannerAd onFailedToReceiveAd for $bannerSize: errorCode: $errorCode, errorMessage: $errorMessage');
          ad.dispose();
          setState(() {
            _adwhaleBannerAd = null;
          });
        },
        onCloseLandingScreen: (ad) {
          print('FlutterBannerAd onCloseLandingScreen for size $bannerSize');
        },
        onShowLandingScreen: (ad) {
          print('FlutterBannerAd onShowLandingScreen for size $bannerSize');
        },
      ),
      adInfo: AdInfo("발급받은 placementUid", bannerSize),
    )..load();
    print('FlutterBannerAd load() called for $bannerSize');
  }
```

