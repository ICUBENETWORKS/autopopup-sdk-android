# 8. API 레퍼런스

SDK 1.1.0 기준입니다. 클래스는 모두 `com.icubeon.autopopup` 패키지에 있습니다.

## AutoPopupManager

### getInstance(context)

```java
AutoPopupManager getInstance(Context context)
```

SDK 관리 객체를 돌려줍니다. 다른 함수를 부르기 전에 호출합니다. `applicationContext`를 넘깁니다.

### enableAutoPopup(listener)

```java
void enableAutoPopup(AutoPopupCompleteListener listener)
```

SDK를 켜고 초기화와 서버 등록을 합니다. 수신 동의와 권한을 받은 뒤 호출합니다.

### autoPopupInit(listener)

```java
void autoPopupInit(AutoPopupCompleteListener listener)
```

켜진 상태일 때 다시 초기화합니다. 푸시 토큰이 아직 서버에 등록되지 않았으면 다시 등록합니다. 앱이 시작될 때나 권한을 다시 받은 뒤 호출합니다.

### disableAutoPopup()

```java
void disableAutoPopup()
```

SDK를 끕니다. 꺼진 상태에서는 팝콘메시지가 와도 표시하지 않습니다. 수신 동의 철회, 회원 탈퇴 때 호출합니다.

### autoPopupFCMHandler(message, listener)

```java
boolean autoPopupFCMHandler(RemoteMessage message, AutoPopupCallbackListener listener)
```

팝콘메시지인지 확인하고 표시합니다. 팝콘메시지이면 `true`를 돌려줍니다. `onMessageReceived()`에서 호출합니다.

### autoPopupOnNewToken(listener)

```java
void autoPopupOnNewToken(AutoPopupCompleteListener listener)
```

새 푸시 토큰으로 서버에 다시 등록합니다. `onNewToken()`에서 호출합니다. SDK가 꺼진 상태이면 등록하지 않습니다.

### getAutoPopupToken()

```java
String getAutoPopupToken()
```

SDK에 저장된 토큰을 돌려줍니다. 디버깅용입니다.

### isTokenSendedServer()

```java
String isTokenSendedServer()
```

서버 등록 요청 결과를 `"true"`, `"false"`, `"none"`(아직 등록하지 않음)으로 돌려줍니다. 디버깅용입니다.

### setClientId(clientId)

```java
void setClientId(String clientId)
```

다른 앱 위에 표시 권한이 없어 알림으로 표시할 때의 알림 아이콘과 발신자명을 정합니다. 운영 담당자가 안내한 값(`POPCON`, `MART`, `HM`)만 쓰며, 설정하지 않으면 `POPCON`으로 표시됩니다. 초기화 전에 호출합니다.

## 콜백

### AutoPopupCompleteListener

초기화, 토큰 갱신, 서버 등록 결과를 받습니다.

```java
public interface AutoPopupCompleteListener {
    void onCompleteListener();
    void onFailureListener(int errorCode);
}
```

`onCompleteListener()`는 서버 등록 요청이 응답을 받으면 호출됩니다. 서버에 등록되지 않은 패키지명처럼 서버가 등록을 받아들이지 않은 경우도 오류 코드로 구분하지 않으므로, 연동 상태는 관리자에서 확인합니다.

### AutoPopupCallbackListener

팝콘메시지 처리 결과를 받습니다.

```java
public interface AutoPopupCallbackListener {
    void autoPopupSuccess(String link);
    void autoPopupFail(int errorCode);
}
```

* `autoPopupSuccess(link)`: 알림까지 처리했을 때 호출됩니다. `link`는 메시지에 지정된 링크입니다(SDK 1.1.0에서 권한이 없을 때는 첫 번째 카드의 이미지 링크).
* `autoPopupFail(errorCode)`: 팝콘메시지를 표시하지 않았을 때 호출됩니다.

상황별로 받는 결과는 아래와 같습니다.

| 상황 | 결과 |
|---|---|
| 수신 동의 철회 등으로 SDK가 꺼짐 | `autoPopupFail(3105)` |
| 다른 앱 위에 표시 권한 없음 | 오류 아님. 알림으로 표시하고 `autoPopupSuccess(link)` |
| 관리자에 등록되지 않은 패키지명 | 오류 코드로 알 수 없음. 관리자 연동 상태로 확인 |

## 오류 코드

| 코드 | 의미 | 확인할 것 |
|---:|---|---|
| 0 | 성공 | |
| 1104 | 네트워크 연결 없음 | 네트워크 상태 |
| 3105 | 원격 설정 또는 켜짐 상태가 꺼져 있음 | 동의·`enableAutoPopup()` 호출 |
| 3106 | 원격 설정 받기 실패 | 네트워크, Firebase 설정 |
| 3107 | Firebase 초기화, 원격 설정 초기화 또는 서버 등록 실패 | Firebase 설정, 서버 통신 |
| 3108 | 사용자 켜짐 값이 꺼짐 | `enableAutoPopup()` 호출 여부 |
| 3201 | Firebase Messaging 클래스 없음 | Firebase Messaging 의존성 |
| 3202 | Firebase Messaging 함수 없음 | Firebase Messaging 버전 충돌 |
| 3203 | 토큰 받기 실패(ExecutionException) | Firebase 설정, 네트워크 |
| 3204 | 토큰 받기 중단(InterruptedException) | 다시 시도 |
| 3205 | Firebase 입출력 오류 | 네트워크, Firebase |
| 3206·3207 | 예전 Firebase 방식 실패 | 최신 Firebase Messaging 사용 |
| 3208·3209 | 토큰 받기 중 예외 | Firebase 버전, 난독화, 상세 로그 |
| 3299 | Firebase 알 수 없는 오류 | 상세 로그 |
