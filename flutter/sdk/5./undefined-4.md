# 네이티브

{% hint style="info" %}
네이티브 광고는 앱의 콘텐츠와 자연스럽게 어우러지는 광고 형식입니다. 앱의 디자인과 일치하도록 커스터마이징할 수 있어 사용자 경험을 해치지 않으면서 높은 참여도를 얻을 수 있습니다.
{% endhint %}

#### 1. 주요특징 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

* 앱 콘텐츠와 자연스럽게 통합
* 템플릿 기반 또는 커스텀 레이아웃 지원
* 다양한 템플릿 타입 (SMALL, MEDIUM, FULLSCREEN)
* 스타일 커스터마이징 가능

#### 2. 네이티브 광고 타입 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

네이티브 광고는 두 가지 방식으로 구현할 수 있습니다:

{% hint style="info" %}
**템플릿 네이티브 광고** (`AdWhaleNativeTemplateView`)

* SDK에서 제공하는 템플릿 사용
* SMALL, MEDIUM, FULLSCREEN 타입 지원
* 스타일 커스터마이징 가능
{% endhint %}

{% hint style="info" %}
**커스텀 네이티브 광고** (`AdWhaleNativeCustomView`)

* 자체 레이아웃 사용
* 완전한 디자인 제어
{% endhint %}

#### 3. 템플릿 네이티브 광고 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

템플릿 네이티브 광고는 다음 세 가지 타입을 지원합니다:

<table><thead><tr><th width="139.9375">타입</th><th>값</th><th>설명</th></tr></thead><tbody><tr><td>SMALL</td><td><code>AdWhaleNativeTemplate.small</code></td><td>작은 크기의 네이티브 광고</td></tr><tr><td>MEDIUM</td><td><code>AdWhaleNativeTemplate.medium</code></td><td>중간 크기의 네이티브 광고</td></tr><tr><td>FULLSCREEN</td><td><code>AdWhaleNativeTemplate.fullscreen</code></td><td>전체 화면 네이티브 광고</td></tr></tbody></table>

**기본 구현 샘플코드**

```dart
import 'package:flutter/material.dart';
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

class NativeAdExample extends StatefulWidget {
  const NativeAdExample({super.key});

  @override
  State<NativeAdExample> createState() => _NativeAdExampleState();
}

class _NativeAdExampleState extends State<NativeAdExample> {
  AdWhaleNativeTemplateView? _nativeAd;

  @override
  void initState() {
    super.initState();
    _loadNativeAd();
  }

  void _loadNativeAd() {
    _nativeAd = AdWhaleNativeTemplateView(
      placementUid: 'your-placement-uid',
      template: AdWhaleNativeTemplate.small,
      nativeAdLoadCallback: AdWhaleNativeAdLoadCallback(
        onLoaded: (ad) {
          print('네이티브 광고 로드 성공');
          setState(() {});
        },
        onLoadFailed: (ad, errorCode, errorMessage) {
          print('네이티브 광고 로드 실패: $errorCode, $errorMessage');
          ad.destroy();
          setState(() {
            _nativeAd = null;
          });
        },
      ),
    )..loadAd();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Column(
        children: [
          Expanded(
            child: Center(
              child: Text('앱 콘텐츠'),
            ),
          ),
          // 네이티브 광고 영역
          if (_nativeAd != null)
            SizedBox(
              height: 120,
              child: AdWhaleAdWidget(ad: _nativeAd!),
            ),
        ],
      ),
    );
  }

  @override
  void dispose() {
    _nativeAd?.destroy();
    super.dispose();
  }
}
```

**템플릿 스타일 커스터마이징 샘플코드**

템플릿 네이티브 광고의 스타일을 커스터마이징할 수 있습니다.

```dart
final customStyle = AdWhaleNativeTemplateStyle(
  mainBackgroundColorHex: '#F5F5F5',
  primaryTextColorHex: '#333333',
  primaryTextSize: 18.0,
  primaryTypeface: 'bold',
  primaryTextBackgroundColorHex: '#FFFFFF',
  secondaryTextColorHex: '#666666',
  secondaryTextSize: 14.0,
  secondaryTextTypeface: 'normal',
  tertiaryTextColorHex: '#999999',
  tertiaryTextSize: 12.0,
  callToActionTextColorHex: '#FFFFFF',
  callToActionTextSize: 16.0,
  callToActionTextTypeface: 'bold',
  callToActionBackgroundColorHex: '#009688',
);

AdWhaleNativeTemplateView(
  placementUid: 'your-placement-uid',
  template: AdWhaleNativeTemplate.medium,
  templateStyle: customStyle,
  nativeAdLoadCallback: AdWhaleNativeAdLoadCallback(
    onLoaded: (ad) => print('로드 성공'),
    onLoadFailed: (ad, code, msg) => print('로드 실패'),
  ),
)..loadAd();
```

**스타일 옵션 샘플코드**

```dart
class AdWhaleNativeTemplateStyle {
  const AdWhaleNativeTemplateStyle({
    this.mainBackgroundColorHex,              // 메인 배경색 (예: '#F5F5F5')
    this.primaryTextColorHex,                 // 헤드라인 텍스트 색상 (예: '#333333')
    this.primaryTextSize,                     // 헤드라인 텍스트 크기 (sp 단위)
    this.primaryTypeface,                     // 헤드라인 텍스트 폰트 ('bold' | 'normal')
    this.primaryTextBackgroundColorHex,       // 헤드라인 텍스트 배경색
    this.secondaryTextColorHex,               // 세컨더리 텍스트 색상
    this.secondaryTextSize,                   // 세컨더리 텍스트 크기 (sp 단위)
    this.secondaryTextTypeface,               // 세컨더리 텍스트 폰트 ('bold' | 'normal')
    this.secondaryTextBackgroundColorHex,     // 세컨더리 텍스트 배경색
    this.tertiaryTextColorHex,                // 바디 텍스트 색상
    this.tertiaryTextSize,                    // 바디 텍스트 크기 (sp 단위)
    this.tertiaryTextTypeface,                // 바디 텍스트 폰트 ('bold' | 'normal')
    this.tertiaryTextBackgroundColorHex,       // 바디 텍스트 배경색
    this.callToActionTextColorHex,            // CTA 버튼 텍스트 색상
    this.callToActionTextSize,                // CTA 버튼 텍스트 크기 (sp 단위)
    this.callToActionTextTypeface,             // CTA 버튼 텍스트 폰트 ('bold' | 'normal')
    this.callToActionBackgroundColorHex,       // CTA 버튼 배경색
  });
}
```

#### 4. 커스텀 네이티브 광고 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

커스텀 네이티브 광고는 사용자가 직접 정의한 커스텀 레이아웃을 사용하여 광고를 표시합니다.

`factoryId`를 사용하여 MainActivity에서 등록한 BinderFactory를 통해 커스텀 레이아웃을 연결합니다.

**Android MainActivity 에서 BinderFactory 등록**

`android/app/src/main/java/.../MainActivity.kt` (또는 `.java`) 파일에서 BinderFactory를 등록합니다:

{% tabs %}
{% tab title="Java" %}
```java
package com.example.app;

import android.os.Bundle;
import io.flutter.embedding.android.FlutterActivity;
import net.adwhale.sdk.mediation.binder.SimpleBinderFactory;
import net.adwhale.sdk.mediation.binder.AdwhaleMediationBinderFactoryRegistry;

public class MainActivity extends FlutterFragmentActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Register BinderFactory for custom binding (factoryId: app_custom)
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
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
package com.example.app

import android.os.Bundle
import io.flutter.embedding.android.FlutterActivity
import net.adwhale.sdk.mediation.binder.SimpleBinderFactory
import net.adwhale.sdk.mediation.binder.AdwhaleMediationBinderFactoryRegistry

class MainActivity: FlutterFragmentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Register BinderFactory for custom binding (factoryId: app_custom)
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
        )
    }
}
```
{% endtab %}
{% endtabs %}

**커스텀 레이아웃 파일 생성**

`android/app/src/main/res/layout/custom_native_ad_main_layout.xml` 파일을 생성합니다:

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
        android:id="@+id/main_view_icon"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:layout_marginEnd="12dp"
        android:scaleType="centerCrop"
        app:layout_constraintBottom_toBottomOf="@id/main_view_title"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="@id/main_view_title" />

    <!-- 제목 -->
    <TextView
        android:id="@+id/main_view_title"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:textSize="16sp"
        android:textStyle="bold"
        android:textColor="@android:color/black"
        android:ellipsize="end"
        android:maxLines="2"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@id/main_view_icon"
        app:layout_constraintTop_toTopOf="parent"
        tools:text="광고 제목입니다" />

    <!-- 설명 -->
    <TextView
        android:id="@+id/main_view_body"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="4dp"
        android:textSize="14sp"
        android:textColor="@android:color/darker_gray"
        android:ellipsize="end"
        android:lines="2"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="@id/main_view_title"
        app:layout_constraintTop_toBottomOf="@id/main_view_title"
        tools:text="광고 설명입니다. 이 앱을 다운로드하세요!" />

    <!-- CTA 버튼 -->
    <Button
        android:id="@+id/main_button_cta"
        android:layout_width="wrap_content"
        android:layout_height="36dp"
        android:layout_marginTop="8dp"
        android:textSize="12sp"
        android:backgroundTint="#009688"
        android:textColor="@android:color/white"
        android:text="설치하기"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toBottomOf="@id/main_view_body" />

    <!-- 미디어 뷰 -->
    <FrameLayout
        android:id="@+id/main_view_media"
        android:layout_width="match_parent"
        android:layout_height="300dp"
        android:layout_marginTop="12dp"
        app:layout_constraintTop_toBottomOf="@id/main_button_cta" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

**Flutter에서 factoryId 사용**

```dart
import 'package:flutter/material.dart';
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

class CustomNativeAdExample extends StatefulWidget {
  const CustomNativeAdExample({super.key});

  @override
  State<CustomNativeAdExample> createState() => _CustomNativeAdExampleState();
}

class _CustomNativeAdExampleState extends State<CustomNativeAdExample> {
  AdWhaleNativeCustomView? _nativeAd;

  @override
  void initState() {
    super.initState();
    _loadNativeAd();
  }

  void _loadNativeAd() {
    _nativeAd = AdWhaleNativeCustomView(
      placementUid: 'your-placement-uid',
      factoryId: 'app_custom', // MainActivity에서 등록한 factoryId
      nativeAdLoadCallback: AdWhaleNativeAdLoadCallback(
        onLoaded: (ad) {
          print('커스텀 네이티브 광고 로드 성공');
          setState(() {});
        },
        onLoadFailed: (ad, errorCode, errorMessage) {
          print('커스텀 네이티브 광고 로드 실패: $errorCode, $errorMessage');
          ad.destroy();
          setState(() {
            _nativeAd = null;
          });
        },
      ),
    )..loadAd();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Column(
        children: [
          Expanded(
            child: Center(
              child: Text('앱 콘텐츠'),
            ),
          ),
          // 커스텀 네이티브 광고 영역
          if (_nativeAd != null)
            SizedBox(
              height: 350,
              child: AdWhaleAdWidget(ad: _nativeAd!),
            ),
        ],
      ),
    );
  }

  @override
  void dispose() {
    _nativeAd?.destroy();
    super.dispose();
  }
}
```

#### 5. 이벤트 리스너 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleNativeTemplateView` 컴포넌트는 다음 이벤트를 지원합니다:

| 이벤트            | 설명            | 파라미터                                                                                               |
| -------------- | ------------- | -------------------------------------------------------------------------------------------------- |
| `onLoaded`     | 광고 로드 성공 시 호출 | `Ad ad`                                                                                            |
| `onLoadFailed` | 광고 로드 실패 시 호출 | <p><code>Ad ad</code>, <br><code>String errorCode,</code> <br><code>String errorMessage</code></p> |
| `onShowFailed` | 광고 표시 실패 시 호출 | <p><code>Ad ad</code>, <br><code>String errorCode,</code> <br><code>String errorMessage</code></p> |
| `onClicked`    | 광고 클릭 시 호출    | `Ad ad`                                                                                            |
| `onClosed`     | 광고 닫힘 시 호출    | `Ad ad`                                                                                            |

`AdWhaleNativeCustomView` 컴포넌트는 다음 이벤트를 지원합니다:

| 이벤트            | 설명            | 파라미터                                                                                               |
| -------------- | ------------- | -------------------------------------------------------------------------------------------------- |
| `onLoaded`     | 광고 로드 성공 시 호출 | `Ad ad`                                                                                            |
| `onLoadFailed` | 광고 로드 실패 시 호출 | <p><code>Ad ad</code>, <br><code>String errorCode,</code> <br><code>String errorMessage</code></p> |

#### 6. 옵션 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleNativeTemplateView` 컴포넌트 호출 시 추가 옵션을 설정할 수 있습니다.

```dart
AdWhaleNativeTemplateView(
  placementUid: 'placement-uid',        // 필수: Placement UID
  template: AdWhaleNativeTemplate.small, // 필수: 템플릿 타입
  templateStyle: customStyle,            // 템플릿 스타일
  nativeAdLoadCallback: AdWhaleNativeAdLoadCallback(...),
)
  ..setRegion('서울시 강남구')            // 지역 정보
  ..setGcoder(37.5, 126.9)              // 위치 정보 (위도, 경도)
  ..setPlacementName('native-main')      // Placement 이름
  ..loadAd();
```

`AdWhaleNativeCustomView` 컴포넌트 호출 시 추가 옵션을 설정할 수 있습니다.

```dart
AdWhaleNativeCustomView(
  placementUid: 'placement-uid',        // 필수: Placement UID
  factoryId: 'app_custom',               // 필수: BinderFactory ID
  nativeAdLoadCallback: AdWhaleNativeAdLoadCallback(...),
)
  ..setRegion('서울시 강남구')            // 지역 정보
  ..setGcoder(37.5, 126.9)              // 위치 정보 (위도, 경도)
  ..setPlacementName('native-custom')   // Placement 이름
  ..loadAd();
```

{% hint style="info" %}
**중요**: `factoryId`는 필수이며, MainActivity에서 해당 `factoryId`로 BinderFactory를 등록해야 합니다.
{% endhint %}

#### 7. 템플릿 네이티브 광고 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

다음은 React Native 컴포넌트에서 템플릿 네이티브 광고를 구현하는 완전한 예시입니다.

```dart
import 'package:flutter/material.dart';
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

class TemplateNativeAdScreen extends StatefulWidget {
  const TemplateNativeAdScreen({super.key});

  @override
  State<TemplateNativeAdScreen> createState() => _TemplateNativeAdScreenState();
}

class _TemplateNativeAdScreenState extends State<TemplateNativeAdScreen> {
  AdWhaleNativeTemplateView? _nativeAd;
  bool _isLoaded = false;
  AdWhaleNativeTemplate _templateType = AdWhaleNativeTemplate.small;

  @override
  void initState() {
    super.initState();
    _initializeAndLoadNativeAd();
  }

  Future<void> _initializeAndLoadNativeAd() async {
    // SDK 초기화
    final result = await AdWhaleMediationAds.instance.initialize();
    if (result.isSuccess) {
      print('SDK 초기화 성공: ${result.message}');
      _loadNativeAd();
    } else {
      print('SDK 초기화 실패: ${result.statusCode}, ${result.message}');
    }
  }

  void _loadNativeAd() {
    _nativeAd?.destroy();
    _nativeAd = null;
    _isLoaded = false;

    final customStyle = AdWhaleNativeTemplateStyle(
      mainBackgroundColorHex: '#F5F5F5',
      primaryTextColorHex: '#333333',
      primaryTextSize: 18.0,
      primaryTypeface: 'bold',
      callToActionTextColorHex: '#FFFFFF',
    );

    _nativeAd = AdWhaleNativeTemplateView(
      placementUid: 'your-placement-uid',
      template: _templateType,
      templateStyle: customStyle,
      nativeAdLoadCallback: AdWhaleNativeAdLoadCallback(
        onLoaded: (ad) {
          print('네이티브 광고 로드 성공');
          setState(() {
            _isLoaded = true;
          });
        },
        onLoadFailed: (ad, errorCode, errorMessage) {
          print('네이티브 광고 로드 실패: $errorCode, $errorMessage');
          ad.destroy();
          setState(() {
            _nativeAd = null;
            _isLoaded = false;
          });
        },
        onShowFailed: (ad, errorCode, errorMessage) {
          print('네이티브 광고 표시 실패: $errorCode, $errorMessage');
        },
        onClicked: (ad) {
          print('네이티브 광고 클릭됨');
        },
        onClosed: (ad) {
          print('네이티브 광고 닫힘');
        },
      ),
    )
      ..setRegion('서울시 강남구')
      ..setGcoder(37.5, 126.9)
      ..setPlacementName('native-main')
      ..load();
  }

  double _getHeight(AdWhaleNativeTemplate type) {
    switch (type) {
      case AdWhaleNativeTemplate.small:
        return 120;
      case AdWhaleNativeTemplate.medium:
        return 400;
      case AdWhaleNativeTemplate.fullscreen:
        return 600;
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('템플릿 네이티브 광고')),
      body: Column(
        children: [
          Expanded(
            child: Padding(
              padding: const EdgeInsets.all(16),
              child: Column(
                children: [
                  ElevatedButton(
                    onPressed: () {
                      setState(() => _templateType = AdWhaleNativeTemplate.small);
                      _loadNativeAd();
                    },
                    child: const Text('SMALL'),
                  ),
                  ElevatedButton(
                    onPressed: () {
                      setState(() => _templateType = AdWhaleNativeTemplate.medium);
                      _loadNativeAd();
                    },
                    child: const Text('MEDIUM'),
                  ),
                  ElevatedButton(
                    onPressed: () {
                      setState(() => _templateType = AdWhaleNativeTemplate.fullscreen);
                      _loadNativeAd();
                    },
                    child: const Text('FULLSCREEN'),
                  ),
                  ElevatedButton(
                    onPressed: _loadNativeAd,
                    child: const Text('광고 다시 로드'),
                  ),
                ],
              ),
            ),
          ),
          if (_nativeAd != null && _isLoaded)
            SizedBox(
              height: _getHeight(_templateType),
              child: AdWhaleAdWidget(ad: _nativeAd!),
            ),
        ],
      ),
    );
  }

  @override
  void dispose() {
    _nativeAd?.destroy();
    super.dispose();
  }
}
```

**FullScreen 템플릿 모달 샘플코드**

```dart
import 'package:flutter/material.dart';
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

class FullscreenNativeAdModal extends StatefulWidget {
  const FullscreenNativeAdModal({super.key});

  @override
  State<FullscreenNativeAdModal> createState() => _FullscreenNativeAdModalState();
}

class _FullscreenNativeAdModalState extends State<FullscreenNativeAdModal> {
  AdWhaleNativeTemplateView? _nativeAd;
  bool _isVisible = false;

  void _openModal() {
    setState(() {
      _isVisible = true;
    });
    _loadNativeAd();
  }

  void _closeModal() {
    setState(() {
      _isVisible = false;
      _nativeAd?.destroy();
      _nativeAd = null;
    });
  }

  void _loadNativeAd() {
    _nativeAd = AdWhaleNativeTemplateView(
      placementUid: 'your-placement-uid',
      template: AdWhaleNativeTemplate.fullscreen,
      nativeAdLoadCallback: AdWhaleNativeAdLoadCallback(
        onLoaded: (ad) {
          // 로드 완료 후 자동 표시
          Future.delayed(const Duration(milliseconds: 100), () {
            ad.showAd();
          });
        },
        onLoadFailed: (ad, code, msg) {
          print('로드 실패: $code, $msg');
          ad.destroy();
          _nativeAd = null;
        },
      ),
    )..loadAd();
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        ElevatedButton(
          onPressed: _openModal,
          child: const Text('FULLSCREEN 광고 보기'),
        ),
        if (_isVisible && _nativeAd != null)
          Expanded(
            child: AdWhaleAdWidget(ad: _nativeAd!),
          ),
      ],
    );
  }

  @override
  void dispose() {
    _nativeAd?.destroy();
    super.dispose();
  }
}
```

#### 8. 주의사항 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

#### 광고 로드 타이밍 <a href="#id-2____542" id="id-2____542"></a>

* `loadAd()`를 호출한 후 `onLoaded` 이벤트가 발생해야 `showAd()`를 호출할 수 있습니다.
* 광고가 로드되기 전에 `showAd()`를 호출하면 표시되지 않습니다.

#### 위젯 재생성 <a href="#id-3___547" id="id-3___547"></a>

* placement UID나 템플릿 타입을 변경할 때는 `AdWhaleNativeTemplateView` (커스텀 네이티브 일 땐 `AdWhaleNativeCustomView`)를 `destroy`하고 새로 생성해야 합니다.
* `setState()` 를 호출하여 UI를 갱신하세요.

#### FULLSCREEN 템플릿 <a href="#id-4_fullscreen__552" id="id-4_fullscreen__552"></a>

* FULLSCREEN 템플릿은 모달이나 별도 화면에서 사용하는 것을 권장합니다.
* 일반 뷰에 표시하면 레이아웃 문제가 발생할 수 있습니다.

#### 스타일 커스터마이징 <a href="#id-5___557" id="id-5___557"></a>

* 템플릿 스타일은 광고 로드 전에 설정해야 합니다.
* 스타일을 변경하려면 컴포넌트를 재생성하세요.

#### 커스텀 네이티브 광고

* `factoryId`는 MainActivity에서 등록한 값과 정확히 일치해야 합니다.
* 레이아웃 파일의 View ID가 올바르게 설정되어 있는지 확인하세요.

#### 에러 처리 <a href="#id-6___562" id="id-6___562"></a>

* `onLoadFailed`와 `onShowFailed` 이벤트에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.

#### 테스트 <a href="#id-7__567" id="id-7__567"></a>

* 개발 환경에서는 테스트용 placement UID를 사용하세요.
* 다양한 템플릿 타입과 스타일을 테스트하세요.
