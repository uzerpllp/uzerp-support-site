---
title: "Gl Period From a Date"
description: ""
lead: "Find the GL period of a transaction given its date."
date: 2021-02-17T15:40:57Z
lastmod: 2021-02-17T15:40:57Z
draft: false
weight: 50
images: ["gl-period-from-a-date.jpg"]
contributors: [Martyn Shiner]
---
So you have a transaction, say an invoice or stock movement, with a date and you'd like to sort it by GL period to line up with the accounting periods rather than its calendar month. Here's how for sales invoices:

```sql
SELECT invoice_number, invoice_date, year, period
FROM gl_periods p, si_header i
WHERE p.enddate = ( SELECT min(enddate)
             FROM gl_periods z
             WHERE z.period `<>` 0 z.enddate >= i.invoice_date
             AND p.usercompanyid = z.usercompanyid)
ORDER BY invoice_number
