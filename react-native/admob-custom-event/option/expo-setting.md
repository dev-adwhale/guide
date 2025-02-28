# expo 환경 설정

{% hint style="info" %}
본 문서는 expo 환경에서 ADwhale Mediation SDK를 연동하는 경우

expo 프로젝트 생성 및 android 폴더 생성을 위한 가이드 문서입니다.
{% endhint %}



### 1. expo 프로젝트 생성

* 터미널에서 다음 명령어를 입력해 프로젝트를 생성합니다.
* 자세한 프로젝트 생성 옵션은 [링크](https://docs.expo.dev/more/create-expo/) 참고 부탁드립니다.&#x20;

```
// 터미널

npx create-expo-app {프로젝트 명}
```



### 2. package name 설정

* app.json 파일에서 다음과 같이 package name을 추가합니다.

```
// app.json

"android": {
      "package": "com.yourcompany.yourappname"
}
```



### 3. android 폴더 생성

* 프로젝트 경로에 android 폴더가 없는 경우 다음 명령어로 android 폴더가 생성됩니다.

```
// 터미널

npx expo run:android
```



### 4. Custom Event 코드 생성

* 생성된 android/app/src/main/java/{package name} 위치에 [Custom Event](../custom-events/) 설정을 진행합니다.
