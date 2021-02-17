---
title: "Set up a view for use in Reporting"
description: ""
lead: "By default, views in the public schema are not surfaced in the uzERP Report Writer. If you want to use a standard view for reporting then set up a new view under the reports schema."
date: 2021-02-17T15:36:45Z
lastmod: 2021-02-17T15:36:45Z
draft: false
weight: 50
images: ["reports-schema.jpg"]
contributors: [Martyn Shiner]
---

The example below takes the standard GL transactions view and makes it available for reporting

````sql
CREATE OR REPLACE VIEW reports.my_gl_transactions AS 
SELECT id, docref, usercompanyid, glaccount_id, glcentre_id, glperiods_id, 
       trandate, source, "comment", reference, twincurrency_id, twinrate, 
       "type", twinvalue, "value", account, cost_centre, glperiod, twincurrency, 
       year_period
FROM gltransactionsoverview;
````

You can of course omit columns or add WHERE clauses to pare the data down.

Use this technique to create custom views from any data in the system - as long as the view is in the reports schema then it will be available for reporting.