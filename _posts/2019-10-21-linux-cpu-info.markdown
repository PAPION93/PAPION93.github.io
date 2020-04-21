---
layout: post
title: Linux CPU Info 정리
subtitle: Linux CPU Info 명령어 정리
date: 2019-10-13 00:00:00 +0300
description: # Add post description (optional)
tags: [linux] # add tag
categories: [linux] # add categories
---

#### 1. CPU 정보 확인

    cat /proc/cpuinfo

#### 2. CPU 코어 전체 개수 확인(가상cpu 존재시 포함)

    grep -c processor /proc/cpuinfo

#### 3. 물리 CPU 수 확인

    grep "physical id" /proc/cpuinfo | sort -u | wc -l

#### 4. CPU당 물리 코어 수 확인

    grep "cpu cores" /proc/cpuinfo | tail -1

#### 5. 하이퍼스레딩 확인

    dmidecode -t processor | egrep 'Core Count|Thread Count' | head -2

#### CPU 코어 전체 개수 = 물리CPU x CPU당 물리 코어 (x 2 하이퍼스레딩)
