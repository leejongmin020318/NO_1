# 인턴 회식 장소 투표

이 저장소의 GitHub Pages로 공개 사이트를 배포할 수 있습니다. **여러 사람의 투표 결과를 공유하려면 Firebase 설정이 필수**입니다. 한 사람은 서로 다른 식당을 최대 3곳까지 선택할 수 있습니다.

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
        ".validate": "newData.hasChildren(['placeIds', 'updatedAt'])",
        "placeIds": {
          "$placeId": {
            ".validate": "$placeId.matches(/^[1-9][0-9]*$/) && newData.val() === true"
          }
        },
        "updatedAt": {
          ".validate": "newData.isNumber()"
        },
        "$other": { ".validate": false }
      }
    },
    "part2Votes": {
      ".read": "auth != null",
      "$uid": {
        ".write": "auth != null && auth.uid === $uid",
        ".validate": "newData.hasChildren(['placeIds', 'updatedAt'])",
        "placeIds": {
          "$placeId": {
            ".validate": "$placeId.matches(/^[1-9][0-9]*$/) && newData.val() === true"
          }
        },
        "updatedAt": {
          ".validate": "newData.isNumber()"
        },
        "$other": { ".validate": false }
      }
    }
  }
}
```

## 식당 후보를 9번, 10번 이후로 추가하는 방법

식당 목록은 코드의 `places` 배열에서 관리합니다. 새 후보를 받으면 해당 파트의 파일에 기존 마지막 번호 다음으로 고유한 `id`, 식당명 `name`, 네이버 플레이스 `url`, 위치 `area`, 소개 `detail` 등을 추가하면 카드와 투표 집계에 자동 반영됩니다.

- 3파트: `index.html`의 `places` 배열에 추가
- 2파트: `part2/index.html`의 `places` 배열에 추가
- 두 파트는 식당 목록을 별도로 관리하고, 투표 저장 경로도 각각 `votes`와 `part2Votes`로 분리됩니다.
- 위 Firebase 규칙은 9번 이후의 양의 정수 ID도 허용합니다. 규칙을 변경한 뒤 **Publish**를 눌러야 적용됩니다.
- 한 사람당 최대 3곳 선택 제한은 현재 페이지에서 적용됩니다. 이 규칙 예시는 Firebase 서버 측에서 최대 선택 수까지 강제하지는 않습니다.

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
