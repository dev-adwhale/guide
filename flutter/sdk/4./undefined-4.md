# 네이티브

{% hint style="info" %}
1. 템플릿 네이티브
2. 템플릿 네이티브 스타일 적용
3. 커스텀 바인딩 네이티브
{% endhint %}

#### 1. 템플릿 네이티브 생성

{% tabs %}
{% tab title="Dart" %}
```dart
AdWhaleTemplateNativeAd({
    required super.nativeAdLoadCallback,
    required super.placementUid,
    this.template = NativeTemplate.small
 });
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>발급받은 placementUid</td></tr><tr><td>AdWhaleNativeAdLoadCallback</td><td>네이티브 미디에이션 광고 호출 콜백 리스너</td></tr><tr><td>NativeTemplate</td><td>small, medium, fullscreen 사이즈 지원</td></tr></tbody></table>

AdWhaleNativeAdLoadCallback

| 리스너 구성                 | 설명          |
| ---------------------- | ----------- |
| onNativeAdLoaded       | 광고 로드시      |
| onNativeAdFailedToLoad | 광고 로드 실패    |
| onNativeAdShowFailed   | 광고 화면 랜딩 실패 |
| onNativeAdClicked      | 광고 화면 클릭    |
| onNativeAdClosed       | 광고 화면 종료    |

```
onNativeAdFailedToLoad: (errorCode, errorMessage) // 미디에이션 템플릿 네이티브 광고요청 실패 시
onNativeAdShowFailed: (errorCode, errorMessage) // 미디에이션 템플릿 네이티브 광고 화면 랜딩 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>errorCode</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>errorMessage</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>
{% endtab %}
{% endtabs %}

```dart
AdWhaleTemplateNativeAd.load() // 미디에이션 템플릿 네이티브 광고로드
```

```dart
AdWhaleTemplateNativeAd.show() // 미디에이션 템플릿 네이티브 광고로드 후 표시할 때 호출
```

#### 템플릿 네이티브 구현 예제

```dart
void _loadTemplateNative() {
    _adWhaleTemplateNativeAd?.dispose();
    _adWhaleTemplateNativeAd = null;
    _isTemplateNativeLoaded = false;

    // 공통 네이티브 상태 초기화
    _adWhaleNativeAd?.dispose();
    _adWhaleNativeAd = null;
    _nativeLoaded = false;
    _nativeShown = false;
    _nativeHeight = null;
    _nativeHeightSub?.cancel();
    _nativeHeightSub = null;

    setState(() {
      _nativeShown = false;
      _nativeHeight = null;
      _nativeLoaded = false;
    });

    _adWhaleTemplateNativeAd =
        AdWhaleTemplateNativeAd(
            placementUid: '발급받은 PlacementUid',
            nativeAdLoadCallback: AdWhaleNativeAdLoadCallback(
              onNativeAdLoaded: (ad) {
                debugPrint('GuideSamplePage TemplateNative onLoaded');
                setState(() {
                  _isTemplateNativeLoaded = true;
                  _nativeLoaded = true;
                });
                if (mounted) {
                  ScaffoldMessenger.of(context).showSnackBar(
                    const SnackBar(content: Text('네이티브 템플릿 광고 로드 완료')),
                  );
                }
              },
              onNativeAdFailedToLoad: (ad, code, message) {
                debugPrint(
                  'GuideSamplePage TemplateNative onFailedToLoad: $code, $message',
                );
                ad.dispose();
                setState(() {
                  _adWhaleTemplateNativeAd = null;
                  _isTemplateNativeLoaded = false;
                  _nativeLoaded = false;
                  _nativeShown = false;
                  _nativeHeight = null;
                });
              },
              onNativeAdShowFailed: (ad, code, message) {
                debugPrint(
                  'GuideSamplePage TemplateNative onShowFailed: $code, $message',
                );
                ad.dispose();
                setState(() {
                  _adWhaleTemplateNativeAd = null;
                  _isTemplateNativeLoaded = false;
                  _nativeLoaded = false;
                  _nativeShown = false;
                  _nativeHeight = null;
                });
              },
              onNativeAdClicked: (ad) {
                debugPrint('GuideSamplePage TemplateNative onClicked');
              },
              onNativeAdClosed: (ad) {
                debugPrint('GuideSamplePage TemplateNative onClosed');
              },
            ),
            template: _selectedTemplateSize,
          )

    _adWhaleNativeAd = _adWhaleTemplateNativeAd;
    _nativeLoaded = false;

    _nativeHeightSub = instanceManager.nativeAdHeightStream.listen((int h) {
      if (!mounted) return;
      setState(() {
        _nativeHeight = h.toDouble();
      });
    });

    _adWhaleTemplateNativeAd!.load();
  }
  void _showTemplateNative() {
    if (_adWhaleTemplateNativeAd == null || !_isTemplateNativeLoaded) {
      if (mounted) {
        ScaffoldMessenger.of(context).showSnackBar(
          const SnackBar(content: Text('네이티브 템플릿 광고를 먼저 로드해 주세요.')),
        );
      }
      return;
    }
    _adWhaleTemplateNativeAd!.show();
    setState(() {
      _isTemplateNativeLoaded = false;
      _nativeLoaded = false;
      _nativeShown = true; // 여기서 setState가 핵심 (native_test_page와 동일)
    });
  }
```

#### 2. 템플릿 네이티브 스타일 적용

```dart
AdWhaleTemplateNativeAd({
    required super.nativeAdLoadCallback,
    required super.placementUid,
    this.templateStyle,
    this.template = NativeTemplate.small
 });
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>발급받은 placementUid</td></tr><tr><td>AdWhaleNativeAdLoadCallback</td><td>네이티브 미디에이션 광고 호출 콜백 리스너</td></tr><tr><td>TemplateStyle</td><td>small, medium, fullscreen 사이</td></tr><tr><td>NativeTemplate</td><td>small, medium, fullscreen 사이즈 지원</td></tr></tbody></table>

AdWhaleNativeAdLoadCallback

| 리스너 구성                 | 설명          |
| ---------------------- | ----------- |
| onNativeAdLoaded       | 광고 로드시      |
| onNativeAdFailedToLoad | 광고 로드 실패    |
| onNativeAdShowFailed   | 광고 화면 랜딩 실패 |
| onNativeAdClicked      | 광고 화면 클릭    |
| onNativeAdClosed       | 광고 화면 종료    |

TemplateStyle

<table data-header-hidden><thead><tr><th width="348">TemplateStyle</th><th>사용 가능한 옵션</th></tr></thead><tbody><tr><td>TemplateStyle</td><td>mainBackgroundColorHex</td></tr><tr><td></td><td>primaryTextColorHex</td></tr><tr><td></td><td>primaryTypeface</td></tr><tr><td></td><td>primaryTextBackgroundColorHex</td></tr><tr><td></td><td>primaryTextSize</td></tr><tr><td></td><td>secondaryTextColorHex</td></tr><tr><td></td><td>tertiaryTextColorHex</td></tr><tr><td></td><td>callToActionTextColorHex</td></tr></tbody></table>

#### 템플릿 네이티브 스타일 적용 구현 예제

```dart
void _loadTemplateStyleSmall() {
    _disposeNative();
    _nativeAd = AdWhaleTemplateNativeAd(
      nativeAdLoadCallback: AdWhaleNativeAdLoadCallback(
        onNativeAdLoaded: (ad) => setState(() { _loaded = true; }),
        onNativeAdFailedToLoad: (ad, c, m) { ad.dispose(); setState(() => _nativeAd = null); },
        onNativeAdShowFailed: (ad, c, m) { ad.dispose(); setState(() => _nativeAd = null); },
      ),
      placementUid: "발급 받은 placementUid",
      template: NativeTemplate.small,
      templateStyle: const TemplateStyle(
        mainBackgroundColorHex: "#2C2C2C",
        primaryTextColorHex: "#FFEB3B",
        primaryTypeface: 'bold',
        primaryTextBackgroundColorHex: "#4CAF50",
        primaryTextSize: 16.0,
        secondaryTextColorHex: "#BDBDBD",
        tertiaryTextColorHex: "#9E9E9E",
        callToActionTextColorHex: "#FFFFFF",
      ),
    )..load();
  }
  void _showTemplateNative() {
    _adWhaleTemplateNativeAd!.show();
    setState(() {
      _isTemplateNativeLoaded = false;
      _nativeLoaded = false;
      _nativeShown = true;
    });
  }
```

#### 3. 커스텀 바인딩 네이티브 생성

{% tabs %}
{% tab title="Dart" %}
```dart
AdWhaleCustomNativeAd({
    required super.nativeAdLoadCallback,
    required super.placementUid,
    this.factoryId
});
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>AdWhaleNativeAdLoadCallback</td><td>네이티브 미디에이션 광고 호출 콜백 리스너</td></tr><tr><td>String</td><td>발급받은 placementUid</td></tr><tr><td>String</td><td>factoryId 구조 연결을 위한 Id값</td></tr></tbody></table>

AdWhaleNativeAdLoadCallback

| 리스너 구성                 | 설명          |
| ---------------------- | ----------- |
| onNativeAdLoaded       | 광고 로드시      |
| onNativeAdFailedToLoad | 광고 로드 실패    |
| onNativeAdShowFailed   | 광고 화면 랜딩 실패 |
| onNativeAdClicked      | 광고 화면 클릭    |
| onNativeAdClosed       | 광고 화면 종료    |

```
onNativeAdFailedToLoad: (errorCode, errorMessage) // 미디에이션 커스텀 네이티브 광고요청 실패 시
onNativeAdShowFailed: (errorCode, errorMessage) // 미디에이션 커스 네이티브 광고 화면 랜딩 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>errorCode</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>errorMessage</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>
{% endtab %}
{% endtabs %}

```dart
AdWhaleCustomNativeAd.load() // 미디에이션 커스텀 바인딩 네이티브 광고로드
```

```dart
AdWhaleCustomNativeAd.show() // 미디에이션 커스텀 바인딩 네이티브 광고로드 후 표시할 때 호출
```

#### 커스텀 바인딩 네이티브 구현 예제

```dart
void _loadCustomNative() {
    _adWhaleCustomNativeAd?.dispose();
    _adWhaleCustomNativeAd = null;
    _isCustomNativeLoaded = false;

    _adWhaleNativeAd?.dispose();
    _adWhaleNativeAd = null;
    _nativeLoaded = false;
    _nativeShown = false;
    _nativeHeight = null;
    _nativeHeightSub?.cancel();
    _nativeHeightSub = null;

    setState(() {
      _nativeShown = false;
      _nativeHeight = null;
      _nativeLoaded = false;
    });

    _adWhaleCustomNativeAd =
        AdWhaleCustomNativeAd(
            placementUid: 'PlacementUid',
            nativeAdLoadCallback: AdWhaleNativeAdLoadCallback(
              onNativeAdLoaded: (ad) {
                debugPrint('GuideSamplePage CustomNative onLoaded');
                setState(() {
                  _isCustomNativeLoaded = true;
                  _nativeLoaded = true;
                });
                if (mounted) {
                  ScaffoldMessenger.of(context).showSnackBar(
                    const SnackBar(content: Text('네이티브 커스텀 광고 로드 완료')),
                  );
                }
              },
              onNativeAdFailedToLoad: (ad, code, message) {
                debugPrint(
                  'GuideSamplePage CustomNative onFailedToLoad: $code, $message',
                );
                ad.dispose();
                setState(() {
                  _adWhaleCustomNativeAd = null;
                  _isCustomNativeLoaded = false;
                  _nativeLoaded = false;
                  _nativeShown = false;
                  _nativeHeight = null;
                });
              },
              onNativeAdShowFailed: (ad, code, message) {
                debugPrint(
                  'GuideSamplePage CustomNative onShowFailed: $code, $message',
                );
                ad.dispose();
                setState(() {
                  _adWhaleCustomNativeAd = null;
                  _isCustomNativeLoaded = false;
                  _nativeLoaded = false;
                  _nativeShown = false;
                  _nativeHeight = null;
                });
              },
              onNativeAdClicked: (ad) {
                debugPrint('GuideSamplePage CustomNative onClicked');
              },
              onNativeAdClosed: (ad) {
                debugPrint('GuideSamplePage CustomNative onClosed');
              },
            ),
            factoryId: 'app_custom',
          )

    _adWhaleNativeAd = _adWhaleCustomNativeAd;
    _nativeLoaded = false;

    _nativeHeightSub = instanceManager.nativeAdHeightStream.listen((int h) {
      if (!mounted) return;
      setState(() {
        _nativeHeight = h.toDouble();
      });
    });

    _adWhaleCustomNativeAd!.load();
  }

  void _showCustomNative() {
    if (_adWhaleCustomNativeAd == null || !_isCustomNativeLoaded) {
      if (mounted) {
        ScaffoldMessenger.of(context).showSnackBar(
          const SnackBar(content: Text('네이티브 커스텀 광고를 먼저 로드해 주세요.')),
        );
      }
      return;
    }
    _adWhaleCustomNativeAd!.show();
    setState(() {
      _isCustomNativeLoaded = false;
      _nativeLoaded = false;
      _nativeShown = true;
    });
  }
```

```java
/**
* factoryId 연결사용자 지정 커스텀 네이티브 레이아웃 예시(custom_native_ad_layout.xml)
*/

public class MainActivity extends FlutterFragmentActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Register BinderFactory for custom binding test (factoryId: app_custom)
        AdWhaleSdkFlutterPlugin.registerBinderFactory(
                "app_custom",
                new SimpleBinderFactory(
                        R.layout.custom_native_ad_main_layout,
                        R.id.main_view_icon,
                        R.id.main_view_title,
                        R.id.main_view_body,
                        R.id.main_button_cta,
                        R.id.main_view_media
                )
        );
    }
}

```

#### 사용자 지정 커스텀 네이티브 레이아웃 예시(custom\_native\_ad\_layout.xml)

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:padding="16dp"
    android:background="@android:color/white"
    android:elevation="4dp">

    <!-- 앱 아이콘 -->
    <ImageView
        android:id="@+id/view_icon"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:layout_marginEnd="12dp"
        android:scaleType="centerCrop"
        app:layout_constraintBottom_toBottomOf="@id/view_title"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="@id/view_title" />

    <!-- 제목 -->
    <TextView
        android:id="@+id/view_title"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:textSize="16sp"
        android:textStyle="bold"
        android:textColor="@android:color/black"
        android:ellipsize="end"
        android:maxLines="2"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@id/view_icon"
        app:layout_constraintTop_toTopOf="parent"
        tools:text="광고 제목입니다" />

    <!-- 설명 -->
    <TextView
        android:id="@+id/view_body"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="4dp"
        android:textSize="14sp"
        android:textColor="@android:color/darker_gray"
        android:ellipsize="end"
        android:lines="2"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="@id/view_title"
        app:layout_constraintTop_toBottomOf="@id/view_title"
        tools:text="광고 설명입니다. 이 앱을 다운로드하세요!" />

    <!-- CTA 버튼 -->
    <Button
        android:id="@+id/button_cta"
        android:layout_width="wrap_content"
        android:layout_height="36dp"
        android:layout_marginTop="8dp"
        android:textSize="12sp"
        android:backgroundTint="#009688"
        android:textColor="@android:color/white"
        android:text="설치하기"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toBottomOf="@id/view_body" />

    <!-- 미디어 뷰 -->
    <FrameLayout
        android:id="@+id/view_media"
        android:layout_width="match_parent"
        android:layout_height="300dp"
        android:layout_marginTop="12dp"
        app:layout_constraintTop_toBottomOf="@id/button_cta" />

</androidx.constraintlayout.widget.ConstraintLayout>

```
