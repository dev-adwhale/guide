# 보상형

{% hint style="info" %}
보상형 광고는 사용자가 광고를 시청한 후 보상을 받는 광고 형식입니다. 게임 내 아이템, 코인, 생명 등 다양한 보상을 제공하여 높은 참여도와 수익을 얻을 수 있습니다.
{% endhint %}

{% tabs %}
{% tab title="Dart" %}
```dart
AdWhaleInterstitialAd({
    required this.appCode,
    required this.adLoadCallback,
  });
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>발급받은 placement</td></tr><tr><td>RewardAdLoadCallback</td><td>보상형 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

AdWhaleRewardAdLoadCallback

| 리스너 구성                 | 설명          |
| ---------------------- | ----------- |
| onRewardAdLoaded       | 광고 로드시      |
| onRewardAdFailedToLoad | 광고 로드 실패    |
| onRewardAdShowed       | 광고 화면 랜딩    |
| onRewardAdFailedToShow | 광고 화면 랜딩 실패 |
| onRewardAdDismissed    | 광고 화면 종료    |
| onRewardAdClicked      | 광고 화면 클릭    |
| onUserRewarded         | 유저가 받은 리워드  |

```
onRewardAdFailedToLoad: (errorCode, errorMessage) // 미디에이션 보상형 광고요청 실패 시
onRewardAdFailedToShow: (errorCode, errorMessage) // 미디에이션 보상형 광고 화면 랜딩 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>errorCode</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>errorMessage</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>
{% endtab %}
{% endtabs %}

```dart
AdWhaleRewardAd.load() // 미디에이션 보상형 광고로드
```

```dart
AdWhaleRewardAd.show() // 미디에이션 보상형 광고로드 후 표시할 때 호출
```

### 보상형 구현 예제

```dart
void _loadReward() {
    _rewardAd?.dispose();
    _rewardAd = null;
    _isRewardLoaded = false;

    _rewardAd =
        AdWhaleRewardAd(
            appCode: '발급받은 PlacementUid',
            adRewardLoadCallback: AdWhaleRewardAdLoadCallback(
              onRewardAdLoaded: () {
                debugPrint('GuideSamplePage Reward onAdLoaded');
                _isRewardLoaded = true;
                if (mounted) {
                  ScaffoldMessenger.of(
                    context,
                  ).showSnackBar(const SnackBar(content: Text('보상형 광고 로드 완료')));
                }
              },
              onRewardAdFailedToLoad: (errorCode, errorMessage) {
                debugPrint(
                  'GuideSamplePage Reward onAdFailedToLoad: errorCode: $errorCode, errorMessage: $errorMessage',
                );
                _rewardAd = null;
                _isRewardLoaded = false;
              },
              onUserRewarded: (amount, type) {
                debugPrint(
                  'GuideSamplePage Reward onUserRewarded: $amount, $type',
                );
              },
              onRewardAdClicked: () {
                debugPrint('GuideSamplePage Reward onAdClicked');
              },
              onRewardAdShowed: () {
                debugPrint('GuideSamplePage Reward onAdShowed');
              },
              onRewardFailedToShow: (String errorCode, String errorMessage) {
                debugPrint(
                  'GuideSamplePage Reward onFailedToShow: errorCode: $errorCode, errorMessage: $errorMessage',
                );
              },
              onRewardAdDismissed: () {
                debugPrint('GuideSamplePage Reward onDismissed');
              },
            ),
          )
    _rewardAd!.load();
  }
  void _showReward() {
    if (_rewardAd == null || !_isRewardLoaded) {
      if (mounted) {
        ScaffoldMessenger.of(
          context,
        ).showSnackBar(const SnackBar(content: Text('보상형 광고를 먼저 로드해 주세요.')));
      }
      return;
    }
    _rewardAd!.show();
    _isRewardLoaded = false;
    _rewardAd = null;
  }
```
