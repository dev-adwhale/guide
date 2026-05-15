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

```typescript
import { AdWhaleRewardAd } from 'adwhale-sdk-react-native';

const PLACEMENT_UID = 'your-placement-uid';

// 광고 로드
AdWhaleRewardAd.loadAd(PLACEMENT_UID);

// 광고 표시
AdWhaleRewardAd.showAd();
```

#### 3. 이벤트 리스너 등록 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

광고의 상태 변화를 추적하기 위해 이벤트 리스너를 등록합니다.

```typescript
import { 
  AdWhaleRewardAd, 
  AdWhaleRewardErrorEvent,
  AdWhaleRewardUserRewardedEvent
} from 'adwhale-sdk-react-native';
import { useEffect } from 'react';

useEffect(() => {
  const subscriptions = AdWhaleRewardAd.addEventListeners({
    onLoaded: () => {
      console.log('보상형 광고 로드 성공');
    },
    onLoadFailed: (event: AdWhaleRewardErrorEvent) => {
      console.log('보상형 광고 로드 실패:', event.statusCode, event.message);
    },
    onShowed: () => {
      console.log('보상형 광고 표시됨');
    },
    onShowFailed: (event: AdWhaleRewardErrorEvent) => {
      console.log('보상형 광고 표시 실패:', event.statusCode, event.message);
    },
    onDismissed: () => {
      console.log('보상형 광고 닫힘');
    },
    onClicked: () => {
      console.log('보상형 광고 클릭됨');
    },
    onUserRewarded: (event: AdWhaleRewardUserRewardedEvent) => {
      console.log('보상 지급:', event.type, event.amount);
      // 보상 처리 로직
    },
  });

  // 컴포넌트 언마운트 시 리스너 제거
  return () => {
    subscriptions.forEach(sub => sub.remove());
  };
}, []);
```

#### 4. 이벤트 리스너 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleRewardAd.addEventListeners()` 메서드는 다음 이벤트를 지원합니다:

| 이벤트              | 설명                 | 파라미터                             |
| ---------------- | ------------------ | -------------------------------- |
| `onLoaded`       | 광고 로드 성공 시 호출      | 없음                               |
| `onLoadFailed`   | 광고 로드 실패 시 호출      | `AdWhaleRewardErrorEvent`        |
| `onShowed`       | 광고 표시 성공 시 호출      | 없음                               |
| `onShowFailed`   | 광고 표시 실패 시 호출      | `AdWhaleRewardErrorEvent`        |
| `onDismissed`    | 광고가 닫힐 때 호출        | 없음                               |
| `onClicked`      | 광고가 클릭될 때 호출       | 없음                               |
| `onUserRewarded` | 사용자에게 보상이 지급될 때 호출 | `AdWhaleRewardUserRewardedEvent` |

#### 5. 에러 이벤트 구조

```typescript
interface AdWhaleRewardErrorEvent {
  statusCode: number;
  message: string;
}
```

#### 6. 보상 이벤트 구조 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

```typescript
interface AdWhaleRewardUserRewardedEvent {
  type: string;    // 보상 타입 (예: 'coin', 'item', 'life')
  amount: number;  // 보상 수량
}
```

#### 7. 보상 처리 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`onUserRewarded` 이벤트에서 보상을 처리하는 예제입니다.

```typescript
const subscriptions = AdWhaleRewardAd.addEventListeners({
  onUserRewarded: (event: AdWhaleRewardUserRewardedEvent) => {
    console.log('보상 지급:', event.type, event.amount);
    
    // 보상 타입에 따른 처리
    switch (event.type) {
      case 'coin':
        // 코인 지급
        addCoins(event.amount);
        break;
      case 'item':
        // 아이템 지급
        addItem(event.type, event.amount);
        break;
      case 'life':
        // 생명 지급
        addLife(event.amount);
        break;
      default:
        console.log('알 수 없는 보상 타입:', event.type);
    }
  },
});
```

#### 6. 옵션 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`loadAd` 메서드 호출 시 추가 옵션을 설정할 수 있습니다.

```typescript
AdWhaleRewardAd.loadAd(placementUid, {
  placementName?: string;  // Placement 이름
  region?: string;          // 지역 코드 (예: 'KR')
  gcoder?: {                // 위치 정보
    lt: number;             // 위도
    lng: number;            // 경도
  }
});
```

#### 7. 보상 광고 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

다음은 React Native 컴포넌트에서 보상형 광고를 구현하는 완전한 예시입니다.

```typescript
import React, { useEffect, useState } from 'react';
import { View, Text, Button, StyleSheet } from 'react-native';
import { 
  AdWhaleRewardAd, 
  AdWhaleRewardErrorEvent,
  AdWhaleRewardUserRewardedEvent,
  AdWhaleMediationAds 
} from 'adwhale-sdk-react-native';

const PLACEMENT_UID = 'your-placement-uid';

const RewardAdExample: React.FC = () => {
  const [isLoaded, setIsLoaded] = useState(false);
  const [coins, setCoins] = useState(0);

  useEffect(() => {
    // SDK 초기화
    AdWhaleMediationAds.initialize()
      .then(result => {
        if (result.isSuccess) {
          console.log('SDK 초기화 성공:', result.message);
        } else {
          console.log('SDK 초기화 실패:', result.statusCode, result.message);
        }
      })
      .catch(err => {
        console.error('SDK 초기화 에러:', err);
      });

    // 이벤트 리스너 등록
    const subscriptions = AdWhaleRewardAd.addEventListeners({
      onLoaded: () => {
        console.log('보상형 광고 로드 성공');
        setIsLoaded(true);
      },
      onLoadFailed: (event: AdWhaleRewardErrorEvent) => {
        console.log('보상형 광고 로드 실패:', event.statusCode, event.message);
        setIsLoaded(false);
      },
      onShowed: () => {
        console.log('보상형 광고 표시됨');
      },
      onShowFailed: (event: AdWhaleRewardErrorEvent) => {
        console.log('보상형 광고 표시 실패:', event.statusCode, event.message);
        setIsLoaded(false);
      },
      onDismissed: () => {
        console.log('보상형 광고 닫힘');
        setIsLoaded(false);
      },
      onClicked: () => {
        console.log('보상형 광고 클릭됨');
      },
      onUserRewarded: (event: AdWhaleRewardUserRewardedEvent) => {
        console.log('보상 지급:', event.type, event.amount);
        // 코인 지급
        if (event.type === 'coin') {
          setCoins(prev => prev + event.amount);
        }
      },
    });

    // 초기 광고 로드
    AdWhaleRewardAd.loadAd(PLACEMENT_UID);

    return () => {
      subscriptions.forEach(sub => sub.remove());
    };
  }, []);

  const handleLoadAd = () => {
    AdWhaleRewardAd.loadAd(PLACEMENT_UID);
  };

  const handleShowAd = () => {
    if (isLoaded) {
      AdWhaleRewardAd.showAd();
    } else {
      console.log('광고가 로드되지 않았습니다.');
    }
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>보상형 광고 예제</Text>
      <Text style={styles.coins}>보유 코인: {coins}</Text>
      <Button title="광고 로드" onPress={handleLoadAd} />
      {isLoaded && (
        <Button title="광고 시청하고 코인 받기" onPress={handleShowAd} />
      )}
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    padding: 20,
  },
  title: {
    fontSize: 20,
    fontWeight: 'bold',
    marginBottom: 20,
  },
  coins: {
    fontSize: 18,
    marginBottom: 20,
  },
});

export default RewardAdExample;
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
