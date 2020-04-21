---
layout: post
title: Git 첫 PR 과정 기록
subtitle: 처음 Pull Request 해본 후기
date: 2019-12-05 00:00:00 +0300
description: # Add post description (optional)
tags: [git] # add tag
categories: [git] # add categories
---

이번에 동료들과 같이 해볼 스터디 환경에 제일 필요한 것 Pull Request. Git 환경 구성하면서 시행착오가 좀 있었는데 포스팅해보겠다.  
친한 대리님이 PR을 관련해 말씀해주셔도 감이 오질않았는데, 다른 블로그의 후기 중 아래 대사를 보고 확 와닿았다.

`'회사 기술블로그 repository를 포크떠서 후기 작성하시고 저한테 PR 보내주세요.'`  
[git 초보를 위한 풀리퀘스트(pull request) 방법](https://wayhome25.github.io/git/2017/07/08/git-first-pull-request-story/)

상황파악이 되다보니 이해가 되면서도, 다른 사람들은 재밌게 일하는 것 같단 생각이 들었다. 이 기능을 사용하는 환경에서는 코드리뷰는 물론, 어떻게 협업하는 환경일지 궁금하다. 여튼 모르는 사람들도 저 대사를 보면 이해가 확 되지 않을까..

1.  Fork

    타겟이 되는 프로젝트를 일단 Fork 해오자. 내 github에 정상적으로 Fork되면 다음 진행.

2.  Clone, remote, branch

    -   내 로컬저장소에 Fork 해온 저장소를 로컬 저장소로 Clone 하자.

        ```
        git clone https://github.com/내계정/프로젝트명
        ```

    -   모든 데이터들은 fork 해온 내 저장소로 올리고 난 뒤에 원본저장소로 Pull Request 해야하기 때문에 아래와 같이 내 원격저장소로 remote를 지정한다.

        ```
        git remote add 별명 https://github.com/내계정/프로젝트명
        ```

    -   branch 생성과 동시에 해당 branch로 진행.

        ```
        git checkout -b 브랜치명
        ```

3.  소스 수정 후 add, commit, push

        git add *
        git commit -m "Commit Message"
        git push <remote 별명> <브랜치명>

4.  Pull Request

    -   push가 정상적으로 진행된 후 내 github로 접속해보면 `Compare & pull reqeust` 버튼이 활성화 되어있다.

5.  원본 저장소의 관리자는 내가 보낸 `pull request`를 보고 `Merge` 여부를 결정하면 된다.

6.  Merge 후 동기화 및 branch 삭제

    -   Merge가 확정되면 내 로컬로 소스 동기화`(git pull <remote 별명>)`를 진행한 뒤, `git branch -d <브랜치명>`으로 브랜치 삭제. 추가 작업시 branch 생성부터 반복 작업.

### 재밌다!! Git을 열심히 배우자!!
