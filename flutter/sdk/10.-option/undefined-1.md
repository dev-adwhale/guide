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

{% endtab %}
{% endtabs %}
