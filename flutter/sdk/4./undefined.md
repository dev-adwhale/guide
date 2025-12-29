# 앱 오프닝 광고

{% tabs %}
{% tab title="Dart" %}
```dart
AdWhaleAppOpenAd({
    required this.placementUid,
    required this.adLoadCallback
  });
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String placementUid</td><td>발급받은 placement</td></tr><tr><td>AppOpenAdLoadCallback adLoadCallback</td><td>앱 오프닝 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

AppOpenAdLoadCallback

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

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

### 앱 오프닝 구현 예제

```dart
void _createAppOpenAd() {
    print('FlutterAppOpenAd _createAppOpenAd()');
    _adwhaleAppOpenAd = AdWhaleAppOpenAd(
      placementUid: "발급 받은 placementUid", // TODO: 실제 발급 UID로 교체
      adLoadCallback: AppOpenAdLoadCallback(
        onAppOpenAdLoaded: () {
          print('FlutterAppOpenAd onAdLoaded');
          _adwhaleAppOpenAd?.show();
        },
        onAppOpenAdLoadFailed: (code, message) {
          print('FlutterAppOpenAd onAdLoadFailed: $code, $message');
          _adwhaleAppOpenAd = null;
        },
        onAppOpenAdShowed: () {
          print('FlutterAppOpenAd onAdShowed');
        },
        onAppOpenAdShowFailed: (code, message) {
          print('FlutterAppOpenAd onAdShowFailed: $code, $message');
          _adwhaleAppOpenAd = null;
        },
        onAppOpenAdDismissed: () {
          print('FlutterAppOpenAd onAdDismissed');
        },
        onAppOpenAdClicked: () {
          print('FlutterAppOpenAd onAdClicked');
        },
      ),
    );
    _adwhaleAppOpenAd!.load();
  }
```
