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
import { Platform } from 'react-native';
import {
  AdWhaleExitPopupAd,
  type AdWhaleExitPopupAdClosedEvent,
  type AdWhaleExitPopupAdErrorEvent,
} from 'adwhale-sdk-react-native';

const PLACEMENT_UID = 'your-exit-popup-placement-uid';

let isExitPopupAdLoaded = false;

AdWhaleExitPopupAd.addEventListeners({
  onLoaded: () => {
    console.log('종료 팝업 광고 로드 성공');
    isExitPopupAdLoaded = true;
  },
  onLoadFailed: (event: AdWhaleExitPopupAdErrorEvent) => {
    console.log('종료 팝업 광고 로드 실패:', event.statusCode, event.message);
    isExitPopupAdLoaded = false;
  },
  onShowed: () => {
    console.log('종료 팝업 광고 표시됨');
    isExitPopupAdLoaded = false;
  },
  onShowFailed: (event: AdWhaleExitPopupAdErrorEvent) => {
    console.log('종료 팝업 광고 표시 실패:', event.statusCode, event.message);
    isExitPopupAdLoaded = false;
  },
  onClosed: (event: AdWhaleExitPopupAdClosedEvent) => {
    console.log('종료 팝업 광고 닫힘:', event.reasonMessage);
    isExitPopupAdLoaded = false;
  },
  onClicked: () => {
    console.log('종료 팝업 광고 클릭됨');
  },
});

function loadExitPopup() {
  if (Platform.OS !== 'android') return;

  AdWhaleExitPopupAd.loadAd(PLACEMENT_UID, {
    primaryButtonText: '취소',
    secondaryButtonText: '종료',
    descriptionText: '앱을 종료하시겠습니까?',
  });
}

// 광고 표시 (로드 완료 후)
function showExitPopup() {
  if (Platform.OS !== 'android' || !isExitPopupAdLoaded) return;
  AdWhaleExitPopupAd.showAd();
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

`AdWhaleExitPopupAd.loadAd()` 호출 시 두 번째 인자로 추가 옵션을 설정할 수 있습니다.

```typescript
import { AdWhaleExitPopupAd } from 'adwhale-sdk-react-native';

AdWhaleExitPopupAd.loadAd('your-placement-uid', {
  primaryButtonText: '취소',              // 주 버튼 문구
  secondaryButtonText: '종료',            // 보조 버튼 문구
  descriptionText: '앱을 종료하시겠습니까?', // 설명 문구
  region: '서울시 서초구',                 // 지역 정보
  gcoder: { lt: 37.49, lng: 127.02 },   // 위치 정보 (위도, 경도)
  placementName: 'exit-popup-main',      // Placement 이름
});
```

#### **5. 종료 팝업 샘플코드**

다음은 React Native 컴포넌트에서 종료 팝업 광고를 구현하는 완전한 예시입니다. Android **뒤로 가기** 시 광고를 표시하고, 연속 뒤로 가기로 `showAd()` 가 중복 호출되지 않도록 처리합니다.

```typescript
import React, { useEffect, useRef, useState } from 'react';
import {
  BackHandler,
  Platform,
  SafeAreaView,
  Text,
  TouchableOpacity,
  View,
} from 'react-native';
import {
  AdWhaleExitPopupAd,
  AdWhaleMediationAds,
  type AdWhaleExitPopupAdClosedEvent,
  type AdWhaleExitPopupAdErrorEvent,
} from 'adwhale-sdk-react-native';

const ExitPopupAdScreen: React.FC = () => {
  const [isLoaded, setIsLoaded] = useState(false);
  const exitPopupAdGuardRef = useRef(false);

  const loadExitPopup = () => {
    if (Platform.OS !== 'android') return;

    AdWhaleExitPopupAd.loadAd('your-placement-uid', {
      primaryButtonText: '취소',
      secondaryButtonText: '종료',
      descriptionText: '앱을 종료하시겠습니까?',
      region: '서울시 서초구',
      gcoder: { lt: 37.49, lng: 127.02 },
      placementName: 'test_exit_popup',
    });
  };

  useEffect(() => {
    if (Platform.OS !== 'android') return;

    AdWhaleMediationAds.initialize().then(result => {
      if (result.statusCode === 100) {
        console.log('SDK 초기화 성공:', result.message);
        loadExitPopup();
      } else {
        console.log('SDK 초기화 실패:', result.statusCode, result.message);
      }
    });

    const subscriptions = AdWhaleExitPopupAd.addEventListeners({
      onLoaded: () => {
        console.log('종료 팝업 광고 로드 성공');
        setIsLoaded(true);
      },
      onLoadFailed: (event: AdWhaleExitPopupAdErrorEvent) => {
        console.log('종료 팝업 광고 로드 실패:', event.statusCode, event.message);
        setIsLoaded(false);
      },
      onShowed: () => {
        console.log('종료 팝업 광고 표시됨');
        setIsLoaded(false);
      },
      onShowFailed: (event: AdWhaleExitPopupAdErrorEvent) => {
        console.log('종료 팝업 광고 표시 실패:', event.statusCode, event.message);
        setIsLoaded(false);
      },
      onClosed: (event: AdWhaleExitPopupAdClosedEvent) => {
        console.log('종료 팝업 광고 닫힘:', event.reasonMessage);
        setIsLoaded(false);
        loadExitPopup(); // 다음 노출을 위해 재로드
      },
      onClicked: () => {
        console.log('종료 팝업 광고 클릭됨');
      },
    });

    return () => {
      subscriptions.forEach(s => s.remove());
      AdWhaleExitPopupAd.destroy();
    };
  }, []);

  useEffect(() => {
    if (Platform.OS !== 'android') return;

    const onHardwareBackPress = (): boolean => {
      if (exitPopupAdGuardRef.current) return true;
      if (!isLoaded) return false;

      exitPopupAdGuardRef.current = true;
      try {
        AdWhaleExitPopupAd.showAd();
      } catch (e) {
        console.warn('showAd failed:', e);
        exitPopupAdGuardRef.current = false;
        return false;
      }

      setTimeout(() => {
        exitPopupAdGuardRef.current = false;
      }, 2000);

      return true; // 팝업 표시 중 뒤로 가기 기본 동작 차단
    };

    const sub = BackHandler.addEventListener(
      'hardwareBackPress',
      onHardwareBackPress,
    );
    return () => sub.remove();
  }, [isLoaded]);

  if (Platform.OS !== 'android') {
    return (
      <SafeAreaView>
        <Text>종료 팝업 광고는 Android 전용입니다.</Text>
      </SafeAreaView>
    );
  }

  return (
    <SafeAreaView>
      <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
        <Text>
          {isLoaded ? '광고 로드 완료 (뒤로 가기로 표시)' : '광고 로드 대기 중'}
        </Text>
        <TouchableOpacity onPress={loadExitPopup} style={{ marginTop: 16 }}>
          <Text>광고 다시 로드</Text>
        </TouchableOpacity>
      </View>
    </SafeAreaView>
  );
};

export default ExitPopupAdScreen;
```

#### **6. 주의사항**

#### **광고 로드 타이밍**

* 광고는 로드가 완료된 후에만 표시할 수 있습니다.
* `onLoaded` 이벤트가 발생한 후에 `showAd()`를 호출해야 합니다.
* SDK 초기화(`AdWhaleMediationAds.initialize()`, `statusCode === 100`) 성공 후 `loadAd()`를 호출하세요.

#### **광고 표시 조건**

* Android **뒤로 가기** 등 앱 종료 직전 등 자연스러운 타이밍에 표시하세요.
* 연속 뒤로 가기로 `showAd()`가 중복 호출되지 않도록 가드 플래그(`exitPopupAdGuardRef`)를 두는 것을 권장합니다.
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
