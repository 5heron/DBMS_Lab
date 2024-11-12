```sql
-- Creating DEPT table
CREATE TABLE DEPT (
    DNO INT PRIMARY KEY,
    DNAME VARCHAR(10) NOT NULL,
    EMP_COUNT INT,
    DEPT_HOD VARCHAR(10)
);

-- Inserting a record into DEPT
INSERT INTO DEPT VALUES
(1, 'SALES', 15, 'ALI');

-- Creating EMP table
CREATE TABLE EMP (
    ENO VARCHAR(10) PRIMARY KEY CHECK(ENO LIKE 'E%'),
    ENAME VARCHAR(10) NOT NULL,
    SALARY INT CHECK(SALARY > 0),
    DNO INT,
    MGRNO VARCHAR(10),
    DOJ DATE,
    DESG VARCHAR(10),
    ADDRESS VARCHAR(10),
    CITY VARCHAR(10) CHECK(CITY IN ('KOCHI', 'CHENNAI', 'MUMBAI', 'DELHI')),
    PINCODE INT,
    FOREIGN KEY(DNO) REFERENCES DEPT(DNO)
);

-- Inserting a record into EMP
INSERT INTO EMP VALUES
('E3', 'CATHY', 160000, 1, '', '09-SEP-2001', 'MANAGER', 'ADD3', 'KOCHI', 80082);
```

### SQL Questions

1.  Which employee(s) joined department 5 after January 1, 2005?
   ```sql
   SELECT ENAME FROM EMP
   WHERE DNO=5
   AND DOJ>'01-JAN-2005';
   ```

2.  Which employee(s) are in departments not associated with department 4?
   ```sql
   SELECT ENAME FROM EMP
   WHERE DNO NOT IN (SELECT DNO FROM EMP WHERE DNO=4);
   ```

3.  Which employee(s) work in the 'SALES' department?
   ```sql
   SELECT ENAME FROM EMP, DEPT
   WHERE EMP.DNO = DEPT.DNO
   AND DNAME = 'SALES';
   ```

4.  Which employee(s) have no manager (`MGRNO IS NULL`)?
   ```sql
   SELECT ENAME FROM EMP
   WHERE MGRNO IS NULL;
   ```

5.  Which employee(s) have names that start with 'J' followed by any character and 'N' (pattern 'J_N%')?
   ```sql
   SELECT ENAME FROM EMP
   WHERE ENAME LIKE 'J_N%';
   ```

6.  Which employee(s) have a date of joining (`DOJ`) in the format '__-___-20'?
   ```sql
   SELECT ENAME FROM EMP
   WHERE DOJ LIKE '__-___-20';
   ```

7.  What is the annual salary of each employee?
   ```sql
   SELECT 'EVERY YEAR ' || ENAME || ' EARNS ' || SALARY*12 AS ANNUAL_SALARY FROM EMP;
   ```

8.  How many employees are in the `EMP` table?
   ```sql
   SELECT COUNT(ENO) FROM EMP;
   ```

9.  What are the minimum salaries for each designation?
   ```sql
   SELECT DESG, MIN(SALARY)
   FROM EMP
   GROUP BY DESG;
   ```

10.  Which employee(s) have a salary greater than the minimum salary for their department and designation and also greater than 50,000?
   ```sql
   SELECT ENAME, SALARY, DNO, DESG
   FROM EMP A
   WHERE SALARY > (SELECT MIN(SALARY) FROM EMP B
                   WHERE A.DNO = B.DNO AND A.DESG = B.DESG)
   AND SALARY > 50000
   ORDER BY DNO;
   ```

11.  Which employee(s) are designated as 'MANAGER' and which department do they belong to?
   ```sql
   SELECT ENAME, DNAME
   FROM EMP, DEPT
   WHERE EMP.DNO = DEPT.DNO
   AND DESG = 'MANAGER';
   ```

12.  What are the `DOJ` dates of each employee in `DD/MM/YYYY` format?
   ```sql
   SELECT ENAME, TO_CHAR(DOJ, 'DD/MM/YYYY') AS DOJ
   FROM EMP;
   ```