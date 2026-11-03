# Architecture Map - github_paseu18

## 1. 개요
`github_paseu18` 프로젝트의 로컬 격리 브라우징 및 3대 플랫폼(GitHub, Hugging Face, GitLab) 자동화 아키텍처 맵입니다.

## 2. 브라우저 격리 구조
- **프로필 디렉터리**: `C:\github_paseu18\chrome_profile`
  - 프로젝트 폴더 내부에 독립된 Chromium 사용자 데이터 디렉터리(User Data Directory)를 유지하여 타 세션과 쿠키/세션 충돌을 원천 차단.
- **CDP 포트**: `9228`
  - 기존 9222/9225/9227 포트와 충돌 없이 독립적인 원격 디버깅 인터페이스 제공.
- **인터랙티브 디스플레이 세션**: Windows 대화형 세션 (`Session 1, WinSta0\Default`) Explorer COM 위임 기동.

## 3. 계정 정보 및 토큰 매핑
- **Google**: `paseu18@gmail.com`
- **GitHub**:
  - 사용자: `paseu18-pixel`
  - 토큰 유형: Personal Access Token (Classic)
  - 권한: 전체 51개 스코프 (All Scopes)
  - 만료일: 없음 (`No expiration`)
  - 환경변수 키: `GITHUB_TOKEN`, `GH_TOKEN`
- **Hugging Face**:
  - 사용자: `sdaera43` (`paseu18@gmail.com`)
  - 토큰 유형: Fine-grained Access Token
  - 권한: Full Access (모든 권한)
  - 만료일: 없음 (`No expiration`)
  - 환경변수 키: `HUGGINGFACE_TOKEN`, `HF_TOKEN`
- **GitLab**:
  - 사용자: `paseu18` (이름: 스파)
  - 토큰 유형: Legacy Personal Access Token
  - 권한: 전체 14개 스코프 (api, read_api, read_user, write_repository 등)
  - 만료일: `2027-11-03` (GitLab 정책상 최대 허용 1년)
  - 환경변수 키: `GITLAB_TOKEN`, `GL_TOKEN`

## 4. 토큰 관리 모듈
- `.env`: 기본 셸/CI 환경변수 정의
- `env.json`: 에이전트 및 툴 직결 JSON 매핑
- `tokens.json`: 계정 메타데이터 및 스코프 상세 저장소
- `load_env.js`: `getTokens()` 및 `loadEnv()` 지원 로더
