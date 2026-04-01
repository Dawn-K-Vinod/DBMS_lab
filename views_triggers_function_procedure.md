================================================================
           VIEWS, TRIGGERS, PROCEDURES, FUNCTIONS
                     QUICK REFERENCE CARD
================================================================

----------------------------------------------------------------
1. VIEW
----------------------------------------------------------------

-- Create
CREATE VIEW view_name AS
SELECT column1, column2
FROM table_name
WHERE condition;

-- Replace existing
CREATE OR REPLACE VIEW view_name AS
SELECT column1, column2
FROM table_name
WHERE condition;

-- Use it
SELECT * FROM view_name;

-- Delete it
DROP VIEW view_name;


----------------------------------------------------------------
2. TRIGGER
----------------------------------------------------------------

CREATE OR REPLACE TRIGGER trigger_name
BEFORE / AFTER                    -- when to fire
INSERT / UPDATE / DELETE          -- what event  (can combine: INSERT OR DELETE)
ON table_name
FOR EACH ROW
IS / DECLARE                      -- optional: declare variables here
BEGIN

    -- Check which operation fired the trigger
    IF INSERTING THEN
        -- use :NEW.column  (old doesnt exist for insert)

    ELSIF UPDATING THEN
        -- use :OLD.column (before update)
        -- use :NEW.column (after update)

    ELSIF DELETING THEN
        -- use :OLD.column  (new doesnt exist for delete)

    END IF;

END;
/


-- KEY KEYWORDS
:NEW.column  →  new value being inserted / updated
:OLD.column  →  old value before update / delete
INSERTING   →  true if INSERT fired the trigger
UPDATING    →  true if UPDATE fired the trigger
DELETING    →  true if DELETE fired the trigger

-- Prevent bad data (used in BEFORE trigger)
RAISE_APPLICATION_ERROR(-20001, 'Your error message here');


----------------------------------------------------------------
3. PROCEDURE
----------------------------------------------------------------

-- Create
CREATE OR REPLACE PROCEDURE procedure_name (
    param1  IN      datatype,     -- input
    param2  OUT     datatype,     -- output
    param3  IN OUT  datatype      -- both
)
IS
    -- declare local variables here
    variable_name datatype;
BEGIN
    -- logic here
    SELECT col INTO variable_name FROM table WHERE condition;
    UPDATE ...;
    INSERT ...;
    COMMIT;
    DBMS_OUTPUT.PUT_LINE('message' || variable_name);
END;
/

-- Call it
BEGIN
    procedure_name(value1, value2);
END;
/

-- Call with OUT parameter
DECLARE
    result datatype;
BEGIN
    procedure_name(input_val, result);
    DBMS_OUTPUT.PUT_LINE(result);
END;
/


----------------------------------------------------------------
4. FUNCTION
----------------------------------------------------------------

-- Create
CREATE OR REPLACE FUNCTION function_name (
    param1  IN  datatype,
    param2  IN  datatype
)
RETURN datatype                   -- return type declared here
IS
    -- declare local variables here
    variable_name datatype;
BEGIN
    -- logic here
    RETURN value;                 -- mandatory
END;
/

-- Call inside PL/SQL block
DECLARE
    result datatype;
BEGIN
    result := function_name(param1, param2);
    DBMS_OUTPUT.PUT_LINE(result);
END;
/

-- Call directly in SELECT
SELECT function_name(column) FROM dual;


================================================================
           QUICK COMPARISON TABLE
================================================================

Feature          VIEW          TRIGGER       PROCEDURE     FUNCTION
-----------      ------        -------       ---------     --------
Purpose          Virtual       Auto-fire     Perform       Calculate
                 table         on events     actions       & return

Returns?         Rows          Nothing       Optional      Mandatory
                               (just acts)   (OUT param)   (RETURN)

Called by        SELECT        Automatic     BEGIN         x := func()
                               (no call)     proc();       SELECT func()
                                             END;          FROM dual;

Parameters?      No            No            IN / OUT      IN only
                                             / IN OUT      (mostly)

Key syntax       CREATE        AFTER INSERT  CREATE OR     RETURN
                 VIEW AS       ON table      REPLACE       datatype
                 SELECT        FOR EACH ROW  PROCEDURE     +
                                             IS            RETURN val

Key keywords     -             :NEW  :OLD    COMMIT        RETURN
                               INSERTING     SELECT INTO   value
                               UPDATING      DBMS_OUTPUT
                               DELETING


================================================================
           COMMON THINGS USED IN ALL
================================================================

-- Print output
DBMS_OUTPUT.PUT_LINE('text' || variable);

-- Read input from user
variable := &variable_name;

-- Store query result into variable
SELECT column INTO variable FROM table WHERE condition;

-- IF ELSIF structure
IF condition THEN
    ...
ELSIF condition THEN
    ...
ELSE
    ...
END IF;

-- FOR loop (used in functions like factorial)
FOR i IN 1..n LOOP
    ...
END LOOP;

-- Save changes
COMMIT;

================================================================