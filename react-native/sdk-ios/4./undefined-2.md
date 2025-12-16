# 리워드

{% hint style="info" %}
* AdWhale SDK를 프로젝트에 추가 해야합니다.
* Reward Ad용으로 발급받은 Ad Unit ID를 사용합니다.
* 광고를 요청하기 전에 SDK 초기화를 진행합니다.
{% endhint %}

{% tabs %}
{% tab title="React-Native" %}
### 1. RewardAdModule.m

```objectivec
#import <React/RCTBridgeModule.h>
#import <React/RCTEventEmitter.h>
#import <AdWhaleSDK/AdWhaleSDK.h>
#import <React/RCTUtils.h>

@interface RewardAdModule : RCTEventEmitter <RCTBridgeModule, AdWhaleRewardDelegate>
@property (nonatomic, strong) AdWhaleRewardAd *rewardAd;
@end

@implementation RewardAdModule

RCT_EXPORT_MODULE();

- (NSArray<NSString *> *)supportedEvents {
    return @[
        @"onRewardAdLoaded",
        @"onRewardAdFailedToLoad",
        @"onRewardAdFailedToShow",
        @"onRewardAdWillPresent",
        @"onRewardAdDismissed",
        @"onRewardAdEarned"
    ];
}

RCT_EXPORT_METHOD(loadAd:(NSString *)adUnitId
                  resolve:(RCTPromiseResolveBlock)resolve
                  rejecter:(RCTPromiseRejectBlock)reject) {
    dispatch_async(dispatch_get_main_queue(), ^{
        self.rewardAd = [[AdWhaleRewardAd alloc] init];
        self.rewardAd.rewardDelegate = self;
        [self.rewardAd load:adUnitId];
        if (resolve) resolve(nil);
    });
}

RCT_EXPORT_METHOD(showAd:(RCTPromiseResolveBlock)resolve
                  rejecter:(RCTPromiseRejectBlock)reject) {
    dispatch_async(dispatch_get_main_queue(), ^{
        UIViewController *rootVC = RCTPresentedViewController();
        if (rootVC) {
            [self.rewardAd show:rootVC];
            if (resolve) resolve(nil);
        } else {
            if (reject) reject(@"ERROR", @"Root view controller not found", nil);
        }
    });
}

// MARK: - AdWhaleRewardDelegate
- (void)adDidReceiveRewardAd:(AdWhaleRewardAd *)ad {
    self.rewardAd = ad;
    [self sendEventWithName:@"onRewardAdLoaded" body:nil];
}
- (void)adDidFailToReceiveRewardAdWithError:(NSError *)error {
    [self sendEventWithName:@"onRewardAdFailedToLoad" body:@{@"error": error.localizedDescription ?: @""}];
}
- (void)ad:(AdWhaleRewardAd *)ad didFailToPresentRewardAdWithError:(NSError *)error {
    [self sendEventWithName:@"onRewardAdFailedToShow" body:@{@"error": error.localizedDescription ?: @""}];
}
- (void)adWillPresentRewardAd:(AdWhaleRewardAd *)ad {
    [self sendEventWithName:@"onRewardAdWillPresent" body:nil];
}
- (void)adDidDismissRewardAd:(AdWhaleRewardAd *)ad {
    [self sendEventWithName:@"onRewardAdDismissed" body:nil];
}
- (void)adDidEarnReward:(AdWhaleReward *)reward {
    [self sendEventWithName:@"onRewardAdEarned" body:@{
        @"amount": reward.amount ?: @0,
        @"type": reward.type ?: @""
    }];
}

@end 

```

### 2. React Native 연동 코드

#### RewardAd.tsx

```typescript
import React, { useState, useEffect } from 'react';
import { StyleSheet, Text, View, Button, NativeEventEmitter, NativeModules } from 'react-native';

const { RewardAdModule } = NativeModules;

const REWARD_AD_UNIT_ID = '리워드 광고 AD_UNIT_ID 입력';

export default function RewardAd() {
  const [rewardLoaded, setRewardLoaded] = useState(false);
  const [isLoading, setIsLoading] = useState(false);

  useEffect(() => {
    const eventEmitter = new NativeEventEmitter(RewardAdModule);
    
    const subscriptions = [
      eventEmitter.addListener('onRewardAdLoaded', () => {
        console.log(`[adwhale] [sample UI] 보상형 광고 로드 성공`);
        setRewardLoaded(true);
        setIsLoading(false);
      }),
      eventEmitter.addListener('onRewardAdFailedToLoad', (event) => {
        console.error(`[adwhale] [sample UI] 보상형 광고 로드 실패: ${event.error}`);
        setRewardLoaded(false);
        setIsLoading(false);
      }),
      eventEmitter.addListener('onRewardAdFailedToShow', (event) => {
        console.error(`[adwhale] [sample UI] 보상형 광고 표시 실패: ${event.error}`);
        setRewardLoaded(false);
      }),
      eventEmitter.addListener('onRewardAdWillPresent', () => {
        console.log(`[adwhale] [sample UI] 보상형 광고가 표시될 예정`);
      }),
      eventEmitter.addListener('onRewardAdDismissed', () => {
        console.log(`[adwhale] [sample UI] 보상형 광고가 닫힘`);
        setRewardLoaded(false);
      }),
      eventEmitter.addListener('onRewardAdEarned', (event) => {
        console.log(`[adwhale] [sample UI] 보상 획득: amount=${event.amount}, type=${event.type}`);
      })
    ];

    return () => {
      subscriptions.forEach(subscription => subscription.remove());
    };
  }, []);

  const loadReward = async () => {
    try {
      setIsLoading(true);
      await RewardAdModule.loadAd(REWARD_AD_UNIT_ID);
    } catch (error) {
      console.error(`[adwhale] [sample UI] 보상형 광고 로드 실패: ${error}`);
      setIsLoading(false);
    }
  };

  const showReward = async () => {
    try {
      await RewardAdModule.showAd();
    } catch (error) {
      console.error(`[adwhale] [sample UI] 보상형 광고 표시 실패: ${error}`);
    }
  };

  return (
    <View style={styles.section}>
      <Text style={styles.title}>보상형 광고</Text>
      <Button
        title="보상형 광고 로드"
        onPress={loadReward}
        disabled={isLoading || rewardLoaded}
      />
      <Button
        title="보상형 광고 표시"
        onPress={showReward}
        disabled={!rewardLoaded}
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
import RewardAd from './src/components/RewardAd';

export default function App() {
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
### 1. RewardAdModule.swift

```swift
import Foundation
import AdWhaleSDK
import React

@objc(RewardAdModule)
class RewardAdModule: RCTEventEmitter {
  private var rewardAd: AdWhaleRewardAd?

  override func supportedEvents() -> [String]! {
    return [
      "onRewardAdLoaded",
      "onRewardAdFailedToLoad",
      "onRewardAdFailedToShow",
      "onRewardAdWillPresent",
      "onRewardAdDismissed",
      "onRewardAdEarned"
    ]
  }

  override static func requiresMainQueueSetup() -> Bool {
    return true
  }

  @objc
  func loadAd(_ adUnitId: String, resolve: @escaping RCTPromiseResolveBlock, rejecter reject: @escaping RCTPromiseRejectBlock) {
    DispatchQueue.main.async {
      print("[adwhale] [sample Native] 보상형 광고 로드 시작")
      self.rewardAd = AdWhaleRewardAd()
      self.rewardAd?.rewardDelegate = self
      self.rewardAd?.load(adUnitId)
      resolve(nil)
    }
  }

  @objc
  func showAd(_ resolve: @escaping RCTPromiseResolveBlock, rejecter reject: @escaping RCTPromiseRejectBlock) {
    DispatchQueue.main.async {
      if let rootViewController = UIApplication.shared.keyWindow?.rootViewController {
        print("[adwhale] [sample Native] 보상형 광고 표시 시도")
        self.rewardAd?.show(rootViewController)
        resolve(nil)
      } else {
        print("[adwhale] [sample Native] Root view controller를 찾을 수 없음")
        reject("ERROR", "Root view controller not found", nil)
      }
    }
  }
}

extension RewardAdModule: AdWhaleRewardDelegate {
  func adDidReceiveRewardAd(_ ad: AdWhaleRewardAd) {
    print("[adwhale] [sample Native] 보상형 광고 로드 성공")
    rewardAd = ad
    sendEvent(withName: "onRewardAdLoaded", body: nil)
  }

  func adDidFailToReceiveRewardAdWithError(_ error: Error) {
    print("[adwhale] [sample Native] 보상형 광고 로드 실패: \(error.localizedDescription)")
    sendEvent(withName: "onRewardAdFailedToLoad", body: ["error": error.localizedDescription])
  }

  func ad(_ ad: AdWhaleRewardAd, didFailToPresentRewardAdWithError error: Error) {
    print("[adwhale] [sample Native] 보상형 광고 표시 실패: \(error.localizedDescription)")
    sendEvent(withName: "onRewardAdFailedToShow", body: ["error": error.localizedDescription])
  }

  func adWillPresentRewardAd(_ ad: AdWhaleRewardAd) {
    print("[adwhale] [sample Native] 보상형 광고가 표시될 예정")
    sendEvent(withName: "onRewardAdWillPresent", body: nil)
  }

  func adDidDismissRewardAd(_ ad: AdWhaleRewardAd) {
    print("[adwhale] [sample Native] 보상형 광고가 닫힘")
    sendEvent(withName: "onRewardAdDismissed", body: nil)
  }

  func adDidEarnReward(_ reward: AdWhaleReward) {
    print("[adwhale] [sample Native] 보상 획득: \(reward.amount.doubleValue), \(reward.type)")
    sendEvent(withName: "onRewardAdEarned", body: [
      "amount": reward.amount,
      "type": reward.type
    ])
  }
}
```

### 2. RewardAdModule.m

```objectivec
#import <React/RCTBridgeModule.h>

@interface RCT_EXTERN_MODULE(RewardAdModule, NSObject)

RCT_EXTERN_METHOD(loadAd:(NSString *)adUnitId
                  resolve:(RCTPromiseResolveBlock)resolve
                  rejecter:(RCTPromiseRejectBlock)reject)

RCT_EXTERN_METHOD(showAd:(RCTPromiseResolveBlock)resolve
                  rejecter:(RCTPromiseRejectBlock)reject)

@end 
```

### 3. Expo (React Native) 연동 코드

#### RewardAd.tsx

```typescript
import React, { useState, useEffect } from 'react';
import { StyleSheet, Text, View, Button, NativeEventEmitter, NativeModules } from 'react-native';

const { RewardAdModule } = NativeModules;

const REWARD_AD_UNIT_ID = '리워드 광고 AD_UNIT_ID 입력';

export default function RewardAd() {
  const [rewardLoaded, setRewardLoaded] = useState(false);
  const [isLoading, setIsLoading] = useState(false);

  useEffect(() => {
    const eventEmitter = new NativeEventEmitter(RewardAdModule);
    
    const subscriptions = [
      eventEmitter.addListener('onRewardAdLoaded', () => {
        console.log(`[adwhale] [sample UI] 보상형 광고 로드 성공`);
        setRewardLoaded(true);
        setIsLoading(false);
      }),
      eventEmitter.addListener('onRewardAdFailedToLoad', (event) => {
        console.error(`[adwhale] [sample UI] 보상형 광고 로드 실패: ${event.error}`);
        setRewardLoaded(false);
        setIsLoading(false);
      }),
      eventEmitter.addListener('onRewardAdFailedToShow', (event) => {
        console.error(`[adwhale] [sample UI] 보상형 광고 표시 실패: ${event.error}`);
        setRewardLoaded(false);
      }),
      eventEmitter.addListener('onRewardAdWillPresent', () => {
        console.log(`[adwhale] [sample UI] 보상형 광고가 표시될 예정`);
      }),
      eventEmitter.addListener('onRewardAdDismissed', () => {
        console.log(`[adwhale] [sample UI] 보상형 광고가 닫힘`);
        setRewardLoaded(false);
      }),
      eventEmitter.addListener('onRewardAdEarned', (event) => {
        console.log(`[adwhale] [sample UI] 보상 획득: amount=${event.amount}, type=${event.type}`);
      })
    ];

    return () => {
      subscriptions.forEach(subscription => subscription.remove());
    };
  }, []);

  const loadReward = async () => {
    try {
      setIsLoading(true);
      await RewardAdModule.loadAd(REWARD_AD_UNIT_ID);
    } catch (error) {
      console.error(`[adwhale] [sample UI] 보상형 광고 로드 실패: ${error}`);
      setIsLoading(false);
    }
  };

  const showReward = async () => {
    try {
      await RewardAdModule.showAd();
    } catch (error) {
      console.error(`[adwhale] [sample UI] 보상형 광고 표시 실패: ${error}`);
    }
  };

  return (
    <View style={styles.section}>
      <Text style={styles.title}>보상형 광고</Text>
      <Button
        title="보상형 광고 로드"
        onPress={loadReward}
        disabled={isLoading || rewardLoaded}
      />
      <Button
        title="보상형 광고 표시"
        onPress={showReward}
        disabled={!rewardLoaded}
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
import RewardAd from './src/components/RewardAd';

export default function App() {
  return (
    <SafeAreaView style={styles.safeArea}>
      <View style={styles.mainViewStyle}>
        <StatusBar style="auto" />
        <View style={styles.contentViewStyle}>
            <RewardAd />
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
