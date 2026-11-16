# POPCON SDK 소개

POPCON SDK는 Android 앱에서 팝콘메시지를 받아 화면에 표시하는 라이브러리입니다. 메시지는 POPCON 관리자([popcon.biz](https://popcon.biz))에서 보내고, SDK는 수신과 표시, 결과 보고를 맡습니다.

## 동작 방식

**1. 관리자에서 메시지 발송**

회원사가 popcon.biz에서 팝콘메시지를 보냅니다.

**2. 화면 위 아이콘 표시**

SDK가 FCM으로 메시지를 받아 화면 위에 아이콘을 띄웁니다. 사용자가 다른 앱을 쓰는 중에도 표시됩니다. 아이콘 오른쪽 위의 X를 누르면 그 메시지만 닫힙니다.

**3. 카드 표시와 앱 실행**

사용자가 아이콘을 누르면 화면 위에 카드 형태의 메시지가 펼쳐지고, 처음 누를 때 앱이 함께 실행됩니다. 광고가 포함된 메시지는 카드를 펼치기 전 광고 화면이나 카드 위 광고 배너가 함께 표시될 수 있습니다.

**4. 결과 보고**

SDK가 수신·표시·클릭 결과를 보고하고, 회원사는 관리자 메시지 이력에서 확인합니다.

## 기존 앱 푸시와 함께 쓰기

기존 앱 푸시는 그대로 두고 함께 쓸 수 있습니다. 앱의 FCM 수신 서비스에서 팝콘메시지만 SDK로 넘기면 됩니다. 자세한 내용은 [기존 앱 푸시와 함께 쓰기](integrate/existing-fcm.md)를 참고합니다.

## 지원 환경

* Android 5.0(API 21) 이상
* 최신 SDK 버전 1.1.1
* iOS는 지원하지 않습니다. iPhone 사용자는 발송 대상에서 빠집니다.

> [!NOTE]
> 기술 문의는 [popcon@popcon.biz](mailto:popcon@popcon.biz)로 보내 주세요.

## 목차

**시작하기**

* [POPCON SDK 소개](README.md)
* [1. 연동 준비](getting-started/quick-start.md)

**연동하기**

* [2. SDK 설치와 초기화](integrate/install-and-init.md)
* [3. 팝콘메시지 수신 설정](integrate/README.md)
  * [수신 동의 연결](integrate/consent.md)
  * [권한 요청](integrate/permissions.md)
  * [기존 앱 푸시와 함께 쓰기](integrate/existing-fcm.md)
* [4. 추가 설정](integrate/extra-settings.md)

**확인과 출시**

* [5. 연동 확인](before-release/verification.md)
* [6. 개인정보 처리방침과 Play 데이터 보안](before-release/privacy-and-play.md)

**참고**

* [7. 문제 해결](troubleshooting.md)
* [8. API 레퍼런스](reference/api.md)
* [9. 릴리스 노트](reference/release-notes.md)
  * [버전 올리기](reference/upgrade.md)
* [10. 예제 앱](reference/sample-app.md)
