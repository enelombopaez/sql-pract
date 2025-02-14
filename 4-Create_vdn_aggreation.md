
## Exercise to aggreate vdn_aggreation 

The query is the following one:

``` sql
CREATE OR REPLACE TABLE ivr_calls AS
SELECT 
    calls_ivr_id,
    calls_phone_number,
    calls_ivr_result,
    calls_vdn_label,
    calls_start_date,
    calls_end_date,
    calls_total_duration,
    calls_customer_segment,
    calls_ivr_language,
    calls_steps_module,
    calls_module_aggregation,
    document_type,
    document_identification,
    customer_phone,
    billing_account_id,
    
    -- Lógica para vdn_aggregation basada en vdn_label
    CASE 
        WHEN calls_vdn_label LIKE 'ATC%' THEN 'FRONT'
        WHEN calls_vdn_label LIKE 'TECH%' THEN 'TECH'
        WHEN calls_vdn_label = 'ABSORPTION' THEN 'ABSORPTION'
        ELSE 'RESTO'
    END AS vdn_aggregation

FROM ivr_calls;
```

To run the query properly, I have added some random values to the table(yep, again chatGPT did the boring job) with the following insert:

``` sql
INSERT INTO ivr_calls (
    calls_ivr_id, calls_phone_number, calls_ivr_result, calls_vdn_label, 
    calls_start_date, calls_end_date, calls_total_duration, calls_customer_segment, 
    calls_ivr_language, calls_steps_module, calls_module_aggregation, document_type, 
    document_identification, customer_phone, billing_account_id
) VALUES 
('CALL001', '+34600111222', 'SUCCESS', 'ATC001', '2024-02-14 10:30:00', '2024-02-14 10:35:00', 300, 'Premium', 
 'ES', 'Modulo_1', 'Agrupacion_1', 'DNI', '12345678X', '+34600111222', 'BA12345'),

('CALL002', '+34600222333', 'FAILED', 'TECH987', '2024-02-14 11:00:00', '2024-02-14 11:05:00', 300, 'Standard', 
 'EN', 'Modulo_2', 'Agrupacion_2', 'NIE', 'Y1234567Z', '+34600222333', 'BA67890'),

('CALL003', '+34600333444', 'SUCCESS', 'ABSORPTION', '2024-02-14 12:00:00', '2024-02-14 12:10:00', 600, 'Standard', 
 'ES', 'Modulo_3', 'Agrupacion_3', 'DNI', '23456789Y', '+34600333444', 'BA11223'),

('CALL004', '+34600444555', 'FAILED', 'CUSTOMLABEL', '2024-02-14 13:00:00', '2024-02-14 13:05:00', 300, 'Premium', 
 'EN', 'Modulo_4', 'Agrupacion_4', 'NIE', 'Z9876543X', '+34600444555', 'BA44556');
```

Now the query gives me what I was looking for:

~~~
|CALL123|+34600111222|SUCCESS|VDN_1001   |2024-02-14 10:30:00|2024-02-14 10:35:00|300|Premium |ES |Modulo_1|Agrupacion_1|DNI|12345678X|+34600111222|BA12345|RESTO     |
|-------|------------|-------|-----------|-------------------|-------------------|---|--------|---|--------|------------|---|---------|------------|-------|----------|
|CALL124|+34600222333|FAILED |VDN_1002   |2024-02-14 11:00:00|2024-02-14 11:05:00|300|Standard|EN |Modulo_2|Agrupacion_2|NIE|Y1234567Z|+34600222333|BA67890|RESTO     |
|CALL001|+34600111222|SUCCESS|ATC001     |2024-02-14 10:30:00|2024-02-14 10:35:00|300|Premium |ES |Modulo_1|Agrupacion_1|DNI|12345678X|+34600111222|BA12345|FRONT     |
|CALL002|+34600222333|FAILED |TECH987    |2024-02-14 11:00:00|2024-02-14 11:05:00|300|Standard|EN |Modulo_2|Agrupacion_2|NIE|Y1234567Z|+34600222333|BA67890|TECH      |
|CALL003|+34600333444|SUCCESS|ABSORPTION |2024-02-14 12:00:00|2024-02-14 12:10:00|600|Standard|ES |Modulo_3|Agrupacion_3|DNI|23456789Y|+34600333444|BA11223|ABSORPTION|
|CALL004|+34600444555|FAILED |CUSTOMLABEL|2024-02-14 13:00:00|2024-02-14 13:05:00|300|Premium |EN |Modulo_4|Agrupacion_4|NIE|Z9876543X|+34600444555|BA44556|RESTO     |

~~~
