# 앱 내 화면 전환

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
* Activity `onResume`에 대응하는 `resume()` API 제공

#### 2. 기본 구현 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

전환 팝업 광고를 로드하고 표시하는 기본적인 구현 방법입니다.

```typescript
import { Platform } from 'react-native';
import {
  AdWhaleTransitionPopupAd,
  type AdWhaleTransitionPopupAdClosedEvent,
  type AdWhaleTransitionPopupAdErrorEvent,
} from 'adwhale-sdk-react-native';

const PLACEMENT_UID = 'your-transition-popup-placement-uid';

let isTransitionPopupLoaded = false;

AdWhaleTransitionPopupAd.addEventListeners({
  onLoaded: () => {
    console.log('전환 팝업 광고 로드 성공');
    isTransitionPopupLoaded = true;
  },
  onLoadFailed: (event: AdWhaleTransitionPopupAdErrorEvent) => {
    console.log('전환 팝업 광고 로드 실패:', event.statusCode, event.message);
    isTransitionPopupLoaded = false;
  },
  onShowed: () => {
    console.log('전환 팝업 광고 표시됨');
    isTransitionPopupLoaded = false;
  },
  onShowFailed: (event: AdWhaleTransitionPopupAdErrorEvent) => {
    console.log('전환 팝업 광고 표시 실패:', event.statusCode, event.message);
    isTransitionPopupLoaded = false;
  },
  onClosed: (event: AdWhaleTransitionPopupAdClosedEvent) => {
    console.log('전환 팝업 광고 닫힘:', event.reasonMessage);
  },
  onClicked: () => {
    console.log('전환 팝업 광고 클릭됨');
  },
});

function loadTransitionPopup() {
  if (Platform.OS !== 'android') return;

  AdWhaleTransitionPopupAd.loadAd(PLACEMENT_UID, {
    region: '서울시 서초구',
    gcoder: { lt: 37.49, lng: 127.02 },
    placementName: 'transition-popup-main',
  });
}

// 광고 표시 (화면 전환 직전 등, 로드 완료 후)
function showTransitionPopup() {
  if (Platform.OS !== 'android' || !isTransitionPopupLoaded) return;
  AdWhaleTransitionPopupAd.showAd();
}
```

#### 3. 이벤트 리스너 등록

광고의 상태 변화를 추적하기 위해 이벤트 리스너를 등록합니다.

| 이벤트            | 설명            | 파라미터                            |
| -------------- | ------------- | ------------------------------- |
| `onLoaded`     | 광고 로드 성공 시 호출 | 없음                              |
| `onLoadFailed` | 광고 로드 실패 시 호출 | `AdWhaleExitPopupAdClosedEvent` |
| `onShowed`     | 광고 표시 성공 시 호출 | 없음                              |
| `onShowFailed` | 광고 표시 실패 시 호출 | `AdWhaleExitPopupAdClosedEvent` |
| `onClosed`     | 광고가 닫힐 때 호출   | `String reason`                 |
| `onClicked`    | 광고가 클릭될 때 호출  | 없음                              |

#### 4. 옵션 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleTransitionPopupAd.loadAd()` 호출 시 두 번째 인자로 추가 옵션을 설정할 수 있습니다.

```typescript
import { AdWhaleTransitionPopupAd } from 'adwhale-sdk-react-native';

AdWhaleTransitionPopupAd.loadAd('your-placement-uid', {
  region: '서울시 서초구',                    // 지역 정보
  gcoder: { lt: 37.49, lng: 127.02 },      // 위치 정보 (위도, 경도)
  placementName: 'transition-popup-main',  // Placement 이름
});
```

#### **5. Activity resume**

네이티브 `AdWhaleMediationTransitionPopupAd.resume(Activity)`는 Activity가 foreground로 올라올 때 필요합니다. React Native SDK에서는 `AppState`가 `active`로 바뀔 때 `AdWhaleTransitionPopupAd.resume()`을 호출하세요.

```typescript
import { useEffect } from 'react';
import { AppState, Platform } from 'react-native';
import { AdWhaleTransitionPopupAd } from 'adwhale-sdk-react-native';

useEffect(() => {
  if (Platform.OS !== 'android') return;

  const subscription = AppState.addEventListener('change', nextState => {
    if (nextState === 'active') {
      AdWhaleTransitionPopupAd.resume();
    }
  });

  return () => subscription.remove();
}, []);
```

#### **5. 전환 팝업 샘플코드**

다음은 React Native 컴포넌트에서 전환 팝업 광고를 구현하는 완전한 예시입니다.

```typescript
import React, { useEffect, useState } from 'react';
import {
  Platform,
  SafeAreaView,
  Text,
  TouchableOpacity,
  View,
} from 'react-native';
import {
  AdWhaleTransitionPopupAd,
  AdWhaleMediationAds,
  type AdWhaleTransitionPopupAdClosedEvent,
  type AdWhaleTransitionPopupAdErrorEvent,
} from 'adwhale-sdk-react-native';

const TransitionPopupAdScreen: React.FC<{
  onNavigateNext: () => void;
}> = ({ onNavigateNext }) => {
  const [isLoaded, setIsLoaded] = useState(false);

  const loadTransitionPopup = () => {
    if (Platform.OS !== 'android') return;

    AdWhaleTransitionPopupAd.destroy();
    setIsLoaded(false);

    AdWhaleTransitionPopupAd.loadAd('your-placement-uid', {
      region: '서울시 서초구',
      gcoder: { lt: 37.49, lng: 127.02 },
      placementName: 'test_transition_popup',
    });
  };

  useEffect(() => {
    if (Platform.OS !== 'android') return;

    AdWhaleMediationAds.initialize().then(result => {
      if (result.statusCode === 100) {
        console.log('SDK 초기화 성공:', result.message);
        loadTransitionPopup();
      } else {
        console.log('SDK 초기화 실패:', result.statusCode, result.message);
      }
    });

    const subscriptions = AdWhaleTransitionPopupAd.addEventListeners({
      onLoaded: () => {
        console.log('전환 팝업 광고 로드 성공');
        setIsLoaded(true);
      },
      onLoadFailed: (event: AdWhaleTransitionPopupAdErrorEvent) => {
        console.log('전환 팝업 광고 로드 실패:', event.statusCode, event.message);
        setIsLoaded(false);
      },
      onShowed: () => {
        console.log('전환 팝업 광고 표시됨');
        setIsLoaded(false);
      },
      onShowFailed: (event: AdWhaleTransitionPopupAdErrorEvent) => {
        console.log('전환 팝업 광고 표시 실패:', event.statusCode, event.message);
        setIsLoaded(false);
      },
      onClosed: (event: AdWhaleTransitionPopupAdClosedEvent) => {
        console.log('전환 팝업 광고 닫힘:', event.reasonMessage);
        loadTransitionPopup(); // 다음 노출을 위해 재로드
      },
      onClicked: () => {
        console.log('전환 팝업 광고 클릭됨');
      },
    });

    return () => {
      subscriptions.forEach(s => s.remove());
      AdWhaleTransitionPopupAd.destroy();
    };
  }, []);

  const showTransitionPopup = () => {
    if (Platform.OS !== 'android' || !isLoaded) return;
    AdWhaleTransitionPopupAd.showAd();
  };

  const goToNextScreen = () => {
    // 화면 전환 직전에 광고 표시
    showTransitionPopup();
    onNavigateNext();
  };

  if (Platform.OS !== 'android') {
    return (
      <SafeAreaView>
        <Text>전환 팝업 광고는 Android 전용입니다.</Text>
      </SafeAreaView>
    );
  }

  return (
    <SafeAreaView>
      <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
        <TouchableOpacity onPress={loadTransitionPopup}>
          <Text>광고 로드</Text>
        </TouchableOpacity>
        <TouchableOpacity
          onPress={showTransitionPopup}
          disabled={!isLoaded}
          style={{ marginTop: 16 }}>
          <Text>광고 표시</Text>
        </TouchableOpacity>
        <TouchableOpacity
          onPress={goToNextScreen}
          disabled={!isLoaded}
          style={{ marginTop: 16 }}>
          <Text>다음 화면으로 (표시 후 이동)</Text>
        </TouchableOpacity>
      </View>
    </SafeAreaView>
  );
};

export default TransitionPopupAdScreen;
```

#### **6. 주의사항**

#### **광고 로드 타이밍**

* 광고는 로드가 완료된 후에만 표시할 수 있습니다.
* `onLoaded` 이벤트가 발생한 후에 `showAd()`를 호출해야 합니다.
* SDK 초기화(`AdWhaleMediationAds.initialize()`, `statusCode === 100`) 성공 후 `loadAd()`를 호출하세요.
* `loadAd()`는 Android에서만 네이티브 로드를 수행합니다.

#### **광고 표시 조건**

* **화면 전환 직전** (`navigation.navigate` / `push` 전), 사용자 액션 직후 등 자연스러운 타이밍에 표시하세요.
* 로드되지 않은 상태에서 `showAd()`를 호출하면 표시되지 않을 수 있습니다.
* 표시 시 Activity가 `ReactActivity`(또는 `FragmentActivity`를 상속한 Activity)여야 합니다.

#### **플랫폼**

* **Android 전용**입니다. iOS에서는 `Platform.OS === 'android'` 분기로 호출을 막으세요.

#### **리스너 정리**

* 컴포넌트가 언마운트될 때 `addEventListeners`로 등록한 구독을 `remove()`하세요.
* 더 이상 사용하지 않을 때 `destroy()`를 호출해 리소스를 해제하세요.

#### **에러 처리**

* `onLoadFailed`와 `onShowFailed` 이벤트에서 적절한 에러 처리를 구현하세요.
* `onShowFailed` 시 네이티브 인스턴스가 정리될 수 있으므로, 필요 시 `loadAd()`를 다시 호출하세요.
* `onClosed`의 `reasonId`, `reasonMessage`로 사용자가 종료·취소했는지 분기할 수 있습니다.

#### **테스트**

* 개발 환경에서는 테스트용 Placement UID를 사용하세요.
