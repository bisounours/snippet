# Snippets about Oracle model

## Foreign keys 

 Get link between tables 

 ```sql
SELECT a.table_name ||';'|| a.column_name || ';' || a.constraint_name || ';' || c_pk.table_name || ';' || a_pk.column_name as constraint_description
  FROM all_cons_columns a
  JOIN all_constraints c ON a.owner = c.owner AND a.constraint_name = c.constraint_name
  JOIN all_constraints c_pk ON c.r_owner = c_pk.owner AND c.r_constraint_name = c_pk.constraint_name
  JOIN all_cons_columns a_pk ON c_pk.owner = a_pk.owner AND c_pk.constraint_name = a_pk.constraint_name
 WHERE c.constraint_type = 'R' AND c.owner = 'XXX' order by a.table_name, a.column_name;
```

## Data model 

Get list of tables and columns of a owner

 ```sql 
select col.table_name||';'||col.column_name||';'||col.data_type||';'||col.char_col_decl_length||';'||col.data_precision||','||col.data_scale||';'||nvl(constraint_info.constraint_type,'N') from all_tab_columns col
left join all_cons_columns constraint_col on constraint_col.table_name = col.table_name and col.column_name = constraint_col.column_name
left join all_constraints constraint_info on constraint_col.constraint_name = constraint_info.constraint_name and constraint_info.constraint_type in ('P','C')
where col.owner = 'XXX'
order by col.table_name, col.column_name;   
```

## Unique constraints

```sql
SELECT a.table_name||';'||a.column_name||';'||a.constraint_name
FROM all_cons_columns a
JOIN all_constraints c ON a.owner = c.owner AND a.constraint_name = c.constraint_name
WHERE c.constraint_type = 'U' 
AND c.owner = 'XXX' order by a.table_name, a.column_name;
```
