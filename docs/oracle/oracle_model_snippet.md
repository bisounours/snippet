# Snippets baout Oracle model

## Foreign keys 

 Get link between tables 

 ```sql
spool /c:/tmp/datamodel.gv
set serveroutput on;
 select c1.table_name || '(' || cc1.column_name || ')' || ' --> ' || c2.table_name || '(' || cc2.column_name || ')' from all_constraints c1
inner join all_cons_columns cc1 on c1.constraint_name = cc1.constraint_name
inner join all_constraints c2 on c1.r_constraint_name = c2.constraint_name
inner join all_cons_columns cc2 on c2.constraint_name = cc2.constraint_name
where c1.owner = 'XXX' 
and c1.constraint_type = 'R';
spool off; 
```

## Data model 

Get list of tables and columns of a owner

 ```sql 
spool /c:/tmp/datamodel.gv
set serveroutput on;
DECLARE  
  CURSOR cur_table IS 
    select table_name from all_tables where owner = 'XXX';
  CURSOR cur_col(current_table_name all_tab_cols.table_name%TYPE) IS
    select * from all_tab_cols where table_name = current_table_name;  
BEGIN
  for current_table_name in cur_table loop
    dbms_output.put_line(current_table_name.table_name || ' { ');
    for current_col in cur_col(current_table_name.table_name) loop
      dbms_output.put_line('    ' || current_col.column_name || '    ' || current_col.data_type || '    ' || current_col.data_length || '    ' || current_col.nullable || '    ' || current_col.data_default);
    end loop;
    dbms_output.put_line('}');
  end loop;
END;
/
spool off; 
```

## Constraints description 

 List all constraint of a schema 

 ```sql 
 SELECT a.table_name ||';'|| a.column_name || ';' || a.constraint_name || ';' || c_pk.table_name || ';' || a_pk.column_name as constraint_description
  FROM all_cons_columns a
  JOIN all_constraints c ON a.owner = c.owner AND a.constraint_name = c.constraint_name
  JOIN all_constraints c_pk ON c.r_owner = c_pk.owner AND c.r_constraint_name = c_pk.constraint_name
  JOIN all_cons_columns a_pk ON c_pk.owner = a_pk.owner AND c_pk.constraint_name = a_pk.constraint_name
 WHERE c.constraint_type = 'R' AND c.owner = 'XXX' order by a.table_name, a.column_name; 
```
