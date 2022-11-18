---
layout: post
title:  "Github blog theme 적용 (chirpy) for window"
date:   2022-11-18 15:39:33 +0900
categories: [Study, Electron]
tags: [github, blog, window, chirpy, theme]
---

### **1.원하는 테마 git zip으로 다운로드**
   원하는 테마 git 주소로 들어가서 zip으로 다운받아 압축 해제한다.
    
   [chirpy theme git 주소]https://github.com/cotes2020/jekyll-theme-chirpy 
   
   ![git_zip_download](https://user-images.githubusercontent.com/118417247/202658890-20e7a5fa-6127-44a3-9a54-c9203cb08251.png)
     
### **2. 로컬에 저장된 레파지토리에 복사**  
   로컬에 저장된 레파지토리(user_name.github.io)에 압축 해제한 폴더 안 파일을 전부 복붙.
     
     이때, 로컬 레파지토리에는 어떤 파일도 없어야한다.
     (숨김 파일 .git 폴더 빼고)
  
### **3. chirpy theme 같은 경우 init 작업이 필요함**
  안타깝게도 윈도우에선 bash 명령어 
  `bash tools/init.sh` 를 쓸 수 없기 때문에 수동으로 작업해준다.

  - .travis.yml 파일 삭제
  - .github 폴더 내 workflows 폴더를 제외한 나머지 폴더 및 파일 총 6개 삭제 (ISSUE_TEMPLATE 폴더, CODE_OF_CONDUCT, CONTRIBUTING, FUNDING, PULL_REQUEST_TEMPLATE, stale 파일들)
  - .github 폴더 안 workflows 폴더 하위 파일 2개 삭제 (ci.yml, issue-interceptor)
  - .github>workflows 폴더 안 pages-deploy.yml.hook 파일 이름을 .hook 확장자 지워서 pages-deploy.yml로 변경
  - (선택) _posts 하위 파일들 삭제 (sample이기 때문에 굳이 삭제 안해도 됨 나중에 포스팅할때 참고해서 쓰기에 좋음)
     
### **4. _config.yml 파일 수정**  
 - [**중요**] url에 자신의 git blog 주소를 넣는다 ('https://beomine.github.io')
 - timezone: Asia/Shanghai -> Asia/Seoul 로 변경
 - title : 자신의 블로그 타이틀
 - tagline : 왼쪽 사이드 바 타이틀 바로 아래에 올 detail
 - github>username : 자신의 github username
 - social : 이름, 이메일, github 주소 등등...
 
이정도만 하면 기본 설정은 끝난다. 아래는 예시

```yml
# Change to your timezone › http://www.timezoneconverter.com/cgi-bin/findzone/findzone
timezone: Asia/Seoul

# jekyll-seo-tag settings › https://github.com/jekyll/jekyll-seo-tag/blob/master/docs/usage.md
# ↓ --------------------------

title: Beomine                          # the main title

tagline: for study   # it will display as the sub-title

description: >-                        # used by seo meta and the atom feed
A minimal, responsive, and powerful Jekyll theme for presenting professional writing.

# fill in the protocol & hostname for your site, e.g., 'https://username.github.io'
url: 'https://beomine.github.io'

github:
username: beomine             # change to your github username

twitter:
username: twitter_username            # change to your twitter username

social:
# Change to your full name.
# It will be displayed as the default author of the posts and the copyright owner in the Footer
name: sarah
email: rlaqjaals01@gmail.com             # change to your email address
links:
   # The first element serves as the copyright owner's link
   # - https://twitter.com/username      # change to your twitter homepage
   - https://github.com/beomine       # change to your github homepage
   # Uncomment below to add more social links
   # - https://www.facebook.com/username
   # - https://www.linkedin.com/in/username

   ```

### **5. Push 하기 전 local test**

``` bash
bundle install
bundle exec jekyll serve
```
마지막 명령어를 실행하면 지킬 서버가 내 컴퓨터에서 실행된다.
해당 주소로 들어가 화면을 확인해본다.

### **6. git에 올리기**

``` bash
git add .
git commit -m "download chirpy theme"
git push
```
git에 올려주면 해당 테마가 적용된 걸 볼 수 있다.


