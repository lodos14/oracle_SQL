# oracle_SQL

## 1. SQL
DBMS(데이터베이스 관리 시스템)에게 질의하는 명령어

## 2. oracle 설치

### 2.1 Oracle

구글에 oracle express edition 입력 후 페이지로 들어가서 다운 

express edition 같은 경우 현재 oracle 21c 버전만 제공

### 2.2 oracle 클라이언트

1. sqlplus - 오라클 설치 시 기본 제공
2. SQL Developer - 윈도우 환경의 클라이언트 도구(밑에 페이지에서 설치)<br>
https://www.oracle.com/tools/downloads/sqldev-downloads.html

### 2.3 데이터베이스 새로 만들기
#### CDB - 포괄하는 컨테이너 데이터베이스
![오라클새로만들기](https://user-images.githubusercontent.com/81665608/138702205-56903f36-f988-4d08-a3d7-e85441746a82.png)

    C:\app\사용자이름\product\21c\oradata\XE\SYSTEM01.DBF -> CDB 위치
    
#### PDB - CDB안에 작은 데이터 베이스

서비스 이름을 체크 후 xepdb1 입력 후 접속하면 PDB 생성

    C:\app\사용자이름\product\21c\oradata\XE\XEPDB1\SYSTEM01  -> PDB 위치


## 3. 수업용 사용자와 데이터베이스 생성하기

오라클을 설치하면 계정이 2개가 있다. admin계정, 쿼리를 연습할 수 있는 샘플 데이터 베이스와 계정

### 3.1 데이터베이스와 데이터 파일 그리고 테이블 스페이스

- 데이터베이스라는 하나의 범주 안에 여러 파일이 있다.
- 내가 관리하고자 하는 데이터를 테이블 형태로 관리 -> 스키마(SCHEMA)

### 3.2 테이블스페이스 생성

1. 테이블 스페이스 유형 : 영구
- 보기 -> DBA클릭 -> 왼쪽 하단에 DBA에서 원하는 서버 추가 -> 저장영역 -> 테이블 스레이스 오른쪽 클릭  -> 새로만들기
2. 테이블 스페이스 유형 : 임시 (영구적이지 않은 임시적으로 사용하는 공간)

### 3.3 사용자 계정 만들기

DBA -> 보안 -> 사용자 오른쪽 클릭 -> 새로 만들기 -> 사용자 이름 및 비밀번호 -> 기본 (영구 테이블) 선택 -> 임시 (임시 테이블) 선택 <br>

시스템 권한 -> 수업에서는 모두 체크 후  SYSRAC, SYSOPER, SYSKM 이 세개만 체크 해제함 -> 사용자 계정으로 서버 접속

## 4. SQL의 구분

1. DDL : CREATE / ALTER / DROP (내가 관리시스템에게 생각하는 데이터 구조를 이야기 하는 것 )
2. DML : INSERT / SELECT / UPDATE / DELETE - CRUD (데이터를 조작)
3. DCL : GRANT / REVOKE (권한관리)

### 4.1 테이블 생성하기(Create)

    CREATE TABLE MEMBER
    (
        ID          VARCHAR2(50),
        PWD         VARCHAR2(50),
        NAME        VARCHAR2(50),
        GENDER      VARCHAR2(50),
        AGE         NUMBER,
        BIRTHDAY    VARCHAR2(50),
        PHONE       VARCHAR2(50),
        REGDATE     DATE
    );

## 5. Oracle 데이터 타입

#### Oracle Built-in Data Types - 기본 내재된 타입

- Character : 'nEWlec', 'A', '148'
- Numeric : 38, 3.85, 3.85F, 137
- Date : '2013-02-09'
- TIMESTAMP : '2013-06-03 10.38.29.00000' 

### 5.1 Character 형식
MAX SIZE<br>
- 32767 bytes - EXTENDED
- 4000 bytes - STANDARD

- CHAR : 10개의 글자를 저장하는 속성이 필요하다 -> CHAR(10) , 단점은 공간을 정해 놓고 사용해서 낭비가 있음
- VARCHAR2 : VARCHAR2(10) -> 10개의 공간을 정하는게 아니라 최대 10개의 공간만 사용하겠다. 단점은 구분자를 기록하기 때문에 데이터를 찾으려면 구분자를 세야해서 검색이 오래걸림
- NCHAR : 여러 언어의 문자가 한번에 들어올 경우 size => 2 or 3 bytes
- NVARCHAR2 : 여러 언어 가변 길이 

#### 형식에 맞게 다시 테이블 생성

    CREATE TABLE MEMBER
    (
        ID          VARCHAR2(50),
        PWD         VARCHAR2(50),
        NAME        VARCHAR2(50),
        GENDER      NCHAR(2), -- 남성, 여성
        AGE         NUMBER,
        BIRTHDAY    CHAR(10),
        PHONE       CHAR(13), 
        REGDATE     DATE
    );

#### 대용량 컨텐츠를 표현하는 형식 
- LONG : 요즘은 LONG보단 CLOB 사용
- CLOB : 대용량 텍스트 데이터 타입(최대 4Gbyte)
- NCLOB : 대용령 텍스트 유니코드 데이터 타입(최대 4Gbyte)

### 5.2 Numberic 형식

- NUMBER(4) : 최대 4자리로 이루어진 숫자
- NUMBER(6,2) : 소수점 2자리를 포함하는 최대 6자리의 숫자(소수점 둘째 자리에서 반올림)
- NUMBER(6,-2) : 소소점 -2자리에서 반올림하는 최대 6자리의 숫자
- NUMBER : NUMBER(38,*)
- NUMBER(*,5) : NUMBER(38,5)


## 6. 테이블 수정(Alter)

테이블을 수정하기 위해서는 수정하고자 하는 타입과 현재 저장된 데이터의 형태가 적절해야 한다.<br>
예를 들어 저장된 데이터의 길이가 50인데 데이터 타입 크기를 10으로 줄일 수 없다. 또는 저장된 데이터가 CHAR이면 NUMBER로 바꿀 수 없다.

    ALTER TABLE MEMBER MODIFY ID NVARCHAR2(50); -- 수정 사항이 있을 때 MODIFY
    AlTER TABLE MEMBER DROP COLUMN AGE; -- 컬럼 삭제는 DROP
    ALTER TABLE MEMBER ADD EMAIL VARCHAR2(200); -- 컬럼 추가는 ADD
    
근데 툴이 있다면 편집 기능을 이용해서 명령어를 사용하지 않아도 수정 가능


## 7. SQL 쿼리 연습

### 7.1 테이블 준비

    CREATE TABLE NOTICE
    (
        ID              NUMBER,
        TITEL           NVARCHAR2(100),
        WRITER_ID       NVARCHAR2(50),
        CONTENT         CLOB,
        REGDATE         TIMESTAMP,
        HIT             NUMBER,
        FILES           NVARCHAR2(1000)
    );

    CREATE TABLE "COMMENT" -- 예약어 같은 경우 큰 따움표로 감싸면 사용 가능
    (
        ID          NUMBER,
        CONTENT     NVARCHAR2(2000),
        REGDATE     TIMESTAMP,
        WRITER_ID   NVARCHAR2(50),
        NOTICE_ID   NUMBER
    );

    CREATE TABLE ROLE
    (
        ID              VARCHAR2(50),
        DISCRIPTION     NVARCHAR2(500)
    );

    CREATE TABLE MEMBER_ROLE
    (
        MEMBER_ID       NVARCHAR2(50),
        ROLE_ID         NVARCHAR2(50)
    );

### 7.2 Insert, Select

![image](https://user-images.githubusercontent.com/81665608/138843112-510a1059-94c0-43e8-bfce-d0dc0237409c.png)

값이 입력이 안된 곳은 null 값이 들어감
SELECT * FROM MEMBER -> 테이블의 모든 컬럼 불러오기

    INSERT INTO MEMBER(ID, PWD) VALUES('newlec', '111');
    INSERT INTO MEMBER(ID, PWD) VALUES('dragon', '111');
    SELECT id , name, pwd FROM MEMBER; -- 예약어나 컬럼명은 소대문자를 안가림
    SELECT id as user_id , name, pwd FROM MEMBER; -- 별칭을 통해 사용자가 원하는 명으로 사용 가능(as 생략가능)
    SELECT id  "user id" , name, pwd FROM MEMBER; -- (as 생략가능) 큰따움표로 감싸면 있는 그대로 출력

### 7.3 Update, Delete

    UPDATE MEMBER SET PWD ='222'; -- 모든 행의 특정 컬럼의 값이 바뀜
    
    //특정 사용자의 값만 바꾸려면 WHERE 컬럼을 해줘야한다.
    UPDATE MEMBER SET PWD = '111' WHERE ID = 'dragon';
    // 여러 컬럼의 값을 바꾸는 경우
    UPDATE MEMBER SET PWD = '333', name='손오공' WHERE ID = 'dragon'; 
    // 삭제하는 경우도 마찬가지로 WHERE를 하지 않으면 전부 삭제됨 주의
    DELETE MEMBER WHERE ID = 'test'; -- 삭제하는 경우
