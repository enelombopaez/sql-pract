
## Exercise to create the details of the table `ivr_Detail`

Create table `ivr_calls`
``` sql
CREATE OR REPLACE TABLE ivr_calls (
    calls_ivr_id VARCHAR(255),
    calls_phone_number VARCHAR(255),
    calls_ivr_result VARCHAR(255),
    calls_vdn_label VARCHAR(255),
    calls_start_date TIMESTAMP,
    calls_end_date TIMESTAMP,
    calls_total_duration INT,
    calls_customer_segment VARCHAR(255),
    calls_ivr_language VARCHAR(255),
    calls_steps_module VARCHAR(255),
    calls_module_aggregation VARCHAR(255),
    document_type VARCHAR(255),
    document_identification VARCHAR(255),
    customer_phone VARCHAR(255),
    billing_account_id VARCHAR(255)
);
```

Create table `ivr_modules`

``` sql 
CREATE OR REPLACE TABLE ivr_modules (
    ivr_id VARCHAR(255),
    module_sequence INT,
    module_name VARCHAR(255),
    module_duration INT,
    module_result VARCHAR(255)
);
```


Create table `ivr_steps`

``` sql
CREATE OR REPLACE TABLE ivr_steps (
    ivr_id VARCHAR(255),
    module_sequence INT,
    step_sequence INT,
    step_name VARCHAR(255),
    step_result VARCHAR(255),
    step_description_error VARCHAR(255)
);
```

### Explanation of the queries

- Created in tembo since GCP was asking for a billing account creation and I don't want to just in case I forget the services and I get poor !

- Use the `create or replace table` since I have been doing some test in the same dataset so I was overwriting the tables in case they existed before.

- Use of `string` for any identity with words(instead of using specialized values) and `int` for numbers.

- I have requested chatGPT help to add some information to the tables so I can run a query to calculate the time of the call, these are the `inserts`:

``` sql
INSERT INTO ivr_steps (
    ivr_id, module_sequence, step_sequence, step_name, step_result, step_description_error
) VALUES 
('CALL123', 1, 1, 'Bienvenida', 'SUCCESS', NULL),
('CALL123', 1, 2, 'Ingreso de documento', 'SUCCESS', NULL),
('CALL123', 2, 1, 'Ingreso de PIN', 'SUCCESS', NULL),

('CALL124', 1, 1, 'Bienvenida', 'FAILED', 'No se detectó entrada de usuario'),
('CALL124', 2, 1, 'Ingreso de PIN', 'SUCCESS', NULL);
```

``` sql
INSERT INTO ivr_calls (
    calls_ivr_id, calls_phone_number, calls_ivr_result, calls_vdn_label, 
    calls_start_date, calls_end_date, calls_total_duration, calls_customer_segment, 
    calls_ivr_language, calls_steps_module, calls_module_aggregation, document_type, 
    document_identification, customer_phone, billing_account_id
) VALUES 
('CALL123', '+34600111222', 'SUCCESS', 'VDN_1001', '2024-02-14 10:30:00', '2024-02-14 10:35:00', 300, 'Premium', 
 'ES', 'Modulo_1', 'Agrupacion_1', 'DNI', '12345678X', '+34600111222', 'BA12345'),

('CALL124', '+34600222333', 'FAILED', 'VDN_1002', '2024-02-14 11:00:00', '2024-02-14 11:05:00', 300, 'Standard', 
 'EN', 'Modulo_2', 'Agrupacion_2', 'NIE', 'Y1234567Z', '+34600222333', 'BA67890');
```

``` sql
INSERT INTO ivr_modules (
    ivr_id, module_sequence, module_name, module_duration, module_result
) VALUES 
('CALL123', 1, 'Identificación', 60, 'SUCCESS'),
('CALL123', 2, 'Autenticación', 120, 'SUCCESS'),

('CALL124', 1, 'Identificación', 50, 'FAILED'),
('CALL124', 2, 'Autenticación', 130, 'SUCCESS');
```

To show the duration of the calls, run the following query:

``` sql
SELECT 
    calls_ivr_id,
    calls_phone_number,
    calls_start_date,
    TO_CHAR(calls_start_date, 'YYYYMMDD') AS calls_start_date_id,
    calls_end_date,
    TO_CHAR(calls_end_date, 'YYYYMMDD') AS calls_end_date_id,
    calls_total_duration,
    calls_ivr_language
FROM ivr_calls;
```

Which will give us the duration in seconds.