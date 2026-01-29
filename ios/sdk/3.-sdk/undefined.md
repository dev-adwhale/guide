# 광고 음소거 및 볼륨 제어

{% hint style="info" %}
AdWhale SDK는 앱의 광고 오디오를 의도적으로 제어할 수 있도록\
전역 음소거 및 볼륨 설정 기능을 제공합니다.

이 기능을 사용하면 광고가 소리를 재생하지 않도록 하거나,\
또는 낮은 볼륨으로만 재생되도록 앱 정책에 맞게 조정할 수 있습니다.

동영상 광고의 오디오 제어는 일부 광고 소스에만 적용되며,\
미디에이션을 통해 로드된 광고에는 적용되지 않을 수 있습니다.
{% endhint %}

### 지원 API

{% tabs %}
{% tab title="Swift" %}
```swift
AdWhaleAds.sharedInstance.setMuted(muted: Bool)
AdWhaleAds.sharedInstance.setVolume(volume: Float)
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[[AdWhaleAds sharedInstance] setMuted:(BOOL)muted];
[[AdWhaleAds sharedInstance] setVolume:(float)volume];
```
{% endtab %}
{% endtabs %}

### 동작 개요

<table data-header-hidden><thead><tr><th valign="bottom">설정 상태</th><th valign="bottom">광고 오디오 동작</th></tr></thead><tbody><tr><td valign="bottom">설정 상태</td><td valign="bottom">광고 오디오 동작</td></tr><tr><td valign="bottom">setMuted(true)</td><td valign="bottom">광고는 의도적으로 무음 상태로 시작</td></tr><tr><td valign="bottom">setMuted(false)</td><td valign="bottom">광고는 소리를 재생할 수 있음</td></tr><tr><td valign="bottom">setVolume(0.1)</td><td valign="bottom">광고 볼륨을 기기 볼륨 대비 10% 비율로 제한</td></tr></tbody></table>

### setMuted(Bool)

{% hint style="info" %}
이 설정은 **배너, 전면, 리워드, 앱 오프닝, 네이티브 광고를 포함한 모든 광고 유형**의\
**초기** **오디오 재생 상태를 음소거로 설정**하기 위한 전역 설정입니다.
{% endhint %}

{% tabs %}
{% tab title="Swift" %}
```swift
AdWhaleAds.sharedInstance.setMuted(true)
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[[AdWhaleAds sharedInstance] setMuted:YES];
```
{% endtab %}
{% endtabs %}

#### 동작 특징

* 음소거 상태에서는 광고의 **오디오가 재생되지 않습니다**.
* 음소거가 설정된 경우, `setVolume()`으로 지정한 볼륨 값은 적용되지 않습니다.
* 음소거 설정은 **광고를 요청(load)하기 전에 적용**되어야 하며,\
  광고가 시작될 때의 **초기 오디오 상태에만 영향을 줍니다**.
* 광고 요청 이후에 변경된 음소거 상태는 이미 요청된 광고에는 반영되지 않을 수 있습니다.

{% hint style="warning" %}
**유의 사항**

* 광고 유형별로 서로 다른 음소거/볼륨 설정을 개별적으로 지정하는 기능은 제공되지 않습니다.
* 이 설정은 **앱 실행 중 메모리 상에 유지되는 전역 설정**이며,\
  앱이 종료되면 자동으로 초기화됩니다.
* ViewController 전환 여부와 관계없이 설정 값은 유지됩니다.
* 광고 재생 중 사용자가 광고 UI를 통해 음소거를 해제하거나 볼륨을 변경할 수 있으며,\
  **해당 사용자 동작은 SDK에서 제어하거나 차단할 수 없습니다**.
* 일부 동영상 광고 소재는 음소거 상태에서는 노출되지 않을 수 있으며,\
  이 경우 **광고 노출률 또는 수익에 영향을 줄 수 있습니다**.
{% endhint %}

### setVolume(float)

{% hint style="info" %}
이 설정은 광고 소리는 필요하지만, 과도한 음량을 피하고 싶은 경우에 사용합니다.
{% endhint %}

{% tabs %}
{% tab title="Swift" %}
```swift
AdWhaleAds.sharedInstance.setVolume(0.3)
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[[AdWhaleAds sharedInstance] setVolume:0.3f];
```
{% endtab %}
{% endtabs %}

#### 동작 특징

* setMuted(true) 상태에서는 setVolume() 값과 관계없이 항상 무음으로 동작합니다.
* 볼륨 값은 0.0 \~ 1.0 범위 내에서 동작합니다.

{% hint style="warning" %}
**유의 사항**

* 적용 대상: **앱 오프닝 / 배너 / 전면 / 보상형**&#x20;
* Native 광고는  `setVolume()` 설정이 그대로 적용되지 않을 수 있습니다.
* 일부 광고 네트워크 또는 광고 소재의 구현 방식에 따라\
  `setVolume()`으로 설정한 볼륨 값이 100% 반영되지 않을 수 있습니다.
* 특히 미디에이션 환경에서는 개별 네트워크 SDK 또는\
  광고 크리에이티브 정책에 의해 볼륨 설정이 적용되지 않을 수 있습니다.
{% endhint %}

#### 적용 예시

<table data-header-hidden><thead><tr><th valign="bottom">설정</th><th valign="bottom">결과</th></tr></thead><tbody><tr><td valign="bottom">설정</td><td valign="bottom">결과</td></tr><tr><td valign="bottom">setMuted(false) + setVolume(0.3)</td><td valign="bottom">광고 소리가 작게 재생됨</td></tr><tr><td valign="bottom">setMuted(true) + setVolume(0.3)</td><td valign="bottom">무음 (의도된 동작)</td></tr></tbody></table>

### 권장 사용 패턴

{% hint style="info" %}
* 광고 로드 직전에 반복 호출할 필요는 없습니다.
* 전역 정책으로 한 번 설정한 뒤 유지하는 방식을 권장합니다.
{% endhint %}

{% tabs %}
{% tab title="Swift" %}
```swift
// 앱 시작 시 또는 광고 초기화 시 1회 설정 
AdWhaleAds.sharedInstance.setMuted(false)
AdWhaleAds.sharedInstance.setVolume(0.3)
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
// 앱 시작 시 또는 광고 초기화 시 1회 설정 
[[AdWhaleAds sharedInstance] setMuted:NO];
[[AdWhaleAds sharedInstance] setVolume:0.3f];
```
{% endtab %}
{% endtabs %}

#### 기본값 안내

{% hint style="info" %}
앱 실행 후 음소거 또는 볼륨 설정을 별도로 호출하지 않은 경우, SDK 초기 상태에서는 아래 기본값이 적용됩니다.
{% endhint %}

<table data-header-hidden><thead><tr><th valign="bottom">설정</th><th valign="bottom">기본값</th></tr></thead><tbody><tr><td valign="bottom">설정</td><td valign="bottom">기본값</td></tr><tr><td valign="bottom">음소거</td><td valign="bottom">OFF (음소거 미적용)</td></tr><tr><td valign="bottom">볼륨</td><td valign="bottom">1.0 (시스템 볼륨 기준 100%)</td></tr></tbody></table>
