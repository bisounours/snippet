# Snippets about Oracle tablespace

## Tablespace description 

 Get tablespace description 

 ```sql 
 SELECT TABLESPACE_NAME, BLOCK_SIZE, STATUS, CONTENTS, EXTENT_MANAGEMENT, ALLOCATION_TYPE, SEGMENT_SPACE_MANAGEMENT FROM DBA_TABLESPACES ORDER BY TABLESPACE_NAME; 
```

## Tablespace file name 

 Get table space file name 

 ```sql 
 SELECT TABLESPACE_NAME, FILE_NAME, FILE_ID, BYTES, STATUS, MAXBYTES FROM DBA_DATA_FILES
UNION
SELECT TABLESPACE_NAME, FILE_NAME, FILE_ID, BYTES, STATUS, MAXBYTES FROM DBA_TEMP_FILES
ORDER BY TABLESPACE_NAME, FILE_NAME; 
```

## Tablespace ID 

 Get id of tablespace 

 ```sql 
 SELECT TS#, NAME FROM V$TABLESPACE ORDER BY TS#; 
```

## Tablespace % used 

  Get % used of each tablespace 

 ```sql 
 SELECT TABLESPACE_NAME, TABLESPACE_SIZE, USED_SPACE, ROUND(USED_PERCENT, 2) FROM DBA_TABLESPACE_USAGE_METRICS ORDER BY TABLESPACE_NAME; 
```

## History of tablespace size 

  Historic size of tablespace (use id of tablespace) 

 ```sql 
 SELECT H.TABLESPACE_ID, T.NAME, H.TABLESPACE_SIZE, H.TABLESPACE_MAXSIZE, H.TABLESPACE_USEDSIZE, round((H.TABLESPACE_USEDSIZE*100)/H.TABLESPACE_MAXSIZE,2) as pourcentage, H.RTIME 
FROM DBA_HIST_TBSPC_SPACE_USAGE H 
JOIN V$TABLESPACE T ON (H.TABLESPACE_ID = T.TS#) 
WHERE H.TABLESPACE_ID =18 ORDER BY H.RTIME DESC; 
```

## Objects of tablespace 

 Get all object stored in tablespace 

 ```sql 
 SELECT e.tablespace_name AS "TABLESPACE", e.owner AS "SCHEMA", e.segment_name AS "NOM OBJET", e.segment_type AS "TYPE OBJET", ROUND(Sum(e.bytes)/1024/1024,2) AS "TAILLE (Mb)", d.file_name AS "FICHIER DE DONNEES" 
FROM dba_extents e 
INNER JOIN dba_data_files d ON ( e.file_id = d.file_id ) 
WHERE e.tablespace_name like 'XXXXXX' group by e.tablespace_name, e.owner, e.segment_name, e.segment_type, d.file_name ORDER BY 2,"TAILLE (Mb)" DESC;
```

## Schema size 

 Schema size 

 ```sql 
  SELECT sum(ROUND(sum(bytes)/1024/1024/1024, 2)) GB
  FROM
  (
    SELECT segment_name table_name, owner, bytes
    FROM dba_segments
    WHERE segment_type IN ('TABLE', 'TABLE PARTITION', 'TABLE SUBPARTITION')
    UNION ALL
    SELECT i.table_name, i.owner, s.bytes
    FROM dba_indexes i, dba_segments s
    WHERE s.segment_name = i.index_name
    AND s.owner = i.owner
    AND s.segment_type IN ('INDEX', 'INDEX PARTITION', 'INDEX SUBPARTITION')
    UNION ALL
    SELECT l.table_name, l.owner, s.bytes
    FROM dba_lobs l, dba_segments s
    WHERE s.segment_name = l.segment_name
    AND s.owner = l.owner
    AND s.segment_type = 'LOBSEGMENT'
    UNION ALL
    SELECT l.table_name, l.owner, s.bytes
    FROM dba_lobs l, dba_segments s
    WHERE s.segment_name = l.index_name
    AND s.owner = l.owner
    AND s.segment_type = 'LOBINDEX'
  )
  WHERE owner in ('STS','KO1','KO2','KO3','KO4','KO5','KO6')
  GROUP BY table_name, owner
  HAVING SUM(bytes)/1024/1024 > 00/* Ignore really small tables */
  ORDER BY SUM(bytes) DESC; 
```
