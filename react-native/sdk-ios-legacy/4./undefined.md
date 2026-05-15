# 배너

{% hint style="info" %}
* AdWhale SDK를 프로젝트에 추가 해야합니다.
* Banner Ad용으로 발급받은 Ad Unit ID를 사용합니다.
* 광고를 요청하기 전에 SDK 초기화를 진행합니다.
{% endhint %}

| **AdWhaleAdSize(Swift)** | **AdWhaleAdSize(ObjC)**      | **Size (width \* height)** |
| ------------------------ | ---------------------------- | -------------------------- |
| .banner                  | AdWhaleAdSizeBanner          | 320 \*50                   |
| .largeBanner             | AdWhaleAdSizeLargeBanner     | 320 \* 100                 |
| .mediumRectangle         | AdWhaleAdSizeMediumRectangle | 300 \* 250                 |

{% tabs %}
{% tab title="React-Native" %}
### 1. BannerAdView.m

```objectivec
#import <React/RCTViewManager.h>
#import <React/RCTBridge.h>
#import <React/RCTUIManager.h>
#import <AdWhaleSDK/AdWhaleSDK.h>

@interface BannerAdViewContainer : UIView <AdWhaleBannerDelegate>
@property (nonatomic, strong) AdWhaleBannerAd *bannerView;
@property (nonatomic, copy) RCTBubblingEventBlock onAdLoaded;
@property (nonatomic, copy) RCTBubblingEventBlock onAdLoadFailed;
@property (nonatomic, copy) RCTBubblingEventBlock onAdClicked;
@property (nonatomic, copy) NSString *adSize;
@property (nonatomic, copy) NSString *placementUid;
@property (nonatomic, assign) BOOL shouldLoadAd;
@end

@implementation BannerAdViewContainer

- (instancetype)initWithFrame:(CGRect)frame {
    self = [super initWithFrame:frame];
    if (self) {
        _adSize = @"BANNER320x50";
        _placementUid = @"";
        _shouldLoadAd = NO;
    }
    return self;
}

- (void)setAdSize:(NSString *)adSize {
    _adSize = adSize;
    [self updateAdSize];
}

- (void)setPlacementUid:(NSString *)placementUid {
    _placementUid = placementUid;
    [self.bannerView setAdUnitID:placementUid];
}

- (void)setShouldLoadAd:(BOOL)shouldLoadAd {
    _shouldLoadAd = shouldLoadAd;
    if (shouldLoadAd) {
        [self loadAd];
    }
}

- (void)updateAdSize {
    if (!self.bannerView) return;
    CGFloat width = 320;
    CGFloat height = 50;
    if ([_adSize isEqualToString:@"BANNER320x100"]) height = 100;
    else if ([_adSize isEqualToString:@"BANNER300x250"]) height = 250;
    CGRect frame = CGRectMake((UIScreen.mainScreen.bounds.size.width - width) / 2, 0, width, height);
    self.bannerView.frame = frame;
    if ([_adSize isEqualToString:@"BANNER320x50"]) [self.bannerView setAdSize:AdWhaleAdSizeBanner];
    else if ([_adSize isEqualToString:@"BANNER320x100"]) [self.bannerView setAdSize:AdWhaleAdSizeLargeBanner];
    else if ([_adSize isEqualToString:@"BANNER300x250"]) [self.bannerView setAdSize:AdWhaleAdSizeMediumRectangle];
}

- (void)loadAd {
    if (!self.bannerView) {
        CGFloat width = 320;
        CGFloat height = 50;
        if ([_adSize isEqualToString:@"BANNER320x100"]) height = 100;
        else if ([_adSize isEqualToString:@"BANNER300x250"]) height = 250;
        CGRect frame = CGRectMake((UIScreen.mainScreen.bounds.size.width - width) / 2, 0, width, height);
        self.bannerView = [[AdWhaleBannerAd alloc] initWithFrame:frame];
        [self addSubview:self.bannerView];
        [self updateAdSize];
        [self.bannerView setAdUnitID:self.placementUid ?: @""];
        [self.bannerView setRootViewController:RCTPresentedViewController()];
        [self.bannerView setDelegate:self];
    }
    [self.bannerView load];
}

// MARK: - AdWhaleBannerDelegate
- (void)bannerViewDidReceiveAd:(AdWhaleBannerAd *)bannerView {
    if (self.onAdLoaded) self.onAdLoaded(@{});
}
- (void)bannerView:(AdWhaleBannerAd *)bannerView didFailToReceiveAdWithError:(NSError *)error {
    if (self.onAdLoadFailed) self.onAdLoadFailed(@{@"statusCode": @0, @"message": error.localizedDescription ?: @""});
}
- (void)bannerViewDidRecordImpression:(AdWhaleBannerAd *)bannerView {}
- (void)bannerViewWillPresentScreen:(AdWhaleBannerAd *)bannerView {
    if (self.onAdClicked) self.onAdClicked(@{});
}
- (void)bannerViewWillDismissScreen:(AdWhaleBannerAd *)bannerView {}
- (void)bannerViewDidDismissScreen:(AdWhaleBannerAd *)bannerView {}

@end

@interface BannerAdView : RCTViewManager
@end

@implementation BannerAdView
RCT_EXPORT_MODULE()

RCT_EXPORT_VIEW_PROPERTY(onAdLoaded, RCTBubblingEventBlock)
RCT_EXPORT_VIEW_PROPERTY(onAdLoadFailed, RCTBubblingEventBlock)
RCT_EXPORT_VIEW_PROPERTY(onAdClicked, RCTBubblingEventBlock)
RCT_EXPORT_VIEW_PROPERTY(adSize, NSString)
RCT_EXPORT_VIEW_PROPERTY(placementUid, NSString)
RCT_EXPORT_VIEW_PROPERTY(shouldLoadAd, BOOL)

- (UIView *)view {
    return [[BannerAdViewContainer alloc] init];
}
@end 
```

### 2. React Native 연동 코드

#### BannerAd.tsx

```typescript
import React, { Component } from 'react';
import { View, Button, StyleSheet, Dimensions, Text, TouchableOpacity } from 'react-native';
import { requireNativeComponent } from 'react-native';

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
  title: {
    fontSize: 18,
    fontWeight: 'bold',
    marginBottom: 10,
  },
});

const radioStyles = StyleSheet.create({
  radioGroup: {
    justifyContent: 'space-around',
    marginBottom: 10,
  },
  radioButton: {
    flexDirection: 'row',
    alignItems: 'center',
    marginBottom: 10,
  },
  outerCircle: {
    height: 24,
    width: 24,
    borderRadius: 12,
    borderWidth: 2,
    borderColor: '#000',
    alignItems: 'center',
    justifyContent: 'center',
    marginRight: 10,
  },
  innerCircle: {
    height: 12,
    width: 12,
    borderRadius: 6,
    backgroundColor: '#000',
  },
});

interface BannerAdProps {
  style?: any;
  onAdLoaded?: (event: any) => void;
  onAdLoadFailed?: (event: any) => void;
  onAdClicked?: (event: any) => void;
}

interface BannerAdState {
  isReload: boolean;
  selectedSize: string;
}

const BannerAdView = requireNativeComponent<any>('BannerAdView');

class BannerAd extends Component<BannerAdProps, BannerAdState> {
  state: BannerAdState = {
    isReload: false,
    selectedSize: 'BANNER320x50',
  };

  placementUid = '배너 광고 AD_UNIT_ID 입력';

  onPressBannerLoadButton = () => {
    console.log(`[adwhale] [sample UI] 배너 광고 로드 요청`);
    this.setState(prevState => ({ isReload: !prevState.isReload }));
  }

  _onAdLoaded = (event: any) => {
    console.log(`[adwhale] [sample UI] 배너 광고 로드 성공`);
    this.setState({ isReload: false });
  }

  _onAdLoadFailed = (event: any) => {
    const { statusCode, message } = event.nativeEvent;
    console.error(`[adwhale] [sample UI] 배너 광고 로드 실패: statusCode=${statusCode}, message=${message}`);
    this.setState({ isReload: false });
  }

  _onAdClicked = (event: any) => {
    console.log(`[adwhale] [sample UI] 배너 광고 클릭`);
  }

  handleSizeChange = (size: string) => {
    console.log(`[adwhale] [sample UI] 배너 크기 변경: size=${size}`);
    this.setState({ selectedSize: size });
  }

  getBannerHeight = (size: string) => {
    switch (size) {
      case 'BANNER320x50':
        return 50;
      case 'BANNER320x100':
        return 100;
      case 'BANNER300x250':
        return 250;
      default:
        return 50;
    }
  };

  render() {
    const { selectedSize } = this.state;
    const bannerHeight = this.getBannerHeight(selectedSize);

    return (
      <View style={styles.container}>
        <Text style={styles.title}>배너 광고</Text>
        <View style={radioStyles.radioGroup}>
          {['BANNER320x50', 'BANNER320x100', 'BANNER300x250'].map(size => (
            <TouchableOpacity
              key={size}
              style={radioStyles.radioButton}
              onPress={() => this.handleSizeChange(size)}
            >
              <View style={radioStyles.outerCircle}>
                {selectedSize === size && <View style={radioStyles.innerCircle} />}
              </View>
              <Text>{size}</Text>
            </TouchableOpacity>
          ))}
        </View>

        <View style={styles.buttonContainer}>
          <View style={styles.buttonWrapper}>
            <Button title="Banner load" onPress={this.onPressBannerLoadButton} />
          </View>
        </View>

        <View
          style={{
            width: windowWidth,
            height: 250,
            backgroundColor: 'yellow',
            marginBottom: 20,
            alignItems: 'center',
            justifyContent: 'flex-end',
          }}
        >
          <BannerAdView
            style={{
              width: windowWidth,
              height: bannerHeight,
              backgroundColor: 'transparent',
            }}
            placementUid={this.placementUid}
            adSize={selectedSize}
            shouldLoadAd={this.state.isReload}
            onAdLoaded={this._onAdLoaded}
            onAdLoadFailed={this._onAdLoadFailed}
            onAdClicked={this._onAdClicked}
          />
        </View>
      </View>
    );
  }
}

export default BannerAd; 
```

#### App.tsx

```typescript
import React from 'react';
import { StyleSheet, View, SafeAreaView } from 'react-native';
import BannerAd from './src/components/BannerAd';

export default function App() {
  return (
    <SafeAreaView style={styles.safeArea}>
      <View style={styles.mainViewStyle}>
        <View style={styles.contentViewStyle}>
            <BannerAd />
        </View>
      </View>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  safeArea: {
    flex: 1,
    backgroundColor: 'white'
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
```
{% endtab %}

{% tab title="Expo" %}
### 1. BannerAdView.swift

```swift
import UIKit
import AdWhaleSDK
import React

@objc(BannerAdView)
class BannerAdView: RCTViewManager {
    override func view() -> UIView! {
        return BannerAdViewContainer()
    }
    
    override static func requiresMainQueueSetup() -> Bool {
        return true
    }
}

@objc(BannerAdViewContainer)
class BannerAdViewContainer: UIView {
    private var bannerView: AdWhaleBannerAd?
    @objc var adSize: String = "BANNER320x50" {
        didSet {
            updateAdSize()
        }
    }
    @objc var placementUid: String = "" {
        didSet {
            bannerView?.setAdUnitID(placementUid)
        }
    }
    @objc var shouldLoadAd: Bool = false {
        didSet {
            if shouldLoadAd {
                loadAd()
            }
        }
    }
    
    @objc var onAdLoaded: RCTDirectEventBlock?
    @objc var onAdLoadFailed: RCTDirectEventBlock?
    @objc var onAdClicked: RCTDirectEventBlock?
    
    override init(frame: CGRect) {
        super.init(frame: frame)
        setupBannerAd()
    }
    
    required init?(coder: NSCoder) {
        super.init(coder: coder)
        setupBannerAd()
    }
    
    private func setupBannerAd() {
        let screenWidth = UIScreen.main.bounds.width
        let bannerWidth: CGFloat = 320
        let xPosition = (screenWidth - bannerWidth) / 2
        
        let frame = CGRect(x: xPosition,
                          y: 0,
                          width: bannerWidth,
                          height: getBannerHeight(for: adSize))
        
        bannerView = AdWhaleBannerAd(frame: frame)
        if let bannerView = bannerView {
            addSubview(bannerView)
            updateAdSize()
            bannerView.setAdUnitID(placementUid)
            
            if let windowScene = UIApplication.shared.connectedScenes.first as? UIWindowScene,
               let rootViewController = windowScene.windows.first?.rootViewController {
                bannerView.setRootViewController(rootViewController)
            }
            
            bannerView.setDelegate(self)
        }
    }
    
    private func getBannerHeight(for size: String) -> CGFloat {
        switch size {
        case "BANNER320x50":
            return 50
        case "BANNER320x100":
            return 100
        case "BANNER300x250":
            return 250
        default:
            return 50
        }
    }
    
    private func updateAdSize() {
        guard let bannerView = bannerView else { return }
        
        // 배너 뷰의 크기를 새로운 사이즈에 맞게 업데이트
        let screenWidth = UIScreen.main.bounds.width
        let bannerWidth: CGFloat = 320
        let xPosition = (screenWidth - bannerWidth) / 2
        let newHeight = getBannerHeight(for: adSize)
        
        bannerView.frame = CGRect(x: xPosition,
                                y: bannerView.frame.origin.y,
                                width: bannerWidth,
                                height: newHeight)
        
        switch adSize {
        case "BANNER320x50":
            bannerView.setAdSize(.banner)
            break;
        case "BANNER320x100":
            bannerView.setAdSize(.largeBanner)
            break;
        case "BANNER300x250":
            bannerView.setAdSize(.mediumRectangle)
            break
        default:
            bannerView.setAdSize(.banner)
        }
    }
    
    private func loadAd() {
        bannerView?.setAdUnitID(placementUid)
        bannerView?.load()
    }
}

// MARK: - BannerAd Delegate
extension BannerAdViewContainer: AdWhaleBannerDelegate {
    func bannerViewDidReceiveAd(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("[adwhale] [sample Native] 배너 광고 로드 성공")
        onAdLoaded?([:])
    }
    
    func bannerView(_ bannerView: AdWhaleSDK.AdWhaleBannerAd, didFailToReceiveAdWithError error: Error) {
        print("[adwhale] [sample Native] 배너 광고 로드 실패: \(error.localizedDescription)")
        onAdLoadFailed?([
            "statusCode": 0,
            "message": error.localizedDescription
        ])
    }
    
    func bannerViewDidRecordImpression(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("[adwhale] [sample Native] 배너 광고 노출")
    }
    
    func bannerViewWillPresentScreen(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("[adwhale] [sample Native] 배너 광고 클릭")
        onAdClicked?([:])
    }
    
    func bannerViewWillDismissScreen(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("[adwhale] [sample Native] 배너 광고 화면 닫힘 예정")
    }
    
    func bannerViewDidDismissScreen(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("[adwhale] [sample Native] 배너 광고 화면 닫힘")
    }
}
```

### 2. BannerAdView.m

```objectivec
#import <React/RCTViewManager.h>
#import <React/RCTUIManager.h>
#import <React/RCTBridge.h>

@interface RCT_EXTERN_MODULE(BannerAdView, RCTViewManager)

RCT_EXPORT_VIEW_PROPERTY(onAdLoaded, RCTDirectEventBlock)
RCT_EXPORT_VIEW_PROPERTY(onAdLoadFailed, RCTDirectEventBlock)
RCT_EXPORT_VIEW_PROPERTY(onAdClicked, RCTDirectEventBlock)
RCT_EXPORT_VIEW_PROPERTY(placementUid, NSString *)
RCT_EXPORT_VIEW_PROPERTY(shouldLoadAd, BOOL)
RCT_EXPORT_VIEW_PROPERTY(adSize, NSString *)

@end 
```

### 3. Expo (React Native) 연동 코드

#### BannerAd.tsx

```typescript
import React, { Component } from 'react';
import { View, Button, StyleSheet, Dimensions, Text, TouchableOpacity } from 'react-native';
import { requireNativeComponent } from 'react-native';

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
  title: {
    fontSize: 18,
    fontWeight: 'bold',
    marginBottom: 10,
  },
});

const radioStyles = StyleSheet.create({
  radioGroup: {
    justifyContent: 'space-around',
    marginBottom: 10,
  },
  radioButton: {
    flexDirection: 'row',
    alignItems: 'center',
    marginBottom: 10,
  },
  outerCircle: {
    height: 24,
    width: 24,
    borderRadius: 12,
    borderWidth: 2,
    borderColor: '#000',
    alignItems: 'center',
    justifyContent: 'center',
    marginRight: 10,
  },
  innerCircle: {
    height: 12,
    width: 12,
    borderRadius: 6,
    backgroundColor: '#000',
  },
});

interface BannerAdProps {
  style?: any;
  onAdLoaded?: (event: any) => void;
  onAdLoadFailed?: (event: any) => void;
  onAdClicked?: (event: any) => void;
}

interface BannerAdState {
  isReload: boolean;
  selectedSize: string;
}

const BannerAdView = requireNativeComponent<any>('BannerAdView');

class BannerAd extends Component<BannerAdProps, BannerAdState> {
  state: BannerAdState = {
    isReload: false,
    selectedSize: 'BANNER320x50',
  };

  placementUid = '배너 광고 AD_UNIT_ID 입력';

  onPressBannerLoadButton = () => {
    console.log(`[adwhale] [sample UI] 배너 광고 로드 요청`);
    this.setState(prevState => ({ isReload: !prevState.isReload }));
  }

  _onAdLoaded = (event: any) => {
    console.log(`[adwhale] [sample UI] 배너 광고 로드 성공`);
    this.setState({ isReload: false });
  }

  _onAdLoadFailed = (event: any) => {
    const { statusCode, message } = event.nativeEvent;
    console.error(`[adwhale] [sample UI] 배너 광고 로드 실패: statusCode=${statusCode}, message=${message}`);
    this.setState({ isReload: false });
  }

  _onAdClicked = (event: any) => {
    console.log(`[adwhale] [sample UI] 배너 광고 클릭`);
  }

  handleSizeChange = (size: string) => {
    console.log(`[adwhale] [sample UI] 배너 크기 변경: size=${size}`);
    this.setState({ selectedSize: size });
  }

  getBannerHeight = (size: string) => {
    switch (size) {
      case 'BANNER320x50':
        return 50;
      case 'BANNER320x100':
        return 100;
      case 'BANNER300x250':
        return 250;
      default:
        return 50;
    }
  };

  render() {
    const { selectedSize } = this.state;
    const bannerHeight = this.getBannerHeight(selectedSize);

    return (
      <View style={styles.container}>
        <Text style={styles.title}>배너 광고</Text>
        <View style={radioStyles.radioGroup}>
          {['BANNER320x50', 'BANNER320x100', 'BANNER300x250'].map(size => (
            <TouchableOpacity
              key={size}
              style={radioStyles.radioButton}
              onPress={() => this.handleSizeChange(size)}
            >
              <View style={radioStyles.outerCircle}>
                {selectedSize === size && <View style={radioStyles.innerCircle} />}
              </View>
              <Text>{size}</Text>
            </TouchableOpacity>
          ))}
        </View>

        <View style={styles.buttonContainer}>
          <View style={styles.buttonWrapper}>
            <Button title="Banner load" onPress={this.onPressBannerLoadButton} />
          </View>
        </View>

        <View
          style={{
            width: windowWidth,
            height: 250,
            backgroundColor: 'yellow',
            marginBottom: 20,
            alignItems: 'center',
            justifyContent: 'flex-end',
          }}
        >
          <BannerAdView
            style={{
              width: windowWidth,
              height: bannerHeight,
              backgroundColor: 'transparent',
            }}
            placementUid={this.placementUid}
            adSize={selectedSize}
            shouldLoadAd={this.state.isReload}
            onAdLoaded={this._onAdLoaded}
            onAdLoadFailed={this._onAdLoadFailed}
            onAdClicked={this._onAdClicked}
          />
        </View>
      </View>
    );
  }
}

export default BannerAd; 
```

#### App.tsx

```typescript
import React from 'react';
import { StyleSheet, View, SafeAreaView } from 'react-native';
import { StatusBar } from 'expo-status-bar';
import InterstitialAd from './src/components/InterstitialAd';
import RewardAd from './src/components/RewardAd';
import AdWhaleSetting from './src/components/AdWhaleSetting';
import BannerAd from './src/components/BannerAd';

export default function App() {
  return (
    <SafeAreaView style={styles.safeArea}>
      <View style={styles.mainViewStyle}>
        <StatusBar style="auto" />
        <View style={styles.contentViewStyle}>
            <BannerAd />
        </View>
      </View>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  safeArea: {
    flex: 1,
    backgroundColor: 'white'
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

```
{% endtab %}
{% endtabs %}

