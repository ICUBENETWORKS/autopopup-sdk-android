# AutoPopup SDK for Android (POPCON SDK)

POPCON SDK(AutoPopup SDK)의 **배포 전용 저장소**입니다. 소스 코드는 포함하지 않으며, 빌드된 AAR을 토큰 없이 받을 수 있는 Maven 저장소로 배포합니다.

## 연동 가이드

전체 연동 방법은 **[POPCON SDK 연동 가이드](docs/README.md)** 를 참고하세요.

- [1. 연동 준비](docs/getting-started/quick-start.md)
- [2. SDK 설치와 초기화](docs/integrate/install-and-init.md)
- [3. 팝콘메시지 수신 설정](docs/integrate/README.md)
- [7. 문제 해결](docs/troubleshooting.md)
- [8. API 레퍼런스](docs/reference/api.md)
- [9. 릴리스 노트](docs/reference/release-notes.md)

## 설치

### 1. 저장소 추가 (`settings.gradle`)

별도 계정이나 토큰 없이 받을 수 있습니다.

```groovy
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        maven { url 'https://icubenetworks.github.io/autopopup-sdk-android/maven' }
    }
}
```

### 2. 의존성 추가 (`app/build.gradle`)

```groovy
dependencies {
    implementation 'com.icubeon:autopopup-sdk:1.1.1'
}
```

SDK가 사용하는 라이브러리는 함께 설치되므로 따로 선언하지 않아도 됩니다.

## 버전
| 버전 | 주요 변경 |
|---|---|
| 1.1.1 | 2026-11-16 배포 |
| 1.1.0 | 토큰 없이 받는 배포 저장소, 광고 스플래시 전체화면, 메시지 광고 정보(스플래시·배너) 수신 |
| 1.0.8 | 기기 식별자(광고 ID / 서비스 식별자) 등록 전송, 아이콘 닫기 버튼, 아이콘·배너 최초 탭 시 앱 실행, 다른 앱 위에 표시 권한이 없을 때 알림으로 표시 |

자세한 내용은 [릴리스 노트](docs/reference/release-notes.md)를 참고하세요.

## 문의

[popcon@popcon.biz](mailto:popcon@popcon.biz)
