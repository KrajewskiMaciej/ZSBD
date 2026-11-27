## ZADANIE 1
```SQL
SELECT employee_id, first_name, last_name, salary,
    RANK() OVER (ORDER BY salary DESC) AS ranking
FROM employees;
```




## ZADANIE 2
```SQL
SELECT employee_id, first_name, last_name, salary,
    SUM(salary) OVER () AS suma_wszystkich_pensji
FROM employees;
```




## ZADANIE 3
```SQL
SELECT e.last_name, p.product_name,
	SUM(s.price * s.quantity) OVER (PARTITION BY s.employee_id
							ORDER BY s.sale_date, s.sale_id) AS skumulowana_sprzedaz,
	RANK() OVER (ORDER BY s.price * s.quantity DESC) AS ranking_sprzedazy
FROM sales s
JOIN employees e ON e.employee_id = s.employee_id
JOIN products p ON p.product_id = s.product_id
ORDER BY e.last_name, s.sale_date;
```




## ZADANIE 4
```SQL
SELECT e.last_name, p.product_name, s.price,
	COUNT(*) OVER (PARTITION BY s.product_id, TRUNC(s.sale_date)) AS transakcje_tego_dnia,
	SUM(s.price * s.quantity) OVER (PARTITION BY s.product_id, TRUNC(s.sale_date)) AS suma_tego_dnia,
	LAG(s.price)  OVER (PARTITION BY s.product_id ORDER BY s.sale_date, s.sale_id) AS poprzednia_cena,
	LEAD(s.price) OVER (PARTITION BY s.product_id ORDER BY s.sale_date, s.sale_id) AS kolejna_cena
FROM sales s
JOIN employees e ON e.employee_id = s.employee_id
JOIN products p ON p.product_id = s.product_id
ORDER BY p.product_name, s.sale_date;
```




## ZADANIE 5
```SQL
SELECT p.product_name, s.sale_date, s.price,
	SUM(s.price * s.quantity) OVER (PARTITION BY TRUNC(s.sale_date, 'MM')) AS suma_w_miesiacu,
	SUM(s.price * s.quantity) OVER (PARTITION BY s.product_id, TRUNC(s.sale_date, 'MM')
							ORDER BY s.sale_date, s.sale_id) AS suma_rosnaca_produktu
FROM sales s
JOIN products p ON p.product_id = s.product_id
ORDER BY TRUNC(s.sale_date, 'MM'), p.product_name, s.sale_date;
```




## ZADANIE 6
```SQL
SELECT p.product_name, p.product_category,
	TO_CHAR(s22.sale_date, 'DD-MM')  AS dzien,
	s22.price AS cena_2022,
	s23.price AS cena_2023,
	s23.price - s22.price AS roznica
FROM sales s22
JOIN sales s23 ON s23.product_id = s22.product_id 
	AND TO_CHAR(s23.sale_date, 'MM-DD') = TO_CHAR(s22.sale_date, 'DD-MM')
JOIN products p ON p.product_id = s22.product_id
WHERE EXTRACT(YEAR FROM s22.sale_date) = 2022
AND EXTRACT(YEAR FROM s23.sale_date) = 2023
ORDER BY p.product_name, dzien;
```




## ZADANIE 7
```SQL
SELECT p.product_category, p.product_name, s.price,
	MIN(s.price) OVER (PARTITION BY p.product_category) AS min_cena_kategorii,
	MAX(s.price) OVER (PARTITION BY p.product_category) AS max_cena_kategorii,
	MAX(s.price) OVER (PARTITION BY p.product_category)
	- MIN(s.price) OVER (PARTITION BY p.product_category) AS roznica
FROM sales s
JOIN products p ON p.product_id = s.product_id
ORDER BY p.product_category, p.product_name;
```




## ZADANIE 8
```SQL
SELECT p.product_name, s.sale_date, s.price,
	ROUND(AVG(s.price) OVER (PARTITION BY s.product_id
							ORDER BY s.sale_date, s.sale_id
							ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING), 2)
							AS srednia_kroczaca
FROM sales s
JOIN products p ON p.product_id = s.product_id
ORDER BY p.product_name, s.sale_date;
```




## ZADANIE 9
```SQL
SELECT p.product_name, p.product_category, s.price,
	RANK() OVER (PARTITION BY p.product_category ORDER BY s.price DESC) AS ranking,
	ROW_NUMBER() OVER (PARTITION BY p.product_category ORDER BY s.price DESC) AS nr_wiersza,
	DENSE_RANK() OVER (PARTITION BY p.product_category ORDER BY s.price DESC) AS ranking_gesty
FROM sales s
JOIN products p ON p.product_id = s.product_id
ORDER BY p.product_category, s.price DESC;
```




## ZADANIE 10
```SQL
SELECT e.last_name, p.product_name, s.sale_date,
	s.price * s.quantity AS wartosc_zamowienia,
	SUM(s.price * s.quantity) OVER (PARTITION BY s.employee_id
							ORDER BY s.sale_date, s.sale_id) AS sprzedaz_rosnaca,
	RANK() OVER (ORDER BY s.price * s.quantity DESC) AS ranking_globalny
FROM sales s
JOIN employees e ON e.employee_id = s.employee_id
JOIN products  p ON p.product_id  = s.product_id
ORDER BY e.last_name, s.sale_date;
```




## ZADANIE 11
```SQL
SELECT e.first_name, e.last_name, j.job_title
FROM employees e
JOIN jobs j ON j.job_id = e.job_id
WHERE EXISTS (SELECT 1 FROM sales s WHERE s.employee_id = e.employee_id)
ORDER BY e.last_name;
```