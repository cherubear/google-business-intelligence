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

