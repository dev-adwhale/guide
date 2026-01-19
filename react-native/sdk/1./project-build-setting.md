# 1.3 프로젝트 빌드 설정(build.gradle)

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
        // ADwhale SDK Repository Public Access Info
        maven { url 'https://dev-adwhale.github.io/adwhale-sdk-android-maven/maven-repo' }

        // Cauly SDK Repository Public Access Info
        maven { url 'https://cauly.github.io/cauly-sdk-android-maven/maven-repo' }
        
        // Admize SDK Repository Public Access Info
        maven { url 'https://cauly.github.io/admize-sdk-android-maven/maven-repo' }

        // AdFit SDK Repository Public Access Info
        maven { url 'https://devrepo.kakao.com/nexus/content/groups/public/' }
    }
}
```

