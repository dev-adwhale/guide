# Error code

ADwhale Mediation SDK에서 정의하는 Error code는 다음과 같습니다.

<table data-header-hidden><thead><tr><th width="94.28125">Code</th><th>상수</th><th>설명</th></tr></thead><tbody><tr><td>Code</td><td>상수</td><td>설명</td></tr><tr><td>100</td><td>INTERNAL_SUCCESS</td><td>성공(SDK 초기화 성공/광고 로드 성공)</td></tr><tr><td>200</td><td>INTERNAL_ERROR</td><td>연동 오류 - 파라미터 오설정이나 광고 준비가 안된 상태에서 표시 요청한 경우</td></tr><tr><td>300</td><td>MEDIATION_NETWORK_ERROR</td><td>광고 미충족 - 응답이 무효하거나, 모든 광고 네트워크에서 광고를 받지 못한 경우</td></tr></tbody></table>

#### 광고 로드 실패 메시지

대상: 전면, 보상형, 앱 오프닝, 앱 종료, 앱 내 화면 전환 광고의 `onAdLoadFailed` String 값

| 코드    | String 값                                                              | 의미                                |
| ----- | --------------------------------------------------------------------- | --------------------------------- |
| `200` | `placementUid is required.`                                           | 연동 오류 (placementUid 미설정)          |
| `300` | `No ad mediation network found.` + 실패 원인                              | 광고를 채우지 못함 (설정된 네트워크 모두 실패)       |
| `300` | `Failed to load Mediation Ad Config Info. Network error. [config: …]` | 광고 설정 조회 실패 (네트워크 오류, 저장된 설정도 없음) |

**실패 원인 표기 (SDK 2.7.10 이상)**

실패한 네트워크마다 메시지 뒤에 원인이 붙습니다.

| 원인            | 표기 예                                |
| ------------- | ----------------------------------- |
| 시간 초과         | `[admob: timeout 10s]`              |
| 광고 네트워크 오류 응답 | `[admob: code=3, message=No fill.]` |
| 예외            | `[levelplay: error, …]`             |

예: `No ad mediation network found. [admob: timeout 10s] [levelplay: code=509, message=No ads to show]`

{% hint style="info" %}
메시지는 원인 분석용이며 내용이 바뀔 수 있습니다. 결과 판단은 메시지 문자열이 아니라 코드(`300`)로 해 주세요.
{% endhint %}
