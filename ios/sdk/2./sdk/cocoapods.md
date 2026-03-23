# CocoaPods

{% hint style="danger" %}
CocoaPods 정책 변경에 따라,\
**AdWhale iOS SDK의 CocoaPods 지원은 2026년 내 종료될 예정**입니다.

이에 따라 CocoaPods 환경에서는\
**신규 기능 추가 및 장기적인 업데이트가 제공되지 않습니다.**

신규 연동 또는 향후 유지보수를 고려 중인 프로젝트의 경우\
**Swift Package Manager(SPM) 사용을 권장**드립니다.
{% endhint %}

### 1. SDK 설치

{% hint style="info" %}
AdWhale iOS SDK 업데이트에 따라 아래 내용이 변경될 수 있습니다.
{% endhint %}

1\) 프로젝트의 `Podfile`을 열고 다음 항목을 추가합니다.

```
source 'https://github.com/CocoaPods/Specs.git'
source 'https://github.com/dev-adwhale/AdWhaleSDK_iOS.git'

pod 'AdWhaleSDK', '1.0.4'

# Xcode15.0 이상 버전에서 TOOL CHAIN 관련 빌드 에러가 발생하는 경우, 아래 코드를 추가합니다.
post_install do |installer|
  installer.pods_project.targets.each do |target|
    target.build_configurations.each do |config|
      xcconfig_path = config.base_configuration_reference.real_path
      xcconfig = File.read(xcconfig_path)
      xcconfig_mod = xcconfig.gsub(/DT_TOOLCHAIN_DIR/, "TOOLCHAIN_DIR")
      File.open(xcconfig_path, "w") { |file| file << xcconfig_mod }
    end
  end
end
```

{% hint style="warning" %}
Xcode15.0 이상 버전에서 빌드 시, 일부 광고 네트워크 SDK에서 다음과 같은 에러가 발생할 수 있습니다.

이 경우 Podfile에 위 내용을 참조하여 수정합니다.

***

**DT\_TOOLCHAIN\_DIR cannot be used to evaluate LIBRARY\_SEARCH\_PATHS, use TOOLCHAIN\_DIR instead**
{% endhint %}



2\) pod을 설치하고 `.xcworkspace` 파일을 엽니다.

```
pod install --repo-update
```

### 2. **`$(inherited)`** 플래그 추가 안내

Xcode > Build Settings > Search Paths 에서 Framework Search Paths 에 **`$(inherited)`** 값이 추가되어 있는지 확인하고, 추가되어 있지 않다면 관련 라이브러리를 찾을 수 없다는 빌드 에러가 발생할 수 있으므로 반드시 추가합니다.

<figure><img src="../../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>
