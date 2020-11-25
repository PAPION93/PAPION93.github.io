---
layout: post
title: 서비스중인 테이블의 데이터를 서비스 중단을 최소화하여 변경하기
subtitle: 
date: 2020-01-07 00:00:00 +0300
description: # Add post description (optional)
tags: [database, mysql]
categories: [database, mysql]
---

지속적으로 생성, 수정, 삭제되는 테이블의 암호화데이터를 복호화하여 수정해야하는데, 최대한 서비스 중단 없이 진행하고 싶었습니다.

### 데이터 변경 방법 고민

1. 원본 테이블에 칼럼을 추가하여 복호화한 데이터를 넣고, 칼럼명을 수정한다.  
    - Table Lock 으로 인해 제외  
    - Online DDL 을 지원하지 않는 Mysql 5.5 버전이였습니다.
    - **Percona pt-online-schema-change** 를 사용하면 online ddl 이 가능합니다. 이때는 미처 알지 못하였습니다..
    - [Percona pt-online-schema-change 설치 및 사용하기](https://jojoldu.tistory.com/358)

2. 임시테이블을 생성해서 원본데이터를 옮겨가며 복호화 진행한다.
   - 신규 이벤트를 어떻게 반영해줄지 고민...

3. 임시테이블 생성 후 원본데이터를 복호화하면서 Insert한다.
   - 신규데이터는 Trigger를 통해 로그를 남긴다.
   - 로그를 기반으로 변동 데이터를 업데이트 해준다.

위와 같이 고민해보았고, 최종으로 3번과 같이 진행하기로 하였습니다. 그전에 준비물을 미리 정리하면

- 원본데이터를 복호화하면서 임시테이블에 저장하는 일회용 스크립트
- 원본테이블의 변경을 감지하여 Log를 남기는 Trigger
- Trigger를 통해 저장되는 Log를 기반으로 복호화 테이블로 반영해주는 데몬

총 3가지가 필요하고 차례대로 진행해보겠습니다.

#### 1. Log를 저장하는 Trigger 실행

- Log 테이블 생성  
- Insert, Update, Delete 발생시 Log 테이블로 원본테이블의 고유번호와 이벤트 종류(insert, update, delete)를 Insert 한다.  
- Desc Log  
    ![desc_logtable.jpg](https://papion93.github.io/img/desc_logtable.jpg)

- Trigger  

    ```mysql
    DELIMITER $$

    CREATE
        TRIGGER `원본테이블_after_insert` AFTER INSERT ON `원본테이블`
        FOR EACH ROW BEGIN
    INSERT INTO 로그테이블 SET num = NEW.num, dml_status = 'insert' ;
    END;
    $$

    CREATE
        TRIGGER `원본테이블_after_update` AFTER UPDATE ON `원본테이블`
        FOR EACH ROW BEGIN
    INSERT INTO 로그테이블 SET num = NEW.num, dml_status = 'update' ;
    END;
    $$

    CREATE
        TRIGGER `원본테이블_after_delete` BEFORE DELETE ON `원본테이블`
        FOR EACH ROW BEGIN
    INSERT INTO 로그테이블 SET num = old.num, dml_status = 'delete' ;
    END;
    $$

    DELIMITER ;
    ```

#### 2. 복호화하며 데이터를 저장하는 스크립트 실행

- 복호화데이터를 저장할 테이블 생성
- 이때 Log테이블에 `dml_status`가 `insert`인 최초 `num`을 확인합니다.
- 복호화하며 데이터를 저장할 스크립트를 실행하여, `num - 1` 까지 복호화테이블로 원본데이터를 복호화하며 `Insert`를 진행합니다. 저장된 `num`부터는 아래 데몬에서 시작합니다.

#### 3. Log 테이블에 저장한 이벤트를 복호화테이블로 반영하는 데몬 실행

- 위의 스크립트가 종료되면 데몬을 실행하는데, 이 데몬은 Trigger를 통해 Log 테이블에 작성되고 있는 이벤트를 복호화테이블에 순차적으로 적용하도록 합니다.
- 데몬은 Log테이블 Select -> 원본테이블 Select -> 복호화 -> 복호화테이블에 반영
- Insert 후 Delete 되어 원본데이터를 Select할 때 없는 경우를 예외처리해줍니다.
- 복호화테이블에 제대로 복호화되고 있는지 데이터를 확인합니다.

### 배포

- 복호화된 데이터를 처리하도록 변경된 소스를 배포 해야합니다. 이때는 잠시 동안 해당테이블의 데이터 변경사항을 막아둡니다.
- 데몬이 Log 테이블을 통해 변경되는 정보를 실시간 반영중이기 때문에 금방 완료됩니다.
- Log테이블의 데이터가 모두 반영되었는지 확인하고, Trigger 삭제 및 데몬을 중지합니다.
- 마지막으로 복호화 테이블을 원본테이블로, 그리고 원본 테이블은 백업용으로 이름을 변경하고 다시 서비스를 시작합니다.

### 마무리

이렇게 진행해서 중단시간은 3~5분정도 소요되었습니다. 배포과정을 조금더 자동화 하였으면 더 짧은 중단시간을 가질 수 있을거라 생각됩니다. 하지만 무중단 배포가 최선인만큼 다음에는 무중단으로 진행해보겠습니다. 대단한 방법도 아니고, 무식한 방법이였지만 삽질로그를 남겨보았습니다.
감사합니다.
