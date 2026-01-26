# 배너

{% hint style="info" %}
배너 광고는 앱 화면의 특정 영역에 표시되는 직사각형 광고입니다. 사용자가 앱을 사용하는 동안 지속적으로 노출되어 높은 노출 빈도를 제공합니다.
{% endhint %}

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
import { AdWhaleAdView, AdWhaleAdSize } from 'adwhale-sdk-react-native';

const BannerAdExample: React.FC = () => {
  const [isLoaded, setIsLoaded] = useState(false);

  return (
    <View style={styles.container}>
      <AdWhaleAdView
        style={styles.banner}
        placementUid="your-placement-uid"
        adSize={AdWhaleAdSize.BANNER_320x50}
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

<table><thead><tr><th width="181.7578125">사이즈</th><th width="283.515625">값</th><th width="146.29296875">사이즈 별 높이</th><th>설명</th></tr></thead><tbody><tr><td>320x50</td><td><code>AdWhaleAdSize.BANNER_320x50</code></td><td>50</td><td>표준 배너 (Banner)</td></tr><tr><td>320x100</td><td><code>AdWhaleAdSize.BANNER_320x100</code></td><td>100</td><td>큰 배너 (Large Banner)</td></tr><tr><td>300x250</td><td><code>AdWhaleAdSize.BANNER_300x250</code></td><td>250</td><td>중간 직사각형 (Medium Rectangle)</td></tr><tr><td>250x250</td><td><code>AdWhaleAdSize.BANNER_250x250</code></td><td>250</td><td>정사각형 (Square)</td></tr><tr><td>ADAPTIVE_ANCHOR</td><td><code>AdWhaleAdSize.ADAPTIVE_ANCHOR</code></td><td>60(대략적인 높이)</td><td>적응형 앵커 배너</td></tr></tbody></table>

**사용예시**

```typescript
import { AdWhaleAdView, AdWhaleAdSize } from 'adwhale-sdk-react-native';

// 표준 배너
<AdWhaleAdView
  placementUid={PLACEMENT_UID}
  adSize={AdWhaleAdSize.BANNER_320x50}
  loadAd={true}
/>

// 큰 배너
<AdWhaleAdView
  placementUid={PLACEMENT_UID}
  adSize={AdWhaleAdSize.BANNER_320x100}
  loadAd={true}
/>

// 적응형 배너 (너비 지정)
<AdWhaleAdView
  placementUid={PLACEMENT_UID}
  adSize={AdWhaleAdSize.ADAPTIVE_ANCHOR}
  adaptiveAnchorWidth={360} // 디바이스 너비 값 예시
  loadAd={true}
/>

// 적응형 배너 (디바이스 전체 너비)
<AdWhaleAdView
  placementUid={PLACEMENT_UID}
  adSize={AdWhaleAdSize.ADAPTIVE_ANCHOR}
  adaptiveAnchorWidth={0} // 0을 주거나, 아예 호출하지 않으면 디바이스 전체 너비를 자동으로 감지
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
import { AdWhaleAdView, AdWhaleAdSize } from 'adwhale-sdk-react-native';

// 기본 옵션
<AdWhaleAdView
  placementUid="your-placement-uid"
  adSize={AdWhaleAdSize.BANNER_320x50}
  loadAd={true}
/>

// 모든 옵션 포함
<AdWhaleAdView
  placementUid="your-placement-uid"
  adSize={AdWhaleAdSize.BANNER_320x50}
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

// 적응형 배너 (너비 지정)
<AdWhaleAdView
  placementUid="your-placement-uid"
  adSize={AdWhaleAdSize.ADAPTIVE_ANCHOR}
  adaptiveAnchorWidth={360}
  loadAd={true}
/>
```

#### 7. 배너 광고 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

다음은 React Native 컴포넌트에서 배너 광고를 구현하는 완전한 예시입니다.

```typescript
import React, { useState, useEffect } from 'react';
import { View, Text, StyleSheet, ScrollView } from 'react-native';
import { 
  AdWhaleAdView, 
  AdWhaleAdSize,
  AdWhaleMediationAds 
} from 'adwhale-sdk-react-native';

const PLACEMENT_UID = 'your-placement-uid';

const BannerAdScreen: React.FC = () => {
  const [isLoaded, setIsLoaded] = useState(false);
  const [loadAd, setLoadAd] = useState(false);

  useEffect(() => {
    // SDK 초기화
    AdWhaleMediationAds.initialize()
      .then(result => {
        if (result.isSuccess) {
          console.log('SDK 초기화 성공:', result.message);
          // SDK 초기화 후 광고 로드 시작
          setLoadAd(true);
        } else {
          console.log('SDK 초기화 실패:', result.statusCode, result.message);
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
          adSize={AdWhaleAdSize.BANNER_320x50}
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

export default BannerAdScreen;
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
