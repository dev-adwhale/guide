# Swift Package Manager

프로젝트에 [패키지 종속 항목을 추가](https://developer.apple.com/documentation/xcode/adding-package-dependencies-to-your-app#Add-a-package-dependency)하려면 다음 단계를 진행합니다.

{% hint style="info" %}
1. Xcode에서 **File(파일) > Add Package Dependencies(패키지 종속 항목 추가)...**&#xB85C; 이동하여 Cauly Swift 패키지를 설치합니다.
2. 표시되는 메시지에서 다음 Google 모바일 광고 Swift 패키지 GitHub 저장소를 검색합니다.
   *   AdWhale SDK Swift 패키지 저장소

       ```
       https://github.com/dev-adwhale/AdWhaleSPM.git
       ```
   *   AdWhale Mediation Adapter Swift 패키지 저장소

       ```
       https://github.com/dev-adwhale/AdWhaleMediationAdapterSPM.git
       ```
3. 사용할 AdWhale Swift 패키지의 버전을 선택합니다. (최신 SDK 버전: 1.0.4)
{% endhint %}

#### `-ObjC` 플래그 추가 안내

Xcode > Build Settings > Linking 에서 **Other Linker Flags**에 `-ObjC` 플래그가 설정되지 않은 경우,\
Objective-C 기반 어댑터가 정상적으로 로드되지 않아 **일부 미디에이션 네트워크가 인식되지 않을 수 있습니다.**\
이 경우 `-ObjC` 플래그를 추가합니다.

<figure><img src="../../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

