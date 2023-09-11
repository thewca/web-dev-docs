---
title: Spinning up a New Server
layout: default
parent: Wiki Imports
---

Spinning up a new server is now handled by terraform in the wca-iac repo.

## Changing our Google API KEY

You should go to the [developer console](https://console.developers.google.com/apis/credentials?project=wca-website&pli=1) (login with the wca.software google account which is in our credentials document), select the "WCA production key", and add the new server's IP (you should also remove the old server's IP).
