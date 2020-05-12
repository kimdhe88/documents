# guide

## tutorial page

[postgresql-tutorial](http://www.postgresqltutorial.com/)

## constraint type

* `UNIQUE (column_list)` –  to force the value stored in the columns listed inside the parentheses to be unique.
* `PRIMARY KEY(column_list)` – to define the primary key that consists of multiple columns.
* `CHECK (condition)` – to check a condition when inserting or updating data.
* `REFERENCES` – to constrain the value stored in the column that must exist in a column in another table.

## data type

| name | description |
| :---: | :--- |
| Boolean | ture\(t,ture,y,yes,1\), false\(f,false,n,no,0\) |
| CHAR\(n\) | char\(n\), fixed-length, blank padded |
| VARCHAR\(n\) | varchar\(n\), variable-length with limit |
| TEXT, varchar | variable unlimited length |
| NUMERIC | NUMERIC \(precision, scale\) |
| SMALLINT | 2 bytes, -32768, +32,767 |
| INTEGER | 4 bytes, -2,147,483,648, +2,147,483,647 |
| BIGINT | 8 bytes, -9,223,372,036,854,775,808, +9,223,372,036,854,775,807 |
| SMALLSERIAL | 2 bytes, 1 to 32,767 |
| SERIAL | 4 bytes, 1 to 2,147,483,647 |
| BIGSERIAL | 8 bytes, 1 to 922,337,2036,854,775,807 |
| DATE |  |
| TIMESTAMP |  |
| interval |  |
| TIME |  |
| UUID |  |
| JSON |  |
| HSTORE |  |
| Array |  |
| User-defined Data Types |  |

## command

### Help Informatino

```bash
postgres=# \?
```

### database list

```bash
postgres=# \l
                             List of databases
   Name    |  Owner   | Encoding | Collate | Ctype |   Access privileges
-----------+----------+----------+---------+-------+-----------------------
 postgres  | postgres | UTF8     | C       | C     |
 ptm       | postgres | UTF8     | C       | C     |
 template0 | postgres | UTF8     | C       | C     | =c/postgres          +
           |          |          |         |       | postgres=CTc/postgres
 template1 | postgres | UTF8     | C       | C     | =c/postgres          +
           |          |          |         |       | postgres=CTc/postgres
(4 rows)
```

### Information Commands

* relation

  ```bash
  postgres=# \d
  ```

* relation more infomation

  ```bash
  postgres=# \d+
  ```

* System table

  ```bash
  postgres=# \dS
  ```

* table

  ```bash
  postgres=# \dt
  ```

* view

  ```bash
  postgres=# \dv
  ```

### 다른 Database에 접속하기

* view

  ```bash
  postgres=# \c <database name>
  ```

### exit psql

```bash
postgres=# \q
```

### 쿼리 수정 및 실행

```bash
postgres=# \e
```

### 변수 선언하기

```bash
postgres=# \set city Seoul
postgres=# \echo :city
```

