---
title: "Reporting API Guide"
---
The MerchantE by Omise platform provides a robust variety of reports to assist in reconciliation of card and ACH transactions. In addition to providing reports via the web, an API servlet is available for users that prefer to download the data into their own system in CSV format. An additional option to download data in XML format is also available.

To initiate a request, users must provide a valid user name and password with the rights necessary to use the reporting API and access the requested report. Although several reports are available via the API, only reports enabled for that user name will be accessible. 

The Report API servlet is available on all servers as:

[**https://www.merchante-solutions.com/jsp/reports/report_api.jsp**](https://www.merchante-solutions.com/jsp/reports/report_api.jsp)

This document describes the MerchantE (ME) Reporting Application Programming Interface (API). The following documents provide additional definition and background information. Refer to the following documents listed for applicable information:

- [MerchantE Payment Gateway Specification](https://www.merchante.com/developer-portal/payment-gateway-api)

# Available Reports

The Reporting API provides access to several standard reports as well as customized queries.

## List of ME Reports

The following table provides the report ID for each report offered.

| **Report ID** | **Report Name** | **Description** | **Required Parameters** | **Optional Parameters** |
| --------- | ----------- | ----------- | -------------------- | ------------------- |
| 1 | Batch Summary Report | View credit card totals and details by date and batch. This includes pre- settlement data for all card and payment types. | UserId, userPass, reportType, dsReportId | begin & end date*,encoding, includeTridentTranId, includePurchaseId, xmlEncoding, nodeId |
| 2 | Settlement Summary | View credit card totals and details for transactions settled by MerchantE to the merchant’s bank deposit account. | UserId, userPass, reportType, dsReportId | begin & end date*,encoding, includeTridentTranId, includePurchaseId, xmlEncoding, nodeId |
| 3 | Deposit Summary | The Deposit Summary is a listing of all ACH entries made by MerchantE to the merchant’s bank deposit account. | UserId, userPass, reportType, dsReportId | begin & end date*,encoding, includeTridentTranId, includePurchaseId, xmlEncoding, nodeId |
| 5 | Chargeback Adjustments | The Chargeback Adjustments report is used to identify individual chargebacks and determine the status of each chargeback. | UserId, userPass, reportType, dsReportId | begin & end date*,encoding, includeTridentTranId, includePurchaseId, xmlEncoding, nodeId |
| 7 | Retrieval Requests | The Retrieval Request report is used to identify individual retrieval requests and determine the status of each request. | UserId, userPass, reportType, dsReportId | begin & end date*,encoding, includeTridentTranId, includePurchaseId, xmlEncoding, nodeId |
| 8 | Daily Interchange Summary | The Daily Interchange Report allows the merchant to view the interchange category for which each transaction has qualified and the number of transactions processed by that category. | UserId, userPass, reportType, dsReportId | begin & end date*,encoding, includeTridentTranId, includePurchaseId, xmlEncoding, nodeId |
| 9 | Custom Queries** | Custom Queries are setup for the individual merchant. The most common Custom Query is to the Visa and MasterCard ARDEF tables. | UserId, userPass, reportType, dsReportId queryId | begin & end date*,encoding, includeTridentTranId, includePurchaseId, xmlEncoding, nodeId |
| 10 | FX Batch Summary | The FX Batch Summary Report provides detail information about their International Transactions when using the FX currency conversion product. | UserId, userPass, reportType, dsReportId | begin & end date*,encoding, includeTridentTranId, includePurchaseId, xmlEncoding, nodeId |
| 12 | International Retrieval Requests | The Retrieval Request report is used to identify individual international retrieval requests and determine the status. | UserId, userPass, reportType, dsReportId | begin & end date*,encoding, includeTridentTranId, includePurchaseId, xmlEncoding, nodeId |
| 13 | FX Interchange Summary | The FX Interchange Summary Report assigns an Interchange rate to each transaction. | UserId, userPass, reportType, dsReportId | begin & end date*,encoding, includeTridentTranId, includePurchaseId, xmlEncoding, nodeId |
| 15 | Authorization Log | The Authorization Log Report provides a real-time log of all authorization attempts as they are processed by the ME authorization network. | UserId, userPass, reportType, dsReportId | begin & end date*,encoding, includeTridentTranId, includePurchaseId, xmlEncoding, nodeId |
| 21 | Payment Gateway Unsettled Transactions | This report will provide all transactions that are pending settlement in the Payment Gateway | UserId, userPass, reportType, dsReportId | searchCriteria= CN – Card number IN – Invoice TA – Trans Amount searchValue=XXXX |
| 22 | Payment Gateway Settled Transactions | This report will provide transactions that were settled in the Payment Gateway | UserId, userPass, reportType, dsReportId, profileId, batchDateFrom, batchDateTo | None |
| 23 | Payment Gateway Rejected Transactions | Provides a list of all transactions that were not approved. | UserId, userPass, reportType, dsReportId, profileId, batchDateFrom, batchDateTo | None |
| 28 | Split Funding Summary | The is a report on transactions where the settlement amount is distributed across multiple recipients. | UserId, userPass, reportDateBegin, reportDateEnd, nodeId, reportType, dsReportId profileId, batchDateFrom, batchDateTo | None |
| 29 | Account Validation | This report checks the validity of a bank account before submitting an ACH transaction. | UserId, userPass,reportType, dsReportId, profileId,  accountNumber, routingNumber  | None |

**Note:** The fields **accountNumber** and **routingNumber** are introduced in 2026 with the new Account Validation report (*ReportId=29*).

**Note:** The parameters userId, userPass and dsReportId are required in all report requests.

**Note:** The term ‘begin & end date’ is short-hand for the three parameter sets that make up the report dates: `beginDate.month`, `beginDate.day`, and `beginDate.year`.

**Note:** Custom Queries require consultation with an MerchantE representative. The representative will provide the query ID value to be used and any other necessary parameters.

## Available Custom Queries

The following table lists the custom queries that are available. These queries can be sent using the Custom Queries report type.

| **Query ID** | **Query Name** | **Description** |
| -------- | ---------- | ----------- |
| 283 | Visa ARDEF Table | Allows for download of the current Visa account range definition table. |
| 284 | MasterCard ARDEF Table | Allows for download of the current MasterCard account range definition table. |
| 286 | Discover ARDEF Data | Allows for download of the current Discover account range definition table. |

# Parameters

There is a basic request message for the reporting available from ME.

## Parameter Types

There is a basic request message for the reporting available from ME.

| **Parameter type** | **Description** |
|----------------|-------------------------|
| AN             | Alphanumeric            |
| N              | Numeric                 |
| B              | Boolean (true or false) |

## Report Parameters

The following parameters can be provided depending on the report type.

| **Name** | **Type** | **Max. Length** | **Description** |
| ------ | ------ | ----------- | ----------- |
| userId | A/N | 75 | User Login ID for the ME web reporting system |
| userPass | A/N | 30 | User Login Password for the ME web reporting system |
| dsReportId | N | 4 | ID of the report the client is requesting. Report values include:  1 – Batch Summary 2 – Settlement Summary 3 – Deposit Summary 5 – Chargeback Adjustments 7 – Retrieval Requests 8 – Daily Interchange 9 – Custom Queries 10 – FX Batch Summary 11 – International Chargebacks 12 – International Retrieval Requests 13 – FX Interchange Summary 14 - International Settlement Summary 15 – Authorization Log 21 – Unsettled Payment Gateway 22 – Settled Payment Gateway 23 – Rejected Payment Gateway 28 – Split Funding Summary 29 – Account Validation |
| beginDate.month | AN | 2 | Begin date month for web report. Valid values include:  0 - January 1 - February 2 - March 3 - April 4 - May 5 - June 6 - July 7 - August 8 - September 9 - October 10 - November 11 - December |
| beginDate.day | N | 2 | Begin date day of month for web report |
| beginDate.year | N | 4 | Begin date year for web report |
| endDate.month | AN | 2 | End date month for web report. Valid values include:  0 - January 1 - February 2 - March 3 - April 4 - May 5 - June 6 - July 7 - August 8 - September 9 - October 10 - November 11 - December |
| endDate.day | N | 2 | End date day of month for web report |
| endDate.year | N | 4 | End date year for web report |
| reportDateBegin | AN | 10 | Begin date for the web report. Format should be mm/dd/yyyy. |
| reportDateEnd | AN | 10 | End date for the web report. Format should be mm/dd/yyyy. |
| nodeId | N | 16 | Specific hierarchy node (for example, merchant account number) |
| reportType | N | 4 | Report type. In most cased 0 for summary or 1 for detail. Default is 0. |
| encoding | N | 2 | Sets the download encoding. Most reports only support default of 0 (CSV). Valid values  0 : CSV 1 : Fixed |
| includeTridentTranId | B | n/a | Allows clients using the API to receive the transaction id in the download file. If the Client ID is submitted using the ME Payment Gateway, the client ID will also be provided in the Batch and Settlement Reports. |
| includePurchaseId | B | n/a | Allows clients using the API to receive the purchase id in the report. |
| queryid | N | 6 | Only used with Report ID 9. Used to select a predefined custom query. |
| xmlEncoding | 0,1 | 1 | Send if report should be delivered in xml format. Examples:  XML Encoding Style 0: &lt;ReportColumn name="CardNumber"value="434257xxxxxx9403"/&gt; XML Encoding Style 1: &lt;CardNumber&gt;434257xxxxxx9403&lt;/CardNumber&gt; |
| accountNumber | AN | 17 | Customer's bank account number. Required for Account Validation (dsReportId=29). Checking or savings accounts: 3–17 digits. |
| routingNumber | N | 9 | Bank Routing/Transit (ABA) number. Required for Account Validation (dsReportId=29). |

# Account Validation Report

The **Account Validation Report** is used to check the validity of a bank account before submitting an ACH transaction. For parameter definitions, see the [Report Parameters table](#report-parameters).

## Account Validation Request and Response Sample

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=aaaaaa"
  -d "userPass=xxxxxx"
  -d "reportType=1"
  -d "dsReportId=29"
  -d "profileId=9410000xxxxx00000001"
  -d "accountNumber=xxxxxxxxxx"
  -d "routingNumber=xxxxxxxxxx"

```

### Response

```csv
"Reference Number","Success","Error","Response Code","Actual Date","Response Message","Originated As"
"28141756e1794d509e0a8299ad3b8972","true","false","Success","2026-06-17T16:59:38.691Z","","None"

```

### Field Definitions

| **Field Name** | **Description** | **Length** | **Values** |
| ---------------- | --------------- | ---------- | ---------- |
| Reference Number | Unique identifier for the validation request | 32 | AN |
| Success | Indicates whether the account validation was successful | n/a | B |
| Error | Indicates whether an error occurred during validation | n/a | B |
| Response Code* | Result of the account validation | 50 | AN |
| Actual Date | Date and time the validation was processed (ISO 8601) | 24 | AN |
| Response Message | Additional detail about the validation result | 255 | AN |
| Originated As | Origination type of the validation request | 50 | AN |

**Note:** Response Code responses are `Success`, `Account_Invalid`, `Error_Internal`, and `Error_Not_Subscribed`.

# Batch Summary Report

The **Batch Summary Report** is used to view credit card totals and details by date and batch. This includes pre-settlement data for all card and payment types. The summary version provides batch and summary totals for each card type. The detailed version provides transaction level data. For parameter definitions, see the [Report Parameters table](#report-parameters).

## Batch Summary Report – Summary Version Request and Response Samples

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=aaaaaaa"
  -d "userPass=xxxxxx"
  -d "nodeId=941000123456"
  -d "reportType=0"
  -d "dsReportId=1"

```

### Response

```csv
"Org Id","Org Name","Card Type","Sales Cnt","Sales Amt","Credits Cnt","Credits Amt","Net Amt"
941000123456,"YOURSTORENAME",Visa,144,35635.94,22,-8004.35,27631.589999999997
941000123456,"YOURSTORENAME",Mastercard,69,20959.35,5,-1579.8,19379.550000000003
941000123456,"YOURSTORENAME",Debit/EBT,0,0.0,0,0.0,0.0
941000123456,"YOURSTORENAME",Bill Me Later,0,0.0,0,0.0,0.0
941000123456,"YOURSTORENAME",American Express,111,27976.85,11,-3354.59,24622.25
941000123456,"YOURSTORENAME",Discover,18,7400.22,1,-3500.0,3900.22
941000123456,"YOURSTORENAME",Diners Club,0,0.0,0,0.0,0.0
941000123456,"YOURSTORENAME",JCB,0,0.0,0,0.0,0.0

```

### Field Definitions

| **Field Name** | **Description** | **Length** | **Values** |
|-------------|--------------------------------------|--------|--------|
| Org ID      | Merchant Number                      | 12     | N      |
| Org Name    | Business DBA                         | 25     | AN     |
| Card Type   | Brand of Card                        | 50     | AN     |
| Sales Cnt   | Number of Sale Transactions          | 12     | N      |
| Sales Amt   | Dollar amount of Sale Transactions   | 12     | N      |
| Credits Cnt | Number of Credit Transactions        | 12     | N      |
| Credits Amt | Dollar amount of Credit Transactions | 12     | N      |

## Batch Summary Report - Detail Version Request and Response Samples

The following is a sample of Batch Summary Report request.

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=aaaaaaa"
  -d "userPass=xxxxxx"
  -d "reportDateBegin=04%2F06%2F2012"
  -d "reportDateEnd=04%2F09%2F2012"
  -d "nodeId=941000123456"
  -d "reportType=1"
  -d "includeTridentTranId=true"
  -d "includePurchaseId=true"
  -d "includeClientRefNum=true"
  -d "dsReportId=1"

```

### Response

```csv
"Merchant Id","DBA Name","Term Num","Batch Num","Batch Date","Tran Date","Card Type","Card Number","Reference","Purchase Id","Client Reference","Auth Code","Entry Mode","Tran Amount","Digital Wallet"
941000060005,"PAUL'S TEST ACCOUNT","1",765,05/03/2023,04/20/2023,"AM","341111xxxxxx1000","'3891738407","3891738407","","000000","KEYED",56.0,"APPLE"
941000060005,"PAUL'S TEST ACCOUNT","1",765,05/03/2023,04/20/2023,"AM","345607xxxxxx0874","'3891731307","3891731307","","000000","KEYED",388.0,"GOOGLE"
941000060005,"PAUL'S TEST ACCOUNT","1",765,05/03/2023,04/22/2023,"AM","345607xxxxxx0874","'3891756639","3891756639","","000000","KEYED",500.0," "

```

### Field Definitions

| **Field Name** | **Description** | **Length** | **Values** |
|----------------|-----------------------------|--------|------------|
| Merchant ID    | Merchant Number             | 12     | N          |
| DBA Name       | Business DBA                | 25     | AN         |
| Term Num       | Constant assigned by ME     | 8      | N          |
| Batch Num      | Batch Number                | 3      | N          |
| Batch Date     | Batch Settlement Date       | 10     | MM/DD/YYYY |
| Tran Date      | Transaction Date            | 10     | MM/DD/YYYY |
| Card Type      | Brand of Card               | 2      | N          |
| Card Number    | Truncated Card Number       | 16     | AN         |
| Reference      | ARN                         | 23     | N          |
| Purchase ID    | Invoice Number              | 17     | AN         |
| Auth Code      | Authorization Number        | 6      | AN         |
| Entry Mode     | Method of Card Number Entry | 50     | AN         |
| Tran Amount    | Transaction Amount          | 12     | N          |
| Digital Wallet | Type of Digital Wallet      |        | AN         |

# Settlement Summary Report

The Settlement Summary Report is used to view credit card totals and details for transactions settled by MerchantE to the merchant’s bank deposit account. Transactions that will be funded by a third party are not included in this report. The summary version provides batch and summary totals for each card type. The detail version provides transaction level data. For parameter definitions, see the [Report Parameters table](#report-parameters).

## Settlement Summary Report – Summary Version Request and Response Samples

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=aaaaaaa"
  -d "userPass=xxxxxx"
  -d "reportDateBegin=04%2F04%2F2012"
  -d "reportDateEnd=04%2F13%2F2012"
  -d "nodeId=941000123456"
  -d "reportType=0"
  -d "dsReportId=2"

```

### Response

```csv
"Org Id","Org Name","Card Type","Sales Cnt","Sales Amt","Credits Cnt","Credits Amt","Net Amt"
941000123456,"YOURSTORENAME",Visa,144,35635.94,22,-8004.35,27631.589999999997
941000123456,"YOURSTORENAME",Mastercard,69,20959.35,5,-1579.8,19379.550000000003
941000123456,"YOURSTORENAME",Debit/EBT,0,0.0,0,0.0,0.0
941000123456,"YOURSTORENAME",Bill Me Later,0,0.0,0,0.0,0.0
941000123456,"YOURSTORENAME",American Express,111,27976.85,11,-354.59,24622.25
941000123456,"YOURSTORENAME",Discover,18,7400.22,1,-3500.0,3900.22
941000123456,"YOURSTORENAME",Diners Club,0,0.0,0,0.0,0.0
941000123456,"YOURSTORENAME",JCB,0,0.0,0,0.0,0.0

```

### Field Definitions

| **Field Name** | **Description** | **Length** | **Values** |
|-------------|--------------------------------------|--------|--------|
| Org ID      | Merchant Number                      | 12     | N      |
| Org Name    | Business DBA                         | 25     | AN     |
| Card Type   | Brand of Card                        | 50     | AN     |
| Sales Cnt   | Number of Sale Transactions          | 12     | N      |
| Sales Amt   | Dollar Amount of Sale Transactions   | 12     | N      |
| Credits Cnt | Number of Credit Transactions        | 12     | N      |
| Credits Amt | Dollar Amount of Credit Transactions | 12     | N      |
| Net Amt     | (Sales Amt) - (Credit Amt)           | 12     | N      |

## Settlement Summary Report – Detailed Version Request and Response Samples

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=aaaaaaa"
  -d "userPass=xxxxxx"
  -d "reportDateBegin=04%2F04%2F2012"
  -d "reportDateEnd=04%2F13%2F2012"
  -d "nodeId=941000123456"
  -d "reportType=1"
  -d "includeTridentTranId=true"
  -d "includePurchaseId=true"
  -d "includeClientRefNum=true"
  -d "dsReportId=2"

```

### Response

```csv
"Merchant Id","DBA Name","Term Num","Batch Num","Batch Date","Tran Date","Card Type","Card Number","Reference","Purchase Id","Auth Code","Entry Mode","Tran Amount","Trident Tran Id","Client Ref Num"
941000123456,”YOURSTORENAME”,”1”,216,04/04/2012,04/04/2012,”DS”,”352853xxxxxx1234”,”'2347168185”,”ABCDEF9737619”,”000844”,”KEYED”,”0.99”,”840”,”.50”,e090c187970dxxxxxxxxxx4902e41800,2346317925
941000123456,”YOURSTORENAME”,”1”,216,04/04/2012,04/04/2012,”DS”,”401234xxxxxx1234”,”'2346748735”,”ABCDEF8688519”,”091444”,”KEYED”,”2.99”,,”840”,”2.50”,a43ab3f6417bxxxxxxxxxx5868129ffc,2345824145
941000123456,”YOURSTORENAME”,”1”,216,04/04/2012,04/04/2012,”DS”,”501234xxxxxx1234”,”'2345676155”,”ABCDEF6027219”,”101648”,”KEYED”,”0.99”,,”840”,”.99”,b40398f81a31xxxxxxxxxxd36f3d3657,2344312885

```

### Field Definitions

| **Field Name** | **Description** | **Length** | **Values** |
| --- | --- | --- | --- |
| Merchant ID | Merchant Number | 12 | N |
| DBA Name | Business DBA | 25 | AN |
| Term Num | Constant assigned by ME | 8 | N |
| Batch Num | Batch Number | 3 | N |
| Batch Date | Batch Settlement Date | 10 | MM/DD/YYYY |
| Tran Date | Transaction Date | 10 | MM/DD/YYYY |
| Card Type | Brand of Card | 2 | N |
| Card Number | Truncated Card Number | 16 | AN |
| Reference | ARN | 23 | N |
| Purchase ID | Invoice Number | 17 | AN |
| Auth Code | Authorization Number | 6 | AN |
| Entry Mode | Method of Card Number Entry | 50 | AN |
| Tran Amount | Transaction Amount | 12 | N |
| Currency Code | Funded Currency (Only present when Multi-Currency is enabled) | 3 | 3 digitnumericcurrency code |
| Original Amount | Original amount of authorization (Only presentwhen Multi-Currency is enabled) | 12 | N |
| Original CurrencyCode | Currency code that was authorized andcleared to cardholder (Only present whenMulti- Currency is enabled) | 3 | 3 digitnumericcurrency code |
| Trident Tran ID | Trident Transaction ID | 32 | AN |
| Client Ref Num | Client Reference Number | 96 | AN |

# Chargeback Adjustments Report

The Chargeback Adjustments report is used to identify individual chargebacks and determine the status of each chargeback. This report is only available in the detail version. For parameter definitions, see the [Report Parameters table](#report-parameters).

## Chargeback Adjustments – Detail Version Request and Response Sample

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=aaaaaaa"
  -d "userPass=xxxxxx"
  -d "reportDateBegin=04%2F09%2F2012"
  -d "reportDateEnd=04%2F09%2F2012"
  -d "nodeId=941000123456"
  -d "reportType=1"
  -d "includeTridentTranId=true"
  -d "includePurchaseId=true"
  -d "includeClientRefNum=true"
  -d "dsReportId=5"

```

### Response

```csv
"Merchant Id","DBA Name","Control Number","VCR Workflow","Incoming Date","Card Number","Reference Number","Tran Date","Tran Amount","Trident Tran ID","Purchase ID","Client Ref Num","Auth Code","Adj Date","Adj Ref Num","Reason","First Time","Reason Code","CB Ref Num","Terminal ID”
941000123456,"YOURSTORENAME",2811904,"Collaboration",04/08/2012,401234xxxxxx7806,'24436542026000728820478,01/25/2012,299.95,qwertyuiop12345da8dcf4180084fe42,72880690,"72880690:0:01-25-2012--03:48:24",0000 ,"04/10/2012-","00072882047","Fraudulent Transaction - Card Absent Environment",Y,83,97352,"94100012345600000001"
941000123456,"YOURSTORENAME",2811922,04/08/2012,501234xxxxxx6321,'24436542075000732192708,03/14/2012,399.95,qwertyuiop12345eb571320dd8b5b9d1,73217957,"73217957:0:03-14-2012--03:43:48",374908,"04/10/2012-","00073219270","Fraudulent Transaction - Card Absent Environment",Y,83,595035,"94100012345600000021"
941000123456,"YOURSTORENAME",2812436,04/08/2012,601234xxxxxx9484,'73251542,03/19/2012,5000.0,qwertyuiop123450b3a43f558b6aed0e,73250149,"73250149:0:03-19-2012--03:42:24",,"04/10/2012-","73251542","Cardholder or Issuer challenges the validity of a Card Not Present Card Transaction because the Acquirer or Merchant did not obtain Address Verification or did not obtain and submit the CID with the Authorization Request",Y,UA32,8243606419,"94100012345600000013"

```

### Field Definitions

| **Field Name** | **Description** | **Length** | **Values** |
| --- | --- | --- | --- |
| Merchant ID | Merchant Number | 12 | N |
| DBA Name | Business DBA | 25 | AN |
| Control Number | ME Internal Tracking Number | 7 | N |
| VCR Workflow | Visa Chargeback Workflow(Collaboration/Allocation) | 13 | AN |
| Incoming Date | Date of the Chargeback | 10 | MM/DD/YYYY |
| Card Number | Truncated Card Number | 16 | AN |
| Reference Number | ARN-23 digit reference number used whentransaction is processed to issuing bank | 23 | N |
| Tran Date | Date of the Transaction | 10 | MM/DD/YYYY |
| Tran Amount | Transaction Amount | 12 | N |
| Trident Tran ID | Trident Transaction ID | 32 | AN |
| Purchase ID | Invoice Number | 17 | AN |
| Client Ref Num | Client Reference Number | 96 | AN |
| Auth Code | Authorization Number | 6 | AN |
| Adj Date | Date of the Adjustment | 10 | MM/DD/YYYY |
| Adj Ref Num | Internal Tracking Number | 11 | N |
| Reason | Text explaining reason for chargeback | 60 | AN |
| First Time | Is this a first time Chargeback | 1 | Y or N |
| Reason Code | Numeric code for chargeback type | 4 | N |
| CB Ref Num | Internal Tracking Number | 10 | N |
| Terminal ID | The Profile ID | 20 | N |

# Retrieval Request Report

The Retrieval Request report is used to identify individual retrieval requests and determine the status of each request. This report is only available in the detail version. For parameter definitions, see the [Report Parameters table](#report-parameters).

## Retrieval Request Report – Detail Version Request and Response Sample

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=aaaaaaa"
  -d "userPass=xxxxxx"
  -d "reportDateBegin=01%2F01%2F2012"
  -d "reportDateEnd=04%2F16%2F2012"
  -d "nodeId=941000123456"
  -d "reportType=1"
  -d "includeTridentTranId=true"
  -d "includePurchaseId=true"
  -d "includeClientRefNum=true"
  -d "dsReportId=7"

```

### Response

```csv
"Merchant Id","DBA Name","Control Number","Incoming Date","Card Number","Reference Number","Tran Date","Tran Amount","Due Date","Reason Code","Retrieval Ref Num"
941000123456,"YourStoreName",2815962,04/13/2012,601100xxxxxx5317,'72835320,01/17/2012,129.95,04/27/2012,RN2,3833638576
941000123456,"YourStoreName",2815983,04/12/2012,601100xxxxxx6662,'73259493,03/20/2012,999.95,04/26/2012,RM,9166493410
941000123456,"YourStoreName",2812419,04/09/2012,601100xxxxxx1007,'73252653,03/19/2012,149.95,04/23/2012,A,1848961375

```

### Field Definitions

| **Field Name** | **Description** | **Length** | **Values** |
|-------------------|----------------------------------|--------|------------|
| Merchant Id       | Merchant Number                  | 12     | N          |
| DBA Name          | Business DBA                     | 25     | AN         |
| Control Number    | Internal Tracking Number         | 7      | N          |
| Incoming Date     | Date of the Chargeback           | 10     | MM/DD/YYYY |
| Card Number       | Truncated Card Number            | 16     | AN         |
| Reference Number  | Internal Tracking Number         | 8      | N          |
| Tran Date         | Transaction Date                 | 10     | MM/DD/YYYY |
| Due Date          | Last Day to Respond to ME        | 10     | MM/DD/YYYY |
| Reason Code       | Reason for the Retrieval Request | 4      | AN         |
| Retrieval Ref Num | Retrieval Reference Number       | 10     | N          |

# Daily Interchange Summary Report

The **Daily Interchange** Report allows the merchant to view the interchange category for which each transaction has qualified and the number of transactions processed by that category. This report is only available in the detail version. For parameter definitions, see the [Report Parameters table](#report-parameters).

## Daily Interchange Summary Report – Detail Version Request and Response Sample

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=aaaaaaa"
  -d "userPass=xxxxxx"
  -d "reportDateBegin=04%2F09%2F2012"
  -d "reportDateEnd=04%2F16%2F2012"
  -d "nodeId=941000123456"
  -d "reportType=1"
  -d "includeTridentTranId=true"
  -d "includePurchaseId=true"
  -d "includeClientRefNum=true"
  -d "dsReportId=8"

```

### Response

```csv
"Org Id","Org Name","Batch Date","Tran Date","CT","Cat","Cat Desc","Rate","Per Item","Card Number","Tran Amount","Auth Code","Entry Mode","Purchase ID","Reference","IC Due"
941000123456,"YourStoreName",04/09/2012,04/08/2012,"DS","9125","Commercial - MSL / Electronic",2.3,0.1,"601149xxxxxx1234",399.95,"00825B", KEYED","73374264","'73375647",9.298849999999998
941000123456,"YourStoreName.",04/10/2012,04/09/2012,"DS","9125","Commercial - MSL / Electronic",2.3,0.1,"601100xxxxxx2345",29.95,"00962B","KEYED","73382904","'73382907",0.7888499999999999
941000123456,"YourStoreName",04/10/2012,04/09/2012,"DS","9125","Commercial - MSL / Electronic",2.3,0.1,"601139xxxxxx3456",229.95,"00912B","KEYED","73377156","'73378509",5.38884999999999

```

### Field Definitions

| **Field Name** | **Description** | **Length** | **Values** |
| --- | --- | --- | --- |
| Org Id | Merchant Number | 12 | N |
| Org Name | Business DBA | 25 | AN |
| Batch Date | Batch Settlement Date | 10 | MM/DD/YYYY |
| Tran Date | Transaction Date | 10 | MM/DD/YYYY |
| CT | Type of Card | 2 | AN |
| Cat | Interchange level at which the transaction wasprocessed – Numeric version | 4 | N |
| Cat Desc | Text Description of the Interchange level atwhich the transaction was processed | 60 | AN |
| Rate | Discount Rate for that Interchange level | 20 | N |
| Per Item | Per Item for that Interchange level | 20 | N |
| Card Number | Truncated Card Number | 16 | AN |
| Tran Amount | Transaction Amount | 12 | AN |
| Auth Code | Authorization Number | 6 | AN |
| Entry Mode | Method of Card Number Entry | 20 | AN |
| Purchase ID | Invoice Number | 17 | AN |
| Reference | Internal tracking Number | 8 | N |
| IC Due | Total Interchange Amount | 12 | AN |

# FX Batch Summary Report

The FX Batch Summary Report provides the merchant with detail information about their International Transactions when using the FX currency conversion product. This report is only available in the detail version. For parameter definitions, see the [Report Parameters table](#report-parameters).

## FX Batch Summary Report – Detail Version Request and Response Sample

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=xxxxxx"
  -d "userPass=xxxxxx"
  -d "reportDateBegin=01%2F01%2F2012"
  -d "reportDateEnd=01%2F04%2F2012"
  -d "nodeId=941000123456"
  -d "reportType=1"
  -d "includeTridentTranId=true"
  -d "includePurchaseId=true"
  -d "includeClientRefNum=true"
  -d "dsReportId=10"

```

### Response

```csv
"Merchant ID","Dba Name","Term \#","Batch Number","Batch Date","Tran Date","Card Type","Card Number","Reference Number","Purchase ID","Trident Tran ID","Client Reference","Auth Code","Entry Mode","Debit Credit Ind","Currency Code","Amount","Amount USD"
941000123456,"YourStoreName","1",20120101,01/01/2012,01/01/2012,"VS","401234xxxxxx1234","'4602428770","4602428791","1234567890asdfghjklpe5c373131e71","393500","","KEYED","D","ARS",347.89,77.42
941000123456,"YourStoreName","1",20120101,01/01/2012,01/01/2012,"VS","401234xxxxxx2345","'4602179723","4602179744","1234567890asdfghjklpcc4468ac5fb1","1464807","","KEYED","D","ARS",71.23,15.85
941000123456,"YourStoreName","1",20120101,01/01/2012,01/01/2012,"MC","501234xxxxxx3456","'4602630011","4602630032","1234567890asdfghjklpc3be8d959b17","1254303","","KEYED","D","ARS",34.92,7.77

```

### Field Definitions

| **Field Name** | **Description** | **Length** | **Values** |
|------------------|-----------------------------|--------|------------|
| Merchant ID      | Merchant Number             | 12     | N          |
| Dba Name         | Business DBA                | 25     | AN         |
| Term \#          | Constant assigned by ME     | 8      | N          |
| Batch Number     | Batch Number                | 8      | N          |
| Batch Date       | Batch Settlement Date       | 10     | MM/DD/YYYY |
| Tran Date        | Transaction Date            | 10     | MM/DD/YYYY |
| Card Type        | Brand of Card               | 4      | AN         |
| Card Number      | Truncated Card Number       | 16     | AN         |
| Reference Number | Internal Tracking Number    | 10     | N          |
| Purchase ID      | Invoice Number              | 17     | AN         |
| Trident Tran ID  | Trident Transaction ID      | 32     | AN         |
| Client Reference | Client Reference Number     | 96     | AN         |
| Auth Code        | Authorization Number        | 6      | AN         |
| Entry Mode       | Method of Card Number Entry | 50     | AN         |
| Debit Credit Ind | Debit or Credit Indicator   | 1      | AN         |
| Currency Code    | Native Currency Code        | 3      | AN         |
| Amount           | Native Currency Amount      | 12     | N          |
| Amount USD       | USD Amount                  | 12     | N          |

# International Retrieval Requests Report

The International Retrieval Request Report is used to identify individual international retrieval requests and determine the status. This report is only available at the detail level. For parameter definitions, see the [Report Parameters table](#report-parameters).

## International Retrieval Request Report – Detail Version Request and Response Sample

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=aaaaaaa"
  -d "userPass=xxxxxx"
  -d "reportDateBegin=02%2F01%2F2012"
  -d "reportDateEnd=02%2F08%2F2012"
  -d "nodeId=941000123456"
  -d "reportType=1"
  -d "includeTridentTranId=true"
  -d "includePurchaseId=true"
  -d "includeClientRefNum=true"
  -d "dsReportId=12"

```

### Response

```csv
"Merchant ID","Dba Name","Case \#","Incoming Date","Card Number","Reference Number","Trident Tran ID","Tran Date","Currency Code","Tran Amount","Reason Code","Reason","Type","Adjustment Date"
941000123456,"YourStoreName","040212713547",02/05/2012,"401234xxxxxx1234","4593872049","24d961234567890asdfghjklpe954508",12/19/2011,"ARS",1329.53,298.08,"83","","Request for Photocopy",
941000123456,"YourStoreName","070212716948",02/08/2012,"501234xxxxxx2345","4618127225","42c061234567890asdfghjklpbf38290",01/25/2012,"AUD",109.97,110.0,"4837","","Fraud Investigation",
941000123456,"YourStoreName","070212716947",02/08/2012,"502345xxxxxx2345","4612765873","ebde21234567890asdfghjklp39809f2",01/17/2012,"AUD",76.42,75.0,"4837","","Request for Photocopy",

```

### Field Definitions

| **Field Name** | **Description** | **Length** | **Values** |
| --- | --- | --- | --- |
| Merchant ID | Merchant Number | 12 | N |
| Dba Name | Business DBA | 25 | A/N |
| Case # | Internal Tracking Number | 12 | N |

| Incoming Date | Date of the Retrieval | 10 | MM/DD/YYYY |
| Card Number | Truncated Card Number | 16 | A/N |
| Reference Number | ARN-23 digit reference number used whentransaction is processed to issuing bank | 10 | N |
| Trident Tran ID | Trident Transaction ID | 32 | A/N |
| Tran Date | Transaction Date | 10 | MM/DD/YYYY |
| Currency Code | Native Currency Code | 3 | A/N |
| Tran Amount | Original Currency Amount, USD Amount | 25 | N |
| Reason Code | Numeric code for retrieval type | 4 | N |
| Reason | Text explaining reason for retrieval | 60 | A/N |
| Type | Type of Retrieval | 50 | A/N |
| Adjustment Date | Date the Adjustment was made to theMerchant’s Account | 10 | MM/DD/YYYY |

# FX Interchange Summary Report

The FX Interchange Summary Report assigns an Interchange rate to each transaction. This report is only available at the detail level. For parameter definitions, see the [Report Parameters table](#report-parameters).

## FX Interchange Summary Report – Detail Version Request and Response Sample

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=aaaaaaa"
  -d "userPass=xxxxxx"
  -d "reportDateBegin=02%2F28%2F2012"
  -d "reportDateEnd=02%2F28%2F2012"
  -d "nodeId=941000123456"
  -d "reportType=1"
  -d "includeTridentTranId=true"
  -d "includePurchaseId=true"
  -d "includeClientRefNum=true"
  -d "dsReportId=13"

```

### Response

```csv
"Merchant ID","Dba Name","Batch Date","Tran Date","Card Type","Card Number","Reference Number","Purchase ID","Trident Tran ID","Client Reference","Auth Code","Entry Mode","Debit Credit Ind","Currency Code","Tran Amount","Tran Amount USD","IC Due"
941000086319,"2CHECKOUT.COM, INC.2",02/28/2012,02/24/2012,"Visa","401234xxxxxx1234","'4641783816","4641784008","f8c5d6ba0d0c3555898593d618cee6d8","1641210","","KEYED","D","ARS",57.61,12.69,0.17
941000086319,"2CHECKOUT.COM, INC.2",02/28/2012,02/24/2012,"Visa","412345xxxxxx2345","'4641797197","4641797218","3537abd86c773b04801a1889abf2cff9","96849","","KEYED","D","ARS",81.74,18.0,0.24
941000086319,"2CHECKOUT.COM, INC.2",02/28/2012,02/24/2012,"Visa","404567xxxxxx3456","'4559106314","4641666894","df93767c921931b4a8d0899709c9f6c0","","","KEYED","D","ARS",13.58,2.99,0.04

```

### Field Definitions

| **Field Name** | **Description** | **Length** | **Values** |
|------------------|-----------------------------|--------|------------|
| Merchant ID      | Merchant Number             | 12     | N          |
| Dba Name         | Business DBA                | 25     | AN         |
| Batch Date       | Batch Settlement Date       | 10     | MM/DD/YYYY |
| Tran Date        | Transaction Date            | 10     | MM/DD/YYYY |
| Card Type        | Brand of Card               | 4      | AN         |
| Card Number      | Truncated Card Number       | 16     | AN         |
| Reference Number | Internal Tracking Number    | 10     | N          |
| Purchase ID      | Invoice Number              | 17     | AN         |
| Trident Tran ID  | Trident Transaction ID      | 32     | AN         |
| Client Reference | Client Reference Number     | 96     | AN         |
| Auth Code        | Authorization Number        | 6      | AN         |
| Entry Mode       | Method of Card Number Entry | 50     | AN         |
| Debit Credit Ind | Debit or Credit Indicator   | 1      | AN         |
| Currency Code    | Native Currency Code        | 4      | AN         |
| Tran Amount      | Native Currency Amount      | 12     | N          |
| Tran Amount USD  | USD Amount                  | 12     | N          |
| IC Due           | Total Interchange amount    | 12     | N          |

# International Summary Settlement Report

This report provides detail of all Settled International transactions. This report is only available at the detail level. For parameter definitions, see the [Report Parameters table](#report-parameters).

## International Summary Settlement Report – Detail Version Request and Response Sample

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=aaaaaaa"
  -d "userPass=xxxxxx"
  -d "reportDateBegin=02%2F28%2F2012"
  -d "reportDateEnd=02%2F28%2F2012"
  -d "nodeId=941000123456"
  -d "reportType=1"
  -d "includeTridentTranId=true"
  -d "includePurchaseId=true"
  -d "includeClientRefNum=true"
  -d "dsReportId=14"

```

### Response

```csv
"Merchant ID","Dba Name","Batch Date","Tran Date","Card Type","Card Number","Reference Number","Purchase ID","Trident Tran ID","Client Reference","Auth Code","Entry Mode","Debit Credit Ind","Currency Code","Tran Amount"
941000123456,"YourStoreName",02/28/2012,02/24/2012,"Visa","404567xxxxxx1234","'4641783816","4641784008","1234567890asdfghjklp93d618cee6d8","1641210","","KEYED","D","ARS",57.61
941000123456,"YourStoreName",02/28/2012,02/24/2012,"Visa","405678xxxxxx2345","'4641797197","4641797218","1234567890asdfghjklp1889abf2cff9","96849","","KEYED","D","ARS",81.74
941000123456,"YourStoreName",02/28/2012,02/24/2012,"Visa","406789xxxxxx3456","'4559106314","4641666894","asdfghjklp1234567890899709c9f6c0","","","KEYED","D","ARS",13.58

```

### Field Definitions

| **Field Name** | **Description** | **Length** | **Values** |
| --- | --- | --- | --- |
| Merchant ID | Merchant Number | 12 | N |
| Dba Name | Merchant DBA | 25 | AN |
| Batch Date | Batch Settlement Date | 10 | MM/DD/YYYY |
| Tran Date | Transaction Date | 10 | MM/DD/YYYY |
| Card Type | Brand of Card | 50 | AN |
| Card Number | Truncated Card Number | 16 | AN |
| Reference Number | ARN-23 digit reference number used whentransaction is processed to issuing bank | 10 | N |
| Purchase ID | Invoice Number | 17 | N |
| Trident Tran ID | Trident Transaction ID | 32 | AN |
| Client Reference | Client Reference Number | 96 | AN |
| Auth Code | Authorization Number | 6 | AN |
| Entry Mode | Method of Card Number Entry | 50 | AN |
| Debit Credit Ind | Debit or Credit Indicator | 1 | AN |
| Currency Code | Native Currency Code | 4 | AN |
| Tran Amount | Transaction Amount in Native Currency | 12 | N |

# Authorization Log Report

The **Authorization Log Report** provides a real-time log of all authorization attempts as they are processed by the ME authorization network. This report is only available at the detail level. For parameter definitions, see the [Report Parameters table](#report-parameters).

## Authorization log report – Detail Version Request and Response sample.

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=aaaaaaa"
  -d "userPass=xxxxxx"
  -d "reportDateBegin=02%2F28%2F2012"
  -d "reportDateEnd=02%2F28%2F2012"
  -d "nodeId=941000123456"
  -d "reportType=1"
  -d "includeTridentTranId=true"
  -d "includePurchaseId=true"
  -d "includeClientRefNum=true"
  -d "dsReportId=15"

```

### Response

```csv
"Terminal Id","Tran Date","Tran Time","POS Cond.","Term Fmt","Card Number","MCC","Card Type","Tran Amount","Exp Date","Response Code","Auth Code","Host","Line Type","SRC","Station ID","AVS","CVV/CVV2","PS/2000","Tran ID","Val Code","Product Code","Currency Code","Digital Wallet"
'94100006000500000001,05/01/2023,19:50:21,8,"3","520424xxxxxx0049",5999,"MC",500.0,"2412","00","T40759","Trident","","7","","A","/","N","0501MCC000001 ","","MCC","USD",""
'94100006000500000001,05/02/2023,16:34:30,8,"3","537143xxxxxx8489",5999,"MC",500.0,"2812","00","T40913","Trident","","7","","0","/","N","0502MCC000001 ","","MCC","USD","GOOGLE"
'94100006000500000001,05/11/2023,15:33:50,59,"3","529905xxxxxx0019",5999,"MC",56.33,"2512","00","T45874","Trident","","7","","0","/","N","0511MCC000001 ","","MCC","USD","APPLE"

```

### Field Definitions

| **Field Name** | **Description** | **Length** | **Values** |
| --- | --- | --- | --- |
| Terminal ID | Profile ID | 20 | N |
| Tran Date | Transaction Date | 10 | MM/DD/YYYY |
| Tran Time | Time of Authorization | 8 | HH:MM:SS |
| POS Cond | POS Condition Code | 4 | N |
| Term Fmt | Historical Data – NA | 10 | AN |
| Card Number | Truncated Card Number | 16 | N |
| MCC | Merchant Category Code | 4 | N |
| Card Type | Brand of Card | 2 | AN |
| Tran Amount | Amount of Authorization | 12 | N |
| Exp Date | Card Expiration Date – YYMM | 4 | N |
| Response Code | Authorization Response Code | 2 | N |
| Auth Code | Authorization Number | 6 | AN |
| Host | Authorization Host | 10 | AN |
| Line Type | Historical Data – NA | 10 | AN |
| SRC | Authorization Source | 2 | N |
| Station ID | Historical Data – NA | 10 | AN |
| AVS | Address Verification Service | 2 | AN |
| CVV/CVV2 | Card Verification Value | 2 | AN |
| PS/2000 | Returned ACI | 2 | AN |
| Tran ID | Transaction ID | 15 | AN |
| Tran Fee Amount | Amount of Surcharge | 9 | AN |
| Val Code | Validation Code | 4 | AN |
| Product Code | Product Level Result Code | 2 | AN |
| Currency Code | Authorized Currency | 3 | ISO 3-DigitAlphaCurrency |
| Digital Wallet | Type of Digital Wallet |  | AN |

# Unsettled Payment Gateway Report

The Unsettled Payment Gateway Report shows all transaction spending settlement in the Payment Gateway. For parameter definitions, see the [Report Parameters table](#report-parameters).

## Unsettled Payment Gateway Report Request and Response Sample

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=aaaaaa"
  -d "userPass=xxxxxx"
  -d "dsReportId=21"
  -d "profileId=9410000xxxxx00000001"
  -d "searchValue=1234"
  -d "searchCriteria=CN"

```

### Response

```csv
"Tran ID", "Tran Date","Card Type","Card Number","Reference Number","Invoice Number", "Auth Code","Status","Currency Code","Amount"
"7679fc5af3253c5fbeaa798458e5c545","11/12/2013","VS","401288xxxxxx8882","528630419","123456","T5566H","D","USD","9.95"

```

### Field Descriptions

| **Field Name** | **Description** | **Length** | **Values** |
| ---------- | ----------- | ------ | ------ |
| Tran ID | Trident Transaction ID | 32 | AN |
| Tran Date | Transaction Date | 10 | MM/DD/YYYY |
| Card Type | Brand of Card | 2 | A |
| Card Number | Truncated Card Number | 16 | AN |
| Reference Number | ARN-23 digit reference number used when transaction is processed to issuing bank | 10 | N |
| Invoice Number | Invoice Number | 17 | AN |
| Auth Code | Authorization Number | 6 | AN |
| Status | Gateway Transaction Type | 1 | A |
| Currency Code | Currency Type | 3 | A |
| Amount | Dollar Amount of the Transaction | 12 | N |

# Settled Payment Gateway Report

The Settled Payment Gateway Report shows all transactions settled in the Payment Gateway. For parameter definitions, see the [Report Parameters table](#report-parameters).

## Settled Payment Gateway Report Request and Response Sample

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=aaaaaa"
  -d "userPass=xxxxxx"
  -d "batchDateFrom=10%2F03%2F2013"
  -d "batchDateTo=10%2F03%2F2013"
  -d "dsReportId=22"
  -d "profileId=9410000xxxxx00000001"

```

### Response

```csv
"Tran ID","Tran Date","Settle Date","Card Type","Card Number","Reference Number","Invoice Number", "Auth Code","Currency Code","Amount"
"7679fc5af3253c5fbeaa798458e5c111","11/12/2013","11/13/2013","VS","401288xxxxxx8882","528630419","123456","T5566H","USD","9.95"

```

### Field Descriptions

| **Field Name** | **Description** | **Length** | **Values** |
| ---------- | ----------- | ------ | ------ |
| Tran ID | Trident Transaction ID | 32 | A/N |
| Tran Date | Transaction Processed Date | 10 | MM/DD/YYYY |
| Settle Date | Transaction Settlement Date | 10 | MM/DD/YYYY |
| Card Type | Brand of Card | 2 | A |
| Card Number | Truncated Card Number | 16 | A/N |
| Reference Number | ARN-23 digit reference number used when transaction is processed to issuing bank | 10 | N |
| Invoice Number | Invoice Number | 17 | A/N |
| Auth Code | Authorization Number | 6 | A/N |
| Currency Code | Currency Type | 3 | A |
| Amount | Transaction Amount | 12 | N |

# Rejected Payment Gateway Report

The Rejected Payment Gateway Report shows all transactions that did not received approval in the Payment Gateway. For parameter definitions, see the [Report Parameters table](#report-parameters).

## Rejected Payment Gateway Report Request and Response Sample

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=aaaaaa"
  -d "userPass=xxxxxx"
  -d "fromDate=10%2F02%2F2013"
  -d "toDate=10%2F03%2F2013"
  -d "dsReportId=23"
  -d "profileId=9410000xxxxx00000001"

```

### Response

```csv
"Date","Tran Type","DBA Name","Account Number","Tran Amount","Error Code/Description","AVS Result","CVV2 Result","Transaction_id","invoice_number"
"11/12/2013 3:55:15","Sale","YourStoreName","401288xxxxxx8882","19.95","005 - Decline","N","M","7679fc5af3253c5fbeaa798458e5c545","123456"

```

### Field Definitions

| **Field Name** | **Description** | **Length** | **Values** |
| --- | --- | --- | --- |
| Date | Date and Time Stamp of Transaction | 18 | MM/DD/YYYY11:22:33 |
| Tran Type | Type of Transaction Attempted | 12 | A |
| DBA Name | Merchant DBA Name | 25 | AN |
| Account Number | Truncated Card Number | 16 | AN |
| Tran Amount | Transaction Amount | 12 | N |
| ErrorCode/Description | 3 Digit Error Code and Detail Description | 20 | AN |
| AVS Result | Address Verification Result Code | 1 | AN |
| CVV2 Result | CVV Card Code Result | 1 | AN |
| Transaction ID | Trident Transaction ID | 32 | AN |
| Invoice Number | Invoice Number | 17 | AN |

# Deposit Summary Report

The Deposit Summary is a listing of all ACH entries made by MerchantE to the merchant’s bank deposit account. This report is only available in the detail version. For parameter definitions, see the [Report Parameters table](#report-parameters).

## Deposit Summary - Detail Version Request and Response Sample

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=aaaaaaa"
  -d "userPass=xxxxxx"
  -d "reportDateBegin=04%2F09%2F2012"
  -d "reportDateEnd=04%2F09%2F2012"
  -d "nodeId=941000123456"
  -d "reportType=1"
  -d "dsReportId=3"

```

### Response

```csv
"Org Id","Org Name","Date","Type","Amount","Reference"
941000123456,"YourStoreName",04/04/2012,"CREDIT",4239.21,"'121123456789012"
941000123456,"YourStoreName",04/04/2012,"DEBIT",-199.95,"'121234567890123"
941000123456,"YourStoreName",04/04/2012,"CREDIT",17119.24,"'121345678901234"

```

### Field Definitions

| **Field Name** | **Description** | **Length** | **Values** |
|------------|---------------------------|--------|-----------------|
| Org Id     | Merchant Number           | 12     | N               |
| Org Name   | Business DBA              | 25     | AN              |
| Date       | Date of ACH Transaction   | 10     | MM/DD/YYYY      |
| Type       | Type of ACH Transaction   | 10     | Credit or Debit |
| Amount     | Amount of ACH Transaction | 12     | N               |
| Reference  | ACH Reference Number      | 15     | N               |

# Split Funding Summary Report

The Split Funding Summary Report is a report on transactions where the settlement amount is distributed across multiple recipients. For parameter definitions, see the [Report Parameters table](#report-parameters).

The parameter **dsReportId=28** designates the report type as the Split Funding Summary.

## Split Funding Summary Report Request and Response Sample

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=xxxxx"
  -d "userPass=xxxxxx"
  -d "reportDateBegin=04%2F09%2F2012"
  -d "reportDateEnd=04%2F09%2F2012"
  -d "nodeId=941000123xxx"
  -d "reportType=1"
  -d "dsReportId=28"

```

### Response

```csv
"Transaction ID", "Merchant Number", "Date Created", "Card Number", "Split Amount", "Transaction Total"
"e72ae53348e732aea1b977bc4ac85111","94100012xxxx","2018-09-28","463962xxxxxx7200","102.6400","105.67"
"f039d7701d8b317c84bf43a42c4df111","94100012xxxx","2018-09-28","435545xxxxxx8703","51.3200","54.27"
"1bf1e186901e3c8e9194cd8379bae111","94100012xxxx","2018-09-28","546170xxxxxx8274","51.3200","54.27"
"5dc47e15e7a831c0a247d81c77293111","94100012xxxx","2018-09-28","414720xxxxxx9959","51.3200","54.27"

```

### Field Definitions

| **Field Name** | **Description** | **Length** | **Values** |
|----|----|----|----|
| Transaction ID | Trident Transaction ID | 32 | AN |
| Merchant Number | A unique Org ID assigned to the merchant | 12 | N |
| Date Created | Day, month, and year of transaction | 10 | N |
| Card Number | Masked card number | 16 | A/N |
| Split Amount | The amount of the transaction funded to the specified merchant | 12 | N |
| Transaction Total | Full amount of the underlying transaction before splits are applied | 12 | N |

# Custom Queries

Custom Queries are setup for the individual merchant. The most common Custom Query is to the Visa and MasterCard ARDEF tables. They are used to specify the query type for dsReportId=9.

## Visa ARDEF Request and Response Sample

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=aaaaaaa"
  -d "userPass=xxxxxx"
  -d "reportDateBegin=04%2F22%2F2012"
  -d "reportDateEnd=04%2F23%2F2012"
  -d "reportType=0"
  -d "queryId=283"
  -d "dsReportId=9"

```

### Response

```csv
"RANGE_LOW","RANGE_HIGH","USAGE","CARD_TYPE","COMM_CARD_SERVICE_IND","PRODUCT_ID","PRODUCT_TYPE_EXT","ISSUER_COUNTRY"
"101100000","152080999","D","A","null","null","null","US"
"11300000","11306999","D","A","null","E","null","PH"
"11308000","11309999","D","A","null","E","null","PH"
"11310000","11399999","D","A","null","null","null","US"
"11400000","21502008","D","A","null","null","null","US"
"180000","180999","D","A","null","null","null","US"
"200100000","200199999","D","A","null","null","null","US"

```

### Field Descriptions

| **Field Name** | **Description** |
| **---** | **---** |
| RANGE_LOW |  |
| RANGE_HIGH |  |
| USAGE |  |
| CARD_TYPE |  |
| COMM_CARD_SERVICE_IND |  |
| PRODUCT_ID | |
|| A^ = Visa Classic/Traditional AX = American Express |  |
|| • B^ = Visa Gold/Plat/Trad Rewards C^ = Visa Signature |  |
|| • D^ = Visa Infinite/Signature Pref DI = Discover |  |
|| • DN = Diners |  |
|| • E^ = Proprietary ATM F^ = Visa Classic |  |
|| • G^ = Visa Business |  |
|| • G1 = Visa Signature Business |  |
|| • G3 = Visa Business Enhanced (U.S. Only) |  |
|| • I^ = Visa Infinite |  |
|| • I1 = Visa Infinite Privilege |  |
|| • I2 = Ultra High Net Worth |  |
|| • JC = JCB |  |
|| • J3 = Visa Healthcare |  |
|| • K^ = Visa Corporate T&E |  |
|| • K1 = Visa GSA Corporate T&E |  |
|| • L^ = Electron |  |
|| • M^ = MasterCard/Euro Card and Diners |  |
|| • N^ = Visa Platinum |  |
|| • N1 = Visa Rewards |  |
|| • N2 = Visa Select |  |
|| • O^ = Reserved |  |
|| • P^ = Visa Gold |  |
|| • Q^ = Private Label |  |
|| • Q2 = Private Level Basic |  |
|| • Q3 = Private Level Standard |  |
|| • Q4 = Private Level Enhanced |  |
|| • Q5 = Private Label Specialized |  |
|| • Q6 = Private Level Premium |  |
|| • R^ = Proprietary |  |
|| • S^ = Visa Purchasing |  |
|| • S1 = Visa Purchasing with Fleet |  |
|| • S2 = Visa GSA Purchasing |  |
|| • S3 = Visa GSA Purchasing with Fleet |  |
|| • S4 = Commercial Agriculture |  |
|| • S5 = Commercial Transport EBT |  |
|| • S6 = Commercial Marketplace |  |
|| • U^ = Visa Travel Money |  |
|| • V^ = V Pay |  |
|| • Spaces = Unspecified |  |
| PRODUCT_TYPE_EXT | Null, no longer used |
| ISSUER_COUNTRY | Country of Issuer BIN |
| FUNDING_SOURCE | • C = Credit • D = Debit • P = Prepaid • H = Charge • R = Deferred Debit |

## Mastercard ARDEF Request and Response Sample

### Request

```bash
curl https://www.merchante-solutions.com/jsp/reports/report_api.jsp \
  -d "userId=xxxxxx"
  -d "userPass=xxxxxx"
  -d "reportDateBegin=04%2F22%2F2012"
  -d "reportDateEnd=04%2F23%2F2012"
  -d "reportType=0"
  -d "queryId=284"
  -d "dsReportId=9"

```

### Response

```csv
"EFFECTIVE_DATE","RANGE_LOW","RANGE_HIGH","ISSUER_COUNTRY","CARD_PROG
RAM_ID","GCMS_PRODUCT_ID","LICENSED_PRODUCT_ID","PRODUCT_TYPE_ID","AC
COUNT_LEVEL_IND","ACCOUNT_LEVEL_DATE"
04/11/2012,"0","0","SGP","MCC","MCC","MCC","1","N",11/30/1999
04/11/2012,"0","0","SGP","MCC","MCC","MCC","1","N",11/30/1999
04/11/2012,"0","0","SGP","MCC","MCC","MCC","1","N",11/30/1999
04/11/2012,"0","0","HKG","MCC","MCC","MCC","1","N",11/30/1999
04/11/2012,"3600000000000000000","3612339999999999999","CHL","MCC","MCC","MCC","1","N",11/30/1999
04/11/2012,"3612340000000000000","3612349999999999999","CHL","MCC","MCC","MCC","1","N",11/30/1999
04/11/2012,"3612350000000000000","3613329999999999999","CHL","MCC","MCC","MCC","1","N",11/30/1999
04/11/2012,"3613330000000000000","3613339999999999999","CHL","MCC","MCC","MCC","1","N",11/30/1999
04/11/2012,"3613340000000000000","3613349999999999999","CHL","MCC","MCC","MCC","1","N",11/30/1999
04/11/2012,"3613350000000000000","3613359999999999999","CHL","MCC","MCC","MCC","1","N",11/30/1999
04/11/2012,"3613360000000000000","3613369999999999999","CHL","MCC","MCC","MCC","1","N",11/30/1999

```

### Field Descriptions

| **Field Name** | **Description** |
| ---------- | ----------- |
| EFFECTIVE_DATE | Date and time that the information in this record becomes effective or was last updated |
| RANGE_LOW | The low range ARDEF value, for example 5101200000. |
| RANGE_HIGH | The high range ARDEF value, for example 510129999. |
| ISSUER_COUNTRY | Country of Issuer BIN MasterCard are 3 digit codes |
| CARD_PROGRAM_ID | Three-character code that identifies the card program or financial network to which a transaction belongs. Valid values: • CIR - Cirrus® • DMC - Debit MasterCard® • MCC - MasterCard® Credit • MSI - Maestro® • PRO - Sweden domestic brand • PVL - Private label • SOL - U.K. domestic Solo brand (Pre-edit system only) • SWI - U.K. domestic Switch brand (Pre-edit system only) • Spaces - Members must submit all spaces in this subfield unless specifically directed otherwise by MasterCard. |
| GCMS_PRODUCT_ID | Identifies the product recognized by the GCMS for the combination of issuer account rante and Card Program should be used when |
| LICENSED_PRODUCT_ID | Identifies the actual product code assigned by MasterCard when licensing the combination of issuer account range and Card Program Identifier. This field should be used a primary means to |
| PRODUCT_TYPE_ID | The product type of the associated account range and card program identifier. Valid Values: • 1 = Consumerl • 2 = Commercial • 3 = Both |
| ACCOUNT_LEVEL_IND | • N = No cardholder accounts within range are participants in Account Level Processing or Co-Brand Proprietary Card Program. • Y = Some cardholder accounts within range are participants in Account Level Processing or Co-Brand Proprietary Card Program. |
| ACCOUNT_LEVEL_DATE | The account level activation date (yymmdd) |

## Discover ARDEF Field Values

### Field Descriptions

| **Field Name** | **Description** |
| ---------- | ----------- |
| EFFECTIVE_DATE | Date and time that the information in this record becomes effective or was last updated |
| PREFIX | IIN (Prefix) Number |
| PREFIX_LENGTH | IIN Prefix Length (00 through 19) |
| LENGTH_MIN | Minimum Pan Length (00 through 19) |
| LENGTH_MAX | Maximum Pan Length (00 through 19) |
| ISSUER_COUNTRY | This is the ISO country code value |
| CARD_PRODUCT_ID | • 001 = Consumer Credit-Rewards • 002 = Commercial Credit • 003 = Consumer Debit • 004 = Commercial Debit • 005 = Prepaid Gift • 006 = Prepaid ID Known • 007 = Consumer Credit - Premium • 008 = Consumer Credit – Core • 009 = Private Label Credit Card • 010 = Commercial Credit - Executive Business • 011 = Consumer Credit - Premium Plus • 012 = Commercial Prepaid – Non-Reloadable • 013 = PayPal Payment Card • 014 = PayPal Mobile In-Store Payment: Payment Code |

| FOREIGN_CARD_IND | Indicates country of issuance • 0 = U.S • 1 = Outside of the U.S. |
| ACCOUNT_LEVEL_IND | Indicates if this IIN ranges have account level processing enabled: • 0 = No • 1 = Yes |

## Mastercard GCMS and Licensed Product ID Field Values

### Identifier Descriptions

| **GCMS Product Identifier** | **Licensed Product Identifier** | **Licensed Product Identifier Description** |
| ----------------------- | ------------------------- | ---------------------------------------- |
| CIR | CIR | Cirrus |
| DLG | DLG | Debit MasterCard Gold—Delayed Debit |
| DLH | DLH | Debit MasterCard World Embossed—Delayed Debit |
| DLS | DLI | Debit MasterCard Standard ISIC Student Card— Delayed Debit |
| DLP | DLP | Debit MasterCard Platinum—Delayed Debit |
| DLS | DLS | Debit MasterCard Card—Delayed Debit |
| DLS | DLU | Debit MasterCard Unembossed—Delayed Debit |
| MAB | MAB | World Elite MasterCard for Business Card |
| MAC | MAC | MasterCard World Elite Corporate Card |
| MBD | MBD | MasterCard Professional Debit Business Card |
| MBE | MBE | MasterCard Electronic Business Card |
| MBE | MRB | MasterCard Prepaid Electronic Business Card (Non-U.S.) |
| MBK | MBK | MasterCard Black Card |
| MCB | MBD | MasterCard Professional Debit BusinessCard Card |
| MCB | MCB | MasterCard BusinessCard Card |
| MCB | MDB | Debit MasterCard BusinessCard Card |
| MCB | MEB | MasterCard Executive BusinessCard Card |
| MCB | MEO | MasterCard Corporate Executive Card |
| MCB | MLC | MasterCard Micro-Business Card |
| MCB | MLD | MasterCard Distribution Card |
| MCB | MPC | MasterCard Professional Card |
| MCB | MPW | Debit MasterCard Business Card Prepaid Workplace Business to Business |
| MCB | MXB | Debit Card X Code |
| MCC | MCC | MasterCard Credit Card (mixed BIN) |
| MCC | MHC | MasterCard Healthcare Credit Non- Substantiated |
| MCD | MCD | Debit MasterCard Card |
| MCE | MCA | MasterCard Electronic Card |
| MCE | MCE | MasterCard Electronic Card |
| MCE | MED | Debit MasterCard Electronic Card (Non-U.S.) |
| MCE | MIB | MasterCard Electronic Student Card |
| MCE | MIH | Debit MasterCard Electronic Student Card (Non- U.S.) |
| MCE | MRL | Prepaid MasterCard Electronic Commercial Card (Non-U.S.) |
| MCF | MCF | MasterCard Fleet Card |
| MCF | MDM | Middle Market Fleet Card |
| MCG | MCG | Gold MasterCard Card |
| MCO | MCM | MasterCard Corporate Meeting Card |
| MCO | MCO | MasterCard Corporate Card |
| MCO | MDQ | Middle Market Corporate Card |
| MCO | MLA | MasterCard Central Travel Solutions Air Card |
| MCO | MLL | MasterCard Central Travel Solutions Land Card |
| MCP | MCP | MasterCard Purchasing Card |
| MCP | MDN | Middle Market Purchasing Card |
| MCS | MCS | Standard MasterCard Card |
| MCS | MCU | Unembossed MasterCard Card |
| MCS | MIC | Standard MasterCard Student Card |
| MCS | MIG | Unembossed MasterCard Student Card |
| MCS | MIJ | Debit MasterCard Unembossed Non-U.S. Student Card |
| MCS | MIU | Debit MasterCard Unembossed (Non-U.S.) |
| MCS | MSD | MasterCard Deferred Debit Consumer |
| MCT | MCT | Titanium MasterCard Card |
| MCV | MCV | Merchant-Branded Program |
| MCW | MCW | World MasterCard Card |
| MDB | MDB | Debit MasterCard BusinessCard Card |
| MDG | MDG | Gold Debit MasterCard Card |
| MDG | MXG | Gold Debit MasterCard Card |
| MDH | MDH | World Debit Embossed MasterCard Card |
| MDH | MDI | World Debit MasterCard Card |
| MDJ | MDJ | Debit Other 2 Embossed |
| MDJ | MDK | Debit Other 2 Unembossed |
| MDL | MDL | Business Debit Other Embossed |
| MDO | MDO | Debit Other |
| MDO | MXO | Debit Card Other |
| MDP | MDP | Platinum Debit MasterCard Card |
| MDP | MEP | Premium Debit MasterCard Card |
| MDP | MUP | Premium Debit Unembossed MasterCard Card |
| MDP | MXP | Platinum Debit MasterCard Card |
| MDR | MDR | Debit Brokerage |
| MDR | MXR | Debit Card Brokerage |
| MDS | MDS | Debit MasterCard Card |
| MDS | MDU | Debit Unembossed MasterCard Card |
| MDS | MHA | MasterCard Healthcare Prepaid Non-tax |
| MDS | MHB | MasterCard HSA Substantiated |
| MDS | MHH | MasterCard HSA Non-Substantiated |
| MDS | MID | Debit Unembossed MasterCard Student Card |
| MDS | MIS | Debit MasterCard Standard Student Card |
| MDS | MPA | MasterCard Prepaid Debit Standard—Payroll |
| MDS | MPF | MasterCard Prepaid Debit Standard—Gift |
| MDS | MPM | MasterCard Prepaid Debit Standard—Consumer Incentive |
| MDS | MPN | MasterCard Prepaid Debit Standard—Insurance |
| MDS | MPO | MasterCard Prepaid Debit Standard—Other |
| MDS | MPQ | MasterCard Prepaid Debit Standard— Government Disaster Relief |
| MDS | MPR | MasterCard Prepaid Debit Standard—Travel |
| MDS | MPT | MasterCard Prepaid Debit Standard—Teen |
| MDS | MPV | MasterCard Prepaid Debit Standard— Government |
| MDS | MPX | MasterCard Prepaid Debit Standard—Flex Benefit |
| MDS | MPY | MasterCard Prepaid Debit Standard—Employee Incentive |
| MDS | MPZ | MasterCard Prepaid Debit Standard— Government Consumer |
| MDS | MXS | Standard Debit MasterCard Card |
| MDT | MDT | Commercial Debit MasterCard Card |
| MEC | MEC | MasterCard Electronic Commercial |
| MEF | MEF | MasterCard Electronic Payment Account |
| MFB | MFB | Flex World Elite |
| MFD | MFD | Flex Platinum |
| MFE | MFE | Flex Charge World Elite |
| MFH | MFH | Flex World |
| MFL | MFL | Flex Charge Platinum |
| MFW | MFW | Flex Charge World |
| MGF | MGF | MasterCard Government Commercial Card |
| MHA | MHA | MasterCard Healthcare Prepaid Non-Tax |
| MIA | MIA | Prepaid MasterCard Unembossed Student Card |
| MIP | MIP | Prepaid Debit MasterCard Student Card |
| MIU | MIU | Debit MasterCard Unembossed (Non-U.S.) |
| MNF | MNF | MasterCard Public Sector Commercial Card |
| MNW | MNW | MasterCard World Card |
| MOG | MOG | Maestro Gold Card |
| MOP | MOP | Maestro Platinum |
| MOW | MOW | World Maestro |
| MPA | MPA | MasterCard Prepaid Debit Standard-Payroll |
| MPB | MPB | MasterCard Preferred Business Card |
| MPF | MPF | MasterCard Prepaid Debit Standart-Gift |
| MPG | MIA | Prepaid MasterCard Unembossed Student Card |
| MPG | MPG | Debit MasterCard Standard Prepaid—General Spend |
| MPG | MUS | Prepaid Unembossed MasterCard Card |
| MPH | MPH | MasterCard Cash |
| MPJ | MPJ | Prepaid Debit MasterCard Card Gold |
| MPL | MCT | Titanium MasterCard Card |
| MPL | MPL | Platinum MasterCard Card |
| MPK | MPK | MasterCard Prepaid Government Commercial Card |
| MPM | MPM | MasterCard Prepaid Debt Standard-Consumer Incentive |
| MPN | MPN | MasterCard Prepaid Debit Standard-Insurance |
| MPO | MPO | MasterCard Prepaid Debit Standard-Other |
| MPP | MFR | MasterCard Commercial Reward Funding |
| MPP | MIP | Prepaid Debit MasterCard Student Card |
| MPP | MPP | MasterCard Prepaid Card |
| MPR | MPR | MasterCard Prepaid Debit Standard-Travel |
| MPT | MPT | MasterCard Prepaid Debit Standard-Teen |
| MPV | MPV | MasterCard Prepaid Debit Standard- Government |
| MPW | MPW | Debit MasterCard BusinessCard Prepaid Workplace Business to Business |
| MPX | MPX | MasterCard Prepaid Debit Standard-Flex Benefit |
| MPY | MPY | MasterCard Prepaid Debit Standard-Employee Incentive |
| MPZ | MPZ | MasterCard Prepaid Debit-Government Consumer |
| MRC | MIK | Prepaid MasterCard Electronic Student Card (Non-U.S.) |
| MRC | MRC | Prepaid MasterCard Electronic Card (Non-U.S.) |
| MRG | MIL | Prepaid Unembossed MasterCard Student Card(Non-U.S.) |
| MRG | MRG | MasterCard Prepaid Card (Non-U.S.) |
| MRG | MRS | Prepaid MasterCard ISIC Student Card |
| MRJ | MRJ | Prepaid MasterCard Gold Card |
| MRK | MRK | Prepaid MasterCard Public Sector Commercial Card |
| MRO | MRO | MasterCard Rewards Only |
| MRW | MRW | Prepaid MasterCard Business Card (Non-U.S.) |
| MSA | MSA | Prepaid Maestro Payroll Card |
| MSB | MSB | Maestro Small Business Card |
| MSF | MSF | Prepaid Maestro Gift Card |
| MSG | MSG | Prepaid Maestro Consumer Reloadable Card |
| MSI | MSB | Maestro Small Business Card |
| MSI | MSI | Maestro Card |
| MSI | MSS | Maestro Student Card |
| MSJ | MSJ | Prepaid Maestro Gold |
| MSM | MSM | Maestro Prepaid Consumer Promotion Card |
| MSN | MSN | Maestro Prepaid Insurance Card |
| MSO | MSO | Maestro Prepaid Other Card |
| MSQ | MSQ | Reserved for Future Use |
| MSR | MSR | Prepaid Maestro Travel Card |
| MST | MST | Prepaid Maestro Teen Card |
| MSV | MSV | Prepaid Maestro Government Benefit Card |
| MSW | MSW | Prepaid Maestro Corporate Card |
| MSX | MSX | Prepaid Maestro Flex Benefit Card |
| MSY | MSY | Prepaid Maestro Employee Incentive Card |
| MSZ | MSZ | Prepaid Maestro Emergency Assistance Card |
| MUW | MUW | MasterCard World Domestic Affluent |
| MWB | MWB | World MasterCard for Business Card |
| MWE | MWE | World Elite MasterCard Card |
| MWO | MWO | World Elite MasterCard Corporate Card |
| OLB | OLB | Maestro Small Business—Delayed Debit |
| OLG | OLG | Maestro Gold—Delayed Debit |
| OLP | OLP | Maestro Platinum—Delayed Debit |
| OLS | OLI | ISIC Maestro Student Card—Delayed Debit |
| OLS | OLS | Maestro—Delayed Debit |
| OLW | OLW | World Maestro Delayed Debit |
| PMC | PMC | Proprietary Credit Card (Sweden Domestic) |
| PMD | PMD | Proprietary Debit Card (Sweden Domestic) |
| PSC | PSC | Common Proprietary Swedish Credit Card |
| PSD | PSD | Common Proprietary Swedish Debit Card |
| PVA | PVA | Private Label A |
| PVB | PVB | Private Label B |
| PVC | PVC | Private Label C |
| PVD | PVD | Private Label D |
| PVE | PVE | Private Label E |
| PVF | PVF | Private Label F |
| PVG | PVG | Private Label G |
| PVF | PVF | Private Label H |
| PVH | PVH | Private Label I |
| PVI | PVI | Private Label J |
| PVJ | PVJ | Private Label L |
| PVL | PVL | Private Labe |
| SOL | SOL | UK Domestic Solo brand |
| SWI | SWI | UK Domestic Switch brand |
| TBE | TBE | MasterCard Electronic Business—Immediate Debit |
| TCB | TCB | MasterCard Business Card—Immediate Debit |
| TCC | TCC | MasterCard (mixed BIN)—Immediate Debit |
| TCE | TCE | MasterCard Electronic—Immediate Debit |
| TCF | TCF | MasterCard Fleet Card—Immediate Debit |
| TCG | TCG | Gold MasterCard Card—Immediate Debit |
| TCO | TCO | MasterCard Corporate—Immediate Debit |
| TCP | TCP | MasterCard Purchasing Card—Immediate Debit |
| TCS | TCS | MasterCard Standard Card—Immediate Debit |
| TCW | TCW | World Signia MasterCard Card—Immediate Debit |
| TCP | TDN | Middle Market MasterCard Purchasing Card— Immediate Debit |
| TCB | TEB | MasterCard Executive BusinessCard Card—Immediate Debit |
| TEC | TEC | MasterCard Electronic Commercial—Immediate Debit |
| TCB | TEO | MasterCard Corporate Executive Card— Immediate Debit |
| TCE | TIB | ISIC MasterCard Electronic Student Card—Immediate Debit |
| TCS | TIC | ISIC MasterCard Standard Student Card— Immediate Debit |
| TCS | TIU | MasterCard Unembossed—Immediate Debit |
| TCO | TLA | MasterCard Central Travel Solutions Air— Immediate Debit |
| TNF | TNF | MasterCard Public Sector Commercial Card— Immediate Debit |
| TNW | TNW | MasterCard New World—Immediate Debit |
| TPB | TPB | MasterCard Preferred Business Card— Immediate Debit |
| TCB | TPC | MasterCard Professional Card—Immediate Debit |
| TPL | TPL | Platinum MasterCard—Immediate Debit |

## Country Codes for Visa and Mastercard

### Country Codes

| **Country** | **Numeric Country Code** | **MC Alpha** | **Visa Alpha** |
| ------- | ------------------- | -------- | ---------- |
| Afghanistan | 004 | AFG | AF |
| Albania | 008 | ALB | AL |
| Algeria | 012 | DZA | DZ |
| American Samoa | 016 | ASM | AS |
| Andorra | 020 | AND | AD |
| Angola | 024 | AGO | AO |
| Anguilla | 660 | AIA | AI |
| Antarctica | 010 | ATA | AQ |
| Antigua and Barbuda | 028 | ATG | AG |
| Argentina | 032 | ARG | AR |
| Aruba | 533 | ABW | AW |
| Australia | 036 | AUS | AU |
| Austria | 040 | AUT | AT |
| Azerbaijan | 031 | AZE | AZ |
| Bahamas | 044 | BHS | BS |
| Bahrain | 048 | BHR | BH |
| Bangladesh | 050 | BGD | BD |
| Barbados | 052 | BRB | BB |
| Belarus | 112 | BLR | BY |
| Belgium | 056 | BEL | BE |
| Belize | 084 | BLZ | BZ |
| Bermuda | 060 | BMU | BM |
| Bhutan | 064 | BTN | BT |
| Bolivia, Plurinational State of | 068 | BOL | BO |
| Bosnia and Herzegovina | 070 | BIH | BA |
| Botswana | 072 | BWA | BW |
| Bouvet Island | 074 | BVT | BV |
| Brazil | 076 | BRA | BR |
| British Indian Ocean Territory (BIOT) | 086 | IOT | IO |
| Brunei Darussalam | 096 | BRN | BN |
| Bulgaria | 100 | BGR | BG |
| Burkina Faso | 854 | BFA | BF |
| Burundi | 108 | BDI | BI |
| Cambodia | 116 | KHM | KH |
| Cameroon | 120 | CMR | CM |
| Canada | 124 | CAN | CA |
| Cape Verde | 132 | CPV | CV |
| Cayman Islands | 136 | CYM | KY |
| Central African Republic | 140 | CAF | CF |
| Chad | 148 | TCD | TD |
| Chile | 152 | CHL | CL |
| China | 156 | CHN | CN |
| Christmas Island | 162 | CXR | CX |
| Cocos (Keeling) Islands | 166 | CCK | CC |
| Colombia | 170 | COL | CO |
| Comoros | 174 | COM | KM |
| Congo | 178 | COG | CG |
| Cook Islands | 184 | COK | CK |
| Costa Rica | 188 | CRI | CR |
| Croatia | 191 | HRV | HR |
| Cyprus | 196 | CYP | CY |
| Czech Republic | 203 | CZE | CZ |
| Democratic Republic of the Congo | 180 | ZAR | CD |
| Denmark | 208 | DNK | DK |
| Djibouti | 262 | DJI | DJ |
| Dominica | 212 | DMA | DM |
| Dominican Republic | 214 | DOM | DO |
| Ecuador | 218 | ECU | EC |
| Egypt | 818 | EGY | EG |
| El Salvador | 222 | SLV | SV |
| Equatorial Guinea | 226 | GNQ | GQ |
| Eritrea | 232 | ERI | ER |
| Estonia | 233 | EST | EE |
| Ethiopia | 230 | ETH | ET |
| Falkland Islands (Malvinas) | 238 | FLK | FK |
| Faroe Islands | 234 | FRO | FO |
| Fiji | 242 | FJI | FJ |
| Finland | 246 | FIN | FI |
| France | 250 | FRA | FR |
| French Guiana | 254 | GUF | GF |
| French Polynesia | 258 | PYF | PF |
| French Southern Territories | 260 | ATF | TF |
| Gabon | 266 | GAB | GA |
| Gambia | 270 | GMB | GM |
| Georgia | 268 | GEO | GE |
| Germany,Republic of | 280 | DEU | DE |
| Ghana | 288 | GHA | GH |
| Gibraltar | 292 | GIB | GI |
| Greece | 300 | GRC | GR |
| Greenland | 304 | GRL | GL |
| Grenada | 308 | GRD | GD |
| Guadeloupe | 312 | GLP | GP |
| Guam | 316 | GUM | GU |
| Guatemala | 320 | GTM | GT |
| Guinea-Bissau | 624 | GNB | GW |
| Guyana | 328 | GUY | GY |
| Heard and McDonald Islands | 334 | HMD | HM |
| Honduras | 340 | HND | HN |
| Hong Kong | 344 | HKG | HK |
| Hungary | 348 | HUN | HU |
| Iceland | 352 | ISL | IS |
| India | 356 | IND | IN |
| Indonesia | 360 | IDN | ID |
| Iraq | 368 | IRQ | IQ |
| Ireland | 372 | IRL | IE |
| Israel | 376 | ISR | IL |
| Italy | 380 | ITA | IT |
| Jamaica | 388 | JAM | JM |
| Japan | 392 | JPN | JP |
| Johnston Island | 396 | JTN | JT |
| Jordan | 400 | JOR | JO |
| Kazakhstan | 398 | KAZ | KZ |
| Kenya | 404 | KEN | KE |
| Kiribati | 296 | KIR | KI |
| Korea, Republic of | 410 | KOR | KR |
| Kosovo, United Nations Mission in Kosovo (UNMIK) | 900 | QZZ | QZ |
| Kuwait | 414 | KWT | KW |
| Kyrgyzstan | 417 | KGZ | KG |
| Lao People’s Democratic Republic | 418 | LAO | LA |
| Latvia | 428 | LVA | LV |
| Lebanon | 422 | LBN | LB |
| Lesotho | 426 | LSO | LS |
| Liberia | 430 | LBR | LR |
| Libyan Arab Jamahiriya | 434 | LBY | LY |
| Liechtenstein | 438 | LIE | LI |
| Lithuania | 440 | LTU | LT |
| Luxembourg | 442 | LUX | LU |
| Macau | 446 | MAC | MO |
| Macedonia | 807 | MKD | MK |
| Madagascar | 450 | MDG | MG |
| Malawi | 454 | MWI | MW |
| Malaysia | 458 | MYS | MY |
| Maldives | 462 | MDV | MV |
| Mali | 466 | MLI | ML |
| Malta | 470 | MLT | MT |
| Marshall Islands | 584 | MHL | MH |
| Martinique | 474 | MTQ | MQ |
| Mauritania | 478 | MRT | MR |
| Mauritius | 480 | MUS | MU |
| Mexico | 484 | MEX | MX |
| Micronesia | 583 | FSM | FM |
| Midway Islands | 488 | MID | MI |
| Moldova | 498 | MDA | MD |
| Monaco | 492 | MCO | MC |
| Mongolia | 496 | MNG | MN |
| Montenegro | 499 | MNE | ME |
| Montserrat | 500 | MSR | MS |
| Morocco | 504 | MAR | MA |
| Mozambique | 508 | MOZ | MZ |
| Namibia | 516 | NAM | NA |
| Nauru | 520 | NRU | NR |
| Nepal | 524 | NPL | NP |
| Netherlands | 528 | NLD | NL |
| Netherlands Antilles | 530 | ANT | AN |
| New Caledonia | 540 | NCL | NC |
| New Zealand | 554 | NZL | NZ |
| Nicaragua | 558 | NIC | NI |
| Niger | 562 | NER | NE |
| Niue | 570 | NIU | NU |
| Norfolk Island | 574 | NFK | NF |
| Northern Mariana Islands | 580 | MNP | MP |
| Norway | 578 | NOR | NO |
| Oman | 512 | OMN | OM |
| Pakistan | 586 | PAK | PK |
| Palau | 585 | PLW | PW |
| Palestine | 275 | PSE | PS |
| Panama | 591 | PAN | PA |
| Papua New Guinea | 598 | PNG | PG |
| Paraguay | 600 | PRY | PY |
| Peru | 604 | PER | PE |
| Philippines | 608 | PHL | PH |
| Pitcairn | 612 | PCN | PN |
| Poland | 616 | POL | PL |
| Portugal | 620 | PRT | PT |
| Puerto Rico | 630 | PRI | PR |
| Qatar | 634 | QAT | QA |
| Reunion | 638 | REU | RE |
| Romania | 642 | ROM | RO |
| Russian Federation | 643 | RUS | RU |
| Rwanda | 646 | RWA | RW |
| Samoa | 882 | WSM | WS |
| San Marino | 674 | SMR | SM |
| Sao Tome and Principe | 678 | STP | ST |
| Saudi Arabia | 682 | SAU | SA |
| Senegal | 686 | SEN | SN |
| Serbia, Republic of | 688 | SRB | RS |
| Seychelles | 690 | SYC | SC |
| Sierra Leone | 694 | SLE | SL |
| Singapore | 702 | SGP | SG |
| Slovak Republic | 703 | SVK | SK |
| Slovenia | 705 | SVN | SI |
| Solomon Islands | 090 | SLB | SB |
| Somalia | 706 | SOM | SO |
| South Africa | 710 | ZAF | ZA |
| Spain | 724 | ESP | ES |
| Sri Lanka | 144 | LKA | LK |
| St Helena | 654 | SHN | SH |
| St. Kitts-Nevis | 659 | KNA | KN |
| St. Lucia | 662 | LCA | LC |
| St. Pierre and Miquelon | 666 | SPM | PM |
| St. Vincent and the Grenadines | 670 | VCT | VC |
| Suriname | 740 | SUR | SR |
| Svalbard and Jan Mayen | 744 | SJM | SJ |
| Swaziland | 748 | SWZ | SZ |
| Sweden | 752 | SWE | SE |
| Switzerland | 756 | CHE | CH |
| Syrian Arab Republic | 760 | SYR | SY |
| Taiwan | 158 | TWN | TW |
| Tajikistan | 762 | TJK | TJ |
| Tanzania, United Republic of | 834 | TZA | TZ |
| Thailand | 764 | THA | TH |
| Timor-Leste | 626 | TMP | TP |
| Togo | 768 | TGO | TG |
| Tokelau | 772 | TKL | TK |
| Tonga | 776 | TON | TO |
| Trinidad and Tobago | 780 | TTO | TT |
| Tunisia | 788 | TUN | TN |
| Turkey | 792 | TUR | TR |
| Turkmenistan | 795 | TKM | TM |
| Turks and Caicos Islands | 796 | TCA | TC |
| Tuvalu | 798 | TUV | TV |
| U.S. Minor Outlying Islands | 581 | UMI | UM |
| Uganda | 800 | UGA | UG |
| Ukraine | 804 | UKR | UA |
| United Arab Emirates | 784 | ARE | AE |
| United Kingdom | 826 | GBR | GB |
| United States | 840 | USA | US |
| Uruguay | 858 | URY | UY |
| Uzbekistan | 860 | UZB | UZ |
| Vanuatu | 548 | VUT | VU |
| Holy See (Vatican City State) | 336 | VAT | VA |
| Venezuela, Bolivarian Republic of | 862 | VEN | VE |
| Vietnam | 704 | VNM | VN |
| Virgin Islands, British | 092 | VGB | VG |
| Virgin Islands,U.S | 850 | VIR | VI |
| Wake Island | 872 | WAK | WK |
| Wallis and Futuna | 876 | WLF | WF |
| Western Sahara | 732 | ESH | EH |
| Yemen | 887 | YEM | YE |
| Zambia | 894 | ZMB | ZM |
| Zimbabwe | 716 | ZWE | ZW |

## Chargeback and Retrieval Reason Codes

### Chargeback Descriptions

| **Description** | **Visa** | **MC** | **Discover** | **Amex** |
| ----------- | ---- | ---- | -------- | ---- |
| Services Not Provided or Merchandise Not Received | 30 | 4855 4869 4859 | RG | R01 |
| Cancelled Recurring Transaction | 41 | 4841 | AP |  |
| Not as Described or Defective Merchandise | 53 | 4853 | RM |  |
| Fraudulent Multiple Transactions | 57 | 4840 | UA01 UA02 UA03 UANR | FR2 R03 |
| Illegible Fulfillment | 60 | 4802 | IC |  |
| Counterfeit Transaction | 62 | 4862 |  |  |
| Card Recovery Bulletin or Exception File | 70 | 4807 |  |  |
| Declined Authorization | 71 | 4808 | DA |  |
| No Authorization | 72 | 4808 4847 |  | A08 |
| Expired Card | 73 | 4835 | EX |  |
| Late Presentment | 74 | 4842 | LP |  |
| Transaction Not Recognized | 75 | 4863 | UA |  |
| Incorrect Transaction Code or Incorrect Currency Code or Domestic Transaction Processing Violation | 76 | 4846 | AW |  |
| Non-matching Account Number | 77 |  |  |  |
| Service Code Violation | 78 |  |  |  |
| Incorrect Transaction Amount or Incorrect Account Number | 80 | 4831 |  |  |
| Fraud – Card-present Environment | 81 | 4862 |  |  |
| Duplicate Processing | 82 | 4834 | DP | P08 |
| Fraud – Card-absent Environment | 83 | 4857 |  |  |
| Credit Not Processed | 85 | 4860 | RN |  |
| Paid By Other Means | 86 |  |  | P08 |
| Non-receipt of Cash or Load Transaction Value at ATM or Load Device | 90 |  |  |  |
| Merchant Fraud Performance Program | 93 | 4849 |  |  |
| Transaction Exceeds Limited Amount | 96 |  |  | A08 |
| Requested Data Transaction Not Received |  | 4801 | RI | R14 |
| Warning Bulletin File |  | 4807 |  |  |
| Account Number Not on File |  | 4812 |  |  |
| Credit Posted as a Purchase |  | 4850 | CD |  |
| Cardholder Dispute-Not Elsewhere Classified |  | 4854 |  |  |
| The full contents of magnetic stripe were not included in the authorization. |  |  | CV |  |
| Split Sale |  |  | SS |  |
| Service Establishment Adjustment |  |  | TF |  |
| Altered Amount |  |  | AW |  |
| Transaction Exceeds Floor Limit |  |  | EF |  |
| Missing Signature |  |  | IS |  |
| No Imprint on Sales Slip |  |  | SI |  |
| Debit Adjustment |  |  |  | P17 |
| Merchant Accepted Chargeback |  |  |  | M01 M04 |
| Non Dispute Adjustment |  |  |  | R17 S01 R21 |

### Retrieval Descriptions

| **Description** | **Visa** | **MC** | **Discover** | **Amex** |
| ----------- | ---- | ---- | -------- | ---- |
| Cardholder Request Copy Bearing Signature | 28 | 01 02 | A | R12 |
| Request for T&E Documents | 29 |  | AL |  |
| Cardholder Dispute, Draft Requested | 30 | 03 |  |  |
| Fraud Analysis | 33 | 6341 | 04 |  |
| Legal Process Request | 34 |  |  |  |
| Does Not Agree with Amount Billed |  | 6305 | AW |  |
| Cardholder Does Not Recognize | 32 | 6321 |  | 33 127 176 |
| Chip Transaction Request |  | 6322 |  |  |
| Personal Records Request |  | 6323 |  |  |
| Potential Chargeback/Compliance |  | 6342 |  |  |
| Recurring Payment |  |  | AP |  |
| Cash Advance Dispute |  |  | CA |  |
| Credit Posted as a Card Sale |  |  | CD |  |
| Cancelled Reservation |  |  | CR | 20 21 40 41 43 154 701 |
| Duplicate Processing |  |  | DP | 173 658 |
| Not Classified |  |  | NC |  |
| Non-Receipt of Goods or Services |  |  | RG | 190 |
| Cardholder Disputes Quality of Goods or Services |  |  | RM | 16 63 |
| Fraud: Request Trans Receipt for Swiped Card Transaction |  |  | U10 | 193 |
| Fraud: Request Trans Receipt for Keyed Card Transaction |  |  | U20 | 193 |
| Fraud: Request Trans Receipt for Card-NotPresent\ Transaction |  |  | U30 | 175 |
| Credit Not Processed |  |  | RN2 |  |
| Good Faith Investigation |  |  | 05 |  |

### VCR Chargeback Descriptions

| **Description** | **Visa** | **MC** | **Discover** | **Amex** |
| ----------- | ---- | ---- | -------- | ---- |
| EMV Liability Shift Counterfeit Fraud | 10.1 |  |  |  |
| EMV Liability Shift – Non-Counterfeit Fraud | 10.2 |  |  |  |
| Fraud – Card-present Environment | 10.3 |  |  |  |
| Fraud – Card-absent Environment | 10.4 |  |  |  |
| Visa Fraud Monitoring Program | 10.5 |  |  |  |
| Card Recovery Bulletin | 11.1 |  |  |  |
| Declined Authorization | 11.2 |  |  |  |
| No Authorization | 11.3 |  |  |  |
| Late Presentment | 12.1 |  |  |  |
| Incorrect Transaction Code | 12.2 |  |  |  |
| Incorrect Currency Code | 12.3 |  |  |  |
| Incorrect Account Number | 12.4 |  |  |  |
| Incorrect Transaction Amount | 12.5 |  |  |  |
| Duplicate Processing/Paid By Other Means | 12.6 |  |  |  |
| Invalid Data | 12.7 |  |  |  |
| Merchandise/Services Not Received | 13.1 |  |  |  |
| Cancelled Recurring Transaction | 13.2 |  |  |  |
| Not as Described or Defective Merchandise/Services | 13.3 |  |  |  |
| Counterfeit Merchandise | 13.4 |  |  |  |
| Misrepresentation | 13.5 |  |  |  |
| Credit Not Processed | 13.6 |  |  |  |
| Cancelled Merchandise/Services | 13.7 |  |  |  |
| Original Credit Transaction Not Accepted | 13.8 |  |  |  |
| Non-receipt of Cash | 13.9 |  |  |  |

## Appendix A: Required and Optional Fields for Report Requests

Legend:
R - Required field  
O - Optional field

| **Report Name** | **userId** | **userPass** | **dsReportId** | **beginDate** | **endDate** | **nodeId** | **reportType** | **encoding** | **includeTridentTranId** | **includePurchaseId** | **xmlEncoding** | **queryId** | **profileId** | **batchDateFrom** | **batchDateTo** |
|------------|--------|----------|------------|-----------|---------|--------|------------|----------|----------------------|------------------|-------------|---------|-----------|----------------|--------------|
| Batch Summary Report | R | R | R | O | O | O | R | O | O | O | O |  |  |  |  |
| Settlement Summary Report | R | R | R | O | O | O | R | O | O | O | O |  |  |  |  |
| Deposit Summary Report | R | R | R | O | O | O | R | O | O | O | O |  |  |  |  |
| Chargeback Adjustments Report | R | R | R | O | O | O | R | O | O | O | O |  |  |  |  |
| Retrieval Request Report | R | R | R | O | O | O | R | O | O | O | O |  |  |  |  |
| Daily Interchange Summary | R | R | R | O | O | O | R | O | O | O | O |  |  |  |  |
| Custom Queries | R | R | R | O | O | O | R | O | O | O | O | R |  |  |  |
| FX Batch Summary | R | R | R | O | O | O | R | O | O | O | O |  |  |  |  |
| Authorization Log | R | R | R | O | O | O | R | O | O | O | O |  |  |  |  |
| Payment Gateway Unsettled Transactions | R | R | R |  |  |  |  |  |  |  |  |  | R |  |  |
| Payment Gateway Settled Transactions | R | R | R |  |  |  |  |  |  |  |  |  | R | R | R |
| Payment Gateway Rejected Transactions | R | R | R |  |  |  |  |  |  |  |  |  | R |  |  |

## Version History with Revisions and Changelog

| **Date** | **Description** |
| ------------- | --------------- |
| October 2026 | Added tran fee amount, aka surcharge fee. |
| July 2026 | Updated source format. |
| July 2026 | Added report type 29 that verifies validity of a bank account before submitting an ACH transaction. |
| March 2026 | Added Split Funding Summary Report with the parameter dsReportId=28 |
| April 2023 | Switched to monthly update. Updated API Specification template. |
| November 2022 | Added Digital Wallet to Settlement Detail, Batch Detail, and Authorization Log Detail reports. |
| May 2022 | Removed all reports related to Payvision. Added KeyBank to copyright statement. |
| December 2021 | Completed rebranding. Updated for consistent formatting. |
| July 2021 | Updated branding. Removed the following legacy ACH product sections and references: ACHP Settlement Summary Report, ACHP Return Summary Report |
| January 2020 | Updated branding. Contact doc team for earlier revisions. |
