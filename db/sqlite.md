## Commands



```
.mode - set data mode csv
.exit - exit database
.tables - show tables in database
```

```
.import - import data to table
```

## Importing with pragma

Before importing data to table, it is good to example Foreign Key validation. It will prevents from importgin data into table, if data do not cover reference to REFERENCE key. 

For exaple, if we import the data to the table that has FOREIGN KEY reference , and this table does not have value for this key, it will not be imported. 