# 미니홈피 프로젝트 — 배포/설정 안내

이 저장소에는 GitHub Pages로 배포되는 정적 프론트엔드와 Firebase(Firestore, Storage, Auth)를 이용한 백엔드 통합 예제가 포함되어 있습니다.
아래 파일들이 생성되어 있습니다:

- `index.html` — 사용자용 메인 페이지 (로그인/프로필/사진/다이어리/친구/알림)
- `admin_page.html` — 관리자 페이지 (BGM 업로드, 신고 관리, 관리자 지정 등)
- `firestore_rules.rules` — Firestore 보안 규칙 예시
- `storage_rules.rules` — Storage 보안 규칙 예시

## 사용 전 필수 설정

1. Firebase 콘솔에서 프로젝트 생성 및 구성
   - Authentication > Sign-in method에서 Google 로그인 활성화
   - Firestore Database 생성 (모드: 테스트 또는 필요시 보안 규칙 배포 후 프로덕션)
   - Storage 활성화

2. Firebase SDK 설정 확인
   - `index.html` / `admin_page.html`에 포함된 `firebaseConfig`를 본인 프로젝트 값으로 교체하세요.
   - 현재 샘플값이 포함되어 있으니 **운영 전 반드시 확인**하세요.

3. Firestore 보안 규칙 배포
   - 콘솔 또는 Firebase CLI에서 `firestore_rules.rules` 파일의 내용을 Rules에 복사/붙여넣기 후 배포하세요.
     (로컬에서는 `firebase deploy --only firestore:rules` 를 사용)

4. Storage 규칙 배포
   - 콘솔 또는 Firebase CLI에서 `storage_rules.rules` 파일 내용을 복사하여 배포하세요.

5. 관리자 권한 설정 (권장)
   - 운영 환경에서는 Firebase Admin SDK를 사용해 사용자에게 custom claim `admin:true`를 부여하세요.
   - 단축 방법(비권장): 콘솔 > Firestore에 `admins` 컬렉션을 만들고 문서 ID로 admin UID를 추가하면 클라이언트 쪽에서 admins 컬렉션 존재 여부로 admin 판별합니다.
   - Admin SDK를 통한 예시 (Node.js):
```js
const admin = require('firebase-admin');
admin.auth().setCustomUserClaims(uid, { admin: true });
```

6. Cloud Function(선택)
   - custom claim을 부여하거나, 신고 처리 자동화, 썸네일 생성 등의 작업은 Cloud Functions가 편리합니다.

## 보안 주의사항 (중요)
- 클라이언트에서 admin 권한을 부여하는 코드를 작성하면 안 됩니다. 반드시 서버(Admin SDK or Cloud Functions)를 통해 custom claim을 설정하세요.
- Storage에서 민감한 파일(비공개 사진 등)은 public=true 같은 메타데이터로 제어합니다. 해당 메타데이터를 조작하지 못하도록 규칙을 엄격히 다듬으세요.
- Firestore 규칙은 프로젝트의 서비스 구조에 맞춰 세밀하게 조정해야 합니다. 제공된 규칙은 예시입니다.

## 배포 (GitHub Pages)
1. 레포지토리 루트에 `index.html`을 두고 Commit & Push
2. GitHub > Settings > Pages에서 Branch를 선택해 배포
3. `admin_page.html`은 퍼블릭 경로로 남으며, 관리자 계정만 UI가 활성화됩니다.

## 기능 확장 제안
- 이미지 썸네일(Cloud Function + ImageMagick)
- content moderation (비속어/음란물 필터) — 업로드 후 Cloud Function으로 검사
- 알림 푸시(FCM) — 알림을 실시간으로 푸시하려면 FCM 연동
- 팔로우/피드 알고리즘 개선, 검색엔진(Algolia 등) 연동

---
위 파일들을 테스트해보시고, 바로 적용·수정해드릴 부분(예: custom claim용 Cloud Function 코드, 이미지 리사이즈, 디자인 변경 등)을 알려주시면 추가로 만들어 드리겠습니다.
