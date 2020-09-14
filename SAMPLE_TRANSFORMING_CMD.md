## Stats
Using the db_audit_30day.csv source & csv sourcetype. Find the usage of `credit_card ` table for Command `select`
The SQL Command as below:
```
SELECT * FROM creditcard
```

SPL Query:
```
index="database_audit" source="db_audit_30day.csv" date_month="august" 
| where like(Command,"SELECT * FROM creditcard%")
| stats count by date_mday
```

## Chart

Using chart with column name changed
```
index="database_audit" source="db_audit_30day.csv" 
| where like(Command,"SELECT * FROM creditcard%") 
| eval Month=upper(date_month)
| chart count over Month 
| rename count as "Total Hit"
```
