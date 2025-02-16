

``` sql
WITH info_by_phone AS (
    SELECT 
        ivc.calls_ivr_id,
        CASE 
            WHEN EXISTS (
                SELECT 1 
                FROM ivr_steps ivs
                WHERE ivs.ivr_id = ivc.calls_ivr_id
                AND ivs.step_name = 'CUSTOMERINFOBYPHONE.TX'
                AND ivs.step_result = 'OK'
            ) 
            THEN 1 
            ELSE 0 
        END AS info_by_phone_lg
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
    ibp.info_by_phone_lg
FROM ivr_calls ivc
LEFT JOIN info_by_phone ibp
    ON ivc.calls_ivr_id = ibp.calls_ivr_id;


```

## explanation

Use of `Exists` to verify if there is at least one value that matches the condition without doing the join, this is something I learnt from a colleague at work so I gave it a try since it seems it improves the performance