
---

# 9.Client Audit Trigger

**Question:** How can you create a trigger to log updates and deletions on the `client_master` table?

---

## Schema

### Tables
```sql
CREATE TABLE client_master(
    client_no NUMBER(4) PRIMARY KEY,
    name VARCHAR2(20),
    balance_due NUMBER(9),
    address VARCHAR2(20)
);

CREATE TABLE audit_client(
    client_no NUMBER(4),
    name VARCHAR2(20),
    balance_due NUMBER(9),
    operation VARCHAR2(20),
    operation_date DATE
);
```

## Trigger to Log Changes
This trigger logs each update or deletion in `audit_client`:
```sql
CREATE OR REPLACE TRIGGER audit_operation
    AFTER UPDATE OR DELETE ON client_master
    FOR EACH ROW
BEGIN
    IF UPDATING THEN
        INSERT INTO audit_client VALUES(:OLD.client_no, :OLD.name, :OLD.balance_due, 'UPDATE', SYSDATE);
    ELSIF DELETING THEN
        INSERT INTO audit_client VALUES(:OLD.client_no, :OLD.name, :OLD.balance_due, 'DELETE', SYSDATE);
    END IF;
END;
/
```

## Example Operations

- **Update:** `UPDATE client_master SET name = 'John' WHERE client_no = 204;`
- **Delete:** `DELETE FROM client_master WHERE client_no = 205;`

**Audit Log Sample:**
```
CLIENT_NO  NAME   BALANCE_DUE  OPERATION  OPERATION_DATE
204        Kevin  1500         UPDATE     21-OCT-24
205        David  900          DELETE     21-OCT-24
```

Here's a more concise version with a rephrased question:

---

# 10.Bank Trigger and Sequence

**Question:** How can you create a trigger to ensure that a customer cannot have more than three accounts?

---

## Database Schema

### Branch Table
```sql
CREATE TABLE BRANCH(
    BRANCH_ID INT PRIMARY KEY,
    BR_CITY VARCHAR(10)
);
```

### Customer Table
```sql
CREATE TABLE CUSTOMER(
    NAME VARCHAR(10) PRIMARY KEY,
    ADDRESS VARCHAR(10)
);
```

### Account Table with Constraints
```sql
CREATE TABLE ACCOUNT(
    NAME VARCHAR(10),
    A_NO INT PRIMARY KEY,
    BALANCE INT CHECK(BALANCE >= 500),
    BRANCH_ID INT,
    FOREIGN KEY(NAME) REFERENCES CUSTOMER(NAME),
    FOREIGN KEY(BRANCH_ID) REFERENCES BRANCH(BRANCH_ID)
);
```

### Sequence for Account Numbers
```sql
CREATE SEQUENCE seq1
    INCREMENT BY 1
    START WITH 1001
    MAXVALUE 9999
    NOCYCLE
/
```

## Insert Statements

```sql
INSERT INTO CUSTOMER VALUES('Arun','Loc1');
INSERT INTO CUSTOMER VALUES('Sheron','Address1');
INSERT INTO BRANCH VALUES(1, 'THRISSUR');
```

## Trigger to Limit Accounts

This trigger ensures each customer can have no more than three accounts:
```sql
CREATE OR REPLACE TRIGGER l_acc
    BEFORE INSERT ON ACCOUNT
    FOR EACH ROW
DECLARE
    acc_count INT;
BEGIN
    SELECT COUNT(*) INTO acc_count FROM ACCOUNT WHERE name = :NEW.name;

    IF acc_count >= 3 THEN
        RAISE_APPLICATION_ERROR(-20001, 'A customer cannot have more than 3 accounts');
    END IF;
END;
/
```

## Example Insertion with Trigger in Action

```sql
INSERT INTO ACCOUNT VALUES('Arun', seq1.NEXTVAL, 2000, 1);
-- Further insertions will be blocked after 3 accounts per customer
```
