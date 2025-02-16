``` sql
WITH calls_24h AS (
    SELECT 
        ivc1.calls_ivr_id,
        ivc1.calls_phone_number,
        CASE 
            WHEN EXISTS (
                SELECT 1 
                FROM ivr_calls ivc2
                WHERE ivc2.calls_phone_number = ivc1.calls_phone_number
                AND ivc2.calls_start_date BETWEEN (ivc1.calls_start_date - INTERVAL '24 hours') 
                                             AND ivc1.calls_start_date
                AND ivc2.calls_ivr_id <> ivc1.calls_ivr_id
            ) 
            THEN 1 
            ELSE 0 
        END AS repeated_phone_24H,
        CASE 
            WHEN EXISTS (
                SELECT 1 
                FROM ivr_calls ivc2
                WHERE ivc2.calls_phone_number = ivc1.calls_phone_number
                AND ivc2.calls_start_date BETWEEN ivc1.calls_start_date 
                                             AND (ivc1.calls_start_date + INTERVAL '24 hours')
                AND ivc2.calls_ivr_id <> ivc1.calls_ivr_id
            ) 
            THEN 1 
            ELSE 0 
        END AS cause_recall_phone_24H
    FROM ivr_calls ivc1
)
SELECT 
    ivc.calls_ivr_id,
    ivc.calls_phone_number,
    ivc.calls_ivr_result,
    ivc.calls_vdn_label,
    ivc.calls_start_date,
    ivc.calls_end_date,
    ivc.calls_total_duration,
    ivc.calls_customer_segment,
    ivc.calls_ivr_language,
    ivc.calls_steps_module,
    ivc.calls_module_aggregation,
    ivc.document_type,
    ivc.document_identification,
    ivc.billing_account_id,
    ivc.customer_phone,
    c24.repeated_phone_24H,
    c24.cause_recall_phone_24H
FROM ivr_calls ivc
LEFT JOIN calls_24h c24
    ON ivc.calls_ivr_id = c24.calls_ivr_id;



```


### Comprobation

``` sql
WITH calls_24h AS (
    SELECT 
        ivc1.calls_ivr_id,
        ivc1.calls_phone_number,
        CASE 
            WHEN EXISTS (
                SELECT 1 
                FROM ivr_calls ivc2
                WHERE ivc2.calls_phone_number = ivc1.calls_phone_number
                AND ivc2.calls_start_date BETWEEN (ivc1.calls_start_date - INTERVAL '24 hours') 
                                             AND ivc1.calls_start_date
                AND ivc2.calls_ivr_id <> ivc1.calls_ivr_id
            ) 
            THEN 1 
            ELSE 0 
        END AS repeated_phone_24H,
        CASE 
            WHEN EXISTS (
                SELECT 1 
                FROM ivr_calls ivc2
                WHERE ivc2.calls_phone_number = ivc1.calls_phone_number
                AND ivc2.calls_start_date BETWEEN ivc1.calls_start_date 
                                             AND (ivc1.calls_start_date + INTERVAL '24 hours')
                AND ivc2.calls_ivr_id <> ivc1.calls_ivr_id
            ) 
            THEN 1 
            ELSE 0 
        END AS cause_recall_phone_24H
    FROM ivr_calls ivc1
)
SELECT 
    calls_phone_number, 
    repeated_phone_24H, 
    cause_recall_phone_24H
FROM calls_24h
ORDER BY repeated_phone_24H DESC, cause_recall_phone_24H DESC;
``` 

output:

~~~
|calls_phone_number|repeated_phone_24h|cause_recall_phone_24h|
|------------------|------------------|----------------------|
|34600111222       |1                 |1                     |
|34600111222       |1                 |1                     |
|34600222333       |1                 |1                     |
|34600222333       |1                 |1                     |
|34600111222       |1                 |1                     |
|34600111222       |1                 |1                     |


~~~