
# Table of Contents
```toc
```

# Metabase
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


```sql
SELECT DISTINCT
  i."info_id"   AS "info_id",
  i."name"   AS "name"
FROM
  "schema_1"."table_1" d
  LEFT JOIN "schema_2"."table_2" i
    ON d."item_id" = i."id"
WHERE
  d."item_id" IS NOT NULL
ORDER BY
  "info_id"
```

