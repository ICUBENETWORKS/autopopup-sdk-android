# 2. SDK 설치와 초기화

SDK를 앱에 추가하고, 수신 동의를 받은 사용자에게 SDK를 켭니다.

## SDK 설치

### 의존성 추가하기

#### 1. 저장소를 추가합니다.

별도 계정이나 토큰 없이 받을 수 있습니다.

**settings.gradle**

```gradle
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        maven { url 'https://icubenetworks.github.io/autopopup-sdk-android/maven' }
    }
}
```

**settings.gradle.kts**

```kotlin
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        maven("https://icubenetworks.github.io/autopopup-sdk-android/maven")
    }
}
```

#### 2. 앱 모듈에 의존성을 추가합니다.

**build.gradle**

```gradle
dependencies {
    implementation 'com.icubeon:autopopup-sdk:1.1.1'

    implementation platform('com.google.firebase:firebase-bom:33.16.0')
    implementation 'com.google.firebase:firebase-messaging'
}
```

**build.gradle.kts**

```kotlin
dependencies {
    implementation("com.icubeon:autopopup-sdk:1.1.1")

    implementation(platform("com.google.firebase:firebase-bom:33.16.0"))
    implementation("com.google.firebase:firebase-messaging")
}
```

SDK가 쓰는 AndroidX, Firebase, Retrofit·OkHttp, Glide, Lottie, YouTube 플레이어, 광고 ID 라이브러리는 SDK와 함께 받아지므로 앱에 따로 넣지 않습니다. `firebase-messaging`은 앱에서 `FirebaseMessagingService`를 직접 만들기 때문에 명시합니다.

> [!NOTE]
> 앱이 같은 라이브러리를 다른 버전으로 쓰고 있으면 Gradle이 높은 버전 하나로 맞춥니다. 메이저 버전 차이가 크면 동작을 확인합니다.

### Firebase 설정하기

#### 1. Firebase 콘솔에서 앱 패키지명으로 Android 앱을 등록합니다.

받은 `google-services.json`을 `app/` 폴더에 넣습니다.

#### 2. Google Services 플러그인을 적용합니다.

**build.gradle**

```gradle
// 루트 build.gradle
buildscript {
    dependencies {
        classpath 'com.google.gms:google-services:4.4.3'
    }
}

// app/build.gradle
plugins {
    id 'com.google.gms.google-services'
}
```

**build.gradle.kts**

```kotlin
// 루트 build.gradle.kts
plugins {
    id("com.google.gms.google-services") version "4.4.3" apply false
}

// app/build.gradle.kts
plugins {
    id("com.google.gms.google-services")
}
```

이미 FCM을 쓰고 있다면 [기존 앱 푸시와 함께 쓰기](existing-fcm.md)를 함께 봅니다.

### 매니페스트 설정하기

SDK에 필요한 권한과 내부 구성 요소는 SDK 매니페스트에 들어 있어 앱 매니페스트에 자동으로 합쳐집니다. 앱에는 FCM 수신 서비스만 등록합니다.

**AndroidManifest.xml**

```xml
<application>
    <service
        android:name=".MyFirebaseMessagingService"
        android:exported="false">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT" />
        </intent-filter>
    </service>
</application>
```

자동으로 합쳐지는 주요 항목은 아래와 같습니다.

* 권한: 인터넷, 네트워크 상태, 알림(`POST_NOTIFICATIONS`), 다른 앱 위에 표시(`SYSTEM_ALERT_WINDOW`), 광고 ID(`com.google.android.gms.permission.AD_ID`)
* 팝콘메시지 표시 서비스
* 같은 기기에서 POPCON SDK를 넣은 다른 앱과 기기 식별자를 주고받는 리시버(`com.icubeon.autopopup.AppIdQueryReceiver`)와 패키지 조회 선언(`<queries>`)

> [!WARNING]
> 광고 ID 권한(`AD_ID`)을 `tools:node="remove"` 등으로 빼면 광고 ID를 받을 수 없습니다. 이때 SDK는 다른 앱과 공유하는 자체 식별자로 대신 등록합니다. 병합 매니페스트에서 `AD_ID`가 빠지지 않았는지 확인합니다.

전화번호 권한, 배터리 최적화 예외처럼 앱에 따라 넣는 설정은 [추가 설정](extra-settings.md)에 있습니다.

## SDK 초기화

### 초기화하기

#### 1. 수신 동의와 권한을 받은 뒤 `enableAutoPopup()`을 호출합니다.

SDK 켜기와 서버 등록이 함께 진행됩니다. 동의와 권한은 [팝콘메시지 수신 설정](README.md)을 참고합니다.

**Kotlin**

```kotlin
AutoPopupManager.getInstance(applicationContext)
    .enableAutoPopup(object : AutoPopupCompleteListener {
        override fun onCompleteListener() {
            Log.d("PopconSDK", "SDK init success")
        }
        override fun onFailureListener(errorCode: Int) {
            Log.e("PopconSDK", "SDK init failed: $errorCode")
        }
    })
```

**Java**

```java
AutoPopupManager.getInstance(getApplicationContext())
    .enableAutoPopup(new AutoPopupCompleteListener() {
        @Override
        public void onCompleteListener() {
            Log.d("PopconSDK", "SDK init success");
        }
        @Override
        public void onFailureListener(int errorCode) {
            Log.e("PopconSDK", "SDK init failed: " + errorCode);
        }
    });
```

#### 2. 앱이 시작될 때 `autoPopupInit()`으로 다시 초기화합니다.

`Application.onCreate()` 또는 첫 화면에서 한 번 호출합니다. SDK가 켜진 상태일 때만 진행되며, 푸시 토큰이 아직 서버에 등록되지 않았으면 다시 등록합니다. 권한을 다시 받은 뒤에도 호출할 수 있습니다.

```kotlin
AutoPopupManager.getInstance(applicationContext).autoPopupInit(listener)
```

> [!NOTE]
> 팝콘메시지 수신과 표시는 FCM 수신 서비스에서 처리하므로, 사용자가 아이콘을 눌러 앱이 새로 시작될 때 초기화를 다시 하지 않아도 표시에는 영향이 없습니다.

#### 3. 수신 동의를 철회하면 `disableAutoPopup()`을 호출합니다.

```kotlin
AutoPopupManager.getInstance(applicationContext).disableAutoPopup()
```

> [!NOTE]
> 초기화 결과는 `onCompleteListener()` 또는 `onFailureListener(errorCode)`로 받습니다. 오류 코드는 [API 레퍼런스](../reference/api.md)에서 확인합니다.

### 알림 표시 정보 설정하기 (선택)

다른 앱 위에 표시 권한이 없어 팝콘메시지를 알림으로 받을 때, 알림 아이콘과 발신자명은 `setClientId()` 값으로 정해집니다. 운영 담당자가 값을 안내한 경우에만 초기화 전에 설정합니다. 설정하지 않으면 POPCON 아이콘과 발신자명으로 표시됩니다.

```kotlin
AutoPopupManager.getInstance(applicationContext).setClientId("POPCON")
```
