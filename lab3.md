## ZADANIE 1
```SQL
CREATE VIEW v_wysokie_pensje AS
SELECT * FROM EMPLOYEES e WHERE e.salary > 6000;
```




## ZADANIE 2
```SQL
CREATE OR REPLACE VIEW v_wysokie_pensje AS
SELECT * FROM EMPLOYEES e WHERE e.salary > 12000;
```




## ZADANIE 3
```SQL
DROP VIEW v_wysokie_pensje;
```




## ZADANIE 4
```SQL
CREATE OR REPLACE VIEW v_departament_finanse AS
SELECT e.employee_id, e.last_name, e.first_name 
FROM EMPLOYEES e 
WHERE e.department_id =
				(SELECT department_id 
				FROM DEPARTMENTS 
				WHERE department_name = 'Finance');
```




## ZADANIE 5
```SQL
CREATE OR REPLACE VIEW v_pensje_przedzial AS
SELECT e.employee_id, e.last_name, e.first_name, e.salary, e.job_id, e.email, e.hire_date
FROM EMPLOYEES e
WHERE e.salary BETWEEN 5000 AND 12000;
```




## ZADANIE 6
### A 
```SQL
INSERT INTO v_departament_finanse (employee_id, last_name, first_name)
VALUES (900, 'Kowalski', 'Jan');
```
Nie da się utworzyć, błąd: ORA-01400
```SQL
INSERT INTO v_pensje_przedzial (employee_id, last_name, first_name, salary, job_id, email, hire_date)
VALUES (902, 'Nowak', 'Anna', 8000, 'IT_PROG', 'ANOWAK', SYSDATE);
```
Da się utworzyć

### B
```SQL
UPDATE v_departament_finanse SET first_name = 'John'  WHERE employee_id = 109;
```
Da się edytować

```SQL
UPDATE v_pensje_przedzial SET salary = 9500 WHERE employee_id = 109;
```
Da się edytować

### C
```SQL
DELETE FROM v_departament_finanse WHERE employee_id = 109; 
```
Da się usunąć

```SQL
DELETE FROM v_pensje_przedzial WHERE employee_id = 902;
```
Da się usunąć




## ZADANIE 7
```SQL
CREATE OR REPLACE VIEW v_dzialy_statystyki AS
SELECT d.department_id,
	d.department_name,
	COUNT(e.employee_id) AS liczba_pracownikow,
	ROUND(AVG(e.salary)) AS srednia_pensja,
	MAX(e.salary) AS najwyzsza_pensja
FROM DEPARTMENTS d
JOIN EMPLOYEES e ON e.department_id = d.department_id
GROUP BY d.department_id, d.department_name
HAVING COUNT(e.employee_id) >= 4;
```

### A
```SQL
INSERT INTO v_dzialy_statystyki (department_id, department_name, liczba_pracownikow, srednia_pensja, najwyzsza_pensja)
VALUES (300, 'Testowy', 5, 5000, 8000);
```
Nie da się dodać danych, błąd: ORA-01779




## ZADANIE 8
```SQL
CREATE OR REPLACE VIEW v_wysokie_pensje AS
SELECT * FROM EMPLOYEES e WHERE e.salary > 12000
WITH CHECK OPTION;
```
### I
```SQL
INSERT INTO v_wysokie_pensje (employee_id, last_name, first_name, email, hire_date, job_id, salary)
VALUES (904, 'Jankowska', 'Ewa', 'EJANKOWSKA', SYSDATE, 'IT_PROG', 8000);
```
Nie da się dodać, błąd: ORA-01402

### II
```SQL
INSERT INTO v_wysokie_pensje (employee_id, last_name, first_name, email, hire_date, job_id, salary)
VALUES (904, 'Jankowska', 'Ewa', 'EJANKOWSKA', SYSDATE, 'IT_PROG', 15000);
```
Da się dodać




## ZADANIE 9
```SQL
CREATE MATERIALIZED VIEW v_managerowie AS
SELECT e.first_name,
	e.last_name,
	d.department_name
FROM EMPLOYEES e
LEFT JOIN DEPARTMENTS d ON d.department_id = e.department_id
WHERE e.employee_id IN
				(SELECT manager_id
				FROM EMPLOYEES
				WHERE manager_id IS NOT NULL);

```




## ZADANIE 10
```SQL
CREATE OR REPLACE VIEW v_najlepiej_oplacani AS
SELECT employee_id, first_name, last_name, job_id, salary
FROM employees
ORDER BY salary DESC
FETCH FIRST 10 ROWS ONLY;
```
