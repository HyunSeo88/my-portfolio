# 다음 작업 목록 (Next To-Dos)

## 1. 필수 구현 사항

- [ ] **작성자 정보 업데이트:** `_config.yml` 파일의 `author` 섹션에 있는 이름, 이메일, GitHub 등 플레이스홀더 값을 실제 정보로 수정합니다.
- [ ] **프로필 이미지 추가:** `_config.yml`의 `author.avatar` 경로 (`/assets/images/bio-photo.jpg`)에 실제 프로필 이미지 파일을 추가하거나, 경로를 수정합니다.
- [ ] **실제 콘텐츠 추가:**
    - [ ] 한국어 블로그 포스트 (`_posts/`) 추가
    - [ ] 한국어 프로젝트 (`_projects/`) 추가
    - [ ] 영어 블로그 포스트 (`en/_posts/`) 추가
    - [ ] 영어 프로젝트 (`en/_projects/`) 추가
    - 각 콘텐츠 파일에 적절한 Front Matter (title, date, categories, tags 등) 작성
- [ ] **카테고리 페이지 구현:**
    - [ ] `categories.md` (한국어) 페이지에 카테고리별 포스트 목록 표시 로직 추가 (Minimal Mistakes 테마 기능 활용 또는 커스텀 구현)
    - [ ] `en/categories.md` (영어) 페이지에 카테고리별 포스트 목록 표시 로직 추가

## 2. 기능 보완 및 개선

- [ ] **영문 페이지 언어 명시:**
    - [ ] `en/projects.md` Front Matter에 `lang: en` 추가
    - [ ] `en/categories.md` Front Matter에 `lang: en` 추가
- [ ] **Breadcrumbs 설정:** (선택 사항) Minimal Mistakes 테마의 Breadcrumbs 기능 활성화 및 설정 (`_config.yml` 또는 페이지 Front Matter)
- [ ] **다국어 콘텐츠 연결:** 각 포스트/프로젝트 페이지 내부에 다른 언어 버전으로 바로 이동할 수 있는 링크 추가 (`jekyll_guide.md` Liquid 코드 참고)
- [ ] **검색 기능 추가:** (선택 사항) Lunr.js 등 검색 솔루션 연동
- [ ] **댓글 기능 추가:** (선택 사항) Disqus, Giscus 등 댓글 시스템 연동
- [ ] **웹 분석 도구 추가:** (선택 사항) Google Analytics 등 연동
- [ ] **URL 리디렉션:** (필요 시) `jekyll-redirect-from` 플러그인 설치 및 설정 (URL 구조 변경 시 이전 URL 접근 처리)

## 3. 디자인 및 UI/UX 개선

- [ ] **테마 스킨 적용:** `_config.yml`의 `minimal_mistakes_skin` 값을 변경하여 다른 스킨 테스트 및 적용
- [ ] **CSS 커스터마이징:** (필요 시) `assets/css/main.scss` 파일을 생성하여 테마 CSS를 덮어쓰거나 확장
- [ ] **로고 추가/설정:** (선택 사항) 사이트 로고 이미지 추가 및 설정
- [ ] **폰트 변경:** (선택 사항) 웹 폰트 적용 등
- [ ] **레이아웃 조정:** (필요 시) `_layouts` 또는 `_includes` 디렉토리에 커스텀 파일 추가하여 레이아웃 수정

## 4. 문서화 업데이트

- [ ] **README.md 업데이트:** 프로젝트 진행 상황, 추가된 기능, 커스터마이징 내용 등을 반영하여 README 파일 업데이트 