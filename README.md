# goit-rdb-fp

1.
<img width="1919" height="1032" alt="t1" src="https://github.com/user-attachments/assets/7b8ac273-6adc-4984-99e0-6f8b50c45b57" />
2.
<img width="1915" height="1029" alt="t2" src="https://github.com/user-attachments/assets/cb92108a-9d2c-4357-88f7-48b19cfa0e35" />

<img width="1918" height="1027" alt="t3" src="https://github.com/user-attachments/assets/1cb3e8c3-389c-4a5d-917d-b71029f91e93" />

<img width="1919" height="1031" alt="t4" src="https://github.com/user-attachments/assets/f54ab1df-6373-44b2-824a-4be8dd679d1e" />

<img width="1919" height="1032" alt="t5" src="https://github.com/user-attachments/assets/a314ec9b-7f69-4da9-b005-9e947e56704f" />

<img width="1913" height="1030" alt="t5 1" src="https://github.com/user-attachments/assets/644ebb7b-64d3-451c-abc5-5d312ab456e9" />
3.
<img width="1919" height="1032" alt="t6" src="https://github.com/user-attachments/assets/20070eae-b5c1-49b8-a4b9-2c6d345358b2" />
4.
<img width="1919" height="1032" alt="t7" src="https://github.com/user-attachments/assets/c3cec65a-0565-4a2e-88a1-1e8aba325703" />
5.
<img width="1919" height="1032" alt="t8" src="https://github.com/user-attachments/assets/0a766779-9736-44fd-b283-fbb8fef212a7" />

[goit-rdb-fp(text).txt](https://github.com/user-attachments/files/25830660/goit-rdb-fp.text.txt)
CREATE SCHEMA IF NOT EXISTS pandemic;
USE pandemic;

SELECT COUNT(*) AS total_rows
FROM infectious_cases;

CREATE TABLE IF NOT EXISTS entities (
    id INT AUTO_INCREMENT PRIMARY KEY,
    entity VARCHAR(255) NOT NULL,
    code VARCHAR(50) NOT NULL,
    UNIQUE(entity, code)
);
SELECT * FROM entities;

INSERT INTO entities (entity, code)
SELECT DISTINCT Entity, Code
FROM infectious_cases
WHERE Entity IS NOT NULL
  AND Code IS NOT NULL
  AND Entity <> ''
  AND Code <> '';
  SELECT * FROM entities;

CREATE TABLE IF NOT EXISTS infectious_cases_normalized (
    id INT AUTO_INCREMENT PRIMARY KEY,
    entity_id INT NOT NULL,
    year INT NOT NULL,
    number_yaws VARCHAR(50),
    polio_cases VARCHAR(50),
    cases_guinea_worm VARCHAR(50),
    number_rabies VARCHAR(50),
    number_malaria VARCHAR(50),
    number_hiv VARCHAR(50),
    number_tuberculosis VARCHAR(50),
    number_smallpox VARCHAR(50),
    number_cholera_cases VARCHAR(50),
    FOREIGN KEY (entity_id) REFERENCES entities(id)
);
SELECT * FROM infectious_cases_normalized;

INSERT INTO infectious_cases_normalized (
    entity_id,
    year,
    number_yaws,
    polio_cases,
    cases_guinea_worm,
    number_rabies,
    number_malaria,
    number_hiv,
    number_tuberculosis,
    number_smallpox,
    number_cholera_cases
)
SELECT 
    e.id,
    ic.Year,
    ic.Number_yaws,
    ic.polio_cases,
    ic.cases_guinea_worm,
    ic.Number_rabies,
    ic.Number_malaria,
    ic.Number_hiv,
    ic.Number_tuberculosis,
    ic.Number_smallpox,
    ic.Number_cholera_cases
FROM infectious_cases ic
JOIN entities e
    ON ic.Entity = e.entity
   AND ic.Code = e.code;
   SELECT * FROM infectious_cases_normalized;

SELECT 
    e.id AS entity_id,
    e.entity,
    e.code,
    AVG(CAST(icn.number_rabies AS DECIMAL(20,2))) AS avg_rabies,
    MIN(CAST(icn.number_rabies AS DECIMAL(20,2))) AS min_rabies,
    MAX(CAST(icn.number_rabies AS DECIMAL(20,2))) AS max_rabies,
    SUM(CAST(icn.number_rabies AS DECIMAL(20,2))) AS sum_rabies
FROM infectious_cases_normalized icn
JOIN entities e
    ON icn.entity_id = e.id
WHERE icn.number_rabies IS NOT NULL
  AND icn.number_rabies <> ''
GROUP BY e.id, e.entity, e.code
ORDER BY avg_rabies DESC
LIMIT 10;

SELECT
    year,
    MAKEDATE(year, 1) AS year_start_date,
    CURDATE() AS today_date,
    TIMESTAMPDIFF(YEAR, MAKEDATE(year, 1), CURDATE()) AS year_difference
FROM infectious_cases_normalized
LIMIT 10;

DROP FUNCTION IF EXISTS year_diff_from_current;

DELIMITER //

CREATE FUNCTION year_diff_from_current(input_year INT)
RETURNS INT
DETERMINISTIC
BEGIN
    RETURN TIMESTAMPDIFF(
        YEAR,
        MAKEDATE(input_year, 1),
        CURDATE()
    );
END //

DELIMITER ;

SELECT
    year,
    year_diff_from_current(year) AS year_difference
FROM infectious_cases_normalized
LIMIT 10;
