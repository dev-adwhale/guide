# 배너

#### 1. 주요특징 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

* 다양한 사이즈 지원 (320x50, 320x100, 300x250, 250x250, ADAPTIVE\_ANCHOR)
* 자동 로드 및 새로고침 기능
* 간단한 컴포넌트 기반 구현
* 이벤트 기반 콜백으로 광고 상태 추적

#### 2. 기본 구현 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

배너 광고는 `AdWhaleAdView` 컴포넌트를 사용하여 구현합니다.

```typescript
import React, { useState } from 'react';
import { View, StyleSheet } from 'react-native';
import { AdWhaleAdView } from 'adwhale-sdk-react-native';

const BannerAdExample: React.FC = () => {
  const [isLoaded, setIsLoaded] = useState(false);

  return (
    <View style={styles.container}>
      <AdWhaleAdView
        style={styles.banner}
        placementUid="your-placement-uid"
        adSize="320x50"
        loadAd={true}
        onAdLoaded={() => {
          console.log('배너 광고 로드 성공');
          setIsLoaded(true);
        }}
        onAdLoadFailed={(event) => {
          console.log('배너 광고 로드 실패:', event.statusCode, event.message);
          setIsLoaded(false);
        }}
        onAdClicked={() => {
          console.log('배너 광고 클릭됨');
        }}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    width: '100%',
    alignItems: 'center',
    justifyContent: 'center',
  },
  banner: {
    width: '100%',
    height: 50,
  },
});

export default BannerAdExample;
```

#### 3. 배너 사이즈 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

배너 광고는 다음 사이즈를 지원합니다:

| 사이즈              | 값                   | 설명                         |
| ---------------- | ------------------- | -------------------------- |
| 320x50           | `'320x50'`          | 표준 배너 (Banner)             |
| 320x100          | `'320x100'`         | 큰 배너 (Large Banner)        |
| 300x250          | `'300x250'`         | 중간 직사각형 (Medium Rectangle) |
| 250x250          | `'250x250'`         | 정사각형 (Square)              |
| ADAPTIVE\_ANCHOR | `'ADAPTIVE_ANCHOR'` | 적응형 앵커 배너                  |

**사용예시**

```typescript
// 표준 배너
<AdWhaleAdView
  placementUid={PLACEMENT_UID}
  adSize="320x50"
  loadAd={true}
/>

// 큰 배너
<AdWhaleAdView
  placementUid={PLACEMENT_UID}
  adSize="320x100"
  loadAd={true}
/>

// 적응형 배너
<AdWhaleAdView
  placementUid={PLACEMENT_UID}
  adSize="ADAPTIVE_ANCHOR"
  loadAd={true}
/>
```

#### 4. 이벤트 리스너 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleAdView` 컴포넌트는 다음 이벤트를 지원합니다:

| 이벤트              | 설명            | 파라미터                 |
| ---------------- | ------------- | -------------------- |
| `onAdLoaded`     | 광고 로드 성공 시 호출 | 없음                   |
| `onAdLoadFailed` | 광고 로드 실패 시 호출 | `AdWhaleBannerError` |
| `onAdClicked`    | 광고 클릭 시 호출    | 없음                   |

#### 5. 에러 이벤트 구조

```typescript
interface AdWhaleBannerError {
  statusCode: number;
  message: string;
}
```

#### 6. 옵션 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleAdView` 컴포넌트 호출 시 추가 옵션을 설정할 수 있습니다.

```typescript
// 기본 옵션
<AdWhaleAdView
  placementUid="your-placement-uid"
  adSize="320x50"
  loadAd={true}
/>

// 모든 옵션 포함
<AdWhaleAdView
  placementUid="your-placement-uid"
  adSize="320x50"
  loadAd={true}
  placementName="main-banner"
  region="KR"
  gcoder={{
    lt: 37.5665,
    lng: 126.9780
  }}
  style={{ width: '100%', height: 50 }}
  onAdLoaded={() => console.log('로드 성공')}
  onAdLoadFailed={(e) => console.log('로드 실패', e)}
  onAdClicked={() => console.log('클릭됨')}
/>
```

#### 7. 배너 광고 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

다음은 React Native 컴포넌트에서 배너 광고를 구현하는 완전한 예시입니다.

```typescript
import React, { useState, useEffect } from 'react';
import { View, Text, StyleSheet, ScrollView } from 'react-native';
import { 
  AdWhaleAdView, 
  AdWhaleMediationAds 
} from 'adwhale-sdk-react-native';

const PLACEMENT_UID = 'your-placement-uid';

const BannerAdExample: React.FC = () => {
  const [isLoaded, setIsLoaded] = useState(false);
  const [loadAd, setLoadAd] = useState(false);

  useEffect(() => {
    // SDK 초기화
    AdWhaleMediationAds.initialize()
      .then(code => {
        if (code === 100) {
          console.log('SDK 초기화 성공');
          // SDK 초기화 후 광고 로드 시작
          setLoadAd(true);
        }
      })
      .catch(err => {
        console.error('SDK 초기화 에러:', err);
      });
  }, []);

  return (
    <ScrollView style={styles.container}>
      <View style={styles.content}>
        <Text style={styles.title}>배너 광고 예제</Text>
        <Text style={styles.description}>
          화면 하단에 배너 광고가 표시됩니다.
        </Text>
      </View>

      {/* 배너 광고 영역 */}
      <View style={styles.bannerContainer}>
        <AdWhaleAdView
          style={styles.banner}
          placementUid={PLACEMENT_UID}
          adSize="320x50"
          loadAd={loadAd}
          onAdLoaded={() => {
            console.log('배너 광고 로드 성공');
            setIsLoaded(true);
          }}
          onAdLoadFailed={(event) => {
            console.log('배너 광고 로드 실패:', event.statusCode, event.message);
            setIsLoaded(false);
          }}
          onAdClicked={() => {
            console.log('배너 광고 클릭됨');
          }}
        />
      </View>
    </ScrollView>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: 'white',
  },
  content: {
    padding: 20,
    minHeight: 400,
  },
  title: {
    fontSize: 24,
    fontWeight: 'bold',
    marginBottom: 10,
  },
  description: {
    fontSize: 16,
    color: '#666',
  },
  bannerContainer: {
    width: '100%',
    alignItems: 'center',
    justifyContent: 'center',
    paddingVertical: 10,
    backgroundColor: '#f5f5f5',
  },
  banner: {
    width: '100%',
    height: 50,
  },
});

export default BannerAdExample;
```

#### 8. 주의사항 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

#### 광고 로드 타이밍

* `loadAd` prop을 `true`로 설정해야 광고가 로드됩니다.
* SDK 초기화가 완료된 후에 광고를 로드하는 것을 권장합니다.

#### 컴포넌트 재생성 <a href="#id-2___489" id="id-2___489"></a>

* 배너 사이즈나 placement UID를 변경할 때는 컴포넌트를 재생성해야 합니다.
* `key` prop을 사용하여 컴포넌트를 강제로 재생성할 수 있습니다.

#### 에러 처리 <a href="#id-4___499" id="id-4___499"></a>

* `onAdLoadFailed` 이벤트에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.

#### 성능 최적화 <a href="#id-5___504" id="id-5___504"></a>

* 배너 광고는 자동으로 새로고침되므로, 불필요한 리렌더링을 방지하세요.

#### 테스트 <a href="#id-6__509" id="id-6__509"></a>

* 개발 환경에서는 테스트용 placement UID를 사용하세요.
