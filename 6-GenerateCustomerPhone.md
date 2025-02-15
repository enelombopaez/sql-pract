## Generation of CustomerPhone


``` sql
WITH customer_info AS (
    SELECT 
        ivr_id, 
        MAX(customer_phone) AS customer_phone
    FROM ivr_steps
    WHERE step_name = 'detail' 
      AND customer_phone IS NOT NULL
    GROUP BY ivr_id
)
SELECT 
    ivc.*,
    COALESCE(ivc.calls_phone_number, ci.customer_phone) AS calls_phone_number
FROM ivr_calls AS ivc
LEFT JOIN customer_info AS ci 
    ON ivc.ivr_id = ci.ivr_id;
```

