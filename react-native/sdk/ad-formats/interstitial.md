# 전면

{% hint style="info" %}
전면 광고는 앱 화면 전체를 덮는 전면형 광고입니다. 게임 레벨 완료, 콘텐츠 전환 등 자연스러운 타이밍에 노출하여 높은 참여도를 얻을 수 있습니다.
{% endhint %}

#### 1. 주요특징 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

* 화면 전체를 덮는 전면형 광고
* 사용자 액션 후 자연스러운 타이밍에 노출
* 이벤트 기반 콜백 시스템으로 광고 상태 추적 가능
* 다양한 옵션 설정 지원

#### 2. 기본 구현 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

전면 광고를 로드하고 표시하는 기본적인 구현 방법입니다.

```typescript
import { AdWhaleInterstitialAd } from 'adwhale-sdk-react-native';

const PLACEMENT_UID = 'your-placement-uid';

// 광고 로드
AdWhaleInterstitialAd.loadAd(PLACEMENT_UID);

// 광고 표시
AdWhaleInterstitialAd.showAd();
```

#### 3. 이벤트 리스너 등록 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

광고의 상태 변화를 추적하기 위해 이벤트 리스너를 등록합니다.

```typescript
import { 
  AdWhaleInterstitialAd, 
  AdWhaleInterstitialErrorEvent 
} from 'adwhale-sdk-react-native';
import { useEffect } from 'react';

useEffect(() => {
  const subscriptions = AdWhaleInterstitialAd.addEventListeners({
    onLoaded: () => {
      console.log('전면 광고 로드 성공');
    },
    onLoadFailed: (event: AdWhaleInterstitialErrorEvent) => {
      console.log('전면 광고 로드 실패:', event.statusCode, event.message);
    },
    onShowed: () => {
      console.log('전면 광고 표시됨');
    },
    onShowFailed: (event: AdWhaleInterstitialErrorEvent) => {
      console.log('전면 광고 표시 실패:', event.statusCode, event.message);
    },
    onClosed: () => {
      console.log('전면 광고 닫힘');
    },
    onClicked: () => {
      console.log('전면 광고 클릭됨');
    },
  });

  // 컴포넌트 언마운트 시 리스너 제거
  return () => {
    subscriptions.forEach(sub => sub.remove());
  };
}, []);
```

#### 4. 이벤트 리스너 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleInterstitialAd.addEventListeners()` 메서드는 다음 이벤트를 지원합니다:

| 이벤트            | 설명            | 파라미터                            |
| -------------- | ------------- | ------------------------------- |
| `onLoaded`     | 광고 로드 성공 시 호출 | 없음                              |
| `onLoadFailed` | 광고 로드 실패 시 호출 | `AdWhaleInterstitialErrorEvent` |
| `onShowed`     | 광고 표시 성공 시 호출 | 없음                              |
| `onShowFailed` | 광고 표시 실패 시 호출 | `AdWhaleInterstitialErrorEvent` |
| `onClosed`     | 광고가 닫힐 때 호출   | 없음                              |
| `onClicked`    | 광고가 클릭될 때 호출  | 없음                              |

#### 5. 에러 이벤트 구조

```typescript
interface AdWhaleInterstitialErrorEvent {
  statusCode: number;
  message: string;
}
```

#### 6. 옵션 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`loadAd` 메서드 호출 시 추가 옵션을 설정할 수 있습니다.

```typescript
AdWhaleInterstitialAd.loadAd(placementUid, {
  placementName?: string;  // Placement 이름
  region?: string;          // 지역 코드 (예: 'KR')
  gcoder?: {                // 위치 정보
    lt: number;             // 위도
    lng: number;            // 경도
  }
});
```

#### 7. 전면 광고 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

다음은 React Native 컴포넌트에서 전면 광고를 구현하는 완전한 예시입니다.

```typescript
import React, { useEffect, useState } from 'react';
import { View, Text, Button, StyleSheet } from 'react-native';
import { 
  AdWhaleInterstitialAd, 
  AdWhaleInterstitialErrorEvent,
  AdWhaleMediationAds 
} from 'adwhale-sdk-react-native';

const PLACEMENT_UID = 'your-placement-uid';

const InterstitialAdExample: React.FC = () => {
  const [isLoaded, setIsLoaded] = useState(false);

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
    const subscriptions = AdWhaleInterstitialAd.addEventListeners({
      onLoaded: () => {
        console.log('전면 광고 로드 성공');
        setIsLoaded(true);
      },
      onLoadFailed: (event: AdWhaleInterstitialErrorEvent) => {
        console.log('전면 광고 로드 실패:', event.statusCode, event.message);
        setIsLoaded(false);
      },
      onShowed: () => {
        console.log('전면 광고 표시됨');
      },
      onShowFailed: (event: AdWhaleInterstitialErrorEvent) => {
        console.log('전면 광고 표시 실패:', event.statusCode, event.message);
        setIsLoaded(false);
      },
      onClosed: () => {
        console.log('전면 광고 닫힘');
        setIsLoaded(false);
        // 광고가 닫힌 후 다음 광고 로드
        AdWhaleInterstitialAd.loadAd(PLACEMENT_UID);
      },
      onClicked: () => {
        console.log('전면 광고 클릭됨');
      },
    });

    // 초기 광고 로드
    AdWhaleInterstitialAd.loadAd(PLACEMENT_UID);

    return () => {
      subscriptions.forEach(sub => sub.remove());
    };
  }, []);

  const handleLoadAd = () => {
    AdWhaleInterstitialAd.loadAd(PLACEMENT_UID);
  };

  const handleShowAd = () => {
    if (isLoaded) {
      AdWhaleInterstitialAd.showAd();
    } else {
      console.log('광고가 로드되지 않았습니다.');
    }
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>전면 광고 예제</Text>
      <Button title="광고 로드" onPress={handleLoadAd} />
      {isLoaded && (
        <Button title="광고 표시" onPress={handleShowAd} />
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
});

export default InterstitialAdExample;
```

#### 8. 주의사항 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

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
