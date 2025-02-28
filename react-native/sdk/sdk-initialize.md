# 4. SDK 초기화 및 구현

### 1. MainActivity 클래스

{% hint style="info" %}
MainActivity 클래스 설정이 없을 경우 앱 실행에 문제가 발생합니다.

Android 5.0(API Level 21) 하위 버전을 지원하는 앱의 경우 Multidex 적용이 필요합니다.
{% endhint %}

ADwhale Mediation SDK 동작을 위해서는 MainActivity(메인 액티비티 / Launch, Main) 설정이 필요합니다.

{% hint style="info" %}
더 안전한 구성요소 내보내기 설정: Android 12(API Level 31) 이상

intent-filter를 사용하는 액티비티를 포함하는 경우 exported=true 적용이 필요합니다.
{% endhint %}

```xml
<!-- AndroidManifest.xml -->
<application>
    ...
    <activity
        android:name=".MainActivity"
        android:exported="true">
        <intent-filter>
            <action android:name="android.intent.action.MAIN" />

            <category android:name="android.intent.category.LAUNCHER" />
        </intent-filter>
    </activity>
    ...
</application>
```

### 2. RNAdWhaleMediationAdSettingModule 클래스

{% hint style="info" %}
React Native 와의 통신을 위해 사용하며, AdWhale Mediation SDK를 초기화하는 네이티브 모듈입니다.

***

광고를 요청하기 전에 초기화가 이루어져야 합니다. 초기화 방법은 초기화를 담당하는 **AdWhaleMediationAds 클래스를 MainActivity 에서 호출**해주며, 초기화 **호출 결과는 AdWhaleMediationOnInitCompleteListener 클래스를 통해 통지**됩니다. 자세한 구현은 아래 API와 샘플코드를 참고하여 구현하면 됩니다.
{% endhint %}

{% tabs %}
{% tab title="Java" %}
**AdWhaleMediationAds 클래스**

<pre class="language-java"><code class="lang-java"><strong>public static void init(Context context, AdWhaleMediationOnInitCompleteListener listener)
</strong></code></pre>

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.content.Context</td><td>Android Main Activity 클래스</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener</td><td>초기화 성공 여부 콜백 리스너</td></tr></tbody></table>

**AdWhaleMediationOnInitCompleteListener 클래스**

```java
public void onInitComplete(int statusCode, String message)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>초기화 결과 코드</p><p>(성공 시 <mark style="color:green;">100</mark>, 실패 시 <mark style="color:red;">200</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(성공 시 <mark style="color:green;">"success!",</mark> 실패 시 <mark style="color:red;">"Initialization failed. error message:..."</mark>)</p></td></tr></tbody></table>

```java
public class RNAdWhaleMediationAdSettingModule extends ReactContextBaseJavaModule {
    private static final String REACT_CLASS_NAME = RNAdWhaleMediationAdSettingModule.class.getSimpleName();

    public RNAdWhaleMediationAdSettingModule(ReactApplicationContext context) {
        super(context);
    }


    @NonNull
    @Override
    public String getName() {
        return REACT_CLASS_NAME;
    }

    @ReactMethod(isBlockingSynchronousMethod = true)
    public void initialize(final Promise promise) {
        Log.i(REACT_CLASS_NAME, ".initialize()");
        Activity currentActivity = getCurrentActivity();
        if (currentActivity == null) {
            promise.reject("NO_ACTIVITY", "Unable to initialize AdWhale SDK: No activity available");
            return;
        }

        AdWhaleMediationAds.init(currentActivity, new AdWhaleMediationOnInitCompleteListener() {
            @Override
            public void onInitComplete(int statusCode, String message) {
                Log.i(REACT_CLASS_NAME, ".onInitComplete(" + statusCode + ", " + message + ")");
                promise.resolve(statusCode);
            }
        });
    }

    @Nullable
    @Override
    public Map<String, Object> getConstants() {
        final Map<String, Object> constants = new HashMap<>();
        return constants;
    }
}
```
{% endtab %}

{% tab title="Kotlin" %}
**AdWhaleMediationAds 클래스**

<pre class="language-kotlin"><code class="lang-kotlin"><strong>fun init(context : Context, listener : AdWhaleMediationOnInitCompleteListener) : Unit
</strong></code></pre>

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.content.Context</td><td>Android Main Activity 클래스</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationOnInitCompleteListener</td><td>초기화 성공 여부 콜백 리스너</td></tr></tbody></table>

**AdWhaleMediationOnInitCompleteListener 클래스**

```kotlin
fun onInitComplete(statusCode : Int, message : String) : Unit
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td><p>초기화 결과 코드</p><p>(성공 시 <mark style="color:green;">100</mark>, 실패 시 <mark style="color:red;">200</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(성공 시 <mark style="color:green;">"success!",</mark> 실패 시 <mark style="color:red;">"Initialization failed. error message:..."</mark>)</p></td></tr></tbody></table>

```kotlin
class RNAdWhaleMediationAdSettingModule(context : ReactApplicationContext?) : ReactContextBaseJavaModule(context!!) {

    companion object {
        private val REACT_CLASS_NAME = RNAdWhaleMediationAdSettingModule::class.java.simpleName
    }

    override fun getName() : String {
        return REACT_CLASS_NAME
    }

    @ReactMethod(isBlockingSynchronousMethod = true)
    fun initialize(promise: Promise) : Unit {
        AdWhaleMediationAds.init(currentActivity) {
            statusCode, message -> Log.i(REACT_CLASS_NAME, ".onInitComplete($statusCode, ${message});")
            promise.resolve(statusCode)
        }
    }

    override fun getConstants() : Map<String, String> {
        return mapOf<String, String>("" to "");
    }

}
```
{% endtab %}
{% endtabs %}

### 3. RNAdWhaleMediationAdPackage 클래스

{% hint style="info" %}
네이티브 모듈과 뷰 매니저를 React Native에 연결하는 패키지 클래스입니다.
{% endhint %}

{% tabs %}
{% tab title="Java" %}
```java
public class RNAdWhaleMediationAdPackage implements ReactPackage {

    private RNAdWhaleMediationAdSettingModule rnAdWhaleMediationAdSettingModule;

    @NonNull
    @Override
    public List<NativeModule> createNativeModules(@NonNull ReactApplicationContext reactApplicationContext) {
        List<NativeModule> modules = new ArrayList<>();
        rnAdWhaleMediationAdSettingModule = new RNAdWhaleMediationAdSettingModule(reactApplicationContext);
        modules.add(rnAdWhaleMediationAdSettingModule);
        return modules;
    }

    @NonNull
    @Override
    public List<ViewManager> createViewManagers(@NonNull ReactApplicationContext reactApplicationContext) {
        return null;
    }
}
```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
class RNAdWhaleMediationAdPackage : ReactPackage {

    private lateinit var rnAdWhaleMediationAdSettingModule : RNAdWhaleMediationAdSettingModule

    override fun createNativeModules(reactApplicationContext : ReactApplicationContext) : List<NativeModule> {
        rnAdWhaleMediationAdSettingModule = RNAdWhaleMediationAdSettingModule(reactApplicationContext)
        return listOf<NativeModule>(rnAdWhaleMediationAdSettingModule)
    }

    override fun createViewManagers(reactApplicationContext : ReactApplicationContext) : List<ViewManager<*, *>> {
        return null
    }
}
```
{% endtab %}
{% endtabs %}



### 4. MainApplication 클래스

{% hint style="info" %}
React Native 어플리케이션의 엔트리 포인트로, AdWhale Mediation SDK 패키지를 등록합니다.
{% endhint %}

{% tabs %}
{% tab title="Java" %}
```java
public class MainApplication extends Application implements ReactApplication {
    private final ReactNativeHost mReactNativeHost = new DefaultReactNativeHost(this) {
        @Override
        protected List<ReactPackage> getPackages() {
            List<ReactPackage> packages = new PackageList(this).getPackages();
            packages.add(new RNAdWhaleMediationAdPackage());
            return packages;
        }

        ...
    };
    
    ...
}

```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
class MainApplication : Application(), ReactApplication {

    override val reactNativeHost: ReactNativeHost =
        object : DefaultReactNativeHost(this) {
            override fun getPackages(): List<ReactPackage> =
                PackageList(this).packages.apply {
                    // Packages that cannot be autolinked yet can be added manually here, for example:
                    add(RNAdWhaleMediationAdPackage())
            }
        
        ...
    }
    
    ...
}
```
{% endtab %}
{% endtabs %}



### 5. React-Native 코드

```javascript
import React, { Component } from 'react';
import PropTypes from 'prop-types';
import {SafeAreaView, View, StyleSheet, NativeModules, NativeEventEmitter } from 'react-native';

// 상수 정의
const { RNAdWhaleMediationAdSettingModule } = NativeModules;

// 스타일 정의
const styles = StyleSheet.create({
    safeArea: {
        flex: 1,
        backgroundColor: 'white',
    },
    mainViewStyle: {
        flex: 1,
        marginTop: 50
    },
    contentViewStyle: {
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center'                
    },
    footerViewStyle: {
        height: 50,
        width: '100%'
    },
    buttonWrapper: {
        marginBottom: 10,
        width: '80%',
    },
});

// App 클래스 정의(React.Component 확장)
class App extends Component {

    componentDidMount() {
        this._initialize();
    }
    
    // 화면 렌더링
    render() {
        
        return (            
            <SafeAreaView style={styles.safeArea}>
                <View style={styles.mainViewStyle}>
                    <View style={styles.contentViewStyle}>
                        {/* 광고뷰 위치 */}
                    </View>
                </View>
            </SafeAreaView>
        );
    }

    // SDK 초기화
    _initialize = () => {
        RNAdWhaleMediationAdSettingModule.initialize()
            .then((statusCode) => {
                if (statusCode === 100) {
                    console.log('AdWhale SDK initialized successfully');
                } else {
                    console.error('AdWhale SDK initialization failed');
                }
            })
            .catch((error) => {
                console.error('Error initializing AdWhale SDK:', error);
            });
    }
};

export default App;
```

















