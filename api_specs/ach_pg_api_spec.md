---
title: "Payment Gateway ACH API Guide"
---

The MerchantE by Omise Payment Gateway ACH API lets merchants process payments and refunds with checking account information through the Automated Clearing House (ACH) system.

## ACH payment processing

Merchants use the MerchantE ACH solution to charge or refund customers' bank accounts.

Unlike credit card or in-store check payment services, this payment process doesn't guarantee or verify funds. Keep this in mind when releasing merchandise or services.

MerchantE partners with Jack Henry & Associates, Inc.®, a provider of payment processing services for the financial industry.

## Related documentation

The Payment Gateway API Specification and other technical documentation are available at [https://www.merchante.com/developer-portal](https://www.merchante.com/developer-portal).

The National Automated Clearing House Association (NACHA) manages the rules that merchants must follow when using the ACH network. Those rules are available at [https://nacha.org/](https://nacha.org/).

## Authorization types

The ACH payment solution supports four types of authorization:

- **PPD - Prearranged payment and deposit.** Process a sale or refund to a personal consumer account with written, signed approval.

- **CCD - Corporate credit or debit (sometimes called cash concentration or disbursement).** Process a sale or refund to a business corporate account with written, signed approval.

- **TEL - Telephone-initiated transactions.** Process a telephone sale with recorded verbal approval when the customer already has a relationship with the merchant, or when the customer calls the merchant. Example relationships include:
  - A customer has made a purchase from the merchant in the last two years
  - A written agreement exists between the customer and the merchant

- **WEB - Web-initiated transactions.** Process an internet sale to a customer's personal consumer account with online approval.

Notify MerchantE about each authorization type you plan to use before sending transactions.

### Functionality supported

The following table defines the types of functionality supported for each authorization type.

| **Type** | **Credits allowed?** | **Accept consumer accounts?** | **Accept business accounts?** | **Authorization** |
| -------- | ------------------ | --------------------------- | --------------------------- | ----------------- |
| PPD | Yes | Yes | No | Written, signed |
| CCD | Yes | No | Yes | Written, signed |
| TEL | No | Yes | No | Verbal, recorded |
| WEB | No | Yes | No | Online |

## Processing options

The following processing options are available for each of the authorization types.

| **Processing option** | **Description** |
| --------------------- | --------------------------------------------------------------------------------- |
| Sale | Charges a customer for goods or services. |
| Credit | Refunds a customer for goods or services. **Note:** MerchantE net settles on refunds. |
| Void | Deletes a transaction. **Note:** Void is only available until the transaction has been processed. |
| Store Account | Securely stores a customer account number and returns a 32-character Globally Unique Identifier (GUID) to use in its place. The routing number isn't stored, so every request that uses the GUID must also include the transit routing number. |
| Delete Account | Deletes a stored account number. |

## Returns

A return happens when the customer's bank rejects a transaction after MerchantE submits it. Returns can arrive within several days or, in some cases, several months later.

**Note:** MerchantE doesn't net settle on returns.

Common reasons for returns include:

- Insufficient funds
- Account closed
- Invalid account number
- Payment stopped

In these cases, the return typically happens within two business days.

Some returns happen much later than this time frame. If a customer claims that a product or service wasn't authorized or provided, a return can arrive as late as six to nine months after purchase.

When a return occurs, MerchantE charges the merchant's account and notifies the merchant. Whether returns are resubmitted automatically depends on the merchant's boarding setup.

**Note:** NACHA allows merchants to resubmit transactions up to two times.

Some rejected transactions don't count as returns. These include transactions rejected because of:

- An invalid routing number
- Invalid data in the request
- Incomplete data in the request

Because these rejections happen immediately at the Payment Gateway, they don't require back-end reconciliation.

## Notice of Change (NOC)

A customer's bank sends a Notice of Change to a merchant when the customer's account information is outdated or incorrect. A Notice of Change usually doesn't cause a return. The merchant must update the customer's account information.

## Transaction workflows and hold days

MerchantE sets transaction thresholds during underwriting, based on each merchant's risk profile, and shares them with the merchant. With a two-day credit hold, the merchant receives funds two days after the customer's account is debited.

### Transaction workflow

The following table shows the transaction workflow for a regular originated transaction with two credit hold days.

| **Event type** | **Occurrence** | **Transaction status** | **Settlement status** | **Comments** |
| -------------- | -------------------------------------- | ------------- | ---------------- | -------------------------------- |
| Approved | Any time before 10:00 PM EST | Approved | To be originated | Monday |
| Processed | 10:00 PM EST | Processed | To be originated | Monday |
| Originated | 1:00 AM EST | Processed | Originated/settlement pending | Tuesday |
| Settled | 1:00 AM EST on the second business day after the originated event | Processed | Settled | Thursday. This is the final status for the transaction unless it's returned. |

**Note:** For guaranteed next day debits, transactions must be sent before 10:00 PM EST.

# Request Fields Parameters

## Transaction type

The `transaction_type` field tells the Payment Gateway which API is processing the request. All ACH requests use `transaction_type=H`. The `ach_request` field then tells the Payment Gateway which ACH operation to perform.

### ACH request values

The following table describes all supported `ach_request` values.

| **Name** | **ACH request** | **Description** |
| -------------- | --------------- | ------------------------------------------------------------------------------- |
| Sale | SALE | Charges a customer for goods or services. |
| Credit | CREDIT | Refunds a customer for goods or services. Credit is available for PPD and CCD authorizations only. |
| Void | VOID | Deletes a transaction. Void is only available until the transaction has been processed. |
| Store Account | STORE | Securely stores a customer account number and returns a 32-character GUID to use in its place. Use the `transaction_id` in the response as the customer's `account_id`. |
| Delete Account | DELETE | Deletes a stored account number. |

## Authorization type

The `auth_type` field describes the type of authorization being submitted.

If you don't send `auth_type`, the gateway uses `WEB` (online authorization).

### Authorization type values

| **Value** | **Description** |
| --------- | ------------------------------------------------------------ |
| CCD | Business-to-business (B2B) paper authorization |
| PPD | Consumer paper authorization |
| TEL | Telephone voice authorization |
| WEB | Online authorization (default) |

## Request types

The API accepts five types of requests:

- Sale
- Credit
- Void
- Store Account
- Delete Account

## Sale

A Sale transaction charges a customer for goods or services.

| **Field** | **Description** | **Example** | **Type** |
| ----------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| profile_id | **Required.** ID issued by MerchantE | 94100099999900000001 | N (20) |
| profile_key | **Required.** API password assigned by MerchantE | FhcOqsIRkbrsvhlq... | AN (32) |
| transaction_type | **Required.** H = ACH API transaction type | H | A (1) |
| ach_request | **Required.** SALE = Sale transaction | SALE | A (8 (max)) |
| auth_type | **Optional.** Type of authorization being submitted. CCD = Business-to-business (B2B) paper authorization, PPD = Consumer paper authorization, TEL = Telephone voice authorization, WEB = Online authorization. The default value is WEB. If no value is submitted, the system processes the transaction like an online authorization. | WEB | A (3) |
| transit_num | **Required.** Customer bank transit routing number | 121140399 | N (9) |
| account_num | **Conditional.** Customer bank account number. account_num is required if account_id isn't used. | 3300674738 | N (17 (max)) |
| account_id | **Conditional.** Stored customer account information. This is the transaction_id response from a Store Account request. The routing number isn't stored with it, so send transit_num in the same request. account_id is required if account_num isn't used. | c0ed89a21f193873... | AN (32) |
| account_type | **Required.** Type of customer account for the ACH transaction. C = Customer checking account, S = Customer savings account | C | A (1) |
| amount | **Required.** Amount of the ACH transaction | 10.00 | N (10 (max)) |
| cust_name | **Required.** Name of the customer | Jess Smith | AN (22 (max)) |
| cust_id | **Required.** Customer ID | 123456 | AN (15 (max)) |
| ref_num | **Required.** Merchant-generated data field | Item ID 3689234 | AN (96 (max)) |
| ip_address | **Conditional.** IP address of the customer. ip_address is required for WEB authorizations. | 74.125.224.72 | AN+ (15) |

## Credit

A Credit transaction refunds a customer for goods or services.

Credit isn't available for WEB transactions. Use PPD or CCD to process credits for internet sales.

| **Field** | **Description** | **Example** | **Type** |
| ----------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| profile_id | **Required.** ID issued by MerchantE | 94100099999900000001 | N (20) |
| profile_key | **Required.** API password assigned by MerchantE | FhcOqsIRkbrsvhlq... | AN (32) |
| transaction_type | **Required.** H = ACH API transaction type | H | A (1) |
| ach_request | **Required.** CREDIT = Credit transaction | CREDIT | A (8 (max)) |
| auth_type | **Optional.** Type of authorization being submitted. CCD = Business-to-business (B2B) paper authorization, PPD = Consumer paper authorization | CCD | A (3) |
| transit_num | **Required.** Customer bank transit routing number | 121140399 | N (9) |
| account_num | **Conditional.** Customer bank account number. account_num is required if account_id isn't used. | 3300674738 | N (17 (max)) |
| account_id | **Conditional.** Stored customer account information. This is the transaction_id response from a Store Account request. The routing number isn't stored with it, so send transit_num in the same request. account_id is required if account_num isn't used. | c0ed89a21f193873... | AN (32) |
| account_type | **Required.** Type of customer account for the ACH transaction. C = Customer checking account, S = Customer savings account | C | A (1) |

## Void

A Void transaction deletes a transaction.

Void is only available until the transaction has been processed.

| **Field** | **Description** | **Example** | **Type** |
| ----------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| profile_id | **Required.** ID issued by MerchantE | 94100099999900000001 | N (20) |
| profile_key | **Required.** API password assigned by MerchantE | FhcOqsIRkbrsvhlq... | AN (32) |
| transaction_type | **Required.** H = ACH API transaction type | H | A (1) |
| ach_request | **Required.** VOID = Void transaction | VOID | A (8 (max)) |
| transaction_id | **Required.** Transaction ID of the transaction to be voided | 755f9180808f3725... | AN (32) |

## Store Account

A Store Account transaction securely stores a customer account number and returns a 32-character Globally Unique Identifier (GUID) to use in its place.

Use the `transaction_id` in the response as the customer's `account_id` in subsequent requests.

| **Field** | **Description** | **Example** | **Type** |
| ----------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| profile_id | **Required.** ID issued by MerchantE | 94100099999900000001 | N (20) |
| profile_key | **Required.** API password assigned by MerchantE | FhcOqsIRkbrsvhlq... | AN (32) |
| transaction_type | **Required.** H = ACH API transaction type | H | A (1) |
| ach_request | **Required.** STORE = Store Account transaction | STORE | A (8 (max)) |
| account_num | **Required.** Customer bank account number | 3300674738 | N (17 (max)) |

## Delete Account

A Delete Account transaction deletes a stored account number.

| **Field** | **Description** | **Example** | **Type** |
| ----------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| profile_id | **Required.** ID issued by MerchantE | 94100099999900000001 | N (20) |
| profile_key | **Required.** API password assigned by MerchantE | FhcOqsIRkbrsvhlq... | AN (32) |
| transaction_type | **Required.** H = ACH API transaction type | H | A (1) |
| ach_request | **Required.** DELETE = Delete Account transaction | DELETE | A (8 (max)) |
| account_id | **Required.** Stored customer account information. This is the transaction_id response from a Store Account request. | c0ed89a21f193873... | AN (32) |

# Response Fields & Codes

Each response contains three fields: `transaction_id`, `error_code`, and `auth_response_text`.

**Best Practice:** Record `transaction_id` responses for your own future reference.

## Response fields

The following table gives the response field descriptions and an example response.

| **Field** | **Description** | **Example** | **Type** |
| ----------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| transaction_id | Unique ID that confirms the transaction | 755f9180808f3725... | AN (32) |
| error_code | 3-character code summarizing the transaction response. The error codes table describes all error_code responses. | 000 | N (3) |
| auth_response_text | Text associated with the transaction error code. Special characters require URL encoding. | OK | A (64) |

## Error codes

The following table gives descriptions for the `error_code` provided in the Response.

| **Error code** | **Description** | **Required action** |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 000 | OK | Transaction accepted |
| 000 | Account Data Stored | Successfully stored account number |
| 101 | Invalid ID or Key | Verify the profile_id or profile_key and resubmit |
| 102 | Incomplete Request | Provide all required request fields |
| 800 | Request Failed | Internal system error, retry transaction |
| 801 | Request Type Invalid | Verify the request type (ach_request) and resubmit |
| 802 | ACH Auth Type Invalid | Verify the authorization type (auth_type) and resubmit |
| 803 | Transit Routing Number Invalid | Verify the transit routing number and resubmit |
| 804 | Account Number Invalid | Verify the account number and resubmit |
| 805 | Account Type Invalid | Verify the account type (account_type) and resubmit |
| 806 | Amount Invalid | Verify the amount (amount) and resubmit |
| 808 | Transaction Source Invalid | Resubmit with a valid value |
| 809 | ACH Auth Type Disabled | Merchant profile doesn't have the specified auth_type enabled |
| 810 | IP Address Required for WEB | Resubmit with an IP address |
| 811 | IP Address Invalid | Verify the IP address format (example: 192.166.150.2) and resubmit |
| 812 | Customer Name Invalid | Verify cust_name is 22 or fewer alphanumeric characters and resubmit |
| 813 | Customer ID Invalid | Verify cust_id is 15 or fewer alphanumeric characters and resubmit |
| 814 | Account ID Invalid | Stored account information isn't found; use a Store Account request to save the customer's account information |
| 815 | Account ID Duplicate | Account number already stored; use the transaction_id returned as the customer's account_id |
| 818 | No record of provided transaction_id | Transaction ID isn't valid |

# Request and Response Samples

This section provides sample requests with their associated sample response messages.

## Sale WEB Transaction Sample

This section provides a sample of a sale request and response message for a web-based (online) authorization. The transaction is for $0.15 and comes from the customer's checking account. The response provides the `transaction_id` and confirms that the transaction was successfully received by MerchantE.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
Host: cert.merchante-solutions.com
Connection: keep-alive
Content-Length: 578

profile_id=xxx
&profile_key=xxx
&transaction_type=H
&account_num=1234567890123
&transit_num=123456
&ach_request=SALE
&auth_type=WEB
&amount=.15
&cust_name=John
&account_type=C
&cust_id=1234
&ip_address=127.0.0.1
&ref_num=12345
```

### Response

```text
transaction_id=755f9180808f37258abe61cb76e3d65e
&error_code=000
&auth_response_text=OK
```

## Credit PPD Transaction Sample

This section provides a sample of a credit request and response message for a prearranged payment and deposit (PPD) authorization. The refund is for $0.15 and goes to the customer's checking account. The response provides the `transaction_id` and confirms that the transaction was successfully received by MerchantE.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
Host: cert.merchante-solutions.com
Connection: keep-alive
Content-Length: 578

profile_id=xxx
&profile_key=xxx
&transaction_type=H
&account_num=1234567890123
&transit_num=123456
&ach_request=CREDIT
&auth_type=PPD
&amount=.15
&cust_name=John
&account_type=C
&cust_id=1234
&ref_num=12345
```

### Response

```text
transaction_id=osi83180808f37258abe61cb76e3d08z
&error_code=000
&auth_response_text=OK
```

## Store Account Sample

This section provides a sample of a request to store a new account number. The response provides the `transaction_id` and confirms that the account number was successfully received and stored by MerchantE.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
Host: cert.merchante-solutions.com
Connection: keep-alive
Content-Length: 578

profile_id=xxxx
&profile_key=xxxx
&transaction_type=H
&ach_request=STORE
&account_num=11111111111111111
```

### Response

```text
transaction_id=c0ed89a21f193873bd9714d6b660374d
&error_code=000
&auth_response_text=Account Data Stored
```

## Delete Account Sample

This section provides a sample of a request to delete an existing stored account number. The response provides the `transaction_id` and confirms that the account number was successfully deleted by MerchantE.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
Host: cert.merchante-solutions.com
Connection: keep-alive
Content-Length: 578

profile_id=xxxx
&profile_key=xxxx
&transaction_type=H
&ach_request=DELETE
&account_id=c0ed89a21f193873bd9714d6b660374d
```

### Response

```text
transaction_id=f1b0e9c84a36358694797242cd87a02e
&error_code=000
&auth_response_text=OK
```

# Testing

Before you can use the Payment Gateway ACH API:

- Your MerchantE account needs to be approved for ACH transactions
- You need to confirm which authorization types the account will send (PPD, CCD, TEL, WEB)

To request ACH certification, contact [MerchantE](https://www.merchante.com/contact).

**Best Practice:** Use the certification environment and test merchant information for setup and testing.

The MerchantE certification servers are available 24 x 7 for unattended testing. The certification servers replicate the Payment Gateway production servers and support testing for all available functions. Certification transactions are tests and never settle to a real account. All requests should be over HTTPS.

Certification Host:
[https://cert.merchante-solutions.com/mes-api/tridentApi](https://cert.merchante-solutions.com/mes-api/tridentApi)

## Test ACH account numbers

The following table lists test accounts that can be used during testing and certification.

| **Account number** | **Routing number** |
| ------------------------- | ------------------------- |
| 11111111111111111 | 011000028 |
| 3300674738 | 121140399 |
| 3300674723 | 121140399 |

## ACH testing velocity settings

ACH accounts have limits on transaction volume, called *velocity settings*. The following table lists the velocity settings for the ACH test account by transaction type.

| **Threshold type** | **Debit** | **Credit** | **Refund** |
| ------------------------- | ------------ | ------------ | ------------ |
| Single Trans Amount | $500.00 | $50.00 | $500.00 |
| Single Day Count | 50 | 5 | 50 |
| Single Day Amount | $10,000.00 | $1,000.00 | $10,000.00 |
| Period Count (30 Days) | 300 | 30 | 300 |
| Period Amount (30 Days) | $50,000.00 | $5,000.00 | $50,000.00 |

## Required fields for request types

The following table explains the fields required for each request type supported by the ACH payment solution.

| **Field** | **Sale** | **Credit** | **Void** | **Store Account** | **Delete Account** |
| ---------------- | ----------- | ------------- | -------- | --------- | --------- |
| ach_request | Yes | Yes | Yes | Yes | Yes |
| account_id | Yes\* | Yes\* | No | No | Yes |
| account_num | Yes\* | Yes\* | No | Yes | No |
| account_type | Yes | Yes | No | No | No |
| amount | Yes | Yes | No | No | No |
| auth_type | No | No | No | No | No |
| cust_id | Yes | Yes | No | No | No |
| cust_name | Yes | Yes | No | No | No |
| ip_address | Yes for WEB | Not available++ | No | No | No |
| profile_id | Yes | Yes | Yes | Yes | Yes |
| profile_key | Yes | Yes | Yes | Yes | Yes |
| recur_flag | No | No | No | No | No |
| ref_num | Yes | Yes | No | No | No |
| transaction_id | No | No | Yes | No | No\*\* |
| transaction_type | Yes | Yes | Yes | Yes | Yes |
| transit_num | Yes | Yes | No | No | No |

**Note:** \* Account information is required for sale and credit requests. You can use `account_id` or `account_num` to satisfy the requirement.

**Note:** \*\* When storing account information, the `transaction_id` in that response becomes the customer's `account_id`. To delete a stored account, send this information as `account_id`.

**Note:** ++ Credit isn't available for WEB transactions. Use PPD or CCD to process credits for internet sales.

## Version history with revisions and changelog

| **Date** | **Change** |
| ---------------------------- | -------------------------------------------------- |
| September 2026 | Rewrote for grammar. Converted to Markdown and reorganized to match the Payment Gateway API Specification layout. |
| October 2020 | Updated branding\newline Added a note to the account_id field explaining that when the field is used, the transit (routing) number is also saved and stored. Added to the Sale, Credit, and Delete Account sections. |
| December 2019 | Rebranded document\newline Added descriptions to the request and response samples |
| July 2019 | Removed recurring billing as supported functionality\newline Updated the definition for Store Account\newline Added the Credit section\newline Updated the description for auth_type in the Sale section\newline Added response code 818 to the Error Codes table |
| July 2017 | Updated Returns and Notice of Change sections\newline Added Transaction Workflows and Hold Days section\newline Added Recurring Billing section |
| March 2017 | Removed references to eCheck brand name\newline Added Void request table |
| November 2015 | Added Processing Options\newline Added account_id to request field tables\newline Added Store Account request table\newline Added Delete Account request table\newline Added account_id response field to response fields\newline Added new request and response examples |
