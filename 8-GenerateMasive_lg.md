``` sql
WITH masiva_modules AS (
    SELECT 
        ivc.calls_ivr_id, 
        CASE 
            WHEN ivm.module_name = 'AVERIA_MASIVA' THEN 1 
            ELSE 0 
        END AS masiva_lg
    FROM ivr_calls ivc
    LEFT JOIN ivr_modules ivm
        ON ivc.calls_ivr_id = ivm.ivr_id
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
    mm.masiva_lg
FROM ivr_calls ivc
LEFT JOIN masiva_modules mm
    ON ivc.calls_ivr_id = mm.calls_ivr_id;

```