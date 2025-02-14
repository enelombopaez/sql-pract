

## Generation of Document type and document identification

``` sql
WITH CI AS (
    SELECT 
        calls_ivr_id,
        -- Selección del primer tipo de documento y su identificación para la llamada
        MIN(document_type) AS document_type,
        MIN(document_identification) AS document_identification,
        customer_phone,
        billing_account_id
    FROM ivr_calls
    WHERE document_type IS NOT NULL 
      AND document_identification IS NOT NULL
    GROUP BY calls_ivr_id, customer_phone, billing_account_id
)
-- Inserción de los datos en ivr_calls
INSERT INTO ivr_calls (
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
    billing_account_id
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
    ci.document_type,
    ci.document_identification,
    ci.customer_phone,
    ci.billing_account_id
FROM ivr_calls ivc
LEFT JOIN CI ci 
    ON ivc.calls_ivr_id = ci.calls_ivr_id;
```

