``` sql
WITH info_by_dni AS (
    SELECT 
        ivc.calls_ivr_id,
        CASE 
            WHEN EXISTS (
                SELECT 1 
                FROM ivr_steps ivs
                WHERE ivs.ivr_id = ivc.calls_ivr_id
                AND ivs.step_name = 'CUSTOMERINFOBYDNI.TX'
                AND ivs.step_result = 'OK'
            ) 
            THEN 1 
            ELSE 0 
        END AS info_by_dni_lg
    FROM ivr_calls ivc
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
    ibd.info_by_dni_lg
FROM ivr_calls ivc
LEFT JOIN info_by_dni ibd
    ON ivc.calls_ivr_id = ibd.calls_ivr_id;

```