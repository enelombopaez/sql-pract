## Generation of Clean Fuction

``` sql
CREATE OR REPLACE FUNCTION limpiar_entero(valuet INTEGER)
RETURNS INTEGER AS $$
BEGIN
    RETURN COALESCE(valuet, -999999);
END;
$$ LANGUAGE plpgsql;

```

## Explanation

- The function receives a parameter tipe INT named valuet, the COALESCE function will give the first not NULL value from the args,, if the value is null, it will give -99999

- Since postgreSQL can receive functions in different languages I have added the `plpgsql` since it's the most common one from what I have found 