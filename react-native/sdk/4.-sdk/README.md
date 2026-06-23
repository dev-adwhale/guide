# 4. SDK 초기화

### 초기화

{% hint style="info" %}
광고를 요청하기 전에 초기화가 이루어져야 합니다. 초기화 방법은 초기화를 담당하는 **AdWhaleMediationAds 클래스를 호출**해주며, 초기화 **호출 결과는 return 값을 통해 통지**됩니다. 자세한 구현은 아래 API와 샘플코드를 참고하여 구현하면 됩니다.
{% endhint %}

**AdWhaleMediationAds 클래스**

```typescript
initialize(): Promise<InitializeResult>
```

```typescript
interface InitializeResult {
  statusCode: number;
  message: string;
}
```

**초기화 샘플코드**

```typescript
import { AdWhaleMediationAds } from 'adwhale-sdk-react-native';

// 앱 시작 시 SDK 초기화
AdWhaleMediationAds.initialize()
  .then(result => {
    if (result.statusCode === 100) {
      console.log('SDK 초기화 성공:', result.message);
    } else {
      console.log('SDK 초기화 실패:', result.statusCode, result.message);
    }
  })
  .catch(err => {
    console.error('SDK 초기화 에러:', err);
  });
```
