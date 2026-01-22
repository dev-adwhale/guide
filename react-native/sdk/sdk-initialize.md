# 2. SDK 초기화 및 구현

#### 1. 초기화 <a href="#id-2.-initialize" id="id-2.-initialize"></a>

{% hint style="info" %}
광고를 요청하기 전에 초기화가 이루어져야 합니다. 초기화 방법은 초기화를 담당하는 **AdWhaleMediationAds 클래스를 MainActivity 에서 호출**해주며, 초기화 **호출 결과는 return 값을 통해 통지**됩니다. 자세한 구현은 아래 API와 샘플코드를 참고하여 구현하면 됩니다.
{% endhint %}

{% tabs %}
{% tab title="JS 혹은 TypeScript" %}
**AdWhaleMediationAds 클래스**

<pre class="language-typescript"><code class="lang-typescript"><strong>initialize(): Promise&#x3C;InitializeResult>
</strong></code></pre>

**초기화 샘플코드**

```typescript
import { AdWhaleMediationAds } from 'adwhale-sdk-react-native';

// 앱 시작 시 SDK 초기화
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
```
{% endtab %}
{% endtabs %}

#### 2. 로거 설정 <a href="#id-3.-gdpr-coppa" id="id-3.-gdpr-coppa"></a>

{% tabs %}
{% tab title="First Tab" %}
**AdWhaleMediationAds 클래스**

```typescript
setLoggerEnabled(enabled: boolean)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>boolean</td><td>true : Error, Warn, Info 로그 출력<br>false : 모든 로그 출력 안함</td></tr></tbody></table>

```typescript
getLogLevel() // 현재 설정된 로그레벨 출력
```

**로거설정 샘플코드**

```typescript
import { AdWhaleMediationAds } from 'adwhale-sdk-react-native';

// setLoggerEnabled 호출
AdWhaleMediationAds.setLoggerEnabled(true);

// 테스트: setLoggerEnabled 호출 후 getLogLevel 확인
const logLevel = await AdWhaleMediationAds.getLogLevel();
console.log('[AdWhaleAppOpen] getLogLevel() 결과:', logLevel);
```
{% endtab %}
{% endtabs %}

#### 3. GDPR, COPPA설정 <a href="#id-3.-gdpr-coppa" id="id-3.-gdpr-coppa"></a>

{% hint style="info" %}
GDPR은 유럽연합(이하 'EU')의 개인정보 보호 법령으로 서비스 제공자는 EU 사용자의 개인정보 또는 그에 준하는 정보에 대한 수집 및 활용에 대해 사용자에게 동의 여부를 확인받아야 합니다. GDPR 규정 준수 및 규정에 따른 광고네트워크 동작에 필요한 절차입니다. 유럽 경제 지역 (European Economic Area) 사용자에게 동의 여부를 선택할 수 있는 기능을 제공하며 애드몹 UMP (User Messaging Platform)에 의해서 처리됩니다.
{% endhint %}

{% tabs %}
{% tab title="JS 혹은 TypeScript" %}
**AdWhaleMediationAds 클래스**

```typescript
requestGdprConsent(): Promise<{ isSuccess: boolean; message: string }> //GDPR 동의 선택화면 호출
```

```typescript
resetGdprConsentStatus() //GDPR 설정 리셋
```

{% hint style="info" %}
setGdpr 자체적으로 GDPR 선택창을 사용하실 경우 사용하며 AdWhale SDK 내부 GDPR 세팅을 위해 호출.
{% endhint %}

```typescript
setGdpr(consent: boolean) 
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>boolean</td><td>true : gdpr 동의<br>false : gdpr 비동의</td></tr></tbody></table>

```typescript
setCoppa(enabled: boolean)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>boolean</td><td>true : coppa 동의<br>false : coppa 비동의</td></tr></tbody></table>

**GDPR, Coppa 샘플코드**

<pre class="language-typescript"><code class="lang-typescript">// request gdpr
const result = await AdWhaleMediationAds.requestGdprConsent();
console.log(`GDPR Request: ${result.isSuccess ? 'Success' : 'Failed'}\n${result.message}`);
<strong>
</strong><strong>// reset consent
</strong>AdWhaleMediationAds.resetGdprConsentStatus();

// set gdpr 
<strong>AdWhaleMediationAds.setGdpr(true);
</strong><strong>
</strong><strong>// set coppa
</strong>AdWhaleMediationAds.setCoppa(true);
</code></pre>

**에러 메시지**

<table data-header-hidden><thead><tr><th width="348">에러 종류</th><th>에러 메시지</th></tr></thead><tbody><tr><td><strong>TIME_OUT</strong><br><sub>(WebView 로 consent form 을 불러오는 데 시간이 너무 오래 걸려 발생 또는 네트워크 연결에 실패한 경우)</sub></td><td>Web view timed out</td></tr><tr><td></td><td>Timed out while loading consent form</td></tr><tr><td><strong>INTERNET_ERROR</strong><br><sub>(네트워크 연결이 없거나, 요청이 서버까지 도달하지 못한 경우)</sub></td><td>Failed to load consent form due to network error</td></tr><tr><td></td><td>No internet connection</td></tr><tr><td><strong>INTERNAL_ERROR</strong><br><sub>(SDK 내부 로직이나 예외적인 상황에서 발생. 드물게 WebView 초기화 실패 등에서도 볼 수 있음)</sub></td><td>Internal error</td></tr><tr><td></td><td>An unexpected error occurred while loading the consent form</td></tr><tr><td><strong>INVALID_OPERATION</strong><br><sub>(아직 load 되지 않은 폼을 show 하려 하거나, 이미 표시 중인데 다시 show 호출했을 때 발생)</sub></td><td>Invalid operation. Consent form already showing</td></tr><tr><td></td><td>Invalid operation: Consent form not ready</td></tr></tbody></table>
{% endtab %}
{% endtabs %}
