# BannerAdCreator

```java

import android.content.Context;

import androidx.annotation.NonNull;

import net.adwhale.sdk.mediation.ads.AdWhaleMediationAdView;

public class BannerAdCreator {

    @NonNull
    private final Context context;

    public BannerAdCreator(@NonNull Context context) {
        this.context = context;
    }

    public AdWhaleMediationAdView createAdView() {
        return new AdWhaleMediationAdView(context);
    }
}
```
