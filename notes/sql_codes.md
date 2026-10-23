

```table-of-contents
```

# Metabase
## Select
```sql
SELECT  
"Sales"."table_1"."id" AS "id",  
"Sales"."table_1"."company_id" AS "company_id"  
FROM  
"Sales"."table_1"
```

```sql
SELECT DISTINCT
  "Sales"."table_1"."company_id" AS "company_id"
FROM
  "Sales"."table_1"
WHERE
  "Sales"."table_1"."company_id" IS NOT NULL
ORDER BY
  "company_id"
```

**`COALESCE(...)`**: This is a standard SQL function that evaluates a list of values from left to right and returns the **first value that is not NULL**.
```sql
-- Combine two potential name columns into a single output
-- using the `source_1` name if it exists
-- and falling back to the `source_2` name if it is empty (NULL).
SELECT
COALESCE(source_1."name_1", source_2."name_2") AS "name",
```

## Join
```sql
SELECT DISTINCT
  t."customer_id",
  c."name"
FROM
  "schema_1"."table" t
  LEFT JOIN "schema_2"."customers" c
    ON t."customer_id" = c."id"
WHERE
  t."customer_id" IS NOT NULL
ORDER BY
  "customer_id"
```

The inner `JOIN` on `customers` keeps only records whose `customer_id` matches a customer. Empty `customer_id` values never match; then the unmatched ones are dropped. Thus, no need for `t."customer_id" IS NOT NULL`.
```sql
SELECT DISTINCT
  t."customer_id",
  c."name"
FROM
  "schema_1"."table" t
  JOIN "schema_2"."customers" c
    ON t."customer_id" = c."id"
ORDER BY
  "customer_id"
```



```sql
SELECT DISTINCT
  i."info_id"   AS "info_id",
  i."name"   AS "name"
FROM
  "schema_1"."table" t
  JOIN "schema_2"."customers" c
    ON t1."customer_id" = t2."id"
WHERE
  d."item_id" IS NOT NULL
ORDER BY
  "info_id"
```