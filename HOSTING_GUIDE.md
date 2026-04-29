# WhiteVine 약관·개인정보처리방침 호스팅 가이드 (Cloudflare Pages)

이 문서는 13d-C-2 단계 — 페어가 작성한 정적 사이트 (`docs/legal/web/`) 를 Cloudflare Pages 에 배포하고 thelayerhq.com 도메인에 연결하는 단계별 가이드입니다.

목표 URL:
- **이용약관**: `https://thelayerhq.com/whitevine/terms`
- **개인정보처리방침**: `https://thelayerhq.com/whitevine/privacy`
- **인덱스**: `https://thelayerhq.com/whitevine/`
- **whitevine.app/terms** → `https://thelayerhq.com/whitevine/terms` 로 redirect (Cloudflare Bulk Redirect)

---

## 사전 조건

1. Cloudflare 계정 (없으면 https://dash.cloudflare.com/sign-up 에서 무료 가입)
2. thelayerhq.com 도메인 보유
3. whitevine.app 도메인 보유
4. GitHub 계정 (Cloudflare Pages 와 연결 — 약관 변경 시 자동 배포)

---

## 1단계: 두 도메인을 Cloudflare 에 등록

이미 두 도메인이 Cloudflare 의 nameserver 를 사용 중이라면 이 단계 skip.

1. Cloudflare 대시보드 → "Add a site" → `thelayerhq.com` 입력
2. Free plan 선택
3. Cloudflare 가 안내한 두 nameserver 를 도메인 등록처 (예: 가비아·후이즈) 의 DNS 설정에서 변경
4. 24~48시간 이내에 활성화 (보통 1시간 이내)
5. `whitevine.app` 도 동일 절차로 추가

확인 방법: Cloudflare 대시보드의 도메인 옆에 초록 체크 + "Active" 표시.

---

## 2단계: GitHub repo 생성 및 사이트 파일 업로드

방법 A — 새 repo 만들기 (권장):

1. GitHub 에서 새 repo 생성: 이름 예시 `thelayerhq-site` (private 도 OK)
2. 로컬에서 다음 폴더 구조로 파일 배치:

```
thelayerhq-site/
├── whitevine/
│   ├── index.html
│   ├── terms.html
│   └── privacy.html
├── _redirects
└── robots.txt
```

3. 페어가 작성한 파일 (`docs/legal/web/` 안의 모든 파일) 을 위 구조 그대로 복사.
4. git push origin main

방법 B — 기존 WhiteVine repo 의 별도 브랜치 사용:

WhiteVine 앱 코드와 호스팅 사이트를 같은 repo 의 별도 브랜치로 관리. 다만 분리가 깔끔하지 않으니 방법 A 권장.

---

## 3단계: Cloudflare Pages 프로젝트 생성

1. Cloudflare 대시보드 → 좌측 메뉴 → Workers & Pages → Create → Pages → Connect to Git
2. GitHub 계정 연결 → 위에서 만든 repo 선택
3. 프로젝트 이름: `thelayerhq` (또는 원하는 이름)
4. Production branch: `main`
5. Build settings: **빈 칸 그대로 둠** (정적 HTML 파일이라 빌드 명령 불필요)
   - Framework preset: None
   - Build command: (비움)
   - Build output directory: `/` (루트)
6. Save and Deploy

배포 완료 후 임시 URL (예: `thelayerhq.pages.dev`) 에서 사이트 확인:
- `https://thelayerhq.pages.dev/whitevine/terms` 정상 로드 확인
- `https://thelayerhq.pages.dev/whitevine/privacy` 정상 로드 확인
- `https://thelayerhq.pages.dev/whitevine/` 인덱스 정상 로드 확인

---

## 4단계: thelayerhq.com Custom domain 연결

1. Cloudflare Pages 프로젝트 페이지 → Custom domains 탭 → Set up a custom domain
2. `thelayerhq.com` 입력 → Activate domain
3. Cloudflare 가 자동으로 DNS 레코드 추가 (CNAME `thelayerhq.com` → `thelayerhq.pages.dev`)
4. 1~2분 후 `https://thelayerhq.com/whitevine/terms` 정상 로드 확인

(선택) `www.thelayerhq.com` 도 같이 연결하려면 동일 절차로 한 번 더.

---

## 5단계: whitevine.app → thelayerhq.com Redirect 설정

whitevine.app 의 약관·개인정보처리방침 경로를 thelayerhq.com 으로 redirect:

1. Cloudflare 대시보드 → whitevine.app 선택 → 좌측 메뉴 → Rules → Redirect Rules
2. Create rule 클릭
3. 규칙 1 — 이용약관 redirect:
   - Rule name: `whitevine.app terms redirect`
   - When incoming requests match: Custom filter expression
     - Field: URI Path
     - Operator: equals
     - Value: `/terms`
   - Then: Static
     - Type: Static
     - URL: `https://thelayerhq.com/whitevine/terms`
     - Status code: 301
   - Save
4. 규칙 2 — 개인정보처리방침 redirect:
   - Rule name: `whitevine.app privacy redirect`
   - 위와 동일한 절차로 `/privacy` → `https://thelayerhq.com/whitevine/privacy`
   - Save

확인:
- `https://whitevine.app/terms` 접속 → `https://thelayerhq.com/whitevine/terms` 로 자동 이동
- `https://whitevine.app/privacy` 접속 → `https://thelayerhq.com/whitevine/privacy` 로 자동 이동

(선택) whitevine.app 의 루트 (`/`) 는 추후 앱 마케팅 사이트로 별도 작업.

---

## 6단계: 동작 검증 체크리스트

배포 완료 후 다음 URL 모두 정상 동작하는지 확인:

| URL | 기대 동작 |
|---|---|
| https://thelayerhq.com/whitevine/ | 인덱스 페이지 |
| https://thelayerhq.com/whitevine/terms | 이용약관 본문 |
| https://thelayerhq.com/whitevine/privacy | 개인정보처리방침 본문 |
| https://whitevine.app/terms | thelayerhq.com 으로 redirect |
| https://whitevine.app/privacy | thelayerhq.com 으로 redirect |

iOS 시뮬레이터에서 회원가입 화면의 "보기" 버튼도 13d-C-2 (URL 교체) 후 정상 로드되는지 확인.

---

## 약관 변경 시 업데이트 흐름

1. 페어가 마스터 markdown (`docs/legal/terms_ko.md`, `privacy_policy_ko.md`) 수정
2. 페어가 HTML 재변환 (`docs/legal/web/whitevine/*.html` 자동 갱신)
3. 영빈님이 GitHub repo (`thelayerhq-site`) 에 변경된 HTML 파일 push
4. Cloudflare Pages 가 자동 감지 → 자동 배포 (1~2분)

---

## 트러블슈팅

**404 에러** — Build output directory 가 `/` 인지 확인. Cloudflare Pages 의 Settings → Build & deployments 에서.

**도메인 연결 실패** — DNS 전파 시간 (최대 24시간). 보통 1~2분 내 활성화. `dig thelayerhq.com` 으로 확인 가능.

**redirect 동작 안 함** — Redirect Rules 의 우선순위 (Order) 확인. Page Rules 와 충돌 가능. Page Rules 우선이라 Redirect Rules 가 무시될 수 있음.

**HTTPS 인증서 에러** — Cloudflare Pages 가 자동으로 SSL 발급. 1~2분 대기. SSL/TLS → Edge Certificates 탭에서 상태 확인.

---

## 다음 단계

호스팅 셋업 완료 후 13d-C-2 진행:
- 페어가 코덱스 박스 메시지 작성 (앱 내 placeholder URL 을 실제 URL 로 교체)
- 코덱스가 SignUpView·Constants 의 placeholder URL 을 실제 thelayerhq.com URL 로 교체
- 시뮬레이터에서 "보기" 버튼 → SafariView 가 실제 약관 페이지 로드 확인

13e (Cloudflare Email Routing — report@thelayerhq.com 셋업) 도 같은 Cloudflare 계정에서 이어서 진행하면 깔끔.
