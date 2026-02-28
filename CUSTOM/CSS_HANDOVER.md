# CSS 인수인계서 (CSS Handover Document)

> **SillyTavern CSS 커스터마이징 요청 및 인수인계를 위한 가이드 문서**
>
> 최종 업데이트: 2026-02-27

---

## 목차

1. [프로젝트 CSS 아키텍처 개요](#1-프로젝트-css-아키텍처-개요)
2. [전체 CSS 파일 목록](#2-전체-css-파일-목록)
3. [핵심 CSS 변수 (Custom Properties)](#3-핵심-css-변수-custom-properties)
4. [파일별 상세 설명](#4-파일별-상세-설명)
5. [확장 기능 CSS](#5-확장-기능-css)
6. [서드파티 / 벤더 CSS](#6-서드파티--벤더-css)
7. [CSS 커스터마이징 가이드](#7-css-커스터마이징-가이드)
8. [자주 사용되는 커스터마이징 패턴](#8-자주-사용되는-커스터마이징-패턴)
9. [주의사항 및 알려진 이슈](#9-주의사항-및-알려진-이슈)
10. [20대 여성 감성 테마 — `CUSTOM/feminine-design.css`](#10-20대-여성-감성-테마--customfeminine-designcss)

---

## 1. 프로젝트 CSS 아키텍처 개요

### 파일 구조

```
public/
├── style.css                          ← 메인 스타일시트 (6,301줄, 엔트리 포인트)
├── css/
│   ├── animations.css                 ← 키프레임 애니메이션 정의
│   ├── popup.css                      ← 다이얼로그/팝업 시스템
│   ├── promptmanager.css              ← 프롬프트 매니저 UI
│   ├── loader.css                     ← 프리로더/스피너
│   ├── character-group-overlay.css    ← 캐릭터 그룹 오버레이
│   ├── file-form.css                  ← 파일 첨부 & 오디오 플레이어
│   ├── logprobs.css                   ← 토큰 로그 확률 뷰어
│   ├── accounts.css                   ← 사용자 계정 스타일
│   ├── tags.css                       ← 태그 시스템
│   ├── scrollable-button.css          ← 스크롤 가능 버튼 컨테이너
│   ├── welcome.css                    ← 환영 패널 & 최근 채팅
│   ├── data-maid.css                  ← 데이터 관리 다이얼로그
│   ├── secrets.css                    ← API 키 관리 UI
│   ├── backgrounds.css                ← 배경 이미지 관리
│   ├── chat-backups.css               ← 채팅 백업 목록
│   ├── login.css                      ← 로그인 페이지
│   ├── macros.css                     ← 매크로 브라우저 & 자동완성
│   ├── mobile-styles.css              ← 모바일/반응형 미디어 쿼리
│   ├── popup-safari-fix.css           ← Safari 팝업 수정
│   ├── rm-groups.css                  ← 그룹 채팅 관리
│   ├── select2-overrides.css          ← Select2 드롭다운 테마 오버라이드
│   ├── st-tailwind.css                ← Tailwind 스타일 유틸리티 클래스
│   ├── toggle-dependent.css           ← 토글/조건부 가시성 스타일
│   ├── world-info.css                 ← 월드 인포/로어북 에디터
│   ├── extensions-panel.css           ← 확장 기능 설정 패널
│   ├── group-avatars.css              ← 그룹 아바타 콜라주
│   ├── *.min.css                      ← 서드파티 라이브러리 (수정 금지)
│   └── ...
├── scripts/extensions/
│   ├── vectors/style.css              ← 벡터 검색 표시
│   ├── connection-manager/style.css   ← 연결 관리자
│   ├── memory/style.css               ← 요약/메모리 확장
│   ├── attachments/style.css          ← 첨부 파일/데이터 뱅크
│   ├── expressions/style.css          ← 캐릭터 표정 이미지
│   ├── caption/style.css              ← 이미지 캡션
│   ├── token-counter/style.css        ← 토큰 카운터
│   ├── translate/style.css            ← 번역
│   ├── quick-reply/style.css          ← 퀵 리플라이 (1,087줄)
│   ├── gallery/style.css              ← 이미지 갤러리
│   ├── stable-diffusion/style.css     ← Stable Diffusion
│   ├── regex/style.css                ← 정규식 확장
│   ├── regex/debugger.css             ← 정규식 디버거
│   ├── assets/style.css               ← 에셋 다운로더
│   ├── tts/style.css                  ← TTS (음성 합성)
│   ├── tts/css/openai-tts.css         ← OpenAI TTS 설정
│   └── tts/css/minimax-tts.css        ← MiniMax TTS 설정
├── lib/dialog-polyfill.css            ← 다이얼로그 폴리필 (수정 금지)
├── webfonts/
│   ├── NotoSans/stylesheet.css        ← 웹폰트 정의 (수정 금지)
│   └── NotoSansMono/stylesheet.css    ← 모노스페이스 폰트 정의 (수정 금지)
└── default/public/css/user.css        ← 사용자 커스텀 CSS (비어있음)
```

### 로드 순서

`public/style.css`가 메인 엔트리 포인트이며, 상단에서 다음 파일들을 `@import`합니다:

```css
@import url(css/animations.css);
@import url(css/popup.css);           /* → dialog-polyfill.css, popup-safari-fix.css 를 추가 import */
@import url(css/promptmanager.css);
@import url(css/loader.css);
@import url(css/character-group-overlay.css);
@import url(css/file-form.css);
@import url(css/logprobs.css);
@import url(css/accounts.css);
@import url(css/tags.css);
@import url(css/scrollable-button.css);
@import url(css/welcome.css);
@import url(css/data-maid.css);
@import url(css/secrets.css);
@import url(css/backgrounds.css);
@import url(css/chat-backups.css);
```

나머지 CSS 파일들 (`mobile-styles.css`, `st-tailwind.css`, `select2-overrides.css`, `toggle-dependent.css` 등)은 HTML에서 직접 `<link>` 태그로 로드됩니다.

확장 기능 CSS (`scripts/extensions/*/style.css`)는 각 확장이 활성화될 때 동적으로 로드됩니다.

---

## 2. 전체 CSS 파일 목록

### 커스텀 CSS (수정 가능) — 48개 파일

| 파일 경로 | 줄 수 | 용도 |
|-----------|-------|------|
| `public/style.css` | 6,301 | 메인 스타일시트 — 전체 레이아웃, 채팅, 설정, 아바타 |
| `public/css/animations.css` | 154 | 키프레임 애니메이션 (fade, pop, flash, pulse, slide) |
| `public/css/backgrounds.css` | 378 | 배경 이미지 관리 UI (썸네일, 탭, 스크롤 버튼) |
| `public/css/chat-backups.css` | 54 | 채팅 백업 목록 패널 |
| `public/css/login.css` | 50 | 로그인 화면 (사용자 선택, 폼 입력) |
| `public/css/welcome.css` | 238 | 환영 패널 (최근 채팅, 바로가기, 버전 표시) |
| `public/css/popup.css` | 217 | 다이얼로그/팝업 시스템 (열기/닫기 애니메이션) |
| `public/css/macros.css` | 603 | 매크로 브라우저 (검색, 목록, 상세 패널, 자동완성) |
| `public/css/popup-safari-fix.css` | 19 | Safari 전용 팝업 높이 수정 |
| `public/css/logprobs.css` | 148 | 토큰 로그 확률 뷰어 패널 |
| `public/css/rm-groups.css` | 245 | 그룹 채팅 생성/관리, 멤버 카드 |
| `public/css/group-avatars.css` | 91 | 그룹 아바타 콜라주 (2~4명 레이아웃) |
| `public/css/promptmanager.css` | 377 | 프롬프트 매니저 (드래그 정렬, 에디터, 토큰 수) |
| `public/css/scrollable-button.css` | 21 | 스크롤 가능 버튼 컨테이너 |
| `public/css/mobile-styles.css` | 580 | 반응형 미디어 쿼리 (≤1000px, ≤450px, iOS) |
| `public/css/world-info.css` | 328 | 월드 인포/로어북 에디터 |
| `public/css/data-maid.css` | 149 | 데이터 관리 다이얼로그 (파일 뷰어, 카테고리) |
| `public/css/character-group-overlay.css` | 106 | 캐릭터 그룹 선택 오버레이, 컨텍스트 메뉴 |
| `public/css/accounts.css` | 5 | 사용자 계정 선택 항목 |
| `public/css/secrets.css` | 102 | API 키 관리 인터페이스 |
| `public/css/select2-overrides.css` | 242 | Select2 드롭다운 테마 커스터마이징 |
| `public/css/st-tailwind.css` | 613 | Tailwind 스타일 유틸리티 클래스 |
| `public/css/toggle-dependent.css` | 570 | 토글 기반 조건부 표시 스타일 |
| `public/css/loader.css` | 31 | 프리로더/스피너 오버레이 |
| `public/css/extensions-panel.css` | 163 | 확장 기능 설정 패널 |
| `public/css/tags.css` | 293 | 태그 시스템 (필터, 폴더, 인라인 태그) |
| `public/css/file-form.css` | 161 | 파일 첨부 표시 & 오디오 플레이어 |
| `default/public/css/user.css` | 1 | 사용자 커스텀 CSS (기본값: 비어있음) |

#### 확장 기능 CSS — 16개 파일

| 파일 경로 | 줄 수 | 용도 |
|-----------|-------|------|
| `scripts/extensions/quick-reply/style.css` | 1,087 | 퀵 리플라이 바, 에디터, 설정 |
| `scripts/extensions/regex/debugger.css` | 263 | 정규식 디버거 UI |
| `scripts/extensions/expressions/style.css` | 220 | 캐릭터 표정 이미지 표시/관리 |
| `scripts/extensions/assets/style.css` | 200 | 에셋 다운로더/브라우저 |
| `scripts/extensions/regex/style.css` | 161 | 정규식 스크립트 관리 |
| `scripts/extensions/tts/style.css` | 130 | TTS 음성 합성 설정 |
| `scripts/extensions/stable-diffusion/style.css` | 93 | Stable Diffusion 이미지 생성 설정 |
| `scripts/extensions/attachments/style.css` | 63 | 첨부 파일/데이터 뱅크 |
| `scripts/extensions/gallery/style.css` | 55 | 이미지/비디오 갤러리 |
| `scripts/extensions/memory/style.css` | 47 | 요약/메모리 확장 |
| `scripts/extensions/tts/css/minimax-tts.css` | 38 | MiniMax TTS 커스텀 보이스 |
| `scripts/extensions/connection-manager/style.css` | 11 | 연결 프로필 관리 |
| `scripts/extensions/token-counter/style.css` | 11 | 토큰 카운터 |
| `scripts/extensions/tts/css/openai-tts.css` | 11 | OpenAI TTS 설정 |
| `scripts/extensions/translate/style.css` | 6 | 번역 설정 버튼 |
| `scripts/extensions/vectors/style.css` | 4 | 벡터화 메시지 표시 |
| `scripts/extensions/caption/style.css` | 3 | 캡션 폼 숨김 |

### 서드파티 CSS (수정 금지) — 9개 파일

| 파일 | 라이브러리 |
|------|-----------|
| `css/fontawesome.min.css` | Font Awesome 6 아이콘 |
| `css/solid.min.css` | Font Awesome Solid 변형 |
| `css/brands.min.css` | Font Awesome Brands 변형 |
| `css/jquery-ui.min.css` | jQuery UI 위젯 |
| `css/select2.min.css` | Select2 드롭다운 |
| `css/toastr.min.css` | Toastr 알림 |
| `css/cropper.min.css` | Cropper.js 이미지 크롭 |
| `css/bright.min.css` | Highlight.js 코드 테마 |
| `gallery/nanogallery2.woff.min.css` | nanogallery2 갤러리 |

---

## 3. 핵심 CSS 변수 (Custom Properties)

모든 테마 커스터마이징은 CSS 변수를 통해 이루어집니다. 변수는 `public/style.css`의 `:root` 블록(19~132줄)에 정의되어 있습니다.

### 색상 변수

| 변수명 | 기본값 | 용도 |
|--------|--------|------|
| `--SmartThemeBodyColor` | `rgb(220,220,210)` | 기본 텍스트 색상 |
| `--SmartThemeEmColor` | `rgb(145,145,145)` | 강조/보조 텍스트 |
| `--SmartThemeUnderlineColor` | `rgb(188,231,207)` | 밑줄 색상 |
| `--SmartThemeQuoteColor` | `rgb(225,138,36)` | 인용/강조 색상 (오렌지) |
| `--SmartThemeBlurTintColor` | `rgba(23,23,23,1)` | 배경 블러 틴트 |
| `--SmartThemeChatTintColor` | `rgba(23,23,23,1)` | 채팅 영역 틴트 |
| `--SmartThemeUserMesBlurTintColor` | `rgba(0,0,0,0.3)` | 사용자 메시지 배경 |
| `--SmartThemeBotMesBlurTintColor` | `rgba(60,60,60,0.3)` | 봇 메시지 배경 |
| `--SmartThemeShadowColor` | `rgba(0,0,0,0.5)` | 텍스트 그림자 |
| `--SmartThemeBorderColor` | `rgba(0,0,0,0.5)` | 테두리 색상 |

### 투명도 색상 프리셋

| 변수 | 값 | 비고 |
|------|----|------|
| `--black30a` ~ `--black100` | 검정 30~100% 불투명도 | 배경, 오버레이 |
| `--white20a` ~ `--white100` | 흰색 20~100% 불투명도 | 호버, 강조 |
| `--grey10` ~ `--grey75` | 회색 계열 | 보조 UI 요소 |
| `--fullred`, `--crimson70a` | 빨강 계열 | 삭제, 경고 |
| `--golden` | `rgb(248,211,0)` | 즐겨찾기, 활성 상태 |
| `--active` | `rgb(88,182,0)` | 활성화 표시 |

### 레이아웃 & 크기 변수

| 변수명 | 기본값 | 용도 |
|--------|--------|------|
| `--sheldWidth` | `50vw` | 채팅 영역 너비 |
| `--fontScale` | `1` | 글꼴 스케일 팩터 |
| `--mainFontSize` | `calc(var(--fontScale) * 15px)` | 기본 글꼴 크기 |
| `--mainFontFamily` | `"Noto Sans", sans-serif` | 기본 글꼴 |
| `--monoFontFamily` | `'Noto Sans Mono', 'Courier New', ...` | 코드 글꼴 |
| `--blurStrength` | `10` | 블러 강도 (px 단위 계산) |
| `--shadowWidth` | `2` | 텍스트 그림자 너비 |
| `--avatar-base-height/width` | `50px` | 아바타 기본 크기 |
| `--animation-duration` | `125ms` | 기본 애니메이션 시간 |
| `--animation-duration-2x` | `250ms` | 2배 애니메이션 시간 |
| `--animation-duration-3x` | `375ms` | 3배 애니메이션 시간 |

---

## 4. 파일별 상세 설명

### `public/style.css` — 메인 스타일시트 (6,301줄)

가장 큰 파일로, 앱의 핵심 레이아웃과 UI 컴포넌트를 정의합니다.

**주요 섹션:**

| 줄 범위 (대략) | 내용 |
|----------------|------|
| 1~18 | CSS 임포트 |
| 19~132 | `:root` CSS 변수 정의 |
| 134~200 | 글로벌 리셋, body, 스크롤바 |
| 200~290 | 공통 UI 요소 (버튼, 입력, 체크박스) |
| 290~320 | 키보드/포커스 네비게이션 |
| 320~470 | 드래그/리사이즈, 인라인 드로어 |
| 470~750 | 메시지 스타일 (채팅, 시스템 메시지, 추론 블록) |
| 750~900 | 토퍼 마진, 사이드바, sheld(채팅 컨테이너) |
| 900~1220 | Send form, 메시지 편집, 텍스트 포맷팅 |
| 1222~1340 | 스와이프 스타일 |
| 1340~2720 | 메시지 버튼, 아바타, 캐릭터 카드 |
| 2723~3010 | 오른쪽 패널 상단 탭 |
| 3011~3130 | 드롭다운 셀렉트 색상 |
| 3130~3360 | API 연결 설정 |
| 3360~3650 | 배경 메뉴, 키 바인딩 |
| 3645~3975 | 포커스 스타일, 팝업 레이아웃 |
| 3975~4200 | Toastr 알림 오버라이드 |
| 4200~5040 | 채팅 기록, 설정 패널, 슬라이더 |
| 5043~5360 | 네비게이션 패널, 메시지 이미지/비디오 |
| 5357~5600 | 상단 설정 바 |
| 5600~5900 | 확대 아바타, draggable, 페이지네이션 |
| 5900~6301 | 컬러 팔레트, 유틸리티, 기타 |

### `public/css/animations.css` — 애니메이션 (154줄)

모든 `@keyframes` 정의가 모여있습니다.

| 애니메이션 | 용도 |
|-----------|------|
| `fade-in` / `fade-out` | 투명도 전환 |
| `pop-in` / `pop-out` | 수직 스케일 + 투명도 (팝업) |
| `flash` | 깜빡이는 하이라이트 |
| `pulse` | 미세한 크기/밝기 펄스 |
| `ellipsis` | 로딩 말줄임표 (...) |
| `infinite-spinning` | 무한 회전 |
| `script_progress_pulse` | STscript 진행 표시 |
| `hide-scroll` | 스크롤 지연 |
| `slide` | 수평 슬라이드 |

### `public/css/toggle-dependent.css` — 조건부 스타일 (570줄)

`body` 클래스에 따라 UI를 동적으로 변경합니다.

| body 클래스 | 효과 |
|-------------|------|
| `.tts` | TTS 나레이션 버튼 표시 |
| `.sd` | SD 생성 버튼 표시 |
| `.translate` | 번역 버튼 표시 |
| `.no-hotswap` | 핫스왑 버튼 숨김 |
| `.no-timer` | 타이머 숨김 |
| `.no-timestamps` | 타임스탬프 숨김 |
| `.hideChatAvatars` | 채팅 아바타 숨김 |
| `.square-avatars` | 사각형 아바타 |
| `.rounded-avatars` | 둥근 아바타 |
| `.charListGrid` | 그리드 모드 캐릭터 목록 |
| `.big-avatars` | 큰 아바타 모드 |
| `.bubblechat` | 버블 채팅 스타일 |
| `.documentstyle` | 문서 스타일 |
| `.no-blur` | 블러 제거 |
| `.waifuMode` | 와이후 모드 (이미지 + 작은 채팅) |
| `.movingUI` | 드래그 가능 UI 모드 |
| `.noShadows` | 텍스트 그림자 제거 |

### `public/css/st-tailwind.css` — 유틸리티 클래스 (613줄)

Tailwind CSS와 유사한 단일 목적 유틸리티 클래스입니다. HTML에서 `class="flex gap10px alignItemsCenter"` 형태로 사용됩니다.

**주요 카테고리:**
- **마진**: `.m-t-0` ~ `.m-t-5`, `.m-b-1` ~ `.m-b-5`, `.marginBot10`, `.marginTop5` 등
- **패딩**: `.padding0`, `.padding5`, `.padding10`, `.paddingTopBot5` 등
- **Flex**: `.flex`, `.flex-container`, `.flexFlowColumn`, `.flexWrap`, `.flexGrow` 등
- **Gap**: `.gap0`, `.gap5px`, `.gap10px`, `.flexGap2` ~ `.flexGap10`
- **너비**: `.wide100p`, `.wide50p`, `.width100px`, `.widthFitContent` 등
- **정렬**: `.alignItemsCenter`, `.justifyCenter`, `.spaceBetween` 등
- **타이포그래피**: `.fontsize120p`, `.fontsize80p`, `.monospace` 등
- **상태**: `.text_warning`, `.text_danger`, `.success`, `.failure`
- **디버그**: `.debug-red`, `.debug-green` 등 (개발용)

---

## 5. 확장 기능 CSS

각 확장 기능은 자체 `style.css`를 갖고 있으며, 확장이 활성화될 때 동적으로 로드됩니다.

### 주요 확장 CSS 요약

| 확장 | 파일 | 커스터마이징 포인트 |
|------|------|---------------------|
| **Quick Reply** | `quick-reply/style.css` (1,087줄) | QR 바, 버튼 크기, 팝아웃 레이아웃 |
| **Expressions** | `expressions/style.css` (220줄) | 캐릭터 표정 이미지 크기/위치, 비주얼 노벨 모드 |
| **Regex** | `regex/style.css` + `debugger.css` (424줄) | 스크립트 카드, 디버거 출력 |
| **Assets** | `assets/style.css` (200줄) | 에셋 카드 레이아웃, 다운로드 버튼 |
| **TTS** | `tts/style.css` (130줄) | 음성 미리듣기, TTS 버튼, AllTalk 설정 |
| **Stable Diffusion** | `stable-diffusion/style.css` (93줄) | SD 설정, ComfyUI 에디터 |
| **Gallery** | `gallery/style.css` (55줄) | 갤러리 정렬/폴더 컨트롤 |
| **Attachments** | `attachments/style.css` (63줄) | 첨부 목록, 대량 편집 모드 |
| **Memory** | `memory/style.css` (47줄) | 요약 텍스트, 잠금/건너뛰기 |
| **Vectors** | `vectors/style.css` (4줄) | 벡터화 메시지 녹색 점 표시 |

---

## 6. 서드파티 / 벤더 CSS

> ⚠️ **아래 파일들은 수정하지 마세요.** 업데이트 시 덮어씌워집니다.

| 파일 | 역할 |
|------|------|
| `fontawesome.min.css`, `solid.min.css`, `brands.min.css` | 아이콘 폰트 |
| `jquery-ui.min.css` | jQuery UI 위젯 (탭, 정렬 등) |
| `select2.min.css` | Select2 기본 스타일 (`select2-overrides.css`로 커스터마이징) |
| `toastr.min.css` | 토스트 알림 기본 스타일 |
| `cropper.min.css` | 이미지 크롭 도구 |
| `bright.min.css` | 코드 하이라이트 테마 |
| `nanogallery2.woff.min.css` | 갤러리 아이콘 폰트 |
| `dialog-polyfill.css` | HTML `<dialog>` 폴리필 |
| `NotoSans/stylesheet.css`, `NotoSansMono/stylesheet.css` | 웹폰트 `@font-face` 정의 |

---

## 7. CSS 커스터마이징 가이드

### 방법 1: 사용자 CSS (user.css) — 권장

**파일**: `default/public/css/user.css`

이 파일은 사용자 커스텀 스타일을 위한 전용 파일입니다. SillyTavern 업데이트 시에도 유지됩니다.

```css
/* 예시: 채팅 폰트 크기 변경 */
:root {
    --mainFontSize: 16px;
}

/* 예시: 봇 메시지 배경색 변경 */
.mes:not([is_user="true"]) .mes_text {
    background-color: rgba(30, 60, 90, 0.3);
    border-radius: 10px;
    padding: 10px;
}
```

### 방법 2: CSS 변수 수정

테마 색상을 변경하려면 `:root` 변수를 오버라이드합니다:

```css
:root {
    --SmartThemeBodyColor: #e0e0e0;        /* 텍스트 색상 */
    --SmartThemeQuoteColor: #4a9eff;       /* 인용 색상 */
    --SmartThemeBlurTintColor: rgba(10, 10, 30, 0.95); /* 배경 틴트 */
    --SmartThemeBorderColor: rgba(100, 100, 255, 0.3); /* 테두리 */
}
```

### 방법 3: 소스 파일 직접 수정

특정 파일을 수정해야 할 경우, 각 파일의 상단 주석을 참고하여 해당 섹션을 찾으세요.

---

## 8. 자주 사용되는 커스터마이징 패턴

### 채팅 메시지 스타일링

```css
/* 봇 메시지 */
.mes:not([is_user="true"]) { /* ... */ }

/* 사용자 메시지 */
.mes[is_user="true"] { /* ... */ }

/* 메시지 텍스트 */
.mes_text { /* ... */ }

/* 메시지 아바타 */
.mesAvatarWrapper .avatar { /* ... */ }

/* 캐릭터 이름 */
.ch_name .name_text { /* ... */ }
```

### 배경 & 레이아웃

```css
/* 메인 배경 */
#bg1 { /* ... */ }

/* 채팅 영역 너비 */
:root { --sheldWidth: 60vw; }

/* 상단 바 */
#top-bar { /* ... */ }

/* Send form */
#send_form { /* ... */ }
```

### 사이드 패널

```css
/* 오른쪽 네비게이션 패널 */
#right-nav-panel { /* ... */ }

/* 왼쪽 네비게이션 패널 */
#left-nav-panel { /* ... */ }

/* 드로어 컨텐츠 */
.drawer-content { /* ... */ }
```

### 버튼 & 인터렉터블

```css
/* 메뉴 버튼 */
.menu_button { /* ... */ }

/* 오른쪽 메뉴 버튼 */
.right_menu_button { /* ... */ }

/* 인라인 드로어 토글 */
.inline-drawer-toggle { /* ... */ }
```

### 아바타 커스터마이징

```css
/* 아바타 기본 크기 변경 */
:root {
    --avatar-base-height: 60px;
    --avatar-base-width: 60px;
}

/* 둥근 아바타 */
body.rounded-avatars .avatar { border-radius: 50% !important; }

/* 큰 아바타 모드 배율 */
:root {
    --big-avatar-height-factor: 2.0;
    --big-avatar-width-factor: 1.3;
}
```

### 팝업 크기 조정

```css
/* 기본 팝업 너비 */
.popup { width: 600px; }

/* 큰 팝업 */
.popup.large_dialogue_popup { width: 80vw; }

/* 넓은 팝업 */
.popup.wide_dialogue_popup { width: 60vw; }
```

---

## 9. 주의사항 및 알려진 이슈

### 수정 시 주의사항

1. **`*.min.css` 파일은 절대 수정하지 마세요** — 서드파티 라이브러리이며 업데이트 시 덮어씌워집니다.
2. **CSS 변수 의존성** — 많은 스타일이 `--SmartTheme*` 변수에 의존합니다. 변수를 제거하면 광범위한 UI 깨짐이 발생합니다.
3. **`!important` 사용 최소화** — `toggle-dependent.css`, `mobile-styles.css` 등에서 이미 `!important`가 사용되고 있어 오버라이드가 어려울 수 있습니다.
4. **미디어 쿼리 순서** — `mobile-styles.css`의 미디어 쿼리 순서 (1000px → 450px → iOS)를 유지하세요.
5. **body 클래스 의존성** — `toggle-dependent.css`의 스타일은 JavaScript가 `body`에 추가하는 클래스에 의존합니다. 클래스명을 변경하면 CSS가 작동하지 않습니다.
6. **`user.css`를 우선 사용** — 직접 소스를 수정하기 전에 `user.css`에서 오버라이드를 시도하세요.

### 알려진 특이사항

- **Safari 전용 수정**: `popup-safari-fix.css`와 `mobile-styles.css` 하단의 `@supports (-webkit-touch-callout: none)` 블록
- **Select2 오버라이드**: `select2.min.css` 위에 `select2-overrides.css`가 로드되어 테마를 적용합니다
- **팝업 import 체인**: `popup.css` → `dialog-polyfill.css` + `popup-safari-fix.css`
- **Quick Reply는 가장 큰 확장 CSS** (1,087줄) — 수정 시 주의
- **`style.css`의 6,301줄**은 리팩토링 대상이지만 현재 상태를 유지하고 있습니다

### CSS 디버깅 팁

1. 브라우저 개발자 도구에서 `Elements` 탭으로 실시간 확인
2. `st-tailwind.css`의 `.debug-*` 클래스를 활용 (빨강/노랑/초록/파랑/보라 테두리)
3. `body` 클래스를 확인하여 현재 활성 모드 파악
4. `--SmartTheme*` 변수 값을 개발자 도구 `:root`에서 실시간 변경하여 미리보기

---

> **인수인계 요약**: 이 문서와 각 CSS 파일의 섹션 주석을 참고하여 커스터마이징을 진행하세요. 테마 변경은 CSS 변수 오버라이드로, UI 레이아웃 변경은 해당 파일의 섹션을 찾아 수정하면 됩니다. `user.css`를 최우선으로 활용하는 것을 권장합니다.


---

## 10. 20대 여성 감성 테마 — `CUSTOM/feminine-design.css`

> **파일**: `CUSTOM/feminine-design.css`
> **디자인 콘셉트**: Soft Cream × Dusty Rose × Elegant Simplicity

### 설계 원칙 (우선순위 순)

| 우선순위 | 원칙 |
|----------|------|
| 1 | 가독성 — 이북리더기 수준의 텍스트 읽힘 |
| 2 | 웹페이지 디자인 — 여백의 미, 깔끔한 레이아웃 |
| 3 | 게임 감성 — 모바일 가차 dialogue 같은 채팅창 |

### 컬러 팔레트

| 변수 | 값 | 용도 |
|------|----|------|
| `--fem-rose` | `#C9848F` | 주 강조색 (dusty rose) |
| `--fem-sage` | `#9BAF9A` | 보조 강조색 (sage green) |
| `--fem-warm-dark` | `rgba(28,18,21,0.96)` | 배경 기반 색 |
| `--fem-card-bg` | `rgba(42,30,34,0.55)` | 카드/패널 배경 |
| `--fem-text` | `rgba(242,230,225,0.95)` | 본문 텍스트 (크림 화이트) |
| `--fem-text-muted` | `rgba(195,175,168,0.68)` | 보조 텍스트 |

### 주요 구현 사항

- **Pretendard 폰트**: CDN via jsDelivr 자동 임포트
- **채팅창 줄간격**: `1.9rem` (이북리더기 수준)
- **UI 창 줄간격**: `1.4rem`
- **메시지 방향**: `.mes { flex-direction: column }` 적용
- **메시지 버튼 애니메이션**: 호버 시 `translateY(-20px → 0)` + `opacity 0→1`
- **상단바**: 완전 투명 배경, 패널 최대 높이 `80dvh`, 아이콘 소형화(`0.78em`)
- **QR 팝업**: `backdrop-filter blur(26px)`, `max-height: 68dvh`, 스크롤 가능, 슬라이드 호버 효과
- **스크롤바**: `scrollbar-width: none` + `::-webkit-scrollbar { display: none }` 전역 적용
- **텍스트 정렬**: `text-align: justify; text-justify: inter-character` (균등분할 X)
- **UI 헤더 그라데이션 제거**: `h1~h6, .title_restorable` 등 배경 초기화
- **SillyTavern CSS 변수 오버라이드**: `--SmartTheme*` 변수로 테마 통합

### 적용 방법

`default/public/css/user.css` 파일에 아래 내용을 붙여넣습니다:

```css
/* 20대 여성 감성 테마 적용 */
/* (아래 CUSTOM/feminine-design.css 의 전체 내용을 여기에 붙여넣기) */
```

또는 SillyTavern 설정 > 사용자 CSS 입력창에 `CUSTOM/feminine-design.css` 내용을 붙여넣으세요.

---

----

# 공통적인 요소
### 모든 사용자 작성 css는 해당 폴더 안에 새로운 파일로 만들어야 합니다.
### 하단의 항목들을 지켜야 합니다.
### 기본적으로 이북리더기처럼 글이 잘 읽히는게 먼저입니다.

* 말풍선/플랫버블/문서 각각 호환되도록 css구조 정밀하게
* 말풍선- 간격 empty하게 해서 채팅 여러개 보내는 것처럼 / 플랫버블- 게임 대사 dialogue 처럼 (char user 다  채팅 가로 flex하게) / 문서: 이북리더기 튼것처럼
* * 페르소나창/캐릭터선택창 꾸밈요소 특히 신중하게 (가독성+디자인 두마리토끼 다잡아야함)
*** 꾸밈에 매몰되어 uiux 좆박지않도록 주의
* 상단바의 최대 패널높이 전체 높이의 80%여야 합니다. 상단바는 배경색이 투명해야합니다.
* QR 버튼을 누를 때 나오는 내부 아이템은 예쁜 팝업 형태로 나와야 하며 스크롤도 가능해야합니다.
* 모든 요소에 디자인적 요소가 들어가야 합니다.
* 모든 요소는 컴팩트하고, 심플하고, 깔끔하면서도 디자인적 요소가 들어가야 합니다. 제1순위는 글이 가독성 좋게 읽히느냐입니다.
* .mes {flex-direction: column;} 사용하세요.
* .mes_buttons {opacity: 0 !important; transform: translateY(-20px) !important;transition: opacity .3s cubic-bezier(.25,.1,.25,1), transform .3s cubic-bezier(.25,.1,.25,1);} .mes:hover .mes_buttons {opacity: 1 !important;transform: translateY(0) !important;} 사용하세요.
* 상단, 하단의 아이콘은 작아야 합니다.
* 아이콘들이 깨지면 안됩니다.
* 채팅창 부분이 상단바까지 보이도록 해야합니다.
* 그림자 일체금지
* 20대 여성이 만족할만한 가독성이어야 합니다 (핑크 쓰라는말 X 여성혐오적 고정관념에서 벗어나셈)
* 스크롤바는 보여선 안 됩니다.
* 양쪽정렬을 사용합니다. (단 균등분할 X)
* 라이트테마 기준
* UI의 h4, h5 등 검은색 그라데이션이 배경으로 들어가는 부분을 제거합니다.
* 프리텐다드를 사용하며, 채팅창의 줄간격은 1.9rem, ui창의 줄간격은 1.4rem을 사용합니다.
* column, margin, padding 등의 적당한 분배로 ui 디자인을 가독성 좋게 작업합니다. 간격 등 여백의 미를 중요시하세요
* 아주 다양한 요소에 디자인이 가미되어야합니다.
* 채팅창 디자인은 모바일 가차게임의 채팅 dialogue같은, 그러나 심플한 느낌으로 가독성 좋게 작업합니다.
* 너무 심한 디자인은 금지합니다.
* 너무 흰색은 눈이 아프니 약간 크림색으로 작업합니다.
* 전반적으로 가장 중요한 것: 1순위-가독성, 2순위-웹페이지 디자인, 3순위- 약간의 게임같은 디자인 (아주살짝)
