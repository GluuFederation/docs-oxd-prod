# License Management  

## Overview
In order to run the oxd server you will need a valid license from Gluu. You can signup on the [oxd website](https://oxd.gluu.org) to obtain your license. Alternatively, if you work at a large enterprise and do not want to pay monthly with a credit card, [contact us](https://gluu.org/booking) to obtain a site license.   

- Read the [account signup docs](./auth/signup/index.md)

## How it Works 
Once you sign up for a license you will be directed to a dashboard that provides your license details. 
Each time you install oxd you will need to grab your license details from your dashboard and add them to 
your oxd configuration file. 

- Read the [dashboard docs](./dashboard/index.md)   
- Read the [installation docs](../install/index.md)
- Read the [oxd configuration docs](../configuration/index.md)   

## Pricing
oxd is priced $0.33 USD per server per day. New accounts include a $50 credit that will be automatically applied to any charges incurred within the first 60 days of usage.

Each time you use your license to install oxd, the servers MAC address is recorded. For each subsequent day that the server is active, you will be charged $0.33 USD. You can use your license to install as many oxd servers as needed. 

## Billing

The oxd billing cycle has three important dates each month: the 1st, 7th and 14th of the month.

### 1st of the month
On the first of every month, your oxd usage for the previous month is compiled and a billing summary is sent to your email, detailing all active servers and the charge associated with them.

### 7th of the month
On the 7th of every month, your credit card is charged for the amount billed in the previous month. If this credit card charge fails or you do not have a credit card on your account, you will be notified of the failure and warned of a possible disabling of your oxd license.

### 14th of the month
If your credit card charge was successful on the 7th of the month, nothing happens on the 14th. However if your card was declined or you did not have a credit card on file for your account, we will try to charge your card again on the 14th of the month. If the payment fails this time, your oxd licence will be disabled and you will need to send a message to `support@gluu.org` to get it reactivated.
