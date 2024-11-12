
## Table Creation

```sql
-- Creating STUD table
CREATE TABLE STUD (
    STDNO CHAR(9) PRIMARY KEY,
    LNAME VARCHAR(10),
    FNAME VARCHAR(10),
    ADDRESS VARCHAR(10),
    CITY VARCHAR(10),
    STATE CHAR(2),
    PCODE CHAR(6)
);

-- Creating INSTR table
CREATE TABLE INSTR (
    IID CHAR(5) PRIMARY KEY,
    ILNAME VARCHAR(10),
    IFNAME VARCHAR(10),
    IPHONE CHAR(10)
);

-- Creating COURSE table
CREATE TABLE COURSE (
    CCODE CHAR(6) PRIMARY KEY,
    CTITLE VARCHAR(10),
    CHRS NUMBER(2,0)
);

-- Creating SECTION table
CREATE TABLE SECTION (
    SID CHAR(7),
    TIME_OFFERED VARCHAR(5),
    DAYS_OFFERED VARCHAR(5),
    SECTION_ROOM VARCHAR(5),
    CLS_SIZE NUMBER(3,0) CHECK(CLS_SIZE >= 0),
    NO_ENROLLED NUMBER(3,0) CHECK(NO_ENROLLED >= 0),
    IID CHAR(5),
    CCODE CHAR(6),
    PRIMARY KEY (SID, TIME_OFFERED),
    FOREIGN KEY (IID) REFERENCES INSTR (IID),
    FOREIGN KEY (CCODE) REFERENCES COURSE (CCODE)
);

-- Creating ENROLLMENT table
CREATE TABLE ENROLLMENT (
    STDNO CHAR(9),
    SID CHAR(7),
    GRADE VARCHAR(2),
    TIME_OFFERED VARCHAR(5),
    PRIMARY KEY (STDNO, SID),
    FOREIGN KEY (STDNO) REFERENCES STUD (STDNO),
    FOREIGN KEY (SID, TIME_OFFERED) REFERENCES SECTION (SID, TIME_OFFERED)
);
```

## SQL Questions and Answers

1. **Modify the `GRADE` column in the `ENROLLMENT` table to increase its length.**
   ```sql
   ALTER TABLE ENROLLMENT
   MODIFY GRADE VARCHAR(4);
   ```

2. **Add a new column `INSTR_MAILID` to the `INSTR` table.**
   ```sql
   ALTER TABLE INSTR
   ADD INSTR_MAILID VARCHAR(10);
   ```

3. **Create a view `INSTRUCTOR_SECTIONS_VIEW` combining information from instructors, sections, and courses.**
   ```sql
   CREATE VIEW INSTRUCTOR_SECTIONS_VIEW AS
   SELECT I.ILNAME, I.IFNAME, S.TIME_OFFERED, S.DAYS_OFFERED, S.SECTION_ROOM, S.CCODE, C.CTITLE
   FROM INSTR I
   JOIN SECTION S ON I.IID = S.IID
   JOIN COURSE C ON C.CCODE = S.CCODE;
   ```

4. **Insert a new student record into the `STUD` table.**
   ```sql
   INSERT INTO STUD VALUES ('111111111', 'SMITH', 'JOHN', '123MAINST', 'COCHIN', 'KL', '680013');
   ```

5. **Insert a new course into the `COURSE` table.**
   ```sql
   INSERT INTO COURSE VALUES ('CSE240', 'WEBDESIGN', 4);
   ```

6. **Insert a new instructor and a new section record.**
   ```sql
   INSERT INTO INSTR VALUES ('11113', 'DOE', 'JOHN', '9374519834', 'JD@M.COM');
   INSERT INTO SECTION VALUES ('3011255', '2-4', 'MW', 'BL102', 35, 0, '11113', 'CSE240');
   ```

7. **Insert a record into `ENROLLMENT` with a null grade.**
   ```sql
   INSERT INTO ENROLLMENT VALUES ('111111111', '3011255', NULL, '2-4');
   ```

8. **Update the class size in `SECTION` by increasing it by 10%.**
   ```sql
   UPDATE SECTION
   SET CLS_SIZE = CLS_SIZE * 1.1;
   ```

9. **Update the timing and days offered for a specific section.**
   ```sql
   UPDATE SECTION
   SET TIME_OFFERED = '2-4', DAYS_OFFERED = 'MW'
   WHERE SID = '3011236';
   ```

10. **Delete a specific section from `SECTION`, maintaining referential integrity.**
    ```sql
    DELETE FROM ENROLLMENT WHERE SID = '3011255';
    DELETE FROM SECTION WHERE SID = '3011255';
    ```

11. **Attempt to delete a course from `COURSE` that has existing dependencies.**
    ```sql
    DELETE FROM COURSE WHERE CCODE = 'CSE240';
    ```

12. **Select sections with a class size between 40 and 50 and offered on days starting with 'M'.**
    ```sql
    SELECT SID, TIME_OFFERED, DAYS_OFFERED, CCODE
    FROM SECTION
    WHERE CLS_SIZE BETWEEN 40 AND 50
    AND DAYS_OFFERED LIKE 'M%';
    ```

13. **Select courses with 4 credit hours and order by course code.**
    ```sql
    SELECT * FROM COURSE
    WHERE CHRS = 4
    ORDER BY CCODE DESC;
    ```

14. **Calculate the total class size grouped by instructor.**
    ```sql
    SELECT IID, SUM(CLS_SIZE) AS TOTAL_CLASS_SIZE
    FROM SECTION
    GROUP BY IID
    ORDER BY TOTAL_CLASS_SIZE;
    ```

15. **Select students who have grades 'B+', 'B', or 'B-'.**
    ```sql
    SELECT STDNO, SID, GRADE
    FROM ENROLLMENT
    WHERE GRADE IN ('B+', 'B', 'B-');
    ```

16. **Find the average class size for each course.**
    ```sql
    SELECT CCODE, AVG(CLS_SIZE) AS AVG_CLASS_SIZE
    FROM SECTION
    GROUP BY CCODE
    ORDER BY AVG_CLASS_SIZE;
    ```

17. **Join `INSTR`, `SECTION`, and `COURSE` to list instructors, sections, and course titles.**
    ```sql
    SELECT I.ILNAME, I.IFNAME, S.TIME_OFFERED, S.DAYS_OFFERED, S.SECTION_ROOM, S.CCODE, C.CTITLE
    FROM SECTION S
    JOIN INSTR I ON S.IID = I.IID
    JOIN COURSE C ON S.CCODE = C.CCODE;
    ```

18. **Retrieve student first names, last names, section IDs, and grades by joining `ENROLLMENT` and `STUD`.**
    ```sql
    SELECT FNAME, LNAME, SID, GRADE
    FROM ENROLLMENT
    JOIN STUD ON ENROLLMENT.STDNO = STUD.STDNO;
    ```

19. **Find the first names of students who are not enrolled in any courses.**
    ```sql
    SELECT FNAME FROM STUD
    WHERE STDNO NOT IN (SELECT STDNO FROM ENROLLMENT);
    ```
