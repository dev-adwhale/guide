# 2. 프로젝트 빌드 설정(build.gradle)

프로젝트 단위의 "**build.gradle**" 설정에 다음 항목을 추가 합니다.

{% hint style="info" %}
**원격 저장소 목록**

* mavenCentral()
{% endhint %}

```gradle
// project / build.gradle
allprojects {
    repositories {
        google()
        mavenCentral()
        // ADwhale Mediation SDK Repository Public Access Info
        maven {
            name = "GitHubPackages"
            url = uri("https://maven.pkg.github.com/dev-adwhale/AdWhaleAndroid/SDK")
            credentials {
                username = 'dev-adwhale'
                password = 'SDK 부록페이지 참고(https://adwhale.gitbook.io/adwhale-mediation-sdk-android-1)'
            }
        }
        
        // Cauly SDK: 3.5.33 이하만 적용가능
        maven {
            url "s3://repo.cauly.net/releases"
            credentials(AwsCredentials) {
                accessKey "SDK 부록페이지 참고(https://adwhale.gitbook.io/adwhale-mediation-sdk-android-1)"
                secretKey "SDK 부록페이지 참고(https://adwhale.gitbook.io/adwhale-mediation-sdk-android-1)"
            }
        }
        maven { url 'https://devrepo.kakao.com/nexus/content/groups/public/' }
        maven { url "https://bidmad-sdk.s3.amazonaws.com/" }
    }
}
```

### 최신 SDK version: 2.5.1

### dependencies 설정

앱 단위 "**build.gradle**" 설정에 다음 항목을 확인 후 설정 합니다.&#x20;

```gradle
// app / build.gradle
android {
    ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_11
        targetCompatibility JavaVersion.VERSION_11
    }
}

dependencies {
    ...
    // 기본 디펜던시: ADwhale Mediation SDK Repository
    implementation 'net.adwhale.sdk.mediation:adwhale-mediation-sdk:1.0.5'

    // 옵션 디펜던시(제외가능): Cauly Adapter SDK Repository
    implementation 'net.adwhale.sdk.cauly.adapter:cauly-sdk:3.5.32'
    
    // 옵션 디펜던시(제외가능): AdFit Adapter SDK Repository
    implementation 'net.adwhale.sdk.adfit.adapter:adfit-sdk:3.16.0'

    // 옵션 디펜던시(제외가능): Techlabs Adapter SDK Repository
    implementation 'net.adwhale.sdk.techlabs.adapter:techlabs-sdk:3.15.0'
    ...
}


```

{% hint style="info" %}
**Gradle Sync 에러 발생 시 캐시 삭제 후 재 빌드 부탁드립니다.**

캐시 삭제 : AndroidStudio 상단 file 탭 → Invalidate Cache / Restart... → Invalidate and Restart

&#x20;

**\* 캐시 삭제 후에도 에러 발생 시 수동 SDK 삭제 후 재 빌드 부탁드립니다.**&#x20;

* Android Studio 의 gradle 창 > gradle --build-cache 명령 입력 및 실행 > gradle assemble --refresh-dependencies 명령 입력 및 실행 > Android Studio 상단 build > Clean & Rebuild project 실행



**\* 위의 수동 SDK 삭제 후에도 에러 발생 시 SDK 캐시 경로에서 직접 삭제 후 재 빌드 부탁드립니다.**

* /Users/.gradle/caches/modules-2/files-2.1/net.adwhale.sdk.mediation
{% endhint %}

{% hint style="warning" %}
**Gradle 의존성 버전 충돌이 발생할 수 있습니다.**

앱과 SDK 간에 중복 사용중인 의존성에 대해 버전 충돌이 생길 수 있습니다. 이런 경우, 앱 프로젝트의 build.gradle에서 라이브러리의 버전을 특정 버전으로 강제 통일하여 해결할 수 있습니다.

```gradle
// 앱 level build.gradle 예시
configurations.all {
        resolutionStrategy.force 'com.android.support:support-annotations:23.4.0' // 충돌나는 com.android.support:support-annotations 의존성 버전을 23.4.0 버전으로 강제 통일
}

```
{% endhint %}

