---
layout: $/layouts/post.astro
title: How to get the last publish date of a form
description: If you ever needed to find out when a form was last published in a model driven app within the Power Platform (Dynamics 365 too) then you can get this, and a bunch more, using the WebAPI via the browser.
tags:
  - webapi
  - customisation
author: Reece Campbell
authorTwitter: RamblingReece
date: 2021-02-22
category: powerplatform
permalink: "/2021/02/22/how-to-get-the-last-publish-date-of-a-form/"
---

If you ever needed to find out when a form was last published in a model driven app within the Power Platform (Dynamics 365 too) then you can get this, and a bunch more, using the WebAPI via the browser. There is no need to write code to access this as we are simply querying the data. Below is the snippet to get that and I will soon write up a series of posts about other useful ways to use the WebAPI in this fashion.

Given your environment URL is <https://dev.crm.dynamics.com> and the form ID is c1fc3828-7e6f-4a40-8d90-52796bede0d3 then the following link would provide the date you seek:

<p>https://dev.crm.dynamics.com/api/data/v9.1/systemforms(c1fc3828-7e6f-4a40-8d90-52796bede0d3)?$select=name,publishedon</p>

Breaking this down we have the URL to the API at the start and we are selecting the v9.1 version:

<p><strong>https://dev.crm.dynamics.com/api/data/v9.1/</strong>systemforms(c1fc3828-7e6f-4a40-8d90-52796bede0d3)?$select=name,publishedon</p>

Next we are selecting the specific System Form (systemforms is the unique set name) record by ID, you can get this by editing the form and getting the ID from the URL as shown below:

<p>https://make.powerapps.com/e/07501211-21f1-454d-af56-d9aeeadbd854/s/1eba17cb-d8d2-e811-a958-000d3ab5a0d7/entity/rr_table1/form/edit/<strong>c1fc3828-7e6f-4a40-8d90-52796bede0d3</strong>?source=powerappsportal</p>

Lastly we are selecting specific fields of the record; name and publishedon in this case. We can drop this entirely to see all columns on the table:

<https://dev.crm.dynamics.com/api/data/v9.1/systemforms(c1fc3828-7e6f-4a40-8d90-52796bede0d3)?$select=name,publishedon>

The result of this query in a browser will be JSON like below where I have highlighted the field you are looking for. You can also get extensions for the browser to make this formatted and easier to read, just search for JSON Formatter extension for your browser and you should get some options.

```json
{
    "@odata.context":"https://dev.crm.dynamics.com/api/data/v9.1/$metadata#systemforms(name,publishedon)/$entity",
    "@odata.etag":"W/\"100214972\"",
    "name":"Information",
    "publishedon":"2021-02-17T17:08:38Z",
    "objecttypecode":"rr_table1",
    "type":2,
    "formid":"c1fc3828-7e6f-4a40-8d90-52796bede0d3",
    "_organizationid_value":"898fbf97-5b4d-486d-a89a-8a3801283ae2"
}
```
