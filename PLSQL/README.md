
## 1. Grade Calculation Using Procedure

### Program

```sql
CREATE OR REPLACE PROCEDURE grde(r IN NUMBER, g OUT VARCHAR) IS
    m1 MARKS.mark1%TYPE;
    m2 MARKS.mark2%TYPE;
    m3 MARKS.mark3%TYPE;
    m4 MARKS.mark4%TYPE;
    m5 MARKS.mark5%TYPE;
    total NUMBER(3);
    avge NUMBER(3);
BEGIN
    SELECT MARK1, MARK2, MARK3, MARK4, MARK5 INTO m1, m2, m3, m4, m5 FROM MARKS WHERE ROLL_NO = r;
    total := m1 + m2 + m3 + m4 + m5;
    avge := FLOOR(total / 5);

    IF avge >= 90 THEN
        g := 'S';
    ELSIF avge >= 85 THEN
        g := 'A+';
    ELSIF avge >= 80 THEN
        g := 'A';
    ELSIF avge >= 75 THEN
        g := 'B+';
    ELSIF avge >= 70 THEN
        g := 'B';
    ELSIF avge >= 65 THEN
        g := 'C+';
    ELSIF avge >= 60 THEN
        g := 'C';
    ELSIF avge >= 55 THEN
        g := 'D';
    ELSIF avge >= 50 THEN
        g := 'P';
    ELSE
        g := 'F';
    END IF;
EXCEPTION
    WHEN no_data_found THEN
        g := '';
END;
/
```

### Function Call

```sql
DECLARE
    r NUMBER(9) := &ROLL_NO;
    grade VARCHAR(2);
BEGIN
    grde(r, grade);

    IF grade IS NOT NULL THEN
        dbms_output.put_line('Grade: ' || grade);
    ELSE
        dbms_output.put_line('Roll No. NOT FOUND!!!');
    END IF;
END;
/
```

### Output

```sql
@procedgr.sql

Enter value for roll_no: 333333333

Grade: C
```

## 2. Salary Updation Using Function

### Program

```sql
CREATE OR REPLACE FUNCTION update_sal(eid IN VARCHAR, incsal IN NUMBER)
RETURN NUMBER IS
    net_sal NUMBER(6);
    sal EMP.SALARY%TYPE;
BEGIN
    SELECT SALARY INTO sal FROM EMP WHERE ENO = eid;
    dbms_output.put_line('Old Salary: ' || sal);

    net_sal := sal + incsal;
    UPDATE EMP SET NET_SALARY = net_sal, UPDATE_DATE = SYSDATE WHERE ENO = eid;

    RETURN net_sal;
EXCEPTION
    WHEN no_data_found THEN
        RETURN NULL;
END;
/
```

### Function Call

```sql
DECLARE
    empid VARCHAR(5) := '&ENO';
    incsal NUMBER(6) := &Salary_increment;
    netsal NUMBER(6);
BEGIN
    netsal := update_sal(empid, incsal);

    IF netsal IS NOT NULL THEN
        dbms_output.put_line('Current Salary: ' || netsal);
    ELSE
        dbms_output.put_line('Employee ID NOT FOUND!!!');
    END IF;
END;
/
```

### Output

```sql
@update_sal.sql

Enter value for eno: E3
Enter value for salary_increment: 3200

Old Salary: 6000
Current Salary: 9200

PL/SQL procedure successfully completed.
```

## 3. Salary Updation Using Cursor

### Program

```sql
CREATE OR REPLACE FUNCTION update_sal_cur(eid IN VARCHAR)
RETURN NUMBER IS
    net_sal NUMBER(7);
    sal EMP.SALARY%TYPE;
BEGIN
    SELECT SALARY INTO sal FROM EMP WHERE ENO = eid;

    IF sal > 20000 THEN
        net_sal := sal * 1.15;
    ELSIF sal > 10000 THEN
        net_sal := sal * 1.1;
    ELSE
        net_sal := sal * 1.05;
    END IF;

    RETURN net_sal;
EXCEPTION
    WHEN no_data_found THEN
        RETURN NULL;
END;
/
```

### Cursor Implementation

```sql
DECLARE
    empid VARCHAR(5);
    netsal NUMBER(7);
    CURSOR cur IS SELECT ENO FROM EMP;
BEGIN
    OPEN cur;

    IF cur%ISOPEN THEN
        LOOP
            FETCH cur INTO empid;
            EXIT WHEN cur%NOTFOUND;

            netsal := update_sal_cur(empid);

            IF netsal IS NOT NULL THEN
                UPDATE EMP SET NET_SALARY = netsal, UPDATE_DATE = SYSDATE WHERE ENO = empid;
                dbms_output.put_line('New Salary of Employee ' || empid || ': ' || netsal);
            ELSE
                dbms_output.put_line('Employee ID NOT FOUND !!!');
            END IF;
        END LOOP;
        CLOSE cur;
    ELSE
        dbms_output.put_line('Cursor NOT OPENED');
    END IF;
END;
/
```

### Output

```sql
@curfunsal.sql

New Salary of Employee E1: 34500
New Salary of Employee E2: 17600
New Salary of Employee E3: 6300
New Salary of Employee E4: 13200
New Salary of Employee E5: 26450
```

## 4. Electricity Bill Calculation Using Cursor

### Program

```sql
DECLARE
    pres BILL_DETAILS.PRESENT_READING%TYPE;
    prev BILL_DETAILS.PREVIOUS_READING%TYPE;
    units BILL_DETAILS.UNITS_CONSUMED%TYPE;
    amt BILL_DETAILS.PAYABLE_AMOUNT%TYPE;
    cno BILL_DETAILS.CONSUMER_NO%TYPE;
    CURSOR cur IS SELECT CONSUMER_NO, PRESENT_READING, PREVIOUS_READING FROM BILL_DETAILS;
BEGIN
    OPEN cur;

    IF cur%ISOPEN THEN
        LOOP
            FETCH cur INTO cno, pres, prev;
            EXIT WHEN cur%NOTFOUND;

            units := pres - prev;

            IF units <= 50 THEN
                amt := units * 5;
            ELSIF units <= 100 THEN
                amt := 50 * 5 + (units - 50) * 10;
            ELSIF units <= 200 THEN
                amt := 50 * 5 + 50 * 10 + (units - 100) * 15;
            ELSE
                amt := 50 * 5 + 50 * 10 + 100 * 15 + (units - 200) * 20;
            END IF;

            UPDATE BILL_DETAILS SET UNITS_CONSUMED = units, PAYABLE_AMOUNT = amt WHERE CONSUMER_NO = cno;
            COMMIT;
        END LOOP;
        CLOSE cur;
    ELSE
        dbms_output.put_line('Cursor NOT OPENED');
    END IF;
END;
/
```

### Output

```sql
@elecur.sql

PL/SQL procedure successfully completed.

SQL> SELECT * FROM BILL_DETAILS;

CONSUMER_NO  PRESENT_READING  PREVIOUS_READING  UNITS_CONSUMED  PAYABLE_AMOUNT
--------------------------------------------------------------------------
C1           100              35                65              400
C2           560              200               360             5450
C3           250              120               130             1200
C4           998              334               664             11530
C5           876              123               753             13310
```


