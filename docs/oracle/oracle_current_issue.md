# Snippets to fix or detect current Oracle issue

## DBA alerts 

 Get DBA alerts 

 ```sql 
 SELECT REASON, CREATION_TIME, METRIC_VALUE, MESSAGE_TYPE FROM DBA_OUTSTANDING_ALERTS; 
```

## Kill session 

 Kill a oracle session 

 ```sql 
  ALTER SYSTEM KILL SESSION 'sid,serial#' IMMEDIATE; 
```

## Locked object 

 Find the locked object 

 ```sql 
  SELECT O.OBJECT_NAME, S.SID, S.SERIAL#, S.STATE, P.SPID, S.PROGRAM,S.USERNAME,S.MACHINE,S.PORT , S.LOGON_TIME,SQ.SQL_FULLTEXT
  FROM V$LOCKED_OBJECT L, DBA_OBJECTS O, V$SESSION S, V$PROCESS P, V$SQL SQ 
  WHERE L.OBJECT_ID = O.OBJECT_ID 
  AND L.SESSION_ID = S.SID AND S.PADDR = P.ADDR 
  AND S.SQL_ADDRESS = SQ.ADDRESS; 
```

```sql
select
   (select username from v$session where sid=a.sid) blocker,
   a.sid,
   ' is blocking ',
   (select username from v$session where sid=b.sid) blockee,
   b.sid
from v$lock a, v$lock b
where a.block = 1
    and b.request > 0
    and a.id1 = b.id1
    and a.id2 = b.id2;
```

## Drop import table 

 Drop import table 

 ```sql 
 SELECT * FROM dba_datapump_jobs WHERE job_name NOT LIKE 'BIN$%' ORDER BY 1,2;
drop table SYSTEM.IMPORT_KO6; 
```
