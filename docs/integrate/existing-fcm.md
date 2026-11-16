# 기존 앱 푸시와 함께 쓰기

앱이 이미 Firebase Cloud Messaging(FCM)으로 푸시를 받고 있어도 팝콘메시지를 함께 쓸 수 있습니다. **팝콘메시지는 SDK가 처리하고, 나머지 푸시는 앱이 지금처럼 처리합니다.**

## FCM 수신 서비스 연결하기

#### 1. 메시지를 받으면 먼저 SDK에 넘깁니다.

`autoPopupFCMHandler()`가 `true`를 돌려주면 팝콘메시지이므로 SDK가 처리합니다. 앱 처리를 하지 않고 끝냅니다.

#### 2. 푸시 토큰이 바뀌면 SDK에도 알립니다.

`autoPopupOnNewToken()`을 호출해 서버에 다시 등록합니다.

**Kotlin**

```kotlin
class MyFirebaseMessagingService : FirebaseMessagingService() {

    private val callback = object : AutoPopupCallbackListener {
        override fun autoPopupSuccess(link: String) {}
        override fun autoPopupFail(errorCode: Int) {
            Log.e("PopconSDK", "failed: $errorCode")
        }
    }

    override fun onMessageReceived(message: RemoteMessage) {
        val handled = AutoPopupManager
            .getInstance(applicationContext)
            .autoPopupFCMHandler(message, callback)
        if (handled) return

        // 앱 자체 푸시 처리
    }

    override fun onNewToken(token: String) {
        super.onNewToken(token)
        AutoPopupManager.getInstance(applicationContext)
            .autoPopupOnNewToken(object : AutoPopupCompleteListener {
                override fun onCompleteListener() {}
                override fun onFailureListener(errorCode: Int) {
                    Log.e("PopconSDK", "token refresh failed: $errorCode")
                }
            })
    }
}
```

**Java**

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {

    private final AutoPopupCallbackListener callback = new AutoPopupCallbackListener() {
        @Override public void autoPopupSuccess(String link) {}
        @Override public void autoPopupFail(int errorCode) {
            Log.e("PopconSDK", "failed: " + errorCode);
        }
    };

    @Override
    public void onMessageReceived(@NonNull RemoteMessage message) {
        boolean handled = AutoPopupManager.getInstance(getApplicationContext())
            .autoPopupFCMHandler(message, callback);
        if (handled) return;

        // 앱 자체 푸시 처리
    }

    @Override
    public void onNewToken(@NonNull String token) {
        super.onNewToken(token);
        AutoPopupManager.getInstance(getApplicationContext())
            .autoPopupOnNewToken(new AutoPopupCompleteListener() {
                @Override public void onCompleteListener() {}
                @Override public void onFailureListener(int errorCode) {
                    Log.e("PopconSDK", "token refresh failed: " + errorCode);
                }
            });
    }
}
```

FCM 서비스가 없는 앱은 위 서비스를 새로 만들고 [매니페스트](install-and-init.md)에 등록합니다. 앱 자체 푸시 처리 자리는 비워 둡니다.

> [!CAUTION]
> 팝콘메시지를 앱이 직접 알림으로 띄우면 같은 메시지가 두 번 표시됩니다. 팝콘메시지는 SDK에만 넘깁니다.

팝콘메시지인지는 SDK가 발신자로 구분합니다. 앱에서 따로 구분하지 않아도 됩니다.

## 처리 결과 받기

* `autoPopupSuccess(link)`: 팝콘메시지를 받아 알림까지 처리했을 때 호출됩니다. `link`는 메시지에 지정된 링크입니다. 알림을 띄우지 않는 메시지에서는 호출되지 않을 수 있습니다. 알림을 눌렀을 때의 동작은 [권한 요청](permissions.md)의 "알림을 눌렀을 때"를 참고합니다.
* `autoPopupFail(errorCode)`: 팝콘메시지를 표시하지 않았을 때 호출됩니다. 예를 들어 `disableAutoPopup()`으로 꺼진 상태이면 `3105`가 전달됩니다.

사용자가 아이콘·카드·버튼을 눌렀을 때 앱에 결과를 알려 주는 콜백이나 딥링크는 없습니다. 아이콘을 처음 누르면 앱의 첫 화면(런처 Activity)이 실행되고, 앱이 이미 실행 중이면 기존 화면이 앞으로 옵니다. 카드 버튼은 메시지에 지정된 링크를 브라우저로 엽니다.

## 함께 쓸 때 확인할 것

* 기존 앱 푸시가 이전과 같이 도착하는지
* 팝콘메시지가 알림 표시줄이 아니라 화면 위 아이콘으로 표시되는지
