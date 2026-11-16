# AutoPopup SDK for Android

AutoPopup SDK 의 **배포 전용 저장소**입니다. 소스 코드는 포함하지 않으며, 빌드된 AAR 이 [GitHub Packages](../../packages) 로 배포됩니다.

## 설치

### 1. GitHub 토큰 준비
GitHub Packages 의 Maven 저장소는 공개 패키지라도 내려받을 때 인증이 필요합니다. **아무 GitHub 계정**으로 `read:packages` 권한의 Personal access token (classic) 을 발급하세요. (조직 권한은 필요 없습니다.)

`~/.gradle/gradle.properties` (프로젝트에 커밋하지 마세요):
```properties
gpr.user=<GitHub 사용자명>
gpr.key=<read:packages 토큰>
```

### 2. 저장소 추가 (`settings.gradle`)
```groovy
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        maven {
            url = uri("https://maven.pkg.github.com/ICUBENETWORKS/autopopup-sdk-android")
            credentials {
                username = providers.gradleProperty("gpr.user").getOrElse(System.getenv("GPR_USER") ?: "")
                password = providers.gradleProperty("gpr.key").getOrElse(System.getenv("GPR_TOKEN") ?: "")
            }
        }
    }
}
```

### 3. 의존성 추가 (`app/build.gradle`)
```groovy
dependencies {
    implementation 'com.icubeon:autopopup-sdk:1.0.8'
}
```

## 버전
| 버전 | 주요 변경 |
|---|---|
| 1.0.8 | 기기 식별자(AAID / SDK 공유 AppId) 등록 전송, Type 5 아이콘 닫기 버튼, Type 4/5 최초 탭 시 호스트 앱 실행, 다른 앱 위에 표시 권한이 없을 때 알림만 표시 |
