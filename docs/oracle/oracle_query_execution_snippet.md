# Snippets about Oracle executed queries 

## Executed queries 

 Check queries executed with spend time + additionnal info 

 ```sql 
 SELECT 
  sql_fulltext
  ,sql_id
  ,fetches
  ,executions
  ,disk_reads
  ,rows_processed
  ,optimizer_cost
  ,cpu_time/1000000 \"cpu_time (s)\"
  ,user_io_wait_time/1000000 \"user_io_wait_time (s)\"
  ,elapsed_time/1000000 \"elapsed_time (s)\"
  ,first_load_time
  ,last_load_time
  ,round(cpu_time / executions,0)/1000000 \"cpu_time / exec (s)\"
  ,round(user_io_wait_time / executions,0)/1000000 \"user_io_wait_time / exec (s)\"
  ,round(elapsed_time / executions,0)/1000000 \"elapsed_time / exec (s)\"
from GV$SQLAREA where 
  parsing_schema_name = 'XXX'
  and executions != 0
  and sql_text like '%/*%'
--  and upper(sql_fulltext) like upper('%KO6QTO06%')
--  and last_load_time > to_date ('14/02/2014 10','DD/MM/YYYY HH24')
order by 
  \"elapsed_time / exec (s)\" desc
  ,lower(sql_text)
; 
```

## Time slot queries 

 Select queries executed during in given time slot 

 ```sql 
 select 
  snap.begin_interval_time
  ,snap.end_interval_time
  ,text.sql_text 
  ,text.sql_id
 ,stat.EXECUTIONS_DELTA \"execution\"
  ,round(stat.ELAPSED_TIME_DELTA,0)/1000000 \"elapsed time (s)\"
  ,case stat.EXECUTIONS_DELTA when 0 then 0 else round(stat.CPU_TIME_DELTA / stat.EXECUTIONS_DELTA,0)/1000000  end \"CPU time / exec (s)\"
  ,case stat.EXECUTIONS_DELTA when 0 then 0 else round(stat.CCWAIT_DELTA / stat.EXECUTIONS_DELTA,0)/1000000  end \"concurrency w time / exec (s)\"
  ,case stat.EXECUTIONS_DELTA when 0 then 0 else round(stat.IOWAIT_DELTA / stat.EXECUTIONS_DELTA,0)/1000000  end \"IO wait time / exec (s)\"
  ,case stat.EXECUTIONS_DELTA when 0 then 0 else round(stat.ELAPSED_TIME_DELTA / stat.EXECUTIONS_DELTA,0)/1000000  end \"elapsed time / exec (s)\"
from 
  dba_hist_sqlstat stat 
    inner join dba_hist_sqltext text 
      on stat.sql_id = text.sql_id 
    inner join DBA_HIST_SNAPSHOT snap 
      on snap.snap_id = stat.snap_id
where 
  stat.EXECUTIONS_DELTA is not null
  --and  snap.snap_id between 685 and 687
  and snap.begin_interval_time between to_date('08/02/2017 00', 'DD/MM/YYYY HH24') and to_date('08/02/2017 03', 'DD/MM/YYYY HH24')
order by
  \"elapsed time (s)\" desc
  ,\"elapsed time / exec (s)\" desc
; 
```

## Check execution plan 

 Plan execution évolution
Check plan efficiency 

 ```sql 
 select 
  text.sql_id
  ,stat.plan_hash_value
  ,sum(stat.EXECUTIONS_DELTA) \"execution\"
  ,sum(round(stat.ELAPSED_TIME_DELTA,0)/1000000) \"elapsed time (s)\"
  ,round(avg(case stat.CPU_TIME_DELTA when 0 then 0 else round(stat.CPU_TIME_DELTA / stat.EXECUTIONS_DELTA,0)/1000000  end),7) \"CPU time / exec (s)\"
  ,round(avg(case stat.EXECUTIONS_DELTA when 0 then 0 else round(stat.CCWAIT_DELTA / stat.EXECUTIONS_DELTA,0)/1000000  end),7) \"concurrency w time / exec (s)\"
  ,round(avg(case stat.EXECUTIONS_DELTA when 0 then 0 else round(stat.IOWAIT_DELTA / stat.EXECUTIONS_DELTA,0)/1000000  end),7) \"IO wait time / exec (s)\"
  ,round(avg(case stat.EXECUTIONS_DELTA when 0 then 0 else round(stat.ELAPSED_TIME_DELTA / stat.EXECUTIONS_DELTA,0)/1000000  end),7) \"elapsed time / exec (s)\"
  ,min(snap.begin_interval_time) \"start date\"
  ,max(snap.end_interval_time) \"end date\"
from 
  dba_hist_sqlstat stat 
    inner join dba_hist_sqltext text 
      on stat.sql_id = text.sql_id 
    inner join DBA_HIST_SNAPSHOT snap 
      on snap.snap_id = stat.snap_id
where 
  stat.EXECUTIONS_DELTA is not null
  and stat.sql_id = '0dtgrsg44spbh'
group by
  text.sql_id
  ,stat.plan_hash_value
order by
  \"elapsed time / exec (s)\" desc
; 
```

## Histo duration query 

 Select the histo of execution of a SQL ID with duration 

 ```sql 
 SELECT 
  a.snap_id,
  A.PLAN_HASH_VALUE,
  TO_CHAR(begin_interval_time,'dd-mm-yyyy') btime,
  executions_delta executions,
  ROUND(ELAPSED_TIME_delta/1000000/greatest(executions_delta,1),4) \"avg duration (sec)\"
FROM dba_hist_SQLSTAT a,
  dba_hist_snapshot b
WHERE sql_id         ='07j4dc0q7rrxt'
AND a.snap_id        =b.snap_id
AND A.INSTANCE_NUMBER=B.INSTANCE_NUMBER
ORDER BY snap_id DESC,
  a.instance_number; 
```