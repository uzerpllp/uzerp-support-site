---
title: "PostgreSQL Date Sorting"
description: ""
lead: ""
date: 2021-02-17T15:34:49Z
lastmod: 2021-02-17T15:34:49Z
draft: false
weight: 50
images: ["postgres-date-sorting.jpg"]
contributors: [Martyn Shiner]
---

This looks a bit complicated, but the date_part - date_part('year',
invoice_date)||to_char(date_part('month', invoice_date),'FM09') - could
be encapsulated in a pgsql function.

```sql
SELECT date_part('year', invoice_date)||to_char(date_part('month', invoice_date),'FM09') as inv_year_month,
  CASE WHEN date_part('year', current_date)||to_char(date_part('month', current_date),'FM09')=date_part('year', 
  invoice_date)||to_char(date_part('month', invoice_date),'FM09')
         THEN 'current = '||net_value
       WHEN date_part('year', current_date - interval '1 
  month')||to_char(date_part('month', current_date - interval '1 
  month'),'FM09')=date_part('year', 
  invoice_date)||to_char(date_part('month', invoice_date),'FM09')
         THEN '1 Month = '||net_value
       WHEN date_part('year', current_date - interval '2 
  month')||to_char(date_part('month', current_date - interval '2 
  month'),'FM09')=date_part('year', 
  invoice_date)||to_char(date_part('month', invoice_date),'FM09')
         THEN '2 Month = '||net_value
    END
FROM si_header;
```

Checking the date function, it does handle the differing day lengths in months, as follows:-

```sql
SELECT date_part('year', timestamp 
 '2011-01-04')||to_char(date_part('month', timestamp '2011-01-04'),'FM09')
       , date_part('year', timestamp '2011-03-31'- interval '1 
 month')||to_char(date_part('month', timestamp '2011-03-31'- interval '1 
 month'),'FM09')
       , date_part('year', timestamp '2011-04-01'- interval '1 
 month')||to_char(date_part('month', timestamp '2011-04-01'- interval '1 
 month'),'FM09');
```

In the above, taking one month off 31/03/2011 gives 201102; taking one month off 01/04/2011 gives 201103; i.e. it works on months not days.

```sql 
SELECT date_part('year', current_date)||to_char(date_part('month', 
 current_date),'FM09')
       , date_part('year', current_date - interval '1 
 month')||to_char(date_part('month', current_date - interval '1 
 month'),'FM09')
       , date_part('year', current_date - interval '2 
 month')||to_char(date_part('month', current_date - interval '2 
 month'),'FM09')
       , date_part('year', current_date - interval '3 
 month')||to_char(date_part('month', current_date - interval '3 
 month'),'FM09')
       , date_part('year', current_date - interval '4 
 month')||to_char(date_part('month', current_date - interval '4 
 month'),'FM09')
       , date_part('year', current_date - interval '5 
 month')||to_char(date_part('month', current_date - interval '5 
 month'),'FM09')
       , date_part('year', current_date - interval '6 
 month')||to_char(date_part('month', current_date - interval '6 
 month'),'FM09')
       , date_part('year', current_date - interval '7 
 month')||to_char(date_part('month', current_date - interval '7 
 month'),'FM09')
       , date_part('year', current_date - interval '8 
 month')||to_char(date_part('month', current_date - interval '8 
 month'),'FM09')
       , date_part('year', current_date - interval '9 
 month')||to_char(date_part('month', current_date - interval '9 
 month'),'FM09')
       , date_part('year', current_date - interval '10 
 month')||to_char(date_part('month', current_date - interval '10 
 month'),'FM09')
       , date_part('year', current_date - interval '11 
 month')||to_char(date_part('month', current_date - interval '11 
 month'),'FM09');
```
