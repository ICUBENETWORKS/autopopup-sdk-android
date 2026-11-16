# 권한 요청

팝콘메시지를 표시하려면 [수신 동의](consent.md)를 받은 사용자에게 휴대폰 권한 두 가지를 요청합니다.

* **다른 앱 위에 표시** (`SYSTEM_ALERT_WINDOW`): 화면 위 아이콘과 카드를 띄우는 데 필요합니다. 없으면 아이콘 대신 알림으로 받습니다.
* **알림** (`POST_NOTIFICATIONS`, Android 13 이상): 알림을 띄우는 데 필요합니다. 다른 앱 위에 표시 권한도 없고 알림 권한도 없으면 팝콘메시지가 표시되지 않습니다.

광고 ID(`AD_ID`)는 사용자에게 묻는 권한이 아니라 매니페스트에 선언되는 권한이며, SDK에 들어 있어 자동으로 합쳐집니다. [SDK 설치와 초기화](install-and-init.md)를 참고합니다.

## 권한 요청하기

SDK에는 권한을 대신 확인·요청하는 함수가 없으므로 앱이 Android 표준 방법으로 요청합니다.

**1. 권한이 필요한 이유를 먼저 안내합니다**

이미 권한이 있으면 건너뜁니다.

**2. 알림 권한을 요청합니다**

Android 13 이상에서 시스템 권한 창으로 요청합니다.

**Kotlin**

```kotlin
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.TIRAMISU) {
    permissionLauncher.launch(arrayOf(Manifest.permission.POST_NOTIFICATIONS))
}
```

**Java**

```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.TIRAMISU) {
    permissionLauncher.launch(new String[]{Manifest.permission.POST_NOTIFICATIONS});
}
```

**3. 다른 앱 위에 표시 권한 화면으로 이동합니다**

이 권한은 사용자가 휴대폰 설정 화면에서 직접 켜야 합니다. Android 6.0 미만은 설치할 때 자동으로 허용됩니다.

**Kotlin**

```kotlin
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M && !Settings.canDrawOverlays(this)) {
    val uri = Uri.parse("package:$packageName")
    startActivity(Intent(Settings.ACTION_MANAGE_OVERLAY_PERMISSION, uri))
}
```

**Java**

```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M && !Settings.canDrawOverlays(this)) {
    startActivity(new Intent(Settings.ACTION_MANAGE_OVERLAY_PERMISSION,
        Uri.parse("package:" + getPackageName())));
}
```

**4. 돌아오면 권한을 다시 확인하고 SDK를 켭니다**

`enableAutoPopup()`을 호출합니다. → [SDK 초기화](install-and-init.md)

> [!WARNING]
> 알림 권한 창은 사용자가 거부하면 다시 띄울 수 없는 경우가 있습니다. 권한 창 전에 필요한 이유를 먼저 안내합니다.

배터리 최적화 예외, 전화번호 권한은 [추가 설정](extra-settings.md)을 참고합니다.

## 권한이 없을 때

SDK는 팝콘메시지를 받은 시점에 다른 앱 위에 표시 권한을 확인합니다.

* **다른 앱 위에 표시 권한이 없으면** 화면 위 아이콘과 카드를 띄우지 않고 팝콘메시지를 알림으로 받습니다. 알림을 눌렀을 때의 동작은 아래 "알림을 눌렀을 때"를 참고합니다.
* **알림 권한이 없으면** 알림은 표시되지 않습니다. 다른 앱 위에 표시 권한이 있으면 화면 위 아이콘은 그대로 표시됩니다.
* **사용자가 권한을 거부하면** 시스템 창을 다시 띄울 수 없는 경우가 있습니다. 앱 설정의 팝콘메시지 메뉴에서 휴대폰 설정으로 이동할 수 있게 합니다.

## 알림을 눌렀을 때

팝콘메시지 알림을 누르면 SDK가 누른 시점의 상태를 보고 동작을 정합니다. (SDK 1.1.1부터)

* **카드 메시지이고 다른 앱 위에 표시 권한이 있으면** 화면 위에 카드를 펼칩니다. 알림을 받은 뒤 권한을 허용한 경우도 포함됩니다.
* **카드 메시지가 아니거나 권한이 없으면** 메시지에 지정된 링크를 브라우저로 엽니다. 카드 정보를 5초 안에 받지 못해도 링크를 엽니다.
* **링크도 없으면** 앱을 실행합니다.

> [!NOTE]
> SDK 1.1.0에서는 다른 앱 위에 표시 권한이 없을 때 받은 알림을 누르면 첫 번째 카드의 이미지 링크가 열립니다.

알림으로 받을 때 알림 채널 이름은 "오토팝업 알림"이며, 알림 아이콘과 발신자명은 [SDK 설치와 초기화](install-and-init.md)의 "알림 표시 정보 설정하기"를 따릅니다.
