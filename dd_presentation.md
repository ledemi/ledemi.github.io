---
layout: page
title: "Desk and Derrick Club Presentation Notes"
permalink: /dd-presentation
---

Thank you to Ms. Susan Miller and Ms. Gabriella Hunter for inviting me to speak. This presentation is motivated in part by Mr. James Hunter, who over the course of several conversations, has tried to convince me of the opportunity that remains in "Shallow Water."

[OCSEA](https://gff856e6c70bc1a-afadb1.adb.us-chicago-1.oraclecloudapps.com/ords/r/ocs/ocsea) is primarily a personal learning project and a tool I'll be using as an observer of the oil and gas industry to gain insight into BSEE's publicly available federal offshore [data](https://www.data.bsee.gov/).

It's built with [Oracle APEX](https://apex.oracle.com/en/) (low-code front end framework) and hosted for free on [Oracle Cloud](https://www.oracle.com/cloud/free/).

This website is also hosted for free on [GitHub Pages](https://pages.github.com/).

Current Entity Relationship Diagram (ERD):  
![ocsea_erd](/assets/images/ocsea_erd.png)

The largest hurdle was building the ETL (extract, transform, load) process. I used AI to generate code and help me understand database concepts.    
- [Generating a Python script](https://grok.com/share/c2hhcmQtMg%3D%3D_31ea3654-8ce8-4616-8df0-f9e47ff5b8a2)
- [Iterating on Python script](https://grok.com/share/c2hhcmQtMg%3D%3D_3abf8f5f-679e-4aa7-b653-7a19909f656b)
- [OCSEA logo](https://grok.com/share/c2hhcmQtMg%3D%3D_e9dc10e7-4f74-4673-9598-5817a4f7595a)

 I ended up writing stored procedures that will run on a schedule. Still working on this bit.  
 ![ocsea_procedures](/assets/images/ocsea_procedures.png)

SQL for Production by Platform page:  
```plsql
with prod_months as (
    select
        add_months(trunc(sysdate, 'month'), - 4 - (level - 1)) prod_month_start,
        add_months(last_day(trunc(sysdate)), - 4 - (level - 1)) prod_month_end
    from
        dual
    connect by level < 13
),
cmpnys as (
    select distinct
        mms_company_num,
        bus_asc_name
    from
        companies
)
select
    complex_id_num,
    structure_number,
    platform,
    struc_type_code,
    install_date,
    water_depth,
    operator,
    year_end,
    round(avg_bpd) avg_bpd,
    round(avg_bpd / sum(avg_bpd) over() * 100, 2) avg_bpd_pct,
    round(sum(avg_bpd) over(order by avg_bpd desc nulls last) / sum(avg_bpd) over() * 100, 2) avg_bpd_pct_cum,
    round(sum(avg_bpd) over(partition by operator)) bpd_operator,
    round(avg_mcfpd) avg_mcfpd,
    round(avg_mcfpd / sum(avg_mcfpd) over() * 100, 2) avg_mcfpd_pct,
    round(sum(avg_mcfpd) over(order by avg_mcfpd desc nulls last) / sum(avg_mcfpd) over() * 100, 2) avg_mcfpd_pct_cum
from (
    select
        ps.complex_id_num,
        ps.structure_number,
        ps.area_code || ' ' || ps.block_number || ' ' || ps.structure_name platform,
        ps.struc_type_code,
        ps.install_date,
        pm.water_depth,
        c.bus_asc_name operator,
        max(prod_months.prod_month_start) year_end,
        sum(oa.mon_o_prod_vol / extract(day from prod_months.prod_month_end)) / 12 avg_bpd,
        sum(oa.mon_g_prod_vol / extract(day from prod_months.prod_month_end)) / 12 avg_mcfpd
    from
        prod_months
        cross join platform_structures ps
        inner join platform_masters pm on ps.complex_id_num = pm.complex_id_num
        left join cmpnys c on pm.mms_company_num = c.mms_company_num
        left join boreholes b on ps.complex_id_num = b.complex_id_num and ps.structure_number = b.structure_number
        left join ogor_a oa on
            b.api_well_number = oa.api_well_number
            and oa.production_date = prod_months.prod_month_start
    where
        ps.install_date is not null
        and ps.removal_date is null
    group by
        ps.complex_id_num,
        ps.structure_number,
        ps.area_code || ' ' || ps.block_number || ' ' || ps.structure_name,
        ps.struc_type_code,
        ps.install_date,
        pm.water_depth,
        c.bus_asc_name
);
```
