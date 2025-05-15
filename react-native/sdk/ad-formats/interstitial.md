# 전면

### 1. RNAdWhaleMediationInterstitialAd 클래스

{% tabs %}
{% tab title="Java" %}
**AdWhaleMediationInterstitialAd 클래스 API 설명**

```java
public AdWhaleMediationInterstitialAd(String placementUid)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```java
public void setAdWhaleMediationInterstitialAdListener(AdWhaleMediationInterstitialAdListener listener)
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationInterstitialAdListener</p></td><td>전면 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```java
public void loadAd() // 미디에이션 전면광고로드
```

<pre class="language-java"><code class="lang-java"><strong>public void showAd() // 미디에이션 전면광고로드 후 표시할 때 호출
</strong></code></pre>

```java
public void showAd(Activity activity) // 미디에이션 전면광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="352">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr></tbody></table>

```java
public void destroy() // onDestroy() 시 호출
```

\
**AdWhaleMediationInterstitialAdListener 클래스 API 설명**

```java
public void onAdLoaded() // 미디에이션 전면광고요청 성공 시
```

```java
public void onAdLoadFailed(int statusCode, String message) // 미디에이션 전면광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고로드 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```java
public void onAdShowed() // 미디에이션 전면광고표시 후
```

```java
public void onAdShowFailed(int statusCode, String message) // 미디에이션 전면광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>광고표시 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```java
public void onAdClosed() // 미디에이션 전면광고닫기 시
```

```java
public void onAdClicked() // 미디에이션 전면광고클릭 시
```



**전면 구현 샘플은 아래와 같습니다.**&#x20;

```java
public class RNAdWhaleMediationInterstitialAd extends ReactContextBaseJavaModule implements AdWhaleMediationInterstitialAdListener {

    private static final String REACT_CLASS_NAME = RNAdWhaleMediationInterstitialAd.class.getSimpleName();
    private AdWhaleMediationInterstitialAd adWhaleMediationInterstitialAd;

    public RNAdWhaleMediationInterstitialAd(ReactApplicationContext context) {
        super(context);
    }

    @NonNull
    @Override
    public String getName() {
        return REACT_CLASS_NAME;
    }

    @ReactMethod
    public void loadAd(String placementUid) {
        Log.e(REACT_CLASS_NAME, ".loadAd()");
        adWhaleMediationInterstitialAd = new AdWhaleMediationInterstitialAd(placementUid);
        adWhaleMediationInterstitialAd.setAdWhaleMediationInterstitialAdListener(this);

        UiThreadUtil.runOnUiThread(() -> {
            adWhaleMediationInterstitialAd.loadAd();
        });
    }

    @ReactMethod
    public void showAd() {
        Log.e(REACT_CLASS_NAME, "showAd()");
        if (adWhaleMediationInterstitialAd == null) {
            Log.e(REACT_CLASS_NAME, "adWhaleMediationInterstitialAd is null");
            return;
        }

        UiThreadUtil.runOnUiThread(() -> {
            adWhaleMediationInterstitialAd.showAd();
        });
    }

    @Override
    public void onAdLoaded() {
        Log.i(REACT_CLASS_NAME, "onAdLoaded()");
        sendEvent("onInterstitialAdLoaded", null);

    }

    @Override
    public void onAdLoadFailed(int statusCode, String message) {
        Log.i(REACT_CLASS_NAME, "onAdLoadFailed(" + statusCode + ", " + message + ")");
        WritableMap params = Arguments.createMap();
        params.putInt("statusCode", statusCode);
        params.putString("message", message);
        sendEvent("onInterstitialAdLoadFailed", params);
    }

    @Override
    public void onAdShowed() {
        Log.i(REACT_CLASS_NAME, "onAdShowed()");
        sendEvent("onInterstitialAdShowed", null);
    }

    @Override
    public void onAdShowFailed(int statusCode, String message) {
        Log.i(REACT_CLASS_NAME, "onAdShowFailed(" + statusCode + ", " + message + ")");
        WritableMap params = Arguments.createMap();
        params.putInt("statusCode", statusCode);
        params.putString("message", message);
        sendEvent("onInterstitialAdShowFailed", params);
    }

    @Override
    public void onAdClosed() {
        Log.i(REACT_CLASS_NAME, "onAdClosed()");
        sendEvent("onInterstitialAdClosed", null);
    }

    @Override
    public void onAdClicked() {
        Log.i(REACT_CLASS_NAME, "onAdClicked()");
        sendEvent("onInterstitialAdClicked", null);
    }

    @Nullable
    @Override
    public Map<String, Object> getConstants() {
        final Map<String, Object> constants = new HashMap<>();
        return constants;
    }

    private void sendEvent(String eventName, @Nullable WritableMap params) {
        getReactApplicationContext()
                .getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter.class)
                .emit(eventName, params);
    }

    @ReactMethod
    public void addListener(String eventName) {
        //TODO NOTHING
    }

     @ReactMethod
     public void removeListeners(Integer count) {
        //TODO NOTHING
     }
}
```
{% endtab %}

{% tab title="Kotlin" %}
**AdWhaleMediationInterstitialAd 클래스 API 설명**

```kotlin
AdWhaleMediationInterstitialAd(placementUid : String)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```kotlin
fun setAdWhaleMediationInterstitialAdListener(listener : AdWhaleMediationInterstitialAdListener) : Unit
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.AdWhaleMediationInterstitialAdListener</td><td>전면 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```kotlin
fun loadAd() : Unit // 미디에이션 전면광고로드
```

```kotlin
fun showAd() : Unit // 미디에이션 전면광고로드 후 표시할 때 호출
```

```kotlin
fun showAd(activity : Activity) : Unit // 미디에이션 전면광고로드 후 표시할 때 호출
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.app.Activity</td><td>Android Activity 클래스</td></tr></tbody></table>

```kotlin
fun destroy() : Unit // onDestroy() 시 호출
```





**AdWhaleMediationInterstitialAdListener 클래스 API 설명**

```kotlin
fun onAdLoaded() : Unit // 미디에이션 전면광고요청 성공 시
```

```kotlin
fun onAdLoadFailed(statusCode : Int, message : String) : Unit // 미디에이션 전면광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td><p>초기화 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdShowed() : Unit // 미디에이션 전면광고표시 후
```

```kotlin
fun onAdShowFailed(statusCode : Int, message : String) : Unit // 미디에이션 전면광고표시 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Int</td><td><p>초기화 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdClosed() : Unit // 미디에이션 전면광고닫기 시
```

```kotlin
fun onAdClicked() : Unit // 미디에이션 전면광고클릭 시
```



**전면 구현 샘플은 아래와 같습니다.**&#x20;

```kotlin
class RNAdWhaleMediationInterstitialAd(private val context: ReactApplicationContext) : ReactContextBaseJavaModule(context), AdWhaleMediationInterstitialAdListener {

    companion object {
        private  val REACT_CLASS_NAME = RNAdWhaleMediationInterstitialAd::class.java.simpleName
    }

    private lateinit var adWhaleMediationInterstitialAd: AdWhaleMediationInterstitialAd

    override fun getName(): String {
        return REACT_CLASS_NAME
    }

    @ReactMethod
    fun loadAd(placementUid: String) {
        Log.e(REACT_CLASS_NAME, "loadAd()")
        adWhaleMediationInterstitialAd = AdWhaleMediationInterstitialAd(placementUid)
        adWhaleMediationInterstitialAd.setAdWhaleMediationInterstitialAdListener(this)
        
        UiThreadUtil.runOnUiThread{
            adWhaleMediationInterstitialAd.loadAd()
        }
    }

    @ReactMethod
    fun showAd() {
        Log.e(REACT_CLASS_NAME, "showAd()")
        UiThreadUtil.runOnUiThread{
            adWhaleMediationInterstitialAd.showAd()
        }
    }

    override fun onAdLoaded() {
        Log.i(REACT_CLASS_NAME, "onAdLoaded()")
        sendEvent("onInterstitialAdLoaded", null)
    }

    override fun onAdLoadFailed(statusCode: Int, message: String) {
        Log.i(REACT_CLASS_NAME, "onAdLoadFailed($statusCode, ${message})")
        val params = Arguments.createMap()
        params.putInt("statusCode", statusCode)
        params.putString("message", message)
        sendEvent("onInterstitialAdLoadFailed", params)
    }

    override fun onAdShowed() {
        Log.i(REACT_CLASS_NAME, "onAdShowed()")
        sendEvent("onInterstitialAdShowed", null)
    }

    override fun onAdShowFailed(statusCode: Int, message: String?) {
        Log.i(REACT_CLASS_NAME, "onAdShowFailed($statusCode, ${message})")
        val params = Arguments.createMap()
        params.putInt("statusCode", statusCode)
        params.putString("message", message)
        sendEvent("onInterstitialAdShowFailed", params)
    }

    override fun onAdClosed() {
        Log.i(REACT_CLASS_NAME, "onAdClosed()")
        sendEvent("onInterstitialAdClosed", null)
    }
    
    override fun onAdClicked() {
        Log.i(REACT_CLASS_NAME, "onAdClicked()")
        sendEvent("onInterstitialAdClicked", null)
    }

    fun sendEvent(eventName: String, params: WritableMap?) {
        reactApplicationContext
            .getJSModule(RCTDeviceEventEmitter::class.java)
            .emit(eventName, params)
    }

    @ReactMethod
    fun addListener(eventName: String) {
        //TODO NOTHING
    }

    @ReactMethod
    fun removeListeners(count: Int) {
        //TODO NOTHING
    }
}
```
{% endtab %}
{% endtabs %}

### 2. RNAdWhaleMediationAdPackage 클래스

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
        RNAdWhaleMediationInterstitialAd rnAdWhaleMediationInterstitialAd = new RNAdWhaleMediationInterstitialAd(reactApplicationContext);
        modules.add(rnAdWhaleMediationAdSettingModule);
        modules.add(rnAdWhaleMediationInterstitialAd);
        return modules;
    }

    ...
}
```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
class RNAdWhaleMediationAdPackage : ReactPackage {

    private lateinit var rnAdWhaleMediationAdSettingModule : RNAdWhaleMediationAdSettingModule

    override fun createNativeModules(reactApplicationContext : ReactApplicationContext) : List<NativeModule> {
        rnAdWhaleMediationAdSettingModule = RNAdWhaleMediationAdSettingModule(reactApplicationContext)
        return listOf<NativeModule>(rnAdWhaleMediationAdSettingModule, RNAdWhaleMediationInterstitialAd(reactApplicationContext))
    }

    ...
}
```
{% endtab %}
{% endtabs %}

### 3. React-Native 코드

#### InterstitialAd.js

```javascript
import React, { Component } from 'react';
import { View, Button, StyleSheet, NativeModules, NativeEventEmitter } from 'react-native';

const { RNAdWhaleMediationInterstitialAd } = NativeModules;

const styles = StyleSheet.create({
  buttonWrapper: {
    marginBottom: 10,
    width: '80%',
  },
  disabledButton: {
    opacity: 0.5,
  },
});

class InterstitialAd extends Component {
  state = {
    isInterstitialAdLoaded: false,
  };

  componentDidMount() {
    this._interstitialAdInit();
  }

  _interstitialAdInit = () => {
    const eventEmitter = new NativeEventEmitter(RNAdWhaleMediationInterstitialAd);
    eventEmitter.addListener('onInterstitialAdLoaded', this._onInterstitialAdLoaded);
    eventEmitter.addListener('onInterstitialAdShowed', this._onInterstitialAdShowed);
    eventEmitter.addListener('onInterstitialAdClosed', this._onInterstitialAdClosed);
    eventEmitter.addListener('onInterstitialAdLoadFailed', this._onInterstitialAdLoadFailed);
    eventEmitter.addListener('onInterstitialAdShowFailed', this._onInterstitialAdShowFailed);
    eventEmitter.addListener('onInterstitialAdClicked', this._onInterstitialAdClicked);
  }

  onPressInterstitialRequestButton = () => {
    RNAdWhaleMediationInterstitialAd.loadAd("발급받은 placement uid 값");
  }

  onPressInterstitialShowButton = () => {
    RNAdWhaleMediationInterstitialAd.showAd();
  }

  _onInterstitialAdLoaded = () => {
    console.log("[InterstitialAd.js]js did registered _onInterstitialAdLoaded successfully!!!");
    this.setState({ isInterstitialAdLoaded: true });
  }

  _onInterstitialAdLoadFailed = (event) => {
    const { statusCode, message } = event;
    console.log("[InterstitialAd.js]js did registered _onInterstitialAdLoadFailed successfully!!! status code: " + statusCode + ", message: " + message);
    this.setState({ isInterstitialAdLoaded: false });
  }

  _onInterstitialAdShowed = () => {
    console.log("[InterstitialAd.js]js did registered _onInterstitialAdShowed successfully!!!");
  }

  _onInterstitialAdShowFailed = (event) => {
    const { statusCode, message } = event;
    console.log("[InterstitialAd.js]js did registered _onInterstitialAdShowFailed successfully!!! status code: " + statusCode + ", message: " + message);
    this.setState({ isInterstitialAdLoaded: false });
  }

  _onInterstitialAdClosed = () => {
    console.log("[InterstitialAd.js]js did registered _onInterstitialAdClosed successfully!!!");
    this.setState({ isInterstitialAdLoaded: false });
  }
  
  _onInterstitialAdClicked = () => {
    console.log("[InterstitialAd.js]js did registered _onInterstitialAdClicked successfully!!!");
  }

  render() {
    return (
      <View>
        <View style={styles.buttonWrapper}>
          <Button title="Interstitial Ad Load" onPress={this.onPressInterstitialRequestButton} />
        </View>
        <View style={styles.buttonWrapper}>
        <Button 
          title="Interstitial Ad Show" 
          onPress={this.onPressInterstitialShowButton}
          disabled={!this.state.isInterstitialAdLoaded}
          style={!this.state.isInterstitialAdLoaded ? styles.disabledButton : null}
        />
        </View>
      </View>
    );
  }
}

export default InterstitialAd;
```

#### App.js

```javascript
import React, { Component } from 'react';
import {SafeAreaView, View, StyleSheet, NativeModules, NativeEventEmitter } from 'react-native';
import InterstitialAd from './src/java/InterstitialAd';

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
                        <InterstitialAd />
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



