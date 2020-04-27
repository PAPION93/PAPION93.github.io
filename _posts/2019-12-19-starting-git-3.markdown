---
layout: post
title: 사용하면서 익힌 Git 정리
subtitle: 간단한 git 사용법을 정리해봅시다.
date: 2019-12-19 00:00:00 +0300
description: # Add post description (optional)
tags: [git] # add tag
categories: [git] # add categories
---

1. gitignore

    > .gitignore 파일에 Github 에 올리고 싶지 않은 파일 및 디렉터리를 지정한다.

2. gitignore가 늦었을 땐? Github 저장소에 있는 데이터 삭제하기

    > gitignore 파일을 등록해도 이미 원격저장소에 올라간 파일들이 지워지진 않는다. 그럴땐 이렇게!

    ```
    $ git rm -r --cached .
    $ git add *
    $ git commit -m "Commit Message"
    $ git push
    ```

    위와 같이 진행하면 Github 저장소에 있는 모든 데이터를 지운 뒤 로컬저장소의 데이터를 다시 추가하는데, 이렇게하면 gitignore에 등록된 파일만 삭제가 된다.  
     `--cached` 옵션이 원격저장소에만 있는 파일을 삭제하는 옵션이다.

3. git add 취소하기

    아래 명령어로 아직 로컬저장소의 `HEAD(마지막 커밋 스냅샷, 다음 커밋의 부모 커밋)` 안에 머물고있는 데이터를 원복시킬 수 있다.

    ```
    $ git reset HEAD [file]
    ```

4. git commit 확인 및 취소하기

    ```
    # commit 목록 확인
    $ git log

    # commit을 취소하고 해당 파일들을 staged 상태로 워킹 디렉터리에서 보존
    $ get reset --soft HEAD^

    # commit을 취소하고 해당 파일들을 unstaged 상태로 워킹 디렉터리에서 보존
    $ get reset --mixed HEAD^
    $ get reset HEAD^

    # commit을 취소하고 해당 파일들을 unstaged 상태로 워킹 디렉터리에서 삭제
    $ get reset --hard HEAD^
    ```

5. commit에 빠뜨린 파일을 commit 하기

    커밋을 했는데 Stage 하는 것을 깜빡하고 빠트린 파일이 있으면 아래와 같이 추가하자.

    ```
    $ git commit -m 'initial commit'
    $ git add forgotten_file
    $ git commit --amend
    ```

6. commit message 변경하기

    ```
    git commit --amend -m "커밋 메세지"
    ```

7. Git flow

    1. `git flow init`
    1. `git flow feature start MYFEATURE`
        > 새로운 기능 개발 시작
    1. `git flow feature publish MYFEATURE`
        > 작업 중인 feature를 원격 저장소에 게시한다.
    1. `git flow feature pull origin MYFEATURE`
        > The command 'git flow feature pull' will be deprecated per version 2.0.0. Use 'git flow feature track' instead.
        > 원격저장소의 feature를 가져온다.
    1. `git flow feature finish MYFEATURE`
        > 기능 개발 완료.  
        > 자동으로 develop에 병합되며 feature를 삭제한다.
