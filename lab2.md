# I
```SQL
DROP TABLE job_history CASCADE CONSTRAINTS;
DROP TABLE jobs CASCADE CONSTRAINTS;
DROP TABLE employees CASCADE CONSTRAINTS;
DROP TABLE countries CASCADE CONSTRAINTS;
DROP TABLE departments CASCADE CONSTRAINTS;
DROP TABLE locations CASCADE CONSTRAINTS;
DROP TABLE regions CASCADE CONSTRAINTS;
```



# II
```SQL
CREATE TABLE regions AS SELECT * FROM hr.regions;
CREATE TABLE countries AS SELECT * FROM hr.countries;
CREATE TABLE locations AS SELECT * FROM hr.locations;
CREATE TABLE departments AS SELECT * FROM hr.departments;
CREATE TABLE jobs AS SELECT * FROM hr.jobs;
CREATE TABLE employees AS SELECT * FROM hr.employees;
CREATE TABLE job_history AS SELECT * FROM hr.job_history;
CREATE TABLE job_grades AS SELECT * FROM hr.job_grades;
CREATE TABLE products AS SELECT * FROM hr.products;
CREATE TABLE sales AS SELECT * FROM hr.sales;
```
```SQL
ALTER TABLE regions ADD PRIMARY KEY (region_id);
ALTER TABLE countries ADD PRIMARY KEY (country_id);
ALTER TABLE locations ADD PRIMARY KEY (location_id);
ALTER TABLE departments ADD PRIMARY KEY (department_id);
ALTER TABLE jobs ADD PRIMARY KEY (job_id);
ALTER TABLE employees ADD PRIMARY KEY (employee_id);
ALTER TABLE job_history ADD PRIMARY KEY (employee_id, start_date);
ALTER TABLE job_grades ADD PRIMARY KEY (grade);
 
ALTER TABLE employees ADD UNIQUE (email);
```
```SQL
ALTER TABLE countries ADD FOREIGN KEY (region_id) REFERENCES regions (region_id);
ALTER TABLE locations ADD FOREIGN KEY (country_id) REFERENCES countries (country_id);
ALTER TABLE departments ADD FOREIGN KEY (location_id) REFERENCES locations (location_id);
ALTER TABLE departments ADD FOREIGN KEY (manager_id) REFERENCES employees (employee_id);
ALTER TABLE employees ADD FOREIGN KEY (job_id) REFERENCES jobs (job_id);
ALTER TABLE employees ADD FOREIGN KEY (department_id) REFERENCES departments (department_id);
ALTER TABLE employees ADD FOREIGN KEY (manager_id) REFERENCES employees (employee_id);
ALTER TABLE job_history ADD FOREIGN KEY (employee_id) REFERENCES employees (employee_id);
ALTER TABLE job_history ADD FOREIGN KEY (job_id) REFERENCES jobs (job_id);
ALTER TABLE job_history ADD FOREIGN KEY (department_id) REFERENCES departments (department_id);
```


# III
## ZADANIE 1
```SQL
SELECT e.last_name || ' ' || e.salary AS wynagrodzenie 
FROM EMPLOYEES e 
WHERE e.DEPARTMENT_ID IN(20,50) AND e.salary BETWEEN 2000 AND 7000 
ORDER BY e.last_name;
```



## ZADANIE 2
```SQL
SELECT e.hire_date, e.last_name, e.JOB_ID 
FROM EMPLOYEES e 
WHERE e.MANAGER_ID IS NOT NULL AND EXTRACT(YEAR FROM e.HIRE_DATE) = 2005 
ORDER BY e.LAST_NAME ;
```



## ZADANIE 3
```SQL
SELECT e.FIRST_NAME || ' ' || e.LAST_NAME AS "Imie i Nazwisko",
    e.SALARY, e.PHONE_NUMBER 
FROM EMPLOYEES e 
WHERE SUBSTR(e.LAST_NAME, 3, 1) = 'e' AND e.FIRST_NAME LIKE '%e%' 
ORDER BY e.SALARY DESC,e.PHONE_NUMBER ASC;
```



## ZADANIE 4
```SQL
SELECT imie_nazwisko, miesiace,
    CASE
        WHEN miesiace < 150 THEN salary * 0.10
        WHEN miesiace < 200 THEN salary * 0.20
        ELSE salary * 0.30
    END AS wysokosc_dodatku
FROM (SELECT first_name || ' ' || last_name AS imie_nazwisko, salary,
        ROUND(MONTHS_BETWEEN(SYSDATE, hire_date)) AS miesiace FROM employees)
ORDER BY miesiace;
```



## ZADANIE 5
```SQL
SELECT department_id,
    SUM(salary) AS suma_zarobkow,
    ROUND(AVG(salary)) AS srednia_zarobkow
FROM employees
WHERE department_id IS NOT NULL
GROUP BY department_id
HAVING MIN(salary) > 5000;
```



## ZADANIE 6
```SQL
SELECT e.last_name,
    e.department_id,
    d.department_name,
    e.job_id
FROM employees e
JOIN departments d ON d.department_id = e.department_id
JOIN locations l ON l.location_id = d.location_id
WHERE l.city = 'Toronto';
```




## ZADANIE 7
```SQL
SELECT j.first_name || ' ' || j.last_name AS pracownik,
    w.first_name || ' ' || w.last_name AS wspolpracownik
FROM employees j
JOIN employees w ON w.department_id = j.department_id AND w.employee_id <> j.employee_id
WHERE j.first_name = 'Jennifer'
ORDER BY pracownik, wspolpracownik;
```




## ZADANIE 8
```SQL
SELECT d.department_id, d.department_name
FROM departments d
WHERE NOT EXISTS 
              (SELECT 1
              FROM employees e
              WHERE e.department_id = d.department_id);
```




## ZADANIE 9
```SQL
SELECT e.first_name || ' ' || e.last_name AS imie_nazwisko,
    e.job_id,
    d.department_name,
    e.salary,
    g.grade
FROM employees e
LEFT JOIN departments d ON d.department_id = e.department_id
JOIN job_grades g ON e.salary BETWEEN g.min_salary AND g.max_salary;
```




## ZADANIE 10
```SQL
SELECT first_name, last_name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees)
ORDER BY salary DESC;
```




## ZADANIE 11
```SQL
SELECT employee_id, first_name, last_name
FROM employees
WHERE department_id IN 
              (SELECT department_id
              FROM employees
              WHERE LOWER(last_name) LIKE '%u%');
```




## ZADANIE 12
```SQL
SELECT employee_id, first_name, last_name, hire_date,
    ROUND(MONTHS_BETWEEN(SYSDATE, hire_date)) AS staz_miesiace
FROM employees
WHERE MONTHS_BETWEEN(SYSDATE, hire_date) >
              (SELECT AVG(MONTHS_BETWEEN(SYSDATE, hire_date)) FROM employees)
ORDER BY hire_date;
```




## ZADANIE 13
```SQL
SELECT d.department_name,
    COUNT(e.employee_id) AS liczba_pracownikow,
    ROUND(AVG(e.salary)) AS srednie_wynagrodzenie
FROM departments d
JOIN employees e ON e.department_id = d.department_id
GROUP BY d.department_id, d.department_name
ORDER BY liczba_pracownikow DESC;
```




## ZADANIE 14
```SQL
SELECT first_name, last_name, salary
FROM employees
WHERE salary < ALL (SELECT e.salary
              FROM employees e
              JOIN departments d ON d.department_id = e.department_id
              WHERE d.department_name = 'IT');
```




## ZADANIE 15
```SQL
SELECT d.department_id, d.department_name
FROM departments d
WHERE EXISTS (SELECT 1
              FROM employees e
              WHERE e.department_id = d.department_id
              AND e.salary > (SELECT AVG(salary) FROM employees));
```




## ZADANIE 16
```SQL
SELECT j.job_id,
    j.job_title,
    ROUND(AVG(e.salary)) AS srednie_zarobki
FROM jobs j
JOIN employees e ON e.job_id = j.job_id
GROUP BY j.job_id, j.job_title
ORDER BY srednie_zarobki DESC
FETCH FIRST 5 ROWS ONLY;
```




## ZADANIE 17
```SQL
SELECT r.region_name,
    COUNT(DISTINCT c.country_id)  AS liczba_krajow,
    COUNT(DISTINCT e.employee_id) AS liczba_pracownikow
FROM regions r
LEFT JOIN countries c ON c.region_id = r.region_id
LEFT JOIN locations l ON l.country_id = c.country_id
LEFT JOIN departments d ON d.location_id = l.location_id
LEFT JOIN employees e ON e.department_id = d.department_id
GROUP BY r.region_id, r.region_name;
```




## ZADANIE 18
```SQL
SELECT e.first_name, e.last_name, e.salary,
    m.first_name || ' ' || m.last_name AS menedzer,
    m.salary AS zarobki_menedzera
FROM employees e
JOIN employees m ON m.employee_id = e.manager_id
WHERE e.salary > m.salary;
```




## ZADANIE 19
```SQL
SELECT nr_miesiaca, miesiac, COUNT(*) AS liczba_pracownikow
FROM (SELECT EXTRACT(MONTH FROM hire_date) AS nr_miesiaca,
    TO_CHAR(hire_date, 'fmMonth') AS miesiac
    FROM employees)
GROUP BY nr_miesiaca, miesiac
ORDER BY nr_miesiaca;
```




## ZADANIE 20
```SQL
SELECT d.department_name, 
    ROUND(AVG(e.salary)) AS srednie_wynagrodzenie
FROM departments d
JOIN employees e ON e.department_id = d.department_id
GROUP BY d.department_id, d.department_name
ORDER BY srednie_wynagrodzenie DESC
FETCH FIRST 3 ROWS ONLY;
```