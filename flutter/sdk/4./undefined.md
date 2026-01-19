# 앱 오프닝 광고

{% hint style="info" %}
앱오프닝 광고는 앱이 실행될 때 또는 백그라운드에서 포그라운드로 전환될 때 표시되는 전면 광고입니다. 이 문서는 Flutter 프로젝트에서 AdWhale Mediation SDK를 사용하여 앱오프닝 광고를 연동하는 방법을 설명합니다.
{% endhint %}

{% tabs %}
{% tab title="Dart" %}
```dart
AdWhaleAppOpenAd({
    required this.placementUid,
    required this.adLoadCallback
  });
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String placementUid</td><td>발급받은 placementUid</td></tr><tr><td>AdWhaleAppOpenAdLoadCallback adLoadCallback</td><td>앱 오프닝 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

AdWhaleAppOpenAdLoadCallback

| 리스너 구성                | 설명          |
| --------------------- | ----------- |
| onAppOpenAdLoaded     | 광고 로드시      |
| onAppOpenAdLoadFailed | 광고 로드 실패    |
| onAppOpenAdShowed     | 광고 화면 랜딩    |
| onAppOpenAdShowFailed | 광고 화면 랜딩 실패 |
| onAppOpenAdDismissed  | 광고 화면 종료    |
| onAppOpenAdClicked    | 광고 화면 클릭    |

```dart
onAppOpenAdLoadFailed: (code, message) // 미디에이션 앱 오프닝 광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>code</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>message</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```dart
AdWhaleAppOpenAd.load() // 미디에이션 앱 오프닝 광고로드
```

```dart
AdWhaleAppOpenAd.show() // 미디에이션 앱 오프닝 광고로드 후 표시할 때 호출
```

### 앱 오프닝 구현 예제

```dart
void _loadAppOpen() {
    _appOpenAd?.dispose();
    _appOpenAd = null;
    _isAppOpenLoaded = false;

    _appOpenAd =
        AdWhaleAppOpenAd(
            placementUid: '발급받은 placementUid',
            adLoadCallback: AdWhaleAppOpenAdLoadCallback(
              onAppOpenAdLoaded: () {
                debugPrint('GuideSamplePage AppOpen onAdLoaded');
                _isAppOpenLoaded = true;
                if (mounted) {
                  ScaffoldMessenger.of(context).showSnackBar(
                    const SnackBar(content: Text('앱오프닝 광고 로드 완료')),
                  );
                }
              },
              onAppOpenAdLoadFailed: (code, message) {
                debugPrint(
                  'GuideSamplePage AppOpen onAdLoadFailed: $code, $message',
                );
                _appOpenAd = null;
                _isAppOpenLoaded = false;
              },
              onAppOpenAdShowed: () {
                debugPrint('GuideSamplePage AppOpen onAdShowed');
              },
              onAppOpenAdShowFailed: (code, message) {
                debugPrint(
                  'GuideSamplePage AppOpen onAdShowFailed: $code, $message',
                );
                _appOpenAd = null;
                _isAppOpenLoaded = false;
              },
              onAppOpenAdDismissed: () {
                debugPrint('GuideSamplePage AppOpen onAdDismissed');
              },
              onAppOpenAdClicked: () {
                debugPrint('GuideSamplePage AppOpen onAdClicked');
              },
            ),
          )
    _appOpenAd!.load();
  }
  void _showAppOpen() {
    if (_appOpenAd == null || !_isAppOpenLoaded) {
      if (mounted) {
        ScaffoldMessenger.of(
          context,
        ).showSnackBar(const SnackBar(content: Text('앱오프닝 광고를 먼저 로드해 주세요.')));
      }
      return;
    }
    _appOpenAd!.show();
    _isAppOpenLoaded = false;
    _appOpenAd = null;
  }
```
