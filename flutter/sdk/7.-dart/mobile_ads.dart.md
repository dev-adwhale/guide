# mobile\_ads.dart

```dart
import 'dart:io';

import 'ad_instance_manager.dart';

class MobileAds {
  MobileAds._();

  static final MobileAds _instance = MobileAds._().._init();

  static MobileAds get instance => _instance;

  void initialize() {
    // nothing

  }

  void _init() {
    if (Platform.isAndroid) {
      instanceManager.channel.invokeMethod('_init');
    }
  }
}

```
