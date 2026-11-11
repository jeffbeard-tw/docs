---
title: "Batch Processing API Guide"
---

The MerchantE by Omise (ME) Batch Processing API enables merchants and resellers to process live credit card transactions in a batch format. Additionally, merchants can utilize the ME reporting tool to upload batches manually.

## Related documentation

The following documents are supporting documentation for this specification:

- MerchantE Payment Gateway Specification.
- MerchantE technical documentation is available at: [http://merchante.com/developer-portal](http://merchante.com/developer-portal).
- The PCI Security Standards Council manages the rules that merchants must follow when using payment cards. Those rules are available at [https://www.pcisecuritystandards.org/](https://www.pcisecuritystandards.org/).

# Batch Processing Overview

Batch Processing functionality allows a merchant to send a batch of transactions to the MerchantE (ME) Reporting Tool for processing Batch request files can be sent to ME in two ways:

- Through the ME reporting portal.
- Through an API call to ME.

Batch response files can be retrieved from ME in two ways:

- Through an API call to ME.
- From the ME reporting web portal.

Processing time will vary depending on the transaction type and overall size of the batch, however all batches will be processed within 24 hours of submission. The batch processing option may be used for large recurring files that require both authorization and capture (sale transaction), or in conjunction with the Payment Gateway (ex: captures at the end of day for all online authorizations).

## Batch Processing Best Practices

There are some functions in the Payment Gateway that may not be ideal when submitting transactions in a batch mode. Examples include:

- Transactions that need to reference a prior transactions are not good candidates for batch processing.
- Sending multiple settlements for a single authorization concurrently is not recommended within the same batch.
- Transactions such as a settlement followed immediately by a void should be avoided.

# Request Files

A request file is used to upload a batch of transactions to ME. This section defines the layout of the request file and the transmission methods for the request file.

**Note:** Transactions are formatted according to the layout defined in the ME Payment Gateway Specification.

Batch request files can be sent to ME in two ways:

- Through an API call to ME.
- Through the ME reporting portal

## Request Files through the ME API

All batch API requests are handled by an HTTPS POST. File data should be passed to the server using Content-Type: multipart/form-data. After uploading a file, a confirmation message is sent containing the response identification number.

### Upload Request Parameters

Request file parameters are defined in the following table.

| **Field Name** | **Length** | **Format** | **Required** | **Description** |
| -------------- | ---------- | ---------- | ------------- | ---------------- |
| `reqFile` | | AN | Yes | Contains the transaction file that is being transmitted to ME |
| `userId` | 32 | AN | Yes | ME login ID |
| `userPass` | 32 | AN | Yes | ME login password |
| `profileId` | 20 | N | Yes | The ME gateway profile ID the record is associated with |
| `testFlag` | 1 | A | No | Indicates if the file is a test file or not. The default value is N, but Y can be set in order to send a test file that will route to the ME simulator |

## Transaction Data Sample

The transactional data that is being uploaded should be a text file (.TXT) that has transactions formatted as outlined in the ME Payment Gateway Specification. The following sample provides and example of what a transaction data file may look like.

```text
transaction_type=P
&card_number=4012888812348882
&card_exp_date=1216
&transaction_amount=1.00
&cvv2=123
&cardholder_street_address=123+N.+Main
&cardholder_zip=55555
&invoice_number=123456
```

```text
transaction_type=P
&card_number=4012888812348882
&card_exp_date=1216
&transacton_amount=1.00
&cvv2=123
&cardholder_street_address=123+N.+Main
&cardholder_zip=55555
&invoice_number=123456
```

```text
transaction_type=P
&card_number=4012888812348882
&card_exp_date=1216
&transaction_amount=1.00
&cvv2=123
&cardholder_street_address=123+N.+Main
&cardholder_zip=55555
&invoice_number=123456
```

```text
transaction_type=P
&card_number=4012888812348882
&card_exp_date=1216
&transaction_amount=1.00
&cvv2=123
&cardholder_street_address=123+N.+Main
&cardholder_zip=55555
&invoice_number=123456
```

## Upload Request Confirmation

Upload request confirmation parameters are defined in the following table.

| **Field Name** | **Length** | **Format** | **Required** | **Description** |
| -------------- | ---------- | ---------- | ------------- | ---------------- |
| `reqFile` | | AN | Yes | Contains the transaction file that is being transmitted to ME |
| `userId` | 32 | AN | Yes | ME login ID |
| `userPass` | 32 | AN | Yes | ME login password |
| `respFileId` | 15 | N | Yes | The File ID from the processed batch. |
| `profileId` | 20 | N | Yes | The ME gateway profile ID the record is associated with |
| `testFlag` | 1 | A | No | Indicates if the file is a test file or not. The default value is N, but Y can be set in order to send a test file that will route to the ME simulator |

## Request Files through the ME Reporting Portal

Another way to upload batch processing files is to use the ME reporting portal. Users can upload a text file (.TXT) that has transactions formatted as outlined in the ME Payment Gateway Specification. In order to upload a file using the ME reporting portal, a user must have a valid User ID and password. Additionally, the User ID must be configured by ME for batch processing. The following procedure defines the steps for uploading a batch file to the ME reporting portal.

1. Log into the ME reporting portal with a valid **User ID** and **password**.
1. Select **Batch Processing**. The following screen is displayed:
1. From the **Batch Processing** menu, select **Upload Request File**. The following screen is displayed:
1. From the **Profile ID** drop down, select the profile you'd like to upload a batch file to.
1. From the Test flag drop down, select if this is a test file or not.
   - **No** — The file is not a test file.
   - **Yes** — The file is a test file.
1. Click **Browse** to navigate to the location on your computer where the batch file is. Select the file you wish to upload and click **Open**. The path of where the batch file is located is displayed in the **Upload File** field.
1. Click **Submit** to upload the file. The confirmation message is like the following message is displayed.

# Response Files

Response files are used to determine the status of each of the transactions that was submitted in a batch. Batch response files can be retrieved from ME in two ways:

- Through an API call to ME
- From the ME reporting web portal

## Downloading Response Files through the ME API

All batch API requests are handled by an HTTPS POST.

### Download Request Parameters

The following table defines the layout for download request file.

| **Field Name** | **Length** | **Format** | **Required** | **Description** |
| -------------- | ---------- | ---------- | ------------- | ---------------- |
| `userId` | 32 | AN | Yes | ME login ID |
| `userPass` | 32 | AN | Yes | ME login password |
| `respFileId` | 15 | N | Yes | The File ID from the processed batch |

## Download Request Confirmation

Confirmation file parameters are defined in the following table.

**Note:** This request confirmation is only received if an error has occurred.

| **Field Name** | **Length** | **Format** | **Required** | **Description** |
| -------------- | ---------- | ---------- | ------------- | ---------------- |
| `userId` | 32 | AN | Yes | ME login ID |
| `respFileId` | 15 | N | Yes | The File ID from the processed batch. |
| `rspCode` | 3 | N | Yes | Three-digit response/error code. |
| `rspMessage` | 45 | AN | Yes | Text that accompanies the three-digit response/error code. |

## Requesting a Response File through the ME Reporting Portal

Another way to request a batch response files is to use the ME reporting portal. In order to request a response file using the ME reporting portal, a user must have a valid User ID and password. Additionally, the User ID must be configured by ME for batch processing. The following procedure defines the steps for requesting a batch response file from the ME reporting portal.

1. Log into the ME reporting portal with a valid **User ID** and **password**.
1. Select **Batch Processing**. The following screen is displayed:
1. From the **Batch Processing** menu, select **Download Response File**. The following screen is displayed:
1. Type the **Response File ID** and click **Submit**. The following screen is displayed:
1. The file can either be **Saved** to the location of your choice or opened with the software package of your choice.

## Sample Text Response File

The following image is a sample of what a response file may look like.

# Error Codes

ME has several error codes that are specific to the batch upload process. Similar codes are used when transactions are processed through the Payment Gateway. The following table explains the error codes and associated required action.

| **Response Code** | **Error Description** | **Required Action** |
| ------------------ | ----------------------------- | -------------------- |
| `000` | OK | Batch was successfully received |
| `001` | System Error | Contact ME for research and resolution |
| `002` | Authentication Error | Invalid access right or invalid user ID and/or user password |
| `003` | Invalid Parameter | Check format to correct the invalid parameter |
| `004` | Missing parameter | Check format for missing parameter |
| `300` | Invalid Profile ID | Verify Profile ID is correct and resend batch |
| `301` | Invalid response file ID | Verify that user ID, user password, and file ID are valid and retry |
| `302` | Invalid test flag | Valid values are Y and N, default is N, field not required. Please check request file message |
| `303` | Batch processing not allowed | Contact ME, batch processing must be enabled for the profile ID used |

# Email Confirmation

An email will be sent upon successful receipt of a batch. A follow up email is sent once the batch has been processed and will contain a response file. Email address is configured by ME at the profile ID level.

## Sample of an Email Confirmation for File Received

```text
-----Original Message-----
From: mesPGbatchprocessing@merchante-solutions.com [mailto:mesPGbatchprocessing@merchante-solutions.com]
Sent: Tuesday, November 08, 2011 1:25 PM
To: Jeff Doe
Subject: ME Payment Gateway Batch File processing was received
DO NOT REPLY TO THIS MESSAGE - This email has been automatically generated.
MERCHANTE
ME Payment Gateway batch file processing has received and prepared your file for processing. You will be receiving a second email with the response data.
Please keep in mind that the response data is also available via our standard ME web reporting options.
If you have any questions about this email, your MerchantE merchant account or processing procedures, please contact our 24-hour Help Desk at 888-288-2692 or help@MerchantE.com.
DO NOT REPLY TO THIS MESSAGE - This email has been automatically generated.
```

## Sample of an Email Confirmation for Batch Processed

```text
DO NOT REPLY TO THIS MESSAGE - This email has been automatically generated.
MERCHANTE
ME Payment Gateway batch file processed your transactions and prepared a response file. The attached .txt file contains a response for each of your transactions.
Here is a summary of your file.
# of Transaction: 4 $ Amount: $4.00
# Approved: 4 $ Approved: $4.00
# Declined: 0 $ Declined: $0.00
Please remember this response data is also available via our standard ME web reporting options.
If you have any questions about this email, your MerchantE merchant account or processing procedures, please contact our 24-hour Help Desk at 888-288-2692 or help@MerchantE.com.
DO NOT REPLY TO THIS MESSAGE - This email has been automatically generated.
```

## Version History with Revisions and Changelog

| **Date** | **Change** |
| -------------- | ---------- |
| July 2026 | Updated source format.|
| April 2023 | **Switched to monthly updates.** **Revised template.** |
| May 2022 | **Company Information** Updated statement on page ii to include KeyBank; removed mention of SDK libraries in Related Documentation section on page 1. **Branding** Applied current ME-branded template to the document. |
| November 2019 | **Branding** Applied current MeS-branded template to the document. |
| October 2017 | New document |
