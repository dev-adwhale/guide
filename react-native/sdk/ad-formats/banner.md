# 배너

### 1. RNAdWhaleMediationAdView 클래스

{% tabs %}
{% tab title="Java" %}
```java
public class RNAdWhaleMediationAdView extends ViewGroupManager<RNWrapperView> implements AdWhaleMediationAdViewListener {

    private static final String REACT_CLASS_NAME = RNAdWhaleMediationAdView.class.getSimpleName();
    private RNAdWhaleMediationAdSettingModule rnAdWhaleMediationAdSettingModule;
    private RNWrapperView rootView;

    private AdWhaleMediationAdView adWhaleMediationAdView;
    public RNAdWhaleMediationAdView(RNAdWhaleMediationAdSettingModule rnAdWhaleMediationAdSettingModule){
        this.rnAdWhaleMediationAdSettingModule = rnAdWhaleMediationAdSettingModule;
    }

    @NonNull
    @Override
    public String getName() {
        return REACT_CLASS_NAME;
    }

    @NonNull
    @Override
    protected RNWrapperView createViewInstance(@NonNull ThemedReactContext themedReactContext) {
        rootView = new RNWrapperView(themedReactContext);
        adWhaleMediationAdView = new AdWhaleMediationAdView(themedReactContext.getCurrentActivity());
        adWhaleMediationAdView.setAdWhaleMediationAdViewListener(this);
        
        RelativeLayout.LayoutParams params = new RelativeLayout.LayoutParams(
                LinearLayout.LayoutParams.WRAP_CONTENT, LinearLayout.LayoutParams.WRAP_CONTENT);
        params.addRule(RelativeLayout.CENTER_HORIZONTAL);
        rootView.addView(adWhaleMediationAdView, params);
        Log.e(REACT_CLASS_NAME, "adWhaleMediationAdView is attached to rootView.");

        return rootView;
    }

    @ReactProp(name = "placementUid")
    public void setPlacementUid(RNWrapperView view, String placementUid) {
        adWhaleMediationAdView.setPlacementUid(placementUid);
    }

    @ReactProp(name = "adSize")
    public void setAdSize(RNWrapperView view, String adSize) {
        adWhaleMediationAdView.setAdwhaleAdSize(ADWHALE_AD_SIZE.valueOf(adSize));
    }

    @ReactProp(name = "loadAd")
    public void loadAd(RNWrapperView view, boolean isReload) {
        Log.e(REACT_CLASS_NAME, "loadAd()");
        if (adWhaleMediationAdView != null && isReload) {
            adWhaleMediationAdView.loadAd();
        }
    }

    @Override
    public void onAdLoaded() {
        Log.e(REACT_CLASS_NAME, "onAdLoaded()");
        final Context context = rootView.getContext();
        if (context instanceof ReactContext) {
            ((ReactContext) context).getJSModule(RCTEventEmitter.class)
                    .receiveEvent(rootView.getId(),
                            "onAdLoaded", null);
        }
    }

    @Override
    public void onAdLoadFailed(int statusCode, String message) {
        Log.e(REACT_CLASS_NAME, "onAdLoadFailed(" + statusCode + "," + message + ")");
        final Context context = rootView.getContext();
        if (context instanceof ReactContext) {
            WritableMap params = Arguments.createMap();
            params.putInt("statusCode", statusCode);
            params.putString("message", message);
            ((ReactContext) context).getJSModule(RCTEventEmitter.class)
                    .receiveEvent(rootView.getId(),
                            "onAdLoadFailed", params);
        }
    }

    @Override
    public void onAdClicked() {
        Log.e(REACT_CLASS_NAME, "onAdClicked()");
        final Context context = rootView.getContext();
        if (context instanceof ReactContext) {
            ((ReactContext) context).getJSModule(RCTEventEmitter.class)
                    .receiveEvent(rootView.getId(),
                            "onAdClicked", null);
        }
    }

    @Nullable
    @Override
    public Map<String, Object> getExportedCustomDirectEventTypeConstants() {
        Log.e(REACT_CLASS_NAME, "getExportedCustomDirectEventTypeConstants()");
        return MapBuilder.<String, Object>builder()
                .put("onAdLoaded",
                        MapBuilder.of("registrationName", "onAdLoaded"))
                .put("onAdLoadFailed",
                        MapBuilder.of("registrationName", "onAdLoadFailed"))
                .put("onAdClicked",
                        MapBuilder.of("registrationName", "onAdClicked"))
                .build();


    }
}
```


{% endtab %}

{% tab title="Kotlin" %}
```kotlin
class RNAdWhaleMediationAdView(private val rnAdWhaleMediationAdSettingModule: RNAdWhaleMediationAdSettingModule) : ViewGroupManager<RNWrapperView>(), AdWhaleMediationAdViewListener {

    companion object {
        private val REACT_CLASS_NAME = RNAdWhaleMediationAdView::class.java.simpleName
    }

    private lateinit var rootView : RNWrapperView

    private lateinit var adWhaleMediationAdView : AdWhaleMediationAdView

    override fun getName() : String {
        return REACT_CLASS_NAME
    }

    override fun createViewInstance(themedReactContext : ThemedReactContext) : RNWrapperView {
        rootView = RNWrapperView(themedReactContext)

        adWhaleMediationAdView = AdWhaleMediationAdView(themedReactContext.currentActivity)
        adWhaleMediationAdView.adWhaleMediationAdViewListener = this

        val params : RelativeLayout.LayoutParams = RelativeLayout.LayoutParams(
            RelativeLayout.LayoutParams.WRAP_CONTENT, RelativeLayout.LayoutParams.WRAP_CONTENT)
        params.addRule(RelativeLayout.CENTER_HORIZONTAL)
        rootView.addView(adWhaleMediationAdView, params)
        Log.e(REACT_CLASS_NAME, "adwhaleMediationAdView is attached to rootView.")

        return rootView
    }

    @ReactProp(name = "placementUid")
    fun setPlacementUid(view : RNWrapperView, placementUid : String) {
        adWhaleMediationAdView.placementUid = placementUid
    }

    @ReactProp(name = "adSize")
    fun setAdSize(view : RNWrapperView, adSize : String) {
        adWhaleMediationAdView.adwhaleAdSize = ADWHALE_AD_SIZE.valueOf(adSize)
    }

    @ReactProp(name = "loadAd")
    fun loadAd(view : RNWrapperView, isReload : Boolean) {
        Log.e(REACT_CLASS_NAME, "loadAd()")
        if (isReload) {
            adWhaleMediationAdView.loadAd()
        }
    }

    override fun onAdLoaded() {
        Log.e(REACT_CLASS_NAME, "onAdLoaded()")
        val context : Context = rootView.context
        if(context is ReactContext){
            context.getJSModule(RCTEventEmitter::class.java).receiveEvent(
                rootView.id, "onAdLoaded", null)
        }
    }

    override fun onAdLoadFailed(statusCode : Int, message : String) {
        Log.e(REACT_CLASS_NAME, "onAdLoadFailed($statusCode, ${message})")
        val context : Context = rootView.context
        if(context is ReactContext) {
            val params : WritableMap = Arguments.createMap()
            params.putInt("statusCode", statusCode)
            params.putString("message", message)
            context.getJSModule(RCTEventEmitter::class.java).receiveEvent(
                rootView.id, "onAdLoadFailed", params)
        }
    }

    override fun onAdClicked() {
        Log.e(REACT_CLASS_NAME, "onAdClicked()")
        val context : Context = rootView.context
        if(context is ReactContext) {
            context.getJSModule(RCTEventEmitter::class.java).receiveEvent(
                rootView.id, "onAdClicked", null)
        }
    }

    override fun getExportedCustomDirectEventTypeConstants() : Map<String, Any> {
        Log.e(REACT_CLASS_NAME, "getExportedCustomDirectEventTypeConstants()")
        return mapOf<String, Any>(
            "onAdLoaded" to MapBuilder.of("registrationName", "onAdLoaded"),
            "onAdLoadFailed" to MapBuilder.of("registrationName", "onAdLoadFailed"),
            "onAdClicked" to MapBuilder.of("registrationName", "onAdClicked"),
        )
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

    ...

    @NonNull
    @Override
    public List<ViewManager> createViewManagers(@NonNull ReactApplicationContext reactApplicationContext) {
        return Arrays.<ViewManager>asList(new RNAdWhaleMediationAdView(rnAdWhaleMediationAdSettingModule));
    }
}
```
{% endtab %}

{% tab title="Kotlin" %}
```kotlin
class RNAdWhaleMediationAdPackage : ReactPackage {

    ...

    override fun createViewManagers(reactApplicationContext : ReactApplicationContext) : List<ViewManager<*, *>> {
        return listOf<ViewManager<*, *>>(RNAdWhaleMediationAdView(rnAdWhaleMediationAdSettingModule))
    }
}
```
{% endtab %}
{% endtabs %}

### 3. React-Native 코드

#### AdWhaleMediationAdView.js

```javascript
import React, {Component} from 'react';
import PropTypes from 'prop-types';
import {requireNativeComponent } from 'react-native';
const RNAdWhaleMediationAdView = requireNativeComponent("RNAdWhaleMediationAdView");

class AdWhaleMediationAdView extends Component {
    
    render() {
        return (
            <RNAdWhaleMediationAdView
                {...this.props}
            />
        );
    }
}

AdWhaleMediationAdView.propTypes = {

}

module.exports = AdWhaleMediationAdView;
```

#### BannerAd.js

```javascript
import React, { Component } from 'react';
import { View, Button, StyleSheet, Dimensions, Text, TouchableOpacity } from 'react-native';
import AdWhaleMediationAdView from './AdWhaleMediationAdView';

const windowWidth = Dimensions.get('window').width;

const styles = StyleSheet.create({
  container: {
    justifyContent: 'flex-start',
    alignItems: 'center',
    paddingTop: 20,
  },
  buttonContainer: {
    width: '100%',
  },
  buttonWrapper: {
    marginBottom: 5,
    width: '80%',
    alignSelf: 'center',
  },
  adWhaleMediationAdViewStyle: {
    height: 250,
    width: windowWidth,
    backgroundColor: 'yellow',
    marginBottom: 20,
  },
});

class BannerAd extends Component {
  state = {
    isReload: false,
  };


  onPressBannerLoadButton = () => {
    console.log('banner load');
    this.setState(prevState => ({ isReload: !prevState.isReload }));
  }

  // 배너 광고 수신 성공 시 이벤트 콜백 처리
  _onAdLoaded = (event) => {
    console.warn("[BannerAd.js]js did registered _onAdLoaded successfully!!!" + event);
    this.setState({ isReload: false });
  }

  // 배너 광고 수신 실패 시 이벤트 콜백 처리
  _onAdLoadFailed = (event) => {
    const { statusCode, message } = event.nativeEvent;
    console.warn("[BannerAd.js]js did registered _onAdLoadFailed successfully!!! status code: " + statusCode + ", message: " + message);
    this.setState({ isReload: false });
  }

  // 배너 광고 클릭하여 랜딩 페이지로 넘어갈 때 이벤트 콜백 처리 
  _onAdClicked = (event) => {
    console.warn("[BannerAd.js]js did registered _onAdClicked successfully!!!" + event);
  }

  render() {
    
    return (
      <View style={styles.container}>
        <View style={styles.buttonContainer}>
          <View style={styles.buttonWrapper}>
            <Button title="Banner load" onPress={this.onPressBannerLoadButton} />
          </View>
        </View>

        <AdWhaleMediationAdView
          style={styles.adWhaleMediationAdViewStyle}
          placementUid="발급받은 placement uid 값"
          adSize="BANNER320x50"  // (사이즈 종류: BANNER320x50, BANNER320x100, BANNER300x250, BANNER250x250) 
          loadAd={this.state.isReload}
          onAdLoaded={this._onAdLoaded}
          onAdLoadFailed={this._onAdLoadFailed}
          onAdClicked={this._onAdClicked}
        />
      </View>
    );
  }
}

export default BannerAd;
```

#### App.js

```javascript
import React, { Component } from 'react';
import {SafeAreaView, View, StyleSheet, NativeModules, NativeEventEmitter } from 'react-native';
import BannerAd from './src/java/BannerAd';

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



