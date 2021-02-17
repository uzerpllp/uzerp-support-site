---
title: "Transactions in time buckets"
description: ""
lead: "SQL script to put uzERP transactions into time buckets using case statements "
date: 2021-02-01T15:29:11Z
lastmod: 2021-02-01T15:29:11Z
draft: false
weight: 50
images: ["time-buckets.jpg"]
contributors: [Martyn Shiner]
---

This applies in something like an aged debt report (shown below).

```sql
SELECT 
sltransactionsoverview.customer, 
sltransactionsoverview.transaction_date, 
sltransactionsoverview.id, sltransactionsoverview.transaction_type, sltransactionsoverview.our_reference, sltransactionsoverview.ext_reference, 
sltransactionsoverview.gross_value, 
sltransactionsoverview.currency, 
sltransactionsoverview.rate, sltransactionsoverview.description, sltransactionsoverview.payment_terms,
      CASE
            WHEN to_char(sltransactionsoverview.transaction_date::timestamp with time zone, 'YYYYMM'::text) = to_char('now'::text::date::timestamp with time zone, 'YYYYMM'::text) 
             THEN sltransactionsoverview.gross_value
            ELSE 0::numeric
      END AS current_gross, 
      CASE
            WHEN to_char(sltransactionsoverview.transaction_date::timestamp with time zone, 'YYYYMM'::text) = to_char('now'::text::date - '1 mon'::interval, 'YYYYMM'::text) 
             THEN sltransactionsoverview.gross_value
            ELSE 0::numeric
 END AS m1_gross, 
 CASE
       WHEN to_char(sltransactionsoverview.transaction_date::timestamp with time zone, 'YYYYMM'::text) = to_char('now'::text::date - '2 mons'::interval, 'YYYYMM'::text) 
             THEN sltransactionsoverview.gross_value
       ELSE 0::numeric
 END AS m2_gross, 
 CASE
       WHEN to_char(sltransactionsoverview.transaction_date::timestamp with time zone, 'YYYYMM'::text) = to_char('now'::text::date - '3 mons'::interval, 'YYYYMM'::text) 
        THEN sltransactionsoverview.gross_value
       ELSE 0::numeric
      END AS m3_gross, 
 CASE
       WHEN to_char(sltransactionsoverview.transaction_date::timestamp with time zone, 'YYYYMM'::text) = to_char('now'::text::date - '4 mons'::interval, 'YYYYMM'::text) 
             THEN sltransactionsoverview.gross_value
             ELSE 0::numeric
 END AS m4_gross, 
 CASE
       WHEN to_char(sltransactionsoverview.transaction_date::timestamp with time zone, 'YYYYMM'::text) <= to_char('now'::text::date - '5 mons'::interval, 'YYYYMM'::text) 
            THEN sltransactionsoverview.gross_value
       ELSE 0::numeric
 END AS m5_gross
FROM sltransactionsoverview
LEFT JOIN slmaster_overview ON sltransactionsoverview.slmaster_id = slmaster_overview.id
LEFT JOIN companyoverview ON slmaster_overview.company_id = companyoverview.id
LEFT JOIN countries ON companyoverview.countrycode = countries.code
WHERE sltransactionsoverview.status::text `<>` 'P'::text
ORDER BY sltransactionsoverview.customer, sltransactionsoverview.transaction_date, sltransactionsoverview.id;
```
