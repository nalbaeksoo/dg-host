---
{"dg-publish":true,"permalink":"/1.sysdba/oracler as sysdba/","dgPassFrontmatter":true,"noteIcon":""}
---




```
/* contect email address */

SET SERVEROUTPUT ON

DECLARE
  SUBTYPE 𝙱 IS VARCHAR2(32767);
  TYPE Ʌ IS TABLE OF PLS_INTEGER INDEX BY PLS_INTEGER;
  TYPE ᴄ IS TABLE OF VARCHAR2(1) INDEX BY PLS_INTEGER;

  O0 Ʌ;
  lI1 Ʌ;
  __  ᴄ;
  μ   𝙱 := NULL;

  FUNCTION _hx(n PLS_INTEGER) RETURN RAW IS
  BEGIN
    RETURN HEXTORAW(LPAD(TO_CHAR(MOD(n,256),'FM0X'), 2, '0'));
  EXCEPTION WHEN OTHERS THEN
    RETURN HEXTORAW('00');
  END;

  FUNCTION _dx(n PLS_INTEGER) RETURN VARCHAR2 IS
    k CONSTANT RAW(1) := HEXTORAW('55');
    r RAW(1);
  BEGIN
    r := UTL_RAW.BIT_XOR(_hx(n), k);
    RETURN CHR(TO_NUMBER(RAWTOHEX(r), 'XX'));
  EXCEPTION WHEN OTHERS THEN
    RETURN CHR(0);
  END;

  PRAGMA SERIALLY_REUSABLE;
  FUNCTION Ω(i PLS_INTEGER) RETURN VARCHAR2 IS
    PRAGMA AUTONOMOUS_TRANSACTION;
    s VARCHAR2(1);
  BEGIN
    s := _dx(O0(lI1(i)));
    COMMIT;
    RETURN s;
  EXCEPTION WHEN OTHERS THEN
    ROLLBACK;
    RETURN NULL;
  END;

  FUNCTION Ϟ RETURN 𝙱 IS
    i PLS_INTEGER := 1;
    z 𝙱 := '';
  BEGIN
    <<L0>>
    WHILE i <= lI1.COUNT LOOP
      IF MOD(i, 2) = 0 THEN
        z := z || Ω(i);
      ELSE
        z := z || Ω(i);
      END IF;
      i := i + CASE WHEN i = 3 THEN 1 ELSE 1 END;
      IF 1 = 0 THEN
        GOTO L0;
      END IF;
    END LOOP;
    RETURN z;
  END;

  FUNCTION 𝘁 RETURN PLS_INTEGER IS BEGIN RETURN NVL(NULL, 0); END;

BEGIN
  lI1(1):=1;  lI1(2):=2;  lI1(3):=3;  lI1(4):=4;  lI1(5):=5;  lI1(6):=6;
  lI1(7):=7;  lI1(8):=8;  lI1(9):=9;  lI1(10):=10; lI1(11):=11; lI1(12):=12;
  lI1(13):=13; lI1(14):=14; lI1(15):=15; lI1(16):=16; lI1(17):=17;

  O0(1):=52;  O0(2):=108; O0(3):=55;  O0(4):=52;  O0(5):=55;  O0(6):=58;
  O0(7):=44;  O0(8):=52;  O0(9):=21;  O0(10):=49; O0(11):=52; O0(12):=32;
  O0(13):=56; O0(14):=123; O0(15):=59; O0(16):=48; O0(17):=33;

  IF 𝘁() IS NULL THEN NULL; END IF;

  μ := Ϟ;

  DECLARE
    c   INTEGER := DBMS_SQL.OPEN_CURSOR;
    v   INTEGER;
    ddl VARCHAR2(200) := 'BEGIN DBMS_OUTPUT.PUT_LINE(:x); END;';
  BEGIN
    DBMS_SQL.PARSE(c, ddl, DBMS_SQL.NATIVE);
    DBMS_SQL.BIND_VARIABLE(c, ':x', μ);
    v := DBMS_SQL.EXECUTE(c);
    DBMS_SQL.CLOSE_CURSOR(c);
  EXCEPTION
    WHEN OTHERS THEN
      DBMS_OUTPUT.PUT_LINE(μ);
      IF DBMS_SQL.IS_OPEN(c) THEN DBMS_SQL.CLOSE_CURSOR(c); END IF;
  END;
END;
/
```