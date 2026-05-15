# 전면

{% hint style="info" %}
* AdWhale SDK를 프로젝트에 추가 해야합니다.
* Interstitial Ad용으로 발급받은 Ad Unit ID를 사용합니다.
* 광고를 요청하기 전에 SDK 초기화를 진행합니다.
{% endhint %}

{% tabs %}
{% tab title="React-Native" %}
### 1. InterstitialAdModule.m

```objectivec
#import <React/RCTBridgeModule.h>
#import <React/RCTEventEmitter.h>
#import <AdWhaleSDK/AdWhaleSDK.h>
#import <React/RCTUtils.h>

@interface InterstitialAdModule : RCTEventEmitter <RCTBridgeModule, AdWhaleInterstitialDelegate>
@property (nonatomic, strong) AdWhaleInterstitialAd *interstitialAd;
@end

@implementation InterstitialAdModule

RCT_EXPORT_MODULE();

- (NSArray<NSString *> *)supportedEvents {
    return @[
        @"onInterstitialAdLoaded",
        @"onInterstitialAdFailedToLoad",
        @"onInterstitialAdFailedToShow",
        @"onInterstitialAdWillPresent",
        @"onInterstitialAdDismissed"
    ];
}

RCT_EXPORT_METHOD(loadAd:(NSString *)adUnitId
                  resolve:(RCTPromiseResolveBlock)resolve
                  rejecter:(RCTPromiseRejectBlock)reject) {
    dispatch_async(dispatch_get_main_queue(), ^{
        self.interstitialAd = [[AdWhaleInterstitialAd alloc] init];
        self.interstitialAd.interstitialDelegate = self;
        [self.interstitialAd load:adUnitId];
        if (resolve) resolve(nil);
    });
}

RCT_EXPORT_METHOD(showAd:(RCTPromiseResolveBlock)resolve
                  rejecter:(RCTPromiseRejectBlock)reject) {
    dispatch_async(dispatch_get_main_queue(), ^{
        UIViewController *rootVC = RCTPresentedViewController();
        if (rootVC) {
            [self.interstitialAd show:rootVC];
            if (resolve) resolve(nil);
        } else {
            if (reject) reject(@"ERROR", @"Root view controller not found", nil);
        }
    });
}

// MARK: - AdWhaleInterstitialDelegate
- (void)adDidReceiveInterstitialAd:(AdWhaleInterstitialAd *)ad {
    self.interstitialAd = ad;
    [self sendEventWithName:@"onInterstitialAdLoaded" body:nil];
}
- (void)adDidFailToReceiveInterstitialAdWithError:(NSError *)error {
    [self sendEventWithName:@"onInterstitialAdFailedToLoad" body:@{@"error": error.localizedDescription ?: @""}];
}
- (void)ad:(AdWhaleInterstitialAd *)ad didFailToPresentInterstitialAdWithError:(NSError *)error {
    [self sendEventWithName:@"onInterstitialAdFailedToShow" body:@{@"error": error.localizedDescription ?: @""}];
}
- (void)adWillPresentInterstitialAd:(AdWhaleInterstitialAd *)ad {
    [self sendEventWithName:@"onInterstitialAdWillPresent" body:nil];
}
- (void)adDidDismissInterstitialAd:(AdWhaleInterstitialAd *)ad {
    [self sendEventWithName:@"onInterstitialAdDismissed" body:nil];
}

@end 

```

### 2. React Native 연동 코드

#### InterstitialAd.tsx

```typescript
import React, { useState, useEffect } from 'react';
import { StyleSheet, Text, View, Button, NativeEventEmitter, NativeModules } from 'react-native';

const { InterstitialAdModule } = NativeModules;

const INTERSTITIAL_AD_UNIT_ID = '전면 광고 AD_UNIT_ID 입력';

export default function InterstitialAd() {
  const [interstitialLoaded, setInterstitialLoaded] = useState(false);
  const [isLoading, setIsLoading] = useState(false);

  useEffect(() => {
    const eventEmitter = new NativeEventEmitter(InterstitialAdModule);
    
    const subscriptions = [
      eventEmitter.addListener('onInterstitialAdLoaded', () => {
        console.log(`[adwhale] [sample UI] 전면 광고 로드 성공`);
        setInterstitialLoaded(true);
        setIsLoading(false);
      }),
      eventEmitter.addListener('onInterstitialAdFailedToLoad', (event) => {
        console.error(`[adwhale] [sample UI] 전면 광고 로드 실패: ${event.error}`);
        setInterstitialLoaded(false);
        setIsLoading(false);
      }),
      eventEmitter.addListener('onInterstitialAdFailedToShow', (event) => {
        console.error(`[adwhale] [sample UI] 전면 광고 표시 실패: ${event.error}`);
        setInterstitialLoaded(false);
      }),
      eventEmitter.addListener('onInterstitialAdWillPresent', () => {
        console.log(`[adwhale] [sample UI] 전면 광고가 표시될 예정`);
      }),
      eventEmitter.addListener('onInterstitialAdDismissed', () => {
        console.log(`[adwhale] [sample UI] 전면 광고가 닫힘`);
        setInterstitialLoaded(false);
      })
    ];

    return () => {
      subscriptions.forEach(subscription => subscription.remove());
    };
  }, []);

  const loadInterstitial = async () => {
    try {
      setIsLoading(true);
      await InterstitialAdModule.loadAd(INTERSTITIAL_AD_UNIT_ID);
    } catch (error) {
      console.error(`[adwhale] [sample UI] 전면 광고 로드 실패: ${error}`);
      setIsLoading(false);
    }
  };

  const showInterstitial = async () => {
    try {
      await InterstitialAdModule.showAd();
    } catch (error) {
      console.error(`[adwhale] [sample UI] 전면 광고 표시 실패: ${error}`);
    }
  };

  return (
    <View style={styles.section}>
      <Text style={styles.title}>전면 광고</Text>
      <Button
        title="전면 광고 로드"
        onPress={loadInterstitial}
        disabled={isLoading || interstitialLoaded}
      />
      <Button
        title="전면 광고 표시"
        onPress={showInterstitial}
        disabled={!interstitialLoaded}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  section: {
    marginBottom: 20,
  },
  title: {
    fontSize: 18,
    fontWeight: 'bold',
    marginBottom: 10,
  },
}); 
```

#### App.tsx

```typescript
import React from 'react';
import { StyleSheet, View, SafeAreaView } from 'react-native';
import InterstitialAd from './src/components/InterstitialAd';

export default function App() {
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
### 1. InterstitialAdModule.swift

```swift
import Foundation
import AdWhaleSDK
import React

@objc(InterstitialAdModule)
class InterstitialAdModule: RCTEventEmitter {
  private var interstitialAd: AdWhaleInterstitialAd?
  
  override func supportedEvents() -> [String]! {
    return [
      "onInterstitialAdLoaded",
      "onInterstitialAdFailedToLoad",
      "onInterstitialAdFailedToShow",
      "onInterstitialAdWillPresent",
      "onInterstitialAdDismissed"
    ]
  }

  @objc
  override static func requiresMainQueueSetup() -> Bool {
    return true
  }

  @objc
  func loadAd(_ adUnitId: String, resolve: @escaping RCTPromiseResolveBlock, rejecter reject: @escaping RCTPromiseRejectBlock) {
    DispatchQueue.main.async {
      print("[adwhale] [sample Native] 전면 광고 로드 시작")
      self.interstitialAd = AdWhaleInterstitialAd()
      self.interstitialAd?.interstitialDelegate = self
      self.interstitialAd?.load(adUnitId)
      resolve(nil)
    }
  }

  @objc
  func showAd(_ resolve: @escaping RCTPromiseResolveBlock, rejecter reject: @escaping RCTPromiseRejectBlock) {
    DispatchQueue.main.async {
      if let rootViewController = UIApplication.shared.keyWindow?.rootViewController {
        print("[adwhale] [sample Native] 전면 광고 표시 시도")
        self.interstitialAd?.show(rootViewController)
        resolve(nil)
      } else {
        print("[adwhale] [sample Native] Root view controller를 찾을 수 없음")
        reject("ERROR", "Root view controller not found", nil)
      }
    }
  }
}

extension InterstitialAdModule: AdWhaleInterstitialDelegate {
  func adDidReceiveInterstitialAd(_ ad: AdWhaleInterstitialAd) {
    print("[adwhale] [sample Native] 전면 광고 로드 성공")
    interstitialAd = ad
    sendEvent(withName: "onInterstitialAdLoaded", body: nil)
  }

  func adDidFailToReceiveInterstitialAdWithError(_ error: Error) {
    print("[adwhale] [sample Native] 전면 광고 로드 실패: \(error.localizedDescription)")
    sendEvent(withName: "onInterstitialAdFailedToLoad", body: ["error": error.localizedDescription])
  }

  func ad(_ ad: AdWhaleInterstitialAd, didFailToPresentInterstitialAdWithError error: Error) {
    print("[adwhale] [sample Native] 전면 광고 표시 실패: \(error.localizedDescription)")
    sendEvent(withName: "onInterstitialAdFailedToShow", body: ["error": error.localizedDescription])
  }

  func adWillPresentInterstitialAd(_ ad: AdWhaleInterstitialAd) {
    print("[adwhale] [sample Native] 전면 광고가 표시될 예정")
    sendEvent(withName: "onInterstitialAdWillPresent", body: nil)
  }

  func adDidDismissInterstitialAd(_ ad: AdWhaleInterstitialAd) {
    print("[adwhale] [sample Native] 전면 광고가 닫힘")
    sendEvent(withName: "onInterstitialAdDismissed", body: nil)
  }
}
```

### 2. InterstitialAdModule.m

```objectivec
#import <React/RCTBridgeModule.h>

@interface RCT_EXTERN_MODULE(InterstitialAdModule, NSObject)

RCT_EXTERN_METHOD(loadAd:(NSString *)adUnitId
                  resolve:(RCTPromiseResolveBlock)resolve
                  rejecter:(RCTPromiseRejectBlock)reject)

RCT_EXTERN_METHOD(showAd:(RCTPromiseResolveBlock)resolve
                  rejecter:(RCTPromiseRejectBlock)reject)

@end 
```

### 3. Expo (React Native) 연동 코드

#### InterstitialAd.tsx

```typescript
import React, { useState, useEffect } from 'react';
import { StyleSheet, Text, View, Button, NativeEventEmitter, NativeModules } from 'react-native';

const { InterstitialAdModule } = NativeModules;

const INTERSTITIAL_AD_UNIT_ID = '전면 광고 AD_UNIT_ID 입력';

export default function InterstitialAd() {
  const [interstitialLoaded, setInterstitialLoaded] = useState(false);
  const [isLoading, setIsLoading] = useState(false);

  useEffect(() => {
    const eventEmitter = new NativeEventEmitter(InterstitialAdModule);
    
    const subscriptions = [
      eventEmitter.addListener('onInterstitialAdLoaded', () => {
        console.log(`[adwhale] [sample UI] 전면 광고 로드 성공`);
        setInterstitialLoaded(true);
        setIsLoading(false);
      }),
      eventEmitter.addListener('onInterstitialAdFailedToLoad', (event) => {
        console.error(`[adwhale] [sample UI] 전면 광고 로드 실패: ${event.error}`);
        setInterstitialLoaded(false);
        setIsLoading(false);
      }),
      eventEmitter.addListener('onInterstitialAdFailedToShow', (event) => {
        console.error(`[adwhale] [sample UI] 전면 광고 표시 실패: ${event.error}`);
        setInterstitialLoaded(false);
      }),
      eventEmitter.addListener('onInterstitialAdWillPresent', () => {
        console.log(`[adwhale] [sample UI] 전면 광고가 표시될 예정`);
      }),
      eventEmitter.addListener('onInterstitialAdDismissed', () => {
        console.log(`[adwhale] [sample UI] 전면 광고가 닫힘`);
        setInterstitialLoaded(false);
      })
    ];

    return () => {
      subscriptions.forEach(subscription => subscription.remove());
    };
  }, []);

  const loadInterstitial = async () => {
    try {
      setIsLoading(true);
      await InterstitialAdModule.loadAd(INTERSTITIAL_AD_UNIT_ID);
    } catch (error) {
      console.error(`[adwhale] [sample UI] 전면 광고 로드 실패: ${error}`);
      setIsLoading(false);
    }
  };

  const showInterstitial = async () => {
    try {
      await InterstitialAdModule.showAd();
    } catch (error) {
      console.error(`[adwhale] [sample UI] 전면 광고 표시 실패: ${error}`);
    }
  };

  return (
    <View style={styles.section}>
      <Text style={styles.title}>전면 광고</Text>
      <Button
        title="전면 광고 로드"
        onPress={loadInterstitial}
        disabled={isLoading || interstitialLoaded}
      />
      <Button
        title="전면 광고 표시"
        onPress={showInterstitial}
        disabled={!interstitialLoaded}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  section: {
    marginBottom: 20,
  },
  title: {
    fontSize: 18,
    fontWeight: 'bold',
    marginBottom: 10,
  },
}); 
```

#### App.tsx

```typescript
import React from 'react';
import { StyleSheet, View, SafeAreaView } from 'react-native';
import { StatusBar } from 'expo-status-bar';
import InterstitialAd from './src/components/InterstitialAd';

export default function App() {
  return (
    <SafeAreaView style={styles.safeArea}>
      <View style={styles.mainViewStyle}>
        <StatusBar style="auto" />
        <View style={styles.contentViewStyle}>
            <InterstitialAd />
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
