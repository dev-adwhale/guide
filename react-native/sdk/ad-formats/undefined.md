# 앱 오프닝 광고

{% hint style="info" %}
앱오프닝 광고는 앱이 실행될 때 또는 백그라운드에서 포그라운드로 전환될 때 표시되는 전면 광고입니다. 이 문서는 React Native 프로젝트에서 AdWhale Mediation SDK를 사용하여 앱오프닝 광고를 연동하는 방법을 설명합니다.
{% endhint %}

#### 1. 주요특징 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

* 앱 실행 시 또는 포그라운드 전환 시 자동 표시 가능
* 다양한 옵션 설정 지원 (placementName, region, gcoder)
* 이벤트 기반 콜백 시스템으로 광고 상태 추적 가능

#### 2. 기본 구현 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

앱오프닝 광고를 로드하고 표시하는 기본적인 구현 방법입니다.

```typescript
import { AdWhaleAppOpenAd } from 'adwhale-sdk-react-native';

const PLACEMENT_UID = 'your-placement-uid';

// 광고 로드
AdWhaleAppOpenAd.loadAd(PLACEMENT_UID);

// 광고 표시
AdWhaleAppOpenAd.showAd();
```

#### 3. 이벤트 리스너 등록 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

광고의 상태 변화를 추적하기 위해 이벤트 리스너를 등록합니다.

```typescript
import { 
  AdWhaleAppOpenAd, 
  AdWhaleAppOpenErrorEvent 
} from 'adwhale-sdk-react-native';
import { useEffect } from 'react';

useEffect(() => {
  const subscriptions = AdWhaleAppOpenAd.addEventListeners({
    onLoaded: () => {
      console.log('앱오프닝 광고 로드 성공');
    },
    onLoadFailed: (event: AdWhaleAppOpenErrorEvent) => {
      console.log('앱오프닝 광고 로드 실패:', event.statusCode, event.message);
    },
    onShowed: () => {
      console.log('앱오프닝 광고 표시됨');
    },
    onShowFailed: (event: AdWhaleAppOpenErrorEvent) => {
      console.log('앱오프닝 광고 표시 실패:', event.statusCode, event.message);
    },
    onDismissed: () => {
      console.log('앱오프닝 광고 닫힘');
    },
    onClicked: () => {
      console.log('앱오프닝 광고 클릭됨');
    },
  });

  // 컴포넌트 언마운트 시 리스너 제거
  return () => {
    subscriptions.forEach(sub => sub.remove());
  };
}, []);
```

#### 4. 이벤트 리스너 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleAppOpenAd.addEventListeners()` 메서드는 다음 이벤트를 지원합니다:

| 이벤트            | 설명            | 파라미터                       |
| -------------- | ------------- | -------------------------- |
| `onLoaded`     | 광고 로드 성공 시 호출 | 없음                         |
| `onLoadFailed` | 광고 로드 실패 시 호출 | `AdWhaleAppOpenErrorEvent` |
| `onShowed`     | 광고 표시 성공 시 호출 | 없음                         |
| `onShowFailed` | 광고 표시 실패 시 호출 | `AdWhaleAppOpenErrorEvent` |
| `onDismissed`  | 광고가 닫힐 때 호출   | 없음                         |
| `onClicked`    | 광고가 클릭될 때 호출  | 없음                         |

#### 5. 에러 이벤트 구조

```typescript
interface AdWhaleAppOpenErrorEvent {
  statusCode: number;
  message: string;
}
```

#### 6. 옵션 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`loadAd` 메서드 호출 시 추가 옵션을 설정할 수 있습니다.

```typescript
AdWhaleAppOpenAd.loadAd(placementUid, {
  placementName?: string;  // Placement 이름
  region?: string;          // 지역 코드 (예: 'KR')
  gcoder?: {                // 위치 정보
    lt: number;             // 위도
    lng: number;            // 경도
  }
});

```

#### 7. 앱 오프닝 광고 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

다음은 React Native 컴포넌트에서 앱오프닝 광고를 구현하는 완전한 예시입니다.

```typescript
import React, { useEffect, useState } from 'react';
import { AppState, View, Text, Button } from 'react-native';
import { 
  AdWhaleAppOpenAd, 
  AdWhaleAppOpenErrorEvent,
  AdWhaleMediationSdk 
} from 'adwhale-sdk-react-native';

const PLACEMENT_UID = 'your-placement-uid';

const AppOpenAdExample: React.FC = () => {
  const [isLoaded, setIsLoaded] = useState(false);
  const [appState, setAppState] = useState(AppState.currentState);

  useEffect(() => {
    // SDK 초기화
    AdWhaleMediationSdk.initialize()
      .then(code => {
        if (code === 100) {
          console.log('SDK 초기화 성공');
        }
      })
      .catch(err => {
        console.error('SDK 초기화 에러:', err);
      });

    // 이벤트 리스너 등록
    const subscriptions = AdWhaleAppOpenAd.addEventListeners({
      onLoaded: () => {
        console.log('앱오프닝 광고 로드 성공');
        setIsLoaded(true);
      },
      onLoadFailed: (event: AdWhaleAppOpenErrorEvent) => {
        console.log('앱오프닝 광고 로드 실패:', event.statusCode, event.message);
        setIsLoaded(false);
      },
      onShowed: () => {
        console.log('앱오프닝 광고 표시됨');
      },
      onShowFailed: (event: AdWhaleAppOpenErrorEvent) => {
        console.log('앱오프닝 광고 표시 실패:', event.statusCode, event.message);
        setIsLoaded(false);
      },
      onDismissed: () => {
        console.log('앱오프닝 광고 닫힘');
        setIsLoaded(false);
      },
      onClicked: () => {
        console.log('앱오프닝 광고 클릭됨');
      },
    });

    // 앱 상태 변화 감지
    const subscription = AppState.addEventListener('change', nextAppState => {
      if (
        appState.match(/inactive|background/) &&
        nextAppState === 'active'
      ) {
        // 백그라운드에서 포그라운드로 전환 시 광고 표시
        if (isLoaded) {
          AdWhaleAppOpenAd.showAd();
        }
      }
      setAppState(nextAppState);
    });

    // 초기 광고 로드
    AdWhaleAppOpenAd.loadAd(PLACEMENT_UID);

    return () => {
      subscriptions.forEach(sub => sub.remove());
      subscription.remove();
    };
  }, [appState, isLoaded]);

  const handleLoadAd = () => {
    AdWhaleAppOpenAd.loadAd(PLACEMENT_UID);
  };

  const handleShowAd = () => {
    if (isLoaded) {
      AdWhaleAppOpenAd.showAd();
    } else {
      console.log('광고가 로드되지 않았습니다.');
    }
  };

  return (
    <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
      <Text>AdWhale 앱오프닝 광고 예제</Text>
      <Button title="광고 로드" onPress={handleLoadAd} />
      {isLoaded && (
        <Button title="광고 표시" onPress={handleShowAd} />
      )}
    </View>
  );
};

export default AppOpenAdExample;
```

#### 8. 앱 시작 시 자동 표시 예시 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

앱이 시작될 때 자동으로 앱오프닝 광고를 표시하는 예시입니다.

```typescript
import React, { useEffect, useRef } from 'react';
import { AppState } from 'react-native';
import { AdWhaleAppOpenAd, AdWhaleMediationSdk } from 'adwhale-sdk-react-native';

const PLACEMENT_UID = 'your-placement-uid';

const App: React.FC = () => {
  const isAdLoaded = useRef(false);
  const appState = useRef(AppState.currentState);

  useEffect(() => {
    // SDK 초기화
    AdWhaleMediationSdk.initialize()
      .then(() => {
        // 이벤트 리스너 등록
        AdWhaleAppOpenAd.addEventListeners({
          onLoaded: () => {
            isAdLoaded.current = true;
            // 앱이 활성 상태일 때만 광고 표시
            if (AppState.currentState === 'active') {
              AdWhaleAppOpenAd.showAd();
            }
          },
          onDismissed: () => {
            isAdLoaded.current = false;
            // 광고가 닫힌 후 다음 광고 로드
            AdWhaleAppOpenAd.loadAd(PLACEMENT_UID);
          },
        });

        // 초기 광고 로드
        AdWhaleAppOpenAd.loadAd(PLACEMENT_UID);
      });

    // 앱 상태 변화 감지
    const subscription = AppState.addEventListener('change', nextAppState => {
      if (
        appState.current.match(/inactive|background/) &&
        nextAppState === 'active' &&
        isAdLoaded.current
      ) {
        AdWhaleAppOpenAd.showAd();
      }
      appState.current = nextAppState;
    });

    return () => {
      subscription.remove();
    };
  }, []);

  return (
    // 앱 컴포넌트 내용
  );
};
```

#### 9. 주의사항 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

#### 광고 로드 타이밍 <a href="#id-1____385" id="id-1____385"></a>

* 광고는 로드가 완료된 후에만 표시할 수 있습니다.
* `onLoaded` 이벤트가 발생한 후에 `showAd()`를 호출해야 합니다.

#### 광고 표시 조건 <a href="#id-2____390" id="id-2____390"></a>

* 앱이 포그라운드 상태일 때만 광고를 표시해야 합니다.
* `AppState`를 확인하여 앱이 활성 상태일 때만 광고를 표시하세요.

#### 리스너 정리 <a href="#id-3___395" id="id-3___395"></a>

* 컴포넌트가 언마운트될 때 등록한 이벤트 리스너를 반드시 제거해야 합니다.

#### 에러 처리 <a href="#id-4___400" id="id-4___400"></a>

* `onLoadFailed`와 `onShowFailed` 이벤트에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.

#### 테스트 <a href="#id-6__410" id="id-6__410"></a>

* 개발 환경에서는 테스트용 placement UID를 사용하세요.
* 실제 배포 전에 다양한 시나리오에서 테스트하세요.
