# Snippets about Oracle configuration

## Stat session and processes 

 Get the current, max and limit value for sessions and processes 

 ```sql 
 select resource_name, current_utilization, max_utilization, limit_value 
  from v$resource_limit 
  where resource_name in ('sessions', 'processes');

  SELECT   rl.snap_id, s.begin_interval_time, s.end_interval_time,
          rl.instance_number, rl.resource_name, rl.current_utilization,
          rl.max_utilization
  FROM dba_hist_resource_limit rl, dba_hist_snapshot s
  WHERE s.snap_id = rl.snap_id AND rl.resource_name = 'sessions'
  ORDER BY s.begin_interval_time desc, rl.instance_number; 
```

## Resize datafiles 

 Resize the datafiles to reduce size of database on disk 

 ```sql 
 set serveroutput on;
DECLARE
  env varchar2(10) := 'XXX';  
  count_db_resized number(10);
BEGIN
FOR s in reverse 1..7 LOOP 
  count_db_resized := 0;
  FOR k IN 1..60 LOOP
    BEGIN
      if k < 10 then
        execute immediate 'ALTER DATABASE DATAFILE ''/path/'||env||'/base/yyyyy'||k||'.dbf'' RESIZE '||(s*100)||'M';
        count_db_resized := count_db_resized+1;
      else 
        execute immediate 'ALTER DATABASE DATAFILE ''/path/'||env||'/base/yyyyy'||k||'.dbf'' RESIZE '||(s*100)||'M';
        count_db_resized := count_db_resized+1;
      end if;
    EXCEPTION WHEN OTHERS THEN NULL;
    END;
  END LOOP;
  
  FOR k IN 1..60 LOOP
    BEGIN
      if k < 10 then
        execute immediate 'ALTER DATABASE DATAFILE ''/path/'||env||'/base/yyyyy'||k||'.dbf'' RESIZE '||(s*100)||'M';
        count_db_resized := count_db_resized+1;
      else 
        execute immediate 'ALTER DATABASE DATAFILE ''/path/'||env||'/base/yyyyy'||k||'.dbf'' RESIZE '||(s*100)||'M';
        count_db_resized := count_db_resized+1;
      end if;
    EXCEPTION WHEN OTHERS THEN NULL;
    END;
  END LOOP;  
  dbms_output.put_line('Nb db files resized at '||(s*100)||'Mo : '||count_db_resized);
END LOOP;
END; 
```

## Update memory parameters 

 update memory parameters when database is down 

 ```sql 
    sqlplus /nolog
    SQL> connect / as sysdba
    SQL> CREATE PFILE='/tmp/xxxxx.spfile' from SPFILE='/users/ora00/spfile/xxxxx.ora';
    -- Modification of /tmp/xxxxx.spfile to modify memory values
    SQL> CREATE SPFILE='/users/ora00/spfile/xxxxx.ora' from PFILE='/tmp/xxxxx.spfile';
    SQL> startup SPFILE='/users/ora00/spfile/xxxxx.ora' 
```