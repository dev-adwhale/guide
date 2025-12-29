# 보상형

{% tabs %}
{% tab title="Dart" %}
```dart
AdWhaleInterstitialAd({
    required this.appCode,
    required this.adLoadCallback,
  });
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>발급받은 placement</td></tr><tr><td>RewardAdLoadCallback</td><td>보상형 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

RewardAdLoadCallback

| 리스너 구성                 | 설명          |
| ---------------------- | ----------- |
| onRewardAdLoaded       | 광고 로드시      |
| onRewardAdFailedToLoad | 광고 로드 실패    |
| onRewardAdShowed       | 광고 화면 랜딩    |
| onRewardAdFailedToShow | 광고 화면 랜딩 실패 |
| onRewardAdDismissed    | 광고 화면 종료    |
| onRewardAdClicked      | 광고 화면 클릭    |
| onUserRewarded         | 유저가 받은 리워드  |
{% endtab %}
{% endtabs %}

### 보상형 구현 예제

```dart
void _createReward() {
    print('FlutterRewardAd _createReward()');
    _adwhaleRewardAd = AdWhaleRewardAd(
      appCode: "발급 받은 placementUid",
      adRewardLoadCallback: RewardAdLoadCallback(
        onRewardAdLoaded: () {
          print('FlutterRewardAd onAdLoaded');
          _adwhaleRewardAd!.show();
        },
        onRewardAdFailedToLoad: (errorCode, errorMessage) {
          print('FlutterRewardAd onAdFailedToLoad: errorCode: $errorCode, errorMessage: $errorMessage');
          _adwhaleRewardAd = null;
        },
        onUserRewarded: (amount, type) {
          print('FlutterRewardAd onUserRewarded');
          print('FlutterRewardAd onUserRewarded: amount: $amount, type: $type');
        },
        onRewardAdClicked: () {
          print('FlutterRewardAd onAdClicked');
        },
        onRewardAdShowed: () {
          print('FlutterRewardAd onAdShowed');
          _adwhaleRewardAd = null;
        },
        onRewardFailedToShow: (String errorCode, String errorMessage) {
          print('FlutterRewardAd onFailedToShow: errorCode: $errorCode, errorMessage: $errorMessage');
        },
        onRewardAdDismissed: () {
          print('FlutterRewardAd onDismissed');
        },
      ),
    );
    _adwhaleRewardAd!.load();
  }
```
