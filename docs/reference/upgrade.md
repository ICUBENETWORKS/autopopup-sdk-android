# 버전 올리기

SDK 버전을 올릴 때는 앱 모듈의 의존성 버전만 바꾸면 됩니다. 버전별로 따로 바꿔야 하는 코드가 있으면 아래에 적습니다.

## AutoPopup SDK 1.0.6에서 옮겨 오기

클래스·함수 이름과 패키지 좌표(`com.icubeon:autopopup-sdk`)는 그대로입니다. 저장소와 버전만 바꿉니다.

#### 1. 저장소 주소를 바꿉니다.

예전 GitHub Packages 저장소(`maven.pkg.github.com/...`)와 `gpr.user`·`gpr.key` 설정을 지우고 아래 저장소를 넣습니다. 토큰이 필요 없습니다.

**settings.gradle**

```gradle
maven { url 'https://icubenetworks.github.io/autopopup-sdk-android/maven' }
```

**settings.gradle.kts**

```kotlin
maven("https://icubenetworks.github.io/autopopup-sdk-android/maven")
```

#### 2. 의존성 버전을 바꿉니다.

```gradle
implementation 'com.icubeon:autopopup-sdk:1.1.0'
```

#### 3. SDK 때문에 넣어 둔 라이브러리를 정리합니다.

SDK를 AAR 파일로 넣으면서 Retrofit, Glide, Lottie 등을 앱에 직접 넣었다면 지워도 됩니다. 저장소에서 받으면 SDK와 함께 받아집니다.

#### 4. Play Console 신고를 고칩니다.

1.0.8부터 광고 ID 권한이 앱에 합쳐집니다. Play Console의 광고 ID 선언과 데이터 보안 양식을 고칩니다. → [6. 개인정보 처리방침과 Play 데이터 보안](../before-release/privacy-and-play.md)

## 버전별로 바꿔야 하는 코드

1.0.6부터 1.1.0까지 바꿔야 하는 코드는 없습니다.

## 지원 기간

[정책 확정 후 작성: SDK 버전 지원 기간과 지원 종료 안내 방식]
