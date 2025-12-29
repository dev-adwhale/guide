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
TemplateNativeAd({
    required super.nativeAdLoadCallback,
    required super.placementUid,
    this.template = NativeTemplate.small
 });
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>발급받은 placement</td></tr><tr><td>NativeAdLoadCallback</td><td>네이티브 미디에이션 광고 호출 콜백 리스너</td></tr><tr><td>NativeTemplate</td><td>small, medium, fullscreen 사이즈 지원</td></tr></tbody></table>

NativeAdLoadCallback

| 리스너 구성                 | 설명          |
| ---------------------- | ----------- |
| onNativeAdLoaded       | 광고 로드시      |
| onNativeAdFailedToLoad | 광고 로드 실패    |
| onNativeAdShowFailed   | 광고 화면 랜딩 실패 |
| onNativeAdClicked      | 광고 화면 클릭    |
| onNativeAdClosed       | 광고 화면 종료    |

```dart
Future<void> load() async{};
```

```dart
Future<void> show() async{};
```
{% endtab %}
{% endtabs %}

#### 템플릿 네이티브 구현 예제

```dart
void _loadTemplateSmall() {
    _disposeNative();
    _nativeAd = TemplateNativeAd(
      nativeAdLoadCallback: NativeAdLoadCallback(
        onNativeAdLoaded: (ad) => setState(() { _loaded = true; }),
        onNativeAdFailedToLoad: (ad, c, m) { ad.dispose(); setState(() => _nativeAd = null); },
        onNativeAdShowFailed: (ad, c, m) { ad.dispose(); setState(() => _nativeAd = null); },
      ),
      placementUid: "발급 받은 placementUid",
      template: NativeTemplate.small,
      
    )..load();
  }
```

#### 2. 템플릿 네이티브 스타일 적용

```dart
TemplateNativeAd({
    required super.nativeAdLoadCallback,
    required super.placementUid,
    this.templateStyle,
    this.template = NativeTemplate.small
 });
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>발급받은 placement</td></tr><tr><td>NativeAdLoadCallback</td><td>네이티브 미디에이션 광고 호출 콜백 리스너</td></tr><tr><td>TemplateStyle</td><td>small, medium, fullscreen 사이</td></tr><tr><td>NativeTemplate</td><td>small, medium, fullscreen 사이즈 지원</td></tr></tbody></table>

<table data-header-hidden><thead><tr><th width="348">TemplateStyle</th><th>사용 가능한 옵션</th></tr></thead><tbody><tr><td>TemplateStyle</td><td>mainBackgroundColorHex</td></tr><tr><td></td><td>primaryTextColorHex</td></tr><tr><td></td><td>primaryTypeface</td></tr><tr><td></td><td>primaryTextBackgroundColorHex</td></tr><tr><td></td><td>primaryTextSize</td></tr><tr><td></td><td>secondaryTextColorHex</td></tr><tr><td></td><td>tertiaryTextColorHex</td></tr><tr><td></td><td>callToActionTextColorHex</td></tr></tbody></table>

#### 템플릿 네이티브 스타일 적용 구현 예제

```dart
void _loadTemplateStyleSmall() {
    _disposeNative();
    _nativeAd = TemplateNativeAd(
      nativeAdLoadCallback: NativeAdLoadCallback(
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
```

#### 3. 커스텀 바인딩 네이티브 생성

{% tabs %}
{% tab title="Dart" %}
```dart
CustomNativeAd({
    required super.nativeAdLoadCallback,
    required super.placementUid,
    this.factoryId
});
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>NativeAdLoadCallback</td><td>네이티브 미디에이션 광고 호출 콜백 리스너</td></tr><tr><td>String</td><td>발급받은 placement</td></tr><tr><td>String</td><td>factoryId 구조 연결을 위한 Id값</td></tr></tbody></table>

NativeAdLoadCallback

| 리스너 구성                 | 설명          |
| ---------------------- | ----------- |
| onNativeAdLoaded       | 광고 로드시      |
| onNativeAdFailedToLoad | 광고 로드 실패    |
| onNativeAdShowFailed   | 광고 화면 랜딩 실패 |
| onNativeAdClicked      | 광고 화면 클릭    |
| onNativeAdClosed       | 광고 화면 종료    |

```dart
Future<void> load() async{};
```

```dart
Future<void> show() async{};
```
{% endtab %}
{% endtabs %}

#### 커스텀 바인딩 네이티브 구현 예제

```dart
void _loadCustomFactory() {
    _disposeNative();
    _nativeAd = CustomNativeAd(
      nativeAdLoadCallback: NativeAdLoadCallback(
        onNativeAdLoaded: (ad) => setState(() { _loaded = true; }),
        onNativeAdFailedToLoad: (ad, c, m) { ad.dispose(); setState(() => _nativeAd = null); },
        onNativeAdShowFailed: (ad, c, m) { ad.dispose(); setState(() => _nativeAd = null); },
      ),
      placementUid: "발급 받은 placementUid",
      factoryId: 'app_custom',
    )..load();
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
