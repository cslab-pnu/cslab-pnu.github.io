# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 개요

부산대학교 컴퓨터보안연구실(CSLab @ PNU, 권동현 교수) 홈페이지. 빌드 도구·패키지 매니저·테스트가 전혀 없는 **순수 정적 HTML 사이트**이며, GitHub Pages로 서비스된다 (`origin/main`에 push하면 자동 배포).

```bash
# 로컬 미리보기 (chrome.js 주입 확인을 위해 http 서버 권장)
python3 -m http.server 8000   # → http://localhost:8000

# 배포
git push origin main
```

## 콘텐츠 갱신 작업은 UPDATE_GUIDE.md가 마스터 가이드

논문 추가, 뉴스/수상 추가, 멤버 합류·졸업, 모집 공고 등 **콘텐츠 갱신 요청을 받으면 먼저 `UPDATE_GUIDE.md`를 읽을 것.** 시나리오별 DOM 템플릿(`<article class="pub">`, `.news-row`, `.mcard` 등), pid 부여 규칙(IC/IJ/DC/DJ/DP/IP), 작업 후 체크리스트가 전부 정리되어 있다.

가장 자주 틀리는 함정 (상세는 가이드 §0.3, §9):

- **통계 숫자가 여러 파일에 하드코딩**되어 있다. 논문·멤버 수가 바뀌면 `index.html`(About stats, 멤버 수 문구 2곳)과 `publications.html`(stats 박스 4개 + maintab `.ct` 3개 + 연도 summary)을 전부 동기화해야 한다.
- 메인 ticker는 무한 스크롤을 위해 **내용이 lane 안에 2번 반복**된다. 항목 추가 시 양쪽 절반에 똑같이 넣을 것.
- 데이터 마스터: 논문 = `publications.html`, 멤버 = `members.html`. `index.html`의 News/stats는 보조 표시.

## 아키텍처

두 가지 페이지 패턴이 공존한다:

1. **`index.html` — 자체 완결형.** `assets/site.css`·`chrome.js`를 사용하지 않고 인라인 `<style>`(line 12~534), 자체 `<header>`(line 539~), 자체 푸터·스크립트를 가진다. 다크 테마 기본.
2. **서브페이지 5개** (`members` / `publications` / `research` / `projects` / `photos`) — `assets/site.css` 링크 + 페이지 전용 `<style>` 블록, 그리고 `<div id="site-header">` / `<div id="site-footer">` 플레이스홀더에 `assets/chrome.js`가 DOMContentLoaded 시점에 공통 헤더·푸터·모바일 드로어를 주입한다.

이 구조 때문에:

- **네비게이션 항목 변경은 두 곳**: `chrome.js`의 `nav` 배열(서브페이지용) + `index.html`의 자체 헤더.
- 헤더의 모집 pill("2026 연구원 모집 중")도 `chrome.js`와 `index.html` 양쪽에 존재한다.
- 새 서브페이지를 만들 때는 기존 서브페이지(예: `photos.html`)의 head + placeholder 패턴을 따를 것.

공통 동작 (`assets/chrome.js`):

- **테마 토글**: `localStorage['cslab-theme']` → `html[data-theme="dark"]`. 스타일은 두 테마 모두에서 확인할 것.
- **이메일 난독화**: 이메일은 평문 `mailto:`로 쓰지 않고 `<a class="js-email" data-u="로컬파트" data-d="도메인">` 형태로 넣으면 chrome.js가 런타임에 조립한다 (스크래핑 방지 목적, 커밋 `57c063c` 참고).

## 주의사항

- **30MB급 standalone HTML** (`CSLAB Webpage.html`, `CSLAB Webpage (Light).html`, `CSLab-PNU.html`, `cslab-standalone.html`)은 오프라인용 스냅샷 번들이다. **절대 Read/Grep으로 열지 말 것** (컨텍스트 초과). 평소 갱신 대상도 아니다.
- `index_light.html`은 레거시 라이트 테마 변형으로 nav에 연결되어 있지 않다. 평소 수정 불필요.
- `index.html`, `members.html`, `publications.html`, `index_light.html`의 head에 `local.adguard.org` 스크립트 태그가 잔존한다 (브라우저 저장 과정에서 유입된 잔재). 새 페이지에 복사하지 말 것.
- 멤버 프로필 사진은 `assets/researchers/<한글이름>.jpg` (파일명 = 한글 이름). `uploads/`는 원본 소스 모음이고 실제 페이지가 참조하는 것은 `assets/` 쪽이다.
