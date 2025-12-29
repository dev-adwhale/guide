# 로거 설정

{% hint style="info" %}
&#x20;AdWhaleLog 클래스를 통해 SDK 로그레벨 및 로그 내용을 컨트롤 할 수 있습니다.&#x20;
{% endhint %}

{% tabs %}
{% tab title="Java" %}
**AdWhaleLog 클래스 API 설명**

```java
public static void setLogLevel(AdWhaleLog.LogLevel logLevel)
```

| 파라미터 타입             | 파라미터 값   |
| ------------------- | -------- |
| AdWhaleLog.LogLevel | 로그 레벨 설정 |

```java
public static AdWhaleLog.LogLevel getLogLevel()
```

| 리턴 타입               | 리턴 값         |
| ------------------- | ------------ |
| AdWhaleLog.LogLevel | 설정된 로그 레벨 반환 |

**AdWhaleLog.LogLevel 클래스 설명**

| 값                           | 설명                      |
| --------------------------- | ----------------------- |
| AdWhaleLog.LogLevel.None    | 모든 로그 출력 안함             |
| AdWhaleLog.LogLevel.Error   | Error 로그 출력             |
| AdWhaleLog.LogLevel.Warn    | Error, Warn 로그 출력       |
| AdWhaleLog.LogLevel.Info    | Error, Warn, Info 로그 출력 |
| AdWhaleLog.LogLevel.Debug   | 해당사항없음                  |
| AdWhaleLog.LogLevel.Verbose | Error, Warn, Info 로그 출력 |
{% endtab %}

{% tab title="Kotlin" %}
**AdWhaleLog 클래스 API 설명**

```kotlin
fun setLogLevel(logLevel : AdWhaleLog.LogLevel) : Unit
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>AdWhaleLog.LogLevel</td><td>로그 레벨 설정</td></tr></tbody></table>

```kotlin
fun getLogLevel() : AdWhaleLog.LogLevel
```

<table data-header-hidden><thead><tr><th width="348">리턴 타입</th><th>리턴 값</th></tr></thead><tbody><tr><td>리턴 타입</td><td>리턴 값</td></tr><tr><td>AdWhaleLog.LogLevel</td><td>설정된 로그 레벨 반환</td></tr></tbody></table>

**AdWhaleLog.LogLevel 클래스 설명**

<table data-header-hidden><thead><tr><th width="348">값</th><th>설명</th></tr></thead><tbody><tr><td>값</td><td>설명</td></tr><tr><td>AdWhaleLog.LogLevel.None</td><td>모든 로그 출력 안함</td></tr><tr><td>AdWhaleLog.LogLevel.Error</td><td>Error 로그 출력</td></tr><tr><td>AdWhaleLog.LogLevel.Warn</td><td>Error, Warn 로그 출력</td></tr><tr><td>AdWhaleLog.LogLevel.Info</td><td>Error, Warn, Info 로그 출력</td></tr><tr><td>AdWhaleLog.LogLevel.Debug</td><td>해당사항없음</td></tr><tr><td>AdWhaleLog.LogLevel.Verbose</td><td>Error, Warn, Info 로그 출력</td></tr></tbody></table>
{% endtab %}
{% endtabs %}
