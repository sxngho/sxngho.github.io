---
title:  "[Oracle] 테이블 백업/복원 방법"
categories: 
- database
- oracle
tags: 
- oracle
- table
- backup
- index
---

## 테이블 백업 방법
```sql
CREATE TABLE [백업 테이블 명] AS SELECT * FROM [원본 테이블 명]
```
```sql
CREATE TABLE  Student_backup AS SELECT * FROM Student
```

### 테이블 백업 시 주의사항

Primary Key 나 Index 등 **Constraint 는 복제되지 않습니다.**

출처: [https://applejara.tistory.com/400](https://applejara.tistory.com/400) [애플자라]

## 테이블 복원 방법
```sql
DELETE FROM [원본 테이블 명]
INSERT INTO [원본 테이블 명] SELECT * FROM [백업 테이블 명]
```
```sql
DELETE FROM Student
INSERT INTO Student SELECT * FROM Student_backup
```

## 백업 테이블의 속도만 현저하게 느린현상

: 테이블을 백업하는 쿼리를 통해 백업할 때 Constraint가 복제되지 않는다는 것은 백업된 테이블에는 PK와 INDEX가 없다는 것이다.

INDEX가 없는 테이블은 CRUD 속도는 **매우매우매우** 느리다. 실제로 백업테이블을 조회해보니 PK가 설정되어있지 않았다.

테이블 내의 PK 조회하는 쿼리
```sql
SELECT C.COLUMN_NAME FROM USER_CONS_COLUMNS C, USER_CONSTRAINTS S
WHERE C.CONSTRAINT_NAME = S.CONSTRAINT_NAME AND S.CONSTRAINT_TYPE = 'P'
AND C.TABLE_NAME = [TABLE_NAME]
```
```sql
SELECT C.COLUMN_NAME FROM USER_CONS_COLUMNS C, USER_CONSTRAINTS S 
WHERE C.CONSTRAINT_NAME = S.CONSTRAINT_NAME AND S.CONSTRAINT_TYPE = 'P'
AND C.TABLE_NAME = 'Student_backup'
```
테이블 내의 INDEX 조회하는 쿼리
```sql
SELECT * FROM USER_INDEXES WHERE TABLE_NAME = [TABLE_NAME]
```
```sql
SELECT * FROM USER_INDEXES WHERE TABLE_NAME = 'Student_backup'
```

실제로 백업테이블을 사용하면서 PK가 없는 테이블은 처음 사용해봤다.

다음에도 비슷하게 속도가 느려진 경우가 생긴다면 PK와 INDEX를 먼저 조회해보지 않을까


이미 만들어진 테이블에 PK를 부여하고자 할때는 다음과 같은 쿼리를 사용한다.
```sql
ALTER TABLE [TABLE_NAME] ADD CONSTRAINT [TABLE_PK_NAME] PRIMARY KEY([PK COLUMN NAME])
```
```sql
ALTER TABLE Student_backup ADD CONSTRAINT Student_backup_PK PRIMARY KEY(StudentNo)
```

다음과 같은 쿼리로 PK를 부여해주었더니 INDEX가 생겨 백업테이블의 CRUD 속도가 정상적으로 나왔다.

결론적으로, 원본 테이블과 백업 테이블의 CRUD 속도 차이가 나는 이유는 **INDEX의 유무**였으며, 
INDEX는 PK를 생성하게되면 PK에 대한 INDEX가 만들어지기 때문에 원본테이블과 백업테이블의 속도차이를 없앨 수 있었다.
