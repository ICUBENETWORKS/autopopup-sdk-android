# 7. 문제 해결

## 연동과 표시

### 연동 상태가 정상으로 바뀌지 않음

* 관리자에 등록한 패키지명과 테스트 빌드의 패키지명이 같은지 확인합니다.
* SDK 초기화가 호출되는지 확인합니다. → [SDK 초기화](integrate/install-and-init.md)

### 메시지가 오지 않음

* 팝콘메시지 수신 동의 후 `enableAutoPopup()`을 호출했는지 확인합니다. → [수신 동의 연결](integrate/consent.md)
* 다른 앱 위에 표시 권한과 알림 권한이 모두 없으면 표시되지 않습니다. → [권한 요청](integrate/permissions.md)
* 관리자에서 앱이 '운영사 차단' 상태이면 앱 상세에 표시된 사유를 확인하고 문의합니다.

### 아이콘 대신 알림 표시줄로 옴

다른 앱 위에 표시 권한이 없는 상태입니다. [권한 요청](integrate/permissions.md) 순서로 권한을 받습니다.

### 아이콘이 표시되지 않음

* 다른 앱 위에 표시 권한을 확인합니다.
* 배터리 최적화로 백그라운드 실행이 막혔는지 확인합니다. 필요하면 [배터리 최적화 예외](integrate/extra-settings.md)로 안내합니다.

### 아이콘을 눌러도 앱이 실행되지 않음

* 앱은 메시지마다 아이콘을 **처음** 누를 때만 실행됩니다. 카드를 펼친 뒤 다시 최소화한 아이콘을 누를 때는 실행되지 않습니다.
* 앱에 런처 화면(`MAIN`/`LAUNCHER` Activity)이 있는지 확인합니다.

### 광고가 표시되지 않음

* 관리자 앱 상세에서 광고 동의 상태를 확인합니다.
* 다른 앱 위에 표시 권한이 없어 알림으로 받은 메시지에는 광고가 붙지 않습니다.

### 같은 메시지가 두 번 표시됨

앱의 FCM 서비스가 팝콘메시지를 직접 알림으로 띄우고 있습니다. 팝콘메시지는 SDK에만 넘깁니다. → [기존 앱 푸시와 함께 쓰기](integrate/existing-fcm.md)

### 기존 앱 푸시가 오지 않음

`autoPopupFCMHandler()`가 `false`를 돌려줄 때만 앱 자체 푸시 처리를 실행하는지 확인합니다.

## 빌드와 초기화 오류

### SDK를 찾을 수 없음

* `settings.gradle`에 `https://icubenetworks.github.io/autopopup-sdk-android/maven` 저장소가 있는지 확인합니다. → [의존성 추가하기](integrate/install-and-init.md)
* 예전 GitHub Packages 주소(`maven.pkg.github.com`)를 쓰고 있다면 위 주소로 바꿉니다. 이 주소는 토큰이 필요 없습니다.

### 초기화가 3108 또는 3105로 실패

SDK가 켜지지 않은 상태입니다. 동의한 시점에 `enableAutoPopup()`을 호출했는지 확인합니다. `autoPopupInit()`만 호출하면 꺼진 상태일 수 있습니다.

### 초기화가 3201 또는 3202로 실패

Firebase Messaging 라이브러리가 없거나 버전이 충돌합니다. `firebase-messaging` 의존성과 Firebase BoM 버전을 확인합니다.

### 알림이 표시되지 않음

Android 13 이상에서 알림 권한(`POST_NOTIFICATIONS`)을 받았는지 확인합니다.

> [!NOTE]
> 오류 코드 전체는 [API 레퍼런스](reference/api.md)에 있습니다. 원인을 찾을 때는 Logcat에서 `AUTOPOPUP_SDK`와 `AutoPopupRepository`로 검색합니다.

## 자주 묻는 질문

### iPhone 사용자에게도 보낼 수 있나요?

아니요. POPCON SDK는 Android만 지원합니다.

### SDK 키는 어디서 받나요?

별도 키가 없습니다. 관리자에 등록한 패키지명으로 앱을 구분합니다.

### 사용자가 아이콘을 닫으면 다시 안 오나요?

닫은 메시지만 사라지고 다음 메시지는 다시 표시됩니다. 받지 않으려면 앱 설정의 팝콘메시지 메뉴에서 끕니다.

## 문의하기

해결되지 않으면 [popcon@popcon.biz](mailto:popcon@popcon.biz)로 아래 정보를 함께 보내 주세요. 원인을 더 빨리 찾을 수 있습니다.

* SDK 버전
* 앱 패키지명
* 기기 모델과 Android 버전
* 문제가 생긴 시각
* Logcat 로그(`AUTOPOPUP_SDK`, `AutoPopupRepository`로 거른 내용)
