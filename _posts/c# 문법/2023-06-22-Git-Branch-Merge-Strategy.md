---
layout: post
title: Git Branch Merge Strategy
author: beomine
date: 2023-06-22 16:58:00 +0900
categories: [Study, git]
tags: [git, branch, merge, fast-forward, rebase, squash]
---

## Git Branch Merge Strategy
1. 3 Way Merge  
    **특징**
    - Merge 시 새로운 Commit이 생성 됨  
    - 중요한 Branch (feature/dev) merge 시 사용

    **단점**
    - git log 출력 시(history) 복잡해 보임
2. Fast-forward Merge   
   **특징**
    - 선형적인 History 유지   
    - branch는 땄지만 메인 브랜치에 변경 사항이 없이 Merge 하고자 할 때
    - 자동으로 Fast-Forward merge 하는 게 싫을 때 : `git merge --no-ff branchX`
3. Rebase & Fast-forward Merge  
   **특징**
   - Semi-linear History 유지  
   - FastForward가 불가능할 때 Rebase후 Fast-Forward Merge(강제)
   - 간단한 브랜치 merge 시 유용   
   
   **단점** 
   - Rebase시 충돌이 많이 발생할 수 있음. 해결 귀찮음.
  
    **방법** 
    ``` bash
    git switch branchX
    git rebase main
    git switch main
    git merge branchX
   ```
4. Squash & Merge  
   **특징**
   - 선을 끊는 것  
   - 여러 커밋을 하나로 합쳐 메인 브랜치 다음에 붙여 줌(텔레포트)
   - 중요하지 않은 브랜치 Merge 할 때 좋음
  
    **방법** 
   ``` bash
    git merge --squash branchX
   ```

### 브랜치 삭제
병합이 완료된 브랜치 삭제 : `git branch -d branchX`   
병합하지 않은 브랜치 삭제 : `git branch -D branchX`