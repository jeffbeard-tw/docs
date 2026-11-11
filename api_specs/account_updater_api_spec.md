
# Overview

The MerchantE by Omise (ME) Account Updater Service (AUS) helps merchants who do installment, recurring, or on-demand billing obtain updated credit card expiration dates and/or account numbers. AUS also identifies cards that have been closed with no new number assigned. By programmatically uploading a data file with account information to MerchantE through a simple API, an inquiry will be made to both Visa and MasterCard.

The account information will be updated if:

- The expiration date of the card has been changed.
- The card has been reissued with a new account number.
- The account has been closed.

The updated information will be made available in a response file that is also available through an API call.

In addition to the API method, files can also be uploaded, checked, and retrieved manually through a web-based interface.

By obtaining these updates systematically, a merchant can avoid contacting their customers directly and prevent the need to manually handle rejected transactions.

Merchants that use the ME Payment Gateway Store Card tokenization feature can also benefit from the use of AUS. Instead of sending full card numbers, such merchants would only need to send the stored token Card ID value in the AUS request file. ME will automatically pull the card data for the update check. Any updates are then automatically applied to the token value with no merchant involvement needed. The merchant can then continue to use the same token Card ID that will then represent the updated card data.

This document is intended to offer a basic understanding of the file format and transmission requirements for sending and receiving updater files.

In this document, the term credit card associations are sometimes used to describe Visa, MasterCard, and Discover. These are the only associations participating in the AUS program.

In addition, Visa, MasterCard, and Discover have their own brand names for these products. There may be some paperwork or collateral material where the association-specific product name is used. Keep in mind that the Visa Account Updater (VAU), MasterCard Automatic Billing Updater (ABU), and Discover Network Account Updater (DAU) all fall under the MerchantE Account Updater Service (AUS).

# Certification Process

Merchants must be certified to use AUS with ME. A member of the ME certification team will be assigned to work directly with the merchant. The primary aim of certification is to validate that a request file can be formed accurately and transmitted successfully.

## Certification Flow

The certification process is simple and is comprised of four steps:

1. **Receive Documentation:** ME will provide partners with the following documentation:
   - ME Account Updater API Specifications.
   - ME AUS Test Script with test account numbers.
   - Required enrollment forms and agreements.
1. **Integration Launch Meeting:** A ME certification team member will meet with the partner to:
   - Answer any questions regarding the file format or the API process.
   - Determine that all the necessary paperwork required by the associations has been completed and that enrollment with the associations is complete.
   - Discuss any outstanding timeframes with association enrollment.
   - Confirm and test email alert address.
   - Determine if files will be uploaded under a single or multiple Merchant IDs.
   - Schedule a testing date.
1. **Conduct certification testing:** Submit file to certification simulator and review results file. Transactions in a test file will not be sent to the card associations to obtain actual results. Upon successful completion of this phase, the AUS account will be removed from ```Test``` mode and submission of production files can begin.
1. **Submit First Production File:** Submit the first production file and validate results. This is to determine that no enrollment errors have been returned from the associations.

## Email Addresses

Email alerts are sent out under the following conditions:

- When a response file has been made available for retrieval.
- When a file failure has occurred.

Since both events require immediate attention, it is critical that MerchantE has an email address on file for the person responsible for handling these situations. It is also necessary to validate that emails from ME can be received at that address. During the certification process, a test email will be sent and receipt of email will be verified. It may be necessary to add ME to a list of acceptable senders if SPAM filters are blocking receipt.

All emails will originate from <accountupdater@merchante-solutions.com>.

## Testing

While AUS is setup in **Test** mode, no data will be sent to the card associations for verification or updates. All files sent will be processed by our simulated test environment. This test environment will only process specific test values and return specific responses. Any other value sent in a test request file will not produce a response value.

These test values can also be used with the ME Payment Gateway Card Store function by first storing the value using the gateway API, and then sending the test request file with the returned Card ID value. Any test value that returns a new card number will update the card number associated with that stored Card ID. The Card ID will now represent this new card number and will no longer represent the test value used for the test environment and will no longer produce a response.

**Note:** Multiple tests with Card ID and test values that return a new card number will need a new Card ID created each time.

### Test Request and Response Values

The following table lists test scenarios and expected response values.

| **Card Type** | **Card Number** | **Source** | **Response Code** | **New Card Number** | **New Expiration Date** |
| ---------------------------------------------------------- | ----------------------------------------------------------------------------------------------- | ------------------------------------------- | ----------------------------------------------------- | ----------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| VISA | 4805000000000009 | VAU | CLOSED | | No |
| | | ABU | NEWACCT | 5133272000000046 | Yes |
| VISA | 4805000000000017 | VAU | NEWACCT | 4805000000000058 | Yes |
| VISA | 4805000000000033 | VAU | NEWEXP | 4805000000000033 | Yes |
| VISA | 4805000000000041 | VAU | CLOSED | | No |
| MC | 5133272000000053 | ABU | CLOSED | | No |
| | | VAU | NEWACCT | 4805000000000058 | Yes |
| MC | 5133272000000004 | ABU | NEWACCT | 5133272000000012 | Yes |
| MC | 5133272000000020 | ABU | NEWEXP | 5133272000000020 | Yes |
| MC | 5133272000000038 | ABU | CLOSED | | No |
| DISC | 6011000000000004 | DAU | NEWACCT | 6011000000000012 | Yes |
| DISC | 6011000000000020 | DAU | CLOSED | | No |
| DISC | 6011000000000038 | DAU | NEWEXP | 6011000000000038 | Yes |
| DISC | 6011000000000046 | DAU | CALL | | No |
| DISC | 6011000000000053 | DAU | OVERRIDE | 6011000000000061 | Yes |

### Testing Notes

- In ```Test``` mode, the expiration date submitted in the request file must be in the correct YYMM format but can be any valid YYMM value.
- Expiration dates that are returned in the response file will be generated dynamically by the test system.
- There are two testing values that will return two response values, each from different card associations. This simulates the case where a card is switched from one type to another, such as a Visa becoming a MasterCard.
- When using Card Store ID values, card numbers will be returned truncated to the first 6 and last 4 digits, such as 480500xxxxxx0058.

# Request and Response Formats

This chapter describes both the request and response file layouts.

## Request File Layout

Merchants will use the following message format to create files to send to ME via the API. The file is transmitted using a multi-part form post. The file starts with a header record, is followed by some number of detail records, and is terminated with a trailer record. The header record will identify the merchant and specify a format version. Detail records list the card account numbers to be checked, a card type and an expiration date. The trailer record terminates the file and gives a count of the detail records sent.

**Note:** All fields and field lengths listed are required. Any field with no data, or data that does not completely fill the required length, must be padded with trailing 'spaces'.

### Header Record

| **Field Name** | **Length** | **Format** | **Description** |
| ---------------------------------------- | ------------------------------ | ------------------------------ | ---------------------------------------------------------------------------------------------------- |
| Record Type | 2 | AN | H1 |
| Version ID | 6 | AN | Message format version ID, '100000' |
| Merchant ID | 32 | AN | ME Merchant Number |

### Detail Record

| **Field Name** | **Length** | **Format** | **Description** |
| ---------------------------------------- | ------------------------------ | ------------------------------ | ---------------------------------------------------------------------------------------------------- |
| Record Type | 2 | AN | D1 when using full card numbers D2 when using Card Store ID |
| Account Type | 4 | AN | VISA or MC or DISC |
| Account Number | 32 | AN | Credit card account number or Card Store ID value. |
| Expiration Date | 4 | N | YYMM |
| Discretionary Data | 32 | AN | Optional data field, returned in response record |

### Trailer Record

| **Field Name** | **Length** | **Format** | **Description** |
| ---------------------------------------- | ------------------------------ | ------------------------------ | ---------------------------------------------------------------------------------------------------- |
| Record Type | 2 | AN | T1 |
| Record Count | 6 | N | Number of detail records sent |

### Sample Request File

The following is a sample request file containing four records with no Discretionary Data.

![Sample request file](media/image3.png)

The following is the same sample file with dots representing blank spaces and paragraph marks displaying carriage returns.

![Sample request file with formatting marks](media/image4.png)

![Sample request file continued](media/image5.png)

The following is a sample file using the 32-character Card Store ID value (underlined in red) in place of the full card number and the exp date (underlined in blue).

![Sample request file using Card Store ID](media/image6.png)

The following is the same sample file with dots representing blank spaces and paragraph marks displaying carriage returns.

## Response File Format

A response file format that delivers the VAU/ABU response data follows. The general layout of the file is a leading header record with a version ID and merchant number, followed by some number of detail records and a final trailer record giving a count of the detail records contained in the file.

### Header Record

| **Field Name** | **Length** | **Format** | **Description** |
| ---------------------------------------- | ------------------------------ | ------------------------------ | ---------------------------------------------------------------------------------------------------- |
| Record Type | 2 | AN | H1 |
| Version ID | 6 | AN | Message format version ID, '100000' |
| Merchant ID | 32 | AN | ME merchant number |

### Detail Record

| **Field Name** | **Length** | **Format** | **Description** |
| ---------------------------------------- | ------------------------------ | ------------------------------ | ---------------------------------------------------------------------------------------------------- |
| Record Type | 2 | AN | D1 when using full card numbers D2 when using Card Store ID |
| Old Account Type | 4 | AN | VISA or MC or DISC |
| Old Account Number | 32 | AN | Credit card account number or Card Store ID value. |
| Old Expiration Date | 4 | N | YYMM |
| New Account Type | 4 | AN | VISA or MC or DISC |
| New Account Number | 32 | AN | New full card number or new truncated card number if using Card Store ID |
| New Expiration Date | 4 | N | YYMM |
| Response Code | 8 | AN | (See response code table below) |
| Response Source | 2 | AN | 01 -- MES 02 -- VAU 03 -- ABU 04 -- DAU |
| Discretionary Data | 32 | AN | Discretionary data field returned from request detail |

### Trailer Record

| **Field Name** | **Length** | **Format** | **Description** |
| ---------------------------------------- | ------------------------------ | ------------------------------ | ---------------------------------------------------------------------------------------------------- |
| Record Type | 2 | AN | T1 |
| Record Count | 6 | N | Number of detail records sent |

### Sample Response File

The following is a sample response file.

![Sample response file](media/image7.png)

The following is the same sample file with dots representing blank spaces and paragraph marks displaying carriage returns.

![Sample response file with formatting marks](media/image8.png)

The following is a sample response file using Card Store ID values.

![Sample response file using Card Store ID](media/image9.png)

The following is the same sample file with dots representing blank spaces and paragraph marks displaying carriage returns.

![Sample response file using Card Store ID with formatting marks](media/image10.png)

**Note:** In all of these file samples, 32 blank spaces are found at the end of each record because the optional Discretionary Data field was not submitted.

## Response Codes

The following table lists all possible responses from each updater service and maps it to a unique ME response code.

| **ME Response Code** | **Description** |
| --------------------- | -------------------------------------- |
| NEWACCT | Account number update |
| NEWEXP | Expiration date update |
| CALL | Contact customer advise |
| CLOSED | Account closed |
| NPBIN | Non participating BIN |
| NOMATCH | Participating BIN, no match |
| GOOD | Match, no change |
| ACCTERR | Non-V/MC account number |
| ACCTERR | Bad account number |
| EXPERR | Bad expiration date |
| ACCTERR | Bad check digit |
| MERCHERR | Unregistered merchant |
| OVERRIDE | Correction to a previous response |

# API Specification

The API will support three functions:

- Upload an AUS request file
- Status inquiry of request and response files
- Download an AUS response file

API file uploads are sent using Content-Type: multipart/form-data. See RFC-1867.

API response data that is not a file download is delivered as Content-Type text/plain. The response parameters are sent as name value pairs in the body of the response with the value data URL encoded as application/x-www-form-urlencoded.

API file downloads will deliver the file data in the response if successful. If an error occurs the response will contain a standard response code and message. The client will need to be able to detect which response is being delivered in order to correctly handle it. The presence of files download data will be indicated in the standard manner in HTTP responses, via the Content-Disposition header (```Content-Disposition: attachment; filename="xxx"```).

## Upload File

Base URL: `https://www.merchante-solutions.com/srv/api/ausUpload?`

Merchants upload a request file by submitting an HTTP multipart request consisting of a number of request parameters along with the file data.

### Upload File Request Parameters

| **Field Name** | **Length** | **Format** | **Description** |
| ---------------------------------------- | ------------------------------ | ------------------------------ | ---------------------------------------------------------------------------------------------------- |
| userId | 75 | AN | User login name. |
| userPass | 32 | AN | User login password. |
| merchId | 32 | AN | ME merchant number, must be linked to userId in AUS system |

In addition to the above parameters, which should be passed to the server in the multipart formatted body parts, the request should also contain the file data to be uploaded in a separate file body.

An http response is returned indicating the outcome of the upload along with a request file ID if the upload was successful.

### Upload File Response Parameters

| **Field Name** | **Length** | **Format** | **Description** |
| ---------------------------------------- | ------------------------------ | ------------------------------ | ---------------------------------------------------------------------------------------------------- |
| rspCode | 4 | N | Response code, 0 = OK, see error table. |
| rspMessage | 200 | AN | Response message |
| reqfId | 12 | N | AUS system generated request file ID |

## Status Inquiry

Base URL `https://www.merchante-solutions.com/srv/api/ausStatus?`

To determine the availability of response data the merchant should send a file status inquiry request.

### Status Inquiry Request Parameters

| **Field Name** | **Length** | **Format** | **Description** |
| ---------------------------------------- | ------------------------------ | ------------------------------ | ---------------------------------------------------------------------------------------------------- |
| userId | 75 | AN | User login name. |
| userPass | 32 | AN | User login password. |
| merchId | 32 | AN | ME merchant number, must be linked to userId in AUS system |
| statusFilter | 16 | AN | Optional Filter returned statuses. Valid filter codes: - ALL -- all available response files plus all pending request files (default) - RESPONSE -- all available response files - NEW -- all available response files that have not previously been downloaded - ERROR -- any request files that have an error status |
| reqfId | 12 | N | Optional. Returns all response files associated with the indicated request file |

File statuses are returned using the following parameters. Because multiple statuses may be returned the status parameter names end with an index value. The 'n' suffix of the fields below will contain the index value of the status set that the parameter belongs to, starting with index '0' and going up.

### Status Inquiry Response Parameters

| **Field Name** | **Length** | **Format** | **Description** |
| ---------------------------------------- | ------------------------------ | ------------------------------ | ---------------------------------------------------------------------------------------------------- |
| rspCode | 4 | N | Response code, 0 = OK, see error table. |
| rspMessage | 200 | AN | Response message |
| statusCount | 4 | N | Indicates number of status parameters sets that are present in this response. |
| reqfId_n | 12 | N | AUS system generated request file ID |
| reqfName_n | 64 | AN | Request file name |
| status_n | 16 | AN | - PENDING -- request file has been received, response data is pending - FAILED -- request file generated errors while being processed - SUCCESS -- response file received |
| rspfId_n | 12 | N | If the status includes an available response, the AUS system generated response file ID is provided here. Not present if response data is unavailable. |
| dlCount_n | 4 | N | Indicates how many times response file has been downloaded. Not present if response data is unavailable. 0 generally indicates this is a new response file to be processed by the merchant. |

## Download File

Base URL `https://www.merchante-solutions.com/srv/api/ausDownload?`

When a response file is available to download, the merchant may download the response file using the response file ID returned in the file status inquiry response.

### Download File Request Parameters

| **Field Name** | **Length** | **Format** | **Description** |
| ---------------------------------------- | ------------------------------ | ------------------------------ | ---------------------------------------------------------------------------------------------------- |
| userId | 75 | AN | User login name for ME web system |
| userPass | 32 | AN | User login password for ME web system |
| merchId | 32 | AN | ME merchant number, must be linked to userId in AUS system |
| rspfId | 12 | N | ID of response file to download |

If the response file is valid the response file data is returned as a response. If an error occurs the following general error response is returned.

### Upload File Response Parameters

| **Field Name** | **Length** | **Format** | **Description** |
| ---------------------------------------- | ------------------------------ | ------------------------------ | ---------------------------------------------------------------------------------------------------- |
| rspCode | 4 | N | Response code, 0 = OK, see error table. |
| rspMessage | 200 | AN | Response message |

## Response Codes

| **Response Code** | **Description** |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------ |
| 0 | Ok, operation successful |
| 1 | System error, contact ME |
| 2 | Authentication error, userId or userPass incorrect |
| 3 | Upload error, upload data is not accepted by the requested operation, upload file data was missing or failed to be delivered or the upload filename was missing |
| 4 | Invalid parameter xxx, the parameter indicated is invalid for the operation requested |
| 5 | Missing parameter, a required parameter was missing from the request |
| 100 | AUS merchant ID error, the supplied merchant ID was not valid in the AUS system |
| 101 | AUS status error, the statusFilter value sent was invalid |
| 102 | AUS response file ID invalid |

# File Upload and Download

## Request File Upload

Request files are uploaded through a simple web interface that is part of the ME Web Reporting site.

### Uploading Through the Web Interface

The following procedure explains how to upload a request file to ME.

1. Log into the **ME Web Reporting** site. The following screen is displayed.

   ![ME Web Reporting main menu](media/image11.png)
1. ![Account Updater Service menu](media/image12.png)Click the **Account Updater Service** hyperlink. The following screen is displayed.
1. Click the **Upload Request File** from the Account Updater Service menu. The following screen is displayed.

   ![Upload Request File screen](media/image13.png)
1. From the **Merchant Account** drop-down menu, select a merchant to associate with your file upload. There are no file naming convention rules.

   **Note:** In most cases, there will be only one merchant ID. You do have the option to associate multiple merchant IDs with your account during the certification process.
1. ![Browse for file screen](media/image14.png)Click on the **Browse** button to navigate to the file you wish to upload. The following screen is displayed.
1. Once the location of the file is displayed in the **Upload file** text box, click **submit** to upload the file. A confirmation message similar to the following message will be displayed:

   ![Upload confirmation message](media/image15.png)

   **Note:** The confirmation message only shows that the file was uploaded to ME successfully. File validation occurs during the processing of the file. It is possible for a file to fail the validation process. You will be notified of a failure to process when you view the response file.

After the file is uploaded, ME processes the file by splitting out Visa and MasterCard transactions and sending them in the required format to the credit card associations. Once Visa and MasterCard have provided their updates, the file is prepared for retrieval. This amount of time can vary, but is generally within 24-hours.

Realizing that the update information is time-critical, MerchantE will not wait if the results from one card association are returned before the other. Result files will be returned as they are received by MerchantE. This is explained in further detail in the next section.

In the event that an error is encountered while attempting to process the request file, the following email will be sent to the recipient on file.

```text
From: accountupdater@merchante-solutions.com

To: johndoe@yourbusiness.net

Subject: ME Account Updater Request File Error

DO NOT REPLY TO THIS MESSAGE - This email has been automatically generated.

MERCHANTE ACCOUNT UPDATER SERVICE (AUS)

NOTIFICATION OF REQUEST FILE ERROR

This is to notify you that processing has been halted on an AUS request
file received from you due to errors.

File ID: 912345

File name: nameOfFile.txt

Upload date: Thu Sep 17 13:27:06 PDT 2009

The following errors were encountered:

Line.Pos 1.8: Merchant number mismatch (file '900000000000',
upload '911111111111')

If you have any questions about this email, your MerchantE merchant
account or processing procedures, please contact our 24-hour Help Desk
at 888-288-2692 or help@merchante.com.

DO NOT REPLY TO THIS MESSAGE - This email has been automatically generated.
```

## Response File Retrieval

![Account Updater Service menu with View Response Files](media/image16.png)Processed files are retrieved through the same web interface. A user will log in to the ME Web Reporting site, navigate to the Account Updater Service option, and select View Response Files:

![View Response Files results](media/image17.png)By clicking the View Response Files option on any submitted file, the status and (if available) results of that file can be seen:

AUS listing of Request Files status.

AUS Activity Table Fields

| **Field** | **Description** |
| -------------------- | ------------------------------------------------------------------------------------------ |
| Number | Row number assigned by ME. |
| ID | File ID assigned by ME. |
| Request Date | Date and time the Request File was uploaded. |
| User | Name of user who signed into the system. |
| Merchant # | Merchant number that files were uploaded under. |
| Request File | Name of Request File. |
| Status | Status of file. Options are: Pending -- File has been uploaded but response has not yet been received by associations. Successful -- File has been returned by the association(s). Failed -- File failed to process due to errors encountered. |
| Response Date | Date and time that the Response File was made available. |
| Response File | Name of Response File. |

- AUS Response Files will only be available for 4 calendar days from posting. An email alert will be sent to the designated merchant contact when the Response File is posted.
- Because of association requirements, once the Response File has been removed from the listing, it can no longer be made available.
- If a response file is not retrieved within the 4 calendar days, a request file would need to be resubmitted.
- After the 4 calendar days, the file information will still appear in the AUS Activity table (Illustration 8) but the Response File hyperlink will no longer be live.
- Information is kept in the AUS Activity table for 30 calendar days then removed.
- If Visa and MasterCard responses are returned at the same time, MerchantE will merge them into the Response File. In that case, results will return the responses from each association as separate files. This will be denoted by the last digit of the Response File name (-1, -2) and can be checked against the Request File name.

**Note:** It is important to keep in mind that results are made available as they are received from Visa and from MasterCard; ME will not hold back results from one association if the other has not yet responded. Response Files, therefore, may be partial.

```text
From: accountupdater@merchante-solutions.com

To:

Subject: ME Account Updater Response File Available

DO NOT REPLY TO THIS MESSAGE - This email has been automatically generated.

MERCHANTE ACCOUNT UPDATER SERVICE (AUS)

NOTIFICATION OF RESPONSE FILE AVAILABILITY

This is to notify you that an AUS response file has just been posted.

File ID: 911169

File name: rsp-911146-1.txt

Response date: Thu Sep 17 13:30:05 PDT 2009

Please keep in mind that the response data will only be available for a
limited period as outlined in your product documentation. You should
retrieve these results as quickly as possible.

If you have any questions about this email, your MerchantE merchant
account or processing procedures, please contact our 24-hour Help Desk
at 888-288-2692 or help@merchante.com.

DO NOT REPLY TO THIS MESSAGE - This email has been automatically generated.
```

# Account Updater Summary Report

An **Account Updater Summary Report** is available to view a summary of the activity for a user-defined date range. The Summary Report provides statistics on the:

- Total number of requests
- Total number of matches
- Number of Visa requests
- Number of Visa matches
- Percentage of Visa matches
- Number of MasterCard requests
- Number of MasterCard matches
- Percentage of MasterCard matches
- Number of Discover requests
- Number of Discover matches
- Percentage of Discover matches

![Account Updater Summary Report](media/image18.png)

The **Total Matches** link opens a new window that contains specific response codes received by card type. 

![Total Matches detail by card type](media/image19.png)

**Note:** Only Visa provides non-match response codes; MasterCard does not make that information available.

## Version History with Revisions and Changelog

| **Date** | **Description** |
| ------------------ | ------------------------------------------------------------------------------------------------------------------ |
| July 2026 | Changed source format. |
| May 2022 | **Company information** Updated statements on page ii to include KeyBank. **Branding** Applied current ME-branded template to the document. |
| December 2019 | Rebranded |
| March 2015 | Added Discover to Introduction; Added Discover to chart; Testing Section; Added DISC card type; Added DISC card type; Added response code; Added Discover report fields |
