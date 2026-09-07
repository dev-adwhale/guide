# CocoaPods

{% hint style="danger" %}
CocoaPods 정책 변경에 따라,\
**AdWhale iOS SDK의 CocoaPods 지원은 2026년 내 종료될 예정**입니다.

이에 따라 CocoaPods 환경에서는\
**신규 기능 추가 및 장기적인 업데이트가 제공되지 않습니다.**

신규 연동 또는 향후 유지보수를 고려 중인 프로젝트의 경우\
**Swift Package Manager(SPM) 사용을 권장**드립니다.
{% endhint %}

### 1. 로컬 podspec으로 시도하는 경우

```ruby
platform :ios, '15.0'
use_frameworks!

target 'YourApp' do
  pod 'AdWhaleSDK', :podspec => 'https://…/AdWhaleSDK.podspec'
end
```

{% hint style="warning" %}
설치 후 [2-2. Project Setting](https://dillinger.io/02-02-project-setting.md) 의 `-ObjC` 링커 플래그 설정을 반드시 함께 적용하세요.
{% endhint %}

### 2. **`$(inherited)`** 플래그 추가 안내

Xcode > Build Settings > Search Paths 에서 Framework Search Paths 에 **`$(inherited)`** 값이 추가되어 있는지 확인하고, 추가되어 있지 않다면 관련 라이브러리를 찾을 수 없다는 빌드 에러가 발생할 수 있으므로 반드시 추가합니다.

<figure><img src="../../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>
