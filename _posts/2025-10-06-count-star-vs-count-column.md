---
title: COUNT(*) vs COUNT(column_name)
author: kimwanyoung
date: 2025-10-06
category: SQL
layout: post
---

SQL에서 데이터 개수를 세는 `COUNT` 함수는 매우 자주 사용되지만, `COUNT(*)`와 `COUNT(column_name)`의 차이를 정확히 이해하지 못했었다. 이번에 둘의 차이를 비교하고 확실히 이해하고 넘어가보고자 한다.

---

## 기본 개념

### 📝 COUNT(*)
- **행(Row)의 개수를 계산**한다.  
- 행이 존재하기만 하면 `NULL`이 있더라도 포함된다.  
- 즉, **전체 행 수**를 반환한다.

| id | email                     | status    |
| -- | ------------------------- | --------- |
| 1  | [a@a.com](mailto:a@a.com) | completed |
| 2  | NULL                      | pending   |
| 3  | [b@b.com](mailto:b@b.com) | completed |

```sql
SELECT COUNT(*) FROM orders;
-- COUNT(*) = 3 (모든 행 포함)
```

### 📝 COUNT(column_name)
- 해당 컬럼이 NULL이 아닌 행만 카운트한다.
- 즉, **해당 컬럼이 존재하는 행의 수**를 반환한다.

| id | email                     | status    |
| -- | ------------------------- | --------- |
| 1  | [a@a.com](mailto:a@a.com) | completed |
| 2  | NULL                      | pending   |
| 3  | [b@b.com](mailto:b@b.com) | completed |


```sql
SELECT COUNT(email) FROM orders;
-- COUNT(email) = 2 (NULL 제외)
```

### 📝 COUNT(*) 동작 방식

```sql
SELECT COUNT(*) FROM orders WHERE status = 'completed';
```

1. **인덱스 스캔 최적화**: 대부분의 DBMS는 가장 작은 인덱스를 사용하여 행 개수를 센다.
2. **데이터 페이지 접근 최소화**: 실제 테이블 데이터에 접근하지 않고, 인덱스만으로 카운트.
3. **NULL 체크 불필요**: 행의 존재만 확인하면 되므로 컬럼 값 검사를 생략한다.

### 📝 COUNT(column_name) 동작 방식

```sql
SELECT COUNT(email) FROM orders WHERE status = 'completed';
```

1. **컬럼 값 접근 필수**: 해당 컬럼이 NULL인지 확인해야 한다.
2. **인덱스 활용 조건**: `email` 컬럼에 인덱스가 있어야 효율적입니다.
3. **데이터 읽기 오버헤드**: NULL 체크를 위해 실제 컬럼 값을 읽어야 한다.

---

## 성능 차이

### InnoDB (MySQL) 기준

```sql
-- 1. COUNT(*) - 가장 빠름
SELECT COUNT(*) FROM large_table;
-- Secondary Index 사용, 데이터 페이지 접근 X

-- 2. COUNT(primary_key) - 빠름
SELECT COUNT(id) FROM large_table;
-- Primary Key는 NOT NULL 보장, 인덱스 사용

-- 3. COUNT(nullable_column) - 느림
SELECT COUNT(email) FROM large_table;
-- NULL 체크 필요, 컬럼 값 읽기 필요
```
---

### 🔍 COUNT(*) WHERE column_name IS NOT NULL vs COUNT(column_name)

```sql 
SELECT COUNT(*) 
FROM orders 
WHERE email IS NOT NULL;

SELECT COUNT(email) 
FROM orders;
```

| 비교 항목         | COUNT(*) + WHERE             | COUNT(column_name) |
| ------------- | ---------------------------- | ------------------ |
| 필터 처리         | WHERE 절에서 NULL 제거 후 전체 행 카운트 | COUNT 내부에서 NULL 제외 |
| 실행 계획         | 옵티마이저가 WHERE 조건을 먼저 필터링      | 함수 내부에서 조건 처리      |
| 인덱스(email) 유무 | 인덱스 있으면 동일 성능                | 인덱스 없으면 더 느림       |
| 결과            | 동일                           | 동일                 |

즉, 결과는 동일하지만, 인덱스 사용 여부에 따라 성능이 달라진다.

인덱스가 걸려 있으면 두 쿼리 모두 효율적으로 수행됨

인덱스가 없을 경우, COUNT(*) WHERE column_name IS NOT NULL이 약간 더 빠를 수 있음
(WHERE 필터링 시 옵티마이저가 범위 조건으로 처리하기 때문)

### 정리

| 항목                                  | 설명                      | 특징                 |
| ----------------------------------- | ----------------------- | ------------------ |
| `COUNT(*)`                          | 전체 행 수 계산               | 가장 빠름, NULL 포함     |
| `COUNT(column)`                     | 특정 컬럼의 NULL 제외 행 수 계산   | 컬럼 접근 필요           |
| `COUNT(*) WHERE column IS NOT NULL` | 결과는 `COUNT(column)`과 동일 | WHERE 절에서 NULL 필터링 |

### ✅ 결론
- 전체 행 수를 셀 때는 COUNT(*)
- 특정 컬럼의 값이 존재하는 행만 셀 때는 COUNT(column)
- 하지만, 특정 컬럼에 인덱스를 강제하는 COUNT(column_name)을 굳이 사용해야할까 생각이 들기도 한다. 그냥 COUNT(*) WHERE column_name IS NOT NULL을 사용해도 되지 않을까?
