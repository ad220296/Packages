# 📦 2.3 Packages in PL/SQL

Zu:
- 📘 [Records & Rowtypes](https://github.com/ad220296/Records)
- 🧩 [Nested Tables (Array & Hashed)](https://github.com/ad220296/Nested_Tables)

## 🔍 Einführung

Ein **Package** in PL/SQL ist eine Sammlung von logisch zusammengehörenden **Prozeduren, Funktionen, Variablen und Typdefinitionen**. Es besteht aus zwei Teilen:

- **Package Header**: öffentlich sichtbare Deklarationen ("Schnittstelle")
- **Package Body**: Implementierung ("Logik", privat)

📌 Ziel: Wiederverwendbarkeit, Struktur und Kapselung von Code.

---

## 🧠 Aufbau eines Packages

### 🔹 Package Header (Schnittstelle)
```sql
CREATE OR REPLACE PACKAGE my_package AS
    PROCEDURE say_hello(name VARCHAR2);
    counter NUMBER;
END my_package;
```

### 🔸 Package Body (Implementierung)
```sql
CREATE OR REPLACE PACKAGE BODY my_package AS
    counter NUMBER := 0;

    PROCEDURE say_hello(name VARCHAR2) IS
    BEGIN
        counter := counter + 1;
        dbms_output.put_line('Hallo, ' || name || ' (' || counter || ')');
    END;
END my_package;
```

📌 Die Prozedur ist **öffentlich sichtbar**, `counter` **nur innerhalb des Bodys** verwendbar, obwohl er im Header ebenfalls deklariert werden kann.

---

## 🔄 Lebensdauer von Package-Variablen

- Package-Variablen bleiben **für die Dauer der Session** bestehen
- Ideal für globale Zustände, Zähler, Konfigurationen

```sql
-- Variable zählt, wie oft say_hello aufgerufen wurde
EXEC my_package.say_hello('Anna');
EXEC my_package.say_hello('Bernd');
```

---

## 📘 Verwendung von Typen im Package

### 🔹 Beispiel: Record-Typ im Header (öffentlich)
```sql
CREATE OR REPLACE PACKAGE emp_pkg AS
    TYPE emp_rec_t IS RECORD (empno NUMBER, sal NUMBER);
    FUNCTION calc_bonus(e IN emp_rec_t) RETURN NUMBER;
END;
```

### 🔸 Body mit Funktion
```sql
CREATE OR REPLACE PACKAGE BODY emp_pkg AS
    FUNCTION calc_bonus(e IN emp_rec_t) RETURN NUMBER IS
    BEGIN
        RETURN e.sal * 0.1;
    END;
END;
```

---

## 📚 Beispiel: Verwendung
```sql
DECLARE
    my_emp emp_pkg.emp_rec_t;
    bonus NUMBER;
BEGIN
    my_emp.empno := 1001;
    my_emp.sal := 2500;

    bonus := emp_pkg.calc_bonus(my_emp);
    dbms_output.put_line('Bonus: ' || bonus);
END;
```

---

## 🧾 Sichtbarkeit & Organisation

| Element          | Deklaration im Header | Nur im Body | Verwendung möglich in… |
|------------------|------------------------|-------------|--------------------------|
| Funktion/Prozedur| ✅                    | ✅          | überall (Header nötig)   |
| Variable         | ✅                    | ✅          | Session, Prozeduren      |
| Record-Typ       | ✅                    | ✅          | Andere Pakete / Blöcke   |
| Nested Table     | ✅                    | ✅          | BULK / Cursor / Trigger   |
| Exception        | ✅                    | ✅          | Globale Fehlerbehandlung  |

---

## ✅ Empfehlung

| Ziel / Verwendung                          | Empfehlung                       |
|-------------------------------------------|----------------------------------|
| Wiederverwendbare Logik                   | Package Header + Body            |
| Variablen über mehrere Aufrufe behalten   | Package-Variable im Body         |
| Typen für Übergabe zentral deklarieren    | Record / Table-Typ im Header     |
| Fehler zentral verwalten                  | Exception im Package definieren  |

---

