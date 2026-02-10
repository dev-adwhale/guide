# 보상형

{% hint style="info" %}
보상형 광고는 사용자가 광고를 시청한 후 보상을 받는 광고 형식입니다. 게임 내 아이템, 코인, 생명 등 다양한 보상을 제공하여 높은 참여도와 수익을 얻을 수 있습니다.
{% endhint %}

#### 1. 주요특징 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

* 사용자 시청 후 보상 제공
* 높은 참여도와 완료율
* 다양한 보상 타입 지원 (코인, 아이템 등)
* 이벤트 기반 콜백 시스템으로 광고 상태 및 보상 추적

#### 2. 기본 구현 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

보상형 광고를 로드하고 표시하는 기본적인 구현 방법입니다.

```dart
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

const String PLACEMENT_UID = 'your-placement-uid';

// 광고 인스턴스 생성 및 로드
AdWhaleRewardAd? _rewardAd;

void _loadReward() {
  _rewardAd = AdWhaleRewardAd(
    appCode: PLACEMENT_UID,
    adRewardLoadCallback: AdWhaleRewardAdLoadCallback(
      onLoaded: () {
        print('보상형 광고 로드 성공');
      },
      onLoadFailed: (errorCode, errorMessage) {
        print('보상형 광고 로드 실패: $errorCode, $errorMessage');
        _rewardAd = null;
      },
      onUserRewarded: (amount, type) {
        print('보상 지급: $type, $amount');
        // 보상 처리 로직
      },
      onClicked: () {
        print('보상형 광고 클릭됨');
      },
      onShowed: () {
        print('보상형 광고 표시됨');
      },
      onShowFailed: (errorCode, errorMessage) {
        print('보상형 광고 표시 실패: $errorCode, $errorMessage');
        _rewardAd = null;
      },
      onDismissed: () {
        print('보상형 광고 닫힘');
        _rewardAd = null;
      },
    ),
  )..loadAd();
}

void _showReward() {
  if (_rewardAd != null) {
    _rewardAd!.showAd();
  }
}
```

#### 3. 이벤트 리스너 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleRewardAdLoadCallback` 은 다음 이벤트를 지원합니다:

| 이벤트              | 설명                 | 파라미터                                                                       |
| ---------------- | ------------------ | -------------------------------------------------------------------------- |
| `onLoaded`       | 광고 로드 성공 시 호출      | 없음                                                                         |
| `onLoadFailed`   | 광고 로드 실패 시 호출      | <p><code>String errorCode</code>, <br><code>String errorMessage</code></p> |
| `onShowed`       | 광고 표시 성공 시 호출      | 없음                                                                         |
| `onShowFailed`   | 광고 표시 실패 시 호출      | <p><code>String errorCode</code>, <br><code>String errorMessage</code></p> |
| `onDismissed`    | 광고가 닫힐 때 호출        | 없음                                                                         |
| `onClicked`      | 광고가 클릭될 때 호출       | 없음                                                                         |
| `onUserRewarded` | 사용자에게 보상이 지급될 때 호출 | <p><code>int amount</code>, <br><code>String type</code></p>               |

#### 4. 보상 처리 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`onUserRewarded` 이벤트에서 보상을 처리하는 예제입니다.

```dart
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

AdWhaleRewardAd(
  appCode: 'your-placement-uid',
  adRewardLoadCallback: AdWhaleRewardAdLoadCallback(
    onUserRewarded: (amount, type) {
      print('보상 지급: $type, $amount');
      
      // 보상 타입에 따른 처리
      switch (type) {
        case 'coin':
          // 코인 지급
          addCoins(amount);
          break;
        case 'item':
          // 아이템 지급
          addItem(type, amount);
          break;
        case 'life':
          // 생명 지급
          addLife(amount);
          break;
        default:
          print('알 수 없는 보상 타입: $type');
      }
    },
    // ... 다른 콜백들
  ),
)..loadAd();
```

#### 5. 옵션 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleRewardAd` 생성 시 추가 옵션을 설정할 수 있습니다.

```dart
AdWhaleRewardAd(
  appCode: 'placement-uid',  // 필수: Placement UID
  adRewardLoadCallback: AdWhaleRewardAdLoadCallback(...),
)
  ..setRegion('서울시 강남구')      // 지역 정보
  ..setGcoder(37.5, 126.9)        // 위치 정보 (위도, 경도)
  ..setPlacementName('reward-main') // Placement 이름
  ..loadAd();
```

#### 7. 보상 광고 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

다음은 Flutter 위젯에서 보상형 광고를 구현하는 완전한 예시입니다.

```dart
import 'package:flutter/material.dart';
import 'package:adwhale_sdk_flutter/adwhale_sdk_flutter.dart';

class RewardAdScreen extends StatefulWidget {
  const RewardAdScreen({super.key});

  @override
  State<RewardAdScreen> createState() => _RewardAdScreenState();
}

class _RewardAdScreenState extends State<RewardAdScreen> {
  AdWhaleRewardAd? _rewardAd;
  bool _isLoaded = false;
  int _coins = 0;

  @override
  void initState() {
    super.initState();
    _initializeAndLoadReward();
  }

  Future<void> _initializeAndLoadReward() async {
    // SDK 초기화
    final result = await AdWhaleMediationAds.instance.initialize();
    if (result.isSuccess) {
      print('SDK 초기화 성공: ${result.message}');
      _loadReward();
    } else {
      print('SDK 초기화 실패: ${result.statusCode}, ${result.message}');
    }
  }

  void _loadReward() {
    _rewardAd?.destroy();
    _rewardAd = null;
    _isLoaded = false;

    _rewardAd = AdWhaleRewardAd(
      appCode: 'your-placement-uid',
      adRewardLoadCallback: AdWhaleRewardAdLoadCallback(
        onLoaded: () {
          print('보상형 광고 로드 성공');
          setState(() {
            _isLoaded = true;
          });
          if (mounted) {
            ScaffoldMessenger.of(context).showSnackBar(
              const SnackBar(content: Text('보상형 광고 로드 완료')),
            );
          }
        },
        onLoadFailed: (errorCode, errorMessage) {
          print('보상형 광고 로드 실패: $errorCode, $errorMessage');
          setState(() {
            _rewardAd = null;
            _isLoaded = false;
          });
        },
        onUserRewarded: (amount, type) {
          print('보상 지급: $type, $amount');
          // 코인 지급
          if (type == 'coin') {
            setState(() {
              _coins += amount;
            });
            if (mounted) {
              ScaffoldMessenger.of(context).showSnackBar(
                SnackBar(content: Text('$amount 코인을 받았습니다!')),
              );
            }
          }
        },
        onClicked: () {
          print('보상형 광고 클릭됨');
        },
        onShowed: () {
          print('보상형 광고 표시됨');
        },
        onShowFailed: (errorCode, errorMessage) {
          print('보상형 광고 표시 실패: $errorCode, $errorMessage');
          setState(() {
            _rewardAd = null;
            _isLoaded = false;
          });
        },
        onDismissed: () {
          print('보상형 광고 닫힘');
          setState(() {
            _rewardAd = null;
            _isLoaded = false;
          });
          // 다음 광고 미리 로드
          _loadReward();
        },
      ),
    )
      ..setRegion('test_reward_region')
      ..setGcoder(37.5, 126.9)
      ..setPlacementName('test_reward');
    
    _rewardAd!.loadAd();
  }

  void _showReward() {
    if (_rewardAd != null && _isLoaded) {
      _rewardAd!.showAd();
      _isLoaded = false;
      _rewardAd = null;
    } else {
      if (mounted) {
        ScaffoldMessenger.of(context).showSnackBar(
          const SnackBar(content: Text('보상형 광고를 먼저 로드해 주세요.')),
        );
      }
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('보상형 광고 예제')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text(
              '보유 코인: $_coins',
              style: const TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
            ),
            const SizedBox(height: 32),
            ElevatedButton(
              onPressed: _loadReward,
              child: const Text('광고 로드'),
            ),
            const SizedBox(height: 16),
            ElevatedButton(
              onPressed: _isLoaded ? _showReward : null,
              child: const Text('광고 시청하고 코인 받기'),
            ),
          ],
        ),
      ),
    );
  }

  @override
  void dispose() {
    _rewardAd?.destroy();
    super.dispose();
  }
}
```

#### 8. 주의사항 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

#### 보상 지급 시점

* `onUserRewarded` 이벤트는 사용자가 광고를 완전히 시청했을 때만 호출됩니다.
* 광고를 중간에 닫으면 보상이 지급되지 않습니다.

#### 광고 로드 타이밍 <a href="#id-2____486" id="id-2____486"></a>

* 광고는 로드가 완료된 후에만 표시할 수 있습니다.
* `onLoaded` 이벤트가 발생한 후에 `showAd()`를 호출해야 합니다.

#### 보상 중복 지급 방지 <a href="#id-3_____491" id="id-3_____491"></a>

* `onUserRewarded` 이벤트는 한 번만 호출됩니다.
* 서버와 동기화하여 중복 지급을 방지하는 것을 권장합니다.

#### 리스너 정리 <a href="#id-4___496" id="id-4___496"></a>

* 컴포넌트가 언마운트될 때 등록한 이벤트 리스너를 반드시 제거해야 합니다.

#### 에러 처리 <a href="#id-5___501" id="id-5___501"></a>

* `onLoadFailed`와 `onShowFailed` 이벤트에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.

#### 테스트 <a href="#id-7__511" id="id-7__511"></a>

* 개발 환경에서는 테스트용 placement UID를 사용하세요.
* 보상 지급 로직을 충분히 테스트하세요.
