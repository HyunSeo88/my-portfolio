# My Portfolio

개인 포트폴리오 및 학습 아카이브 사이트입니다. Jekyll과 Minimal Mistakes 테마를 사용하여 구축되었습니다.

## 주요 기능

- 한국어/영어 이중언어 지원
- 프로젝트 포트폴리오 섹션
- 학습 내용 아카이브
- 카테고리 기반 콘텐츠 구성

## 디렉토리 구조

```
my-portfolio/
├─ _config.yml          # 사이트 설정
├─ _posts/              # 한국어 블로그
├─ _projects/           # 한국어 프로젝트
├─ en/                  # 영어 콘텐츠
│   ├─ _posts/          # 영어 블로그
│   └─ _projects/       # 영어 프로젝트
├─ _layouts/            # 레이아웃
├─ _includes/           # 포함 파일
├─ _data/               # 설정 파일
└─ assets/              # 정적 파일
```

## 사용 방법

1. 로컬에서 실행:
```bash
bundle install
bundle exec jekyll serve
```

2. GitHub Pages 배포:
- main 브랜치에 푸시하면 자동으로 배포됩니다.

## 작성 규칙

- 블로그 포스트: `_posts/YYYY-MM-DD-title.md`
- 프로젝트: `_projects/project-name.md`
- 영어 콘텐츠: `en/_posts/` 또는 `en/_projects/`에 동일한 구조로 작성 