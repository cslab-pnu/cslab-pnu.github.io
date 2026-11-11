# CSLab 홈페이지 업데이트 가이드

현재 HTML 구현을 기준으로 콘텐츠 위치, 입력 형식, 검증 순서를 정리합니다. 순수 정적 사이트이며 빌드 도구·패키지 매니저는 없습니다. 줄 번호 대신 아래 선택자와 클래스 이름으로 위치를 찾습니다.

## 0. 페이지와 데이터 출처

| 파일 | 역할 |
|---|---|
| `index.html` | Hero, 성과 통계, Recent News 요약, Ticker, 연구 소개, 하위 페이지 링크, 모집 안내 |
| `publications.html` | 논문·특허 기준 목록. 국제/국내/특허 탭과 배지 집계 |
| `members.html` | 교수 소개, 연구원 카드, Alumni 기준 목록 |
| `news.html` | 연도별 전체 뉴스 기록과 Paper/Award/Notice 필터 |
| `research.html` | 연구 주제와 대표 논문 링크 |
| `projects.html` | 수행 중·완료 과제, 기관 역할과 기간 |
| `photos.html` | 연도별 사진 갤러리 |

- `index.html`은 자체 스타일·헤더·푸터·스크립트를 사용합니다. 나머지 페이지는 `assets/site.css`와 `assets/chrome.js`를 사용하고, 공통 헤더·푸터를 `#site-header`, `#site-footer`에 주입합니다.
- 기본 테마는 라이트입니다. 저장된 `localStorage['cslab-theme']`이 `dark`이면 다크 테마를 사용합니다.
- 네비게이션이나 모집 pill 변경 시 `index.html`과 `assets/chrome.js`를 함께 확인합니다.
- `index.html`의 숨겨진 이전 섹션은 현재 표시되는 목록이 아닙니다.
- `index_light.html`은 연결되지 않은 레거시 변형입니다. 30MB급 standalone HTML 4개는 오프라인 스냅샷으로 일상적인 갱신 대상이 아닙니다. 전체 파일을 무분별하게 읽거나 검색하지 않습니다.

### 자원 위치

- 멤버 사진: `assets/researchers/<한글이름>.jpg`
- 연구 이미지: `assets/research/`
- 갤러리·페이지 이미지: `assets/draft/`
- 로고: `assets/cslab-mark.png`, `assets/cslab-landscape.png`
- 모집 포스터: `assets/recruit-2026.png` 등 실제 참조 경로
- 교수 CV: `assets/cv/kwon-cv.pdf` (`members.html`에서 연결)
- `uploads/`는 원본 소스 모음입니다. 화면이 실제 참조하는 자원을 확인합니다.

## 1. 논문·특허 추가

### 1.1 위치와 ID

해당 `.tabsec[data-tab]`의 `.year-block`에 넣습니다. 같은 연도에서는 확인된 날짜 기준 최신순을 유지합니다. 온라인 출판연도와 권·호 발행연도가 다르면 둘 다 명시하고 기존 연도 분류 기준을 유지합니다.

| 종류 | data-cat | data-sub | ID | 배지 |
|---|---|---|---|---|
| 국제 학술대회 | intl | conf | ICnn | 확인된 CSRankings/CORE/KIISE/BK21 등급 |
| 국제 학술지 | intl | journal | IJnn | 확인된 JCR 분위 |
| 국내 학술대회 | dom | conf | DCnn | pb d-conf |
| 국내 논문지 | dom | journal | DJnn | pb d-journal |
| 국내 학회지 | dom | mag | DJnn | pb d-mag |
| 국내 특허 | patent | dfile / dgrant | DPnn | pb p-dfile / pb p-dgrant |
| 국제 특허 | patent | ifile / igrant | IPnn | pb p-ifile / pb p-igrant |

ID는 자동 생성되지 않습니다. 접두사별 실제 최대 번호를 확인한 뒤 새 번호를 부여합니다. 빈 번호를 이유로 논문을 추가하거나 기존 항목을 재번호화하지 않습니다. 등록 특허는 출원·등록 배지를 함께 표시할 수 있지만 한 article로 유지합니다.

### 1.2 현재 템플릿

등급이 확인되지 않은 논문은 `.pbadges`를 비워 둡니다. 예전 `.pvenue` 템플릿을 복사하지 않습니다.

```html
<article class="pub" data-cat="intl" data-sub="conf">
  <div class="pid">ICnn</div>
  <div class="pbadges"></div>
  <div>
    <h3 class="ptitle">출판사에서 확인한 정확한 논문 제목</h3>
    <div class="pauthors">Author One, Author Two, and Author Three</div>
    <div class="pmeta"><span>Full Conference Name (ACRONYM) · Sep. 2026</span></div>
  </div>
  <div class="plinks"></div>
</article>
```

국내 논문도 같은 구조에 `data-cat="dom"`, 해당 `data-sub`, 종류 배지와 한글 저자 목록을 사용합니다.

```html
<div class="pbadges"><span class="pb d-conf">학술대회</span></div>
<div class="pauthors"><u>저자1</u>, <u>저자2</u>, <u class="corr">권동현*</u></div>
<div class="pmeta"><span>학술대회명 (약칭) · 2026.11.05 · pp. 19–21</span></div>
```

위 날짜·페이지·저자 역할은 구조 예시입니다. 실제 항목에는 확인된 값만 넣습니다.

### 1.3 서지 형식과 사실 보존

- 제목은 출판사 페이지·출판본 또는 출판사가 등록한 DOI 메타데이터와 대조합니다. 대소문자·복수형·기술명·하이픈을 일괄 변환하지 않습니다.
- 저자 이름·순서·공동 제1저자·교신저자는 논문별 원문을 보존합니다. 다른 논문이나 프로필의 철자만 보고 자동으로 바꾸지 않습니다.
- 영어 다인 저자 목록은 `A, B, and C`, 한글 목록은 쉼표 구분을 사용합니다.
- 확인된 연구실 저자는 `<u>...</u>`로 표시합니다. 교신저자 `*`, 공동 제1저자 `**`는 근거가 있을 때만 표시합니다. 연구실 교신저자는 `<u class="corr">...</u>`를 사용합니다.
- 국제 논문의 학회·저널은 가능한 한 전체 이름과 괄호 속 약칭으로 씁니다. 날짜는 `Sep. 2026`, `May 2026`처럼 기존 영문 형식을 사용합니다. 국내 항목은 `YYYY.MM` 또는 확인된 `YYYY.MM.DD`를 사용합니다.
- 채택일과 출판일을 구분합니다: `<span>학회명 (약칭)</span><span>Accepted: Sep. 2026</span>`. 연도만 확인되면 `Accepted: 2026`으로 두고 월·일을 추측하지 않습니다.
- 연도만 알려진 논문에는 연도만 표시합니다. 확인된 일자·권·호·페이지·Article 번호·DOI는 단순한 항목에 맞추려고 삭제하지 않습니다.
- JCR/학회 등급은 확인된 기준만 반영합니다. `publications.html`의 `GROUPS`가 집계 라벨·설명을 정의하고 `.pbadges .pb`를 집계합니다.

### 1.4 링크

- 원문·출판사·DOI 링크는 **LINK**, 공개 구현은 **CODE** 버튼입니다. 출판사 안내 페이지에 PDF라고 표시하지 않습니다.
- 새 창 링크에는 `target="_blank" rel="noopener"`를 함께 사용합니다.
- CODE는 공개가 확인된 해당 논문의 구현 저장소에만 연결합니다. 링크가 없으면 `.plinks`를 비워 둡니다.
- `research.html`의 대표 논문도 링크가 확인되면 함께 갱신합니다. 짧은 제목의 대소문자도 원 논문의 기술명을 따릅니다.
- 개인 홈페이지 등 다른 저장소의 변경은 그 작업에 허용된 범위에서 별도로 진행합니다.

### 1.5 통계

- 국제 = intl article 수, 국내 = dom article 수, 특허 = patent article 수입니다. 출원·등록 배지가 여러 개여도 특허를 중복 합산하지 않습니다.
- `publications.html`의 `.maintab button .ct`는 초기 HTML에도 맞는 수를 쓰고, 스크립트가 실제 article 수로 다시 계산합니다.
- `#pubtally`는 배지 기준 자동 집계입니다. 배지가 없는 논문도 탭 전체 건수에는 포함됩니다.
- `index.html`의 `.hero-kpi .n`은 정적 값입니다. International/Domestic/Patents 수를 직접 동기화합니다. 네 번째 값은 운영연수입니다.
- 예전 상단 `.stats` 박스나 About 통계는 현재 화면의 통계 위치가 아닙니다. 남은 CSS만 보고 새 박스를 만들지 않습니다.
- `.year-head .summary`는 현재 숨겨져 있습니다. 갱신한다면 실제 항목 수를 사용하되 화면 집계의 근거로 삼지 않습니다.

## 2. 뉴스·수상·장학·과제 소식

전체 기록은 **news.html**의 `.timeline` → 해당 `.year-band` → `.news-list`에 최신순으로 추가합니다. 메인 페이지에 예전 타임라인을 다시 만들지 않습니다.

```html
<a class="news-row" href="publications.html" target="_blank" rel="noopener" data-tag="paper">
  <span class="date">2026.09.19</span>
  <span class="badge paper">ATC '26</span>
  <span class="title">“검증된 정확한 논문 제목” 채택</span>
  <span class="arr">→</span>
</a>
```

- data-tag와 배지 종류는 paper / award / notice입니다. 특허·과제·장학 등은 notice입니다.
- 링크가 없으면 `<div class="news-row no-link" data-tag="...">`를 사용하고 이동 화살표를 넣지 않습니다.
- 연도 헤더의 `N events`를 실제 행 수와 맞춥니다.
- 날짜는 사건 발생일입니다. 채택일·출판일·발표일을 혼동하지 않습니다.
- `#latest-news-date`는 “최근 소식” 날짜입니다. 정적 텍스트와 datetime을 최신 뉴스에 맞추고, 스크립트도 전체 행의 가장 최근 날짜를 계산합니다. 사이트 편집일로 덮어쓰지 않습니다.
- 메인 노출 소식은 `index.html`의 `.hero-news .hn-list`에 별도 요약합니다. 전체 기록과 제목·날짜가 일치해야 합니다.
- Ticker에 추가하면 `.ticker .lane`의 **앞·뒤 두 절반**에 같은 내용을 넣습니다. 무한 스크롤용 반복을 제거하지 않습니다.

## 3. 멤버 합류·진학·졸업

1. `members.html`의 해당 `.role-band` 다음 `.member-grid`에 `.mcard`를 추가하거나 이동합니다.
2. `M / NN`이 중복되지 않도록 실제 사용 번호를 확인하고, `.role-band .cnt`를 실제 카드 수와 맞춥니다.
3. 프로필 사진·이름·링크를 확인합니다. 공식 프로필 또는 본인이 확인한 철자가 없으면 영문 이름을 임의 수정하지 않습니다.
4. `.focus`는 확인된 연구 키워드를 담습니다. 모르는 관심 분야를 추측하지 않습니다.
5. 졸업생은 `.alumni-list`에 최신순으로 추가합니다. 학위 표기는 Ph.D. / M.S. / B.S.입니다. 졸업일·소속은 확인된 정보만 표시합니다.
6. `index.html`의 숨겨진 `#members`를 유지한다면 요약도 확인합니다. 현재 `.explore-grid` 카드에는 멤버 수 문구가 없습니다.

```html
<div class="row">
  <span class="yr">2027.02</span><span class="who">홍길동</span>
  <span class="deg">M.S.</span><span class="now">확인된 현재 소속</span>
</div>
```

## 4. 모집·프로젝트·사진·CV

- 모집: `index.html`의 `#recruit-pill`, `#recruit`, `#recruit-modal`과 `assets/chrome.js`의 모집 pill을 함께 확인합니다. 포스터 파일 경로도 갱신합니다.
- 과제: `projects.html`에서 현재/완료 상태, 기간, 집계 수를 맞춥니다. 지원부처·전문기관·발주기관·지원기관은 서로 다른 역할이므로 한 라벨로 통일하지 않습니다.
- 사진: `photos.html` 해당 연도에 추가하고 `.year-count`를 맞춥니다. 사진 설명·날짜는 확인된 정보로 작성합니다.
- CV: 재생성한 PDF의 페이지 나눔과 링크를 확인한 뒤 `assets/cv/kwon-cv.pdf`를 갱신합니다. HTML 서지 형식만 수정하는 작업에서 PDF를 덮어쓰지 않습니다.

## 5. 검증과 배포

```bash
python3 -m http.server 8000
# http://localhost:8000 에서 확인
```

- [ ] 기존 항목·ID·저자 순서·역할·날짜 정밀도가 의도 없이 바뀌지 않았는가?
- [ ] 논문 제목 변경을 출판사 근거와 대조했는가?
- [ ] 논문 총계·탭 숫자·메인 hero-kpi가 일치하는가?
- [ ] News 행 수·날짜·필터·“최근 소식” 표시가 일치하는가?
- [ ] 메인 Recent News와 Research의 중복 제목·링크가 일치하는가?
- [ ] Ticker 두 절반이 같은가?
- [ ] 멤버 그룹 수·ID·이미지·외부 링크가 맞는가?
- [ ] 데스크톱/모바일, 라이트/다크에서 줄바꿈과 가로 넘침을 확인했는가?
- [ ] 논문 탭 반복 전환, News 필터, 모바일 메뉴 열기/닫기와 Escape가 동작하는가?
- [ ] 콘솔 오류와 깨진 내부 링크를 확인했는가?
- [ ] 배포가 허용된 작업이면 변경 파일 검토·커밋 후 main에 반영했는가?
- [ ] GitHub Pages 배포 결과와 실제 서비스 파일을 확인했는가?

기존 작성일: 2026.05.19 · 구현 대조 갱신: 2026.11.11

