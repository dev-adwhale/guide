# 앱 오프닝 광고

{% hint style="info" %}
앱오프닝 광고는 앱이 실행될 때 또는 백그라운드에서 포그라운드로 전환될 때 표시되는 전면 광고입니다. 이 문서는 Flutter 프로젝트에서 AdWhale Mediation SDK를 사용하여 앱오프닝 광고를 연동하는 방법을 설명합니다.
{% endhint %}

#### 1. 주요특징 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

* 앱 실행 시 또는 포그라운드 전환 시 자동 표시 가능
* 다양한 옵션 설정 지원 (placementName, region, gcoder)
* 이벤트 기반 콜백 시스템으로 광고 상태 추적 가능

#### 2. 기본 구현 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

앱오프닝 광고를 로드하고 표시하는 기본적인 구현 방법입니다.

```dart
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

const String PLACEMENT_UID = 'your-placement-uid';

// 광고 인스턴스 생성 및 로드
AdWhaleAppOpenAd? _appOpenAd;

void _loadAppOpen() {
  _appOpenAd = AdWhaleAppOpenAd(
    placementUid: PLACEMENT_UID,
    adLoadCallback: AdWhaleAppOpenAdLoadCallback(
      onLoaded: () {
        print('앱오프닝 광고 로드 성공');
      },
      onLoadFailed: (errorCode, errorMessage) {
        print('앱오프닝 광고 로드 실패: $errorCode, $errorMessage');
        _appOpenAd = null;
      },
      onShowed: () {
        print('앱오프닝 광고 표시됨');
      },
      onShowFailed: (errorCode, errorMessage) {
        print('앱오프닝 광고 표시 실패: $errorCode, $errorMessage');
        _appOpenAd = null;
      },
      onDismissed: () {
        print('앱오프닝 광고 닫힘');
        _appOpenAd = null;
      },
      onClicked: () {
        print('앱오프닝 광고 클릭됨');
      },
    ),
  )..loadAd();
}

// 광고 표시
void _showAppOpen() {
  if (_appOpenAd != null) {
    _appOpenAd!.showAd();
  }
}
```

#### 3. 이벤트 리스너 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleAppOpenAdLoadCallback` 은 다음 이벤트를 지원합니다:

<table><thead><tr><th width="209.69140625">이벤트</th><th width="188.94921875">설명</th><th>파라미터</th></tr></thead><tbody><tr><td><code>onLoaded</code></td><td>광고 로드 성공 시 호출</td><td>없음</td></tr><tr><td><code>onLoadFailed</code></td><td>광고 로드 실패 시 호출</td><td><code>String errorCode</code>, <br><code>String errorMessage</code><br></td></tr><tr><td><code>onShowed</code></td><td>광고 표시 성공 시 호출</td><td>없음</td></tr><tr><td><code>onShowFailed</code></td><td>광고 표시 실패 시 호출</td><td><code>String errorCode</code>, <br><code>String errorMessage</code><br></td></tr><tr><td><code>onDismissed</code></td><td>광고가 닫힐 때 호출</td><td>없음</td></tr><tr><td><code>onClicked</code></td><td>광고가 클릭될 때 호출</td><td>없음</td></tr></tbody></table>

#### 4. 옵션 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleAppOpenAd` 생성 시 추가 옵션을 설정할 수 있습니다.

```dart
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

AdWhaleAppOpenAd(
  placementUid: 'placement-uid',  // 필수: Placement UID
  adLoadCallback: AdWhaleAppOpenAdLoadCallback(...),
)
  ..setRegion('서울시 강남구')      // 지역 정보
  ..setGcoder(37.5665, 126.9780)  // 위치 정보 (위도, 경도)
  ..setPlacementName('app-open-main') // Placement 이름
  ..loadAd();
```

#### 5. 앱 오프닝 광고 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

다음은 Flutter 컴포넌트에서 앱오프닝 광고를 구현하는 완전한 예시입니다.

```dart
import 'package:flutter/material.dart';
import 'dart:async';
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

class AppOpenAdScreen extends StatefulWidget {
  const AppOpenAdScreen({super.key});

  @override
  State<AppOpenAdScreen> createState() => _AppOpenAdScreenState();
}

class _AppOpenAdScreenState extends State<AppOpenAdScreen> with WidgetsBindingObserver {
  AdWhaleAppOpenAd? _appOpenAd;
  bool _isLoaded = false;
  AppLifecycleState? _appLifecycleState;

  @override
  void initState() {
    super.initState();
    WidgetsBinding.instance.addObserver(this);
    _initializeAndLoadAppOpen();
  }

  Future<void> _initializeAndLoadAppOpen() async {
    // SDK 초기화
    final result = await AdWhaleMediationAds.instance.initialize();
    if (result.isSuccess) {
      print('SDK 초기화 성공: ${result.message}');
      _loadAppOpen();
    } else {
      print('SDK 초기화 실패: ${result.statusCode}, ${result.message}');
    }
  }

  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    _appLifecycleState = state;
    if (state == AppLifecycleState.resumed) {
      // 백그라운드에서 포그라운드로 전환 시 광고 표시
      if (_isLoaded && _appOpenAd != null) {
        _appOpenAd!.showAd();
        _isLoaded = false;
        _appOpenAd = null;
      }
    }
  }

  void _loadAppOpen() {
    _appOpenAd?.destroy();
    _appOpenAd = null;
    _isLoaded = false;

    _appOpenAd = AdWhaleAppOpenAd(
      placementUid: 'your-placement-uid',
      adLoadCallback: AdWhaleAppOpenAdLoadCallback(
        onLoaded: () {
          print('앱오프닝 광고 로드 성공');
          setState(() {
            _isLoaded = true;
          });
          if (mounted) {
            ScaffoldMessenger.of(context).showSnackBar(
              const SnackBar(content: Text('앱오프닝 광고 로드 완료')),
            );
          }
        },
        onLoadFailed: (errorCode, errorMessage) {
          print('앱오프닝 광고 로드 실패: $errorCode, $errorMessage');
          setState(() {
            _appOpenAd = null;
            _isLoaded = false;
          });
        },
        onShowed: () {
          print('앱오프닝 광고 표시됨');
        },
        onShowFailed: (errorCode, errorMessage) {
          print('앱오프닝 광고 표시 실패: $errorCode, $errorMessage');
          setState(() {
            _appOpenAd = null;
            _isLoaded = false;
          });
        },
        onDismissed: () {
          print('앱오프닝 광고 닫힘');
          setState(() {
            _appOpenAd = null;
            _isLoaded = false;
          });
          // 다음 광고 미리 로드
          _loadAppOpen();
        },
        onClicked: () {
          print('앱오프닝 광고 클릭됨');
        },
      ),
    )
      ..setRegion('서울시 강남구')
      ..setGcoder(37.5665, 126.9780)
      ..setPlacementName('test_app_open');
    
    _appOpenAd!.loadAd();
  }

  void _showAppOpen() {
    if (_appOpenAd != null && _isLoaded) {
      _appOpenAd!.showAd();
      _isLoaded = false;
      _appOpenAd = null;
    } else {
      if (mounted) {
        ScaffoldMessenger.of(context).showSnackBar(
          const SnackBar(content: Text('앱오프닝 광고를 먼저 로드해 주세요.')),
        );
      }
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('앱오프닝 광고 예제')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            ElevatedButton(
              onPressed: _loadAppOpen,
              child: const Text('광고 로드'),
            ),
            const SizedBox(height: 16),
            ElevatedButton(
              onPressed: _isLoaded ? _showAppOpen : null,
              child: const Text('광고 표시'),
            ),
          ],
        ),
      ),
    );
  }

  @override
  void dispose() {
    WidgetsBinding.instance.removeObserver(this);
    _appOpenAd?.destroy();
    super.dispose();
  }
}
```

#### 6. 앱 시작 시 자동 표시 예시 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

앱이 시작될 때 자동으로 앱오프닝 광고를 표시하는 예시입니다.

```dart
import 'package:flutter/material.dart';
import 'dart:async';
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

class MyApp extends StatefulWidget {
  const MyApp({super.key});

  @override
  State<MyApp> createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> with WidgetsBindingObserver {
  AdWhaleAppOpenAd? _appOpenAd;
  bool _isAdLoaded = false;
  AppLifecycleState? _appLifecycleState;

  @override
  void initState() {
    super.initState();
    WidgetsBinding.instance.addObserver(this);
    _initializeAppOpenAd();
  }

  Future<void> _initializeAppOpenAd() async {
    // SDK 초기화
    final result = await AdWhaleMediationAds.instance.initialize();
    if (!result.isSuccess) {
      print('SDK 초기화 실패: ${result.message}');
      return;
    }

    // 이벤트 리스너 등록 및 초기 광고 로드
    _loadAppOpenAd();
  }

  void _loadAppOpenAd() {
    _appOpenAd?.destroy();
    _appOpenAd = null;
    _isAdLoaded = false;

    _appOpenAd = AdWhaleAppOpenAd(
      placementUid: 'your-placement-uid',
      adLoadCallback: AdWhaleAppOpenAdLoadCallback(
        onLoaded: () {
          _isAdLoaded = true;
          // 앱이 활성 상태일 때만 광고 표시
          if (WidgetsBinding.instance.lifecycleState == AppLifecycleState.resumed) {
            _appOpenAd?.showAd();
            _isAdLoaded = false;
            _appOpenAd = null;
          }
        },
        onDismissed: () {
          _isAdLoaded = false;
          _appOpenAd = null;
          // 광고가 닫힌 후 다음 광고 로드
          _loadAppOpenAd();
        },
        onLoadFailed: (code, msg) {
          _appOpenAd = null;
          _isAdLoaded = false;
        },
        onShowed: () {},
        onShowFailed: (code, msg) {
          _appOpenAd = null;
          _isAdLoaded = false;
        },
        onClicked: () {},
      ),
    )..loadAd();
  }

  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    _appLifecycleState = state;
    if (state == AppLifecycleState.resumed) {
      // 백그라운드에서 포그라운드로 전환 시 광고 표시
      if (_isAdLoaded && _appOpenAd != null) {
        _appOpenAd!.showAd();
        _isAdLoaded = false;
        _appOpenAd = null;
      }
    }
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'My App',
      home: const HomePage(),
    );
  }

  @override
  void dispose() {
    WidgetsBinding.instance.removeObserver(this);
    _appOpenAd?.destroy();
    super.dispose();
  }
}
```

#### 7. 주의사항 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

#### 광고 로드 타이밍 <a href="#id-1____385" id="id-1____385"></a>

* 광고는 로드가 완료된 후에만 표시할 수 있습니다.
* `onLoaded` 이벤트가 발생한 후에 `showAd()`를 호출해야 합니다.

#### 광고 표시 조건 <a href="#id-2____390" id="id-2____390"></a>

* 앱이 포그라운드 상태일 때만 광고를 표시해야 합니다.
* `AppLifecycleState`를 확인하여 앱이 활성 상태일 때만 광고를 표시하세요.

#### 리스너 정리 <a href="#id-3___395" id="id-3___395"></a>

* 컴포넌트가 언마운트될 때 등록한 이벤트 리스너를 반드시 제거해야 합니다.

#### 에러 처리 <a href="#id-4___400" id="id-4___400"></a>

* `onLoadFailed`와 `onShowFailed` 이벤트에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.

#### 테스트 <a href="#id-6__410" id="id-6__410"></a>

* 개발 환경에서는 테스트용 placement UID를 사용하세요.
* 실제 배포 전에 다양한 시나리오에서 테스트하세요.
