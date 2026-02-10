# 배너

{% hint style="info" %}
배너 광고는 앱 화면의 특정 영역에 표시되는 직사각형 광고입니다. 사용자가 앱을 사용하는 동안 지속적으로 노출되어 높은 노출 빈도를 제공합니다.
{% endhint %}

#### 1. 주요특징 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

* 다양한 사이즈 지원 (320x50, 320x100, 300x250, 250x250, ADAPTIVE\_ANCHOR)
* 자동 로드 및 새로고침 기능
* 간단한 컴포넌트 기반 구현
* 이벤트 기반 콜백으로 광고 상태 추적

#### 2. 기본 구현 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

배너 광고는 `AdWhaleAdView` 와 `AdWhaleAdWidget` 을 사용하여 구현합니다.

```dart
import 'package:flutter/material.dart';
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

class BannerAdExample extends StatefulWidget {
  const BannerAdExample({super.key});

  @override
  State<BannerAdExample> createState() => _BannerAdExampleState();
}

class _BannerAdExampleState extends State<BannerAdExample> {
  AdWhaleAdView? _bannerAd;

  @override
  void initState() {
    super.initState();
    _loadBanner();
  }

  void _loadBanner() {
    _bannerAd = AdWhaleAdView(
      listener: AdWhaleAdViewListener(
        onLoaded: (ad) {
          print('배너 광고 로드 성공');
          setState(() {});
        },
        onLoadFailed: (ad, errorCode, errorMessage) {
          print('배너 광고 로드 실패: $errorCode, $errorMessage');
          ad.destroy();
          setState(() {
            _bannerAd = null;
          });
        },
        onClicked: (ad) {
          print('배너 광고 클릭됨');
        },
      ),
      adInfo: AdInfo('your-placement-uid', AdWhaleAdSize.BANNER_320x50),
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
          // 배너 광고 영역
          if (_bannerAd != null)
            SizedBox(
              height: 50,
              child: AdWhaleAdWidget(ad: _bannerAd!),
            ),
        ],
      ),
    );
  }

  @override
  void dispose() {
    _bannerAd?.destroy();
    super.dispose();
  }
}
```

#### 3. 배너 사이즈 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

배너 광고는 다음 사이즈를 지원합니다:

<table><thead><tr><th width="180.42578125">사이즈</th><th width="297.95703125">값</th><th>설명</th></tr></thead><tbody><tr><td>320x50</td><td><code>AdWhaleAdSize.BANNER_320x50</code></td><td>표준 배너 (Banner)</td></tr><tr><td>320x100</td><td><code>AdWhaleAdSize.BANNER_320x100</code></td><td>큰 배너 (Large Banner)</td></tr><tr><td>300x250</td><td><code>AdWhaleAdSize.BANNER_300x250</code></td><td>중간 직사각형 (Medium Rectangle)</td></tr><tr><td>250x250</td><td><code>AdWhaleAdSize.BANNER_250x250</code></td><td>정사각형 (Square)</td></tr><tr><td>ADAPTIVE_ANCHOR</td><td><code>AdWhaleAdSize.ADAPTIVE_ANCHOR</code></td><td>적응형 앵커 배너</td></tr></tbody></table>

**사용예시**

```dart
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

// 표준 배너
AdWhaleAdView(
  listener: listener,
  adInfo: AdInfo('placement-uid', AdWhaleAdSize.BANNER_320x50),
)..loadAd();

// 큰 배너
AdWhaleAdView(
  listener: listener,
  adInfo: AdInfo('placement-uid', AdWhaleAdSize.BANNER_320x100),
)..loadAd();

// 적응형 배너 (너비 지정)
AdWhaleAdView(
  listener: listener,
  adInfo: AdInfo('placement-uid', AdWhaleAdSize.ADAPTIVE_ANCHOR),
)
  ..setAdaptiveAnchorWidth(360) // 디바이스 너비 값 예시
  ..loadAd();

// 적응형 배너 (디바이스 전체 너비)
AdWhaleAdView(
  listener: listener,
  adInfo: AdInfo('placement-uid', AdWhaleAdSize.ADAPTIVE_ANCHOR),
)
  ..setAdaptiveAnchorWidth(0) // 0이면 디바이스 전체 너비
  ..loadAd();
```

#### 4. 이벤트 리스너 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleAdView` 컴포넌트는 다음 이벤트를 지원합니다:

| 이벤트            | 설명            | 파라미터                                                                                               |
| -------------- | ------------- | -------------------------------------------------------------------------------------------------- |
| `onLoaded`     | 광고 로드 성공 시 호출 | `Ad ad`                                                                                            |
| `onLoadFailed` | 광고 로드 실패 시 호출 | <p><code>Ad ad</code>, <br><code>String errorCode,</code> <br><code>String errorMessage</code></p> |
| `onClicked`    | 광고 클릭 시 호출    | `Ad ad`                                                                                            |

#### 5. 옵션 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleAdView` 컴포넌트 호출 시 추가 옵션을 설정할 수 있습니다.

```dart
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

AdWhaleAdView(
  listener: AdWhaleAdViewListener(...),
  adInfo: AdInfo(
    'placement-uid',              // 필수: Placement UID
    AdWhaleAdSize.BANNER_320x50 // 필수: 배너 사이즈
  ),
)
  ..setRegion('서울시 강남구')      // 지역 정보
  ..setGcoder(37.5665, 126.9780)  // 위치 정보 (위도, 경도)
  ..setPlacementName('main-banner') // Placement 이름
  ..setAdaptiveAnchorWidth(360)    // ADAPTIVE_ANCHOR 사용 시 배너 너비 (0이면 디바이스 전체)
  ..loadAd();
```

#### 6. 배너 광고 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

다음은 Flutter 위젯에서 배너 광고를 구현하는 완전한 예시입니다.

```dart
import 'package:flutter/material.dart';
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

class BannerAdScreen extends StatefulWidget {
  const BannerAdScreen({super.key});

  @override
  State<BannerAdScreen> createState() => _BannerAdScreenState();
}

class _BannerAdScreenState extends State<BannerAdScreen> {
  AdWhaleAdView? _bannerAd;
  AdWhaleAdSize _selectedSize = AdWhaleAdSize.BANNER_320x50;

  @override
  void initState() {
    super.initState();
    _initializeAndLoadBanner();
  }

  Future<void> _initializeAndLoadBanner() async {
    // SDK 초기화
    final result = await AdWhaleMediationAds.instance.initialize();
    if (result.isSuccess) {
      print('SDK 초기화 성공: ${result.message}');
      _createBanner();
    } else {
      print('SDK 초기화 실패: ${result.statusCode}, ${result.message}');
    }
  }

  void _createBanner() {
    _bannerAd?.destroy();
    _bannerAd = AdWhaleAdView(
      listener: AdWhaleAdViewListener(
        onLoaded: (ad) {
          print('배너 광고 로드 성공');
          setState(() {});
        },
        onLoadFailed: (ad, errorCode, errorMessage) {
          print('배너 광고 로드 실패: $errorCode, $errorMessage');
          ad.destroy();
          setState(() {
            _bannerAd = null;
          });
        },
        onClicked: (ad) {
          print('배너 광고 클릭됨');
        },
      ),
      adInfo: AdInfo('your-placement-uid', _selectedSize),
    )
      ..setRegion('서울시 강남구')
      ..setGcoder(37.5665, 126.9780)
      ..setPlacementName('main-banner')
      ..loadAd();
  }

  double _getBannerHeight() {
    if (_bannerAd == null) return 0;
    switch (_bannerAd!.adInfo.bannerHeight) {
      case AdWhaleAdSize.BANNER_320x50:
        return 50.0;
      case AdWhaleAdSize.BANNER_320x100:
        return 100.0;
      case AdWhaleAdSize.BANNER_300x250:
      case AdWhaleAdSize.BANNER_250x250:
        return 250.0;
      case AdWhaleAdSize.ADAPTIVE_ANCHOR:
        return 50.0;
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('배너 광고 예제')),
      body: Column(
        children: [
          Expanded(
            child: SingleChildScrollView(
              padding: const EdgeInsets.all(16),
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  const Text(
                    '배너 사이즈 선택',
                    style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
                  ),
                  const SizedBox(height: 8),
                  Wrap(
                    spacing: 8,
                    children: [
                      _buildSizeButton('320x50', AdWhaleAdSize.BANNER_320x50),
                      _buildSizeButton('320x100', AdWhaleAdSize.BANNER_320x100),
                      _buildSizeButton('300x250', AdWhaleAdSize.BANNER_300x250),
                      _buildSizeButton('250x250', AdWhaleAdSize.BANNER_250x250),
                      _buildSizeButton('Adaptive', AdWhaleAdSize.ADAPTIVE_ANCHOR),
                    ],
                  ),
                  const SizedBox(height: 16),
                  ElevatedButton(
                    onPressed: _createBanner,
                    child: const Text('광고 다시 로드'),
                  ),
                ],
              ),
            ),
          ),
          // 배너 광고 영역
          if (_bannerAd != null)
            Container(
              width: double.infinity,
              height: _getBannerHeight(),
              color: Colors.grey[200],
              child: AdWhaleAdWidget(ad: _bannerAd!),
            ),
        ],
      ),
    );
  }

  Widget _buildSizeButton(String label, AdWhaleAdSize size) {
    final isSelected = _selectedSize == size;
    return ElevatedButton(
      onPressed: () {
        setState(() {
          _selectedSize = size;
        });
        _createBanner();
      },
      style: ElevatedButton.styleFrom(
        backgroundColor: isSelected ? Colors.blue : Colors.grey,
      ),
      child: Text(label),
    );
  }

  @override
  void dispose() {
    _bannerAd?.destroy();
    super.dispose();
  }
}
```

#### 7. 주의사항 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

#### 광고 로드 타이밍

* `loadAd()` 메서드를 호출해야 광고가 로드됩니다.
* SDK 초기화가 완료된 후에 광고를 로드하는 것을 권장합니다.

#### 위젯 재생성 <a href="#id-2___489" id="id-2___489"></a>

* 배너 사이즈나 placement UID를 변경할 때는 `AdWhaleAdView` 를 `destroy`하고 새로 생성해야 합니다.
* `setState()` 를 호출하여 UI를 갱신하세요.
* 너비는 보통 `double.infinity`로 설정하여 화면 전체를 사용합니다.

#### 에러 처리 <a href="#id-4___499" id="id-4___499"></a>

* `onAdLoadFailed` 이벤트에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.
* 실패한 광고는 `destroy()` 를 호출하여 정리하세요.

#### 성능 최적화 <a href="#id-5___504" id="id-5___504"></a>

* 배너 광고는 자동으로 새로고침되므로, 불필요한 리렌더링을 방지하세요.

#### 테스트 <a href="#id-6__509" id="id-6__509"></a>

* 개발 환경에서는 테스트용 placement UID를 사용하세요.

