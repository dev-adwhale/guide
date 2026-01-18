# 네이티브

{% hint style="info" %}
네이티브 광고는 앱의 콘텐츠와 자연스럽게 어우러지는 광고 형식입니다. 앱의 디자인과 일치하도록 커스터마이징할 수 있어 사용자 경험을 해치지 않으면서 높은 참여도를 얻을 수 있습니다.
{% endhint %}

#### 1. 주요특징 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

* 앱 콘텐츠와 자연스럽게 통합
* 템플릿 기반 또는 커스텀 레이아웃 지원
* 다양한 템플릿 타입 (SMALL, MEDIUM, FULLSCREEN)
* 스타일 커스터마이징 가능

#### 2. 네이티브 광고 타입 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

네이티브 광고는 두 가지 방식으로 구현할 수 있습니다:

{% hint style="info" %}
**템플릿 네이티브 광고** (`AdWhaleNativeTemplateView`)

* SDK에서 제공하는 템플릿 사용
* SMALL, MEDIUM, FULLSCREEN 타입 지원
* 스타일 커스터마이징 가능
{% endhint %}

{% hint style="info" %}
**커스텀 네이티브 광고** (`AdWhaleNativeCustomView`)

* 자체 레이아웃 사용
* 완전한 디자인 제어
{% endhint %}

#### 3. 템플릿 네이티브 광고 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

템플릿 네이티브 광고는 다음 세 가지 타입을 지원합니다:

| 타입         | 값              | 설명             |
| ---------- | -------------- | -------------- |
| SMALL      | `'SMALL'`      | 작은 크기의 네이티브 광고 |
| MEDIUM     | `'MEDIUM'`     | 중간 크기의 네이티브 광고 |
| FULLSCREEN | `'FULLSCREEN'` | 전체 화면 네이티브 광고  |

**기본 구현**

```typescript
import React, { useRef } from 'react';
import { View, StyleSheet } from 'react-native';
import { AdWhaleNativeTemplateView, AdWhaleNativeTemplateHandle } from 'adwhale-sdk-react-native';

const NativeAdExample: React.FC = () => {
  const adRef = useRef<AdWhaleNativeTemplateHandle>(null);

  const handleLoadAd = () => {
    adRef.current?.loadAd();
  };

  const handleShowAd = () => {
    adRef.current?.showAd();
  };

  return (
    <View style={styles.container}>
      <AdWhaleNativeTemplateView
        ref={adRef}
        style={styles.adView}
        placementUid="your-placement-uid"
        template="SMALL"
        onAdLoaded={() => {
          console.log('네이티브 광고 로드 성공');
        }}
        onAdFailedToLoad={(event) => {
          console.log('네이티브 광고 로드 실패:', event.errorCode, event.errorMessage);
        }}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
  },
  adView: {
    width: '100%',
    height: 120,
  },
});

export default NativeAdExample;
```

**템플릿 스타일 커스터마이징**

템플릿 네이티브 광고의 스타일을 커스터마이징할 수 있습니다.

```typescript
import { AdWhaleNativeTemplateStyle } from 'adwhale-sdk-react-native';

const customStyle: AdWhaleNativeTemplateStyle = {
  mainBackgroundColor: '#F5F5F5',
  primaryTextTypefaceColor: '#333333',
  primaryTextSize: 18,
  secondaryTextTypefaceColor: '#666666',
  secondaryTextSize: 14,
  callToActionTypefaceColor: 'white',
  callToActionTextSize: 16,
  callToActionBackgroundColor: '#007AFF',
};

<AdWhaleNativeTemplateView
  ref={adRef}
  placementUid="your-placement-uid"
  template="MEDIUM"
  templateStyle={customStyle}
  onAdLoaded={() => console.log('로드 성공')}
/>
```

**스타일 옵션**

```typescript
interface AdWhaleNativeTemplateStyle {
  mainBackgroundColor?: string;              // 메인 배경색
  primaryTextTypefaceColor?: string;         // 주요 텍스트 색상
  primaryTextSize?: number;                  // 주요 텍스트 크기
  primaryTextBackgroundColor?: string;       // 주요 텍스트 배경색
  secondaryTextTypefaceColor?: string;       // 보조 텍스트 색상
  secondaryTextSize?: number;                // 보조 텍스트 크기
  secondaryTextBackgroundColor?: string;     // 보조 텍스트 배경색
  tertiaryTextTypefaceColor?: string;        // 3차 텍스트 색상
  tertiaryTextSize?: number;                 // 3차 텍스트 크기
  tertiaryTextBackgroundColor?: string;      // 3차 텍스트 배경색
  callToActionTypefaceColor?: string;        // CTA 버튼 텍스트 색상
  callToActionTextSize?: number;             // CTA 버튼 텍스트 크기
  callToActionBackgroundColor?: string;      // CTA 버튼 배경색
  primaryTextTypeface?: 'bold' | 'italic';   // 주요 텍스트 폰트 스타일
  secondaryTextTypeface?: 'bold' | 'italic'; // 보조 텍스트 폰트 스타일
  callToActionTextTypeface?: 'bold' | 'italic'; // CTA 버튼 폰트 스타일
}
```

#### 4. 커스텀 네이티브 광고 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

커스텀 네이티브 광고는 자체 레이아웃을 사용하여 광고를 표시합니다.

**기본 구현**

```typescript
import React, { useRef } from 'react';
import { View, StyleSheet } from 'react-native';
import { AdWhaleNativeCustomView, AdWhaleNativeCustomHandle } from 'adwhale-sdk-react-native';

const CustomNativeAdExample: React.FC = () => {
  const adRef = useRef<AdWhaleNativeCustomHandle>(null);

  const handleLoadAd = () => {
    adRef.current?.loadAd();
  };

  const handleShowAd = () => {
    adRef.current?.showAd();
  };

  return (
    <View style={styles.container}>
      <AdWhaleNativeCustomView
        ref={adRef}
        style={styles.adView}
        placementUid="your-placement-uid"
        layoutName="custom_native_ad_layout"
        onAdLoaded={() => {
          console.log('커스텀 네이티브 광고 로드 성공');
        }}
        onAdFailedToLoad={(event) => {
          console.log('커스텀 네이티브 광고 로드 실패:', event.errorCode, event.errorMessage);
        }}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
  },
  adView: {
    width: '100%',
    height: 350,
  },
});

export default CustomNativeAdExample;
```

**사용자 지정 커스텀 네이티브 레이아웃 예시(custom\_native\_ad\_layout.xml)**

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:padding="16dp"
    android:background="@android:color/white"
    android:elevation="4dp">

    <!-- 앱 아이콘 -->
    <ImageView
        android:id="@+id/view_icon"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:layout_marginEnd="12dp"
        android:scaleType="centerCrop"
        app:layout_constraintBottom_toBottomOf="@id/view_title"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="@id/view_title" />

    <!-- 제목 -->
    <TextView
        android:id="@+id/view_title"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:textSize="16sp"
        android:textStyle="bold"
        android:textColor="@android:color/black"
        android:ellipsize="end"
        android:maxLines="2"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@id/view_icon"
        app:layout_constraintTop_toTopOf="parent"
        tools:text="광고 제목입니다" />

    <!-- 설명 -->
    <TextView
        android:id="@+id/view_body"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="4dp"
        android:textSize="14sp"
        android:textColor="@android:color/darker_gray"
        android:ellipsize="end"
        android:lines="2"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="@id/view_title"
        app:layout_constraintTop_toBottomOf="@id/view_title"
        tools:text="광고 설명입니다. 이 앱을 다운로드하세요!" />

    <!-- CTA 버튼 -->
    <Button
        android:id="@+id/button_cta"
        android:layout_width="wrap_content"
        android:layout_height="36dp"
        android:layout_marginTop="8dp"
        android:textSize="12sp"
        android:backgroundTint="#009688"
        android:textColor="@android:color/white"
        android:text="설치하기"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toBottomOf="@id/view_body" />

    <!-- 미디어 뷰 -->
    <FrameLayout
        android:id="@+id/view_media"
        android:layout_width="match_parent"
        android:layout_height="300dp"
        android:layout_marginTop="12dp"
        app:layout_constraintTop_toBottomOf="@id/button_cta" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

#### 5. 이벤트 리스너 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleNativeTemplateView` 컴포넌트는 다음 이벤트를 지원합니다:

| 이벤트                | 설명            | 파라미터                         |
| ------------------ | ------------- | ---------------------------- |
| `onAdLoaded`       | 광고 로드 성공 시 호출 | 없음                           |
| `onAdFailedToLoad` | 광고 로드 실패 시 호출 | `AdWhaleNativeTemplateError` |
| `onAdShowFailed`   | 광고 표시 실패 시 호출 | `AdWhaleNativeTemplateError` |

`AdWhaleNativeCustomView` 컴포넌트는 다음 이벤트를 지원합니다:

| 이벤트                | 설명            | 파라미터                       |
| ------------------ | ------------- | -------------------------- |
| `onAdLoaded`       | 광고 로드 성공 시 호출 | 없음                         |
| `onAdFailedToLoad` | 광고 로드 실패 시 호출 | `AdWhaleNativeCustomError` |

#### 5. 에러 이벤트 구조

```typescript
interface AdWhaleNativeTemplateError {
  errorCode: number;
  errorMessage: string;
}

interface AdWhaleNativeCustomError {
  errorCode: number;
  errorMessage: string;
}
```

#### 6. 옵션 설정 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

`AdWhaleNativeTemplateView` 컴포넌트 호출 시 추가 옵션을 설정할 수 있습니다.

```typescript
<AdWhaleNativeTemplateView
  placementUid={string}                    // 필수: Placement UID
  template={AdWhaleNativeTemplateType}      // 필수: 템플릿 타입
  templateStyle?: AdWhaleNativeTemplateStyle // 템플릿 스타일
  placementName?: string;                   // Placement 이름
  region?: string;                          // 지역 코드
  gcoder?: {                                // 위치 정보
    lt: number;
    lng: number;
  }
  style?: StyleProp<ViewStyle>              // 스타일
  onAdLoaded?: () => void                   // 로드 성공 콜백
  onAdFailedToLoad?: (event) => void        // 로드 실패 콜백
  onAdShowFailed?: (event) => void          // 표시 실패 콜백
/>
```

`AdWhaleNativeCustomView` 컴포넌트 호출 시 추가 옵션을 설정할 수 있습니다.

```typescript
<AdWhaleNativeCustomView
  placementUid={string}                     // 필수: Placement UID
  layoutName={string}                       // 필수: 레이아웃 이름
  placementName?: string;                    // Placement 이름
  region?: string;                           // 지역 코드
  gcoder?: {                                // 위치 정보
    lt: number;
    lng: number;
  }
  style?: StyleProp<ViewStyle>              // 스타일
  onAdLoaded?: () => void                   // 로드 성공 콜백
  onAdFailedToLoad?: (event) => void       // 로드 실패 콜백
/>
```

#### 7. 템플릿 네이티브 광고 샘플코드 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

다음은 React Native 컴포넌트에서 템플릿 네이티브 광고를 구현하는 완전한 예시입니다.

```typescript
import React, { useRef, useState, useEffect } from 'react';
import { View, Text, Button, StyleSheet } from 'react-native';
import { 
  AdWhaleNativeTemplateView, 
  AdWhaleNativeTemplateHandle,
  AdWhaleNativeTemplateType,
  AdWhaleNativeTemplateStyle,
  AdWhaleMediationSdk 
} from 'adwhale-sdk-react-native';

const PLACEMENT_UID = 'your-placement-uid';

const TemplateNativeAdExample: React.FC = () => {
  const adRef = useRef<AdWhaleNativeTemplateHandle>(null);
  const [isLoaded, setIsLoaded] = useState(false);
  const [templateType, setTemplateType] = useState<AdWhaleNativeTemplateType>('SMALL');

  useEffect(() => {
    AdWhaleMediationSdk.initialize();
  }, []);

  const customStyle: AdWhaleNativeTemplateStyle = {
    mainBackgroundColor: '#F5F5F5',
    primaryTextTypefaceColor: '#333333',
    primaryTextSize: 18,
    callToActionTypefaceColor: 'white',
    callToActionBackgroundColor: '#007AFF',
  };

  const handleLoadAd = () => {
    adRef.current?.loadAd();
  };

  const handleShowAd = () => {
    if (isLoaded) {
      adRef.current?.showAd();
    }
  };

  const getHeight = (type: AdWhaleNativeTemplateType): number => {
    switch (type) {
      case 'SMALL': return 120;
      case 'MEDIUM': return 400;
      case 'FULLSCREEN': return 600;
      default: return 120;
    }
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>템플릿 네이티브 광고</Text>
      
      <View style={styles.buttonContainer}>
        <Button title="SMALL" onPress={() => setTemplateType('SMALL')} />
        <Button title="MEDIUM" onPress={() => setTemplateType('MEDIUM')} />
        <Button title="FULLSCREEN" onPress={() => setTemplateType('FULLSCREEN')} />
      </View>

      <AdWhaleNativeTemplateView
        ref={adRef}
        style={[styles.adView, { height: getHeight(templateType) }]}
        placementUid={PLACEMENT_UID}
        template={templateType}
        templateStyle={customStyle}
        onAdLoaded={() => {
          console.log('네이티브 광고 로드 성공');
          setIsLoaded(true);
        }}
        onAdFailedToLoad={(event) => {
          console.log('네이티브 광고 로드 실패:', event.errorCode, event.errorMessage);
          setIsLoaded(false);
        }}
        onAdShowFailed={(event) => {
          console.log('네이티브 광고 표시 실패:', event.errorCode, event.errorMessage);
        }}
      />

      <View style={styles.buttonContainer}>
        <Button title="광고 로드" onPress={handleLoadAd} />
        {isLoaded && (
          <Button title="광고 표시" onPress={handleShowAd} />
        )}
      </View>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
  },
  title: {
    fontSize: 20,
    fontWeight: 'bold',
    marginBottom: 20,
  },
  buttonContainer: {
    flexDirection: 'row',
    justifyContent: 'space-around',
    marginBottom: 20,
  },
  adView: {
    width: '100%',
    marginBottom: 20,
    borderWidth: 1,
    borderColor: '#ddd',
  },
});

export default TemplateNativeAdExample;
```

**FullScreen 템플릿 모달 샘플코드**

```typescript
import React, { useRef, useState } from 'react';
import { View, Modal, SafeAreaView, TouchableOpacity, Text } from 'react-native';
import { 
  AdWhaleNativeTemplateView, 
  AdWhaleNativeTemplateHandle 
} from 'adwhale-sdk-react-native';

const FullscreenNativeAdModal: React.FC = () => {
  const adRef = useRef<AdWhaleNativeTemplateHandle>(null);
  const [visible, setVisible] = useState(false);

  const handleOpenModal = () => {
    setVisible(true);
    // 모달이 열린 후 광고 로드
    setTimeout(() => {
      adRef.current?.loadAd();
    }, 300);
  };

  const handleCloseModal = () => {
    setVisible(false);
  };

  return (
    <>
      <TouchableOpacity onPress={handleOpenModal}>
        <Text>FULLSCREEN 광고 보기</Text>
      </TouchableOpacity>

      <Modal
        visible={visible}
        animationType="slide"
        onRequestClose={handleCloseModal}>
        <SafeAreaView style={{ flex: 1 }}>
          <View style={{ flexDirection: 'row', justifyContent: 'space-between', padding: 16 }}>
            <Text style={{ fontSize: 18, fontWeight: 'bold' }}>Fullscreen Native Ad</Text>
            <TouchableOpacity onPress={handleCloseModal}>
              <Text>닫기</Text>
            </TouchableOpacity>
          </View>
          
          <AdWhaleNativeTemplateView
            ref={adRef}
            style={{ flex: 1 }}
            placementUid="your-placement-uid"
            template="FULLSCREEN"
            onAdLoaded={() => {
              // 로드 완료 후 자동 표시
              setTimeout(() => {
                adRef.current?.showAd();
              }, 100);
            }}
          />
        </SafeAreaView>
      </Modal>
    </>
  );
};
```

#### 8. 주의사항 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

#### 광고 로드 타이밍 <a href="#id-2____542" id="id-2____542"></a>

* `loadAd()`를 호출한 후 `onAdLoaded` 이벤트가 발생해야 `showAd()`를 호출할 수 있습니다.
* 광고가 로드되기 전에 `showAd()`를 호출하면 표시되지 않습니다.

#### 컴포넌트 재생성 <a href="#id-3___547" id="id-3___547"></a>

* placement UID나 템플릿 타입을 변경할 때는 컴포넌트를 재생성해야 합니다.
* `key` prop을 사용하여 컴포넌트를 강제로 재생성할 수 있습니다.

#### FULLSCREEN 템플릿 <a href="#id-4_fullscreen__552" id="id-4_fullscreen__552"></a>

* FULLSCREEN 템플릿은 모달이나 별도 화면에서 사용하는 것을 권장합니다.
* 일반 뷰에 표시하면 레이아웃 문제가 발생할 수 있습니다.

#### 스타일 커스터마이징 <a href="#id-5___557" id="id-5___557"></a>

* 템플릿 스타일은 광고 로드 전에 설정해야 합니다.
* 스타일을 변경하려면 컴포넌트를 재생성하세요.

#### 에러 처리 <a href="#id-6___562" id="id-6___562"></a>

* `onAdFailedToLoad`와 `onAdShowFailed` 이벤트에서 적절한 에러 처리를 구현하세요.
* 에러 코드와 메시지를 로깅하여 문제를 추적할 수 있습니다.

#### 테스트 <a href="#id-7__567" id="id-7__567"></a>

* 개발 환경에서는 테스트용 placement UID를 사용하세요.
* 다양한 템플릿 타입과 스타일을 테스트하세요.
