# Google Fiber Case Study

A learning journey from Google Business Intelligence Certificate program

## The Scenario

You are interviewing for a job with Google Fiber, which provides people and businesses with fiber optic internet. As part of the interview process, the Fiber customer service team has asked you to design a dashboard using fictional data. The position you are interviewing for is in the customer call center, where Fiber uses business intelligence to monitor and improve customer satisfaction.

To provide the interviewers with both BI value and organizational data maturity, you will use your knowledge of the BI stages: capture, analyze, and monitor. By the time you are done, you will have an end-of-course project that demonstrates your knowledge and skills to potential employers.

## Part 1: Understanding Requirements and Formulating Strategies

With information I gathered from discovery meetings with stakeholders, here are my version of the key BI documents:

[Stakeholder requirements document](https://docs.google.com/document/d/1MOYPn7O9xY-tTu7Pap2xpral1mweDx8NGhPTttFpV1k/edit?usp=sharing)

[Project requirements document](https://docs.google.com/document/d/1Ki9HJlPlBBAfrskhHqk3vH_12x27NkKCiVzfeTsyUxg/edit?usp=sharing)

[Strategy document](https://docs.google.com/document/d/1C_Y1a1gm44j-md7KHnOUjHqLuRkqxVy86olDnIajuRc/edit?usp=sharing)

My takeaways:

* When stakeholders share what they are looking for, different people will offer their asks with different granularities. Some may say they want to uncover factors that contribute to sales, some may be much more detailed by saying they want to see a visualization of such and such. My learning in this exercise is that do not try to normalize all requirements in one attempt, but instead document them thoroughly in the stakeholder requirements document. By doing so, it is less likely to lose track of stakeholders' actual needs.
* When designing dashboard and data visualizations, I always favor showing fewer charts while building dynamics that allow users to customize (such as with a date slider or a filter). But I need to think about whether there are cases where users need to see same chart of different dimensions displayed side by side.

## Part 2: Creating a Pipeline System that Delivers Data to Reporting Tables

### Inspecting Data

In order to anonymize and fictionalize the data, the datasets the columns market_1, market_2, and market_3 to indicate three different city service areas the data represents. 

The data also lists five problem types:

* Type_1 is account management
* Type_2 is technician troubleshooting
* Type_3 is scheduling
* Type_4 is construction
* Type_5 is internet and wifi

Additionally, the dataset records repeat calls over seven-day periods. The initial contact date is listed as contacts_n. The other call columns are then contacts_n_number of days since first call. For example, contacts_n_6 indicates six days since first contact.

Below are the provided data files, and I uploaded them to my BigQuery workspace.

[Market 1](https://docs.google.com/spreadsheets/d/1a9IKjkvOvYHRx84SyRdp4Sq81EzgeOZPufcRtrUcAIc/template/preview#gid=775366698)

[Market 2](https://docs.google.com/spreadsheets/d/19CINdvAwp-2RF5pphkLywZLQJyJu66EOjX6CgrW32nA/template/preview#gid=2065220237)

[Market 3](https://docs.google.com/spreadsheets/d/1K6X9ZhjWtbneBss7PQH7IobGCzQ5NzG1hxs1D-hbsZM/template/preview?resourcekey=0-q90E-1XwD8nkNSjs0Ws3-w)

These three data tables have the same fields, so we can use a simple `UNION ALL` clause to merge them.

| Field | Description | Type | Note |
|:---:|---|:---:|---|
|`date_created`| Date when calls happen | Date |  |
|`contacts_n`| Number of first-time calls | String | Cast to INT |
|`contacts_n_1`| Number of repeat calls received within 1 day of first contact | String | Cast to INT|
|`contacts_n_2`| Number of repeat calls received within 2 day of first contact | String | Cast to INT|
|`contacts_n_3`| Number of repeat calls received within 3 day of first contact | String | Cast to INT|
|`contacts_n_4`| Number of repeat calls received within 4 day of first contact | String | Cast to INT|
|`contacts_n_5`| Number of repeat calls received within 5 day of first contact | String | Cast to INT|
|`contacts_n_6`| Number of repeat calls received within 6 day of first contact | String | Cast to INT|
|`contacts_n_7`| Number of repeat calls received within 7 day of first contact | String | Cast to INT|
|`new_type`| Label for type of issue | String | |
|`new_market`| Label for city service area | String | |

```
CREATE TABLE fiber.market_all AS
SELECT
  *
FROM (
  SELECT
    date_created,
    contacts_n,
    CASE WHEN contacts_n_1="null" THEN '0' ELSE contacts_n_1 END AS contacts_n_1,
    CASE WHEN contacts_n_2="null" THEN '0' ELSE contacts_n_2 END AS contacts_n_2,
    CASE WHEN contacts_n_3="null" THEN '0' ELSE contacts_n_3 END AS contacts_n_3,
    CASE WHEN contacts_n_4="null" THEN '0' ELSE contacts_n_4 END AS contacts_n_4,
    CASE WHEN contacts_n_5="null" THEN '0' ELSE contacts_n_5 END AS contacts_n_5,
    CASE WHEN contacts_n_6="null" THEN '0' ELSE contacts_n_6 END AS contacts_n_6,
    CASE WHEN contacts_n_7="null" THEN '0' ELSE contacts_n_7 END AS contacts_n_7,
    new_type,
    new_market
  FROM `rugged-nucleus-443907-r9.fiber.market_1`
  UNION ALL
  SELECT
    date_created,
    contacts_n,
    CASE WHEN contacts_n_1="null" THEN '0' ELSE contacts_n_1 END AS contacts_n_1,
    CASE WHEN contacts_n_2="null" THEN '0' ELSE contacts_n_2 END AS contacts_n_2,
    CASE WHEN contacts_n_3="null" THEN '0' ELSE contacts_n_3 END AS contacts_n_3,
    CASE WHEN contacts_n_4="null" THEN '0' ELSE contacts_n_4 END AS contacts_n_4,
    CASE WHEN contacts_n_5="null" THEN '0' ELSE contacts_n_5 END AS contacts_n_5,
    CASE WHEN contacts_n_6="null" THEN '0' ELSE contacts_n_6 END AS contacts_n_6,
    CASE WHEN contacts_n_7="null" THEN '0' ELSE contacts_n_7 END AS contacts_n_7,
    new_type,
    new_market
  FROM `rugged-nucleus-443907-r9.fiber.market_2`
  UNION ALL
  SELECT
    date_created,
    contacts_n,
    CASE WHEN contacts_n_1="null" THEN '0' ELSE contacts_n_1 END AS contacts_n_1,
    CASE WHEN contacts_n_2="null" THEN '0' ELSE contacts_n_2 END AS contacts_n_2,
    CASE WHEN contacts_n_3="null" THEN '0' ELSE contacts_n_3 END AS contacts_n_3,
    CASE WHEN contacts_n_4="null" THEN '0' ELSE contacts_n_4 END AS contacts_n_4,
    CASE WHEN contacts_n_5="null" THEN '0' ELSE contacts_n_5 END AS contacts_n_5,
    CASE WHEN contacts_n_6="null" THEN '0' ELSE contacts_n_6 END AS contacts_n_6,
    CASE WHEN contacts_n_7="null" THEN '0' ELSE contacts_n_7 END AS contacts_n_7,
    new_type,
    new_market
  FROM `rugged-nucleus-443907-r9.fiber.market_3`
  )

```

## Part 3: Creating a Reporting Dashboard
<div class='tableauPlaceholder' id='viz1733820955163' style='position: relative'><noscript><a href='#'><img alt='Dashboard 1 ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Go&#47;GoogleFiberCaseStudy_17334879971310&#47;Dashboard1&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='GoogleFiberCaseStudy_17334879971310&#47;Dashboard1' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Go&#47;GoogleFiberCaseStudy_17334879971310&#47;Dashboard1&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en-US' /></object></div>
<script type='text/javascript'>
 var divElement = document.getElementById('viz1733820955163');
 var vizElement = divElement.getElementsByTagName('object')[0];                    
 if ( divElement.offsetWidth > 800 ) { vizElement.style.width='1000px';vizElement.style.height='827px';} 
 else if ( divElement.offsetWidth > 500 ) { vizElement.style.width='1000px';vizElement.style.height='827px';} 
 else { vizElement.style.width='100%';vizElement.style.height='1377px';}                     
 var scriptElement = document.createElement('script');                    
 scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';                    
 vizElement.parentNode.insertBefore(scriptElement, vizElement);                
</script>
