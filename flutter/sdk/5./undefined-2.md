# 전면

{% hint style="danger" %}
전면은 현재 **Android**에서만 지원되며, iOS 지원은 추후 예정입니다.
{% endhint %}

{% hint style="info" %}
전면 광고는 앱 화면 전체를 덮는 전면형 광고입니다. 게임 레벨 완료, 콘텐츠 전환 등 자연스러운 타이밍에 노출하여 높은 참여도를 얻을 수 있습니다.
{% endhint %}

#### 1. 주요특징 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

* 화면 전체를 덮는 전면형 광고
* 사용자 액션 후 자연스러운 타이밍에 노출
* 이벤트 기반 콜백 시스템으로 광고 상태 추적 가능
* 다양한 옵션 설정 지원

#### 2. 기본 구현 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

전면 광고를 로드하고 표시하는 기본적인 구현 방법입니다.

```dart
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

const String PLACEMENT_UID = 'your-placement-uid';

// 광고 인스턴스 생성 및 로드
AdWhaleInterstitialAd? _interstitialAd;

void _loadInterstitial() {
  _interstitialAd = AdWhaleInterstitialAd(
    appCode: PLACEMENT_UID,
    adLoadCallback: AdWhaleInterstitialAdLoadCallback(
      onLoaded: () {
        print('전면 광고 로드 성공');
      },
      onLoadFailed: (errorCode, errorMessage) {
        print('전면 광고 로드 실패: $errorCode, $errorMessage');
        _interstitialAd = null;
      },
      onShowed: () {
        print('전면 광고 표시됨');
      },
      onShowFailed: (errorCode, errorMessage) {
        print('전면 광고 표시 실패: $errorCode, $errorMessage');
        _interstitialAd = null;
      },
      onClosed: () {
        print('전면 광고 닫힘');
        _interstitialAd = null;
      },
      onClicked: () {
        print('전면 광고 클릭됨');
      },
    ),
  )..loadAd();
}

// 광고 표시
void _showInterstitial() {
  if (_interstitialAd != null) {
    _interstitialAd!.showAd();
  }
}
```

#### 3. 이벤트 리스너 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleInterstitialAdLoadCallback` 은 다음 이벤트를 지원합니다:

| 이벤트            | 설명            | 파라미터                                                                           |
| -------------- | ------------- | ------------------------------------------------------------------------------ |
| `onLoaded`     | 광고 로드 성공 시 호출 | 없음                                                                             |
| `onLoadFailed` | 광고 로드 실패 시 호출 | <p><code>String errorCode</code>, <br><code>String errorMessage</code><br></p> |
| `onShowed`     | 광고 표시 성공 시 호출 | 없음                                                                             |
| `onShowFailed` | 광고 표시 실패 시 호출 | <p><code>String errorCode</code>, <br><code>String errorMessage</code><br></p> |
| `onClosed`     | 광고가 닫힐 때 호출   | 없음                                                                             |
| `onClicked`    | 광고가 클릭될 때 호출  | 없음                                                                             |

#### 4. 옵션 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleInterstitialAd` 생성 시 추가 옵션을 설정할 수 있습니다.

```dart
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

AdWhaleInterstitialAd(
  appCode: 'placement-uid',  // 필수: Placement UID
  adLoadCallback: AdWhaleInterstitialAdLoadCallback(...),
)
  ..setRegion('서울시 서초구')      // 지역 정보
  ..setGcoder(37.49, 127.02)       // 위치 정보 (위도, 경도)
  ..setPlacementName('interstitial-main') // Placement 이름
  ..loadAd();
```

#### 5. 전면 광고 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

다음은 Flutter 위젯에서 전면 광고를 구현하는 완전한 예시입니다.

```dart
import 'package:flutter/material.dart';
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

class InterstitialAdScreen extends StatefulWidget {
  const InterstitialAdScreen({super.key});

  @override
  State<InterstitialAdScreen> createState() => _InterstitialAdScreenState();
}

class _InterstitialAdScreenState extends State<InterstitialAdScreen> {
  AdWhaleInterstitialAd? _interstitialAd;
  bool _isLoaded = false;

  @override
  void initState() {
    super.initState();
    _initializeAndLoadInterstitial();
  }

  Future<void> _initializeAndLoadInterstitial() async {
    // SDK 초기화
    final result = await AdWhaleMediationAds.instance.initialize();
    if (result.isSuccess) {
      print('SDK 초기화 성공: ${result.message}');
      _loadInterstitial();
    } else {
      print('SDK 초기화 실패: ${result.statusCode}, ${result.message}');
    }
  }

  void _loadInterstitial() {
    _interstitialAd?.destroy();
    _interstitialAd = null;
    _isLoaded = false;

    _interstitialAd = AdWhaleInterstitialAd(
      appCode: 'your-placement-uid',
      adLoadCallback: AdWhaleInterstitialAdLoadCallback(
        onLoaded: () {
          print('전면 광고 로드 성공');
          setState(() {
            _isLoaded = true;
          });
          if (mounted) {
            ScaffoldMessenger.of(context).showSnackBar(
              const SnackBar(content: Text('전면 광고 로드 완료')),
            );
          }
        },
        onLoadFailed: (errorCode, errorMessage) {
          print('전면 광고 로드 실패: $errorCode, $errorMessage');
          setState(() {
            _interstitialAd = null;
            _isLoaded = false;
          });
        },
        onShowed: () {
          print('전면 광고 표시됨');
        },
        onShowFailed: (errorCode, errorMessage) {
          print('전면 광고 표시 실패: $errorCode, $errorMessage');
          setState(() {
            _interstitialAd = null;
            _isLoaded = false;
          });
        },
        onClosed: () {
          print('전면 광고 닫힘');
          setState(() {
            _interstitialAd = null;
            _isLoaded = false;
          });
          // 다음 광고 미리 로드
          _loadInterstitial();
        },
        onClicked: () {
          print('전면 광고 클릭됨');
        },
      ),
    )
      ..setRegion('서울시 서초구')
      ..setGcoder(37.49, 127.02)
      ..setPlacementName('test_interstitial');
    
    _interstitialAd!.loadAd();
  }

  void _showInterstitial() {
    if (_interstitialAd != null && _isLoaded) {
      _interstitialAd!.showAd();
      _isLoaded = false;
      _interstitialAd = null;
    } else {
      if (mounted) {
        ScaffoldMessenger.of(context).showSnackBar(
          const SnackBar(content: Text('전면 광고를 먼저 로드해 주세요.')),
        );
      }
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('전면 광고 예제')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            ElevatedButton(
              onPressed: _loadInterstitial,
              child: const Text('광고 로드'),
            ),
            const SizedBox(height: 16),
            ElevatedButton(
              onPressed: _isLoaded ? _showInterstitial : null,
              child: const Text('광고 표시'),
            ),
          ],
        ),
      ),
    );
  }

  @override
  void dispose() {
    _interstitialAd?.destroy();
    super.dispose();
  }
}
```

#### 6. 주의사항 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

#### 광고 로드 타이밍

* 광고는 로드가 완료된 후에만 표시할 수 있습니다.
* `onLoaded` 이벤트가 발생한 후에 `showAd()`를 호출해야 합니다.

#### 광고 표시 조건 <a href="#id-2____376" id="id-2____376"></a>

* 사용자 액션(레벨 완료, 페이지 전환 등) 후 자연스러운 타이밍에 표시하세요.
* 너무 자주 표시하면 사용자 경험이 저하될 수 있습니다.

#### 리스너 정리 <a href="#id-3___381" id="id-3___381"></a>

* 컴포넌트가 언마운트될 때 등록한 이벤트 리스너를 반드시 제거해야 합니다.

#### 에러 처리 <a href="#id-4___386" id="id-4___386"></a>

* `onLoadFailed`와 `onShowFailed` 이벤트에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.

#### 테스트 <a href="#id-6__396" id="id-6__396"></a>

* 개발 환경에서는 테스트용 placement UID를 사용하세요.
* 실제 배포 전에 다양한 시나리오에서 테스트하세요.
