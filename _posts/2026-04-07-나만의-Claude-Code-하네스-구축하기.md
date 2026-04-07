---
layout: post
title: "나만의 Claude Code 하네스 구축하기 — 실전 설정 가이드"
author: beomine
date: 2026-04-07 02:00:00 +0900
categories: [Guide, AI]
tags: [AI, LLM, Claude Code, Harness Engineering, 설정]
---

> 이 글은 [하네스 엔지니어링 입문](/posts/하네스-엔지니어링-입문/) 후속편입니다. 이전 글에서 개념을 먼저 확인하세요.

## 목표

이 글에서는 **빈 상태에서 실전 하네스를 완성**하는 과정을 단계별로 따라한다. 끝나면 아래 환경이 갖춰진다:

- 프로젝트를 자동으로 이해하는 `CLAUDE.md`
- 언어별 코딩 규칙 (`rules/`)
- 파일 수정 시 자동 포맷팅 + 린트 (`hooks`)
- 코드 리뷰, 보안 검사를 자동으로 수행하는 에이전트 (`agents/`)
- 슬래시 명령어로 반복 작업을 즉시 실행하는 스킬 (`skills/`)

---

## Step 1: 디렉토리 구조 만들기

먼저 Claude Code의 설정 디렉토리를 생성한다.

```bash
mkdir -p ~/.claude/rules/common
mkdir -p ~/.claude/agents
mkdir -p ~/.claude/skills
mkdir -p ~/.claude/memory
```

최종 구조는 이렇게 된다:

```
~/.claude/
├── settings.json       # 전역 설정
├── CLAUDE.md           # 전역 컨텍스트 (선택)
├── rules/
│   └── common/         # 공통 규칙
├── agents/             # 커스텀 에이전트
├── skills/             # 커스텀 스킬
└── memory/             # 세션 간 기억 저장소
```

---

## Step 2: CLAUDE.md 작성

프로젝트 루트에 `CLAUDE.md`를 만든다. 이것 하나로 Claude Code의 이해도가 극적으로 올라간다.

### 템플릿

```markdown
# CLAUDE.md

## 프로젝트 개요
[프로젝트 한 줄 설명]

## 기술 스택
- Language: [언어]
- Framework: [프레임워크]
- DB: [데이터베이스]
- 패키지 매니저: [npm/pnpm/yarn/pip 등]

## 주요 명령어
- `[dev 명령어]` : 개발 서버
- `[build 명령어]` : 프로덕션 빌드
- `[test 명령어]` : 테스트 실행
- `[lint 명령어]` : 린트 검사

## 코딩 컨벤션
- [규칙 1]
- [규칙 2]
- [규칙 3]

## 디렉토리 구조
- `src/` : 소스 코드
- `tests/` : 테스트
- `docs/` : 문서
```

### 실전 예시: Next.js 프로젝트

```markdown
# CLAUDE.md

## 프로젝트 개요
사내 업무 관리 시스템 (Next.js 14 기반)

## 기술 스택
- Language: TypeScript (strict 모드)
- Framework: Next.js 14 (App Router)
- DB: PostgreSQL + Prisma ORM
- 상태 관리: Zustand
- 스타일: TailwindCSS
- 패키지 매니저: pnpm

## 주요 명령어
- `pnpm dev` : 개발 서버 (localhost:3000)
- `pnpm build` : 프로덕션 빌드
- `pnpm test` : vitest 실행
- `pnpm lint` : ESLint 검사
- `pnpm db:push` : Prisma 스키마 반영
- `pnpm db:studio` : Prisma Studio 실행

## 코딩 컨벤션
- 함수형 컴포넌트 + React Server Components 우선
- API Route는 src/app/api/ 하위에 배치
- 에러는 항상 명시적으로 처리
- console.log 커밋 금지

## 디렉토리 구조
- `src/app/` : 페이지, 레이아웃, API 라우트
- `src/components/` : 공유 컴포넌트
- `src/lib/` : 유틸리티, DB 클라이언트
- `src/hooks/` : 커스텀 훅
- `prisma/` : 스키마, 시드, 마이그레이션
```

> **팁**: 처음엔 간단하게 시작하고, 사용하면서 점진적으로 보강하면 된다.

---

## Step 3: 코딩 규칙 정의 (Rules)

`~/.claude/rules/common/` 에 주제별 규칙 파일을 만든다. 전체 프로젝트에 적용되는 기본 원칙이다.

### coding-style.md

```markdown
# 코딩 스타일

## 불변성 원칙
기존 객체를 수정하지 않고, 항상 새 객체를 반환한다.

## 파일 크기
- 함수: 50줄 이하
- 파일: 800줄 이하
- 초과 시 분리 필수

## 에러 처리
- 모든 레벨에서 에러를 명시적으로 처리
- 에러를 절대 무시하지 않음
- UI에는 사용자 친화적 메시지, 서버에는 상세 로그

## 네이밍
- 변수/함수: camelCase
- 컴포넌트/클래스: PascalCase
- 상수: UPPER_SNAKE_CASE
- 파일명: kebab-case 또는 PascalCase (컴포넌트)
```

### security.md

```markdown
# 보안 규칙

## 커밋 전 필수 체크리스트
- [ ] 하드코딩된 시크릿 없음
- [ ] 사용자 입력 전부 검증됨
- [ ] SQL 인젝션 방지 (파라미터화 쿼리)
- [ ] XSS 방지 (HTML 이스케이프)

## 금지 사항
- 소스코드에 API 키, 비밀번호, 토큰 직접 입력
- eval() 사용
- innerHTML에 사용자 입력 직접 삽입
- 에러 메시지에 민감 정보 포함

## 필수 사항
- 환경변수 또는 시크릿 매니저 사용
- 필요한 시크릿이 없으면 시작 시점에서 실패
```

### testing.md

```markdown
# 테스트 규칙

## 최소 커버리지: 80%

## TDD 워크플로우
1. 테스트 먼저 작성 (RED)
2. 테스트 실행 → 실패 확인
3. 최소 구현 (GREEN)
4. 테스트 실행 → 통과 확인
5. 리팩토링 (IMPROVE)

## 테스트 종류
- 단위 테스트: 개별 함수, 유틸리티
- 통합 테스트: API 엔드포인트, DB 연동
- E2E 테스트: 핵심 사용자 흐름
```

### git-workflow.md

```markdown
# Git 워크플로우

## 커밋 메시지 형식
```
<타입>: <설명>
```

타입: feat, fix, refactor, docs, test, chore, perf, ci

## 예시
- `feat: 사용자 프로필 페이지 추가`
- `fix: 로그인 시 토큰 만료 에러 수정`
- `refactor: 인증 미들웨어 구조 개선`
```

---

## Step 4: Hooks 설정

`~/.claude/settings.json`에 자동 실행 훅을 추가한다.

### 기본 추천 구성

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "command": "pnpm prettier --write \"$FILE_PATH\"",
        "description": "파일 수정 후 자동 포맷팅"
      },
      {
        "matcher": "Write|Edit",
        "command": "pnpm eslint --fix \"$FILE_PATH\"",
        "description": "파일 수정 후 린트 자동 수정"
      }
    ],
    "PreToolUse": [
      {
        "matcher": "Write",
        "command": "node -e \"let d='';process.stdin.on('data',c=>d+=c);process.stdin.on('end',()=>{const i=JSON.parse(d);const c=i.tool_input?.content||'';const lines=c.split('\\n').length;if(lines>800){console.error('BLOCKED: '+lines+' lines. 파일을 분리하세요.');process.exit(2)}})\"",
        "description": "800줄 초과 파일 작성 차단"
      }
    ],
    "Stop": [
      {
        "command": "pnpm build",
        "description": "세션 종료 시 빌드 검증"
      }
    ]
  }
}
```

### Hook 실행 순서

```
파일 수정 발생
  ↓
[PostToolUse] Prettier 포맷팅
  ↓
[PostToolUse] ESLint 자동 수정
  ↓
작업 계속...
  ↓
세션 종료
  ↓
[Stop] 최종 빌드 검증
```

### 프로젝트별 Hook

전역이 아닌 특정 프로젝트에만 적용하려면, 프로젝트 루트에 `.claude/settings.json`을 만든다:

```bash
mkdir -p .claude
```

```json
// .claude/settings.json (프로젝트 로컬)
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "command": "cargo fmt -- \"$FILE_PATH\"",
        "description": "Rust 프로젝트 전용 포맷터"
      }
    ]
  }
}
```

---

## Step 5: 커스텀 에이전트 만들기

`~/.claude/agents/`에 마크다운 파일로 에이전트를 정의한다.

### deploy-checker.md — 배포 전 검증 에이전트

```markdown
---
name: deploy-checker
description: 배포 전 빌드, 테스트, 보안을 종합 검증하는 에이전트
tools: [Read, Bash, Grep, Glob]
---

## 검증 순서

1. **빌드 확인**
   - 프로덕션 빌드 실행
   - 빌드 에러 없는지 확인

2. **테스트 확인**
   - 전체 테스트 실행
   - 실패하는 테스트 있으면 보고

3. **보안 확인**
   - 소스코드에서 하드코딩된 시크릿 패턴 검색
   - .env 파일이 .gitignore에 포함되어 있는지 확인

4. **결과 보고**
   - PASS / FAIL 요약
   - 실패 항목별 상세 설명
```

### pr-reviewer.md — PR 리뷰 에이전트

```markdown
---
name: pr-reviewer
description: Pull Request 변경사항을 분석하고 리뷰하는 에이전트
tools: [Read, Bash, Grep, Glob]
---

## 리뷰 프로세스

1. `git diff`로 변경사항 파악
2. 보안 체크리스트 확인
3. 코드 품질 확인:
   - 함수 크기, 파일 크기
   - 에러 처리 누락
   - 네이밍 일관성
4. 테스트 커버리지 확인
5. 심각도별 분류: CRITICAL / HIGH / MEDIUM / LOW
```

---

## Step 6: 커스텀 스킬 만들기

`~/.claude/skills/`에 슬래시 명령어로 실행할 스킬을 정의한다.

### morning-check.md — 아침 프로젝트 상태 점검

```markdown
---
name: morning-check
description: 프로젝트 아침 점검 - 빌드, 테스트, 의존성 상태 확인
user_invocable: true
---

아래 항목을 순서대로 확인하고 결과를 요약 보고한다:

1. `git status`로 커밋되지 않은 변경사항 확인
2. `git log --oneline -5`로 최근 커밋 확인
3. 빌드 실행 및 결과 확인
4. 테스트 실행 및 결과 확인
5. 의존성 보안 취약점 확인 (`npm audit` 또는 `pnpm audit`)
6. 결과를 표로 정리하여 보고
```

사용법: Claude Code에서 `/morning-check` 입력

### quick-refactor.md — 빠른 리팩토링 점검

```markdown
---
name: quick-refactor
description: 현재 파일의 리팩토링 포인트를 빠르게 분석
user_invocable: true
---

현재 열린 파일 또는 지정한 파일에 대해:

1. 50줄 초과 함수 찾기
2. 중복 코드 패턴 탐지
3. 복잡도 높은 조건문 (4단계 이상 중첩) 찾기
4. 사용되지 않는 import, 변수 찾기
5. 개선안을 우선순위별로 제안 (코드 수정은 하지 않음)
```

사용법: `/quick-refactor src/components/Dashboard.tsx`

---

## Step 7: Memory 설정

Claude Code의 메모리 시스템은 세션 간 정보를 유지한다. `~/.claude/projects/<프로젝트>/memory/`에 자동 저장된다.

### 메모리 종류

| 타입 | 용도 | 예시 |
|------|------|------|
| `user` | 사용자 정보 | "시니어 백엔드 개발자, Go 전문" |
| `feedback` | 작업 피드백 | "테스트에서 DB 모킹하지 말 것" |
| `project` | 프로젝트 상황 | "4/10까지 릴리즈 프리즈" |
| `reference` | 외부 참조 | "버그 트래킹은 Linear INGEST 프로젝트" |

### 메모리 파일 형식

```markdown
---
name: db-testing-rule
description: 테스트에서 실제 DB를 사용해야 하는 규칙
type: feedback
---

테스트에서 DB를 모킹하지 말고 실제 DB를 사용할 것.

**Why:** 지난 분기에 모킹된 테스트는 통과했지만 프로덕션 마이그레이션이 실패한 사고가 있었음.

**How to apply:** 통합 테스트에서는 항상 테스트 DB에 연결하여 실행.
```

메모리는 Claude Code가 **자동으로 관련 세션에서 참조**하므로, 같은 설명을 반복할 필요가 없다.

---

## 전체 구조 최종 확인

```
~/.claude/
├── settings.json
│   └── hooks (PostToolUse, PreToolUse, Stop)
│   └── permissions
├── CLAUDE.md (전역, 선택)
├── rules/
│   └── common/
│       ├── coding-style.md
│       ├── security.md
│       ├── testing.md
│       └── git-workflow.md
├── agents/
│   ├── deploy-checker.md
│   └── pr-reviewer.md
├── skills/
│   ├── morning-check.md
│   └── quick-refactor.md
└── memory/
    └── MEMORY.md (인덱스)

프로젝트 루트/
├── CLAUDE.md (프로젝트 전용)
└── .claude/
    └── settings.json (프로젝트 전용 hooks)
```

---

## 점진적 도입 전략

한번에 모든 걸 설정하면 부담스럽다. 아래 순서로 **점진적으로 도입**하는 것을 추천한다.

### Week 1: 기초

- [ ] 프로젝트 `CLAUDE.md` 작성
- [ ] `rules/common/coding-style.md` 추가

### Week 2: 자동화

- [ ] PostToolUse 훅 (포맷팅, 린트) 설정
- [ ] `rules/common/security.md` 추가

### Week 3: 에이전트

- [ ] 자주 쓰는 에이전트 1~2개 정의
- [ ] `rules/common/testing.md` 추가

### Week 4: 스킬 & 메모리

- [ ] 반복 작업을 스킬로 전환
- [ ] 메모리 시스템 활용 시작

---

## 마무리

하네스는 **한번 만들면 계속 진화하는 시스템**이다. 사용하면서 불편한 점을 발견할 때마다 규칙을 추가하고, 반복 작업이 보일 때마다 스킬로 만들면 된다.

핵심은 **완벽하게 시작하지 않아도 된다**는 것이다. `CLAUDE.md` 하나만으로도 시작할 수 있고, 거기서부터 점진적으로 확장하면 된다.

다음 포스팅에서는 **Everything Claude Code(ECC) 프로젝트의 구조를 뜯어보며**, 잘 설계된 하네스의 패턴을 분석해볼 예정이다.

### 시리즈 목차

1. [Claude Code 실전 활용기](/posts/Claude-Code-실전-활용기/) ✅
2. [하네스 엔지니어링 입문](/posts/하네스-엔지니어링-입문/) ✅
3. **나만의 Claude Code 하네스 구축하기** ✅ (현재 글)
4. ECC 프로젝트 분석 (예정)
