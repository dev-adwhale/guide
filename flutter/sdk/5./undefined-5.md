# 앱 종료 팝업

{% hint style="danger" %}
앱 종료 팝업 광고는 현재 **Android**에서만 지원됩니다.
{% endhint %}

{% hint style="info" %}
앱종료광고(Exit Popup Ad)는 사용자가 앱을 종료하려 할 때(예: 백키 입력) 노출되는 팝업형 광고입니다. 종료 확인 다이얼로그 대신 또는 함께 사용하여 이탈 전 마지막 노출 기회를 제공합니다.
{% endhint %}

#### **1. 주요특징**

* 앱 종료 시점(백키 등)에 노출되는 팝업형 광고
* 메인/보조 버튼 텍스트 및 설명 문구 커스터마이징 지원 (일부 광고 소스에만 적용되며, 타게팅 설정을 지원하지 않는 경우 적용되지 않을 수 있습니다.)
* 다양한 옵션 설정 지원 (placementName, region, gcoder)
* 로드, 표시, 닫힘 등 이벤트 기반 콜백 시스템으로 광고 상태 추적 가능

#### 2. 기본 구현 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

종료 팝업 광고를 로드하고 표시하는 기본적인 구현 방법입니다.

```dart
import 'dart:io' show Platform;
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

const String PLACEMENT_UID = 'your-exit-popup-placement-uid';

AdWhaleExitPopupAd? _exitPopupAd;
bool _isExitPopupAdLoaded = false;

void _loadExitPopup() {
  if (!Platform.isAndroid) return;

  _exitPopupAd = AdWhaleExitPopupAd(
    placementUid: PLACEMENT_UID,
    adLoadCallback: AdWhaleExitPopupAdLoadCallback(
      onLoaded: () {
        print('종료 팝업 광고 로드 성공');
        _isExitPopupAdLoaded = true;
      },
      onLoadFailed: (errorCode, errorMessage) {
        print('종료 팝업 광고 로드 실패: $errorCode, $errorMessage');
        _isExitPopupAdLoaded = false;
      },
      onShowed: () {
        print('종료 팝업 광고 표시됨');
        _isExitPopupAdLoaded = false;
      },
      onShowFailed: (errorCode, errorMessage) {
        print('종료 팝업 광고 표시 실패: $errorCode, $errorMessage');
        _isExitPopupAdLoaded = false;
      },
      onClosed: (reason) {
        print('종료 팝업 광고 닫힘: $reason');
        _isExitPopupAdLoaded = false;
      },
      onClicked: () {
        print('종료 팝업 광고 클릭됨');
      },
    ),
  )
    ..setCustomizeButtonText('취소', '종료')
    ..setCustomDescription('앱을 종료하시겠습니까?');

  _exitPopupAd!.loadAd();
}

// 광고 표시 (로드 완료 후)
Future<void> _showExitPopup() async {
  if (_exitPopupAd != null && _isExitPopupAdLoaded) {
    await _exitPopupAd!.showAd();
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

`AdWhaleExitPopupAd` 생성 시 추가 옵션을 설정할 수 있습니다. `loadAd()` 호출 전에 설정하세요.

```dart
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

AdWhaleExitPopupAd(
  placementUid: 'your-placement-uid', // 필수: Placement UID
  adLoadCallback: AdWhaleExitPopupAdLoadCallback(...),
)
  ..setCustomizeButtonText('취소', '종료')   // 주/보조 버튼 문구
  ..setCustomDescription('앱을 종료하시겠습니까?') // 설명 문구
  ..setRegion('서울시 서초구')              // 지역 정보
  ..setGcoder(37.49, 127.02)               // 위치 정보 (위도, 경도)
  ..setPlacementName('exit-popup-main')   // Placement 이름
  ..loadAd();
```

#### **5. 종료 팝업 샘플코드**

다음은 Flutter 위젯에서 종료 팝업 광고를 구현하는 완전한 예시입니다. Android **뒤로 가기** 시 광고를 표시하고, 표시 중에는 화면이 pop되지 않도록 처리합니다.

```dart
import 'dart:io' show Platform;

import 'package:flutter/material.dart';
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

class ExitPopupAdScreen extends StatefulWidget {
  const ExitPopupAdScreen({super.key});

  @override
  State<ExitPopupAdScreen> createState() => _ExitPopupAdScreenState();
}

class _ExitPopupAdScreenState extends State<ExitPopupAdScreen> {
  AdWhaleExitPopupAd? _exitPopupAd;
  bool _isLoaded = false;
  bool _exitPopupAdGuard = false;

  @override
  void initState() {
    super.initState();
    _initializeAndLoadExitPopup();
  }

  Future<void> _initializeAndLoadExitPopup() async {
    if (!Platform.isAndroid) return;

    final result = await AdWhaleMediationAds.instance.initialize();
    if (result.isSuccess) {
      print('SDK 초기화 성공: ${result.message}');
      _loadExitPopup();
    } else {
      print('SDK 초기화 실패: ${result.statusCode}, ${result.message}');
    }
  }

  void _loadExitPopup() {
    if (!Platform.isAndroid) return;

    _exitPopupAd = AdWhaleExitPopupAd(
      placementUid: 'your-placement-uid',
      adLoadCallback: AdWhaleExitPopupAdLoadCallback(
        onLoaded: () {
          print('종료 팝업 광고 로드 성공');
          setState(() => _isLoaded = true);
        },
        onLoadFailed: (errorCode, errorMessage) {
          print('종료 팝업 광고 로드 실패: $errorCode, $errorMessage');
          setState(() => _isLoaded = false);
        },
        onShowed: () {
          print('종료 팝업 광고 표시됨');
          setState(() => _isLoaded = false);
        },
        onShowFailed: (errorCode, errorMessage) {
          print('종료 팝업 광고 표시 실패: $errorCode, $errorMessage');
          setState(() => _isLoaded = false);
        },
        onClosed: (reason) {
          print('종료 팝업 광고 닫힘: $reason');
          setState(() => _isLoaded = false);
          _loadExitPopup(); // 다음 노출을 위해 재로드
        },
        onClicked: () {
          print('종료 팝업 광고 클릭됨');
        },
      ),
    )
      ..setCustomizeButtonText('취소', '종료')
      ..setCustomDescription('앱을 종료하시겠습니까?')
      ..setRegion('서울시 서초구')
      ..setGcoder(37.49, 127.02)
      ..setPlacementName('test_exit_popup');

    _exitPopupAd!.loadAd();
  }

  Future<bool> _onWillPop() async {
    if (!Platform.isAndroid) return true;
    if (_exitPopupAdGuard) return false;
    if (!_isLoaded || _exitPopupAd == null) return true;

    _exitPopupAdGuard = true;
    try {
      await _exitPopupAd!.showAd();
      return false; // 팝업 표시 중 pop 방지
    } catch (e) {
      print('showAd failed: $e');
      return true;
    } finally {
      Future<void>.delayed(const Duration(seconds: 2)).then((_) {
        _exitPopupAdGuard = false;
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    if (!Platform.isAndroid) {
      return Scaffold(
        appBar: AppBar(title: const Text('종료 팝업 예제')),
        body: const Center(
          child: Text('종료 팝업 광고는 Android 전용입니다.'),
        ),
      );
    }

    return PopScope(
      canPop: false,
      onPopInvokedWithResult: (didPop, result) async {
        if (didPop) return;
        final shouldPop = await _onWillPop();
        if (shouldPop && context.mounted) {
          Navigator.of(context).pop();
        }
      },
      child: Scaffold(
        appBar: AppBar(title: const Text('종료 팝업 예제')),
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              Text(_isLoaded ? '광고 로드 완료 (뒤로 가기로 표시)' : '광고 로드 대기 중'),
              const SizedBox(height: 16),
              ElevatedButton(
                onPressed: _loadExitPopup,
                child: const Text('광고 다시 로드'),
              ),
            ],
          ),
        ),
      ),
    );
  }

  @override
  void dispose() {
    _exitPopupAd?.destroy();
    super.dispose();
  }
}
```

> Flutter 3.12 이하에서는 `PopScope` 대신 `WillPopScope(onWillPop: _onWillPop, child: ...)` 를 사용할 수 있습니다.

#### **6. 주의사항**

#### **광고 로드 타이밍**

* 광고는 로드가 완료된 후에만 표시할 수 있습니다.
* `onLoaded` 이벤트가 발생한 후에 `showAd()`를 호출해야 합니다.
* SDK 초기화(`AdWhaleMediationAds.instance.initialize()`) 성공 후 `loadAd()`를 호출하세요.

#### **광고 표시 조건**

* Android **뒤로 가기** 등 앱 종료 직전 등 자연스러운 타이밍에 표시하세요.
* 연속 뒤로 가기로 `showAd()`가 중복 호출되지 않도록 가드 플래그(`_exitPopupAdGuard`)를 두는 것을 권장합니다.
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
