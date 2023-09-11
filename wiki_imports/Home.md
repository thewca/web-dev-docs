---
title: Wiki Homepage
layout: default
parent: Wiki Imports
nav_order: 1
---


Welcome to the worldcubeassociation.org wiki!

This repository hosts the source code for <https://www.worldcubeassociation.org/>, and is maintained by the World Cube Association Software Team (WST for short).

## Are you interested in joining the team?

Awesome, we need help! Please take a look at [[how to join the WCA Software Team|How to join the WCA Software Team]]!

## Are you interested in translating the website into your native language?

Thanks in advance! We are a global organization, and we love being able to show that by supporting the many languages our members speak. Please see [[translating the website|Translating the website]].

## Are you a member of the WCA Regulations Committee?

* [[Updating TNoodle|Updating TNoodle]]

## Are you a developer looking to integrate with the WCA website?

If you only need to read data from the WCA website, we recommend the excellent [unofficial API](https://wca-rest-api.robiningelbrecht.be/) created by a member of the community.  

If you are looking to write data, we *do* have a v0 API you can interact with, and we would love for you to battle test it and give us feedback! Please bear in mind that we are a small team, and we haven't taken the time to document our API. We plan to do so with v1 of the API, but we do not have a timeline for when we will release a v1 of our API. For now, if you want to figure out what our API does, we recommend looking at the source code (see [here](https://github.com/thewca/worldcubeassociation.org/blob/master/WcaOnRails/config/routes.rb), search for "namespace :api" at the bottom) or the emails content . If you have further questions, feel free to file an issue here on GitHub, or email the WCA Software Team at <mailto:software@worldcubeassociation.org>.

If you are interested in API design and want to help us with developing v1 of our API, please reach out to us over email and we'll figure out some tasks for you to take on =)

We do have some documentation on our OAuth API and the official WCA competition data format standard:

* See our [[OAuth documentation notes|OAuth documentation notes]]
* Read about the [[WCA Competition Interchange Format (WCIF)|WCIF]]

Here we have a collection of replies to different emails about the API
* List of available endpoints [here](https://github.com/thewca/worldcubeassociation.org/blob/805d6ddbff5d55bd8ec67f8477efc579b8212de5/WcaOnRails/config/routes.rb#L176-L200).
* The French association has some integration you can use as an example, like [upcoming competition in France](https://github.com/speedcubingfrance/speedcubingfrance.org/blob/f77c134d50b7fac4a6bcb32cae40f95494054a5f/app/controllers/competitions_controller.rb#L30).
* WCA Data Protection Committee ([WDPC](mailto:dataprotection@worldcubeassociation.org)) might be interested in what you are building and we are currently trying to figure out the best way to handle third-party usage and our regulations, so, please, email them.

### (A little) More details on the endpoints
* [/competitions](https://www.worldcubeassociation.org/api/v0/competitions) lists competitions, paginated 25 per page, you can use _page_ query parameter to get more, prev/next links should also be in the response headers. Additionally you can use the _q_ query parameter to search competitions by a phrase.
* [/competitions/:id/competitors](https://www.worldcubeassociation.org/api/v0/competitions/NewHopeOpenWinter2019/competitors) lists competitors for a competition given it has results uploaded, those are the people who actually competed
* [/competitions/:id/results](https://www.worldcubeassociation.org/api/v0/competitions/NewHopeOpenWinter2019/results) lists competition results, each one corresponds to competitor times for some event&round
* [/search/regulations](https://www.worldcubeassociation.org/api/v0/search/regulations?q=stickers) allows for searching regulations by a phrase
* [/search/users](https://www.worldcubeassociation.org/api/v0/search/users?q=jonatan&persons_table=true) lets you search users (WCA accounts), but when you use the _persons_table=true_ query parameter it searches profiles with official results. It returns results paginated, already sorted by name.
* Competitions are sorted by start date DESC (and currently there is no parameter to change that behavior). You can however specify a time range you're interested in, use _start=yyyy-mm-dd_ parameter to get competitions after the given date and _end=yyyy-mm-dd_ to get competitions before the given date (combine both to specify an exact range). Additionally there is a _country_iso2_ parameter to specify competition's country, in case it's of any use for you.
* [Example](https://www.worldcubeassociation.org/api/v0/competitions?sort=start_date&start=2019-03-13) of getting upcoming competitions referring to a specific date.

Concerning the people list returning only 25 entries, see the first of the points above, the same applies for [/persons](https://www.worldcubeassociation.org/api/v0/persons).

## Are you a member of the software team?

Learn more about:

* [[Operating the website|Operating the website]]
* [[Merging and deploying|Merging and deploying]]
* [[Spinning up a new server|Spinning up a new server]]
* [[The state of the website|The state of the website]]
* [[Building Regulations locally|Building Regulations locally]]
* [[Developer database export|Developer database export]]
* [[Useful commands to know|Useful commands to know]]
* [[Payments with Stripe|Payments with Stripe]]
* [[Why do we have both "users" and "persons"?|Why do we have both "users" and "persons"?]]

### Debugging info

* [[How to access database backups|How to access database backups]]

## Organizational stuff ($, email, etc)

* [[Costs|Costs]]
* [[Emails|Emails]]
* [[How to add someone to the Software Team|How to add someone to the Software Team]]
