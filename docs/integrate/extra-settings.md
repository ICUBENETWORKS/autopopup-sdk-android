# 4. 추가 설정

앱에 따라 필요할 때만 넣는 설정입니다.

## 코드 난독화 (선택)

SDK에 난독화 규칙이 들어 있어 일반적인 R8 설정에서는 따로 할 것이 없습니다. DexGuard 같은 별도 난독화 도구를 쓰면 아래 규칙을 추가합니다.

**proguard-rules.pro**

```proguard
-keep class com.icubeon.autopopup.AutoPopupManager { *; }
-keep class com.icubeon.autopopup.Constant { *; }
-keep class com.icubeon.autopopup.AppIdQueryReceiver { *; }
-keep class com.icubeon.autopopup.model.** { *; }
-keep class com.google.firebase.** { *; }
-keep class com.google.android.gms.** { *; }
-keep class com.google.gson.** { *; }
-keepattributes Signature
-keepattributes *Annotation*
-dontwarn com.google.firebase.**
-dontwarn com.google.android.gms.**
```

## 전화번호로 보내는 앱 (선택)

전화번호로 수신자를 지정해 보내려면 전화번호 권한을 선언하고 [권한 요청](permissions.md) 단계에서 함께 요청합니다. 전화번호 발송을 쓰지 않으면 넣지 않아도 되고, 그때는 푸시 토큰과 광고 ID로만 발송합니다.

**AndroidManifest.xml**

```xml
<!-- Android 10 이상 -->
<uses-permission android:name="android.permission.READ_PHONE_NUMBERS" />
<!-- Android 10 미만 -->
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
```

사용자가 거부해도 SDK 초기화와 팝콘메시지 수신은 정상으로 진행되며, 전화번호 없이 서버에 등록됩니다.

## 배터리 최적화 예외 (선택)

배터리 최적화 때문에 메시지가 늦게 오는 기기가 있으면 예외 설정 화면으로 안내합니다. 앱 정책에 따라 넣습니다.

**Kotlin**

```kotlin
val pm = getSystemService(POWER_SERVICE) as PowerManager
if (!pm.isIgnoringBatteryOptimizations(packageName)) {
    startActivity(
        Intent(Settings.ACTION_REQUEST_IGNORE_BATTERY_OPTIMIZATIONS)
            .setData(Uri.parse("package:$packageName"))
    )
}
```

**AndroidManifest.xml**

```xml
<uses-permission android:name="android.permission.REQUEST_IGNORE_BATTERY_OPTIMIZATIONS" />
```

> [!WARNING]
> Google Play는 `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS` 권한을 일부 용도에만 허용합니다. 앱 심사 정책을 확인한 뒤 넣습니다.

## 백그라운드 실행

SDK는 포그라운드 서비스를 쓰지 않습니다. Android 14의 포그라운드 서비스 유형 선언이나 Android 15의 포그라운드 서비스 시작 제한에 대응할 설정은 없습니다. 팝콘메시지를 받으면 SDK가 FCM 수신 처리 중에 화면 표시 서비스를 바로 시작합니다.
