# Payment Gateway API Specification

The MerchantE by Omise Payment Gateway API lets merchants and resellers process live credit card transactions.

## Credit Card Processing

Processing a payment through the API involves two key steps:

- **Authorization** is the process of obtaining approval for a transaction from the card issuer.

- **Capture** is the process of finalizing a transaction so that funds can change hands.

Authorization depends on factors such as:

- **Credit availability**

- **Account status (card not stolen)**

After a transaction is authorized, the transaction amount is removed
from the available credit on the account. This change is temporary. To
finish the transaction and have money change hands, the transaction must
be captured. Some merchants can charge customers immediately, and in
those cases, the authorization can be automatically marked for capture.
Other merchants might need to wait for an item to ship before billing
the customer. Those merchants can authorize a payment and then mark it
for capture only after the item ships.

## Card on File

Card on File fields are required for merchants to store card information
securely through the MerchantE Payment Gateway API. This is also
required if a merchant is storing card information for future use
outside of the Payment Gateway API. Visa® and Mastercard® have
introduced a new framework for dealing with Card on File (CoF)
transactions. This framework includes mandatory rules and processing
requirements that must be adhered to when merchants store a cardholder's
payment credential for use on future purchases. The term "payment
credential" is used here as the merchant may not have the full card
number in their possession. Instead, the merchant may have a 'token' or
payment credential that is linked to the card number. The merchant is
only required to follow the stored credential framework when they are
storing the credentials for use on future purchases.

## Settlement

Each day, at a time chosen by the merchant, all approved transactions
are marked for capture and automatically settled. If not set by
merchant, the default time is 9PM PDT. Each group of settled
transactions is called a batch. Settlement is the process of moving
money to or from a merchant account. Settling a transaction after 7 days
causes a change in the Interchange rate, which means higher processing
costs. This also increases the risk of a chargeback. The best
practice is to settle transactions within seven days of the
authorization. To settle a transaction after seven days, authorize a new
transaction and void the original one.

### Settlement Amounts

You cannot settle a transaction for more than the authorized amount
unless the merchant type allows tips. Merchants allowed to add tips
include:

| **MCC**  | **Business Type**         |
| ---- | ----------------------------------- |
| 5812 | Restaurants           |
| 5813 | Bars and Taverns      |
| 5814 | Fast Food Restaurants |
| 4121 | Taxicabs & Limousines |
| 7230 | Hair Salons           |
| 7298 | Health & Beauty Spas  |

Settling a transaction for less than the authorized amount causes a
partial authorization reversal, which means that part of the original
request is cancelled. The amount of time it takes for the canceled
amount to be returned to the customer varies by bank.

## International Processing and Currency

By default, the system uses United States Dollars (currency_code=840).
Contact your MerchantE representative to have your account approved for:

- **International transactions**

- **Foreign Currency Exchange (FX) transactions**

For additional currency codes, see [ISO-4217 - Currency
Codes](https://www.iso.org/iso-4217-currency-codes.html).

### Payment Gateway BlueSnap Transactions

| **Name** | **Transaction Types** | **Description** |
| ------------------------------ | -------------------------------------------------- | ------------------------------------------------------------ |
| 3DS Authentication | 3DT | This transaction type is sent to BlueSnap to obtain 3DS Payment token. This transaction is sent with the card number and expiration date. |

### BlueSnap Fields

| **Field** | **Description** | **Required** | **Example** | **Transaction Types** | **Type** |
| ---------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ | ------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------- |
| fraud_session_id | Required for all non-MOTO BlueSnap international transactions. Unique ID of the shopper whose device fingerprint information was collected on the checkout page. For more information, see [transactionFraudInfo](https://developers.bluesnap.com/v8976-JSON/reference/transaction-fraud-info). | Conditional | fbcc094208f54c0e974d56875c73af7a | Card Present Sale, Card Not Present Sale, Card Present, Pre-Authorization, Re-Authorization | AN (32) |
| threed_secure_referenceId | Required for 3DS transactions. Sent by merchant to Payment Gateway after activating BlueSnap 3DS Token. This field is returned by a 3DT transaction and sent to BlueSnap as part of Pre-Authorization, Settle, and Sale transactions. | Conditional |  | Sale, Settle, Pre-Authorization | AN |

# Risk Management and Security

## Address Verification Service (AVS)

The Address Verification Service lets merchants submit customer
information, generally the billing address and ZIP code, in the
transaction request. The system compares the request to the billing
information on file with the bank and responds with how well the values
match. Merchants must choose how to handle transactions with partial or
no matches. The Account Verification Service result will not affect the
transaction; it will be processed regardless of the result.

A Verification Only transaction lets you confirm the
billing address and ZIP code of a customer before submitting a sale.
Using the Account Verification Service qualifies card-not-present
transactions for the best possible Interchange rate. You can use this
service by submitting cardholder_street_address and cardholder_zip
request fields. The response is available in the avs_result field.

## Cardholder Verification Codes

Each card issuer puts a three or four-digit security code on its card,
though the companies use different names for the code.

- **Visa:** Card Verification Value 2 (CVV2)

- **Mastercard:** Card Verification Code 2 (CVC2)

- **American Express:** Card Identification Data (CID)

- **Discover:** Card Identification Data (CID)

The gateway will process the transaction regardless of the verification
response. Some banks decline a transaction if a **No Match** response
occurs. The security code for a credit card cannot be saved after a
transaction is authorized. You can use this service by submitting a cvv2
request field. The response is available in the cvv2_result field.

## Store Card

Merchants can securely store a cardholder account number and receive a
32-character Globally Unique Identifier to use in its place. The
```transaction_id``` in the response from the Store Card request becomes the
customer's `card_id`.

## Commercial and Purchase Card Extended Data

Commercial and Purchase card requests may require additional information
like purchase order or tax amount. Submitting this information is
necessary to qualify for the Commercial or Purchase Card processing
rate. Find additional information about commercial and purchase cards in
the Commercial and Purchase Cards request section and the Level 3
Processing section.

## Dynamic Doing Business As (DBA)

The use of Dynamic Doing Business As (DBA) requires ME Risk approval.
Please contact Solution Consulting at [solution.consulting@merchante.com](mailto:solution.consulting@merchante.com)
for more information. Data in these fields will override the profile DBA
data. The client must complete Payment Gateway Certification for this
feature and provide a static prefix value of 3, 7, or 12 characters to
be set in the merchant profile. If the merchant profile is not allowed
to include this prefix, the Merchant DBA value will be used.

### DBA Fields

| **Name** | **Description** | **Required** | **Type** | **Comments** |
| ------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | -------------------------------- | --------------------------------------------------------------------------------------------- |
| merchant_name | DBA Name to appear on the cardholder statement. | Optional | AN (25) | \* is an allowed character. Must comply with association regulations. First 3, 7, or 12 digits must be static followed by \*. The remaining 25 characters of the field may be unique (ex: HarleyD\* Parts). |
| merchant_phone | Customer Service phone number to be used for direct marketing. | Optional | N (10) | No dashes or special characters. |
| dm_contact_info | URL or other contact information | Optional | AN (13) |  |
| merchant_category_code | Merchant Category Code (SIC) to be used when settling the transaction. | Optional | N (4) | Consult MerchantE for valid MCC codes. |
| merchant_city | Merchant city | Optional | AN (13) |  |
| merchant_state | Merchant state | Optional | AN (3) |  |
| merchant_zip | Merchant zip | Optional | AN (9) |  |

## Security Obligations

Both merchants and resellers have security obligations that must be
satisfied in order to process with MerchantE.

### Merchants

Merchants who choose to accept credit card payments must follow the
Payment Card Industry Data Security Standards. For example, some fields
used by the gateway, like card track data or cardholder verification
codes, should not be stored. Information about PCI compliance is
available at:

- **[https://www.pcisecuritystandards.org/](https://www.pcisecuritystandards.org/)**

- **[https://www.visa.com/cisp](https://www.visa.com/cisp)**

Merchants are also responsible for building safe, secure systems to
process API requests and responses. For example, merchants should use
HTTPS protocol and POST requests on a network following SSL/TLS
standards.

### Resellers

Resellers must be certified in Payment Application Data Security
Standards (PA-DSS). Merchants who act both as merchants and as resellers
need to follow PCI and PA-DSS requirements. Contact your MerchantE
representation for more information about reseller certification.

## Zero Value Account Verification Service (ZVAV)

American Express is introducing a Zero Value Account Verification
Service (ZVAV). This service allows a merchant to validate the
legitimacy of a card using a zero-dollar transaction amount. This new
service will be provided as an enhancement to the existing AMEX
Automated Address Verification Only (AAV) and Card Identification
Verification Only (CID) transactions. Once activated, when merchants use
the Verification Only services, they will receive the card status and
the result of the AAV or CID inquiry. Or they can initiate a
verification-only transaction without the AAV or CID and still receive
the ZVAV card status results.

# Card Brand Processing Considerations

## Card on File/Tokenization

Although various card brands may have different requirements for storing
payment credentials, the current specifications included in this
document do not require a merchant to process differently between
individual card brands. For integration simplicity, the Payment Gateway
handles each of the respective transactions appropriately. 

A Card on File transaction is a transaction where the card credentials come from a file
that is managed by the merchant. If the transaction uses card
credentials that came directly from the Cardholder and the merchant will
only use them for this transaction, then this is NOT a Card on File
transaction. 

There are three types of Card on File transactions:

- **Cardholder-Initiated Transaction (CIT)** — Transactions that
are initiated by the Cardholder and use the card credentials that have
already been stored by the merchant. An example is a web site customer
that logs into that web site account and makes a purchase using the card
credentials that are stored by that web site for use on that web site.

- **Merchant-Initiated Transaction (MIT)** — Transactions that
are initiated by the Merchant per an agreement they have with the
Cardholder and use the card credentials that have already been stored by
the merchant. An example is a Fitness Center that automatically
processes a monthly transaction for the cardholder's membership.

- **Initial Card on File transaction (COF)** — The transaction where the
issuing bank is notified that the merchant will be storing the card
credentials and using them to process additional transactions in the
future.

## Initial Card on File Transaction (COF)

### COF Request Fields

| **Field** | **Description** | **Required** | **Example** | **Type** |
| ------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| account_data_source | Customer data source. Always `Y` for COF. | Required | Y | AN (1) |
| billing_address_1* | First line of the customer’s billing street address, used for address verification (AVS) | Required | 123 Main St | ANS (99) |
| billing_country_code* | ISO 3166 alpha-3 country code for the customer’s billing address | Required | USA | A (3) |
| billing_postal_code* | Customer’s billing postal or ZIP code, used for address verification (AVS) | Required | 30301 | ANS (16) |
| billing_state* | Customer’s billing state or province abbreviation | Required | GA | ANS (3) |
| card_exp_date | Card expiration date, submit as MMYY | Capture | 1222 | N (4) |
| card_id | Stored customer card information. This is the `transaction_id` response from a Store Card request | Required | 8fc94e10b12130f4... | AN (32) |
| card_number | Payment card number | Conditional | 4012301230158 | N (5-22) |
| card_swipe | Contents of payment card read by an electronic device | Conditional | N/A | N/A (N/A) |
| cvv2 | Security code printed on card (CVV, CID, CVV2) | Required | 123 | AN (3-4) |
| device_id* | Unique identifier for the customer’s device, used for fraud detection and device fingerprinting | Optional | d7f3a9b2c1e8f5a0 | ANS (1-64) |
| email_address* | Email associated with the consumer’s identity and transaction | Optional | `customer@example.com` | ANS (99) |
| ip_address* | IP address of the consumer’s device at the time of the transaction. | Optional | 74.125.224.72 | ANS (1-45) |
| profile_id | ID issued by MerchantE | Required | 94100099999900000... | N (20) |
| profile_key | API password assigned by MerchantE | Required | FhcOqsIRkbrsvhlq... | AN (32) |
| store_card | Creates permanent token request | Optional | y | AN (1) |
| transaction_id* | Unique identifier for the transaction. | Required | 8fc94e10b12130f4... | AN (32) |
| transaction_type | **X** = Delete stored card<br /> **T** = Temporary token | Required | X | AN (1) |

**Note:** * Fields added for the Visa Digital Commerce Authentication Program.

## Initial Permanent Token Request

Permanent Tokens are created by including `store_card=y` in an
authorization request. The transaction response will include the value
of the permanent token. Permanent tokens do not expire and can be used as many times as needed.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_key=xxx&profile_id=94100006000500000010&card_number=484718xxxxxx6649&
card_exp_date=06/27&cvv2=123&transaction_amount=1.00&transaction_type=D&
account_data_source=y&store_card=Y&cardholder_street_address=2201
```
### Response

```text
transaction_id=1e5ded8772bb3e98abba30923a54ddec
&error_code=000
&auth_response_text=NoMatch
&avs_result=N
&auth_code=T4602H
&card_id=3c9c9624f7bd442a9cbb6f5acd367501
```

**Note:** The card ID is the permanent token generated when you include `store_card=y` in the request.

## Initial Temporary Token Request

The `transaction_id` in this response becomes the card_id for this card.
Temporary tokens expire after a single use in an authorization request
or 30 minutes after creation, if unused.

## Cardholder-Initiated Transaction (CIT)

### CIT Request Fields

| **Field** | **Description** | **Required** | **Indicator Value / Definition** | **Type** |
| -------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------- | -------------------------------- |
| cit_mit_indicator | Use the value that best meets your use case. This is submitted in subsequent CoF transactions. | Conditional | Cardholder-Initiated:<br />**C101** — Credential-on-File (ad-hoc),<br />**C102** — Standing Order (variable amount and fixed frequency),<br />**C103** — Subscription (fixed amount and frequency),<br />**C104** — Installment (single purchase with known amount and set frequency) | AN (4) |
| account_data_source | For details, see [Account Data Source](#account-data-source-code) (Include `account_data_source=Y` on subsequent temporary token sales with CIT.) | Conditional | Y=subsequent card on file (COF) | AN (1) |
| card_on_file | Transaction is card on file. (Include `card_on_file=Y` on subsequent temporary token sales with CIT.) | Conditional | Y or N (default) | AN (1) |


## CIT Temporary Token Sale Request Sample

Temporary tokens are good for 30 minutes and can only be used once.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx
&profile_key=yyy
&card_id=sc0c6382beba45e7b854eac7870be867
&card_exp_date=1224
&card_on_file=Y
&account_data_source=Y
&transaction_amount=20.24
&transaction_type=D
&moto_ecommerce_ind=2
&cit_mit_indicator=C101
```

### Response

```text
transaction_id=9eefeab4426c37328afd41d3b4fb0357
&error_code=000
&auth_response_text=Approval T84231
&avs_result=0
&auth_code=T84231
```

## CIT Permanent Token Sale Request Sample

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx
&profile_key=yyy
&card_id=de2072a20064328c89fe1c84a8355819
&card_on_file=Y
&transaction_amount=20.25
&transaction_type=D
&moto_ecommerce_ind=2
&cit_mit_indicator=C104
```

### Response

```text
transaction_id=b5fa9301640a39dd9d922f1b5d280ee8
&error_code=000
&auth_response_text=Approval T84264
&avs_result=0
&auth_code=T84264
```

## CIT COF with Card_number Request Sample

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx
&profile_key=xxx
&card_number=5123012301230120
&card_exp_date=1299
&transaction_amount=10
&transaction_type=D
&moto_ecommerce_ind=2
&account_data_source=Y
&card_on_file=Y
&cit_mit_indicator=C101
```

### Response

```text
transaction_id=4d7b56e3107335999c4107aabefa3014
&error_code=000
&auth_response_text=Approval T40330
&avs_result=0
&auth_code=T40330
```

## CIT Subsequent COF With Card_id Request For Recurring Sample

### Request

```text
profile_key=xxx
&profile_id=94100006000500000010
&card_id=3c9c9624f7bd442a9cbb6f5acd367501
&transaction_amount=1.00
&transaction_type=D
&moto_ecommerce_ind=2
&account_data_source=Y
&cit_mit=c103
&recurring_pmt_num=1
&card_on_file=Y
```

### Response

```text
transaction_id=b88918d8aa5733feaf9e86ff30410e93
&error_code=000
&auth_response_text=No Match
&avs_result=N
&auth_code=T4636H
```

## CIT Subsequent COF With Card_id Request For Installments Sample

### Request

```text
profile_key=xxx
&profile_id=94100006000500000010
&card_id=3c9c9624f7bd442a9cbb6f5acd367501
&transaction_amount=1.00
&transaction_type=D
&moto_ecommerce_ind=3
&account_data_source=Y
&cardholder_street_address=2201
&cit_mit=c104
&recurring_pmt_num=1
&recurring_pmt_count=3
&card_on_file=Y
```

### Response

```text
transaction_id=45fcf7051475322c8135abd9cb79bb9e
&error_code=000
&auth_response_text=No Match
&avs_result=N
&auth_code=T4644H
```


## CIT Subsequent COF With Card-id Request With MOTO E-COMM 7 Sample

### Request

```text
profile_key=xxx
&profile_id=94100006000500000010
&&card_id=3c9c9624f7bd442a9cbb6f5acd367501
&transaction_amount=1.00
&transaction_type=D
&moto_ecommerce_ind=7
&account_data_source=Y
&cardholder_street_address=2201
&cit_mit=c101
&CVV2=123
&card_on_file=Y
```

### Response

```text
transaction_id=fe9d089026763613a4fa75d80e8c736c
&error_code=000
&auth_response_text=No Match
&avs_result=N
&auth_code=T4654H
```


## CIT Subsequent COF With Card_number Request For Recurring Sample

### Request

```text
profile_key=xxx
&profile_id=94100006000500000010
&card_number=400070xxxxxx1681
&transaction_amount=1.00
&transaction_type=D
&card_exp_date=06/27
&moto_ecommerce_ind=2
&account_data_source=Y
&cardholder_street_address=2201
&CVV2=123
&recurring_pmt_num=1
&card_on_file=Y
```

### Response

```text
transaction_id=d2e3f6856b253d72830496b83c3cdce5
&error_code=000
&auth_response_text=No Match
&avs_result=N
&auth_code=T2845H
```

## CIT Subsequent COF With Card_number Request For Installment Sample

### Request

```text
profile_key=xxx
&profile_id=94100006000500000010
&card_number=400070xxxxxx1681
&transaction_amount=1.00
&transaction_type=D
&card_exp_date=06/27
&moto_ecommerce_ind=3
&account_data_source=Y
&cardholder_street_address=2201
&CVV2=123
&recurring_pmt_num=1
&recurring_pmt_count=3
&card_on_file=Y
```

### Response

```text
transaction_id=d2e3f6856b253d72830496b83c3cdce5
&error_code=000
&auth_response_text=No Match
&avs_result=N
&auth_code=T2845H
```

## Merchant-Initiated Transaction (MIT)

## MIT Indicators Table

| **Field** | **Description** | **Required** | **Indicator Value / Definition** | **Type** |
| -------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------- | -------------------------------- |
| cit_mit_indicator | Use the value that best meets your use case. This is submitted in subsequent CoF transactions. | Conditional | **Merchant-Initiated Recurring Payment or Installment** | AN (4) |
|  |  |  | M101 — Unscheduled Credential-on-File (fixed or variable amount with no regular intervals) |  |
|  |  |  | M102 — Standing Order (variable amount and fixed frequency at regular intervals) |  |
|  |  |  | M103 — Subscription (fixed amount and frequency) |  |
|  |  |  | M104 — Installment (single purchase with known amount and set frequency) |  |
|  |  |  | **Merchant-Initiated Industry Practice** |  |
|  |  |  | M205 — Partial Shipment (each shipment is a separate transaction) |  |
|  |  |  | M206 — Related/Delayed Charge (additional charge after initial services rendered and paid) |  |
|  |  |  | M207 — No Show Charge (penalty charged according to the merchant's cancellation policy) |  |
|  |  |  | M208 — Resubmission (previous authorization attempt declined but the issuer allows a re-attempt) |  |
| account_data_source | For details, see [Account Data Source](#account-data-source-code) (Include `account_data_source=Y` on subsequent temporary and permanent token sales and on file sales using a card number with MIT.) | Conditional | Y | AN (1) |
| card_on_file | Transaction is card on file. (Include `card_on_file=Y` on subsequent temporary and permanent token sales and on file sales using a card number with MIT.) | Conditional | Y or N (default) | AN (1) |
| merchant_initiated | Transaction is merchant-initiated. (Include `merchant_initiated=Y` on subsequent temporary token sales and on file sales using a card number with MIT.) | Conditional | Y or N (default) | AN (1) |

### Temporary Token Sale with MIT Sample

Temporary tokens are good for 30 minutes and can only be used once.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx
&profile_key=yyy
&card_id=de2072a20064328c89fe1c84a8355819
&card_on_file=Y
&transaction_amount=20.25
&transaction_type=D
&moto_ecommerce_ind=2
&account_data_source=Y
&merchant_initiated=Y
&cit_mit_indicator=M102
```

### Response

```text
transaction_id=7494336097193f7f9d9dd3f273f4bbdd
&error_code=000
&auth_response_text=Approval T84275
&avs_result=0
&auth_code=T84275
```

## MIT Permanent Token Sale Request Sample

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx
&profile_key=yyy
&card_id=de2072a20064328c89fe1c84a8355819
&card_on_file=Y
&transaction_amount=20.25
&transaction_type=D
&moto_ecommerce_ind=2
&merchant_initiated=Y
&cit_mit_indicator=M102
```

### Response

```text
transaction_id=7494336097193f7f9d9dd3f273f4bbdd
&error_code=000
&auth_response_text=Approval T84275
&avs_result=0
&auth_code=T84275
```

## MIT Subsequent COF With Card_id Request With MOTO E-COMM 7 Sample

### Request

```text
profile_key=xxx
&profile_id=94100006000500000010
&&card_id=3c9c9624f7bd442a9cbb6f5acd367501
&transaction_amount=1.00
&transaction_type=D
&moto_ecommerce_ind=7
&account_data_source=Y
&cardholder_street_address=2201
&cit_mit=m101
&CVV2=123
&merchant_initiated=Y
&card_on_file=Y
```

### Response

```text
transaction_id=fe9d089026763613a4fa75d80e8c736c
&error_code=000
&auth_response_text=No Match
&avs_result=N
&auth_code=T4654H
```

## MIT Subsequent COF With Card_id Request For Installments Sample

### Request

```text
profile_key=xxx
&profile_id=94100006000500000010
&card_id=3c9c9624f7bd442a9cbb6f5acd367501
&transaction_amount=1.00
&transaction_type=D
&moto_ecommerce_ind=3
&account_data_source=Y
&cardholder_street_address=2201
&cit_mit=m104
&recurring_pmt_num=1
&recurring_pmt_count=3
&merchant_initiated=Y
&card_on_file=Y
```

### Response

```text
transaction_id=45fcf7051475322c8135abd9cb79bb9e
&error_code=000
&auth_response_text=No Match
&avs_result=N
&auth_code=T4644H
```

## MIT Subsequent COF With Card-id Request For Recurring Sample

### Request

```text
profile_key=xxx
&profile_id=94100006000500000010
&&card_id=3c9c9624f7bd442a9cbb6f5acd367501
&transaction_amount=1.00
&transaction_type=D
&moto_ecommerce_ind=2
&account_data_source=Y
&cardholder_street_address=2201
&cit_mit=m103
&recurring_pmt_num=1
&merchant_initiated=Y
&card_on_file=Y
```

### Response

```text
transaction_id=b88918d8aa5733feaf9e86ff30410e93
&error_code=000
&auth_response_text=No Match
&avs_result=N
&auth_code=T4636H
```

## MIT Subsequent COF Sale with Card_number Request Sample


### Request

```http
POST /mes-api/tridentApi
HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx
&profile_key=xxx
&card_number=5123012301230120
&card_exp_date=1299
&transaction_amount=10
&transaction_type=D
&moto_ecommerce_ind=2
&account_data_source=Y
&card_on_file=Y
&merchant_initiated=Y
&cit_mit_indicator=M102
```

### Response

```text
transaction_id=4d7b56e3107335999c4107aabefa3014
&error_code=000
&auth_response_text=Approval T40330
&avs_result=0
&auth_code=T40330
```


# Reporting Portal

The MerchantE Reporting Portal supplements the integrated processing.

## Home Page

The home page provides:

- A snapshot of transactions pending settlement
- The number of pre-authorizations in the current batch
- A view of the last settled batch
- Important updates and industry news bulletins

## Administration Menu

The administration menu provides quick access to:

- Set the preferred settlement time
- Designate transaction acceptance based on AVS and Cardholder Verification responses

## Transaction Processing Menu

From the transaction processing menu:

- Manual transaction processing is available when integrated processing is not an option
- Virtual Terminal functionality is also available

## Batch Management

Batch Management allows for:

- Management of unsettled transactions (marking as settled or voiding)
- The ability to view and print settled batches

# Request Fields Parameters

## Account Data Source Code

This field contains a one-character code identifying the source of the
customer data in authorization requests. This value is provided as the
`account_data_source` in the Request.

### Account Data Source Code Descriptions

| **Code** | **Description** |
| ---- | ------------------------------------------------------------ |
| D | Full magnetic stripe read and transmit, Track two |
| G | Chip Card Read Data (EMV) |
| H | Full magnetic stripe read and transmit, Track one |
| P | Manually keyed, Chip Card read capable terminal |
| Q | Proximity payment device using track data rules (Track 2 Only) |
| R | Proximity payment device using VSDC or M/Chip data rules (Track 2 Only) |
| S | Full magnetic stripe read (Track 2 only), Chip Card capable terminal |
| T | Manually keyed, Track two capable |
| X | Manually keyed, Track one capable |
| Y | Subsequent Card on File |
| @ | Manually keyed, terminal has no card reading capability |

## Requirement Descriptions

| **Code** | **Description** |
| ------------------ | ------------------------------------------------------------ |
| Required | Include this field in every request, or the transaction won't process. |
| Optional | You can send this field, but the transaction processes without it. |
| Conditional | Send this field only in the scenarios described in the field's description. |
| Capture | Send this field when you capture the transaction. Capture confirms the transaction authorization so the amount can be settled. |

## Format Descriptions

| **Code** | **Description** |
| ---- | ------------------------------------------------------------ |
| AN | Alphanumeric |
| N | Numeric |
| AN+ | Alphanumeric and Special Characters (Special characters must be URL encoded.) |

## Transaction Type

The `transaction_type` field in your API request body contains a key piece
of information for credit card processing. It is the value that tells
the payment gateway API which operation to perform on the credit card
specified in the request.

### Sale Compared to Preauthorization and Settle

The most common transaction type values are Sale (D) and a combination
of Preauthorization (P) and Settle (S).

#### Transaction Type D

For straightforward sales with a fixed transaction amount that you want
to settle immediately, you use transaction_type=D. Transaction type D is
commonly used with point-of-sale retail and online orders to purchase a
product at a fixed price. The sale is authorized and automatically
marked for settlement.

##### Example Request Body Excerpt for transaction_type=D

```text
profile_id=xxx&profile_key=yyy&transaction_type=D
```

#### Transaction Type P and S

For authorizations where the final amount is unknown, you separate the
sale into two steps. You first ensure the transaction is authorized with
transaction_type=P. A common example of this is a hotel front desk
capturing your credit card when you check in. When your stay is over and
your hotel bill is finalized, the hotel wants to settle the final
amount. You use transaction_type=S to mark your pre-authorized
transaction for capture.

##### Example First Request Body Excerpt for transaction_type=P

```text
profile_id=xxx&profile_key=yyy&transaction_type=P
```

##### Example Second Request Body Excerpt for transaction_type=S

```text
profile_id=xxx&profile_key=yyy&transaction_type=S
```

For full examples of these requests, see the [Request Examples](#sale-sample).

## Bin Lookup 

You can do a bin lookup before submitting an authorization request. This determines whether a customer's card is eligible for a surcharge.  It is
required because the surcharge fee must be included in the total
authorization amount at the time of the original authorization. You cannot
add a surcharge retroactively.

### Transaction Type CT

Use `transaction_type=CT` to do a bin lookup.

| **Required Card Number Digits for CT** | **Accepted** | **Parameter**
| ------------------------------ | ---------------------------- | ------------------- |
| Full card number (FCN) | Yes | `card_number` |
| 6-10 digit (BIN) | Yes | `bin_number` |
| Fewer than 6 digits | No | N/A |

You can call `transaction_type=CT` in either of two ways. Submit the full
card number using the existing `card_number` field, or submit only the
card's BIN using the `bin_number` field. The `bin_number` field must be
numeric and between 6 and 10 digits — submitting fewer than 6 digits
returns an error. Using `bin_number` lets you determine surcharge
eligibility without handling or transmitting the full PAN, reducing PCI
DSS scope for merchants who architect their checkout flows this way.

#### Example Request Body Excerpt for transaction_type=CT

```text
profile_id=xxx&profile_key=yyy&transaction_type=CT&bin_number=400129374
```

##### Response Codes for transaction_type=CT

For a CT request, the `auth_response_text` field does not return approval
text as it does for other transaction types. Instead, it returns one of
four values indicating the card class:

| **Value** | **Card Class** | **Surcharge Eligible** | **Description** |
| --------- | --------------- | ----------------------- | ------------------------------------------------------------------------------------------------------------------ |
| C | **Credit** | Yes | The card is a consumer credit card. Surcharging is permitted subject to card brand rules and applicable state law. |
| D | **Debit** | No | The card is a debit card (PIN or signature debit). Surcharging is not permitted on debit cards under card brand rules. |
| P | **Prepaid** | No | The card is a prepaid card. Surcharging is not permitted on prepaid cards under card brand rules. |
| UK | **Unknown** | Indeterminate | The card number could not be matched to a record in MerchantE's ARDEF tables, or a lookup error occurred. |

##### Handle an Unknown (UK) Response

A response of `UK` means MerchantE's tables did not return a definitive
card class for the BIN provided. This can occur when the card's BIN 
is not yet present in the data set, the card is issued by a
non-participating or international issuer, or a system error occurred
during processing.

Do not apply a surcharge when `UK` is returned. This violates card brand rules and may result in
chargebacks or fines. Treat the card as non-surchargeable and proceed with
the authorization at the base transaction amount.

##### Apply the Surcharge

If the card is eligible (`C`), include the `tran_fee_amount` field in the
subsequent authorization request. See the [Surcharging Sample](#surcharging-sample)
for a full request and response example.

**Note:** A BIN Lookup from MerchantE determines card-level eligibility only. Merchants must ensure their surcharge practices comply with applicable state laws and card brand rules.

### Transaction Types Value Table

The following table describes all supported `transaction_type` values.

| **Name** | **Transaction Type** | **Description** |
| ------------------------------------------------------------ | ------------------ | ------------------------------------------------------------------------------- |
| Sale | D | A sale transaction is when a card is used to pay for goods or services rendered by a merchant. If approved, a sale transaction is automatically marked for capture and settled. |
| Pre-Authorization | P | Transaction is authorized but not yet marked for capture. You should never capture an amount higher than the pre-authorized amount. For example, if a customer orders four widgets at $10 each and shipping is $8, the pre-authorized amount is $48. If the customer later cancels an out-of-stock widget before capture, you would capture $38. This capture for an amount less than the preauthorized amount is allowed. A higher capture of $58 is not allowed. |
| Settle | S | Marks a pre-authorized transaction for capture **Best Practice:** There should be at least 15 seconds between a Pre-Authorization request and a Settle request. **Multi-Capture:** You can capture part of a pre-authorized amount. You cannot capture an authorization older than 30 days. Each multi-capture request gets a unique transaction_id response. Multi-Capture is not available for FX transactions. |
| Re- Authorization | J | Resubmits a declined transaction Extends Mastercard pre-authorized transaction expiration date. Transactions older than 28 days cannot be re- authorized. **Best Practice:** If a re-authorization is declined, contact the customer for a recommended day to resubmit. Also, re-authorization is available for United States Dollar transactions only. |
| Void | V | Cancels a Pre-Authorization transaction. **Best Practice:** If you need to cancel a captured transaction, use Refund instead of Void. |
| Refund | U | If a transaction is not captured, voids the transaction. If a transaction is captured, credits the cardholder. The response lists Void, Adjustment, or Credit as confirmation. You cannot refund a transaction for more than the original amount. **Partial Refund:** Provide the transaction amount parameter in the request. Multiple refunds are permitted for a single sale transaction up to but not over the original sale amount. The refund can also support a refund of a credit. **Surcharging:** If a surcharge is applied, it must also be refunded. |
| Credit | C | Sends funds to a cardholder **Best Practice:** Use Refund instead of Credit whenever possible. Credit transactions if approved, a credit will be issued to the merchant. The response message will be similar to the sales response with an authorization code. |
| Temporary Token  | T | Stores a customer's card number for one time use. Use the transaction_id in the response as the customer's card_id. |
| Delete Stored Card | X | Removes stored card information |
| Verification Only | A | Verifies cardholder information. The card services verify different amounts of information. See Verification transactions for more information. Verification is available for United States transactions only. |
| Inquiry | I | Requests the response from an earlier transaction Inquiry is only available for 48 hours after the original transaction. A retry ID must be submitted with every transaction to use this function. |
| Close Batch | Z | Starts a batch close via the API Batch Close is normally used for Point of Sale terminals that have a close function. |
| BIN Lookup for Surcharge | CT | Determines whether a card is eligible for a surcharge before authorization by querying tables for card class (Credit, Debit, Prepaid, or Unknown). Must be called prior to authorization since the surcharge fee cannot be added retroactively. See [Transaction Type CT](#transaction-type-ct) above. |

## Customer Purchases

The credit card associations require information about how customers
make a purchase expressed in the `moto_emcommerce_ind`, which is the MOTO/E-commerce Indicator.

The default for `moto_ecommerce_ind` is `7` (E-commerce Order), so if no
information is submitted for that field, the system processes the
transaction like an Internet sale.

If the card has expired, the expired date is submitted and the
`moto_ecommerce_ind` is set to a `2` or `3`, the Payment Gateway will submit
0000 as the expiration date in the authorization request.

### MOTO/E-commerce Indicator Values

| **Description** | **Name** | **Value** |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------ |
| **Card-Present Card** is physically present with the merchant at the time of sale. Keying a card by hand instead of using an electronic card reader can produce a higher transaction fee. | moto_ecommerce_ind | **Z** — Not a Mail/Telephone Order |
| **Card Not Present: Mail/Telephone Order (MOTO)** Card is not physically present with the merchant; sale happens in writing or by phone, **Recurring** — A charge that will happen again, **Installment** — A charge that will happen again and has a defined number of payments (example: 3 payments of $19.99) | moto_ecommerce_ind | 1 — One Time Mail/Telephone Order, 2 — Recurring Mail/Telephone Order, 3 — Installment Payment or Prepayment Transactions |
| **E-Commerce Transaction** Card is not physically present with the merchant; sale happens on the Internet | moto_ecommerce_ind | **5** - Secure E-commerce, **6** - Non-authorization Secure E-commerce, **7** — Default E-commerce, **8** - Non-secure E-commerce, **A** - AMEX Digital Wallet |

# Sale Types

## Google or Apple Pay Sale

| **Field** | **Description** | **Required** | **Example** | **Type** |
| --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| profile_id | ID issued by MerchantE | Required | 94100099999900000001 | N (20) |
| profile_key | API password assigned by MerchantE | Required | FhcOqsIRkbrsvhlq... | AN (32) |
| transaction_type | D, where D denotes a sale. For more information, see the Transaction Types table. | Optional | D | AN (1) |
| card_number | Payment card number. card_number is required if card_id or card_swipe is not used. | Conditional | 4012301230158 | N (5-22) |
| moto_ecommerce_ind | Acceptable values are 5, 6, 7, and A. For more information, see the Customer Purchases table. | Required | 5 | AN (1) |
| cavv | Use for Visa and Discover. This contains a data value generated by the issuer to enable cardholder validation for 3-D Secure transactions. | Conditional | BwABBJaSmIZUMzS?VMpKYAAAAAAA%3D | AN (40) |
| tac | Use for AMEX. | Conditional | TWsyTEhIakRyQWR?Nakd4SkhoeHI%3D | AN (30) |
| ucaf_auth_data | Use for Mastercard. This is a variable length issuer generated security field up to 32 bytes in length. | Conditional | ucaf:jJJLtQa+Iws?8AREAEbjsA1MAAAA | AN (0-32) |
| xid | Use by AMEX. This field contains a value assigned to a SET or 3-D Secure transaction as a unique transaction identifier. | Conditional | b1h4MTVGa2JXMzF?hSEhOU3FqSjA%3D | AN (0 or 40) |
| requesterapp_name | Mandatory for Apple Pay and Google Pay. | Conditional | APL001, GGL001 | AN |
| token_requestor_id | Mandatory for AMEX. Hex-encoded device manufacturer identifier. | Conditional | 544553544456 | AN (0 or 11) |
| recurring_pmt_num | Required for recurring subscription-based transactions. Payment number of the transaction. Initial and subsequent recurring transactions should pass 1. | Conditional | 1 | N (2) |

## Card Present Sale

Card present transactions occur when the card is physically present at
the point of sale at the merchant's location. There are three types of
card present sale transactions:

- Magstripe Track 1

- Magstripe Track 2

- Card Present — Keyed

| **Field** | **Description** | **Required** | **Example** | **Type** |
| ---------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- | ------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| profile_id | ID issued by MerchantE | Required | 94100099999900000001 | N (20) |
| profile_key | API password assigned by MerchantE | Required | FhcOqsIRkbrsvhlq... | AN (32) |
| account_data_source | If field is provided, there must be a value matching the card entry method from the **Account Data Source Code** table. | Required | D | AN (1) |
| card_number | Payment card number. card_number is required if the card_swipe is not used. | Conditional | 4012301230158 | N (5-22) |
| payment_account_reference | Response: Conditional. Payment Account Reference **AMEX Only:** Length has to be 35 and values should be 9 for request. **Other card types: Length must be 29 and values 9 for request.** | Optional | **AMEX:** 99999999999999999999999999999999999 **Other Cards:** 99999999999999999999999999999 | AN (29 or 35) |
| card_swipe | Contents of payment card read by an electronic device. card_swipe transactions are always processed as card-present transactions. | Conditional |  | - |
| **Condition:** card_swipe is required if card_number is not used. |  |  |  |  |
| card_exp_date | Card expiration date, submit as MMYY | Required | 1222 | N (4) |
| transaction_amount | Transaction amount | Required | 12.35 | N (12 (max)) |
| transaction_type | D, where D denotes a sale | Required | D | AN (1) |
| moto_ecommerce_ind | Z, where Z denotes the transaction is not a mail/telephone order transaction | Required | Z | AN (1) |
| client_reference_number | Merchant-defined reference number. Do not send & or =. |  | 345892 | AN (96) |
| retry_id | Allows Inquiry transaction of this request. | Conditional | 987654 | AN (16 (max)) |
| **Condition:** retry_id is required if implementing the Inquiry transaction type |  |  |  |  |
| tran_fee_amount | Surcharge Only. Amount of the surcharge for a transaction. | Conditional | 1.05 | AN (9) |

## Card Not Present Sale

Card not present transactions occur when the card is not physically
present at the point of sale at the merchant's location.

| **Field** | **Description** | **Required** | **Example** | **Type** |
| ---------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- | ------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| profile_id | ID issued by MerchantE | Required | 94100099999900000001 | N (20) |
| profile_key | API password assigned by MerchantE | Required | FhcOqsIRkbrsvhlq... | AN (32) |
| account_data_source | If field is provided, there must be a value matching the card entry method from the **Account Data Source Code** table. | Required | @ | AN (1) |
| card_number | Payment card number. Do not send card_swipe or card_id if you send card_number. card_number is required if card_id is not used. | Conditional | 4012301230158 | N (5-22) |
| payment_account_reference | Response: Conditional. Payment Account Reference **AMEX Only:** Length must be 35 and values should be 9 for request. **Other card types:** Length must be 29 and values 9 for request. | Optional | **AMEX:** 99999999999999999999999999999999999 **Other Cards:** 99999999999999999999999999999 | AN (29 or 35) |
| card_id | Required if using card_id. Stored customer card information | Conditional | 6117bf65fc4f38b7bc0ca4cd46eed6d1 | AN (32) |
| card_exp_date | Card expiration date, submit as MMYY. Do not send card_exp_date if using card_id. card_exp_date is required if card_number is sent. | Conditional | 1222 | N (4) |
| card_on_file | If the transaction is card on file. (Default=No) | Optional | Yes or No, y or n | AN (1-5) |
| transaction_amount | Transaction amount | Required | 12.35 | N (12 (max)) |
| transaction_type | D, where D denotes a sale | Required | D | AN (1) |
| invoice_number | Merchant-defined invoice number. Send this field to receive preferred interchange rates. | Optional | 1234567890, No special characters | AN (20) |
| cardholder_street_address | First 19 characters of cardholder's address for address verification service use. |  | 2017 South Maple Gl, Addresses longer than 19 characters will be truncated. | AN+ (19) |
| cardholder_zip | Cardholder's ZIP code for address verification use. Send this field to receive preferred interchange rates. | Optional | 10453 | AN (5 or 9) |
| moto_ecommerce_ind | Defines type of order. 1 = One-time Mail/Telephone Order, 2 = Recurring Order, 3 = Installment Order, 7 = Internet Order |  | 7 | AN (1) |
| client_reference_number | Merchant-defined reference number. Do not send & or =. |  | 345892 | AN (96 (max)) |
| cvv2 | Security code printed on card (CVV, CID, CVV2) for card security code verification use. Requires MerchantE activation to use. |  | 123 | AN (3-4) |
| retry_id | Enables Inquiry transaction of this request. retry_id is required if implementing the Inquiry transaction type. | Conditional | 987654 | AN (16 (max)) |
| tran_fee_amount | Surcharge Only. Amount of the surcharge for a transaction. | Conditional | 1.05 | AN (9) |

## Card Present Pre-Authorization

This is the pre-authorization for the sale amount when the card is physically
present.

| **Field** | **Description** | **Required** | **Example** | **Type** |
| ---------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- | ------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| profile_id | ID issued by MerchantE | Required | 94100099999900000001 | N (20) |
| profile_key | API password assigned by MerchantE | Required | FhcOqsIRkbrsvhlq... | AN (32) |
| account_data_source | If field is provided, there must be a value matching the card entry method from the **Account Data Source Code** table. | Required | @ | AN (1) |
| card_number | Payment card number. card_number is required if card_id or card_swipe is not used. | Conditional | 4012301230158 | N (5-22) |
| payment_account_reference | Response: Conditional. Payment Account Reference **AMEX Only:** Length must be 35 and values should be 9 for request. **Other card types:** Length must be 29 and values 9 for request. | Optional | **AMEX:** 99999999999999999999999999999999999 **Other Cards:** 99999999999999999999999999999 | AN (29 or 35) |
| card_swipe | Contents of payment card read by an electronic device. card_swipe is required if card_number or card_id is not used. card_swipe transactions are always processed as card-present transactions. | Conditional |  |  |
| card_id | Stored customer card information | Conditional | 6117bf65fc4f38b7bc0ca4cd46eed6d1 | AN (32) |
| **Condition:** card_id is required if card_swipe or card_number are not used. |  |  |  |  |
| card_exp_date | Card expiration date, submit as MMYY | Capture | 1222 | N (4) |
| card_on_file | If the transaction is card on file. (Default=No) | Optional | Yes or No | AN (5) |
| transaction_amount | Transaction amount | Required | 12.35 | N (12 (max)) |
| transaction_type | P = pre-authorization | Required | P | AN (1) |
| invoice_number | Merchant-defined invoice number. Send this field to receive preferred interchange rates. | Optional | 1234567890, No special characters. | AN (20) |
| cardholder_street_address | First 19 characters of cardholder's address for address verification use. |  | 2017 South Maple Gl, Addresses longer than 19 characters will be truncated. | AN+ (19) |
| cardholder_zip | Cardholder's ZIP code for address verification use. Send this field to receive preferred interchange rates. |  | 10453 | AN (5 or 9) |
| moto_ecommerce_ind | Defines the type of order. Z = Not a Mail/Telephone Order, 1 = One-time Mail/Telephone Order, 2 = Recurring Mail/Telephone Order, 3 = Installment Mail/Telephone Order, 7 = Internet Order |  | 7 | AN (1) |
| client_reference_number | Merchant-defined reference number. Do not send & or =. |  | 345892 | AN (96 (max)) |
| cvv2 | Security code printed on card (CVV, CID, CVV2) for card security code verification use. Requires MerchantE activation to use. |  | 123 | AN (3-4) |
| retry_id | Enables Inquiry transaction of this request. retry_id is required if implementing the Inquiry transaction type. | Conditional | 987654 | AN (16) |
| tran_fee_amount | Surcharge Only. Amount of the surcharge for a transaction. | Conditional | 1.05 | AN (9) |

## Settle

A Settlement transaction makes a payment following a pre-authorization.

| **Field** | **Description** | **Required** | **Example** | **Type** |
| --------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| profile_id | ID issued by MerchantE | Required | 94100099999900000001 | N (20) |
| profile_key | API password assigned by MerchantE | Required | FhcOqsIRkbrsvhlq... | AN (32) |
| transaction_amount | Transaction amount | Required | 12.35 | N (12 (max)) |
| transaction_type | S = capture pre-authorization | Required | S | AN (1) |
| transaction_id | Unique identifier for the transaction. Use transaction_id from the pre-authorization transaction. | Required | 8fc94e10b12130f4... | AN (32) |
| invoice_number | Merchant-defined invoice number. Send this field to receive preferred interchange rates. | Optional | 1234567890 No special characters. | AN (20) |
| tran_fee_amount | Surcharge Only. Amount of the surcharge for a transaction. | Conditional | 1.05 | AN (9) |

## Re-Authorization

A Re-Authorization is a payment made after the original transaction.

| **Field** | **Description** | **Required** | **Example** | **Type** |
| ----------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------- | -------------------------------- |
| profile_id | ID issued by MerchantE | Required | 94100099999900000001 | N (20) |
| profile_key | API password assigned by MerchantE | Required | FhcOqsIRkbrsvhlq... | AN (32) |
| transaction_type | J = re-authorization | Required | J | AN (1) |
| transaction_id | Unique identifier for the transaction. Use transaction_id from the pre-authorization transaction. | Required | 8fc94e10b12130f4... | AN (32) |

## Refund

A Refund is a credit to an account after the sale has been made.

If a surcharge is applied, it must also be refunded.

| **Field** | **Description** | **Required** | **Example** | **Type** |
| --------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| profile_id | ID issued by MerchantE | Required | 94100099999900000001 | N (20) |
| profile_key | API password assigned by MerchantE | Required | FhcOqsIRkbrsvhlq... | AN (32) |
| transaction_type | Refund | Required | U | AN (1) |
| transaction_id | Unique identifier for the transaction. Use transaction_id from the pre-authorization transaction. | Required | 8fc94e10b12130f4... | AN (32) |
| transaction_amount | The amount of the transaction. Send transaction_amount when requesting a partial refund. |  | 12.30 | N (12) |

## Credit

A Credit is a new transaction that returns the sale amount.

Use Refund instead of Credit whenever possible. By default, accounts are
set up to not allow Credits to be processed. Your account must be
enabled to allow Credit requests.

| **Field** | **Description** | **Required** | **Example** | **Type** |
| ---------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| profile_id | ID issued by MerchantE | Required | 94100099999900000001 | N (20) |
| profile_key | API password assigned by MerchantE | Required | FhcOqsIRkbrsvhlq... | AN (32) |
| transaction_type | C = credit | Required | C | AN (1) |
| account_data_source | If field is provided, there must be a value matching the card entry method from the **Account Data Source Code** table. | Required | @ | AN (1) |
| card_number | Payment card number. card_number is required if card_id or card_swipe is not used. | Conditional | 4012301230158 | N (5-22) |
| payment_account_reference | Response: Conditional. Payment Account Reference **AMEX Only:** Length must be 35 and values should be 9 for request. **Other card types: Length must be 29 and values 9 for request.** | Optional | AMEX: 99999999999999999999999999999999999, Other Cards: 99999999999999999999999999999 | AN (29 or 35) |
| card_exp_date | Card expiration date, submit as MMYY | Capture | 1222 | N (4) |
| transaction_amount | Transaction amount |  | 12.30 | N (12) |
| retry_id | Enables Inquiry transaction of this request. retry_id is required if implementing the Inquiry transaction type| Conditional | 987654 | AN (16 (max)) |

## Void

A Void transaction cancels a preauthorization prior to payment.

Use Void to cancel a pre-authorization. If you need to cancel a captured
transaction, use Refund.

| **Field** | **Description** | **Required** | **Example** | **Type** |
| ----------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------- | -------------------------------- |
| profile_id | ID issued by MerchantE | Required | 94100099999900000001 | N (20) |
| profile_key | API password assigned by MerchantE | Required | FhcOqsIRkbrsvhlq... | AN (32) |
| transaction_type | Void | Required | V | AN (1) |
| transaction_id | Unique identifier for the transaction. Use transaction_id from the pre-authorization transaction. | Required | 8fc94e10b12130f4... | AN (32) |

## Batch Close

| **Field** | **Description** | **Required** | **Example** | **Type** |
| ----------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------- | -------------------------------- |
| profile_id | ID issued by MerchantE | Required | 94100099999900000001 | N (20) |
| profile_key | API password assigned by MerchantE | Required | FhcOqsIRkbrsvhlq... | AN (32) |
| transaction_type | Z = Batch Close | Required | Z | AN (1) |

## Verification

| **Field** | **Description** | **Required** | **Example** | **Type** |
| ---------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| profile_id | ID issued by MerchantE | Required | 94100099999900000001 | N (20) |
| profile_key | API password assigned by MerchantE | Required | FhcOqsIRkbrsvhlq... | AN (32) |
| account_data_source | If field is provided, there must be a value matching the card entry method from the **Account Data Source Code** table. | Required | @ | AN (1) |
| card_number | Payment card number. Do not send card_swipe or card_id if you send card_number. | Required | 4012301230158 | N (5-22) |
| card_exp_date | Card expiration date, submit as MMYY | Required | 1222 | N (4) |
| transaction_amount | Transaction amount. Must submit a value of 0.00 | Required | 12.35 | N (12 (max)) |
| transaction_type | A = verification | Required | A | AN (1) |
| moto_ecommerce_ind | Z = Not a mail/telephone order transaction |  | Z | AN (1) |
| cardholder_street_address | First 19 characters of cardholder's address. Addresses longer than 19 characters will be truncated. |  | 2017 South Maple Gl | AN+ (19) |
| cardholder_zip | Cardholder's ZIP code. Send this field to receive preferred interchange rates. | Optional | 10453 | AN (5 or 9) |

## Offline or Forced

Offline transactions (aka Forced Sale, Force Capture) are used to bypass
the authorization process by manually entering a previously obtained
authorization code to force a transaction into the clearing process.
Effective January 24, 2019, offline transactions are no longer allowed
due to the Control Force Post mandate. If you determine you have a
business case that requires the use of offline transactions, contact ME
Customer Care at (888) 288-2692 to request an exemption.

**Caution:** This section is reserved. Do not use unless a specific exemption has
been granted by MerchantE.

The use of force posted transactions is prohibited unless an exemption
is obtained. As a best practice to mitigate additional risk and expense,
MerchantE recommends avoiding using offline transactions.

| **Field** | **Description** | **Required** | **Example** | **Type** |
| ---------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| profile_id | ID issued by MerchantE | Required | 94100099999900000001 | N (20) |
| profile_key | API password assigned by MerchantE | Required | FhcOqsIRkbrsvhlq... | AN (32) |
| account_data_source | If field is provided, there must be a value matching the card entry method from the **Account Data Source Code** table. | Required | @ | AN (1) |
| card_number | Payment card number. Do not send card_swipe or card_id if you send card_number. | Required | 4012301230158 | N (5-22) |
| card_exp_date | Card expiration date, submit as MMYY | Required | 1222 | N (4) |
| card_id | Stored customer card information. This is the transaction_id response from a Store Card request. | Conditional | 6117bf65fc4f38b7bc0ca4cd46eed6d1 | AN (32) |
| **Condition:** card_id is required if card_swipe or card_number are not used. |  |  |  |  |
| transaction_amount | Transaction amount. Must submit a value of 0.00. | Required | 12.35 | N (12 (max)) |
| transaction_type | O = Offline | Required | O | AN (1) |
| invoice_number | Merchant-defined invoice number. Send this field to receive preferred interchange rates. | Optional | 1234567890 (No special characters.) | AN (20) |
| auth_code | Authorization code found outside of the API (generally by telephone). | Required | 235V73 | AN (6) |
| cardholder_street_address | First 19 characters of cardholder's address. Addresses longer than 19 characters will be truncated. | Optional | 2017 South Maple Gl | AN+ (19) |
| cardholder_zip | Cardholder's ZIP code. Send this field to receive preferred interchange rates. | Optional | 10453 | AN (5 or 9) |
| moto_ecommerce_ind | Indicates order type: 1 = One-time Mail/Telephone Order, 2 = Recurring Mail/Telephone Order, 3 = Installment Mail/Telephone Order, 7 = Internet Order |  | 7 | AN (1) |
| client_reference_number | Merchant-defined reference number. Do not send & or =. |  | 345892 | AN (96 (max)) |
| cvv2 | Security code printed on card (CVV, CID, CVV2) for card security code verification use. Requires MerchantE activation to use. |  | 123 | AN (3-4) |
| retry_id | Allows Inquiry transaction of this request. retry_id is required if implementing the Inquiry transaction type. | Conditional | 987654 | AN (16 (max)) |

## Inquiry

| **Field** | **Description** | **Required** | **Example** | **Type** |
| ----------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| profile_id | ID issued by MerchantE | Required | 94100099999900000001 | N (20) |
| profile_key | API password assigned by MerchantE | Required | FhcOqsIRkbrsvhlq... | AN (32) |
| transaction_type | I = Inquiry | Required | I | AN (1) |
| retry_id | Allows Inquiry transaction of this request. |  | 987654 | AN (16 (max)) |

# Additional Request Fields

## International Currency

Foreign Currency Exchange (FX) or International processing must be
activated for an account to use currency_code.

| **Field** | **Description** | **Example** | **Type** |
| ------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- | -------------------------------- |
| currency_code | ISO 4217 currency code for this transaction. [ISO Currency Codes](https://www.iso.org/iso-4217-currency-codes.html). Default is 840, United States Dollars. | 840 | AN (3) |

## Recurring Payments - Installments

| **Field** | **Description** | **Required** | **Example** | **Type** |
| --------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | ----------------------------------------------------------------- | -------------------------------- |
| recurring_pmt_num | Payment number of this transaction | Required | 1 | N (2) |
| recurring_pmt_coun | Total number of payments | Required | 3 | N (2) |

## American Express Request Fields

### American Express Enhanced Address and Cardholder Verification Service

If you send just cardholder_street_address and cardholder_zip, you can
find the verification results in the avs_result response. To verify the
other fields listed here, send rctl_Extended_AVS=y with your request.
You can find the explanation of enhanced address responses in the
Response Control — rctl_Extended_AVS section. **rctl_Extended_AVS
Fields**

| **Field** | **Description** | **Required** | **Example** | **Type** |
| ---------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------ | ----------------------------------------------------------------------------------------------- | -------------------------------- |
| cardholder_first_name | Cardholder first name |  | Jess | AN (15) |
| cardholder_last_name | Cardholder last name |  | Smith | AN (30) |
| cardholder_email | Cardholder's billing email |  | `jsmith@email.com` | AN+ (60) |
| cardholder_phone | Cardholder's billing phone. No dashes or special characters should be included in the phone number formatting. |  | 8005550101 | N (10) |
| cardholder_street_address | First 19 characters of cardholder's address. Addresses longer than 19 characters will be truncated. |  | 2017 South Maple Gl | AN+ (19) |
| cardholder_zip | Cardholder's billing ZIP code | Required | 11106 | AN (5-9) |

### Optional American Express Mail-, Telephone-, and Internet-Order Fields

These fields are optional. American Express may use this information to
make authorization decisions.

#### Optional Fields Descriptions

| **Field** | **Description** | **Required** | **Example** | **Type** |
| --------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------- | -------------------------------- |
| ship_to_first_name | First name for shipping purposes | Optional | Jess | AN (15) |
| ship_to_last_name | Last name for shipping purposes | Optional | Smith | AN (30) |
| ship_to_phone | Phone number for shipping purposes. No dashes or special characters should be included in the phone number formatting. | Optional | 8005550101 | N (10) |
| ship_to_address | Address where purchase will ship to | Optional | 123 Main | AN (50) |
| dest_country_code | Country code for the ship-to country. Country codes are available in the ISO 3166 standard, quick reference available at Wikipedia's ISO 3166 page. | Optional | 840 | N (3) |
| ip_address | Customer's IP address | Optional | 74.125.224.72 | AN+ (15) |
| cust_host_name | Customer's hostname (for Internet sales, generally the domain name) | Optional | web.pointofpurchase.com | AN+ (60) |
| http_browser_type | Customer's HTTP browser type | Optional | MOZILLA/5.0 (WINDOWS NT 6.1) | AN+ (60) |
| customer_ani | The customer's phone number as identified by an Automatic Number Identifier (ANI) service | Optional | 6025551212 | AN+ (15) |
| customer_ani_ii | The customer's call type as determined by an ANI service | Optional | 00 | AN (2) |
| product_sku | Unit inventory reference number or SKU (Stock Keeping Unit) | Optional |  | AN (15) |
| shipping_method | Type of shipping: 01 = same day, 02 = overnight/next day, 03 = priority (2-3 days), 04 = ground (4 or more days), 05 = electronic, 06 = ship to store. When you use shipping_method=06, send the store address as ship_to_address. | Optional | 04 | AN (2) |

## Hotel, Cruise, and Auto Rental Enhanced Fields

These fields are required for preferred Interchange rates for hotel,
cruise, and auto rental merchants.

**Travel Fields**

| **Field** | **Description** | **Required** | **Example** | **Type** |
| -------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------ | -------------------------------- |
| statement_date_begin | Date of hotel check-in, cruise departure, or start date for auto rental | Optional | 07/04/2018 | AN (10) |
| statement_date_end | Date of hotel check-out, cruise end date, return date for auto rental. If the time between statement_begin date and statement_end_date is 99 days or greater, the system will end 99 days. | Optional | 07/08/2018 | AN (10) |
| requester_name | Name of person renting room, taking cruise, or renting car | Optional | Jess Smith | AN (30) |
| name_of_place | Hotel location, cruise ship, or rental agency | Optional | Travel Rental Inc | AN (25) |
| rate_daily | Daily rate of room or car rental; Cruise can be entered as a total amount | Optional | 55.65 | AN (12) |
| no_show_ind | Whether the charge is for a no-show: Y = no-show charge, N = customer checked in or boarded | Optional | N | AN (1) |
| industry_code | Industry type: A = auto rental, D = direct marketing, H = hotel or cruise. Default is D. | Optional | A | AN (1) |
| invoice_number | Invoice, folio, rental agreement, or primary tracking number. No special characters. | Optional | 34567890 | AN (20) |

## Echo Fields

An echo field lets you send information in the request that will be
returned in the response.

| **Field** | **Description** | **Example** | **Type** |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| echo_ | Add custom field name after echo_ and set that equal to the echo field value | echo_customer_id=123456 | AN (50 (max)) |

## Message Reason Code

A message reason code is used when a merchant needs to send a deferred
authorization. This scenario may arise when a merchant's point of sale
is offline, and the merchant chooses to complete the transaction without
obtaining online authorization.

Once the merchant is back online, the merchant submits an online
authorization request. Visa requires a deferred authorization indicator
to be submitted in the message_reason_code field with deferred
authorization transactions.

| **Field** | **Description** | **Required** | **Example** | **Type** |
| -------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------- |
| message_reason_code | If a merchant performs a transaction while in offline mode, once back online, the merchant must send a message reason code for Visa transactions. Value: **5206** = deferred authorization. This field is only sent for Visa transactions that are deferred authorizations. | Optional | message_reason_code=5206 | AN (4) |

# Response Control

Response control fields allow interaction with the system based on
account or transaction features. The rctl_commercial_card request field,
for example, returns a value that identifies the type of commercial card
in the transaction (business card, corporate card, purchasing card,
non-commercial card). The rctl_account_balance field returns the
pre-paid card balance (if offered by the card issuer). The request field
value will be one of the following options:

- Yes
- Y
- True

| **Field** | **Description** | **Example** | **Transaction Type** |
| --------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| rctl_product_level | Legacy feature; returns information about the card provider payment product | rctl_product_level=y | A, D, P, J |
| rctl_commercial_card | Returns information about the type of commercial card used in the transaction | rct_commcercial_card=y | A, D, P |
| rctl_Extended_AVS | Returns extended address verification results | rctl_Extended_AVS=y | D, P |
| rctl_account_balance | Returns the pre-paid card balance if offered by the card issuer | rctl_account_balance=y | D, P |
| rctl_partial_auth | Returns the authorized amount | rctl_partial_auth=y | D, P |
| rctl_card_number_truncated | Returns a truncated version of the card number | rctl_card_number_truncated=y | All |
| rctl_resp_hash | Returns a SHA-1 hash of: Transaction ID, Profile key, Truncated card number (if rctl_card_number_truncated sent) | rctl_resp_hash=y | All |

# Response Fields & Codes

## Response Fields

The following table gives the response field descriptions and an example
response.

**Response Fields**

| **Field** | **Description** | **Example** | **Type** |
| ------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------- |
| transaction_id | Unique ID that confirms the transaction | 12f3befbd035371b9b82d5920465e071 | AN (32) |
| error_code | 3-character code summarizing the transaction response. The Error Codes table describes all error_codes responses. | 000 | AN (3-4) |
| auth_response_text | Text message describing transaction response. Special characters require URL encoding. | Exact Match | AN (64) |
| auth_code | The code provided by the issuer when a transaction is approved, partially approved, or a 'no reason to decline' code provided for successful verifications | T2067H | AN (6) |
| avs_result | Address verification response | Y | AN (1) |
| cvv2_result | CVV2/CID verification response | M | AN (1) |
| retry_count | Number of times this transaction has been requested with Inquiry | 2 | AN (2) |
| product_level | The Product Level table describes the product_level responses | A | AN (2) |
| commercial_card | The Commercial Card table describes the commercial_card responses | B | AN (1) |
| extended_AVS | The Extended AVS tables describe the extended_AVS responses | YYYYY | AN (5) |
| account_balance | Amount remaining on pre-paid card if offered by card provider | 32.50 | N (12) |
| partial_auth | Authorized amount | 12.5 Authorized Amount | N (12) |
| card_number_truncated | Truncated version of the card number | 487165xxxxxx2456 | AN (16) |
| resp_hash | SHA-1 hash of: Transaction ID, Profile key, Truncated card number (if rctl_card_number_truncated sent) | 7569af60aede1bf00c9e?ce6fa3d3c75d205b3665 | AN (32) |

## Error Codes

The following tables give descriptions for the error_code provided in
the Response.

**Visa, American Express, Discover**

| **Code** | **Description** |
| ----------------- | ------------------------------------------------------------ |
| 000 | Approved or successfully completed |
| 000 | Empty Batch |
| 001 | Refer to card issuer |
| 002 | Refer to card issuer, special condition |
| 003 | Invalid merchant |
| 004 | Pick up card (no fraud) |
| 005 | Do not honor |
| 006 | Error |
| 007 | Pick up card, special condition (fraud account) |
| 010 | Partial approval |
| 011 | Approved (V.I.P.) |
| 012 | Invalid transaction |
| 013 | Invalid amount or currency conversion field overflow |
| 014 | Invalid account number (no such number) |
| 015 | No such issuer |
| 019 | Re-enter transaction |
| 021 | No action taken |
| 025 | Unable to locate record in file |
| 028 | File temporarily not available for update or inquiry |
| 039 | No credit account |
| 041 | Lost card |
| 043 | Stolen card |
| 046 | Closed Account |
| 051 | Not sufficient funds |
| 052 | No checking account |
| 053 | No savings account |
| 054 | Expired card of expiration date is missing |
| 055 | Incorrect PIN or PIN missing |
| 057 | Transaction not permitted to cardholder |
| 058 | Transaction not permitted to acquirer / terminal (Offline/Force Post) |
| 059 | Suspected fraud |
| 06P | Verification Data Fail |
| 061 | Exc App Amt Lmt |
| 062 | Restricted card (card invalid in this region or co
 |
| 064 | Transaction does not fulfill AML requirement |
| 065 | Exc W/D Freq Lmt |
| 075 | PIN exceeded |
| 076 | Unsolicited reversal |
| 078 | Blkd, New Card |
| 079 | Already reversed (by Switch) |
| 080 | No financial impact |
| 081 | Cryptographic error found in PIN |
| 082 | Off PIN Auth Int (Offline PIN authentication interrupted) |
| 083 | Potentially fraudulent transactions declined during stand-in processing (**Visa only**) |
| 085 | No reason to decline a request for address verification, CVV2 verification, or a credit voucher or merchandise return |
| 086 | Cannot verify PIN; for example, no PVV |
| 089 | Ineligible to receive financial position information |
| 091 | Issuer or switch inoperative and STIP not applicable or not available for this transaction; Time-out when no stand-in; POS Check Service: Destination unavailable; Credit Voucher and Merchandise Return Authorizations: V.I.P. sent the transaction to the issuer, but the issuer was unavailable. |
| 092 | Financial institution or intermediate network facility cannot be found for routing (receiving institution ID is invalid) |
| 093 | Transaction cannot be completed — violation of law |
| 0Z1 | Offline Decline |
| 0Z3 | Offline Decline |
| 101 | Invalid ID or Key |
| 102 | Incomplete Request |
| 103 | Invoice Number Length Error |
| 104 | Reference Number Length Error |
| 105 | AVS Address Length Error |
| 106 | AVS Zip Length Error |
| 107 | Merchant Name Length Error |
| 108 | Merchant City Length Error |
| 109 | Merchant State Length Error |
| 110 | Merchant Zip Length Error |
| 111 | Merchant Category Code Length Error |
| 112 | Merchant Phone Length Error |
| 113 | Reference Number Must Be Numeric |
| 114 | Missing Card Holder Account Data |
| 115 | Invalid Card Number |
| 116 | Credits Not Allowed |
| 117 | Card Type Not Accepted |
| 118 | Currency Type Not Accepted |
| 119 | Retry ID length error Must be 16 characters or less |
| 120 | An invoice number is required for a 3D enrollment check |
| 121 | MOTO/e-Commerce indicator length error |
| 122 | Non-USD offline transactions are not supported |
| 123 | Client Reference Number length error |
| 127 | Invalid Level III Line Item Detail |
| 128 | Invalid Level III Merchant Tax ID |
| 129 | Invalid Level III Customer Tax ID |
| 130 | Invalid Level III Summary Commodity Code |
| 131 | Invalid Level III Ship to Zip |
| 132 | Invalid Level III Ship from Zip |
| 133 | Invalid Level III Destination Country Code |
| 134 | Invalid Level III VAT Invoice Number |
| 135 | Invalid Level III Alternate Tax Indicator |
| 136 | Transaction type does not support Level III data |
| 137 | Invalid Level III Discount Amount |
| 138 | Invalid Level III Duty Amount |
| 139 | Invalid Level III Order Date |
| 140 | Invalid Level III VAT Amount |
| 141 | Invalid Level III VAT Rate |
| 142 | Invalid Level III Alternate Tax Amount |
| 143 | Invalid Level III Line Item Count |
| 144 | Invalid Level III Card Type |
| 168 | Invalid Industry Code |
| 201 | Invalid Transaction ID |
| 202 | Invalid Transaction Amount |
| 203 | Void Failed |
| 204 | Transaction Already Settled |
| 205 | Transaction Already Voided |
| 206 | Transaction Already refunded |
| 207 | Refund failed |
| 208 | Failed to receive a response from auth host |
| 209 | Invalid tax amount |
| 210 | AVS result is declined by user |
| 211 | CVV2 result is declined by user |
| 212 | Refund amount must be between zero and the original amount |
| 213 | Only sale transactions can be refunded |
| 214 | Only one type of card data allowed per request |
| 215 | Invalid Card ID |
| 216 | Failed to load card data, retry request |
| 217 | Failed to store card data, retry request |
| 218 | Card ID parameter cannot be included in this type of transaction |
| 219 | Offline transactions require an authorization code |
| 220 | Failed to delete card data, retry request |
| 221 | Invalid Card ID |
| 222 | Card ID required |
| 223 | Retry Request ID Lookup Failed |
| 229 | Failed to find currency code for the requested country code |
| 230 | Failed to post transaction the FX service |
| 231 | FX amount in base currency is required |
| 232 | FX transactions not accepted for this account |
| 233 | Request currency code must match FX rate currency code |
| 234 | Pin debit transactions require track 2 swipe data |
| 235 | Invalid pin debit transaction type |
| 236 | Non-USD pin debit transactions are not supported |
| 237 | Batch Close Failed |
| 238 | Quit Duplicate Batch |
| 239 | Invalid retry id |
| 240 | Failed to load declined auth data |
| 300 | Failed to capture International transaction |
| 301 | Failed to void International transaction |
| 302 | Failed to refund International transaction |
| 303 | Card Verify not supported |
| 304 | Failed to reverse International authorization |
| 363 | Invalid bin number |
| 400 | VBV/MSC Enrollment Check |
| 401 | VBV/MSC Verification Failed |
| 412 | Failed to load captured transaction data |
| 413 | Multiple captures are not supported on FX transactions |
| 430 | Statement begin date must be the same or before statement end date |
| 431 | Duration between statement dates must be 99 days or less |
| 999 | Internal Error |
| B2 | Surcharge amount not supported by debit network issuer |
| N0 | Force STIP |
| N3 | Cash service not available |
| N4 | Cash request exceeds issuer or approved limit |
| N7 | Decline for CVV2 failure |
| N8 | Transaction amount exceeds preauthorized approval amount |
| Q1 | Card authentication failed |
| R0 | Stop payment order |

**Mastercard**

| **Code** | **Description** |
| ----------------- | ------------------------------------------------------------ |
| 000 | Approved or successfully completed |
| 000 | A batch number is required when submitted a transaction_type of Z |
| 001 | Refer to card issuer |
| 003 | Invalid merchant |
| 004 | Capture card |
| 005 | Do not honor |
| 008 | Honor with ID |
| 010 | Partial approval |
| 012 | Invalid transaction |
| 013 | Invalid amount |
| 014 | Invalid card number |
| 015 | Invalid issuer |
| 030 | Format error. (Verification only) Transaction type not supported by issuer |
| 041 | Lost card |
| 043 | Stolen card |
| 051 | Insufficient funds / over credit limit (Not applicable to MOTO or ATM transactions). 24 - Retry after 1 hour, 25 - Retry after 24 hours, 26 - Retry after 2 days, 27 - Retry after 4 days, 28 - Retry after 6 days, 29 - Retry after 8 days, 30 - Retry after 10 days |
| 054 | Expired card |
| 055 | Invalid PIN |
| 057 | Transaction not permitted to issuer / cardholder (Verification only) Transaction type not supported by issuer |
| 058 | Transaction not permitted to acquirer / terminal |
| 061 | Exceeds withdrawal amount limit |
| 062 | Restricted card |
| 063 | Security violation |
| 065 | Exceeds withdrawal count limit |
| 068 | Response received late |
| 070 | Contact card issuer |
| 071 | PIN not changed |
| 075 | Allowable number of PIN tries exceeded |
| 076 | Invalid / nonexistent 'To Account' specified |
| 077 | Invalid / nonexistent 'From Account' specified |
| 078 | Invalid / nonexistent account specified (general) |
| 079 | 01 Update Info, 02 Try later, 03 Do not retry, 21 Pymt Canceled. Lifecycle |
| 081 | Domestic debit transaction not allowed (regional use only) |
| 082 | 01 Update Info, 02 Try later, 03 Do not retry, 21 Pymt Canceled. Policy |
| 083 | 01 Update Info, 02 Try later, 03 Do not retry, 21 Pymt Canceled. Security |
| 084 | Invalid authorization life cycle |
| 085 | Not declined. Valid for all zero amount transactions |
| 086 | PIN validation not possible |
| 087 | Purchase amount only, no cashback allowed |
| 088 | Cryptographic failure |
| 089 | Unacceptable PIN — transaction declined — retry |
| 091 | Authorization system or issuer system inoperative |
| 092 | Unable to route transaction |
| 094 | Duplicate transmission detected |
| 096 | System error |
| 101 | Check profile_id and profile_key then retry request |
| 102 | Provide all required data |
| 103 | Confirm invoice_number is 20 or fewer characters then retry request |
| 104 | Reference_number must be 11 or fewer characters |
| 105 | Not possible in PG because it allows unlimited address chars |
| 106 | Confirm cardholder_zip is 9 or fewer characters |
| 107 | Reduce the merchant name length |
| 108 | Reduce the city name length |
| 109 | Provide a valid state |
| 110 | Provide a valid 5 or 9-digit zip |
| 111 | Provide a valid Merchant Category Code |
| 112 | Provide a valid phone number |
| 113 | Provide a valid number |
| 114 | Check card_number and card_exp_date; retry request |
| 115 | Provide a valid card number |
| 116 | Contact MerchantE to have credits enabled |
| 117 | Contact MerchantE to add the card type |
| 118 | Contact MerchantE to add the currency type |
| 119 | Retry request with a valid retry ID |
| 120 | Retry request with a purchase ID |
| 121 | Retry request with a valid indicator |
| 122 | Do not submit any International transactions with the Offline transaction code |
| 123 | Resubmit with appropriate length, maximum is 96 |
| 127 | Correct the data in the line item detail record and resubmit |
| 128 | Provide a valid Merchant Tax ID |
| 129 | Provide a valid Customer Tax ID |
| 130 | Provide a valid Summary Commodity Code |
| 131 | Provide a valid destination postal code |
| 132 | Provide a valid source postal code |
| 133 | Provide a valid Destination Country Code |
| 134 | Provide a valid VAT Invoice Number |
| 135 | Provide a valid Alternate Tax Indicator |
| 136 | Remove the level III data OR use a valid transaction type |
| 137 | Provide a valid Discount Amount |
| 138 | Provide a valid Duty Amount |
| 139 | Provide a valid Order Date |
| 140 | Provide a valid VAT Amount |
| 141 | Provide a valid VAT Rate |
| 142 | Provide a valid Alternate Tax Amount |
| 143 | Provide a valid Line Item Count |
| 144 | Provide valid line item detail data for the card type |
| 168 | Industry Code submitted conflicts with transaction data submitted. Example: D for direct marketing with hotel check in and check out data |
| 201 | Correct the transaction ID, then resubmit |
| 202 | Confirm transaction_amount is greater than 0 and less than 9999999.99. Correct settlement amount to be less than or equal to the original authorization amount |
| 203 | Failed to void transaction, retry void, or issue credit |
| 204 | Void failed because transaction has already settled, submit credit |
| 205 | Void failed because transaction is already voided |
| 206 | A refund has already been performed on the transaction |
| 207 | Internal error Retry refund |
| 208 | Retry request |
| 209 | Correct tax amount and retry request |
| 210 | Correct AVS data and retry request |
| 211 | Correct CVV2 data retry request |
| 212 | Correct amount and retry request |
| 213 | Provide a valid transaction ID |
| 214 | Only use one of the following: card_number, card_swipe, card_id |
| 215 | Provide a valid card ID |
| 216 | Retry request |
| 217 | Retry request |
| 218 | Remove the card_id parameter and resubmit |
| 219 | Provide authorization code |
| 220 | Retry request |
| 221 | Provide a valid card ID |
| 222 | Provide a valid card ID |
| 223 | Retry request |
| 229 | Check country code and retry request |
| 230 | Internal Only |
| 231 | Provide the base amount |
| 232 | Contact MerchantE |
| 233 | Retry request with a currency code that matches the FX currency code |
| 234 | Verify track 2 data is sent with the request |
| 235 | Only a sale `(D)` is supported |
| 236 | PINNED Debit for USD transactions only |
| 237 | Please verify parameters are correct and re-submit |
| 238 | The same batch number was submitted within 5 calendar days of another closed batch. Verify batch is not a duplicate and re-submit with a new batch number |
| 239 | Validate the retry ID and resubmit if valid and within 48 hours of original request. If valid, then code 239 means that transaction does not exist. |
| 240 | System was unable to successfully locate data needed for a new authorization request (only when submitting re-auth or multi-settle requests) |
| 300 | Retry request |
| 301 | Retry request |
| 302 | Retry request |
| 303 | Retry request |
| 304 | Retry request |
| 350 | Check the response for details; response format is Refused — \[details\] |
| 352 | Retry request |
| 353 | Retry request |
| 354 | Retry request |
| 355 | Retry request |
| 356 | Retry request |
| 357 | Retry request |
| 400 | Retry request |
| 401 | Retry request |
| 412 | Retry request |
| 413 | FX not supported for re-auth of declines |
| 999 | Retry request |

## Address Verification Service Response Codes

| **Code** | **Response** | **Visa** | **MC** | **Amex** | **Description** |
| ------------------ | -------------------------------------------------- | ---- | --- | ---- | -------------------------------------------------- |
| 0 | Approved | **X** | **X** | **X** | Address verification not requested |
| A | Address Match | **X** | **X** | **X** | Street address match only. |
| N | No Match | **X** | **X** | **X** | No address or ZIP/postal code match |
| R | Retry | **X** | **X** | **X** | AVS indeterminate outcome, retry. |
| S | AVS not supported | | **X** | **X** | Address unavailable. Issuer does not participate. Merchant ID not allowed AAV function. (AMEX) |
| U | Verification X Unavailable | **X** | **X** | **X** | AVS is unable to verify. |
| W | Domestic and International 9-digit zip match | | **X** | | Postal code match only. |
| X | Domestic and International 9-digit zip and address match | | **X** | | Exact match. Street address and postal code match. |
| Y | Exact Match | **X** | **X** | **X** | Exact match. Street address and postal code match. |
| Z | Zip Match | **X** | **X** | **X** | Postal code match only. |

## CVV Response Codes

| **Code** | **Description** |
| ---- | ------------------------------------------------------------ |
| M | CVV2/CVC2 /Discover CID — Match |
| N | CVV2/CVC2/Discover CID - No Match |
| P | Not Performed |
| S | *For Mastercard and Discover, merchant has indicated that Verification Code is not present on card.* For Visa, System Error (Retry) |
| U | *For MasterCard and Discover, issuer is not certified and / or has not provided encryption keys.* For Visa, Unable to Verify |

## Response Control Product Level

These are response for `product_level`.

| **Field** | **Visa Description** | **Mastercard Description** |
| ----------------------------------- | -------------------------------------------------- | ------------------------------------------------------------------------------ |
| A | Visa Classic/Traditional | Mastercard Consumer |
| B | Visa Traditional Rewards | --- |
| C | Visa Signature | MC World/Elite, MC World Prepaid |
| D | Visa Signature Preferred | --- |
| E | Reserved | --- |
| F | Reserved | --- |
| G | Visa Business | MC Business/Corporate |
| G1 | Visa Signature Business | MC World/Elite Business |
| G2 | Reserved | MC Business Debit |
| G3 | Visa Business Enhanced (U.S.Only) | |
| H | Reserved | Mastercard Debit |
| I | Visa Infinite | --- |
| J | Reserved | --- |
| J1 | Reserved | Mastercard General Prepaid |
| J2 | Reserved | Mastercard Prepaid Gift Card |
| J3 | Visa Healthcare | Mastercard Prepaid Benefits |
| J4 | Reserved | Mastercard Prepaid Commercial |
| K | Visa Corporate | --- |
| K1 | Visa GSA Corporate T&E | |
| L | Electron | --- |
| M | MC/EuroCard and Diners (when unable to define) | MC/EuroCard and Diners (when unable to define) |
| N | Reserved | --- |
| N1 | Visa Rewards | |
| O | Reserved | --- |
| P | Reserved | --- |
| Q | Private Label | Private Label |
| R | Proprietary | --- |
| S | Visa Purchasing | Mastercard Purchasing |
| S1 | Visa Purchasing with Fleet | MC Commercial with Fleet |
| S2 | Visa GSA Purchasing | |
| S3 | Visa GSA Purchasing with Fleet | |
| S4 | Government Services Loan | |
| S5 | Commercial Transport EBT | |
| S6 | Business Loan | |
| S7 | Visa Distribution | |
| T | Reserved/Interlink | --- |
| U | Visa TravelMoney | --- |
| V | V-Pay | --- |
| X | Visa B2B Virtual Payments | |
| **Additional Card Types** | | |
| AX | American Express | American Express |
| DI | Discover (when code is not returned) | Discover (when code is not returned) |
| DN | Diners | |
| JC | JCB | |
| MC | Mastercard (when code is not returned) | Mastercard (when code is not returned) |

## Response Control Commercial Card

These are `commercial_card` responses.

| **Field** | **Values**                                  |
| ----------------- | ------------------------------------------------------------------ |
| B          | Business Card                           |
| D          | Visa Commerce (reserved for future use) |
| R          | Corporate Card                          |
| S          | Purchasing Card                         |
| 0          | Non-commercial Card                     |
| \<SPACE\>  | Invalid Request Indicator Received      |

## Response Control rctl_Extended_AVS

These are Extended Address Verification Service (AVS) response codes within a Risk Control (rctl).

| **Code** | **Response**              |
| ------------------ | -------------------------------------------------- |
| Y           | Match                 |
| N           | No Match              |
| ~           | Data not sent         |
| U           | Data Unavailable      |
| R           | Retry                 |
| S           | Service Not Available |

## rctl_Extended_AVS Position Identifier

These are position identifiers for Extended Address Verification Service (AVS) response codes within a Risk Control (rctl).

| **Position**   | **Position Identifier**                |
| ----------------- | --------------------------------------------------------- |
| Position 1 | Billing Postal Code Result         |
| Position 2 | Billing Address Result             |
| Position 3 | Billing first and last name result |
| Position 4 | Billing phone results              |
| Position 5 | Billing email result               |

## Authorization Switch Error Codes

If you receive repeated switch error code responses, contact the
MerchantE certification team.

#### Authorization Switch Error Codes Descriptions

| **Code** | **Response** | **Cause** | **Resolution** |
| ---------------------- | --------------------------- | -------------------------------------------------- | -------------------------------------------------- |
| 0TA | CT NOT ALLOWED | Account does not accept card type | Confirm card acceptance information in back office system. If a Debt Repayment transaction, card type must be a Debit or Prepaid card |
| 0TB | REENTER EXP DATE | Invalid expiration date | Check expiration date and retry request |
| 0TC | RETRY BAD ADDR | Invalid address or ZIP data | Check AVS data and retry request |
| 0TD | REV NOT ALLOWED | Card type doesn't allow reversals | Authorization cannot be reversed |
| 0TE | BAD MERCHANT KEY | Merchant key not supplied or incorrect | Check profile_key and retry request |
| 003 | Merch ID Error | Merchant ID unknown or disabled | Check profile_id and retry request |
| 012 | Invalid Trans | Unsupported transaction type | Check transaction_type and retry request |
| 013 | Amount Error | Transaction amount is 0 or too long | Check transaction_amount and retry request |
| 077 | No Action Taken | Reversal amounts larger than original amount | Transaction may not qualify for best level of Interchange |
| 014 | Card No. Error | Card number has unknown BIN or fails check digit edit | Re-try card number again, verify with merchant it is a valid card with proper logos for card types the merchants accept |
| 019 | RE Enter | Communication error with Visa or Mastercard switches | Retry request |
| 097 | System Error | System malfunction | Contact the MerchantE certification team |
| 006 | Error 16 | Invalid Moto Indicator | If a Debt Repayment transaction, must be a CNP (Card Not Present) transaction; cannot be keyed/swiped by/at merchant |
| 006 | Error 25 | Message format error | Check request format and retry request |
| 006 | Error NN | Transaction error | Record the NN error code and contact the MerchantE certification team |
| 057 | Error 57 | Unsupported international transaction | System supports only card not present authorizations; confirm missing fields and retry request |
| 059 | Error 59 | Account does not accept international | Contact the MerchantE certification team |

## International Error Codes

BlueSnap error codes will be displayed in the error_code and
auth_response_text fields.

#### BlueSnap Error Codes

| **Code** | **Description** |
| ----------------- | ------------------------------------------------------------ |
| 2001 | SHOPPER HAS NO RELEVANT SAVED PAYMENT METHOD. |
| 2001 | SHOPPER CREDIT CARD CANNOT CONTAIN BOTH PLAIN AND ENCRYPTED DETAILS. |
| 2001 | ENCRYPTED FIELD ENCRYPTED CARD NUMBER IS REQUIRED. |
| 2001 | CC TYPE IS NOT SUPPORTED BY THE MERCHANT |
| 2002 | PAYMENT PROCESSING FAILURE DUE TO AN UNSPECIFIED ERROR. DESCRIPTION WILL CONTAIN THE ERROR CODE AND MESSAGE SENT BY THE PROCESSOR IF APPLICABLE. |
| 2002 | THE PAYMENT DETAILS PROVIDED ARE INVALID. |
| 2002 | THE SHOPPER ID PASSED IN THE REQUEST WAS NOT FOUND. |
| 2002 | ONE OF THE SHOPPERS COUNTRIES WAS DEFINED AS OFAC SANCTIONED. |
| 2002 | SHOPPER HAS MULTIPLE PAYMENT METHODS, BUT NONE IS SELECTED. |
| 2002 | INVALID TRANSACTION TYPE. |
| 2002 | THE GIVEN CURRENCY MUST MATCH THE ORIGINAL SUBSCRIPTION CURRENCY. |
| 2002 | CURRENCY CODE IS MISSING. |
| 2002 | MERCHANT-TRANSACTION-ID SHOULD BE UP TO 50 CHARACTERS. |
| 2002 | THE SHOPPER ID PASSED IN THE REQUEST WAS NOT FOUND. |
| 2002 | ONE OF THE SHOPPERS COUNTRIES WAS DEFINED AS OFAC SANCTIONED. |
| 2002 | TRANSACTION ID IS INVALID. |
| 2002 | THE GIVEN CURRENCY MUST MATCH THE ORIGINAL TAX QUOTE CURRENCY. |
| 2002 | THE GIVEN TOTAL PAYMENT MUST MATCH THE ORIGINAL TOTAL CALCULATED IN THE GIVEN TAX QUOTE REFERENCE. |
| 2002 | THE GIVEN TAX REFERENCE WAS ALREADY IN USE. |
| 2002 | THE SHOPPER'S CREDIT CARD WAS NOT FOUND. |
| 2003 | THE RESOURCE PASSED IN THE REQUEST HAS VIOLATED VALIDATION RULES. ADDITIONAL INFORMATION ABOUT THE SPECIFIC ISSUE IS PROVIDED IN THE ERROR DESCRIPTION. |
| 2004 | PRICE IS A NEGATIVE FIGURE. |
| 2004 | THERE ARE NO AVAILABLE PROCESSORS FOR THE SPECIFIC REQUEST. |
| 2004 | A GENERAL PAYMENT FAILURE HAS OCCURRED. |
| 2004 | PAYMENT PROCESSING FAILURE DUE TO ISSUING BANK DECLINE. |
| 2004 | PAYMENT PROCESSING FAILURE DUE TO AN UNSPECIFIED ERROR. DESCRIPTION WILL CONTAIN THE ERROR CODE AND MESSAGE SENT BY THE PROCESSOR, IF APPLICABLE. |
| 2004 | PAYMENT PROCESSING FAILURE DUE TO TIMEOUT. |
| 2004 | PAYMENT PROCESSING FAILURE BECAUSE THE ISSUER IS UNAVAILABLE OR OFFLINE. |
| 2004 | THERE ARE NO AVAILABLE PROCESSORS FOR THE SPECIFIC REQUEST. |
| 2005 | This fraud error message includes a fraudEvents object with the following information - an ID for the fraud event- an indication of what fraud rule or configurable threshold was triggered and why an indication of whether the transaction was declined or held for review. |
| 2005 | TAX-FREE REFUND AMOUNT CANNOT BE MORE THAN THE ALLOWED REFUNDABLE AMOUNT. |
| 2006 | TRANSACTION RETRIEVAL SERVICE FAILURE. MULTIPLE TRANSACTIONS FOUND WITH MERCHANT TRANSACTION ID: \${MERCHANTTRANSACTIONID}. |
| 2006 | TRANSACTION {TRANSACTIONTYPE} FAILED. TRANSACTION LOCKED. |
| 2006 | PAYMENT PROCESSING FAILURE DUE TO INVALID AMOUNT FOR THIS TRANSACTION. |
| 2006 | PAYMENT PROCESSING FAILURE DUE TO CVV ERROR. |
| 2006 | PAYMENT PROCESSING FAILURE DUE TO INSUFFICIENT FUNDS. |
| 2006 | PAYMENT PROCESSING FAILURE DUE TO INVALID AMOUNT FOR THIS TRANSACTION. |
| 2006 | PAYMENT PROCESSING FAILURE BECAUSE CARD LIMIT WAS EXCEEDED. |
| 2006 | PAYMENT PROCESSING FAILURE DUE TO INVALID AMOUNT FOR THIS TRANSACTION. |
| 2006 | PAYMENT PROCESSING FAILURE DUE TO AN UNSPECIFIED ERROR RETURNED. |
| 2006 | THE TRANSACTION FAILED BECAUSE THERE IS NO STRONG CUSTOMER AUTHENTICATION. |
| 2006 | TRANSACTION IS NOT AUTHORIZED. |
| 2006 | TRANSACTION AUTH_REVERSAL FAILED. TRANSACTION ALREADY EXISTS. |
| 2006 | TRANSACTION WAS ALREADY CAPTURED. |
| 2007 | A GENERAL REFUND FAILURE HAS OCCURRED. RETRY THE TRANSACTION OR TRANSACTION IS NOT VALID FOR REFUND AS TRANSACTION HAS NOT BEEN CLEARED YET. PLEASE WAIT 3-7 DAYS AND TRY AGAIN. |
| 2007 | THE REFUND AMOUNT PASSED IN THE REQUEST IS SMALLER THAN THE MINIMUM AMOUNT ALLOWED. CORRECT THE AMOUNT AND RESUBMIT. |
| 2007 | THE REFUND AMOUNT PASSED IN THE REQUEST EXCEEDS THE MAXIMUM AMOUNT ALLOWED. CORRECT THE AMOUNT AND RESUBMIT. |
| 2007 | REFUND FAILED BECAUSE THE ALLOWED REFUND PERIOD HAS ENDED. |
| 2007 | THERE ARE INSUFFICIENT FUNDS TO PERFORM THE REQUESTED REFUND. |
| 2007 | REFUND FAILED BECAUSE THE PAYMENT WAS ALREADY REFUNDED. |
| 2007 | INVOICE HAS ALREADY BEEN FULLY REFUNDED. |
| 2007 | ANOTHER REFUND REQUEST FOR THIS INVOICE IS CURRENTLY IN PROCESS. PLEASE WAIT FOR IT TO FINISH BEFORE SUBMITTING NEW REQUEST. |
| 2007 | INVOICE CANNOT BE PARTIALLY REFUNDED. |
| 2007 | PLEASE CONTACT MERCHANT SUPPORT TO ISSUE THIS REFUND. |
| 2007 | THERE ARE NOT ENOUGH FUNDS IN VENDOR ACCOUNT TO ISSUE THIS REFUND. CONTACT MERCHANT SUPPORT. |
| 2007 | A TAX AMOUNT IS REQUIRED TO COMPLETE THIS TRANSACTION, EVEN IF THE AMOUNT IS ZERO. |
| 2007 | TAX REFUND FAILED BECAUSE THE AMOUNT MUST BE ZERO OR GREATER. |
| 2007 | TAX REFUND AMOUNT CANNOT BE MORE THAN THE ALLOWED REFUNDABLE AMOUNT. |
| 2007 | TAX REFUND AMOUNT EXCEEDS TOTAL REFUND AMOUNT. |
| 2007 | TAX-FREE REFUND AMOUNT CANNOT BE MORE THAN THE ALLOWED REFUNDABLE AMOUNT. |
| 2007 | A GENERAL PAYMENT FAILURE HAS OCCURRED FOR THIS REFUND. |
| 2008 | MERCHANT CONFIGURATION ERROR. |
| 2008 | PAYMENT PROCESSING FAILURE DUE TO INCORRECT SETUP. |
| 2008 | USER IS NOT AUTHORIZED TO RETRIEVE TAX REFERENCE. |
| 2009 | USER INPUT SUSPECTED AS MALICIOUS. |
| 2009 | USER INPUT SUSPECTED AS MALICIOUS. |
| 2010 | 3D SECURITY AUTHENTICATION IS REQUIRED. |
| 2010 | TOKEN IS EXPIRED. |
| 2010 | COULD NOT FIND THE TOKEN. |
| 2010 | TOKEN IS NOT ASSOCIATED WITH A PAYMENT METHOD, PLEASE VERIFY YOUR CLIENT INTEGRATION OR CONTACT BLUESNAP SUPPORT. |
| 2010 | MISSING REQUIRED FIELDS. |
| 2011 | PAYMENT PROCESSING FAILURE BECAUSE THE AUTHORIZATION HAS ALREADY BEEN REVERSED. |
| 2011 | PAYMENT PROCESSING FAILURE BECAUSE THE AUTHORIZATION HAS EXPIRED. |
| 2011 | PAYMENT PROCESSING FAILURE BECAUSE THE AUTHORIZATION WAS NOT FOUND. |
| 2011 | YOUR PAYMENT REQUEST COULD NOT BE SUCCESSFULLY COMPLETED. |
| 2011 | PAYMENT PROCESSING FAILURE DUE TO AN UNSPECIFIED ERROR. PLEASE CONTACT THE ISSUING BANK. |
| 2011 | THE CARD IS LOST OR STOLEN. |
| 2011 | PAYMENT PROCESSING FAILURE DUE TO HIGH RISK. |
| 2011 | PAYMENT PROCESSING FAILURE DUE TO INCORRECT INFORMATION. DESCRIPTION WILL CONTAIN ADDITIONAL INFORMATION ABOUT THE SPECIFIC ERROR. |
| 2011 | PAYMENT PROCESSING FAILURE BECAUSE THIS TRANSACTION IS NOT ALLOWED BY THE ISSUER. |
| 2011 | PAYMENT PROCESSING FAILURE. THE CARD HAS BEEN REPORTED LOST OR STOLEN AND SHOULD BE REMOVED FROM USE. |
| 2011 | PAYMENT PROCESSING FAILURE DUE TO DUPLICATION. THE TRANSACTION IS A DUPLICATE OF A PREVIOUSLY SUBMITTED TRANSACTION. |
| 2011 | PAYMENT PROCESSING FAILURE DUE TO RESTRICTED CARD. |
| 2011 | PAYMENT PROCESSING FAILURE DUE TO PROBLEMATIC TRANSACTION. |
| 2011 | PAYMENT PROCESSING FAILURE DUE TO 3D SECURE FAILURE. |
| 2011 | PAYMENT PROCESSING FAILURE BECAUSE THE ATTEMPTED OPERATION IS NOT SUPPORTED FOR THIS PAYMENT METHOD. |
| 2011 | TRANSACTION \${TRANSACTIONTYPE} FAILED. TRANSACTION LOCKED. |
| 2011 | TRANSACTION HAS EXPIRED. |
| 2012 | Unable to Process Request. |

# Request and Response Samples

This section gives a wide variety of request and response examples.

## Apple Pay Sample

This section provides a sample of an Apple Pay request and response
message.

### Request

```http
profile_id=xxx&profile_key=yyy&transaction_type=D
&card_number=4189080409331656&card_exp_date=1230
&transaction_amount=2439&moto_ecommerce_ind=5
&cavv=BwABBJaSmIZUMzS?VMpKYAAAAAAA%3D
&requesterapp_name=APL001
&tac=TWsyTEhIakRyQWR?Nakd4SkhoeHI%3D
```

### Response

```text
transaction_id=528d174ea7ad31078e53c3827dcf26ca
&error_code=000
&auth_response_text=Approval T5421H
&avs_result=0
&auth_code=T5421H
```

## Google Pay Sample

This section provides a sample of a Google Pay request and response
message.

### Request

```text
transaction_amount=500.00
&profile_key=EtLwsVwzcecgpHqkrNjhXPVyvUnEYHdn
&card_number=489533XXXXXX1119&transaction_type=D
&card_exp_date=1228&cavv=AgAAAAAABk4DWZ4C28yUQAAAAAA%3D
&requesterapp_name=ggl001&moto_ecommerce_ind=07
&profile_id=xxx
```

### Response

```text
transaction_id=42850f21a16a3449ab3f7b96d16f8974
&error_code=000
&auth_response_text=Approval T4002H
&avs_result=0
&auth_code=T4002H
```

## Sale Sample

This section provides a sample of a sale request and response message.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx&profile_key=yyy&transaction_type=D
&cardholder_street_address=123+Main&cardholder_zip=55555
&transaction_amount=1.00&card_number=4012301230123010
&card_exp_date=1222&invoice_number=123456
```

### Response

```text
transaction_id=8fc94e10b12130f4?ab8a8ceeac40fc69
&error_code=000
&auth_response_text=Exact Match
&avs_result=Y
&cvv2_result=M
&auth_code=T2067H
```

## Pre-Authorization Sample

This section provides a sample of a pre-authorization request and
response message.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx&profile_key=yyy&transaction_type=P
&cardholder_street_address=123+Main&cardholder_zip=55555
&transaction_amount=1.00&card_number=4012301230123010
&card_exp_date=1222&invoice_number=123456
```

### Response

```text
transaction_id=6117bf65fc4f38b7bc0ca4cd46eed6d1
&error_code=000
&auth_response_text=Exact Match
&avs_result=Y
&auth_code=T2067H
```

## Capture Sample

This section provides a sample of a capture request and response
message.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx&profile_key=yyy&transaction_type=S
&transaction_amount=1.00
&transaction_id=6117bf65fc4f38b7bc0ca4cd46eed6d1
&invoice_number=123456
```

### Response

```text
transaction_id=12f3befbd035371b9b82d5920465e071
&error_code=000
&auth_response_text=Settle Request Accepted
```

## Multi-Capture

This section provides a sample of a multi-capture request and response
message.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx&profile_key=yyy&transaction_type=S
&transaction_amount=.25
&transaction_id=a1c142982baa353a976252f7ccf3b18b
&invoice_number=123456
```

### Response

```text
transaction_id=a1c142982baa353a976252f7ccf3b18b
&error_code=000
&auth_response_text=Settle Request Accepted
```

## Re-Authorization Sample

This section provides a sample of a re-authorization request and
response message.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx&profile_key=yyy&transaction_type=J
&transaction_id=72613b899e3937cfa33efa1711973c7d
```

### Response

```text
transaction_id=e48e1bb2823942a6aa6b9ab8af20fe48
&error_code=000
&auth_response_text=Zip Match
&avs_result=Z
&auth_code=T00001
```

## Refund Resulting in a Credit Sample

This section provides an example of a request and response message for a
refund resulting in a credit.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 234

profile_id=xxx
&profile_key=xxx
&transaction_type=U
&card_number=537143xxxxxx8489
&card_exp_date=1225
&transaction_amount=10
&transaction_id=b97f051d46c63ae49d4131bd213ec7ee
```

### Response

```text
transaction_id=253018575ff03aba98b9cffd618e1b65
&error_code=000
&auth_response_text=Refund Request Accepted - Credit \$10.00
&avs_result=0
&auth_code=236680
```

AMEX cards can expect a generic response for credits/refunds as they are
not authorized.

## Refund Resulting in a Void Sample

This section provides an example of a message for a refund resulting in
a void.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx&profile_key=yyy&transaction_type=U
&transaction_id=e4f49b483d833695a39b2787c65bbef1
```

### Response

```text
transaction_id=7c0ab9ec96da370886a116fa7942447a
&error_code=000
&auth_response_text=Refund Request Accepted - Void
```

## Partial Refund Resulting in an Adjustment Sample

This section provides an example of a message for a partial refund
resulting in an adjustment.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx&profile_key=yyy&transaction_type=U
&transaction_amount=0.60
&transaction_id=9f57b14932943a198d51b6a5074fd916
```

### Response

```text
transaction_id=38356d5c6625379a9b3e55920f5f9e8a
&error_code=000
&auth_response_text=Refund Request Accepted - Adjustment \$0.40
```

## Partial Refund Resulting in a Credit Sample

This section provides an example of a message for a partial refund
resulting in a credit.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 233

profile_id=xxx
&profile_key=xxx
&transaction_type=U
&card_number=537143xxxxxx8489
&card_exp_date=1225
&transaction_amount=5
&transaction_id=8e0eb2df8b9735448d0c40dcc7099cdd
```

### Response

```text
transaction_id=9dedb6066dac3d0db45d07d80e6feab2
&error_code=000
&auth_response_text=Refund Request Accepted - Credit \$5.00
&avs_result=0
&auth_code=750670
```

AMEX cards can expect a generic response for credits/refunds as they are
not authorized.

## Credit Sample

This section provides an example of a message for a credit.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 186

profile_id=xxx
&profile_key=xxx
&transaction_type=C
&card_number=537143xxxxxx8489
&card_exp_date=1225
&transaction_amount=10
```

### Response

```text
transaction_id=be7c5c8a61d73c31b7bbca308e508b14
&error_code=000
&auth_response_text=Credit Approved
&avs_result=0
&auth_code=689420
```

AMEX cards can expect a generic response for credits/refunds as they are
not authorized.

## Void Sample

This section provides an example of a message for a void.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx&profile_key=yyy&transaction_type=V
&transaction_id=bd5661892d69399f8879cedb427879e7
```

### Response

```text
transaction_id=d3b221224235331aba05101ae3d35340
&error_code=000
&auth_response_text=Void Request Accepted
```

## Creating a Temporary Token Sample

This section provides an example of a message for creating a temporary
token.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx&profile_key=yyy&transaction_type=T&card_number=4012301230123010
```

### Response

```text
transaction_id=446650ab2a8b3df68390d170cbc0fa0a
&error_code=000
&auth_response_text=Card Data Stored
```

## Creating a Permanent Token Sample

This section provides an example of a message for creating a permanent
token.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx&profile_key=xxx&transaction_type=D
&card_id=km4l6keh90me92mb819c1ca6b28si45f
&card_exp_date=1222&transaction_amount=1.00
&cardholder_street_address=123+N.+Main
&cardholder_zip=55555&invoice_number=123456
&store_card=y
```

### Response

```text
transaction_id=443733d0d7f236728d6ae1cbd7950403
&error_code=000&auth_response_text=ExactMatch
&avs_result=Y&cvv2_result=M&auth_code=T6416H
&card_id=5c51fb49de854a1b819c1ca6b2d55209
```

## Sale Using Token Sample

This section provides an example of a message for a sale using a token.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_key=xxx&profile_id=94100006000500000010&card_number=484718xxxxxx6649&
card_exp_date=06/27& transaction_amount=1.00&transaction_type=D&
account_data_source=@&cardholder_street_address=2201&ccv2=123&
store_card=Y
```

### Response

```text
transaction_id=1e5ded8772bb3e98abba30923a54ddec
&error_code=000
&auth_response_text=No Match
&avs_result=N
&auth_code=T4602H
&card_id=3c9c9624f7bd442a9cbb6f5acd367501
```

## Delete Token Sample

This section provides an example of a message for deleting a token.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx&profile_key=yyy&transaction_type=X
&card_id=446650ab2a8b3df68390d170cbc0fa0a
```

### Response

```text
transaction_id=51e8ea7ef94e3813a00b2a2331cd2679
&error_code=000
&auth_response_text=Stored Card Data Deleted
```

## Batch Close Sample

This section provides an example of a message for closing a batch.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx&profile_key=yyy&transaction_type=
```

### Response

```text
transaction_id=932f3671fa9f34bc8e1517ebb427b269
&error_code=000
&auth_response_text=Batch 328 Closed - USD - Sales: 150 for \$8,039.37
Credits: 83 for \$83.00 Net: \$7956.37
```

## Verification Only Sample

This section provides an example of a verification only message.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx
&profile_key=yyy
&transaction_type=A
&cardholder_street_address=123+Main
&cardholder_zip=55555
&transaction_amount=0.00
&card_number=4012301230123010
&card_exp_date=1222
```

### Response

```text
transaction_id=6117bf65fc4f38b7bc0ca4cd46eed6d1
&error_code=000
&auth_response_text=Exact Match
&avs_result=Y
&auth_code=T2067H
```

## Inquiry Sample

The section provides an example of an inquiry transaction.

### Submitting a Sale Request with a retry_id

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx&profile_key=yyy&transaction_type=D
&cardholder_street_address=123+Main&cardholder_zip=55555
&transaction_amount=1.00&card_number=4012301230123010
&card_exp_date=1222&invoice_number=123456&retry_id=37964
```

### Response

```text
transaction_id=ad988797986d3f02b0e08a6c9f8c5811
&error_code=000
&auth_response_text=Exact Match
&avs_result=Y
&auth_code=T9659H
&retry_count=2
```

### Submitting an Inquiry Request

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx&profile_key=yyy&transaction_type=I&retry_id=123456
```

### Response

```text
transaction_id=51e8ea7ef94e3813a00b2a2331cd2679
&error_code=000
&auth_response_text=Exact Match
&avs_result=Y
&cvv2_result=M
&auth_code=T7362H
&retry_count=2
```

## Partial Authorization Sample

This section provides an example of a partial authorization transaction.
This authorizes part of the transaction amount.

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx
&profile_key=yyy
&transaction_type=D
&card_number=4012xxxxxxxx8888
&card_exp_date=1222
&transaction_amount=1.00
&cvv2=123
&cardholder_street_address=123+N.+Main
&cardholder_zip=55555
&invoice_number=123456
&rctl_partial_auth=true
```

### Response

```text
transaction_id=d96cdfb30aab3e8d9944f9ab4fd5b646
&error_code=010
&auth_response_text=Partial Approval
&avs_result=Y
&cvv2_result=M
&auth_code=T2055H
&partial_auth=0.33
```

## Echo Field Sample

### Request

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 578

profile_id=xxx&profile_key=yyy&transaction_type=D
&cardholder_street_address=123+Main&cardholder_zip=55555
&transaction_amount=1.00&card_number=4012301230123010
&card_exp_date=1222&invoice_number=123456&echo_productline=metals
```

### Response

```text
transaction_id=1b5fd0095ac63700aae5d95ec84364f0
&error_code=000
&auth_response_text=Exact Match
&avs_result=Y
&auth_code=T4012H
&eresp_productline=metals
```

## Surcharging Sample

### Request

```text
card_number=537143xxxxxx8489
&transaction_amount=36.05
&tran_fee_amount=1.05
&transaction_type=D
&card_exp_date=0224
&profile_key=xxx
&profile_id=xxx
```

### Response

```text
transaction_id=7f84a42eeb393eafb76b327ec62eb693
&error_code=000
&auth_response_text=Approval T40792
&avs_result=0
&auth_code=T40792
```

## BIN Lookup for Surcharge Sample

### Request

```text
profile_id=94100092289900000001
&profile_key=khKNVGWcEHKwdwMcLBOFQKohaButvsbs
&transaction_type=CT
&bin_number=400129374
```

### Response

```text
transaction_id=5e5852570045439dbca792f67c483dc8
&error_code=000
&auth_response_text=P
```

## BIN Lookup for Surcharge Error Sample

### Request

```text
profile_id=94100092289900000001
&profile_key=khKNVGWcEHKwdwMcLBOFQKohaButvsbs
&transaction_type=CT
&bin_number=4001
```

### Response

```text
transaction_id=02bf88e004714aeebd8d7fd2d755308d
&error_code=363
&auth_response_text=Invalid bin number
```

**Note:** `bin_number` must be numeric and between 6 and 10 digits. This
example fails because `4001` is only 4 digits.

## Initial Token Sample

### Request

```text
profile_key=xxx
&profile_id=94100006000500000010
&card_number=484718xxxxxx6649
&card_exp_date=06/27
&transaction_amount=1.00
&transaction_type=D
&account_data_source=@
&cardholder_street_address=2201
&ccv2=123
&store_card=Y
```

### Response

```text
transaction_id=1e5ded8772bb3e98abba30923a54ddec
&error_code=000
&auth_response_text=No Match
&avs_result=N
&auth_code=T4602H
&card_id=3c9c9624f7bd442a9cbb6f5acd367501
```

# Testing

To request certification, contact [MerchantE](https://www.merchante.com/contact).

**Best Practice:** It is important to use the test profile for testing to
prevent billing for authorizations obtained during testing. ME will bill
for all authorizations obtained using a production profile even if the
transactions are part of the testing cycle. The MerchantE certification
servers are available 24 x 7 for unattended testing. The certification
servers replicate the Payment Gateway production servers and support
testing for all available functions. Certification transactions are
processed as tests, so there is no need to worry about settlement to a
customer account.

Certification Host:
[https://cert.merchante-solutions.com/mes-api/tridentApi](https://cert.merchante-solutions.com/mes-api/tridentApi)

## Test Card Numbers Table

The following table lists test card numbers that can be used during
testing.

| **Card Type**        | **Length** | **Card Number**         |
| --------------------------- | ------------ | -------------------------------- |
| Visa             | 16     | 4012301230123010    |
| Visa             | 13     | 4012301230158       |
| Mastercard       | 16     | 5123012301230120    |
| Mastercard       | 16     | 2223000010090980    |
| American Express | 15     | 349999999999991     |
| Discover         | 19     | 6011011231231234005 |
| JCB              | 16     | 3528288605211810    |
| Visa             | 19     | 4012888812348882008 |
| Mastercard       | 19     | 5123012301230120001 |

## Business-to-Business Card Numbers Table

To receive these responses, send rctl_commercial_card = y with your
request.

**Card Numbers**

| **Card Type**  | **Card Number**      | **Commercial Card Type** |
| ----------------- | --------------------------- | ---------------------------------- |
| Visa       | 4000700705251681 | B - Business Card    |
| Mastercard | 5589548939080095 | B - Business Card    |
| Mastercard | 5133598939080091 | S - Purchasing Card  |
| Visa       | 4000693061211474 | R - Corporate Card   |
| Mastercard | 5119718939080093 | R - Corporate Card   |

## Address Verification Testing Table

This table lists the AVS result responses.

| **Street Address** | **Zip Code** | **avs_result Response** |
| ----------------------- | --------- | ---------------------------------------------------------------------------------------------------- |
| 123 | 55555 | Y — Exact match. Street address and postal code match. |
| 123 | 999991111 | Y — Exact match. Street address and postal code match. X — street and postal code match (Mastercard) |
| 123 | EH8 9ST | Y — Exact match. Street address and postal code match. |
| 123 | 11101 | A - Street address match only. |
| 234 | 11101 | U - AVS unable to verify. |
| 345 | 11101 | U - AVS unable to verify. |
| 456 | 11101 | AVS indeterminate outcome, retry. |
| 235 | 11101 | U - AVS unable to verify. |
| 177 | 55555 | Z - Postal code match only. |
| 177 | EH8 9ST | Z - Postal code match only. |
| 177 | 999991111 | Z - Postal code match only. (Visa) W - Postal code match only. (Mastercard) |
| 177 | 11101 | N - No street address or postal code match. |

## American Express Automated Address Verification (AAV) Fields Table

These are the AAV Fields.

| **Field** | **Value** | **avs_result Response** |
| ---------------------------------------------------------------- | ------------------------------------ | -------------------------------------------------- |
| cardholder_street_address | 123 | Y = Match |
| cardholder_zip | 55555 or 999999999 | Y = Match |
| cardholder_first_name | CHRIS | Y = Match |
| cardholder_last_name | FROST | Y = Match |
| cardholder_phone | 7035551212 | Y = Match |
| cardholder_email This is an Internet Telephone Data (ITD) field. | CFFROST@EMAILADDRESS.COM | Y = Match |

### American Express Zero Value Account Verification Messaging (ZVAV) Table Fields

The American Express Zero Value Account Verification Messaging (ZVAV)
functionality allows for the validation of a card's status.

Other action codes may be used to indicate issues with the transaction
that are not necessarily specific to ZVAV functionality. Prepaid Card
balance returns are not supported when using ZVAV.

| **Field** | **Action Code** | **Response** |
| ----------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------ |
| 000 | Approved | Indicates a card in good standing |
| 100 | Deny | Indicates an invalid card or account |
| 110 | Invalid Amount | An amount has been populated other than \$0 |
| 111 | Invalid Account | Indicates an invalid account |
| 115 | Requested function not supported | For example, Visa, MC, JCB, etc. |
| 912 | Issuer not available | Indicates that the issuer was unable to verify |

## CVV2, CVC, CID Testing Tables

Values for Visa, Mastercard, and Discover.

| **cvv2 Test Value** | **cvv2_result Response** |
| ------------------------- | -------------------------------------------------------------------------------------- |
| 123 | M - Match |
| 234 | P - Not Processed |
| 345 | G - Verification unavailable due to international issuer non-participation (Visa only) |
| 789 | N - No Match |

Values for American Express.

| **cvv2 Test Value** | **cvv2_result Response** |
| ------------------------- | ---------------------------------- |
| 1234            | M - Match            |
| 2345            | P - Not Processed    |
| 5678            | N - No Match         |

## Amount-Driven Response Testing Table

| **Test transaction_type Value** | **Amount Test Value** | **error_code Response** | **auth_response_text Response** | **Visa Description** |
| --------------------------------------------- | ---------------------------- | -------------------------------- | -------------------------------------------------- | -------------------------------------------------- |
| A | 0.00 | 085 | Card OK or Approval | |
| P or D | 0.01 | 001 | Call | Refer to the card issuer. |
| P or D | 0.02 | 002 | Call | Refer to card issuer, special condition |
| P or D | 0.04 | 004 | Hold-call | Pick up card (no fraud) |
| P or D | 0.05 | 005 | Decline | Do not honor |
| P or D | 0.07 | 007 | Hold-call | Pick up card, special condition (fraud account) |
| P or D | 0.14 | 014 | Card No. Error | Invalid account number (no such number) |
| P or D | 0.15 | 015 | No Such Issuer | No such issuer |
| P or D | 0.19 | 019 | RE Enter | Re-enter transaction |
| P or D | 0.41 | 041 | Hold-call | Lost card, pick up (fraud account) |
| P or D | 0.43 | 043 | Hold-call | Stolen card, pick up (fraud account) |
| P or D | 0.51 | 051 | Decline | Not sufficient funds |
| P or D | 0.54 | 054 | Expired Card | Expired card or expiration date is missing |
| P or D | 0.57 | 057 | Serv Not Allowed | Transaction not permitted to cardholder |
| P or D | 0.59 | 059 | Suspected Fraud | Suspected Fraud |
| P or D | 0.84 | 084 | Invalid Auth | (Mastercard) Invalid authorization life cycle |
| P or D | 0.86 | 086 | Cannot Verify PIN | Cannot verify PIN; for example, no PVV |
| P or D | 1.10 | 0TA | CT NOT ALLOWED | (MerchantE) Merchant does not accept this type of card. |

## Encrypted PIN Debit Testing Table

This feature is only available to merchants using PIN pads with
MerchantE encryption keys. Enter 1234 as the PIN for Approval. Any other
PIN is declined.

| **Encrypted PIN Block** | **KSN**              | **Response**              |
| -------------------------------- | --------------------------- | ----------------------------------- |
| 706E3CADE53F11CA    | 3941013211204321 | 00 — Approval         |
| 43197165510DDD89    | 3941013211204322 | 55 - Wrong PIN        |
| CCCCCCCCCCCCCCCC    | 3941013211204323 | 81 - Encryption Error |

## Partial Authorization and Balance Inquiry

To test partial authorization, submit rctl_partial_auth = y along with
any valid amount. The system will respond with approval of 1/3 of the
requested amount. Balance inquiry testing, such as for pre-paid gift
cards, is not available in the certification environment.

# Level 3 Processing

Visa and Mastercard Line item entries are limited to 950 lines.

## Commercial and Purchase Cards Table

These fields are optional. However, by sending them you obtain preferred
interchange rates.

| **Field** | **Description** | **Required** | **Example** | **Type** |
| ------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------- | ------------------------------------------- |
| invoice_number | Merchant-defined invoice number. No special characters. | Optional | 1234567890 | AN (20) |
| tax_amount | Tax amount. The decimal is required. | Optional | 10.35 | AN (12 (max)) |
| ship_to_zip | ZIP code where order is delivered. This field used for Level III processing. | Optional | 11106 | AN (5-9) |

## Commercial and Purchase Cards 'Visa and Mastercard Level 3 Fields Table

These are the Level 3 Fields.

| **Field** | **Description** | **Example** | **Type** |
| ---------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ | ------------------------------------------- |
| line_item_count | Number of line item records included in the request. Line item count is limited to 950. (**Visa and Mastercard**) | 102 | N (3) |
| merchant_tax_id | Merchant VAT tax number (**Visa**) | 123456789 | AN (20) |
| customer_tax_id | Customer VAT registration number (**Visa**) | 123456789 | AN (20) |
| summary_commodity_code | Summary commodity code (**Visa**) | 123 | 4 |
| discount_amount | Discount amount (**Visa**) | 10.50 | N (12 (max)) |
| shipping_amount | Shipping amount (**Visa**) | 10.50 | N (12 (max)) |
| duty_amount | Duty amount (**Visa and Mastercard**) | 10.50 | N (12 (max)) |
| ship_to_zip | Ship-to ZIP postal code (**Visa and Mastercard**) | 10111 | AN (10) |
| ship_from_zip | Ship-from ZIP postal code (**Visa and Mastercard**) | 10111 | AN (10) |
| dest_country_code | Destination country code. Country codes are available in the [ISO 3166 standard](https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes). (**Visa and Mastercard**) | 826 | N (3) |
| vat_invoice_number | Invoice reference number (**Visa**) | 12345678 | N (15) |
| order_date | Order date. Format is YYMMDD. (**Visa**) | 160604 | N (6) |
| vat_amount | Value added tax amount (**Visa**) | 5.75 | N (12) |
| alt_tax_amount | Mastercard alternate tax amount. If alt_tax_amount_indicator is N, send 0000000000.00. (**Mastercard**) | 5.75 | N (12) |
| alt_tax_amount_indicator | Mastercard alternate tax indicator: Y = Yes, N = No. If sending N in this field, send 0000000000.00 in alt_tax_amount. (**Mastercard**) | Y | AN (1) |
| visa_line_item | See Visa Line Item Fields for a description of each line item | See unencoded example below | - |
| mc_line_item | See Mastercard Line Item Fields for a description of each line item | See unencoded example below | - |

## Separating values in line item fields

Each of the major credit cards has a line item field for Level 3:

```
mc_line_item
```

```
amex_line_item
```

```
visa_line_item
```

Line item fields are complex. They use the pipe symbol with angle
brackets (`<|>`) as a delimiter to separate fields and values in the
request body.

You encode the request body before sending. You can encode and decode
with an online tool, such as the [Meyer URL
Decoder/Encoder](https://meyerweb.com/eric/tools/dencoder/).

Before encoding, use the literal delimiter `<|>` in the request body text:

```text
mc_line_item=Signage <|> Pylon <|> 1 <|>
```

When you send the request body (`application/x-www-form-urlencoded`),
percent-encode the delimiter per RFC 3986: `<` becomes `%3C`, `|` becomes
`%7C`, and `>` becomes `%3E`:

```text
mc_line_item=Signage%20%3C%7C%3E%20Pylon%20%3C%7C%3E%201%20%3C%7C%3E%20
```

# Visa Line Item Fields for Level 3

These are the line item fields.

| **Description** | **Length** | **Example** |
| ------------------------------------------ | ------------------------------------------------------------------ | ----------------------- |
| Item Commodity Code | Maximum 12 characters | 5096 |
| Item Descriptor | Maximum 35 characters | Drill |
| Product Code | Maximum 12 characters | Equipmentsales |
| Quantity | Maximum 12 characters (Example: 1.0000) | 1 |
| Unit of Measure | Maximum 12 characters | Each |
| Unit Cost | Maximum 10 characters (Example: 1.35) | 33439.93 |
| VAT Tax Amount | Maximum 12 digits | 2340.79 |
| VAT Tax Rate | Maximum 4 digits (7.5 = 7.5%) | 7.00 |
| Discount per Line Item | Maximum 12 digits | .00 |
| Line Item Total | Maximum 12 characters (Example: 1.35) | 33439.93 |
| Debit or Credit Indicator | D = Debit, C = Credit | D |

## Visa Level 3 Request and Response Examples

All requests and responses must be UTF-8 encoded with the non-ASCII
characters URL-encoded. You indicate this encoding with a request header
of Content-Type: application/x-www-form-urlencoded. These examples show
a code sample that is encoded and ready to cut and paste. The unencoded
parameters are listed in the tables.

The unencoded visa_line_item parameter is:

```text
visa_line_item=5096 <|> drill <|> equipmentsales <|> 1 <|>
each <|> 123.123 <|> 1.95 <|> 7.0 <|> 0.00 <|> 66.66
<|> D
```

### Visa Level 3/Corporate Card Sale Example 1

Example 1 shows a Level 3/Corporate Card Sale that uses **card_number**
and **card_exp_date**.

### Request (Ready to cut and paste)

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 750

profile_id=xxx
&profile_key=yyy
&transaction_type=D
&cardholder_street_address=123
&cardholder_zip=55555
&cvv2=123
&transaction_amount=1.00
&card_number=4012xxxxxx348882
&card_exp_date=1222
&invoice_number=209e3212d67d2b49d
&ship_to_zip=99216
&ship_from_zip=99212
&line_item_count=1
&merchant_tax_id=123
&customer_tax_id=123
&summary_commodity_code=123
&discount_amount=1.00
&shipping_amount=1.00
&duty_amount=1.00
&dest_country_code=840
&vat_invoice_number=123
&order_date=090908
&vat_amount=1.00
&alt_tax_amount=1.00
&alt_tax_amount_indicator=Y
&visa_line_item=5096%20%3C%7C%3E%20drill%20%3C%7C%3E%20equipmentsales%...
```

### Response

```text
transaction_id=d96cdfb30aab3e8d9944f9ab4fd5b646
&error_code=010
&auth_response_text=Exact Match
&avs_result=Y
&cvv2_result=M
&auth_code=T2055H
```

### Visa Level 3/Corporate Card Stored Card Sale Example 2

Example 2 shows a Level 3/Corporate Card Sale that uses a stored
**card_id** for a settling transaction.

#### Request (Ready to cut and paste)

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 750

profile_id=xxx
&profile_key=yyy
&transaction_type=D
&cardholder_street_address=123
&cardholder_zip=55555
&cvv2=123
&transaction_amount=1.00
&card_id=8fc94e10b12130f4?ab8a8ceeac40fc69
&invoice_number=209e3212d67d2b49d
&ship_to_zip=99216
&ship_from_zip=99212
&line_item_count=1
&merchant_tax_id=123
&customer_tax_id=123
&summary_commodity_code=123
&discount_amount=1.00
&shipping_amount=1.00
&duty_amount=1.00
&dest_country_code=840
&vat_invoice_number=123
&order_date=090908
&vat_amount=1.00
&alt_tax_amount=1.00
&alt_tax_amount_indicator=Y
&visa_line_item=5096%20%3C%7C%3E%20drill%20%3C%7C%3E%20equipmentsales%...
```

### Response

```text
transaction_id=d96cdfb30aab3e8d9944f9ab4fd5b646
&error_code=010
&auth_response_text=Exact Match
&avs_result=Y
&cvv2_result=M
&auth_code=T2055H
```

### Visa Level 3/Corporate Card Capture Sample Example 3

Example 2 shows a Level 3/Corporate Card Sale that relies on a
**transaction_id** instead of card data like the earlier examples.
Settlement transactions are S type transactions that use
**transaction_id**.

### Request (Ready to cut and paste)

```http
POST /mes-api/tridentApi HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
Pragma: no-cache
User-Agent: Java/1.7.0_21
Host: cert.merchante-solutions.com
Accept: text/html, image/gif, image/jpeg, \*; q=.2, \*/\*; q=.2
Connection: keep-alive
Content-Length: 650

profile_id=xxx
&profile_key=yyy
&transaction_type=S
&transaction_amount=1.00
&transaction_id=0dd82a4f676730c1b789c1f43279dc40
&invoice_number=e19b17e690b5c053f
&ship_from_zip=99212
&line_item_count=1
&merchant_tax_id=123
&customer_tax_id=123
&summary_commodity_code=123
&discount_amount=1.00
&shipping_amount=1.00
&duty_amount=1.00
&dest_country_code=840
&vat_invoice_number=123
&order_date=090908
&vat_amount=1.00
&alt_tax_amount=1.00
&alt_tax_amount_indicator=Y
&visa_line_item=5096%20%3C%7C%3E%20drill%20%3C%7C%3E%20equipmentsales%...
```

### Response

```text
transaction_id=d96cdfb30aab3e8d9944f9ab4fd5b646
&error_code=010
&auth_response_text=Exact Match
&avs_result=Y
&cvv2_result=M
&auth_code=T2055H
```

# Mastercard Line Item Fields for Level 3

Line item fields are separated by `<|>` (pipe symbol with angle
brackets).

Line Item Fields

| **Description**               | **Length**                          | **Example**         |
| ------------------------------------------ | ---------------------------------------------------- | ------------------------- |
| Item Description          | Maximum 35 characters           | Signage         |
| Product Code              | Maximum 12 characters           | Pylon           |
| Item Quantity             | Maximum 12 characters           | 1               |
| Item Unit of Measure      | Maximum 12 characters           | ea              |
| Alternate Tax Identifier  | Maximum 15 characters           | 000000000000000 |
| Tax Rate Applied          | Maximum 4 digits (5.25 = 5.25%) | 5.0             |
| Tax Type Applied          | Maximum 4 digits                | stat            |
| Tax Amount                | Maximum 12 digits               | 1400.00         |
| Discount Indicator        | Y = Yes, N = No                 | N               |
| Net or Gross Indicator    | N = Net, G = Gross              | N               |
| Extended Item Amount      | Maximum 9 digits                | 31064.00        |
| Debit or Credit Indicator | D = Debit, C = Credit           | D               |
| Discount Amount           | Maximum 12 digits               | 0.00            |

## Mastercard Level 3 Request and Response Examples

All requests and responses must be UTF-8 encoded with the non-ASCII
characters URL-encoded. You indicate this encoding with a request header
of Content-Type: application/x-www-form-urlencoded. These examples show
a code sample that is encoded and ready to cut and paste.

The encoded mc_line_item parameter is shown in the example. The
unencoded mc_line_item parameter is:

```text
mc_line_item=Signage <|> Pylon <|> 1 <|> ea <|>
000000000000000 <|> 5.0 <|> STAT <|> 1400.00 <|> N <|> N
<|> 31064.00 <|> D <|> 0.00
```

### Mastercard Level 3/Corporate Card Sale Example 1

Example 1 shows a Level 3/Corporate Card Sale that uses **card_number**
and **card_exp_date**.

### Request body (Ready to cut and paste)

```text
profile_id=xxx
&profile_key=yyy
&cvv2=xxx
&line_item_count=1
&card_number=537144xxxxxx1660
&transaction_amount=31.25
&transaction_type=D
&card_exp_date=1231
&currency_code=840
&profile_key=xxx
&profile_id=yyy
&mc_line_item=Signage%20%3C%7C%3E%20Pylon%20%3C%7C%3E%201%20%3C%7C%3E%20ea%20%3C%7C%3E%20000000000000000%20%3C%7C%3E%205.0%20%3C%7C%3E%20STAT%20%3C%7C%3E%201400.00%20%3C%7C%3E%20N%20%3C%7C%3E%20N%20%3C%7C%3E%2031064.00%20%3C%7C%3E%20D%20%3C%7C%3E%200.00
```

#### Response

```text
transaction_id=xxx&error_code=000&auth_response_text=Approval
T15516&avs_result=0&cvv2_result=M&auth_code=T15516
```

**American Express Commercial and Purchase Cards — Additional CPC
Fields**

Level 3 Fields

| **Field** | **Description** | **Example** | **Type** |
| -------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------ | ------------------------------------------------ |
| requester_name | Name of the person who ordered the service or product | J Smith | AN (38) |
| cardholder_reference_number | Reference number used by the cardholder to identify the transaction | 123456789 | AN (17) |
| ship_to_zip | Destination postal code | 11106 | AN (5-9) |
| vat_amount | VAT amount | 10.55 | N (12 (max)) |
| line_item_count | Line item count | 4 Line item count is limited to 4. | N (-) |
| amex_line_item | See American Express Line Item Fields for a description of each line item | See unencoded example below | - |

## American Express Line Item Fields

The amex_line_item is a combined field. It consists of Item Description,
Item Quantity, and Item Unit Cost. The line item fields are separated by
the `<|>` (pipe symbol with angle brackets).

Item Unit Cost should not exceed transaction_amount.

| **Description**      | **Length**                | **Example**         |
| ------------------------------------------------------------ | ----------------------------------- | ------------------------- |
| Item Description | Maximum 40 characters | Commercial Oven |
| Item Quantity    | Maximum 4 characters  | 1               |
| Item Unit Cost   | Maximum 12 characters | 10000.00        |

## American Express Level 3 Request and Response Examples

All requests and responses must be UTF-8 encoded with the non-ASCII
characters URL-encoded. You indicate this encoding with a request header
of Content-Type: application/x-www-form-urlencoded. These examples show
a code sample that is encoded and ready to cut and paste.

The unencoded amex_line_item parameters are:

```text
amex_line_item=Teton Pullover Hoodie-S-Black <|> 1 <|>
0.00&amex_line_item=Bruno Compete Hoodie-M-Blue <|> 1 <|>
0.00&amex_line_item=Teton Pullover Hoodie-M-Purple <|> 1 <|> 0.00
```

### American Express Level 3/Corporate Card Sale Example 1

Example 1 shows a Level 3/Corporate Card Sale that uses a **card_id**.

### Request body (Ready to cut and paste)

```text
profile_id=xxx
&profile_key=yyy
&tax_amount=0.00
&cvv2=xxx
&line_item_count=3
&transaction_amount=177.40
&cardholder_zip=55555
&ship_to_zip=55555
&vat_amount=0.00
&amex_line_item=Teton%20Pullover%20Hoodie-S-Black%20%3C%7C%3E%201%20%3C%7C%3E%200.00
&amex_line_item=Bruno%20Compete%20Hoodie-M-Blue%20%3C%7C%3E%201%20%3C%7C%3E%200.00
&amex_line_item=Teton%20Pullover%20Hoodie-M-Purple%20%3C%7C%3E%201%20%3C%7C%3E%200.00
&profile_key=xxx
&cardholder_reference_number=000000028
&invoice_number=000000028
&requester_name=Auto Test
&transaction_type=D
&card_id=xxx
&moto_ecommerce_ind=7
&profile_id=xxx
&echo_magento_version=Magento 2.4.6-p5 Community
&cardholder_street_address=123 Main st
```

### Response

```text
transaction_id=e45bbecbf92a39a8845ef84b8f94103b
&error_code=000
&auth_response_text=Exact Match
&avs_result=Y
&cvv2_result=P
&auth_code=T25588
```

# Appendix

## Version History with Revisions and Changelog

| **Date** | **Change** |
| ---------------------------- | -------------------------------------------------- |
| November 2026 | Fixed transaction_type definition. |
| October 2026 | Added code 083 for Visa. |
| October 2026 | Added Requirement column back to tables from feedback. |
| October 2026 | Added *Conditional* to some CIT/MIT request fields. |
| October 2026 | Moved requirement level to start of definition to emphasize it. |
| September 2026 | Removed instances of (Mastercard Only).| 
| August 2026 | Added `account_data_source` and `cvv2` fields to CoF Request Fields table.| 
| July 2026 | SME information added for BIN lookup.| 
| July 2026 | Transaction Type CT information added for BIN lookup on p.27.| 
| July 2026 | Source format updated.| 
| July 2026 | CoF Fields table updated with Visa Digital Commerce Authentication Program API parameters.| 
| June 2026 | Updated cover to MerchantE by Omise and validated links.| 
| June 2026 | Added eight new request and response samples, beginning with Initial Token Sample.|
| May 2026 | Reorganization and clean up of Card Brand Processing Considerations section; changed to MerchantE by Omise branding|
| April 2026 | Visa changed support for CVV2 response codes. Changes updated in CVV Response Codes table.|
| January 2026 | Updated L3 field descriptions; added L3 request examples for card brands; added transaction_type section and diagram; added explanations to Conditional transactions; removed encrypted pin feature |
| November 2023 | Increased purchase_id and invoice_number to 20 characters |
| September 2023 | Updated Re-Authorization to reflect it is after a decline; added Surcharge details |
| June 2023 | Added DBA Fields and explanation |
| May 2023 | Added Apple and Google Pay field request fields and samples |
| April 2023 | Switched to monthly updates; updated template; added ISO Country Codes to International Gateway |
| March 2023 | Updated BlueSnap 3DS information; added 3DT Transaction Type; added threed_secure_referenceId field; edited AVS code responses for clarity |
| Earlier revisions | Complete Revision history is available upon request from MerchantE documentation. |