# NEAR · 가까운 합

1:1 심리전 카드 게임. 오프라인(한 기기 번갈아 쥐기) / 온라인(Firebase 실시간 대전).

```
index.html            게임 전체
firebase-config.js    Firebase 웹 앱 설정 (직접 채워야 함)
database.rules.json   Realtime Database 보안 규칙
audio/bgm.mp3         배경음악 (모노 22kHz 32kbps, 끊김 없는 루프)
audio/gunshot.mp3     총소리
```

## 실행

- GitHub Pages: 저장소 Settings → Pages → Branch `main` / `/ (root)`.
- 로컬 테스트: 이 폴더에서 `python3 -m http.server` 후 `http://localhost:8000`.
  `index.html`을 더블클릭해서 열면 게임은 되지만, 음악 효과(리버브·디스토션·쪼개기)는 꺼지고 그냥 재생만 됩니다.

## 온라인 대전 설정 (한 번만)

1. [Firebase 콘솔](https://console.firebase.google.com)에서 프로젝트 만들기.
2. **Authentication → 로그인 방법 → 익명** 사용 설정.
3. **Realtime Database → 데이터베이스 만들기** (위치 아무 곳, 잠금 모드로 시작).
4. Realtime Database → **규칙** 탭에 `database.rules.json` 내용을 붙여 넣고 게시.
5. 프로젝트 설정 → 내 앱 → 웹 앱 추가(`</>`) → 나오는 `firebaseConfig` 값을 `firebase-config.js`에 붙여 넣기.
   `databaseURL`이 비어 있으면 Realtime Database 페이지 상단 주소(`https://…firebasedatabase.app`)를 넣으세요.
6. Authentication → 설정 → **승인된 도메인**에 `아이디.github.io` 추가.

설정 값은 공개 저장소에 올라가도 괜찮습니다. 데이터 보호는 4번의 규칙이 담당합니다.

## 온라인 대전 방식

- 한 명이 **방 만들기** → 4자리 코드(또는 링크)를 상대에게 전달 → 상대가 **참가**.
- 고른 카드와 0–5는 확정 순간 `SHA-256(카드, 0–5, 무작위 소금)`으로 잠긴 뒤 서버에 올라갑니다.
  두 사람이 모두 잠근 뒤에만 목표(합)와 0–5가 공개되고, 고른 카드는 게임이 끝날 때 공개됩니다.
- 게임이 끝나면 서로의 기기가 상대의 모든 판을 처음 잠근 값과 대조합니다. 하나라도 다르면 "검증 실패"로 몰수패 처리됩니다.
- 새로고침하거나 연결이 끊겨도 같은 기기·같은 브라우저로 같은 코드에 다시 들어오면 이어집니다.
- 방 데이터는 자동으로 지워지지 않습니다. 쌓이면 Realtime Database 콘솔에서 `rooms`를 지우면 됩니다.
