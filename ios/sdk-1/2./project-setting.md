# Project Setting

#### `-ObjC` 플래그 추가 안내

Xcode > Build Settings > Linking 에서 **Other Linker Flags**에 `-ObjC` 플래그가 설정되지 않은 경우,\
Objective-C 기반 어댑터가 정상적으로 로드되지 않아 **일부 미디에이션 네트워크가 인식되지 않을 수 있습니다.**\
이 경우 `-ObjC` 플래그를 추가합니다.

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="danger" %}
**이 플래그가 없으면 광고가 로드되지 않습니다.**\
파트너 어댑터는 Objective-C 클래스/카테고리로 구현되어 있어, 이 플래그가 없으면\
링커가 해당 코드를 제거해 런타임에 어댑터를 찾지 못합니다.\
크래시가 아니라 **조용한 실패**로 나타나므로 원인을 찾기 어렵습니다.
{% endhint %}



#### Enable Bitcode = No 설정

1\) Xcode > Build Settings > Build Options 에서 `Enable Bitcode` 를 `No` 로 변경합니다.

* Xcode 버전에 따라 해당 설정이 없을 수 있습니다.

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

2\) Xcode > Build Settings > Linking 에서 Other Linker Flags 에 `-all-load` 또는 `-force_load` 플래그를 사용하여 빌드할 경우, 심볼 중복(Symbol Duplicate)가 발생할 수 있습니다. 이 경우 **`-ObjC`** 플래그로 변경합니다.

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

3\) Xcode > Build Settings > Build Options 에서 `User Script Sandboxing` 를 `No` 로 변경합니다.

* Xcode 버전에 따라 해당 설정이 없을 수 있습니다.

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

