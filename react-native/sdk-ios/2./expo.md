# Expo 환경 설정

{% hint style="info" %}
본 문서는 Expo 환경에서 AdWhale SDK를 연동하는 경우

Expo 프로젝트 생성 및 ios 폴더 생성을 위한 가이드 문서입니다.
{% endhint %}

### 1. Expo 프로젝트 생성

* 터미널에서 다음 명령어를 입력해 프로젝트를 생성합니다.
* 자세한 프로젝트 생성 옵션은 [링크](https://docs.expo.dev/more/create-expo/) 참고 부탁드립니다.

```ruby
// 터미널
npx create-expo-app {프로젝트 명}
```



### 2. bundle Identifier 설정

* app.json 파일에서 다음과 같이 bundle Identifier 를 추가합니다.

```json
// app.json
"ios": {
    ...
    "bundleIdentifier": "com.adwhale.expo"
}
```



### 3. ios 폴더 생성

* 프로젝트 경로에 ios 폴더가 없는 경우 다음 명령어로 ios 폴더가 생성됩니다.
* 이때 생성된 iOS 프로젝트의 Bundle Identifier는 위 과정에서 app.json에 설정한 값으로 자동 설정됩니다.

```ruby
// 터미널
npx expo run:ios
```
