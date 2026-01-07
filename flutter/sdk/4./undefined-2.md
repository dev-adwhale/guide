# 전면

{% tabs %}
{% tab title="Dart" %}
```dart
AdWhaleInterstitialAd({
    required this.appCode,
    required this.adLoadCallback,
  });
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>발급받은 placementUis</td></tr><tr><td>AdWhaleInterstitialAdLoadCallback</td><td>전면 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

AdWhaleInterstitialAdLoadCallback

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

```dart
AdWhaleInterstitialAd.load() // 미디에이션 전면 광고로드
```

```dart
AdWhaleInterstitialAd.show() // 미디에이션 전면 광고로드 후 표시할 때 호출
```

### 전면 구현 예제

```dart
void _loadInterstitial() {
    _interstitialAd?.dispose();
    _interstitialAd = null;
    _isInterstitialLoaded = false;

    _interstitialAd =
        AdWhaleInterstitialAd(
            appCode: '발급받은 placementUid',
            adLoadCallback: AdWhaleInterstitialAdLoadCallback(
              onInterstitialAdLoaded: () {
                debugPrint('GuideSamplePage Interstitial onAdLoaded');
                _isInterstitialLoaded = true;
                if (mounted) {
                  ScaffoldMessenger.of(
                    context,
                  ).showSnackBar(const SnackBar(content: Text('전면 광고 로드 완료')));
                }
              },
              onInterstitialAdLoadFailed: (errorCode, errorMessage) {
                debugPrint(
                  'GuideSamplePage Interstitial onAdLoadFailed: $errorMessage',
                );
                _interstitialAd = null;
                _isInterstitialLoaded = false;
              },
              onInterstitialAdShowed: () {
                debugPrint('GuideSamplePage Interstitial onAdShowed');
              },
              onInterstitialAdShowFailed: (errorCode, errorMessage) {
                debugPrint(
                  'GuideSamplePage Interstitial onAdShowFailed: $errorMessage',
                );
                _interstitialAd = null;
                _isInterstitialLoaded = false;
              },
              onInterstitialAdClosed: () {
                debugPrint('GuideSamplePage Interstitial onAdClosed');
              },
              onInterstitialAdClicked: () {
                debugPrint('GuideSamplePage Interstitial onAdClicked');
              },
            ),
          )
    _interstitialAd!.load();
  }
  void _showInterstitial() {
    if (_interstitialAd == null || !_isInterstitialLoaded) {
      if (mounted) {
        ScaffoldMessenger.of(
          context,
        ).showSnackBar(const SnackBar(content: Text('전면 광고를 먼저 로드해 주세요.')));
      }
      return;
    }
    _interstitialAd!.show();
    _isInterstitialLoaded = false;
    _interstitialAd = null;
  }
```
