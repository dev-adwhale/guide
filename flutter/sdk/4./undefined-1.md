# 배너

{% tabs %}
{% tab title="Dart" %}
```dart
AdWhaleAdView({
    required AdWithViewListener listener,
    required this.adInfo,
  }) : super(listener: listener);
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>AdWhaleAdViewListener Listener</td><td>배너 미디에이션 광고 호출 콜백 리스너</td></tr><tr><td>AdInfo adInfo</td><td>발급받은 placementUid, BannerHeightEnum</td></tr></tbody></table>

AdWhaleAdViewListener

| 리스너 구성               | 설명       |
| -------------------- | -------- |
| onAdLoaded           | 광고 로드시   |
| onAdLoadFailed       | 광고 로드 실패 |
| onShowLandingScreen  | 광고 화면 랜딩 |
| onCloseLandingScreen | 광고 화면 종료 |
| onAdClicked          | 광고 클릭    |

```
onAppOpenAdLoadFailed: (errorCode, errorMessage) // 미디에이션 배너 광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>errorCode</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>errorMessage</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

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
void _createBanner(BannerHeightEnum bannerSize, {int? adaptiveAnchorWidth}) {
    debugPrint(
      'GuideSamplePage _createBanner size=$bannerSize, adaptiveWidth=$adaptiveAnchorWidth',
    );
    _adWhaleAdView =
        AdWhaleAdView(
            listener: AdWhaleAdViewListener(
              onAdLoaded: (ad) {
                debugPrint(
                  'GuideSamplePage BannerAd onAdLoaded for size $bannerSize',
                );
                setState(() {});
              },
              onAdLoadFailed: (ad, errorCode, errorMessage) {
                debugPrint(
                  'GuideSamplePage BannerAd onAdLoadFailed for $bannerSize: errorCode: $errorCode, errorMessage: $errorMessage',
                );
                ad.dispose();
                setState(() {
                  _adWhaleAdView = null;
                });
              },
              onAdClicked: (ad) {
                debugPrint(
                  'GuideSamplePage BannerAd onAdClicked for size $bannerSize',
                );
              },
            ),
            adInfo: AdInfo('발급받은 placementUid', bannerSize),
          )

    if (adaptiveAnchorWidth != null) {
      _adWhaleAdView!.setAdaptiveAnchorWidth(adaptiveAnchorWidth);
    }
    _adWhaleAdView!.load();
  }
```

