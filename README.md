# 인턴 회식 장소 투표

이 저장소의 GitHub Pages로 공개 사이트를 배포할 수 있습니다. **여러 사람의 투표 결과를 공유하려면 Firebase 설정이 필수**입니다.

## 1. Firebase 공용 투표 설정 (무료 Spark 요금제로 시작 가능)

1. https://console.firebase.google.com/ 에서 프로젝트를 생성합니다.
2. 프로젝트 설정(톱니바퀴) → 앱 추가 → Web 앱을 등록하고 SDK 설정의 `firebaseConfig` 값을 복사합니다.
3. Build → Realtime Database → 데이터베이스 만들기에서 데이터베이스를 생성합니다. 생성된 `databaseURL` 값을 확인합니다.
4. Authentication → Sign-in method에서 **Anonymous(익명)** 로그인을 활성화합니다.
5. Realtime Database → Rules에 아래 규칙을 저장합니다.

```json
{
  "rules": {
    "votes": {
      ".read": "auth != null",
      "$uid": {
        ".write": "auth != null && auth.uid === $uid",
        ".validate": "newData.hasChildren(['placeId', 'updatedAt'])",
        "placeId": { ".validate": "newData.isNumber() && newData.val() >= 1 && newData.val() <= 9" },
        "updatedAt": { ".validate": "newData.isNumber()" },
        "$other": { ".validate": false }
      }
    }
  }
}
```

## 2. 사이트에 Firebase 설정 넣기

`index.html`에서 아래 코드를 찾습니다.

```js
const firebaseConfig = {
  apiKey: "PASTE_FIREBASE_API_KEY",
  authDomain: "PASTE_PROJECT_ID.firebaseapp.com",
  databaseURL: "https://PASTE_PROJECT_ID-default-rtdb.firebaseio.com",
  projectId: "PASTE_PROJECT_ID",
  appId: "PASTE_FIREBASE_APP_ID"
};
```

Firebase Web 앱에서 받은 실제 `apiKey`, `authDomain`, `databaseURL`, `projectId`, `appId`로 교체하고 커밋합니다. Firebase 웹 설정 값은 클라이언트에 포함되지만, 보안은 위 데이터베이스 규칙이 담당합니다.

## 3. GitHub Pages 배포

1. 저장소의 **Settings → Pages**로 이동합니다.
2. **Build and deployment → Source**에서 **GitHub Actions**를 선택합니다.
3. 저장소의 **Actions** 탭에서 `Deploy to GitHub Pages` 워크플로 실행 결과가 성공했는지 확인합니다.
4. 사이트 주소는 보통 `https://leejongmin020318.github.io/NO_1/` 입니다.

## 현재 알려진 후보 정보

- 후보 1 — https://naver.me/I5wVGyDS
- 후보 2 — https://naver.me/574ixX13
- 후보 3 — https://naver.me/xLNtUEJE
- 후보 4 — https://naver.me/IDkmHksf
- 후보 5 — https://naver.me/xE6OJeoX
- 후보 6 (지인 추천) — https://naver.me/FA2xwIeo
- 후보 7 — https://naver.me/FgHIUYiY
- 후보 8 — https://naver.me/F6lvM6xs
- 후보 9 (지인 추천) — https://naver.me/Fuz5FEdT

네이버 단축 링크는 이 환경에서 실제 상호와 도보 시간을 자동 확인할 수 없었습니다. 이름과 위치는 네이버 지도에서 확인 후 정확히 반영해 주세요. 사진은 음식점 자체 사진이 아니라 둘러보기용 참고 이미지이며, 각 카드의 네이버 지도 버튼으로 실제 사진·메뉴를 확인할 수 있습니다.
