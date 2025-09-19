# 응답 정보

{% hint style="info" %}
&#x20;AdWhaleMediationResponseInfo 클래스는 광고 응답에 대한 상세 정보를 담고 있는 객체입니다.

각 미디에이션 플랫폼에 따라 제공되는 정보가 다를 수 있습니다.

***

`getMediationProvider()` 외의 상세 정보는 AdMob, LevelPlay와 같이 해당 정보를 제공하는 일부 미디에이션 플랫폼에서만 반환됩니다.

이외에 연동된 다른 광고 네트워크에서는 대부분의 필드가 `null` 일 수 있습니다.
{% endhint %}

{% tabs %}
{% tab title="Java" %}
**AdWhaleMediationResponseInfo 클래스 API 설명**

공통 정보

```java
public String getMediationProvider()
```

| 파라미터 타입 | 파라미터 값                           |
| ------- | -------------------------------- |
| String  | AdWhale SDK 에서 응답된 광고 네트워크 정보 반환 |

```java
public String getAdNetworkName()
```

| 파라미터 타입 | 파라미터 값                                                      |
| ------- | ----------------------------------------------------------- |
| String  | mediationProvider가 미디에이션을 통해 최종적으로 광고를 가져온 실제 광고 네트워크 정보 반환 |

```java
public String getNetworkInstanceName() 
```

| 파라미터 타입 | 파라미터 값        |
| ------- | ------------- |
| String  | 네트워크 인스턴스명 반환 |

```java
public String getNetworkInstanceId()
```

| 파라미터 타입 | 파라미터 값          |
| ------- | --------------- |
| String  | 네트워크 인스턴스 ID 반환 |

AdMob 전용 정보

```java
public String getResponseId()
```

| 파라미터 타입 | 파라미터 값                |
| ------- | --------------------- |
| String  | 광고 응답 추적을 위한 고유 ID 반환 |

```java
public String getAdSourceName()
```

| 파라미터 타입 | 파라미터 값    |
| ------- | --------- |
| String  | 광고 소스명 반환 |

```java
public String getAdSourceId()
```

| 파라미터 타입 | 파라미터 값      |
| ------- | ----------- |
| String  | 광고 소스 ID 반환 |

```java
public String getAdapterClassName()
```

| 파라미터 타입 | 파라미터 값           |
| ------- | ---------------- |
| String  | 사용된 어댑터의 클래스명 반환 |

```java
public Long getLatencyMillis()
```

| 파라미터 타입 | 파라미터 값                     |
| ------- | -------------------------- |
| Long    | 광고 응답에 소요된 시간 반환 (단위: 밀리초) |

```java
public android.os.Bundle getCredentials()
```

| 파라미터 타입           | 파라미터 값           |
| ----------------- | ---------------- |
| android.os.Bundle | 어댑터의 자격 증명 정보 반환 |

```java
public java.util.Map<String, Object> getResponseExtras()
```

| 파라미터 타입                        | 파라미터 값      |
| ------------------------------ | ----------- |
| java.util.Map\<String, Object> | 추가 응답 정보 반환 |



LevelPlay 전용 정보

```java
public Double getRevenue()
```

| 파라미터 타입 | 파라미터 값                |
| ------- | --------------------- |
| Double  | 광고 수익 정보 반환 (단위: USD) |

```java
public String getRevenuePrecision()
```

| 파라미터 타입 | 파라미터 값                        |
| ------- | ----------------------------- |
| String  | revenue의 소스값 (BID, RATE, CPM) |

```java
public String getAuctionId()
```

| 파라미터 타입 | 파라미터 값   |
| ------- | -------- |
| String  | 경매 ID 반환 |

```java
public String getSegmentName()
```

| 파라미터 타입 | 파라미터 값       |
| ------- | ------------ |
| String  | 사용자 세그먼트명 반환 |

```java
public String getCountry()
```

| 파라미터 타입 | 파라미터 값       |
| ------- | ------------ |
| String  | 사용자 국가 코드 반환 |
{% endtab %}

{% tab title="Kotlin" %}
**AdWhaleMediationResponseInfo 클래스 API 설명**

공통 정보

```kotlin
val mediationProvider: String
```

| 파라미터 타입 | 파라미터 값                           |
| ------- | -------------------------------- |
| String  | AdWhale SDK 에서 응답된 광고 네트워크 정보 반환 |

```kotlin
val adNetworkName: String
```

| 파라미터 타입 | 파라미터 값                                                      |
| ------- | ----------------------------------------------------------- |
| String  | mediationProvider가 미디에이션을 통해 최종적으로 광고를 가져온 실제 광고 네트워크 정보 반환 |

```kotlin
val networkInstanceName: String
```

| 파라미터 타입 | 파라미터 값        |
| ------- | ------------- |
| String  | 네트워크 인스턴스명 반환 |

```kotlin
val networkInstanceId: String
```

| 파라미터 타입 | 파라미터 값          |
| ------- | --------------- |
| String  | 네트워크 인스턴스 ID 반환 |



AdMob 전용 정보

```kotlin
val responseId: String
```

| 파라미터 타입 | 파라미터 값                |
| ------- | --------------------- |
| String  | 광고 응답 추적을 위한 고유 ID 반환 |

```kotlin
val adSourceName: String
```

| 파라미터 타입 | 파라미터 값    |
| ------- | --------- |
| String  | 광고 소스명 반환 |

```kotlin
val adSourceId: String
```

| 파라미터 타입 | 파라미터 값      |
| ------- | ----------- |
| String  | 광고 소스 ID 반환 |

```kotlin
val adapterClassName: String
```

| 파라미터 타입 | 파라미터 값           |
| ------- | ---------------- |
| String  | 사용된 어댑터의 클래스명 반환 |

```kotlin
val latencyMillis: Long
```

| 파라미터 타입 | 파라미터 값                     |
| ------- | -------------------------- |
| Long    | 광고 응답에 소요된 시간 반환 (단위: 밀리초) |

```kotlin
val credentials: android.os.Bundle
```

| 파라미터 타입           | 파라미터 값           |
| ----------------- | ---------------- |
| android.os.Bundle | 어댑터의 자격 증명 정보 반환 |

```kotlin
val responseExtras: Map<String, Any>
```

| 파라미터 타입                        | 파라미터 값      |
| ------------------------------ | ----------- |
| java.util.Map\<String, Object> | 추가 응답 정보 반환 |



LevelPlay 전용 정보

```kotlin
val revenue: Double
```

| 파라미터 타입 | 파라미터 값                |
| ------- | --------------------- |
| Double  | 광고 수익 정보 반환 (단위: USD) |

```kotlin
val revenuePrecision: String
```

| 파라미터 타입 | 파라미터 값                        |
| ------- | ----------------------------- |
| String  | revenue의 소스값 (BID, RATE, CPM) |

```kotlin
val auctionId: String
```

| 파라미터 타입 | 파라미터 값   |
| ------- | -------- |
| String  | 경매 ID 반환 |

```kotlin
val segmentName: String
```

| 파라미터 타입 | 파라미터 값       |
| ------- | ------------ |
| String  | 사용자 세그먼트명 반환 |

```kotlin
val country: String
```

| 파라미터 타입 | 파라미터 값       |
| ------- | ------------ |
| String  | 사용자 국가 코드 반환 |
{% endtab %}
{% endtabs %}
