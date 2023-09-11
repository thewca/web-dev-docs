---
title: v0 API
parent: Knowledge Base
layout: default
---

# v0 API

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

# OAuth Notes

This "documentation" assumes a lot of knowledge. If you have questions about how WCA OAuth works, please contact the WST directly at <software@worldcubeassociation.org>. We'd love help turning this wiki page into a useful document on how to set up WCA OAuth on third party websites. In the mean time, we have the documentation below. You can also see some example code at in [thewca/wca-oauth](https://github.com/thewca/wca-oauth) repository.

Known open source projects using WCA OAuth:

- [letscube](https://www.letscube.net/) - GitHub: [coder13/letscube](https://github.com/coder13/letscube)
- [birdflu.lar5.com](https://birdflu.lar5.com/) - GitHub: [larspetrus/Birdflu](https://github.com/larspetrus/Birdflu)
- [cubingza.org](https://cubingza.org/) - GitHub: [AlphaSheep/CubingZA-Website](https://github.com/AlphaSheep/CubingZA-Website)
- [live.cubing.net](https://live.cubing.net/) - GitHub: [cubing/ccm](https://github.com/cubing/ccm)
- wc2017 - GitHub: [viroulep/wc2017](https://github.com/viroulep/wc2017)
- [speedcubingfrance.org](https://speedcubingfrance.org/) - GitHub: [speedcubingfrance/speedcubingfrance.org](https://github.com/speedcubingfrance/speedcubingfrance.org)
- [Scrambles Matcher](https://viroulep.github.io/scrambles-matcher/) - GitHub: [viroulep/scrambles-matcher](https://github.com/viroulep/scrambles-matcher)
- [WCA Live](https://live.worldcubeassociation.org) - GitHub: [thewca/wca-live](https://github.com/thewca/wca-live)
- [Delegate Dashboard](https://delegate-dashboard.netlify.app/) - GitHub: [coder13/delegateDashboard](https://coder13/delegateDashboard)
- [Groupifier](https://groupifier.jonatanklosko.com/) - GitHub: [jonatanklosko/groupifier](https://jonatanklosko/groupifier)
- [Badgifier](https://dallasmcneil.com/projects/badgifier/) 

## Staging OAuth Application 

When populating the staging database (see code [here](https://github.com/thewca/worldcubeassociation.org/blob/master/WcaOnRails/lib/tasks/db.rake)), we create a "Example Application for staging". You can see the details here: https://staging.worldcubeassociation.org/oauth/applications/1. This application is configured in a very special, insecure way: it allows any redirect uri to be specified when you're using it in the OAuth flow. This means anyone can use it for testing/demo purposes, where you don't necessarily want to mess with state on the real production server.

NOTE: If you choose to use a scope other than empty string, be sure to include "public" as one of your scopes.

## How to use response_type "code" (preferred method)

NOTE: This is just a writeup of the very simple "can authenticate with grant_type authorization" test in [spec/requests/oauth/oauth_spec.rb](https://github.com/cubing/worldcubeassociation.org/blob/194bf02f53b259074077f81bbdf382416e439453/WcaOnRails/spec/requests/oauth/oauth_spec.rb#L42). Included throughout are links to the relevant parts of the test.

1) First, create an OAuth application ([see test code](https://github.com/cubing/worldcubeassociation.org/blob/194bf02f53b259074077f81bbdf382416e439453/WcaOnRails/spec/requests/oauth/oauth_spec.rb#L19-L21)) [here](https://www.worldcubeassociation.org/oauth/applications/new).

2) Then, click the Authorize button ([see test code](https://github.com/cubing/worldcubeassociation.org/blob/194bf02f53b259074077f81bbdf382416e439453/WcaOnRails/spec/requests/oauth/oauth_spec.rb#L22-L27)) for that new application:

![Authorize button](http://i.imgur.com/p6r5peC.png)

3) That should should take you to a url like `https://www.worldcubeassociation.org/oauth/authorize?client_id=...&redirect_uri=...&response_type=code&scope=...`, where you can approve the application ([see test code](https://github.com/cubing/worldcubeassociation.org/blob/194bf02f53b259074077f81bbdf382416e439453/WcaOnRails/spec/requests/oauth/oauth_spec.rb#L29-L35)):

![Authorize popup](http://i.imgur.com/DskjdYp.png)

4) After you click the green button, you'll be redirected to your redirect url. If you set your redirect url to `urn:ietf:wg:oauth:2.0:oob`, you'll be redirected back to the WCA website, and it will be very easy to see your authorization code ([see test code](https://github.com/thewca/worldcubeassociation.org/blob/194bf02f53b259074077f81bbdf382416e439453/WcaOnRails/spec/requests/oauth/oauth_spec.rb#L37-L38)):

![Success redirect URL](http://i.imgur.com/nGGQON7.png)

5) Next, you must convert the authorization code you got in step 4 into an access token ([see test code](https://github.com/thewca/worldcubeassociation.org/blob/194bf02f53b259074077f81bbdf382416e439453/WcaOnRails/spec/requests/oauth/oauth_spec.rb#L40-L47))

```bash
~ @kaladin> curl -X POST -F grant_type=authorization_code -F client_id=<APPLICATION_ID> -F client_secret=<SECRET> -F code=<CODE> -F redirect_uri=urn:ietf:wg:oauth:2.0:oob https://www.worldcubeassociation.org/oauth/token
{"access_token":"<ACCESS_TOKEN>","token_type":"bearer","expires_in":7200,"scope":"public","created_at":1458343262}~ @kaladin> 
```
This is actually a `POST` request.

API URL: `https://www.worldcubeassociation.org/oauth/token`

You need to set the following parameters correctly on the POST body:

| parameter | type | description |
| --------- | ---- | ----------- |
| grant_type    | string | The token type of the request, in this case is `authorization_code` |
| code          | string | The oauth code you just obtained                                    |
| client_id     | string | The client_id in your oauth app                                     |
| client_secret | string | The client_secret in your oauth app                                 |
| redirect_uri  | string | redirect uri                                                        |

NOTE: Surprising as it may seem, you *do* need to include redirect_uri!

If everything works, a json will be returned

![image](https://user-images.githubusercontent.com/31311826/187841927-9d3d5517-4c22-4f55-a648-5683f1108b7e.png)

6) Lastly, try hitting the `/me` route with your new access token! ([see test code](https://github.com/thewca/worldcubeassociation.org/blob/194bf02f53b259074077f81bbdf382416e439453/WcaOnRails/spec/requests/oauth/oauth_spec.rb#L76))

```bash
~ @kaladin> curl -H "Authorization: Bearer dfd52608e547abb7d44b209a01ec6f3983494dae25be333b05646361a79632b9" https://www.worldcubeassociation.org/api/v0/me
{"me":{"class":"user","url":"/results/p.php?i=2005FLEI01","id":8,"wca_id":"2005FLEI01","name":"Jeremy Fleischman","gender":"m","country_iso2":"US","created_at":"2010-12-29T22:25:01.000Z","updated_at":"2016-03-17T13:10:02.000Z","avatar":{"url":"https://d1qsrrpnlo9sni.cloudfront.net/uploads/user/avatar/2005FLEI01/1441167621.jpg","thumb_url":"https://d1qsrrpnlo9sni.cloudfront.net/uploads/user/avatar/2005FLEI01/1441167621_thumb.jpg","is_default":false}}}~ 
```

## How to use response_type "token" (implicit authorization)
This is much simpler than the response_type "code" flow described above, but should only be used in pure client side javascript applications, where there is no place to hide your application's secret. Until we write this up in more detail, you will have to deduce how it works from our [test](https://github.com/thewca/worldcubeassociation.org/blob/194bf02f53b259074077f81bbdf382416e439453/WcaOnRails/spec/requests/oauth/oauth_spec.rb#L51).


## How to use response_type "password" (implicit authorization)

This is easier than both methods described above, but please don't use it in scripts without human intervention. You should never store your password in a file! You should instead store an API token (in a secure manner, don't just commit it to GitHub). Dealing with secrets is hard =(

A python3 implementation example can be found in the [thewca/wca-oauth](https://github.com/thewca/wca-oauth) repository.

1) First, get your access token by using your user email address and your WCA user password.

```bash
curl -X POST -F grant_type=password -F username=8@worldcubeassociation.org" -F password="wca" https://staging.worldcubeassociation.org/oauth/token
{"access_token": "336c5803943502d2c960cb3dc7a7fafd87fd1ede2af068239d2c1b040cee0040","token_type": "Bearer","expires_in": 7200,"refresh_token": "44a7166ec9240ab1cda001f017d510870d9755aa059cb5cfa362c6a0d07deaa9","scope": "public","created_at": 1542981494}
```
NOTE: you have to add your scopes in this first request to get the correct access token. (e.g. `-D scope="public competitions"`)

2) And that's about all! Try hitting the `/me` route with your new access token!

```bash
curl H "Authorization: Bearer 336c5803943502d2c960cb3dc7a7fafd87fd1ede2af068239d2c1b040cee0040" https://staging.worldcubeassociation.org/api/v0/me
{"me": {"class": "user","url": "https://staging.worldcubeassociation.org/persons/2005FLEI01","id": 8,"wca_id": "2005FLEI01","name": "Jeremy Fleischman","gender": "m","country_iso2": "US","delegate_status": null,"created_at": "2010-12-29T22:25:01.000Z","updated_at": "2018-11-22T15:41:28.000Z","teams": [{"friendly_id": "wst","leader": true}],"avatar": {"url": "https://staging.worldcubeassociation.org/uploads/user/avatar/2005FLEI01/1441167621.jpg","thumb_url": "https://staging.worldcubeassociation.org/uploads/user/avatar/2005FLEI01/1441167621_thumb.jpg","is_default": false}}}
```

If this works, other routes should work, too, as long as you are authorized to do them and you choose the appropriate scopes.
