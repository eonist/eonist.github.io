Notes on in app purchase <!--more-->

### Gotchas:
- Some users cant pay (kids etc): Consider hiding pay with: `AppStore.canMakePayments.`
- Share subscription with 5 family members: (Create a special price, 50% off etc)
- Let users get a refund: ` beginRefundRequest(for:in:);`
- Redeem codes can be utilized to create some FOMO, and VIP feel: `presentCodeRedemptionSheet`
- Supporting subscription management means people can upgrade, downgrade, or cancel a subscription without leaving your app.
- Display subscription info with `Product.SubscriptionInfo.`
- system-provided subscription-management UI.  `showManageSubscriptions(in:).`
- ⭐️ For an application installed through TestFlight Beta the receipt file is named StoreKit/sandboxReceipt vs the usual StoreKit/receipt (on device, not in simulator)

### Resources:
- https://developer.apple.com/design/human-interface-guidelines/in-app-purchase/overview/introduction/
- https://developer.apple.com/design/human-interface-guidelines/in-app-purchase/overview/auto-renewable-subscriptions/
- https://developer.apple.com/documentation/storekit/skpaymentqueue/3566726-presentcoderedemptionsheet
- Workflow for configuring in-app purchases: https://help.apple.com/app-store-connect/#/devb57be10e7
