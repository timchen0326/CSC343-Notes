## **1. Introduction to SQL DDL**

- DDL is a subset of SQL used for defining database schema.
- Includes commands for creating, modifying, and deleting database objects.

## **2. Data Types in SQL**

### **Built-in Types**

- `CHAR(n)`: Fixed-length string, padded with blanks.
- `VARCHAR(n)`: Variable-length string.
- `TEXT`: Unlimited variable-length string (not standard SQL but supported in PostgreSQL).
- `INT` (`INTEGER`): Integer values.
- `FLOAT` (`REAL`): Floating-point numbers.
- `BOOLEAN`: Stores `TRUE` or `FALSE` values.
- `DATE`, `TIME`, `TIMESTAMP`: Stores date and time values.

### **User-Defined Types**

- Defined using built-in types with additional constraints.
- Example:
    
    ```sql
    CREATE DOMAIN Grade AS INT DEFAULT NULL
    CHECK (value >= 0 AND value <= 100);
    ```
    
    ```sql
    CREATE DOMAIN Campus AS VARCHAR(4) DEFAULT 'StG'
    CHECK (value IN ('StG', 'UTM', 'UTSC'));
    ```
    

## **3. Constraints in SQL**

### **Primary Keys**

- Uniquely identifies rows in a table.
- Cannot be `NULL`.
- Declared in table definition:
    
    ```sql
    CREATE TABLE Person (
        ID INTEGER PRIMARY KEY,
        name VARCHAR(25)
    );
    ```
    
- Multi-column primary key example:
    
    ```sql
    CREATE TABLE Undergrad (
        firstname VARCHAR(25),
        lastname VARCHAR(25),
        campus VARCHAR(3),
        birthdate DATE,
        PRIMARY KEY (lastname, campus)
    );
    ```
    

### **Uniqueness Constraints**

- Ensures values are unique but allows `NULL` values.
- Example:
    
    ```sql
    CREATE TABLE Ontarian (
        SIN INTEGER PRIMARY KEY,
        name VARCHAR(25),
        OHIP INTEGER UNIQUE
    );
    ```
    

### **Foreign Keys**

- References primary key of another table.
- Ensures referential integrity.
- Example:
    
    ```sql
    CREATE TABLE Took (
        sID INTEGER,
        FOREIGN KEY (sID) REFERENCES Student
    );
    ```
    
- Can reference unique columns that are not primary keys:
    
    ```sql
    CREATE TABLE People (
        SIN INTEGER PRIMARY KEY,
        name TEXT,
        OHIP TEXT UNIQUE
    );
    CREATE TABLE Volunteers (
        email TEXT PRIMARY KEY,
        OHIPnum TEXT REFERENCES People(OHIP)
    );
    ```
    

## **4. Check Constraints**

### **Column-based Check Constraints**

- Ensures values meet specified conditions.
- Example:
    
    ```sql
    CREATE TABLE Application (
        sID INTEGER,
        previous INTEGER CHECK (previous >= 0)
    );
    ```
    

### **Row-based Check Constraints**

- Applies conditions across multiple columns.
- Example:
    
    ```sql
    CREATE TABLE Student (
        sID INTEGER,
        age INTEGER, year INTEGER,
        college VARCHAR(4),
        CHECK (year = age - 18)
    );
    ```
    

### **NOT NULL Constraint**

- Ensures column values cannot be `NULL`.
- Example:
    
    ```sql
    CREATE TABLE Course (
        cNum INTEGER,
        name VARCHAR(40) NOT NULL,
        dept Department,
        wr BOOLEAN,
        PRIMARY KEY (cNum, dept)
    );
    ```
    

## **5. Cross-Table Constraints: Assertions**

- Constraints that span multiple tables.
- Example (not supported in PostgreSQL):
    
    ```sql
    CREATE ASSERTION validLoan CHECK (
        (SELECT SUM(amount) FROM Loan) < (SELECT SUM(balance) FROM Account)
    );
    ```
    
- Costly because they must be checked after every update.

## **6. Triggers**

- Automated actions executed on specific database events.
- Example trigger function:
    
    ```sql
    CREATE FUNCTION RecordWinner() RETURNS TRIGGER AS $$
    BEGIN
        IF NEW.grade >= 85 THEN
            INSERT INTO Winners VALUES (NEW.sID);
        END IF;
        RETURN NEW;
    END;
    $$ LANGUAGE plpgsql;
    ```
    
- Example trigger usage:
    
    ```sql
    CREATE TRIGGER TookUpdate
    BEFORE INSERT ON Took
    FOR EACH ROW
    EXECUTE PROCEDURE RecordWinner();
    ```
    

## **7. Reaction Policies for Foreign Keys**

### **Possible Actions**

- `CASCADE`: Propagate the change to referring table.
- `SET NULL`: Set referring column(s) to `NULL`.
- `RESTRICT`: Prevent deletion/update if references exist.

### **Example Usage**

- Define reaction policies when declaring foreign keys:
    
    ```sql
    CREATE TABLE Took (
        sID INTEGER,
        FOREIGN KEY (sID) REFERENCES Student
        ON DELETE CASCADE ON UPDATE RESTRICT
    );
    ```
    

## **8. Schema Modification (DDL Commands)**

### **Altering Tables**

- Adding a column:
    
    ```sql
    ALTER TABLE Course ADD COLUMN numSections INTEGER;
    ```
    
- Removing a column:
    
    ```sql
    ALTER TABLE Course DROP COLUMN breadth;
    ```
    

### **Dropping Tables**

- Removes table permanently:
    
    ```sql
    DROP TABLE Course;
    ```
    
- If referenced by another table, must specify `CASCADE`:
    
    ```sql
    DROP TABLE Course CASCADE;
    ```
    

## **9. SQL History & Standardization**

- **1974**: SEQUEL proposed by IBM Research.
- **1981**: First implementation by IBM.
- Standardization crucial for SQL’s adoption.
- Different vendors support extensions beyond ANSI SQL.

## **Conclusion**

- SQL DDL provides robust tools for defining database schemas.
- Proper use of constraints ensures data integrity.
- Understanding triggers and reaction policies aids in effective database design.