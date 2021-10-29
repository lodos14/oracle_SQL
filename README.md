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

## 8. 트랜잭션
web_JDBC readme 참고

![image](https://user-images.githubusercontent.com/81665608/138891461-052f682e-cf8b-42c0-a1bd-e66e1cd2a3dd.png)

처음 데이터베이스를 만들 때 세부 스탯 중 영구 와 임시가 있었는데 COMMIT 전에는 임시저장소에서 테스트 되고 COMMIT을 해야 다른 세션에도 반영이 된다. 뒤로 돌아가는 ROLLBACK 도 마찬가지다. <br>

어떤 세션에서 임시로 무언가를 작업중이면 COMMIT 또는 ROLLBACK를 하기 전까지 ROCK이 걸려서 다른 세션에서 작업을 할 수 없다.

## 9. 연산자사용

### 9.1 산술연산자

1. 문자열의 더하기는 ||를 사용한다.
2. 기본적인 산술연산자는 숫자에서만 사용 가능하다.

        select hit+1 from notice; HIT 값에 1이 더한 값이 출력되지만 컬럼명이 HIT + 1 로 출력되므로 별칭을 사용해야 한다.
        select 1+'3' from dual; -- 더미 테이블을 통해 단순 연산 결과 출력 가능 마찬가지로 별칭 사용해야 한다. 
        // 출력결과 : 4         -- 문자열의 타입이 자동으로 숫자로 바뀜
        select 1||'3' from dual; -- 문자열의 더하기는 ||를 사용한다 +는 숫자만 가능
        // 출력결과 : 13
        select name||'('||id||')' "user" from member;
        // 출력결과 : 손오공(dragon)

### 9.2 비교연산자

![image](https://user-images.githubusercontent.com/81665608/139009301-cdaa7903-d813-40f1-9a74-47a542e3dd94.png)

### 9.3 관계연산자

![image](https://user-images.githubusercontent.com/81665608/139011083-1f295d75-6e1c-42a5-a305-c582dd0e25a5.png)

![image](https://user-images.githubusercontent.com/81665608/139011421-6c0b609a-2713-4ed5-821f-5c745e966e2e.png)

### 9.4 패턴 연산자

1. 패턴 연산자를 사용할 경우 = 대신에 LIKE를 사용한다.
2. %는 무엇이 와도 상관 없다는 뜻
3. _ 는 자리를 뜻한다. 

![image](https://user-images.githubusercontent.com/81665608/139024939-2c2197df-8d86-428d-8fa5-d8f6bcb3db2d.png)

![image](https://user-images.githubusercontent.com/81665608/139027048-8b20164e-baef-4425-935b-0a6a3a72b0b4.png)

### 9.5 정규식을 이용한 패턴비교

#### 숫자를 정교하게 검색하는 예시

    // ex) 전화번호
    
    [] : 하나의 자리를 뜻함
    [016-9] : 0, 1, 6, 7, 8, 9 중 하나가 들어간다.
    \d : [0-9] 와 같은 의미
    {} : 자리수를 뜻함 {4}는 4자리 {3,4}는 3자리 또는 4자리
    \d{3, 4} : 0에서 9까지의 숫자가 들어간 3자리 또는 4자리
    
    전화번호는 다음과 같이 정규식으로 나타낼 수 있다.
    
    01[016-9]-\d{3,4}-\d{4}


정규식을 사용할 경우 REGEXP_LIKE 함수를 사용해야한다.

![image](https://user-images.githubusercontent.com/81665608/139033972-bad4695e-a211-4cf0-807a-ee22777f0603.png)

#### 자신이 만든 정규식 테스트 하는 사이트 https://regexlib.com/RETester.aspx

#### 문자열을 비교하기 위한 정규식

    // ex) 이메일

    \w : 영대소문자, 숫자 [a-zA-Z_0-9] 와 같다 _ 포함
    \D : \d의 부정으로 숫자가 올 수 없다. [^0-9] 와 같은 의미
    | : OR의 의미
    + : 1개 이상

    이메일은 다음과 같이 정규식으로 나타낼 수 있다.

    \D\w+@\D\w+.(org|net|com)

더 많은 식은 찾아볼것<br>
https://regexlib.com/Search.aspx 에서 원하는 정규식을 찾아도 됨

## 10. 행 제한

### 10.1 Rownum 

ROWNUM은 결과 집합이 만들어질 때 붙는 행의 번호이다.<br>

아래 사진과 같이 ROWNUM >= 1 을 사용할 경우 결과 집합이 만들어 지면서 1부터 ROWNUM가 붙기 때문에 상관없지만 <br>

ROWNUM >= 3 를 하면 아무것도 나오지 않는다. <br>

그 이유는 결과 집합이 만들어질 때 ROWNUM 1부터 채워지기 때문에 3이상이라는 조건에 만족하지 않아 전부 빼버리게 된다. 

![image](https://user-images.githubusercontent.com/81665608/139040206-6e295c0f-d794-4775-b1d1-5013ee106122.png)

그래서 ROWNUM을 이용해서 행을 제한하려면 ROWNUM 컬럼을 만들어주고 사용해야 한다. <br>
    
    SELECT ROWNUM FROM NOTICE  
    // ROWNUM의 컬럼을 생성하고 1부터 순서대로 값이 생성된다.
    
위 코드를 이용하면

    SELECT ROWNUM, NOTICE.* FROM NOTICE
    // ROWNUM 과 NOTICE안의 모든 컬럼을 불러온다. NOTICE.* 와 * 는 차이가 있다. (SELECT NOTICE.ID FROM NOTICE)
    
이 결과 집합을 테이블로 삼아 아래와 같이 작성이 가능하지만

    SELECT * FROM (SELECT ROWNUM, NOTICE.* FROM NOTICE ) WHERE ROWNUM >= 3
    // 아무것도 출력이 안된다. 이유는 WHERE ROWNUM은 새로운 SELECT의 ROWNUM이기 때문에
    
WHERE 뒤에 ROWNUM은 새로운 SELECT의 ROWNUM 이므로 FROM의 ROWNUM에 별칭을 붙여 아래와 같이 작성하면 ROWNUM으로 행을 제한할 수 있다.

    SELECT * FROM (SELECT ROWNUM as NUM, NOTICE.* FROM NOTICE ) WHERE NUM >= 3
    


 ### 10.2 중복된 값 제거 (DISTINCT)
 
 컬럼 앞에 DISTINCT를 붙여주면 그 컬럼의 중복 값을 제거해준다. <br>
 단 다른 컬럼과 같이 동시에 사용하지 못한다. 하나의 컬럼 목록만 뽑아줄 때만 사용가능
 
    SELECT DISTINCT AGE FROM MEMBER;
    
    
 ## 11. 내장함수
 
 ### 11.1 문자열 함수
 
 연산자로 처리 가능하면 함수보다는 연산자로 처리하는게 좋다.
 
 #### SUBSTR 문자열 - 추출 함수
 
    SELECT SUBSTR('HELLO', 3) FROM DUAL; // 세 번째부터 모든 문자 출력
    // LLO
    SELECT SUBSTR('HELLO', 2, 3) FROM DUAL; // 두 번째부터 3개의 문자 출력
    // ELL
    
    예제) 전화번호를 등록하지 않은 사람의 생일이 7, 8, 9월인 사람의 모든 정보를 출력해라.
    SELECT * FROM MEMBER WHERE PHONE IS NULL AND SUBSTR(BIRTHDAY, 6,2) IN (07,08,09); 
    
 #### CONCAT - 문자열 덧셈 함수
    
    SELECT CONCAT('홍', '길동') FROM DUAL;  // 가능하면 || 사용
    // 홍길동
    
 #### TRIM - 문자열 트림 함수(공백제거)
 
    SELECT LTRIM('    HELLO     ') FROM DUAL; // 왼쪽 공백 제거
    SELECT RTRIM('    HELLO     ') FROM DUAL;  // 오른쪽 공백 제거
    SELECT TRIM('    HELLO     ') FROM DUAL;  // 양쪽 공백 제거
  
 
 #### LOWER, UPPER - 문자열 소문자 또는 대문자로 변경하기
 
    SELECT LOWER('NeWlEC') FROM DUAL; // 소문자로 변경
    // newlec
    SELECT UPPER('NeWlEC') FROM DUAL; // 대문자로 변경
    // NEWLEC
    
    EX) 대소문자를 가리지 않고 출력할 때 사용 LOWER이나 UPPER를 사용해서 통일해주고 출력
    
#### REPLACE, TRANSLATE - 문자열 대치 함수
 
    SELECT REPLACE('WHEWE WE ARE', 'WE', 'YOU') FROM DUAL; // 문자열 WE를 YOU로 통채로 교체
    // WHEYOU YOU ARE
    SELECT TRANSLATE('WHEWE WE ARE', 'WE', 'YOU') FROM DUAL; // W를 Y로 E를 O로 변경 자리수 안맞는건 무시됨
    // YHOYO YO ARO
    
#### LPAD, RPAD - 문자열 패딩 함수 

    SELECT LPAD('HELLO', 7, 'A') FROM DUAL; -- 부족한 자리만큼 A로 왼쪽으로 채움 이 때 출력 값은 7자리 넘어가는건 무시됨
    // AAHELLO
    SELECT RPAD('HELLO', 7, 'A') FROM DUAL; -- 부족한 자리만큼 A로 오른쪽을 채움
    // HELLOAA
    SELECT RPAD('홍길', 6, '0') FROM DUAL; -- 한글은 자리 하나당 곱하기 2해줘야함
    // 홍길00    

#### INITCAP - 첫 글자를 대문자로 바꾸는 함수

    SELECT INITCAP('the ...') FROM DUAL; -- 단어의 첫 글자를 대문자로 바꿔줌
    // The ...
    SELECT INITCAP('the most important thing is ....') FROM DUAL; -- 모든 단어 첫 글자를 대문자로 바꿈
    // The Most Important Thing Is ....
    
#### INSTR - 문자열 위치 검색 함수
    SELECT INSTR('ALL WE NEED TO IS JIST TO...', 'TO') FROM DUAL; -- TO의 위치를 알려줌
    // 13
    SELECT INSTR('ALL WE NEED TO IS JIST TO...', 'TO', 15) FROM DUAL; -- 15자리 다음부터 TO의 위치를 알려줌
    // 24
    SELECT INSTR('ALL WE NEED TO IS JIST TO...', 'TO', 1, 2) FROM DUAL; -- 첫 번째 TO위치부터 찾기 시작해서 두 번째 TO위치를 알려줌
    // 24
 
 #### LENGTH - 문자열 길이를 알려주는 함수
 
    SELECT LENGTH('WHERE WE ARE') FROM DUAL; -- 문자열의 길이를 알려줌
    // 12

    EX) 만약에 회원의 전화번호를 컬럼에 포함된 문자 '-'를 없앤 전화번호의 길이를 출력하시오.
    SELECT LENGTH(REPLACE(PHONE, '-', '')) FROM MEMBER; // 함수 중첩 가능

#### ASCII, CHR - 아스키 관련 함수
    SELECT ASCII('A') FROM DUAL; -- 코드 값을 반환하는 함수
    // 65
    SELECT CHR(65) FROM DUAL; -- 코드 값으로 문자를 반환하는 함수
    // A
    
    
### 11.2 숫자 함수

#### ABS -  절대값을 구하는 함수 

    SELECT ABS(35), ABS(-35) FROM DUAL;
    // 35 35

#### SIGN - 음수/양수를 알려주는 함수 

    SELECT SIGN(35), SIGN(-35), SIGN(0) FROM DUAL;
    // 1  -1  0

#### ROUND - 숫자의 반올림 값을 알려주는 함수 

    SELECT ROUND(34.35678), ROUND(34.56567) FROM DUAL;
    // 34 35

    SELECT ROUND(12.3456789, 2), ROUND(12.566789, 3) FROM DUAL; -- 반올림해서 소수 N 번째 자리까지 출력
    // 12.35  12.567

#### TRUNC, MOD - 숫자의 몫 나머지 값을 반환하는 함수 

    SELECT TRUNC(17/5) 몫, MOD(17, 5) 나머지 FROM DUAL;
    // 3  2

#### POWER, SQRT - 숫자의 제곱을 구하는 함수와 제곱근을 구하는 함수

    SELECT POWER(5, 3), SQRT(25) FROM DUAL;
    // 125  5

### 11.3 날짜 함수

#### SYSDATE, CURRENT_DATE, SYSTIMESTAMP, CURRENT_TIMESTAMP - 현재 시간을 얻는 함수

오라클 서버의 시간 : SYS,  세션 설정에 따른 시간 : CURRENT;
년 월 일 : DATE, 시 분 초까지 : TITMESTAMP;

    SELECT SYSDATE, CURRENT_DATE, SYSTIMESTAMP, CURRENT_TIMESTAMP FROM DUAL;


#### 세션 시간과 포맷 변경

    ALTER SESSION SET NLS_DATE_FORMAT = 'YYYY-MM-DD HH24:MI:SS'; -- 원하는 날짜 형태로 포맷
    ALTER SESSION SET TIME_ZONE = '09:00'; -- 다른 나라시간으로 조정해줌

#### EXTRACT - 날짜 추출함수

    SELECT EXTRACT(YEAR FROM SYSDATE) FROM DUAL;
    SELECT EXTRACT(MONTH FROM SYSDATE) FROM DUAL;
    SELECT EXTRACT(DAY FROM SYSDATE) FROM DUAL;
    SELECT EXTRACT(HOUR FROM SYSTIMESTAMP) FROM DUAL; -- 시 분 초는 TIMESTAMP를 사용해야 한다.
    SELECT EXTRACT(MINUTE FROM SYSTIMESTAMP) FROM DUAL;
    SELECT EXTRACT(SECOND FROM SYSTIMESTAMP) FROM DUAL;

    ex)회원가입을 2, 4, 11, 12월에 완료한 회원의 모든 정보를 출력;
    SELECT * FROM MEMBER WHERE EXTRACT(MONTH FROM REGDATE) IN (2, 4, 11, 12);
    
#### ADD_MONTHS - 날짜를 누적하는 함수

    SELECT ADD_MONTHS(SYSDATE, 1) FROM DUAL;
    SELECT ADD_MONTHS(SYSDATE, -1) FROM DUAL;

    ex) 가입 회원 중에 가입한지 6개월 안되는 회원을 조회하시오.;
    SELECT * FROM MEMBER WHERE ADD_MONTHS(SYSDATE, -6) <= REGDATE; -- 현재에서 6개월을 뺀것보다 크면 만족



#### MONTHS_BETWEEN - 날짜의 차이를 알아내는 함수

    SELECT MONTHS_BETWEEN(SYSDATE, TO_DATE('2013-12-25')) FROM DUAL; --  TO_DATE 는 날짜로 변환해주는 함수
    // 94.1200 -> 94개월이 조금 넘는다라는 뜻;
    
    -- ex) 가입 회원 중에 가입한지 6개월 안되는 회원을 조회하시오.
    SELECT * FROM MEMBER WHERE MONTHS_BETWEEN(SYSDATE, REGDATE) < 6;

#### NEXT_DAY - 다음 요일의 날짜를 알려주는 함수  NEXT_DAY

    SELECT NEXT_DAY(SYSDATE, '화요일') FROM DUAL;
    
    
#### LAST_DAY - 월의 마지막 일자를 알려주는 함수

    SELECT LAST_DAY(SYSDATE) FROM DUAL;
    SELECT LAST_DAY(TO_DATE('2021-01-24')) FROM DUAL;
    // 2021-01-31 00:00:00;
       
### 11.4 형식 변환 함수

![image](https://user-images.githubusercontent.com/81665608/139220404-fcc7f977-0512-4ec1-a263-a6f1e3878b1b.png)

#### 숫자를 문자열로
    
![image](https://user-images.githubusercontent.com/81665608/139221115-47c20cfa-d0d2-45f6-bfec-8572a5d70cd9.png)

    SELECT TO_CHAR(12345678, '99999,999') || 'HELLO' FROM DUAL;
    //  12345,678HELLO;

    SELECT TO_CHAR(1234567, '09,999,999,999') || '원' FROM DUAL; -- 0을 써주면 부족한 부분은 0으로 채움
    //  00,001,234,567원;

    SELECT TRIM(TO_CHAR(1234567, '9,999,999,999')) || '원' FROM DUAL; -- TRIM을 써서 공백제거 가능
    // 1,234,567원;

#### 날짜를 문자열로

![image](https://user-images.githubusercontent.com/81665608/139222820-05654a9c-9551-4303-89ed-3ae5f7f5133e.png)

    SELECT TO_CHAR(SYSDATE, 'YY/MM/DD HH24:MI') FROM DUAL;
    // 21/10/28 17:58;

#### 문자열을 날짜로

![image](https://user-images.githubusercontent.com/81665608/139223422-3f39e5f0-1362-4246-9a98-dd51677ca649.png)

    SELECT TO_DATE('2014-03-31', 'YYYY-MM-DD HH:MI:SS') FROM DUAL; -- 기본형태
    // 2014-03-31 00:00:00
    SELECT TO_DATE('2014-03-31 12:23:03', 'YYYY-MM-DD HH:MI:SS') FROM DUAL; -- 기본형태가 아닌건 포맷 문자를 이용해서 설명해야함
    // 2014-03-31 12:23:03

#### 문자열을 숫자로

![image](https://user-images.githubusercontent.com/81665608/139223993-b34f9dc4-f708-4f34-9d19-db202ab1cfb7.png)

    SELECT TO_NUMBER('2') + 3 FROM DUAL; -- 생략해도 되지만 명시적으로 입력

### 11.5 NULL 함수

#### NVL - NULL일 경우에 대체 값을 제공하는 함수;

    SELECT NVL(AGE, 0) FROM MEMBER;

    SELECT NVL(AGE, 0) +3 FROM MEMBER; -- AGE가 NULL인 경우 0으로 대체

#### NVL2 - NVL에서 조건을 하나 더 확장한 함수

    SELECT NVL2(AGE,10+3 ,0) FROM MEMBER; -- NULL인 경우 0, 아닌 경우 10+3

#### NULLIF - 두 값이 같은 경우 NULL 그렇지 않은 경우 첫 번째 값 반환 함수;

    SELECT NULLIF(AGE, 30) FROM MEMBER;

#### DECODE - 조건에 따른 값 선택하기;

    SELECT DECODE(GENDER, '남성', 1, 2) FROM MEMBER;
    SELECT DECODE('011', '011', 'SK', '016', 'KT', '기타') FROM MEMBER; 
    -- 첫 번째 인자가 011 이면 SK, 016 이면 KT 둘 다 아니면 기타 (인자는 여러개 넣을 수 있다.)
    // SK; 

### 11.6 집계 함수

#### COUNT - 카운트 해줌

    SELECT COUNT(ID) FROM NOTICE -> NOTICE에 ID가 몇개냐 (NULL은 제외하고 카운트함 
    
    // 그래서 레코드 수를 얻을려면 NULL인 안들어간 컬럼이나 * 을 사용
    SELECT COUNT(*)FROM NOTICE -> 단점은 속도가 느릴 수 있으니 NULL이 안들어간 컬럼 사용 추천
    
#### SUM, AVG - 합이나 평균을 집계해줌

    SELECT SUM(HIT) FROM NOTICE;
    SELECT AVG(HIT) FROM NOTICE;
    
#### MIN,  MAX
     
## 12. SELECT 문의 구절

SELECT, FROM ,WHERE, GROUP BY, HAVING, ORDER BY - 순서가 바뀌어서는 안된다.

### 12.1 정렬하기 (ORDER BY)

ASC : 오름차순<br>
DESC : 내림차순

옵션을 안쓰면 오름차순
       
    SELECT * FROM MEMBER ORDER BY NAME;
    
    EX)이름을 기준으로 역순으로 정렬해서 조회하시오.
    SELECT * FROM MEMBER ORDER BY NAME DESC;
    
    EX)회원 중에서 박씨 성을 가진 회원을 조회하시오(오름차순)
    SELECT * FROM MEMBER WHERE NAME LIKE '손%' ORDER BY NAME;
    
    // 이차 정렬할 경우
    SELECT FROM NOTICE ORDER BY HIT DESC, REGDATE DESC;


### 12.2 GROUP BY

보통 집계함수랑 쓰이는데 작성자별, ID별 등 으로 집계해줌

    SELECT COUNT(ID) FROM NOTICE GROUP BY WRITER_ID;
    
    SELECT WRITER_ID, COUNT(ID) FROM NOTICE GROUP BY WRITER_ID;
    
    // 주의할 점은 당연한거지만 그룹을 WRITER_ID로 했다면 SELECT에 WRITER_ID만 올 수 있다.
    
    // ORDER BY 까지 사용하면
    SELECT WRITER_ID, COUNT(ID) COUNT FROM NOTICE GROUP BY WRITER_ID ORDER BY COUNT DESC;
    
    // 실행 순서는 FROM -> CONNECT BY -> WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY 
    그래서 FROM 절에 쓴 별칭은 전부 사용 가능하지만 SELECT에서 만든 별칭은 ORDER BY 절에서만 사용 가능

### 12.3 HAVING

집계 함수는 WHERE 절에서 사용할 수 없다. HAVING에서 사용

    EX) 회원별 게시글 수를 조회하시오. 단 게시글이 2이하인 레코드만 출력하시오.    
    SELECT WRITER_ID, COUNT(N.ID) FROM NOTICE N GROUP BY WRITER_ID HAVING COUNT(N.ID) <= 2;


## 13. ROW_NUMBER(), RANK(), 

ROWNUM은 WHERE절 쯤에 생성이 되므로 ORDER BY 를 하면 순서가 섞여서 출력된다.<br>
그러므로 ORDER BY 후에 ROWNUM의 생성은 ROE_NUMBER를 이용한다.

    SELECT  ROW_NUMBER() OVER (ORDER BY HIT), ID, TITLE, WRITER_ID, REGDATE, HIT
    FROM NOTICE;
    
RANK는 정렬을 한 후에 순위를 매겨준다. 단 순위가 1 2 2 4 이런식으로 공동이 있으면 뒤에 순서는 밀린다.

    SELECT  RANK() OVER (ORDER BY HIT), ID, TITLE, WRITER_ID, REGDATE, HIT
    FROM NOTICE;
    
DENSE_RANK는 RANK의 순서가 밀리지 않게 출력한다. 1 2 2 3

    SELECT  DENSE_RANK() OVER (ORDER BY HIT), ID, TITLE, WRITER_ID, REGDATE, HIT
    FROM NOTICE;
    
전체가 아니라 작성자별, ID 별 등수를 매길 수 있는데

    SELECT  DENSE_RANK() OVER (PARTITION BY WRITER_ID ORDER BY HIT), ID, TITLE, WRITER_ID, REGDATE, HIT
    FROM NOTICE; -- 이 때 PARTITION BY 절에 들어가는 컬럼은 오름차순으로 정리된다.

## 14. 부조회(서브쿼리)

먼저 수행해서 그 결과를 남겨야 하는경우 서브쿼리를 사용한다.

![image](https://user-images.githubusercontent.com/81665608/139427910-f735070b-7933-4ea1-b604-6358f2c1230c.png)

    EX) 나이가 30 이상인 회원 목록을 조회하시오.
    SELECT * FROM MEMBER WHERE AGE >= (SELECT AVG(AGE) FROM MEMBER);

## 15. JOIN

두개 이상의 테이블을 결합하여 데이터를 검색하는 방법이다.

### 15.1 INNER JOIN

참조키를 기준으로 일치하는 행만 조인 <br>
일치하지 않는 행은 OUTER라 하고 빠짐

![image](https://user-images.githubusercontent.com/81665608/139436880-5e359eb5-9916-46e0-b329-e76300f0d81e.png)

    SELECT * FROM MEMBER INNER JOIN NOTICE ON MEMBER.ID = NOTICE.WRITER_ID;

### 15.2 OUTER JOIN

참조키를 기준으로 일치하지 않는 행도 포함시키는 조인
 
MEMBER LEFT/RIGHT/FULL OUTER JOIN NOTICE ON MEMBER.ID = NOITCE.WRITER_ID 에서<br>

- LEFT : 왼쪽의 OUTER를  포함시키겠다.
- RIGHT : 오른쪽의 OUTER를 포함시키겠다.
- FULL : 모두 포함시키겠다.

    SELECT * FROM NOTICE FULL OUTER JOIN MEMBER ON NOTICE.WRITER_ID = MEMBER.ID;
    // OUTER의 상대 테이블은 NULL 값으로 채워진다.
    
INNER를 쓸지 OUTER를 쓸지 잘 구분하자.

### 15.3 내부 조인 연습

#### 필드 이름의 충돌 문제

![image](https://user-images.githubusercontent.com/81665608/139440555-0437d153-d893-4baf-8552-854288a44f53.png)

별칭을 써서 식별자를 줄이자.

    // ID, NAME 그리고 회원별 작성한 게시글 수를 조회하시오.
    SELECT M.ID, M.NAME ,COUNT(N.ID) FROM MEMBER M LEFT JOIN NOTICE N ON M.ID = N.WRITER_ID GROUP BY M.ID, M.NAME;
    // M.ID와 N.NAME를 기준으로 GROUP BY를 하면 M.ID, M.NAME의 컬럼을 불러올 수 있음
    
    
### 15.4 SELF JOIN

자신이 자신을 참조

![image](https://user-images.githubusercontent.com/81665608/139446028-b308022d-9e1a-4946-bfd2-fabbcd20d007.png)
    
![image](https://user-images.githubusercontent.com/81665608/139446247-c72b50e9-fe0c-43bb-a9e6-6c2e5a5d8252.png)
    
    SELECT M.*, B.NAME BOSS FROM  MEMBER M LEFT OUTER JOIN MEMBER B ON B.ID = M.BOSS_ID;
 
 ### 15.5 ORACLE JOIN
 
 ![image](https://user-images.githubusercontent.com/81665608/139448181-7cee30b0-e828-46b0-93c2-515f315e2d48.png)
 
 ![image](https://user-images.githubusercontent.com/81665608/139448294-0fbd6194-432f-489c-8a6b-3b340e915022.png)


  
  
  
  
    
    
