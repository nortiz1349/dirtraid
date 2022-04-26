---
title: "다중행 함수와 데이터 그룹화"
excerpt: 오라클 수업 - day 4
excerpt_separator: "<!--more-->"
categories:
  - TIL
tags:
  - oracle
  - database
  - sql

toc: true
toc_sticky: true
date: 2022-04-26
---

## 하나의 열에 결과를 출력하는 다중행 함수

- `SUM`

```sql
SUM([DISTINCT(선택)] [합계 대상(필수)])
OVER (문법지정)(선택)
-- DISTINCT는 중복값을 제외하고 합산하는 옵션, 디폴트는 ALL
```

- `COUNT`

```sql
COUNT([DISTINCT(선택)] [합계 대상(필수)])
OVER (문법지정)(선택)

-- 부서 번호가 30번인 직원의 수
SELECT COUNT(*)
    FROM EMP
  WHERE DEPTNO = 30;

-- COUNT 사용시 NULL데이터는 반환 개수에서 제외 됨.
```

- `MAX, MIN`

```sql
-- 부서번호 10번 사원 중, 최대 급여 출력
SELECT MAX(SAL)
    FROM EMP
  WHERE DEPNO =10;

-- 날짜 데이터에도 사용 가능
```

- `AVG`

```sql
-- 중복을 제거한 급여 평균 구하기
SELECT AVG(DISTINCT SAL)
    FROM EMP
  WHERE DEPTNO = 30;
```

## 열로 묶어 출력하는 GROUP BY, HAVING

```sql
SELECT [COLUMN1], [COLUMN2], ...
FROM [TABLE_NAME]
WHERE [행 선별 조건식]  -- 그룹 대상 데이터를 처음부터 제외할 때 사용
GROUP BY [그룹화할 열 지정]
HAVING [출력 그룹을 제한하는 조건식]
ORDER BY [정렬하려는 열 지정];

SELECT DEPTNO, JOB, AVG(SAL)
  FROM EMP
GROUP BY DEPTNO, JOB
  HAVING AVG(SAL) >= 500
ORDER BY DEPTNO, JOB;
```

## 그룹화와 관련된 여러 함수

- `ROLLUP`, `CUBE` : 중간 집계

```sql
SELECT
FROM
WHERE
GROUP BY ROLLUP (COLUMN1, 2, ...);

SELECT DEPTNO, JOB, COUNT(*), MAX(SAL), SUM(SAL), AVG(SAL)
    FROM EMP;
  GROUP BY ROLLUP(DEPTNO, JOB);
```

```sql
SELECT
FROM
WHERE
GROUP BY CUBE [COLUMN1, 2, ...];
-- CUBE로 그룹화 할때는 그룹 대상 열 수에 따라 그룹수가 기하급수적으로 늘어나므로 사용에 주의해야 한다.

SELECT DEPTNO, JOB, COUNT(*), MAX(SAL), SUM(SAL), AVG(SAL)
    FROM EMP;
  GROUP BY CUBE(DEPTNO, JOB)
  ORDER BY DEPTNO, JOB;
```

- `GROUPING SETS`

```sql
SELECT
FROM
WHERE
GROUP BY GROUPING SETS ([COLUMN1],2,...)


```