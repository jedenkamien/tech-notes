## How to copy data from one table into another one?

I had to copy data from one table from one database into a new table in another database, both lying on the same sql server.

### Create a table being a copy of another one

```sql
-- Create a new table with both structure and data
CREATE TABLE new_table AS 
SELECT * FROM existing_table;
```

```sql
-- Create just the structure without data
CREATE TABLE new_table AS 
SELECT * FROM existing_table 
WHERE 1=0;
```

##### SQL Server-specific methods
```sql
-- With data
SELECT * INTO new_table FROM existing_table;
```

```sql
-- Structure only
SELECT * INTO new_table FROM existing_table WHERE 1=0;
```

### Export data from a table in SSMS

Just right-click on headers of results of a query and click to Save the data as... - and choose an csv file for instance.

### Import the data into another table

Import the data by executing a query like the following:

```sql
-- BULK INSERT
BULK INSERT YourTable
FROM 'C:\path\to\your\file.csv'
WITH (
    FIELDTERMINATOR = ',',
    ROWTERMINATOR = '\n',
    FIRSTROW = 2,  -- Skip header row if present
    TABLOCK,       -- For better performance
    CODEPAGE = '65001'  -- UTF-8 encoding
);
```

```sql
-- OPENROWSET
INSERT INTO YourTable (Column1, Column2, Column3)
SELECT Column1, Column2, Column3
FROM OPENROWSET(
    BULK 'C:\path\to\your\file.csv',
    FORMATFILE = 'C:\path\to\format\file.fmt',
    FIRSTROW = 2
) AS source;
```
