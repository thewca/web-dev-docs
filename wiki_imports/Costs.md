---
title: Infrastructure Costs
layout: default
parent: Wiki Imports
---

Current expenses:

- **AWS** *
- **Tarsnap**
- **Google Domains**

The non-AWS expenses are all pretty minor (they all add up to less than $50 per year) expenses.

*We could potentially save some money by getting a reserved instance (instead of on demand), but that does lock us into that particular instance for one or three years. We could also investigate other cloud service providers (but we'd need to find a good alternative to Amazon SES). For example, DigitalOcean might actually be cheaper for our purposes.

## generating cost reports

go to https://console.aws.amazon.com/billing/home?region=us-west-2#/costexplorer
and click Launch Cost Explorer.

On the advanced options > include costs related to uncheck "Credits".

The rest behaves as you would expect 

some example decent configs are 
 - under time range put in some forecast
![](http://i.imgur.com/5q1zDRR.png)
 - grouping costs by instance type
![](http://i.imgur.com/yC1GIPA.png)
