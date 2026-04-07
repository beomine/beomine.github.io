---
layout: post
title: "Everything Claude Code 프로젝트 분석 — 잘 만든 하네스의 해부"
author: beomine
date: 2026-04-07 03:00:00 +0900
categories: [AI]
tags: [AI, LLM, Claude Code, Harness Engineering, ECC, 오픈소스]
---

> 이 글은 시리즈의 마지막 편입니다. 이전 글을 먼저 읽으시면 이해가 쉽습니다.
> 1. [Claude Code 실전 활용기](/posts/Claude-Code-실전-활용기/)
> 2. [하네스 엔지니어링 입문](/posts/하네스-엔지니어링-입문/)
> 3. [나만의 Claude Code 하네스 구축하기](/posts/나만의-Claude-Code-하네스-구축하기/)

## Everything Claude Code(ECC)란?

**Everything Claude Code(ECC)**는 Claude Code의 하네스 엔지니어링을 **종합적으로 구현한 오픈소스 프로젝트**다. Rules, Hooks, Agents, Skills를 체계적으로 정리해 놓은 일종의 **하네스 프레임워크**라고 볼 수 있다.

단순히 설정 파일 모음이 아니라, **왜 이렇게 구성했는지에 대한 설계 철학**이 담겨 있다. 이 글에서는 ECC의 구조를 뜯어보며, 잘 설계된 하네스의 패턴을 분석한다.

---

## 전체 구조 개요

```
ECC/
├── rules/                    # 코딩 규칙 (계층형)
│   ├── common/               # 언어 무관 공통 원칙
│   │   ├── coding-style.md
│   │   ├── security.md
│   │   ├── testing.md
│   │   ├── performance.md
│   │   ├── patterns.md
│   │   ├── hooks.md
│   │   ├── agents.md
│   │   ├── code-review.md
│   │   ├── git-workflow.md
│   │   └── development-workflow.md
│   ├── typescript/           # TypeScript 전용 확장
│   ├── python/               # Python 전용 확장
│   ├── golang/               # Go 전용 확장
│   ├── web/                  # 웹 프론트엔드 전용
│   ├── swift/                # Swift 전용
│   └── php/                  # PHP 전용
├── agents/                   # 서브 에이전트 정의
├── skills/                   # 슬래시 명령어 스킬
└── install.sh                # 설치 스크립트
```

핵심 설계 원칙은 **계층형 상속 구조**다.

---

## 패턴 1: 계층형 규칙 상속

ECC의 가장 돋보이는 설계는 **common → language-specific** 계층 구조다.

### 구조

```
rules/common/coding-style.md     ← 모든 언어에 적용되는 기본 원칙
rules/typescript/coding-style.md ← TypeScript 전용 확장 (common을 상속)
rules/python/coding-style.md     ← Python 전용 확장 (common을 상속)
```

### common/coding-style.md (공통)

```markdown
# 코딩 스타일

## 불변성 원칙
기존 객체를 수정하지 않고, 항상 새 객체를 반환한다.

> **언어 참고**: 이 규칙은 언어별 관용에 따라 재정의될 수 있다.
```

### golang/coding-style.md (Go 전용)

```markdown
> 이 파일은 [common/coding-style.md](../common/coding-style.md)를 Go에 맞게 확장합니다.

# Go 코딩 스타일

## 불변성 원칙 (재정의)
Go에서는 포인터 리시버로 구조체를 변경하는 것이 관용적이다.
common의 불변성 원칙보다 Go 관용을 우선한다.
```

### 이 패턴이 좋은 이유

| 장점 | 설명 |
|------|------|
| **중복 제거** | 공통 원칙을 한 곳에서 관리 |
| **유연한 재정의** | 언어별 관용이 다를 때 쉽게 오버라이드 |
| **명확한 우선순위** | 언어 전용 규칙 > 공통 규칙 (CSS specificity와 동일) |
| **확장 용이** | 새 언어 추가 시 common을 상속하고 차이점만 작성 |

이것은 CSS의 캐스케이드, `.gitignore`의 우선순위와 같은 **레이어드 설정 패턴**이다.

---

## 패턴 2: 주제별 규칙 분리

하나의 거대한 규칙 파일 대신, **주제별로 분리**한다.

```
rules/common/
├── coding-style.md          # 코드 스타일, 네이밍, 파일 크기
├── security.md              # 보안 체크리스트
├── testing.md               # 테스트 전략, TDD, 커버리지
├── performance.md           # 성능 최적화
├── patterns.md              # 디자인 패턴
├── git-workflow.md          # 커밋 규칙, PR 프로세스
├── code-review.md           # 코드 리뷰 기준
├── development-workflow.md  # 전체 개발 워크플로우
├── hooks.md                 # 훅 시스템 가이드
└── agents.md                # 에이전트 사용 가이드
```

### 분리 기준

각 파일은 **하나의 관심사**만 다룬다:

- `security.md`는 보안만
- `testing.md`는 테스트만
- `git-workflow.md`는 Git만

이렇게 하면 Claude Code가 **필요한 규칙만 참조**할 수 있어 컨텍스트를 효율적으로 사용한다.

---

## 패턴 3: 체크리스트 기반 규칙

ECC의 규칙은 단순한 설명이 아닌 **실행 가능한 체크리스트** 형태로 작성된다.

### security.md 예시

```markdown
## 커밋 전 필수 확인

- [ ] 하드코딩된 시크릿 없음 (API 키, 비밀번호, 토큰)
- [ ] 모든 사용자 입력 검증됨
- [ ] SQL 인젝션 방지 (파라미터화 쿼리)
- [ ] XSS 방지 (HTML 이스케이프)
- [ ] CSRF 보호 활성화
- [ ] 인증/인가 검증됨
- [ ] 모든 엔드포인트에 레이트 리밋 설정
- [ ] 에러 메시지에 민감 정보 미포함
```

### code-review.md 예시

```markdown
## 리뷰 심각도

| 레벨 | 의미 | 액션 |
|------|------|------|
| CRITICAL | 보안 취약점, 데이터 손실 | **차단** - 머지 전 필수 수정 |
| HIGH | 버그, 주요 품질 문제 | **경고** - 머지 전 수정 권장 |
| MEDIUM | 유지보수 우려 | **정보** - 수정 고려 |
| LOW | 스타일, 사소한 제안 | **참고** - 선택 |
```

체크리스트 형태의 장점:
- Claude Code가 **하나씩 확인하며 진행**할 수 있다
- 누락 없이 체계적으로 검증 가능
- 사람이 읽어도 바로 이해 가능

---

## 패턴 4: 개발 워크플로우 파이프라인

ECC는 개별 규칙을 **하나의 워크플로우로 연결**한다.

### development-workflow.md의 파이프라인

```
0. 연구 & 재사용
   ├── GitHub 코드 검색
   ├── 라이브러리 문서 확인
   └── 패키지 레지스트리 검색
       ↓
1. 계획 수립
   └── planner 에이전트 사용
       ↓
2. TDD 개발
   ├── 테스트 먼저 작성 (RED)
   ├── 최소 구현 (GREEN)
   └── 리팩토링 (IMPROVE)
       ↓
3. 코드 리뷰
   └── code-reviewer 에이전트 사용
       ↓
4. 커밋 & 푸시
   └── conventional commits 형식
       ↓
5. 배포 전 확인
   └── CI/CD, 머지 충돌, 브랜치 동기화
```

각 단계에서 **어떤 에이전트를 사용하고, 어떤 규칙을 참조하는지**가 명확하게 연결되어 있다.

### 규칙 간 참조

```markdown
# development-workflow.md

이 규칙은 다음과 함께 사용:
- [testing.md](testing.md) - 테스트 커버리지 요구사항
- [security.md](security.md) - 보안 체크리스트
- [git-workflow.md](git-workflow.md) - 커밋 표준
- [agents.md](agents.md) - 에이전트 위임
```

규칙이 **독립적이면서도 유기적으로 연결**되어 있다.

---

## 패턴 5: 에이전트 자동 실행 전략

ECC의 `agents.md`는 에이전트를 **언제 자동으로 호출할지** 명확히 정의한다.

```markdown
## 즉시 사용 (사용자 요청 불필요)

1. 복잡한 기능 요청 → **planner** 에이전트
2. 코드 작성/수정 후 → **code-reviewer** 에이전트
3. 버그 수정 또는 새 기능 → **tdd-guide** 에이전트
4. 아키텍처 결정 → **architect** 에이전트
```

### 보안 트리거

```markdown
## 즉시 중단하고 security-reviewer 사용:

- 인증/인가 코드
- 사용자 입력 처리
- 데이터베이스 쿼리
- 파일 시스템 접근
- 외부 API 호출
- 암호화 처리
- 결제/금융 코드
```

이렇게 **트리거 조건을 명시**해두면, Claude Code가 상황에 맞는 에이전트를 자동으로 호출한다.

---

## 패턴 6: 언어별 확장 전략

새 언어를 추가할 때의 패턴을 살펴보자.

### 추가 절차

1. `rules/<language>/` 디렉토리 생성
2. common을 상속하는 파일 작성:

```markdown
> 이 파일은 [common/coding-style.md](../common/coding-style.md)를
> Rust에 맞게 확장합니다.

# Rust 코딩 스타일

## 소유권 규칙
- 불필요한 clone() 금지
- 가능한 한 참조(&) 사용
- 라이프타임은 명시적으로 표기
```

3. 해당 언어의 도구 체인에 맞는 훅 추가:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "command": "cargo fmt -- $FILE_PATH",
        "description": "Rust 자동 포맷팅"
      }
    ]
  }
}
```

### 현재 지원 언어

| 언어 | 주요 내용 |
|------|-----------|
| TypeScript/JS | ESLint, Prettier, React 패턴, Node.js |
| Python | PEP 8, pytest, 타입 힌트 |
| Go | go fmt, 관용적 Go, 테이블 드리븐 테스트 |
| Web/Frontend | CSP, CWV, 접근성, 반응형 |
| Swift | SwiftLint, iOS/macOS 패턴 |
| PHP | Laravel, PSR-12 |

---

## 패턴 7: 설치 자동화

ECC는 설치를 **한 줄 명령어**로 해결한다.

```bash
# TypeScript 프로젝트
./install.sh typescript

# Python + Web 복합 프로젝트
./install.sh python web

# 모든 언어
./install.sh typescript python golang web swift php
```

### install.sh가 하는 일

1. `~/.claude/rules/` 디렉토리 생성
2. `rules/common/` 복사 (공통 규칙, 항상 포함)
3. 지정한 언어의 `rules/<language>/` 복사
4. 기존 설정과 충돌 확인

### 주의사항

```bash
# 올바른 설치 (디렉토리 단위 복사)
cp -r rules/common ~/.claude/rules/common
cp -r rules/typescript ~/.claude/rules/typescript

# 잘못된 설치 (파일 플래튼 → 덮어쓰기 발생!)
cp rules/common/* ~/.claude/rules/       # ❌
cp rules/typescript/* ~/.claude/rules/   # ❌ common 파일 덮어씀
```

common과 language-specific에 **동일한 파일명**이 있기 때문에, 반드시 **디렉토리 단위로 복사**해야 한다.

---

## ECC에서 배울 수 있는 설계 원칙

### 1. 관심사 분리

하나의 파일이 하나의 주제만 다룬다. 보안, 테스트, 코딩 스타일이 뒤섞이지 않는다.

### 2. 상속과 재정의

공통 원칙을 정의하고, 언어별로 필요한 부분만 재정의한다. DRY 원칙의 설정 파일 버전이다.

### 3. 실행 가능한 형태

"좋은 코드를 작성하라" 같은 추상적 지시가 아닌, "함수 50줄 이하, 파일 800줄 이하" 같은 **구체적이고 검증 가능한 기준**을 제시한다.

### 4. 자동화 우선

규칙을 정의하는 것에 그치지 않고, 훅과 에이전트를 통해 **자동으로 실행되게** 만든다.

### 5. 점진적 도입

모든 것을 한번에 적용하지 않아도 된다. common부터 시작해서 필요할 때 확장한다.

---

## 나만의 ECC 만들기

ECC를 그대로 쓸 수도 있지만, **내 상황에 맞게 커스터마이징**하는 것이 더 효과적이다.

### 커스터마이징 체크리스트

- [ ] 사용하지 않는 언어 규칙 제거
- [ ] 팀 컨벤션에 맞게 규칙 수정
- [ ] 프로젝트 특화 에이전트 추가
- [ ] 자주 하는 작업을 스킬로 등록
- [ ] 훅을 프로젝트 도구 체인에 맞게 조정

### 추천 시작점

```bash
# 1. ECC 설치
./install.sh typescript  # 본인 언어에 맞게

# 2. 불필요한 규칙 정리
# 사용하지 않는 규칙 삭제 또는 수정

# 3. 프로젝트 CLAUDE.md 작성
# 프로젝트 루트에 컨텍스트 파일 추가

# 4. 훅 설정
# settings.json에 자동 포맷팅, 린트 훅 추가

# 5. 사용하면서 개선
# 불편한 점을 발견할 때마다 규칙 추가/수정
```

---

## 마무리

ECC는 단순한 설정 파일 모음이 아니다. **AI 코딩 도구를 체계적으로 제어하는 방법론의 구현체**다.

핵심 takeaway:

1. **계층형 구조**로 공통 원칙과 언어별 확장을 분리하라
2. **체크리스트 형태**로 검증 가능한 규칙을 만들어라
3. **자동화 우선**: 규칙은 정의만 하지 말고 훅과 에이전트로 자동 실행하라
4. **점진적으로 도입**하라. 완벽을 기다리지 마라
5. **커스터마이징**해서 내 것으로 만들어라

하네스 엔지니어링은 아직 초기 단계의 분야다. AI 코딩 도구가 발전할수록 하네스의 중요성은 더 커질 것이다. 지금부터 자신만의 하네스를 구축해보는 것을 추천한다.

### 시리즈 목차

1. [Claude Code 실전 활용기](/posts/Claude-Code-실전-활용기/) ✅
2. [하네스 엔지니어링 입문](/posts/하네스-엔지니어링-입문/) ✅
3. [나만의 Claude Code 하네스 구축하기](/posts/나만의-Claude-Code-하네스-구축하기/) ✅
4. **Everything Claude Code 프로젝트 분석** ✅ (현재 글)
