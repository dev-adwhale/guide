# 3. SDK 초기화

{% hint style="info" %}
* AdWhale SDK를 프로젝트에 추가해야 합니다.
* 광고를 요청하기 전에 초기화가 이루어져야 합니다.
{% endhint %}

{% tabs %}
{% tab title="Swift" %}
```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    // Override point for customization after application launch.
    
    // SDK Init
    AdWhaleAds.sharedInstance.initialize {
        // SDK 초기화 완료 후 동작 코드
    }
    
    return true
}    
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
 - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.
    
    // SDK Init
    [[AdWhaleAds sharedInstance] initialize:@"0" completionHandler:^{
        // SDK 초기화 완료 후 동작 코드
    }];
    return YES;
}
```
{% endtab %}
{% endtabs %}

<table data-header-hidden><thead><tr><th>속성</th><th>설명</th><th data-hidden></th></tr></thead><tbody><tr><td>속성</td><td>설명</td><td></td></tr><tr><td>appId</td><td>App Store에 등록된 App ID 정보 (선택사항)</td><td></td></tr><tr><td>completionHandler</td><td>SDK 초기화 완료 후 동작 코드</td><td></td></tr></tbody></table>
