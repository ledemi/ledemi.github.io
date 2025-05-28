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
