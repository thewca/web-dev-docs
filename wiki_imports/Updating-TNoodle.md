If the updating affected `tnoodle-lib`, WRC needs to approve the changes.

Whenever a new version of TNoodle is ready to be released, there are a bunch of changes to do to the website.

First of all we need to put the new binary in the `regulations-data`, and move the old one to the `old` directory. See [this](https://github.com/thewca/worldcubeassociation.org/pull/938) PR for an example.

Then we need to update the api (and its spec) to reflect the change, and the scramble page. See [this](https://github.com/thewca/worldcubeassociation.org/pull/939/files) PR for an example.

Then someone from the WRC has to make a post on the website announcing the change. See [this](https://www.worldcubeassociation.org/posts/tnoodle-0-11-3) post for an example.
The preferred day for TNoodle releases is Monday.