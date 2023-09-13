---
title: General
layout: default
parent: Knowledge Base
---

# Payments with Stripe

{: .warning}
> WST has recently implemented a new payments system using the Stripe API. This information may be out of date.

## Stripe Connect
We use [Stripe Connect](https://stripe.com/docs/connect). The WCA has a Stripe account referred to as the Platform account. Organizers of competitions need to have their own Stripe account. When creating a competition the organizer can connect their Stripe account to the WCA Stripe account via the WCA website. Once the organizer's Stripe account is connected, the WCA Stripe account can make charges, and issue refunds on behalf of the organizer's Stripe account.

## Testing
To test this functionality the WCA has 2 Stripe accounts. One is used as the platform account, and one is used as a test organizer's account. This test Stripe account has been connected to the platform account via the WCA website running in a development environment. This allows the tests to issue actual API calls to Stripe. The test Stripe account details are in a trait called entry_fee in the competitions.rb factory. Any testing of payments need to create a competition using this entry_fee trait so that calls to Stripe will succeed.


# Updating TNoodle

If the updating affected `tnoodle-lib`, WRC needs to approve the changes.

Whenever a new version of TNoodle is ready to be released, there are a bunch of changes to do to the website.

First of all we need to put the new binary in the `regulations-data`, and move the old one to the `old` directory. See [this](https://github.com/thewca/worldcubeassociation.org/pull/938) PR for an example.

Then we need to update the api (and its spec) to reflect the change, and the scramble page. See [this](https://github.com/thewca/worldcubeassociation.org/pull/939/files) PR for an example.

Then someone from the WRC has to make a post on the website announcing the change. See [this](https://www.worldcubeassociation.org/posts/tnoodle-0-11-3) post for an example.
The preferred day for TNoodle releases is Monday.


# Why do we have both users and persons? 

If you look at our [database schema](https://github.com/thewca/worldcubeassociation.org/blob/master/WcaOnRails/db/structure.sql), you'll notice that there are 2 tables: one for "users", and one for "persons". The persons table has existed since the start of the WCA, it tracks everyone who has competed. If you have competed, you will have an entry in the persons table. We introduced the users table in early 2015 when the Rails port began. Users are people who have created an account on the website*. As part of creating an account on the website, users are asked if they have competed before, and if they have, they are prompted to claim a WCA ID. They have to enter their birthday and it must match the birthday in the persons table for them to make this claim. Furthermore, this claim must be approved by a WCA delegate of their choice. After approval, their user will have a `wca_id` field.


*With the notable exception of some "dummy accounts" we created to keep profile pictures working. These dummy accounts have been a continuous source of pain, and we will probably get rid of them someday.
