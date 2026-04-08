# Project: beomine.github.io

Jekyll 기반 기술 블로그 (Chirpy 테마)

## Blog Post Format

```yaml
---
layout: post
title: "제목"
author: beomine
date: YYYY-MM-DD HH:MM:SS +0900
categories: [대분류, 소분류]
tags: [태그1, 태그2, ...]
---
```

- 파일명: `_posts/YYYY-MM-DD-제목.md`
- 카테고리 예시: `[AI, Trend]`, `[AI, Tool]`, `[Programming, C#]`, `[Programming, JavaScript]`
- 본문은 `##`(h2)부터 시작, `---`로 섹션 구분
- 문체: ~다 체 (경어체 아님)
- 코드 블록, 테이블, 비교표 적극 활용

## Build & Preview

로컬에 Ruby가 직접 설치되어 있지 않고 **rbenv**를 사용한다. 빌드/서버 명령 앞에 반드시 rbenv 초기화를 붙일 것:

```bash
export PATH="$HOME/.rbenv/bin:$HOME/.rbenv/shims:$PATH" && eval "$(rbenv init - bash)"
```

- **빌드**: `bundle exec jekyll build --future 2>&1 | grep -E "(Error|done in)"`
- **로컬 서버**: `bundle exec jekyll serve --future --host 0.0.0.0 --detach`
- **포스트 확인 URL**: `http://localhost:4000/posts/<포스트-슬러그>/`
- GitHub Pages로 자동 배포 (`.github/workflows/pages-deploy.yml`)

포스트 작성 후 빌드하고 로컬 URL을 사용자에게 제공할 것.

## Important

- 포스트 작성 후 반드시 사용자 확인을 받은 뒤 commit/push
