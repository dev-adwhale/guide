# 로거 설정

{% tabs %}
{% tab title="JS 혹은 TypeScript" %}
**AdWhaleMediationSdk 클래스**

```typescript
setLoggerEnabled(enabled: boolean)
```

| 파라미터 타입 | 파라미터 값   |
| ------- | -------- |
| bool    | 로그 레벨 설정 |

**로그 레벨 설정**

| 값     | 설명                      |
| ----- | ----------------------- |
| false | 모든 로그 출력 안함             |
| true  | Error, Warn, Info 로그 출력 |

**샘플코드**

```typescript
AdWhaleMediationSdk.setLoggerEnabled(true);
```
{% endtab %}
{% endtabs %}
