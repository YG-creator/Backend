

# 1. Intro

## 	1. SQL이란?

​	DB에 접근하고 다루기 위한 언어

​	구조화된 쿼리 언어

## 	2. 무엇을 하는가?

​	DB CRUD

## 	3. 사용처

​	웹사이트에서 DB의 데이터를 보여줄때

## 	4. RDBMS



# 2. 명령어

- `SELECT` - extracts data from a database
- `UPDATE` - updates data in a database
- `DELETE` - deletes data from a database
- `INSERT INTO` - inserts new data into a database
- `CREATE DATABASE` - creates a new database
- `ALTER DATABASE` - modifies a database
- `CREATE TABLE` - creates a new table
- `ALTER TABLE` - modifies a table
- `DROP TABLE` - deletes a table
- `CREATE INDEX` - creates an index (search key)
- `DROP INDEX` - deletes an index

## 	1. SELECT	

```sql
SELECT column1, column2, ...
FROM table_name;
```



### 		DISTINCT

```sql
SELECT DISTINCT column1, column2, ...
FROM table_name;
```



### 		WHERE

```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

#### 		IN

​		조건 값이 여러개일 때

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1, value2, ...);
```

#### 		BETWEEN

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name BETWEEN value1 AND value2;
```



#### 		EXISTS

```sql
SELECT column_name(s)
FROM table_name
WHERE EXISTS
(SELECT column_name FROM table_name WHERE condition);
```



### 	AND,OR,NOT

```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition1 AND condition2 AND condition3 ...;
```



### 	ORDERED BY

​	정렬

```sql
SELECT column1, column2, ...
FROM table_name
ORDER BY column1, column2, ... ASC|DESC;
```



### 	LIMIT

​	출력 갯수 설정

```sql
SELECT column_name(s)
FROM table_name
WHERE condition
LIMIT number;
```







### 	MIN(),MAX()

```sql
SELECT MIN(column_name)
FROM table_name
WHERE condition;
```

```sql
SELECT MAX(column_name)
FROM table_name
WHERE condition;
```



### 	COUNT(), AVG() and SUM()

```sql
SELECT COUNT(column_name)
FROM table_name
WHERE condition;
```

```sql
SELECT AVG(column_name)
FROM table_name
WHERE condition;
```

```sql
SELECT SUM(column_name)
FROM table_name
WHERE condition;
```



### 	LIKE

​		_ : 한글자

```sql
SELECT * FROM Customers
WHERE City LIKE '_ondon';
```

​		% : 한글자 이상

```sql
SELECT * FROM Customers
WHERE CustomerName LIKE 'a%';
```

​		[] : 기준 여러개

```sql
SELECT * FROM Customers
WHERE City LIKE '[bsp]%';	// b or s or p 로 시작하는거
```

### 	ALIAS

​	column, table 이름 짓기

```sql
SELECT column_name AS alias_name
FROM table_name;
```

```sql
SELECT column_name
FROM table_name AS alias_name;
```

​	

	### 	GROUP BY

```sql
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)
ORDER BY column_name(s);
```



### 	HAVING

GROUP BY 조건절

```sql
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)
HAVING condition
ORDER BY column_name(s);
```



### SELECT INTO

새로운 테이블에 복사하기

```sql
SELECT *
INTO newtable [IN externaldb]
FROM oldtable
WHERE condition;
```







## 2. INSERT INTO

테이블에 새로운 record 삽입

```sql
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```

기존 테이블 값 복사

```sql
INSERT INTO table2 (column1, column2, column3, ...)
SELECT column1, column2, column3, ...
FROM table1
WHERE condition;
```



## 3. Null Values

### 	IS NULL

```sql
SELECT column_names
FROM table_name
WHERE column_name IS NULL;
```

### 	IS NOT NULL

```sql
SELECT column_names
FROM table_name
WHERE column_name IS NOT NULL;
```

### IFNULL()

null이면 0 반환

```sql
SELECT ProductName, UnitPrice * (UnitsInStock + IFNULL(UnitsOnOrder, 0))
FROM Products;
```





## 4. UPDATE

기존 record 수정

```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```



## 5. DELETE

```sql
DELETE FROM table_name WHERE condition;
```



## 6. JOIN

연관된 column 를 기준으로 2개이상의 테이블을 합치는 것

![SQL INNER JOIN](https://www.w3schools.com/sql/img_innerjoin.gif) ![SQL LEFT JOIN](https://www.w3schools.com/sql/img_leftjoin.gif) ![SQL RIGHT JOIN](https://www.w3schools.com/sql/img_rightjoin.gif) ![SQL FULL OUTER JOIN](https://www.w3schools.com/sql/img_fulljoin.gif) 



### 	1. INNER JOIN

![SQL INNER JOIN](https://www.w3schools.com/sql/img_innerjoin.gif)

```sql
SELECT column_name(s)
FROM table1
INNER JOIN table2
ON table1.column_name = table2.column_name;
```



### 	2. LEFT JOIN

![SQL LEFT JOIN](https://www.w3schools.com/sql/img_leftjoin.gif)

```sql
SELECT column_name(s)
FROM table1
LEFT JOIN table2
ON table1.column_name = table2.column_name;
```



### 	3. RIGHT JOIN

![SQL RIGHT JOIN](https://www.w3schools.com/sql/img_rightjoin.gif)

```sql
SELECT column_name(s)
FROM table1
RIGHT JOIN table2
ON table1.column_name = table2.column_name;
```



### 	4. FULL OUTER JOIN

![SQL FULL OUTER JOIN](https://www.w3schools.com/sql/img_fulljoin.gif)

```sql
SELECT column_name(s)
FROM table1
FULL OUTER JOIN table2
ON table1.column_name = table2.column_name
WHERE condition;
```



### 	5. Self JOIN

```sql
SELECT column_name(s)
FROM table1 T1, table1 T2
WHERE condition;
```



## 7. UNION

UNION : 중봅없앤 합집합

```sql
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;
```

UNION ALL : 중복안없앤 합집합

```sql
SELECT column_name(s) FROM table1
UNION ALL
SELECT column_name(s) FROM table2;
```



## 8. ANY, ALL

ANY : 조건만족하는것만 출력

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name operator ANY
  (SELECT column_name
  FROM table_name
  WHERE condition);
```

ALL : 모두만족해야 출력

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name operator ALL
  (SELECT column_name
  FROM table_name
  WHERE condition);
```



## 9. Case

```sql
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    WHEN conditionN THEN resultN
    ELSE result
END;
```



## 10. Stored Procedure

### store

```sql
CREATE PROCEDURE procedure_name @column_name datatype    -- @가 인수
AS
sql_statement
GO;
```

### exexute

```sql
EXEC procedure_name;
```



## 11. 주석

```sql
-- 한줄 주석임
/*  여러줄 주석임  */ 
```



## 12. 연산자

1. 수학 연산자

| Operator | Description | Example                                                      |
| :------- | :---------- | :----------------------------------------------------------- |
| +        | Add         | [Try it](https://www.w3schools.com/sql/trysql.asp?filename=trysql_op_add) |
| -        | Subtract    | [Try it](https://www.w3schools.com/sql/trysql.asp?filename=trysql_op_subtract) |
| *        | Multiply    | [Try it](https://www.w3schools.com/sql/trysql.asp?filename=trysql_op_multiply) |
| /        | Divide      | [Try it](https://www.w3schools.com/sql/trysql.asp?filename=trysql_op_divide) |
| %        | Modulo      |                                                              |

2. 비트 연산자

| Operator | Description          |
| :------- | :------------------- |
| &        | Bitwise AND          |
| \|       | Bitwise OR           |
| ^        | Bitwise exclusive OR |

3. 비교 연산자

| Operator | Description              | Example                                                      |
| :------- | :----------------------- | :----------------------------------------------------------- |
| =        | Equal to                 | [Try it](https://www.w3schools.com/sql/trysql.asp?filename=trysql_op_equal_to) |
| >        | Greater than             | [Try it](https://www.w3schools.com/sql/trysql.asp?filename=trysql_op_greater_than) |
| <        | Less than                | [Try it](https://www.w3schools.com/sql/trysql.asp?filename=trysql_op_less_than) |
| >=       | Greater than or equal to | [Try it](https://www.w3schools.com/sql/trysql.asp?filename=trysql_op_greater_than2) |
| <=       | Less than or equal to    | [Try it](https://www.w3schools.com/sql/trysql.asp?filename=trysql_op_less_than2) |
| <>       | Not equal to             |                                                              |

4. 복합 연산자

| Operator | Description              |
| :------- | :----------------------- |
| +=       | Add equals               |
| -=       | Subtract equals          |
| *=       | Multiply equals          |
| /=       | Divide equals            |
| %=       | Modulo equals            |
| &=       | Bitwise AND equals       |
| ^-=      | Bitwise exclusive equals |
| \|*=     | Bitwise OR equals        |

5. 논리 연산자

| Operator | Description                                                  | Example                                                      |
| :------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| ALL      | TRUE if all of the subquery values meet the condition        | [Try it](https://www.w3schools.com/sql/trysql.asp?filename=trysql_op_all&ss=-1) |
| AND      | TRUE if all the conditions separated by AND is TRUE          | [Try it](https://www.w3schools.com/sql/trysql.asp?filename=trysql_op_and) |
| ANY      | TRUE if any of the subquery values meet the condition        | [Try it](https://www.w3schools.com/sql/trysql.asp?filename=trysql_op_any&ss=-1) |
| BETWEEN  | TRUE if the operand is within the range of comparisons       | [Try it](https://www.w3schools.com/sql/trysql.asp?filename=trysql_op_between) |
| EXISTS   | TRUE if the subquery returns one or more records             | [Try it](https://www.w3schools.com/sql/trysql.asp?filename=trysql_op_exists) |
| IN       | TRUE if the operand is equal to one of a list of expressions | [Try it](https://www.w3schools.com/sql/trysql.asp?filename=trysql_op_in) |
| LIKE     | TRUE if the operand matches a pattern                        | [Try it](https://www.w3schools.com/sql/trysql.asp?filename=trysql_op_like) |
| NOT      | Displays a record if the condition(s) is NOT TRUE            | [Try it](https://www.w3schools.com/sql/trysql.asp?filename=trysql_op_not) |
| OR       | TRUE if any of the conditions separated by OR is TRUE        | [Try it](https://www.w3schools.com/sql/trysql.asp?filename=trysql_op_or) |
| SOME     | TRUE if any of the subquery values meet the condition        |                                                              |



# 3. SQL DB

## 1. Create

```sql
CREATE DATABASE databasename;
```

## 2. Drop

```sql
DROP DATABASE databasename;
```

## 3. Backup

1. 그냥 백업

```sql
BACKUP DATABASE databasename
TO DISK = 'filepath';
```

2. differential 백업 : 바뀐거만 백업되서 시간절약됨

```sql
BACKUP DATABASE databasename
TO DISK = 'filepath'
WITH DIFFERENTIAL;
```



# 4. Table

## 1. Create

1. 새로 생성

```sql
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    column3 datatype,
   ....
);
```

2. 기존거 사용

```sql
CREATE TABLE new_table_name AS
    SELECT column1, column2,...
    FROM existing_table_name
    WHERE ....;
```



## 2. Drop

1. table 자체 삭제

```sql
DROP TABLE table_name;
```

2. table 전체 값 삭제

```sql
TRUNCATE TABLE table_name;
```



## 3. Alter

1. 열 추가

```sql
ALTER TABLE table_name
ADD column_name datatype;
```

2. 열 삭제

```sql
ALTER TABLE table_name
DROP COLUMN column_name;
```

3. 열 수정

```sql
ALTER TABLE table_name
MODIFY COLUMN column_name datatype;
```



# 5. Constraints

```sql
CREATE TABLE table_name (
    column1 datatype constraint,
    column2 datatype constraint,
    column3 datatype constraint,
    ....
);
```

- `NOT NULL` - Ensures that a column cannot have a NULL value

- `UNIQUE` - Ensures that all values in a column are different

- `PRIMARY KEY` - A combination of a `NOT NULL` and `UNIQUE`. Uniquely identifies each row in a table

- `FOREIGN KEY` - Prevents actions that would destroy links between tables

- `CHECK` - Ensures that the values in a column satisfies a specific condition

- `DEFAULT` - Sets a default value for a column if no value is specified

- `CREATE INDEX` - Used to create and retrieve data from the database very quickly

  ```sql
  CREATE INDEX index_name
  ON table_name (column1, column2, ...);
  ```

- `AUTO_INCREMENT` - 데이터가 삽입될때마다 자동 증가



# 6. Dates

- `DATE` -  YYYY-MM-DD
- `DATETIME` -  YYYY-MM-DD HH:MI:SS
- `TIMESTAMP` -  YYYY-MM-DD HH:MI:SS
- `YEAR` -  YYYY or YY



# 7. View

1. 생성

```sql
CREATE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

2. update

```sql
CREATE OR REPLACE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

3. 삭제

```sql
DROP VIEW view_name;
```



# 8. Data types

### String Data Types

| Data type                   | Description                                                  |
| :-------------------------- | :----------------------------------------------------------- |
| CHAR(size)                  | A FIXED length string (can contain letters, numbers, and special characters). The *size* parameter specifies the column length in characters - can be from 0 to 255. Default is 1 |
| VARCHAR(size)               | A VARIABLE length string (can contain letters, numbers, and special characters). The *size* parameter specifies the maximum column length in characters - can be from 0 to 65535 |
| BINARY(size)                | Equal to CHAR(), but stores binary byte strings. The *size* parameter specifies the column length in bytes. Default is 1 |
| VARBINARY(size)             | Equal to VARCHAR(), but stores binary byte strings. The *size* parameter specifies the maximum column length in bytes. |
| TINYBLOB                    | For BLOBs (Binary Large Objects). Max length: 255 bytes      |
| TINYTEXT                    | Holds a string with a maximum length of 255 characters       |
| TEXT(size)                  | Holds a string with a maximum length of 65,535 bytes         |
| BLOB(size)                  | For BLOBs (Binary Large Objects). Holds up to 65,535 bytes of data |
| MEDIUMTEXT                  | Holds a string with a maximum length of 16,777,215 characters |
| MEDIUMBLOB                  | For BLOBs (Binary Large Objects). Holds up to 16,777,215 bytes of data |
| LONGTEXT                    | Holds a string with a maximum length of 4,294,967,295 characters |
| LONGBLOB                    | For BLOBs (Binary Large Objects). Holds up to 4,294,967,295 bytes of data |
| ENUM(val1, val2, val3, ...) | A string object that can have only one value, chosen from a list of possible values. You can list up to 65535 values in an ENUM list. If a value is inserted that is not in the list, a blank value will be inserted. The values are sorted in the order you enter them |
| SET(val1, val2, val3, ...)  | A string object that can have 0 or more values, chosen from a list of possible values. You can list up to 64 values in a SET list |

### Numeric Data Types

| Data type                     | Description                                                  |
| :---------------------------- | :----------------------------------------------------------- |
| BIT(*size*)                   | A bit-value type. The number of bits per value is specified in *size*. The *size* parameter can hold a value from 1 to 64. The default value for *size* is 1. |
| TINYINT(*size*)               | A very small integer. Signed range is from -128 to 127. Unsigned range is from 0 to 255. The *size* parameter specifies the maximum display width (which is 255) |
| BOOL                          | Zero is considered as false, nonzero values are considered as true. |
| BOOLEAN                       | Equal to BOOL                                                |
| SMALLINT(*size*)              | A small integer. Signed range is from -32768 to 32767. Unsigned range is from 0 to 65535. The *size* parameter specifies the maximum display width (which is 255) |
| MEDIUMINT(*size*)             | A medium integer. Signed range is from -8388608 to 8388607. Unsigned range is from 0 to 16777215. The *size* parameter specifies the maximum display width (which is 255) |
| INT(*size*)                   | A medium integer. Signed range is from -2147483648 to 2147483647. Unsigned range is from 0 to 4294967295. The *size* parameter specifies the maximum display width (which is 255) |
| INTEGER(*size*)               | Equal to INT(size)                                           |
| BIGINT(*size*)                | A large integer. Signed range is from -9223372036854775808 to 9223372036854775807. Unsigned range is from 0 to 18446744073709551615. The *size* parameter specifies the maximum display width (which is 255) |
| FLOAT(*size*, *d*)            | A floating point number. The total number of digits is specified in *size*. The number of digits after the decimal point is specified in the *d* parameter. This syntax is deprecated in MySQL 8.0.17, and it will be removed in future MySQL versions |
| FLOAT(*p*)                    | A floating point number. MySQL uses the *p* value to determine whether to use FLOAT or DOUBLE for the resulting data type. If *p* is from 0 to 24, the data type becomes FLOAT(). If *p* is from 25 to 53, the data type becomes DOUBLE() |
| DOUBLE(*size*, *d*)           | A normal-size floating point number. The total number of digits is specified in *size*. The number of digits after the decimal point is specified in the *d* parameter |
| DOUBLE PRECISION(*size*, *d*) |                                                              |
| DECIMAL(*size*, *d*)          | An exact fixed-point number. The total number of digits is specified in *size*. The number of digits after the decimal point is specified in the *d* parameter. The maximum number for *size* is 65. The maximum number for *d* is 30. The default value for *size* is 10. The default value for *d* is 0. |
| DEC(*size*, *d*)              | Equal to DECIMAL(size,d)                                     |

### Date and Time Data Types

| Data type        | Description                                                  |
| :--------------- | :----------------------------------------------------------- |
| DATE             | A date. Format: YYYY-MM-DD. The supported range is from '1000-01-01' to '9999-12-31' |
| DATETIME(*fsp*)  | A date and time combination. Format: YYYY-MM-DD hh:mm:ss. The supported range is from '1000-01-01 00:00:00' to '9999-12-31 23:59:59'. Adding DEFAULT and ON UPDATE in the column definition to get automatic initialization and updating to the current date and time |
| TIMESTAMP(*fsp*) | A timestamp. TIMESTAMP values are stored as the number of seconds since the Unix epoch ('1970-01-01 00:00:00' UTC). Format: YYYY-MM-DD hh:mm:ss. The supported range is from '1970-01-01 00:00:01' UTC to '2038-01-09 03:14:07' UTC. Automatic initialization and updating to the current date and time can be specified using DEFAULT CURRENT_TIMESTAMP and ON UPDATE CURRENT_TIMESTAMP in the column definition |
| TIME(*fsp*)      | A time. Format: hh:mm:ss. The supported range is from '-838:59:59' to '838:59:59' |
| YEAR             | A year in four-digit format. Values allowed in four-digit format: 1901 to 2155, and 0000. MySQL 8.0 does not support year in two-digit format. |
