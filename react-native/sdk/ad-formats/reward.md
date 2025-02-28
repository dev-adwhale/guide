# 보상형

### 1. RNAdWhaleMediationRewardAd 클래스

{% tabs %}
{% tab title="Java" %}
```java
public class RNAdWhaleMediationRewardAd extends ReactContextBaseJavaModule implements AdWhaleMediationRewardedAdLoadCallback, AdWhaleMediationFullScreenContentCallback, AdWhaleMediationUserEarnedRewardListener {

    private static final String REACT_CLASS_NAME = RNAdWhaleMediationRewardAd.class.getSimpleName();
    private AdWhaleMediationRewardAd adWhaleMediationRewardAd;

    public RNAdWhaleMediationRewardAd(ReactApplicationContext context) {
        super(context);
    }

    @NonNull
    @Override
    public String getName() {
        return REACT_CLASS_NAME;
    }

    @ReactMethod
    public void initialize(String placementUid) {
        Log.e(REACT_CLASS_NAME, "initialize() placementUid: " + placementUid);
        adWhaleMediationRewardAd = new AdWhaleMediationRewardAd(placementUid);
        adWhaleMediationRewardAd.setAdWhaleMediationFullScreenContentCallback(this);
    }

    @ReactMethod
    public void loadAd() {
        Log.e(REACT_CLASS_NAME, "loadAd()");
        if (adWhaleMediationRewardAd == null) {
            Log.e(REACT_CLASS_NAME, "adWhaleMediationRewardAd is null");
            return;
        }

        UiThreadUtil.runOnUiThread(() -> {
            adWhaleMediationRewardAd.loadAd(this);
        });
    }

    @ReactMethod
    public void showAd() {
        Log.e(REACT_CLASS_NAME, "showAd()");
        if (adWhaleMediationRewardAd == null) {
            Log.e(REACT_CLASS_NAME, "adWhaleMediationRewardAd is null");
            return;
        }

        UiThreadUtil.runOnUiThread(() -> {
            adWhaleMediationRewardAd.showAd(this);
        });
    }


    /// AdWhaleMediationFullScreenContentCallback
    @Override
    public void onAdClicked() {
        Log.i(REACT_CLASS_NAME, "onAdClicked()");
        sendEvnet("onRewardAdClicked", null);
    }

    @Override
    public void onAdDismissed() {
        Log.i(REACT_CLASS_NAME, "onAdDismissed()");
        sendEvnet("onRewardAdDismissed", null);
    }

    @Override
    public void onFailedToShow(int statusCode, String message) {
        Log.i(REACT_CLASS_NAME, "onFailedToShow(" + statusCode + ", " + message + ")");
        WritableMap params = Arguments.createMap();
        params.putInt("statusCode", statusCode);
        params.putString("message", message);
        sendEvnet("onRewardAdFailedToShow", params);
    }

    @Override
    public void onAdShowed() {
        Log.i(REACT_CLASS_NAME, "onAdShowed()");
        sendEvnet("onRewardAdShowed", null);
    }

    /// AdWhaleMediationRewardedAdLoadCallback
    @Override
    public void onAdLoaded(AdWhaleMediationRewardAd adWhaleMediationRewardAd, String message) {
        Log.i(REACT_CLASS_NAME, "onAdLoaded(+ " + message + ")");
        sendEvnet("onRewardAdLoaded", null);
    }

    @Override
    public void onAdFailedToLoad(int statusCode, String message) {
        Log.i(REACT_CLASS_NAME, "onAdFailedToLoad(" + statusCode + ", " + message + ")");
        WritableMap params = Arguments.createMap();
        params.putInt("statusCode", statusCode);
        params.putString("message", message);
        sendEvnet("onRewardAdFailedToLoad", params);
    }


    /// AdWhaleMediationUserEarnedRewardListener
    @Override
    public void onUserRewarded(AdWhaleMediationRewardItem adWhaleMediationRewardItem) {
        Log.i(REACT_CLASS_NAME, "onUserRewarded(" + adWhaleMediationRewardItem.toString() + ")");
        WritableMap params = Arguments.createMap();
        params.putString("type", adWhaleMediationRewardItem.getRewardType());
        params.putInt("amount", adWhaleMediationRewardItem.getRewardAmount());
        sendEvnet("onUserRewarded", params);
    }

    @Nullable
    @Override
    public Map<String, Object> getConstants() {
        final Map<String, Object> constants = new HashMap<>();
        return constants;
    }

    private void sendEvnet(String eventName, @Nullable WritableMap params) {
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
```kotlin
class RNAdWhaleMediationRewardAd(private val context: ReactApplicationContext) : ReactContextBaseJavaModule(context), AdWhaleMediationRewardedAdLoadCallback, AdWhaleMediationFullScreenContentCallback, AdWhaleMediationUserEarnedRewardListener {

    companion object {
        private val REACT_CLASS_NAME = RNAdWhaleMediationRewardAd::class.java.simpleName
    }

    private lateinit var adWhaleMediationRewardAd: AdWhaleMediationRewardAd

    override fun getName(): String {
        return REACT_CLASS_NAME
    }

    @ReactMethod
    fun initialize(placementUid: String) {
        Log.e(REACT_CLASS_NAME, "initialize() placementUid: $placementUid")
        adWhaleMediationRewardAd = AdWhaleMediationRewardAd(placementUid)
        adWhaleMediationRewardAd.setAdWhaleMediationFullScreenContentCallback(this)
    }

    @ReactMethod
    fun loadAd() {
        Log.e(REACT_CLASS_NAME, "loadAd()")

        UiThreadUtil.runOnUiThread {
            adWhaleMediationRewardAd.loadAd(this)
        }
    }

    @ReactMethod
    fun showAd() {
        Log.e(REACT_CLASS_NAME, "showAd()")

        UiThreadUtil.runOnUiThread {
            adWhaleMediationRewardAd.showAd(this)
        }
    }


    /// AdWhaleMediationFullScreenContentCallback
    override fun onAdClicked() {
        Log.i(REACT_CLASS_NAME, "onAdClicked()")
        sendEvent("onRewardAdClicked", null)
    }

    override fun onAdDismissed() {
        Log.i(REACT_CLASS_NAME, "onAdDismissed()")
        sendEvent("onRewardAdDismissed", null)
    }

    override fun onFailedToShow(statusCode: Int, message: String?) {
        Log.i(REACT_CLASS_NAME, "onFailedToShow($statusCode, ${message})")
        val params = Arguments.createMap()
        params.putInt("statusCode", statusCode)
        params.putString("message", message)
        sendEvent("onRewardAdFailedToShow", params)
    }

    override fun onAdShowed() {
        Log.i(REACT_CLASS_NAME, "onAdShowed()")
        sendEvent("onRewardAdShowed", null)
    }

    /// AdWhaleMediationRewardedAdLoadCallback
    override fun onAdLoaded(adWhaleMediationrewardAd: AdWhaleMediationRewardAd?, message: String?) {
        Log.i(REACT_CLASS_NAME, "onAdLoaded($message)")
        sendEvent("onRewardAdLoaded", null)
    }

    override fun onAdFailedToLoad(statusCode: Int, message: String?) {
        Log.i(REACT_CLASS_NAME, "onAdFailedToLoad($statusCode, ${message})")
        val params = Arguments.createMap()
        params.putInt("statusCode", statusCode)
        params.putString("message", message)
        sendEvent("onRewardAdFailedToLoad", params)
    }

    /// AdWhaleMediationUserEarnedRewardListener
    override fun onUserRewarded(adWhaleMediationRewardItem: AdWhaleMediationRewardItem?) {
        Log.i(REACT_CLASS_NAME, "onUserRewarded(${adWhaleMediationRewardItem.toString()})")
        val params = Arguments.createMap()
        params.putString("type", adWhaleMediationRewardItem?.rewardType)
        params.putInt("amount", adWhaleMediationRewardItem?.rewardAmount ?: 0)
        sendEvent("onUserRewarded", params)
    }

    private fun sendEvent(eventName: String, params: WritableMap?) {
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
        RNAdWhaleMediationRewardAd rnAdWhaleMediationRewardAd = new RNAdWhaleMediationRewardAd(reactApplicationContext);
        modules.add(rnAdWhaleMediationAdSettingModule);
        modules.add(rnAdWhaleMediationRewardAd);
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
        return listOf<NativeModule>(rnAdWhaleMediationAdSettingModule, RNAdWhaleMediationRewardAd(reactApplicationContext))
    }

    ...
}
```
{% endtab %}
{% endtabs %}

### 3. React-Native 코드

#### RewardAd.js

```javascript
import React, { Component } from 'react';
import { View, Button, StyleSheet, NativeModules, NativeEventEmitter } from 'react-native';

const { RNAdWhaleMediationRewardAd } = NativeModules;

const styles = StyleSheet.create({
  buttonWrapper: {
      marginBottom: 10,
      width: '80%',
  },
  disabledButton: {
      opacity: 0.5,
  },
});

class RewardAd extends Component {
  state = {
    isRewardAdLoaded: false,
  };

  componentDidMount() {
    this._rewardAdInit();
  }

  _rewardAdInit = () => {
    RNAdWhaleMediationRewardAd.initialize("발급받은 placement uid 값");
    const eventEmitter = new NativeEventEmitter(RNAdWhaleMediationRewardAd);
    eventEmitter.addListener('onRewardAdLoaded', this._onRewardAdLoaded);
    eventEmitter.addListener('onRewardAdShowed', this._onRewardAdShowed);
    eventEmitter.addListener('onRewardAdDismissed', this._onRewardAdDismissed);
    eventEmitter.addListener('onRewardAdClicked', this._onRewardAdClicked);
    eventEmitter.addListener('onRewardAdFailedToLoad', this._onRewardAdFailedToLoad);
    eventEmitter.addListener('onRewardAdFailedToShow', this._onRewardAdFailedToShow);
    eventEmitter.addListener('onUserRewarded', this._onRewardAdUserRewarded);
  }

  onPressRewardRequestButton = () => {
    RNAdWhaleMediationRewardAd.loadAd();
  }

  onPressRewardShowButton = () => {
    RNAdWhaleMediationRewardAd.showAd();
  }

  _onRewardAdLoaded = () => {
    console.log("[RewardAd.js]js did registered _onRewardAdLoaded successfully!!!");
    this.setState({ isRewardAdLoaded: true });
  }

  _onRewardAdFailedToLoad = (event) => {
    const { statusCode, message } = event;
    console.log("[RewardAd.js]js did registered _onRewardAdFailedToLoad successfully!!! status code: " + statusCode + ", message: " + message);
    this.setState({ isRewardAdLoaded: false });
  }

  _onRewardAdShowed = () => {
    console.log("[RewardAd.js]js did registered _onRewardAdShowed successfully!!!");
  }

  _onRewardAdFailedToShow = (event) => {
    const { statusCode, message } = event;
    console.log("[RewardAd.js]js did registered _onRewardAdFailedToShow successfully!!! status code: " + statusCode + ", message: " + message);
    this.setState({ isRewardAdLoaded: false });
  }

  _onRewardAdDismissed = () => {
    console.log("[RewardAd.js]js did registered _onRewardAdDismissed successfully!!!");
    this.setState({ isRewardAdLoaded: false });
  }

  _onRewardAdClicked = () => {
    console.log("[RewardAd.js]js did registered _onRewardAdClicked successfully!!!");
  }

  _onRewardAdUserRewarded = (event) => {
    const { type, amount } = event;
    console.log("[RewardAd.js]js did registered _onRewardAdUserRewarded successfully!!!");
  }

  render() {
    return (
      <View>
        <View style={styles.buttonWrapper}>
          <Button title="Reward Ad Load" onPress={this.onPressRewardRequestButton} />
        </View>
        <View style={styles.buttonWrapper}>
        <Button 
          title="Reward Ad Show" 
          onPress={this.onPressRewardShowButton}
          disabled={!this.state.isRewardAdLoaded}
          style={!this.state.isRewardAdLoaded ? styles.disabledButton : null}
        />
        </View>
      </View>
    );
  }
}

export default RewardAd;
```

#### App.js

```javascript
import React, { Component } from 'react';
import {SafeAreaView, View, StyleSheet, NativeModules, NativeEventEmitter } from 'react-native';
import RewardAd from './src/java/RewardAd';

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
                        <RewardAd />
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



