---
title: Payments with Stripe
layout: default
parent: Wiki Imports
---



## Stripe Connect
We use [Stripe Connect](https://stripe.com/docs/connect). The WCA has a Stripe account referred to as the Platform account. Organizers of competitions need to have their own Stripe account. When creating a competition the organizer can connect their Stripe account to the WCA Stripe account via the WCA website. Once the organizer's Stripe account is connected, the WCA Stripe account can make charges, and issue refunds on behalf of the organizer's Stripe account.

## Testing
To test this functionality the WCA has 2 Stripe accounts. One is used as the platform account, and one is used as a test organizer's account. This test Stripe account has been connected to the platform account via the WCA website running in a development environment. This allows the tests to issue actual API calls to Stripe. The test Stripe account details are in a trait called entry_fee in the competitions.rb factory. Any testing of payments need to create a competition using this entry_fee trait so that calls to Stripe will succeed.
