# CSLab 홈페이지 업데이트 가이드

이 문서는 CSLab @ PNU 홈페이지의 콘텐츠를 갱신할 때, **어떤 시나리오에서 어떤 파일·어떤 위치를 수정해야 하는지** 정리한 종합 가이드입니다. 다른 작업자나 AI 에이전트가 별도의 코드 분석 없이도 즉시 작업할 수 있도록, 각 파일의 역할·DOM 구조·갱신 순서를 명시했습니다.

---

## 0. 사이트 전체 구조

### 0.1 페이지 구성

| 파일 | 역할 | 비고 |
|---|---|---|
| `index.html` | 메인 페이지 (Hero / Ticker / About / Research / News / Photos / Recruit) | **다크 테마 기본**. 사이트 진입점 |
| `members.html` | 교수·박사·석사·학부연구생·Alumni 전체 목록 | |
| `publications.html` | 국제 학술지/학회 · 국내 학회/논문지 · 특허 전체 목록 | 데이터의 마스터 (단일 진실 공급원) |
| `research.html` | 연구 주제 상세 페이지 | 본 가이드 범위 외 |
| `projects.html` | 연구 과제 페이지 | 본 가이드 범위 외 |
| `photos.html` | 사진 갤러리 | 본 가이드 범위 외 |
| `CSLAB Webpage.html`, `CSLAB Webpage (Light).html`, `CSLab-PNU.html`, `cslab-standalone.html` | 오프라인용 standalone 번들 (자원 인라인됨) | 평소엔 손대지 않음. 큰 변경 후 재생성 |

### 0.2 자원 폴더

| 경로 | 내용 |
|---|---|
| `assets/researchers/<이름>.jpg` | 멤버 프로필 사진. **파일명 = 한글 이름** |
| `assets/draft/` | Hero / 페이지 헤더용 사진. `home-*.jpg`, `members-hero.jpg`, `r01.jpg` 등 |
| `assets/research/` | 연구 주제 카드용 이미지 (apps, pemi, uxom) |
| `assets/cslab-mark.png`, `cslab-landscape.png` | 로고 |
| `assets/hero-video*.mp4` | 메인 페이지 Hero 비디오 |
| `assets/recruit-2026.png` | 모집 포스터 |
| `assets/site.css` | 공통 스타일 (대부분 페이지가 link) |
| `assets/chrome.js` | 헤더/푸터 등 공통 chrome 주입 |

### 0.3 "통계" 숫자가 박혀 있는 위치 (자주 잊는 부분)

**같은 숫자가 여러 곳에 중복으로 박혀 있습니다.** 통계가 바뀌면 아래를 **모두** 동기화해야 합니다.

- `index.html` → About 섹션 `.stats` 박스 4개 (International / Domestic / Patents / Years)
- `publications.html` → 상단 `.stats` 박스 4개 (국제 학술지 / 국제 학술대회 / 국내 학술대회·학술지 / Patents)
- `publications.html` → `.maintab` 탭 버튼의 `.ct` 카운트 3개 (International / Domestic / Patents)
- `index.html` → Photos 위 explore-grid의 "박사 N명 · 석사 N명 · 학부연구생 N명" 문구 (line 866 부근)
- `index.html` → 숨겨진 #members 섹션의 `.sec-aside` 문구 (line 889 부근)

---

## 1. 시나리오: 새 논문 게재/채택 (실적 추가)

### 1.1 추가 위치 — `publications.html`

논문은 카테고리별로 다른 섹션에 들어갑니다. 카테고리에 맞는 `<section class="tabsec" data-tab="...">`를 찾고, 해당 연도의 `<div class="year-block">`에 새 `<article class="pub">`를 **연도 내 최신순(맨 위)** 으로 삽입.

| 카테고리 | 섹션 | 자동 부여 ID 규칙 | 가능한 `pvenue` 클래스 |
|---|---|---|---|
| 국제 학술대회 | `data-tab="intl"` | `ICnn` (현재 최대 IC18) | `tier sec` (Sec), `tier sys` (Sys), `tier` (그 외 top-tier), `gen` (일반) |
| 국제 학술지 | `data-tab="intl"` | `IJnn` (현재 최대 IJ24) | `journal` |
| 국내 학회 | `data-tab="dom"` | `DCnn` (현재 최대 DC55) | `dom` |
| 국내 논문지/학회지 | `data-tab="dom"` | `DJnn` (현재 최대 DJ09) | `journal` |
| 특허 | `data-tab="patent"` | 국내: `DPnn`, 국제: `IPnn` | `patent` (출원), `granted` (등록) |

#### `<article>` 템플릿 — 국제 학술대회 (Top-tier)

```html
<article class="pub selected" data-cat="intl">
  <div class="pid">IC19</div>
  <div class="pvenue tier sec">ACSAC '26</div>
  <div>
    <h3 class="ptitle">논문 제목</h3>
    <div class="pauthors">
      <u>저자1</u>, <u>저자2</u>, and <u class="corr">Donghyun Kwon*</u>
    </div>
    <div class="pmeta">
      <span>학회명 · 2026.10</span>
      <span class="tag rank">BK21-IF:3, acceptance rate 20%</span>
    </div>
  </div>
  <div class="plinks">
    <a href="DOI/URL" target="_blank" rel="noopener">PDF</a>
    <a href="https://github.com/cslab-pnu/REPO" target="_blank" rel="noopener">CODE</a>
  </div>
</article>
```

#### `<article>` 템플릿 — 국내 학회

```html
<article class="pub" data-cat="dom">
  <div class="pid">DC56</div>
  <div class="pvenue dom">ASK 학술대회</div>
  <div>
    <h3 class="ptitle">논문 제목</h3>
    <div class="pauthors"><u>저자1</u>, <u>저자2</u>, <u class="corr">권동현*</u></div>
    <div class="pmeta"><span>한국정보처리학회 · 2026.05</span></div>
  </div>
  <div class="plinks"></div>
</article>
```

#### 규칙

- 저자 중 **CSLab 소속 → `<u>...</u>`** 로 밑줄 처리
- **교신저자** → `<u class="corr">권동현*</u>` (별표 포함)
- 강조하고 싶은 논문 → `<article class="pub selected">` (현재 색 강조용)
- `.plinks` 버튼은 **PDF**(원문·출판사 페이지)와 **CODE**(구현 공개 저장소) 두 종류다.
  구현을 [`cslab-pnu`](https://github.com/cslab-pnu) 조직에 **public으로 공개한 논문에만** CODE를
  붙인다 (현재 XCFI, SMORE, ELK). 링크가 없으면 `<div class="plinks"></div>`로 비워 둔다.
  같은 버튼을 개인 홈페이지([`donghyun-kwon.github.io`](https://donghyun-kwon.github.io/))에도
  달아 두었으니, 저장소를 새로 공개하면 **양쪽 모두** 갱신할 것.
- 연도 블록이 없으면 새로 만들기. 템플릿:
  ```html
  <div class="year-block">
    <div class="year-head">
      <div class="y">2027</div>
      <div></div>
      <div class="summary"><b>01</b> top-tier conf · <b>02</b> journals</div>
    </div>
    <!-- article들 -->
  </div>
  ```

### 1.2 `publications.html` — 통계 갱신

상단 `.stats` 박스 + `.maintab .ct` 카운트를 새 숫자로 업데이트.

- 국제 학술지 (IJ 개수)
- 국제 학술대회 (IC 개수)
- 국내 학술대회·학술지 (DC+DJ 개수, "55+"처럼 둥근 표기)
- Patents (DP+IP 개수)
- maintab International = IJ + IC
- maintab Domestic = DC + DJ
- maintab Patents = DP + IP

또한 해당 연도 블록의 `<div class="summary">` 텍스트도 새 개수에 맞춰 수정.

### 1.3 `index.html` — News 타임라인에 행 추가

`#news` 섹션 → 해당 연도의 `<div class="year-band">` → `<div class="news-list">` 안에 행 추가. 같은 연도의 행들은 **최신순(맨 위)** 으로 배치.

```html
<a class="news-row" href="publications.html" target="_blank" rel="noopener" data-tag="paper">
  <span class="date">2026.10.03</span>
  <span class="badge paper">ACSAC '26</span>
  <span class="title">"논문 제목" 채택</span>
  <span class="arr">→</span>
</a>
```

- `data-tag` 값으로 필터링됨: `paper` / `award` / `notice`
- 연도 헤더 `<div class="yr">2026<small>— N events</small></div>`의 `N`을 1 올림
- 새 연도면 `<div class="year-band">`를 통째로 추가

### 1.4 `index.html` — About 통계 동기화

`#about .stats` 박스 4개 중 International / Domestic 숫자 갱신.

### 1.5 `index.html` — Ticker (상단 움직이는 배너) 갱신 (선택)

큰 임팩트 있는 실적(BK21 우수 학회 채택, SCIE Q1 게재 등)이면 ticker에 추가.

```html
<div class="ticker"> <div class="lane">
  <!-- 양쪽 lane에 동일하게 추가해야 무한 스크롤이 자연스러움 -->
  <span><b>USENIX '26</b> 논문 채택</span>
  <span class="star">★</span>
  ...
</div> </div>
```

> ⚠️ **ticker의 lane은 내용이 2번 반복됩니다.** 새 항목을 추가하면 **양쪽 절반에 똑같이** 넣어야 합니다 (CSS `animation: scroll` + `translateX(-50%)`로 무한 루프 구현).

---

## 2. 시나리오: 수상 / 장학금 / 과제 수주 등 뉴스 추가

### 2.1 `index.html` — News 타임라인 행 추가

`data-tag` 를 상황에 맞게 선택:
- `award` → 수상 (badge: `<span class="badge award">수상</span>`)
- `notice` → 특허·과제·장학 등 알림 (badge: `<span class="badge notice">특허</span>`, `과제`, `장학` 등)
- `paper` → 논문 (위 1.3 참고)

```html
<div class="news-row no-link" data-tag="award">
  <span class="date">2026.05.30</span>
  <span class="badge award">수상</span>
  <span class="title">홍길동 — 한국정보처리학회 ASK 2026 학회우수논문상 수상</span>
</div>
```

> 링크가 없는 뉴스는 `<div class="news-row no-link">`, 링크가 있는 뉴스는 `<a class="news-row" href="...">`.

연도 헤더의 `— N events` 숫자도 함께 1 올림.

### 2.2 `index.html` — Ticker 갱신

큰 수상은 ticker에도 추가. 양쪽 lane에 똑같이.

```html
<span><b>ASK '26</b> 김경환 학회우수논문상(학부생) 수상</span>
<span class="star">★</span>
```


---

## 3. 시나리오: 새 멤버(연구원) 합류

### 3.1 자원 추가

- `assets/researchers/<이름>.jpg` 추가 (정사각형, 동일 비율 권장)

### 3.2 `members.html` — 카드 추가

해당 신분(박사/석사/학부연구생)의 `<div class="member-grid">` 안에 카드 한 장 추가.

```html
<a class="mcard" href="#">
  <div class="ph">
    <span class="num">M / 14</span>
    <img src="assets/researchers/홍길동.jpg" alt="홍길동"/>
  </div>
  <div class="info">
    <div class="nm">홍길동<span class="en">GIL-DONG &nbsp;HONG</span></div>
    <div class="focus">연구 키워드 (예: 임베디드 메모리 보안)</div>
  </div>
</a>
```

#### 규칙

- `M / NN` 번호는 멤버 전체에서 고유. 기존 최대값 +1 (현재 M / 13까지 사용)
- 위 신분 그룹별 `<div class="role-band">` 의 카운트도 갱신:
  ```html
  <div class="role-band">
    <span class="lbl">Undergraduate Researchers</span>
    <span class="cnt">— 08 RESEARCHERS</span> <!-- 7→8 -->
  </div>
  ```

### 3.3 `index.html` — 멤버 수 표기 동기화

다음 2곳을 모두 수정:
1. **Photos 위 explore-grid** (라인 866 부근):
   ```html
   <p>박사 4명· 석사 2명 · 학부연구생 7명</p>
   ```
2. **숨겨진 #members 섹션 .sec-aside** (라인 889 부근):
   ```html
   <p class="sec-aside">박사과정 4명 · 석사과정 2명 · 학부연구생 7명 · Alumni.</p>
   ```

### 3.4 (옵션) `index.html` 뉴스에 합류 알림

원할 경우 `notice` 태그로 합류 소식 한 줄 추가 가능.

---

## 4. 시나리오: 멤버 졸업 / 신분 변경 / Alumni 이동

### 4.1 `members.html` — Researchers 그룹에서 제거

해당 카드를 박사/석사/학부연구생 `member-grid`에서 **삭제**하고, 그 그룹 `role-band`의 카운트를 1 감소.

### 4.2 `members.html` — Alumni 리스트에 행 추가

```html
<div class="alumni-list">
  <div class="row">
    <span class="yr">2027.02</span>
    <span class="who">홍길동</span>
    <span class="deg">Ph.D.</span> <!-- 또는 M.S. / B.S. -->
    <span class="now">현재 소속 (예: 삼성전자 종합기술원)</span>
  </div>
  <!-- 기존 행들 -->
</div>
```

학위 표기: `Ph.D.` / `M.S.` / `B.S.`. 최신순(맨 위) 정렬.

### 4.3 신분 변경 (예: 학부생 → 석사 진학)

1. `members.html`: 학부연구생 그룹에서 카드를 잘라내 석사 그룹에 붙여넣기
2. 양쪽 그룹의 `role-band .cnt` 카운트 모두 갱신
3. 카드 안의 영문 보조 텍스트나 focus 키워드도 필요 시 업데이트
4. `index.html`의 멤버 수 표기 동기화 (위 3.3)

### 4.4 `index.html` — 멤버 수 표기 동기화

3.3와 동일. 두 곳 모두 갱신.

### 4.5 `index.html` (옵션) — 졸업 뉴스

```html
<div class="news-row no-link" data-tag="notice">
  <span class="date">2027.02.28</span>
  <span class="badge notice">졸업</span>
  <span class="title">홍길동 — 박사 학위 취득 / 삼성전자 종합기술원 입사</span>
</div>
```

---

## 5. 시나리오: 모집 공고 갱신

### 5.1 `index.html`

- 상단 헤더 우측 pill: `<a id="recruit-pill">2026 연구원 모집 중</a>` (line 539)
- 모집 모달의 텍스트 (라인 597~ `.recruit`, h5/sub/혜택 ol 등)
- 모집 포스터 이미지: `assets/recruit-2026.png` 교체 (혹은 새 파일 추가 후 `<img src=...>` 경로 갱신)

### 5.2 모집 종료

`recruit-pill`을 숨기거나 텍스트를 "모집 마감"으로 바꾸고 모달 차단.

---

## 6. 시나리오: About 본문 / 연구 소개 등 카피 변경

| 위치 | 파일 |
|---|---|
| About 본문 (Mission / 주요 성과) | `index.html` (line 678 부근) |
| Research 카드 3종 (HW-assisted / Architectural / Applications) | `index.html` (#research), `research.html` (상세) |
| News 섹션 헤더 카피 ("최근 업데이트 YYYY.MM.DD") | `index.html` (line 750 부근) — 업데이트 날짜를 새로 고침 |

---

## 7. 작업 후 체크리스트

큰 변경 후 다음을 반드시 확인:

- [ ] `publications.html`의 4개 통계 박스 + 3개 maintab 카운트가 모두 새 수치인가?
- [ ] `index.html` About 통계 4개 + Photos 위 멤버 수 + 숨겨진 #members 멤버 수가 일치하는가?
- [ ] News의 연도 헤더 `— N events`가 그 연도 안의 행 수와 일치하는가?
- [ ] Ticker에 추가했다면 **양쪽 lane**에 똑같이 들어갔는가?
- [ ] `members.html`의 `role-band .cnt`가 실제 카드 수와 일치하는가?
- [ ] `M / NN` 번호가 중복 없이 유일한가?
- [ ] 새 멤버라면 `assets/researchers/<이름>.jpg`가 존재하는가?
- [ ] 추가한 article의 `<u>...</u>` (랩 멤버) 표기가 옳은가?
- [ ] 교신저자에 `<u class="corr">...*</u>` 표기가 있는가?

---

## 8. 표준 작업 흐름 (Workflow)

```
1) publications.html에 신규 article 추가
   ├─ pid 부여 (IC/IJ/DC/DJ/DP/IP)
   ├─ year-block summary 카운트 갱신
   └─ 상단 stats + maintab .ct 모두 갱신

2) index.html News 타임라인에 1줄 추가
   ├─ year-band의 'N events' +1
   └─ 큰 임팩트면 ticker(양쪽 lane) 동기화

3) index.html About 통계 .stats 4개 갱신
   ├─ International / Domestic / Patents 숫자 일치
   └─ Photos 위 멤버 수 문구도 확인

4) (멤버 변경 시) members.html
   ├─ member-grid에 카드 추가/이동/삭제
   ├─ role-band의 N RESEARCHERS 카운트 갱신
   └─ alumni-list에 졸업생 행 추가

5) Git: status → add → commit → push origin main
```

---

## 9. 자주 하는 실수

1. **Ticker 한쪽만 수정** — 무한 스크롤이 끊겨 보임. 반드시 양쪽 lane에 똑같이.
2. **maintab 카운트 깜빡** — 탭 버튼 옆 `<span class="ct">42</span>` 도 함께 갱신.
3. **연도 블록의 summary 깜빡** — `<div class="summary"><b>01</b> top-tier conf · <b>04</b> journal papers</div>` 도 카운트.
4. **standalone HTML 재생성 안 함** — 큰 변경 후엔 `CSLAB Webpage*.html`, `cslab-standalone.html` 도 재생성하거나 무시. 평소 트래픽은 `index.html`로 가므로 보통 무시 OK.
5. **role-band 카운트 깜빡** — `<span class="cnt">— 04 RESEARCHERS</span>`. 멤버 수 변경 시 함께 수정.
6. **M / NN 번호 충돌** — 새 멤버 추가할 때 기존 최대값 확인 후 +1. (현재 M/01 ~ M/13 사용, M/08 비어있음 — 비어있는 번호 채워도 OK)

---

## 10. 데이터 출처 우선순위

- 논문 정보의 **마스터** = `publications.html`. News는 보조 표시.
- 멤버 정보의 **마스터** = `members.html`. `index.html`의 #members 섹션은 현재 `display:none` 처리되어 보이지 않으므로 동기화 가능하면 좋고, 시간 없으면 생략 가능.

---

작성일: 2026.05.19
