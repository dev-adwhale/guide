# 앱 내 화면 전환 팝업

{% hint style="danger" %}
앱 내 화면 전환 팝업 광고는 현재 **Android**에서만 지원됩니다.
{% endhint %}

{% hint style="info" %}
전환 팝업(Transition Popup Ad)은 화면·앱 상태가 전환될 때 노출하는 팝업형 광고입니다. 다른 화면으로 이동하기 직전, 사용자 액션 직후 등 자연스러운 타이밍에 노출하여 참여도를 높일 수 있습니다.
{% endhint %}

#### **1. 주요특징**

* 화면·앱 전환 시점에 노출하는 팝업형 광고
* `loadAd()` → `showAd()` 분리 호출
* 이벤트 기반 콜백 시스템으로 광고 상태 추적 가능
* 지역·위치·Placement 이름 옵션 설정 지원
* Activity `onResume`에 대응하는 `resume()`은 **Flutter 플러그인이 자동 호출**

#### 2. 기본 구현 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

전환 팝업 광고를 로드하고 표시하는 기본적인 구현 방법입니다.

```dart
import 'dart:io' show Platform;
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

const String PLACEMENT_UID = 'your-transition-popup-placement-uid';

AdWhaleTransitionPopupAd? _transitionPopupAd;
bool _isTransitionPopupLoaded = false;

void _loadTransitionPopup() {
  if (!Platform.isAndroid) return;

  _transitionPopupAd = AdWhaleTransitionPopupAd(
    placementUid: PLACEMENT_UID,
    adLoadCallback: AdWhaleTransitionPopupAdLoadCallback(
      onLoaded: () {
        print('전환 팝업 광고 로드 성공');
        _isTransitionPopupLoaded = true;
      },
      onLoadFailed: (errorCode, errorMessage) {
        print('전환 팝업 광고 로드 실패: $errorCode, $errorMessage');
        _isTransitionPopupLoaded = false;
      },
      onShowed: () {
        print('전환 팝업 광고 표시됨');
        _isTransitionPopupLoaded = false;
      },
      onShowFailed: (errorCode, errorMessage) {
        print('전환 팝업 광고 표시 실패: $errorCode, $errorMessage');
        _isTransitionPopupLoaded = false;
      },
      onClosed: (reason) {
        print('전환 팝업 광고 닫힘: $reason');
      },
      onClicked: () {
        print('전환 팝업 광고 클릭됨');
      },
    ),
  )
    ..setRegion('서울시 서초구')
    ..setGcoder(37.49, 127.02)
    ..setPlacementName('transition-popup-main');

  _transitionPopupAd!.loadAd();
}

// 광고 표시 (화면 전환 직전 등, 로드 완료 후)
Future<void> _showTransitionPopup() async {
  if (_transitionPopupAd != null && _isTransitionPopupLoaded) {
    await _transitionPopupAd!.showAd();
  }
}
```

#### 3. 이벤트 리스너 등록

광고의 상태 변화를 추적하기 위해 이벤트 리스너를 등록합니다.

| 이벤트            | 설명            | 파라미터                                                                      |
| -------------- | ------------- | ------------------------------------------------------------------------- |
| `onLoaded`     | 광고 로드 성공 시 호출 | 없음                                                                        |
| `onLoadFailed` | 광고 로드 실패 시 호출 | <p><code>String errorCode</code> <br><code>String errorMessage</code></p> |
| `onShowed`     | 광고 표시 성공 시 호출 | 없음                                                                        |
| `onShowFailed` | 광고 표시 실패 시 호출 | <p><code>String errorCode</code> <br><code>String errorMessage</code></p> |
| `onClosed`     | 광고가 닫힐 때 호출   | `String reason`                                                           |
| `onClicked`    | 광고가 클릭될 때 호출  | 없음                                                                        |

#### 4. 옵션 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleTransitionPopupAd` 생성 시 추가 옵션을 설정할 수 있습니다. `loadAd()` 호출 전에 설정하세요.

```dart
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

AdWhaleTransitionPopupAd(
  placementUid: 'your-placement-uid', // 필수: Placement UID
  adLoadCallback: AdWhaleTransitionPopupAdLoadCallback(...),
)
  ..setRegion('서울시 서초구')                    // 지역 정보
  ..setGcoder(37.49, 127.02)                     // 위치 정보 (위도, 경도)
  ..setPlacementName('transition-popup-main')  // Placement 이름
  ..loadAd();
```

#### **5. Activity resume (자동 처리)**

네이티브 `AdWhaleMediationTransitionPopupAd.resume(Activity)`는 Activity가 foreground로 올라올 때 필요합니다. Flutter 플러그인이 `FlutterFragmentActivity`의 `Lifecycle.ON_RESUME`에 맞춰 **등록된 전환 팝업 광고에 대해 자동으로 `resume`을 호출**합니다. 앱 코드에서 별도 `resume()` API를 호출할 필요는 없습니다.

#### **5. 전환 팝업 샘플코드**

다음은 Flutter 위젯에서 전환 팝업 광고를 구현하는 완전한 예시입니다.

```dart
import 'dart:io' show Platform;

import 'package:flutter/material.dart';
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

class TransitionPopupAdScreen extends StatefulWidget {
  const TransitionPopupAdScreen({super.key});

  @override
  State<TransitionPopupAdScreen> createState() =>
      _TransitionPopupAdScreenState();
}

class _TransitionPopupAdScreenState extends State<TransitionPopupAdScreen> {
  AdWhaleTransitionPopupAd? _transitionPopupAd;
  bool _isLoaded = false;

  @override
  void initState() {
    super.initState();
    _initializeAndLoadTransitionPopup();
  }

  Future<void> _initializeAndLoadTransitionPopup() async {
    if (!Platform.isAndroid) return;

    final result = await AdWhaleMediationAds.instance.initialize();
    if (result.isSuccess) {
      print('SDK 초기화 성공: ${result.message}');
      _loadTransitionPopup();
    } else {
      print('SDK 초기화 실패: ${result.statusCode}, ${result.message}');
    }
  }

  void _loadTransitionPopup() {
    if (!Platform.isAndroid) return;

    _transitionPopupAd?.destroy();
    _transitionPopupAd = null;
    _isLoaded = false;

    _transitionPopupAd = AdWhaleTransitionPopupAd(
      placementUid: 'your-placement-uid',
      adLoadCallback: AdWhaleTransitionPopupAdLoadCallback(
        onLoaded: () {
          print('전환 팝업 광고 로드 성공');
          setState(() => _isLoaded = true);
          if (mounted) {
            ScaffoldMessenger.of(context).showSnackBar(
              const SnackBar(content: Text('전환 팝업 광고 로드 완료')),
            );
          }
        },
        onLoadFailed: (errorCode, errorMessage) {
          print('전환 팝업 광고 로드 실패: $errorCode, $errorMessage');
          setState(() => _isLoaded = false);
        },
        onShowed: () {
          print('전환 팝업 광고 표시됨');
          setState(() => _isLoaded = false);
        },
        onShowFailed: (errorCode, errorMessage) {
          print('전환 팝업 광고 표시 실패: $errorCode, $errorMessage');
          setState(() => _isLoaded = false);
        },
        onClosed: (reason) {
          print('전환 팝업 광고 닫힘: $reason');
          _loadTransitionPopup(); // 다음 노출을 위해 재로드
        },
        onClicked: () {
          print('전환 팝업 광고 클릭됨');
        },
      ),
    )
      ..setRegion('서울시 서초구')
      ..setGcoder(37.49, 127.02)
      ..setPlacementName('test_transition_popup');

    _transitionPopupAd!.loadAd();
  }

  Future<void> _showTransitionPopup() async {
    if (_transitionPopupAd != null && _isLoaded) {
      await _transitionPopupAd!.showAd();
    } else {
      if (mounted) {
        ScaffoldMessenger.of(context).showSnackBar(
          const SnackBar(content: Text('전환 팝업 광고를 먼저 로드해 주세요.')),
        );
      }
    }
  }

  Future<void> _goToNextScreen() async {
    // 화면 전환 직전에 광고 표시
    await _showTransitionPopup();
    if (!mounted) return;
    Navigator.of(context).push(
      MaterialPageRoute(builder: (_) => const _NextScreen()),
    );
  }

  @override
  Widget build(BuildContext context) {
    if (!Platform.isAndroid) {
      return Scaffold(
        appBar: AppBar(title: const Text('전환 팝업 예제')),
        body: const Center(
          child: Text('전환 팝업 광고는 Android 전용입니다.'),
        ),
      );
    }

    return Scaffold(
      appBar: AppBar(title: const Text('전환 팝업 예제')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            ElevatedButton(
              onPressed: _loadTransitionPopup,
              child: const Text('광고 로드'),
            ),
            const SizedBox(height: 16),
            ElevatedButton(
              onPressed: _isLoaded ? _showTransitionPopup : null,
              child: const Text('광고 표시'),
            ),
            const SizedBox(height: 16),
            ElevatedButton(
              onPressed: _isLoaded ? _goToNextScreen : null,
              child: const Text('다음 화면으로 (표시 후 이동)'),
            ),
          ],
        ),
      ),
    );
  }

  @override
  void dispose() {
    _transitionPopupAd?.destroy();
    super.dispose();
  }
}

class _NextScreen extends StatelessWidget {
  const _NextScreen();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('다음 화면')),
      body: const Center(child: Text('화면 전환 완료')),
    );
  }
}
```

#### **6. 주의사항**

#### **광고 로드 타이밍**

* 광고는 로드가 완료된 후에만 표시할 수 있습니다.
* `onLoaded` 이벤트가 발생한 후에 `showAd()`를 호출해야 합니다.
* SDK 초기화(`AdWhaleMediationAds.instance.initialize()`) 성공 후 `loadAd()`를 호출하세요.
* `loadAd()`는 Android에서만 네이티브 로드를 수행합니다.

#### **광고 표시 조건**

* **화면 전환 직전** (`Navigator.push` 전), 사용자 액션 직후 등 자연스러운 타이밍에 표시하세요.
* 로드되지 않은 상태에서 `showAd()`를 호출하면 표시되지 않을 수 있습니다.
* 표시 시 Activity가 `FlutterFragmentActivity`여야 합니다.

#### **플랫폼**

* **Android 전용**입니다. iOS에서는 `Platform.isAndroid` 분기로 호출을 막으세요.

#### **리스너 정리**

* 위젯이 dispose될 때 `destroy()`를 호출해 리소스를 해제하세요.

#### **에러 처리**

* `onLoadFailed`와 `onShowFailed` 이벤트에서 적절한 에러 처리를 구현하세요.
* `onShowFailed` 시 플러그인이 인스턴스를 정리할 수 있으므로, 필요 시 재생성 후 `loadAd()`를 다시 호출하세요.
* `onClosed`의 `reason`으로 사용자가 종료·취소했는지 분기할 수 있습니다.

#### **테스트**

* 개발 환경에서는 테스트용 Placement UID를 사용하세요.
