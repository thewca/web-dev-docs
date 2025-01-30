---
title: Payments
parent: Knowledge Base
layout: default
---

NOTE: This page is still under construction - Paypal is not yet implemented in the live website. 

The WCA website primarily uses Stripe for its payment processing, but WST is in the process of adding Paypal as an additional payment processor, on top ofa more generalized payments adapter which should make it easier to add more payment services in future.

## Generic Payments Workflow
1. A competition must have a `ConnectedPaymentAccount` in order to accept payments from a specific payment provider
    1. This is a polymorphic association, meaning that we can created multiple payment provider models (`ConnectedStripeAccount`, `ConnectedPaypalAccount`, etc) which an organizer can establish an association with, by linking their `Competition` via a UI we provide.
    1. The `ConnectedPaymentAccount` will have the details of the organizers 'merchant' account with the given payment provider, allowing us to provide payment links to the user
1. Once a user registers and moves onto the payment step, we create a `PaymentIntent` object indicating their desire to pay with the selected payment provider
    1. Currently, organizers can only link one payment provider. In future, users may be able to choose the which payment provider to use if both are 'connected' to the `Competition`
    1. This `PaymentIntent` will be matched with an underlying `PaymentRecord` from the payment provider if possible (`StripeRecord::payment_intent` in the case of Stripe, `PaypalRecord::order` in the case of Paypal)
    1. Note that the creation of the `PaymentIntent` happens BEFORE the user attempts payment - it is created as soon as we know they MIGHT want to pay
1. The user initiates payment, triggering either a push-based or pull-based process depending on the provider
    1. Pull-based: We inform the provider that the user would like to pay, and 'capture' the payment (Paypal uses this system - we 'pull' the payment from the user/provider) (OR IS THIS AN OPTIONAL STEP IN THE GENERIC PAYMENT PROCESS?)
    1. Push-based: The user interacts directly with the payment provider, and the payment provider informs us of the outcome of the payment (the payment provider 'push'es a notification of the payment outcome to us)
1. We record the outcome of the payment
    1. This often involves matching up 'atomic' records of payment from the payment provider (Stripe: 'charge', Paypal: 'capture') with our internal `PaymentIntent`.
    1. First we create new `PaymentRecords` for the relevant atomic unit and create relationships between them and their parent `PaymentRecord`
    1. Then, we call a method which updates the WCA `PaymentIntent`
    1. If the payment was successful, the `PaymentIntent` will create a `RegistrationPayment` object. This is the record which is used to determine is a user has paid for their registration.


## Stripe Implementation

## Paypal Implementation

_Paypal is currently in a proof-of-concept phase in its implementation - the following reflects expedient design rather than good design, and there is room for improvement._

A Paypal payment consists of the following steps:
1. An Order is created on the Paypal website - this includes information such as what is being purchase, in what currency and for what amount. 
2. The Payer approves the order - they are redirected to a Paypal web interface, where they are shown the details of the order and given a payment method to select. FUNDS DO NOT FLOW AT THIS STAGE
3. A "Capture" request is raised against the Paypal API - this is what initiates the funds transfer (ie, the Merchant is "capturing" the funds which the Payer has approved to pay in Step 2). 

### Under the hood, this works as follows: 
1. We use the Paypal SDK to display a "Pay with Paypal" (or similar) button to our users
2. The user clicks this button
3. The Paypal SDK triggers a `createOrder` function defined in our frontend. This sends a `/create-order` request to our backend API, including details like currency code and amount
4. Our backend API calls the Paypal API and creates an Order
5. If creating the order was successful, we return the payload we received from the Paypal API to the frontend
6. On receiving the response to its `/create-order` request, the Paypal SDK then opens a Paypal-controlled window for the user to approve the payment
7. Once the user approves the payment, the Paypal SDK triggers an `onApprove` function defined in our frondtend. This sends a request to the `/capture-payment` endpoint on our backend. 
8. Our backend fires a request to the Paypal API looking to capture the payment
9. If payment is captured successfully, we return the success response body from Paypal to the frontend
10. On receiving the success response, the Paypal SDK closes the Paypal window, and returns control to our frontend, which should display a success message to the user.

### Rails Payment Logic

The above described how we interact with the Paypal API to facilitate a payment between a Merchant and a Payer. We still need to record the payment as having been made on our website, though. Currently, we are adapating our implementation to the existing payments logic, which works as follows:
1. `paid_entry_fees` is defined in the `Registration` model as the sum of the `RegistrationPayments` associated which `belong_to` the `Registration`
2. Among other things, a `RegistrationPayment` stores the details of the payment (amount, currency code), and it has a polymorphic association to other objects on `:receipt`
3. During Step 5 above, we create a `PaypalTransaction` object which represents which represents a "product" we are giving the Payer the opportuntiy to pay for.
4. Upon successful capture of a payment in Step 8 above, we create `PaypalCapture` object, which `belongs_to` `PaypalTransaction` in a `has_many` relationship.
5. We then call the `record_payment` function of the Registration, and pass in the amount paid and the `:receipt` object (`PaypalTransaction` in this case). This will create a `RegistrationPayment` object associated to the `Registration`, completing our backend payments workflow.
