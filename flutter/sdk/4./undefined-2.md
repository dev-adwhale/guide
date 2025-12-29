# 전면

{% tabs %}
{% tab title="Dart" %}
```dart
AdWhaleInterstitialAd({
    required this.appCode,
    required this.adLoadCallback,
  });
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>발급받은 placement</td></tr><tr><td>InterstitialAdLoadCallback</td><td>전면 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

InterstitialAdLoadCallback

| 리스너 구성                     | 설명          |
| -------------------------- | ----------- |
| onInterstitialAdLoaded     | 광고 로드시      |
| onInterstitialAdLoadFailed | 광고 로드 실패    |
| onInterstitialAdShowed     | 광고 화면 랜딩    |
| onInterstitialAdShowFailed | 광고 화면 랜딩 실패 |
| onInterstitialAdClosed     | 광고 화면 종료    |
| onInterstitialAdClicked    | 광고 화면 클릭    |
{% endtab %}
{% endtabs %}

### 전면 구현 예제

```dart
void _createInterstitialAd() {
    print('FlutterInterstitialAd _createInterstitialAd()');
    _adwhaleInterstitialAd = AdWhaleInterstitialAd(
      appCode: "발급 받은 placementUid",
      adLoadCallback: InterstitialAdLoadCallback(
        onInterstitialAdLoaded: () {
          print('FlutterInterstitialAd onAdLoaded');
          _adwhaleInterstitialAd!.show();
        },
        onInterstitialAdLoadFailed: (errorCode, errorMessage) {
          print('FlutterInterstitialAd onAdLoadFailed: $errorMessage');
          _adwhaleInterstitialAd = null;
        },
        onInterstitialAdShowed: () {
          print('FlutterInterstitialAd onAdShowed');
        },
        onInterstitialAdShowFailed: (errorCode, errorMessage) {
          print('FlutterInterstitialAd onAdShowFailed: $errorMessage');
          _adwhaleInterstitialAd = null;
        },
        onInterstitialAdClosed: () {
          print('FlutterInterstitialAd onAdClosed');
        },
        onInterstitialAdClicked: () {
          print('FlutterInterstitialAd onAdClicked');
        },

      ),
    );
    _adwhaleInterstitialAd?.load();
  }
```
