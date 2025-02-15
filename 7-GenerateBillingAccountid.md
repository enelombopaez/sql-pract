## Generation of Billing Account ID

``` sql
WITH billing_info AS (
    SELECT 
        ivr_id, 
        MAX(billing_account_id) AS billing_account_id
    FROM ivr_steps
    WHERE step_name = 'detail' 
      AND billing_account_id IS NOT NULL
    GROUP BY ivr_id
)
SELECT 
    ivc.ivr_id,
    ivc.calls_vdn_label,
    ivc.calls_start_date,
    ivc.calls_end_date,
    ivc.calls_total_duration,
    ivc.calls_customer_segment,
    ivc.calls_ivr_language,
    COALESCE(ivc.billing_account_id, bi.billing_account_id) AS billing_account_id
FROM ivr_calls AS ivc
LEFT JOIN billing_info AS bi 
    ON ivc.ivr_id = bi.ivr_id;

```

Revisar porque no funciona

