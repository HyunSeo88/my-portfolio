# Jekyll을 이용한 개인 포트폴리오 및 학습 아카이브 사이트 구축 가이드

## 1. 윈도우 환경 Jekyll 설치 및 준비
- **RubyInstaller**로 Ruby+DevKit 설치 (MSYS2 및 MINGW 개발 도구 포함)
- `gem install jekyll bundler`를 통해 Jekyll과 Bundler 설치
- `jekyll -v`로 설치 확인
- (선택) WSL 환경에서 우분투에 Ruby 설치 후 Jekyll 사용 가능

## 2. 다국어 지원 Jekyll 테마 선택 및 적용
- **GitHub Pages 호환성**: Gem 기반 또는 Remote Theme 지원 테마 선택
- **다국어 지원 여부**: Mr. Green, jekyll-multiple-languages-plugin 등 검토
- **권장 테마**: Minimal Mistakes (Gem/Remote 모두 지원), Mr. Green (다국어 내장)
- **원격 테마 적용**: `_config.yml`에 `remote_theme`로 지정
- **테마 포크**: GitHub에 포크 후 `username.github.io`로 사용

## 3. 다중 섹션 및 이중언어 지원을 위한 폴더 구조
```
my-portfolio/
├─ _config.yml
├─ _posts/               # 한국어 블로그
│   └─ 2025-01-01-sample-post.md
├─ _projects/            # 한국어 프로젝트
│   └─ project1.md
├─ en/                   # 영어 콘텐츠 루트
│   ├─ _posts/           # 영어 블로그
│   │   └─ 2025-01-01-sample-post.md
│   └─ _projects/        # 영어 프로젝트
│       └─ project1.md
├─ _layouts/             # 레이아웃
├─ _includes/            # 포함 파일
├─ _data/                # 네비게이션 등 설정
└─ assets/               # CSS, 이미지 등
```
- **컬렉션 사용**: `_projects`를 `collections`에 등록
- **카테고리 구성**: Front Matter에 `categories: [study, IT]` 등
- **서브폴더**: `study/_posts/` 등 폴더명으로 자동 카테고리 지정

## 4. 한국어·영어 콘텐츠 작성 및 연결
1. `_posts`와 `en/_posts`에 같은 파일명으로 한글/영문 버전 작성
2. `_config.yml`에 defaults 설정:
   ```yaml
   defaults:
     - scope: { path: "_posts", type: posts }
       values: { lang: "ko", permalink: "/:categories/:title/" }
     - scope: { path: "en/_posts", type: posts }
       values: { lang: "en", permalink: "/en/:categories/:title/" }
   ```
3. `display-title`, `display-subtitle` 등 다국어 변수 설정
4. Liquid로 언어 토글 링크 구현:
   ```liquid
   {% if page.lang == "ko" %}
     <a href="{{ "/en" | append: page.url }}">English</a>
   {% else %}
     <a href="{{ page.url | remove_first: "/en" }}">한국어</a>
   {% endif %}
   ```

## 5. 내비게이션 및 레이아웃 커스터마이징
- **_data/navigation.yml**로 메뉴 정의
- 주요 메뉴: Projects, Study; Study 하위: IT, Finance, Satellite
- **카테고리 아카이브**: `layout: category`, `taxonomy: IT` 등 페이지 생성
- **프로젝트 메인 페이지**: `_projects` 컬렉션 출력 그리드
- **언어별 메뉴**: `page.lang` 조건 분기
- **Breadcrumbs**: Minimal Mistakes 베타 기능 사용 가능

## 6. GitHub Pages 배포
1. `username.github.io` 저장소 생성
2. 로컬 Git 초기화 → 커밋 → `main` 브랜치 푸시
3. Settings > Pages 확인 및 빌드 상태 점검
4. `https://<username>.github.io` 접속 테스트

**유의사항**: 지원되지 않는 플러그인 주의, `baseurl` 설정

## 7. 콘텐츠 유지보수 및 업데이트
- **새 글/프로젝트 추가**: `_posts`, `_projects` 폴더에 파일 추가 후 푸시
- **카테고리 관리**: Front Matter와 카테고리 페이지 추가
- **URL 변경 시 Redirect**: `jekyll-redirect-from` 플러그인 활용
- **테마 업그레이드**: Gemfile/GitHub Pages 버전 고려
- **백업 및 브랜치 관리**: 로컬 백업, 브랜치 테스트 후 머지
- **검색/댓글/통계**: Lunr.js, Disqus, Google Analytics 옵션 탐색
- **문서화**: README에 프로젝트 구조, 작성 규칙 정리
