# 3.1 로거 설정

{% hint style="info" %}
`AdWhaleLog` 클래스로 SDK 로그 레벨을 설정할 수 있습니다.
{% endhint %}

### `AdWhaleLog` 클래스 API

{% tabs %}
{% tab title="Swift" %}
```swift
static func setLogLevel(_ level: AdWhaleLogLevel)
```

| 파라미터 타입           | 설명       |
| ----------------- | -------- |
| `AdWhaleLogLevel` | 로그 레벨 설정 |

```swift
static func getLogLevel() -> AdWhaleLogLevel
```

| 리턴 타입             | 설명           |
| ----------------- | ------------ |
| `AdWhaleLogLevel` | 적용된 로그 레벨 반환 |
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
+ (void)setLogLevel:(AdWhaleLogLevel)level;
```

| 파라미터 타입           | 설명       |
| ----------------- | -------- |
| `AdWhaleLogLevel` | 로그 레벨 설정 |

```objectivec
+ (AdWhaleLogLevel)getLogLevel;
```

| 리턴 타입             | 설명           |
| ----------------- | ------------ |
| `AdWhaleLogLevel` | 적용된 로그 레벨 반환 |
{% endtab %}
{% endtabs %}

### `AdWhaleLogLevel` 설명

| 값 (Swift)  | 값 (Objective-C)          | 설명                          |
| ---------- | ------------------------ | --------------------------- |
| `.none`    | `AdWhaleLogLevelNone`    | 로그를 출력하지 않음                 |
| `.error`   | `AdWhaleLogLevelError`   | Error 로그 출력                 |
| `.warn`    | `AdWhaleLogLevelWarn`    | Error, Warn 로그 출력 **(기본값)** |
| `.info`    | `AdWhaleLogLevelInfo`    | Error, Warn, Info 로그 출력     |
| `.debug`   | `AdWhaleLogLevelDebug`   | `.info` 로 적용됨               |
| `.verbose` | `AdWhaleLogLevelVerbose` | `.info` 로 적용됨               |

### 사용 예

{% tabs %}
{% tab title="Swift" %}
```swift
AdWhaleLog.setLogLevel(.info)
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
[AdWhaleLog setLogLevel:AdWhaleLogLevelInfo];
```
{% endtab %}
{% endtabs %}

로그는 Xcode 콘솔에 표시됩니다.
