# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with this repository.

## 개요

부산대학교 컴퓨터보안연구실(CSLab @ PNU, 권동현 교수)의 순수 정적 HTML 사이트입니다. 빌드 도구·패키지 매니저는 없으며 GitHub Pages로 서비스됩니다. main에 반영하면 배포가 시작되므로 배포가 허용된 작업인지 먼저 확인합니다.

```bash
python3 -m http.server 8000
# http://localhost:8000
```

## 콘텐츠 갱신

논문·뉴스·멤버·모집 등 콘텐츠를 변경하기 전에 **UPDATE_GUIDE.md**를 읽습니다. 현재 DOM 템플릿, 출처 확인, ID·집계 규칙과 검증 목록은 이 파일을 기준으로 합니다.

- 논문·특허 기준 목록: `publications.html`
- 멤버 기준 목록: `members.html`
- 전체 뉴스 기록: `news.html`
- 메인 요약: `index.html`의 `.hero-news`, `.hero-kpi`, `.ticker`
- 대표 논문 링크: `research.html`

자주 틀리는 부분:

- 논문은 `.pbadges` 구조를 사용합니다. 예전 `.pvenue` 템플릿을 복사하지 않습니다.
- 논문 링크 버튼은 **LINK / CODE**입니다. 출판사 안내 페이지에 PDF라고 표시하지 않습니다.
- 탭 총계와 배지 집계는 스크립트가 계산하지만 초기 탭 숫자와 메인 hero-kpi 숫자는 정적 HTML도 갱신해야 합니다.
- `.year-head .summary`는 숨겨져 있습니다. 현재 상단 stats 박스는 없으므로 남은 CSS를 실제 UI로 착각하지 않습니다.
- Ticker는 한 lane 안에서 동일 내용을 두 번 반복합니다. 양쪽 절반을 함께 갱신합니다.
- 제목·저자 철자·저자 역할은 논문별 출판사 근거를 확인합니다. 프로필과 논문 간 표기 차이를 기계적으로 덮어쓰지 않습니다.
- 뉴스 날짜는 사건 발생일이며 “최근 소식”은 가장 최근 뉴스 날짜입니다. 사이트 편집일과 구분합니다.

## 아키텍처

1. `index.html`은 자체 인라인 스타일·헤더·푸터·스크립트를 사용합니다.
2. 서브페이지 6개 (members, publications, research, projects, photos, news)는 `assets/site.css`와 페이지 전용 스타일을 사용하며 `assets/chrome.js`가 공통 헤더·푸터와 모바일 메뉴를 주입합니다.
3. 네비게이션 및 모집 pill은 index.html과 chrome.js 양쪽에 있습니다. News는 메인 Recent News의 “전체 보기”에서 연결됩니다.
4. 기본 테마는 라이트입니다. 저장된 localStorage의 cslab-theme이 dark일 때 `html[data-theme="dark"]`를 적용합니다. 두 테마·모바일 화면을 검증합니다.
5. 이메일은 `<a class="js-email" data-u="로컬파트" data-d="도메인">` 형태로 작성합니다. 런타임에 주소를 조립하므로 평문 mailto를 새로 복사하지 않습니다.

## 자원과 보존 대상

- `assets/researchers/<한글이름>.jpg`: 멤버 사진.
- `assets/cv/kwon-cv.pdf`: 교수 CV. HTML 서지 형식 변경만으로 덮어쓰지 않습니다.
- `uploads/`: 원본 모음. 화면이 실제 참조하는 것은 주로 assets입니다.
- index.html의 숨겨진 이전 섹션과 index_light.html은 기본 갱신 대상이 아닙니다.
- CSLAB Webpage.html, CSLAB Webpage (Light).html, CSLab-PNU.html, cslab-standalone.html은 30MB급 오프라인 스냅샷입니다. 전체 파일을 무분별하게 읽거나 검색하지 않습니다. 일상적인 콘텐츠 갱신 대상이 아닙니다.

수정 후 UPDATE_GUIDE.md의 검증 목록을 적용합니다. 배포 시 원격 커밋, GitHub Pages 결과와 실제 서비스 파일을 확인합니다.

