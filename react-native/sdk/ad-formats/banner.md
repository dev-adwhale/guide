# 배너

### 1. RNAdWhaleMediationAdView 클래스

{% tabs %}
{% tab title="Java" %}
**AdWhaleMediationAdView 클래스 API 설명**

```java
public AdWhaleMediationAdView(Context context)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.content.Context</td><td>Android Main Activity 클래스</td></tr></tbody></table>

```java
public void loadAd() // 미디에이션 배너광고로드
```

<pre class="language-java"><code class="lang-java"><strong>public void setPlacementUid(String placementUid) // 지면 등록
</strong></code></pre>

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```java
public void setAdwhaleAdSize(ADWHALE_AD_SIZE adWhaleAdSize) // 미디에이션 배너사이즈 설정
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.ADWHALE_AD_SIZE</td><td>배너 광고 사이즈(사이즈 종류: BANNER320x50, BANNER320x100, BANNER300x250, BANNER250x250) </td></tr></tbody></table>

```java
public void setAdWhaleMediationAdViewListener(AdWhaleMediationAdViewListener listener) // 리스너 등록
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationAdViewListener</p></td><td>배너 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```java
public void resume() // resume 콜백 시 호출필요
```

```java
public void pause() // pause 콜백 시 호출필요
```

```java
public void destroy() // onDestroy 콜백 시 호출필요
```



**AdWhaleMediationAdViewListener 클래스 API 설명**

```java
public void onAdLoaded() // 배너 광고요청 성공 시
```

```java
public void onAdLoadFailed(int statusCode, String message) // 미디에이션 배너광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>int</td><td><p>초기화 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```java
public void onAdClicked() // 배너 클릭 시
```



**배너 구현 샘플은 아래와 같습니다.**&#x20;

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
**AdWhaleMediationAdView 클래스 API 설명**

```kotlin
AdWhaleMediationAdView(context : Context)
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>android.content.Context</td><td>Android Main Activity 클래스</td></tr></tbody></table>

```kotlin
fun loadAd() : Unit // 미디에이션 배너광고 로드
```

<pre class="language-kotlin"><code class="lang-kotlin"><strong>fun setPlacementUid(placementUid : String) : Unit // 지면 등록
</strong></code></pre>

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>String</td><td>placementUid 값(발급 필요)</td></tr></tbody></table>

```kotlin
fun setAdwhaleAdSize(adWhaleAdSize : ADWHALE_AD_SIZE) : Unit // 미디에이션 배너사이즈 설정
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>net.adwhale.sdk.mediation.ads.ADWHALE_AD_SIZE</td><td>배너 광고 사이즈(사이즈 종류: BANNER320x50, BANNER320x100, BANNER300x250, BANNER250x250) </td></tr></tbody></table>

```kotlin
fun setAdWhaleMediationAdViewListener(listener : AdWhaleMediationAdViewListener) : Unit // 리스너 등
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td><p>net.adwhale.sdk.mediation.ads.</p><p>AdWhaleMediationAdViewListener</p></td><td>배너 미디에이션 광고 호출 콜백 리스너</td></tr></tbody></table>

```kotlin
fun resume() : Unit // resume 콜백 시 호출필요
```

```kotlin
fun pause() : Unit // pause 콜백 시 호출필요
```

```kotlin
fun destroy() : Unit // destroy 콜백 시 호출필요
```



**AdWhaleMediationAdViewListener 클래스 API 설명**

```kotlin
fun onAdLoaded() : Unit // 배너 광고요청 성공 시
```

```kotlin
fun onAdLoadFailed(statusCode : Int, message : String) : Unit // 미디에이션 배너광고요청 실패 시
```

<table data-header-hidden><thead><tr><th width="348">파라미터 타입</th><th>파라미터 값</th></tr></thead><tbody><tr><td>파라미터 타입</td><td>파라미터 값</td></tr><tr><td>Inte</td><td><p>초기화 결과 코드</p><p>(<mark style="color:red;">200 또는 300</mark>)</p></td></tr><tr><td>String</td><td><p>초기화 결과 메시지</p><p>(<mark style="color:red;">"Internal error occurred..." 또는 "Mediation network error occurred..."</mark>)</p></td></tr></tbody></table>

```kotlin
fun onAdClicked() : Unit // 배너 클릭 시
```



**배너 구현 샘플은 아래와 같습니다.**&#x20;

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



