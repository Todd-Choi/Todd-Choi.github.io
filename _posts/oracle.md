---
layout: post
title:  "Oracle 유용 정보"
image: ''
date:   2019-03-26 14:27:40
tags:
- oracle
description: ''
categories:
- Learn
---

# 퀴리를 이용한 테이블정의서

{% highlight javascript %}
WITH LIST AS
(
    SELECT A.TABLE_NAME,
           A.COLUMN_NAME,
           A.DATA_TYPE,
           A.DATA_LENGTH,
           A.NULLABLE,
           B.COMMENTS
    FROM   dba_tab_columns A,
           all_col_comments B
    WHERE  A.OWNER = B.OWNER
    AND    A.TABLE_NAME = B.TABLE_NAME
    AND    A.COLUMN_NAME = B.COLUMN_NAME
    AND    A.TABLE_NAME NOT LIKE 'BIN%'
	AND    A.OWNER = 'DB명'
),
PKLIST AS
(
    SELECT C.TABLE_NAME,
           C.COLUMN_NAME,
           C.POSITION
    FROM USER_CONS_COLUMNS C,
         USER_CONSTRAINTS S
    WHERE C.CONSTRAINT_NAME = S.CONSTRAINT_NAME
    AND S.CONSTRAINT_TYPE = 'P'
)
SELECT L.TABLE_NAME AS "테이블명",
       L.COLUMN_NAME AS "컬럼명",
       L.DATA_TYPE AS "데이터타입",
       L.DATA_LENGTH AS "길이",
       CASE WHEN P.POSITION < 99 THEN 'Y'
            ELSE ' '
       END AS "PK",
       L.NULLABLE AS "Null 여부",
       L.COMMENTS AS "Comments"
FROM LIST L,
     PKLIST P
WHERE L.TABLE_NAME = P.TABLE_NAME(+)
  AND L.COLUMN_NAME = P.COLUMN_NAME(+)
 ORDER BY L.TABLE_NAME,
          NVL(P.POSITION, 99)
;
{% endhighlight %}
