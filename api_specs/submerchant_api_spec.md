---
title: "Sub-Merchant API Guide"
---

This document describes how a Payment Facilitator can onboard sub-merchants. This allows the Payment Facilitator to process payments for the sub-merchant using their merchant Identification number. The Sub-Merchant API has been adapted from the Partner to Portal API to allow for sub-merchants to be more quickly onboarded.

## Documentation

MerchantE by Omise (ME) technical documentation is available at: [https://merchante.com/developer-portal](https://www.merchante.com/developer-portal).

The **PCI Security Standards Council** manages the rules that merchants must follow when using payment cards. Those rules are available at: [https://www.pcisecuritystandards.org/](https://www.pcisecuritystandards.org/).

The **North American Industry Classification System** (NAICS) is used by the United States, Canada, and Mexico to classify businesses by industry. Each business is classified into a six-digit NAICS code number based on the majority of activity at the business. The United States Census Bureau NAICS lookup can be found here: [https://www.census.gov/econ/isp/](https://www.census.gov/econ/isp/).

# Sub-Merchant API Introduction

A Payment Facilitator (PayFac) is a merchant that holds a payment processing account or a master merchant identification number. The PayFac can then onboard sub-merchants and allow them to operate under the master MID, greatly simplifying payment processing for the sub-merchant.

The MerchantE Sub-Merchant API provides a route PayFacs to onboard new sub-merchants to their platform. It is a streamlined version of the onboarding process which automatically populates many fields, requiring the manual entry of far fewer fields in the application. The issuance of a sub-merchant MID is quickly accomplished following the standard risk assessment procedure.

MechantE offers two options for PayFacs depending on the underwriting provider. The type will also determine the information required when onboarding Sub-Merchants.

1. **PayFac-as-a-Service (PaaS)** - MerchantE underwrites the transactions and acts as the payment processor.

1. **Wholesale PayFac (WP)** - The Payment Facilitator provides outside underwriting. This is normally done in conjunction with a sponsor bank.

![](media/image3.png)

# Getting Started

To get started using the MerchantE Sub-Merchant API, you must set up a PayFac account setup up after consultation with MerchantE Customer Service. This parent account will be used to populate data fields for sub-merchants. The structure of the data objects being sent and received through the API is summarized below. The hierarchy of objects is described below.

- PayFac (Account)

- Merchant (Opportunity)

- Contact

- Business Checking Account

![](media/image4.png)

# Certification

Prior to utilizing the Sub-Merchant API in production, you must complete a certification process must be completed with MerchantE. A member of our certification team will work with you as the integration interest is identified. The primary goal of certification is to validate that the Sub-Merchant API request is accurately formed and successfully transmitted to MerchantE. The production endpoint will be shared after completing the certification process.

# Aggregate Net Funding

MerchantE's PayFac-as-a-Service (PaaS) and Wholesale PayFac (WP) service offer several payment options depending on your needs. Aggregate Net Funding (ANF) allows you to have deposits combined into a single deposit from each card brand to minimize the number of Automated Clearing House (ACH) transactions needed to process your deposits. Enabling this feature will create one entry for Visa and Master, one entry for Discover, and one entry for American Express. This will greatly simplify your record keeping and reconciliation process.

![](media/image5.png)

You can check to see if Aggregate Net Funding has been enabled by checking the Association entry in the Business Portal. The Association ID can be identified by searching using the Merchant information. The Agg Net Funding cell will reflect the status of Aggregate Net Funding. The results of the aggregated transaction will be reflected in the Bank Reconciliation Report and the Daily Interchange Report.

![](media/image6.png)

To enable Aggregate Net Funding, contact [Customer Service](mailto:help@merchante.com).

# Authentication

For a request to be successfully received by MerchantE, it must adhere to the following basic authentication process:

1. "Get Auth Token" Request Method containing the provisioned Salesforce username and password is sent via the URL to generate an access token.

**Note:** Auth token is valid for 24 hours.

1. The access token must be passed as Type: ```OATH 2.0``` with the authorization header in the create/update POST requests to the production endpoint.

## Request Parameters

| **Parameter** | **Value** | **Notes** |
| -------------------- | ------------------------------ | -------------------------------------------------- |
| grant_type | password | Literal "password" text |
| client_id | *To be provided by SE* | Different for each sandbox/org |
| client_secret | *To be provided by SE* | Different for each sandbox/org |
| username | partnerportalapi@merchante.com.cert | Different for each sandbox/org |
| password | `{{Password+Security_Token}}` | Provided by secure Email, Security Token is not always required depending on the org configuration |

# Merchant Application Fields

The Sub-Merchant API can accept both partial and complete applications. Complete applications contain all the required business information necessary for underwriting.

The Sub-Merchant application is broken up into the following sections which contain data elements that can be sent through this API:

- Merchant Information

- Checking Account

- Beneficial Owners

- Transaction Payment Options

API requests can be made in JSON or XML format. You must transmit Attachment submissions to an alternate endpoint.

For the initial request, it's important to use the sample requests, field lists, and validation rules provided. The following specific guidance should be followed when sending a request:

- All required fields are provided.

- At least one Primary Contact/Beneficial Owner is identified.

- Fields required for the Contact/Beneficial Owner is based on the Legal Entity Type/Ownership for the Account.

  - See the Business Entity Type and Ownership Role field definitions for clarification.

  - A Beneficial Owner can have multiple contact roles assigned in a single request.

- Include the Opportunity SFID for Update requests.

  - Source ID can be used to relate SFID to the corresponding fields provided.

- To prevent changes to approved merchant applications, updates to applications in the "Pending Underwriting" or "Closed Won" stages are not allowed.

Please contact the Partner Support team for help with any questions.

# Response Field Definitions

These fields are shown in both the *create* and *update* responses.

- **SFId -** Returns a unique Salesforce ID value for each of the created/updated Salesforce records (Opportunity, Account, Quote, Quote Line Items, Contact).

- **SourceId -** Indicates whether a specific data object has multiple fields (e.g., QuoteLineItem: Equipment). One field will return "null". Two or more fields will return the following values established in the create/update request:

  - For *Contact*, it returns the value of Beneficial_Owner_Unique_Field__c.

  - For *Business Checking Account*, it returns the value of Checking_Account_Unique_Field__c.

- **Success -** Indicates if the data element was created successfully (e.g., true; false).

- **ObjectType -** The type of data object being sent and received through the API (e.g., Account, Opportunity, Contact, Business Checking Account, Quote, Quote Line Item).

- **Message -** A string of text that indicates if a specific data object was created/updated successfully. (e.g., "Business Checking Account Created Successfully.").

# API Endpoints

## POST Authorization Token Sample

Provide the Authentication Request Parameters in the body of the request. The body of the response will contain the access_token. The access_token will expire after 24 hours. The request URL will depend on the environment being used. The following URL is for the production environment.

[https://login.salesforce.com/services/oauth2/token](https://login.salesforce.com/services/oauth2/token)

### Request

```text
curl -v -X POST \
https://test.salesforce.com/services/oauth2/token \
-H 'content-type: application/x-www-form-urlencoded' \
-d 'grant_type=password
&client_id=xxxXXXXX
&client_secret=xxxXXX
&username=abc@merchante-solutions.com.dev
&password=xxx'
```

### Response

```json
{
    "access_token": "<ACCESS_TOKEN>",
    "instance_url": "https://merchante-solutions--cert.my.salesforce.com",
    "id": "https://test.salesforce.com/id/00D010000004dsTEAQ/00501000002KdrmAAC",
    "token_type": "Bearer",
    "issued_at": "1649432850703",
    "signature": "Z1lxdqcdfOZMCjuICOTjYYbmklGrYE9xbYn5ZJ/BjNA="
}
```

## POST Create and Update Records

Use the access_token in the Authorization section of the request.

**Note:** The host may be different for each organization. Use instance_url from Authorization Token response.

## POST Create Application Sample

**POST {instance_url}/services/apexrest/merchant-application-payfac/**

Set the Is_ME_Underwriting__c parameter:

- false - Another underwriter is used for the account.

- true - MerchatE is underwriting for the account.

### Request

```json
{
  "Opp": {
      "attributes": {
          "type": "Opportunity"
      },
      "Is_ME_Underwriting__c": true
  },
  "Mer": {
      "attributes": {
          "type": "Account"
      },
      "Association_ID_API_Field__c": "3941803045",
      "Legal_Name__c": "test legal name 1111",
      "Tax_ID__c": "333333333",
      "State_of_Entity_Formation_Incorporation__c": "CA",
      "Name": "Test Account",
      "BillingStreet": "123 test",
      "BillingCity": "San Jose",
      "BillingState": "California",
      "BillingPostalCode": "99999",
      "BillingCountry": "united states",
      "Phone": "(444) 555-6666",
      "App_Email__c": "test@mes.com",
      "Website": "www.mes.com",
      "Customer_Service_Phone__c": "888-888-8888",
      "Industry": "Retail",
      "Industry_other__c": "test other",
      "Location_Type__c": "Ecommerce",
      "Location_Other__c": "test other",
      "Ownership": "Government",
      "Years_at_Location__c": "3",
      "Date_Established__c": "2000-12-10",
      "Chain_Non_Chain__c": "Single Outlet",
      "Description": "Test description",
      "MCC_API_Field__c": "5045",
      "Ever_accepted_credit_cards_before__c": true,
      "Ever_had_a_merchant_account_canceled__c": true
  },
    "lstbussCheckAcc": [
        {
            "attributes": {
                "type": "Business_Checking_Account__c"
            },
            "Bank_Name__c": "Wells Fargo",
            "Bank_Street_Address__c": "First street",
            "Bank_City__c": "San Francisco",
            "Bank_State__c": "California",
            "Bank_Zipcode__c": "11111",
            "Checking_Account__c": "1212121212",
            "Transit_Routing__c": "121233333",
            "Years_Open__c": 4,
            "DDA_Type__c": "Chargeback",
            "Checking_Account_Unique_Field__c": "111"
        },
        {
            "attributes": {
                "type": "Business_Checking_Account__c"
            },
            "Bank_Name__c": "Wells Fargo",
            "Bank_Street_Address__c": "First street",
            "Bank_City__c": "San Francisco",
            "Bank_State__c": "California",
            "Bank_Zipcode__c": "11111",
            "Checking_Account__c": "1212121222",
            "Transit_Routing__c": "121212121",
            "Years_Open__c": 4,
            "DDA_Type__c": "fees;Deposit",
            "Checking_Account_Unique_Field__c": "222"
        }
    ]
}
```

### Response

**Note:** The SFId listed for each object type will be used in later GET created data requests.

```json
[
    {
        "Success": true,
        "SourceId": null,
        "SFId": "0018L00000E6XysQAF",
        "ObjectType": "Account",
        "Message": "Merchant Information Created Successfully."
    },
    {
        "Success": true,
        "SourceId": null,
        "SFId": "0068L000008IybaQAC",
        "ObjectType": "Opportunity",
        "Message": "Transaction/Payment Options Created Successfully."
    },
    {
        "Success": true,
        "SourceId": null,
        "SFId": "0Q08L000000HKx0SAG",
        "ObjectType": "Quote",
        "Message": "Record Created Successfully."
    },
    {
        "Success": true,
        "SourceId": null,
        "SFId": "0QL8L000000HPMWWA4",
        "ObjectType": "QuoteLineItem",
        "Message": "Solution & Fees Created Successfully."
    }
]
```

## POST Update Record Sample

**POST {instance_url}/services/apexrest/merchant-application-payfac/**

### Request

```json
{
  "Opp": {
      "attributes": {
          "type": "Opportunity"
      },
      "Id": "0068L000008IybaQAC",
      "Is_ME_Underwriting__c": true
  },
  "Mer": {
      "attributes": {
          "type": "Account"
      },
      "Id": "0018L00000E6XysQAF",
      "Legal_Name__c": "test legal name 1111",
      "Tax_ID__c": "333333333",
      "State_of_Entity_Formation_Incorporation__c": "CA",
      "Name": "Test Account",
      "BillingStreet": "123 test",
      "BillingCity": "San Jose",
      "BillingState": "California",
      "BillingPostalCode": "99999",
      "BillingCountry": "united states",
      "Phone": "(444) 555-6666",
      "App_Email__c": "test@mes.com",
      "Website": "www.mes.com",
      "Customer_Service_Phone__c": "888-888-8888",
      "Industry": "Retail",
      "Industry_other__c": "test other",
      "Location_Type__c": "Ecommerce",
      "Location_Other__c": "test other",
      "Ownership": "Government",
      "Years_at_Location__c": "3",
      "Date_Established__c": "2000-12-10",
      "Chain_Non_Chain__c": "Single Outlet",
      "Description": "Test description",
      "MCC_API_Field__c": "5045",
      "Ever_accepted_credit_cards_before__c": true,
      "Ever_had_a_merchant_account_canceled__c": true
  }
}
```

### Response

```json
[
    {
        "Success": true,
        "SourceId": null,
        "SFId": "0018L00000E6XysQAF",
        "ObjectType": "Account",
        "Message": "Merchant Information Updated Successfully."
    },
    {
        "Success": true,
        "SourceId": null,
        "SFId": "0068L000008IybaQAC",
        "ObjectType": "Opportunity",
        "Message": "Transaction/Payment Options Updated Successfully."
    },
    {
        "Success": true,
        "SourceId": null,
        "SFId": "0Q08L000000HKx0SAG",
        "ObjectType": "Quote",
        "Message": "Record Updated Successfully."
    }
]
```

## POST Create Attachment Sample

**Note:** Opportunity ID is a required field for submitting attachments. This request contains both a plain text and an image file converted to Base64.

**File Attachment Size Limit:** 100 MB

**POST {instance_url}/services/data/v54.0/composite/tree/Attachment**

### Request

```json
{
  "records": [
    {
      "attributes": {
        "type": "Attachment",
        "referenceId": "attachment0"
      },
      "ParentId":"00600000011111XXX",
      "Name":"File.pdf",
      "body":"base64Coded"
    },
    {
      "attributes": {
        "type": "Attachment",
        "referenceId": "attachment1"
      },
      "ParentId":"00100000011111XXX",
      "Name":"TestTXTFIle.txt",
      "body":"base64Coded"
    }
  ]
}
```

### Response

```json
{
    "hasErrors": false,
    "results": [
        {
            "referenceId": "attachment0",
            "id": "00P8L000000qsJKUAY"
        }
    ]
}
```

## GET Created Records

You can use the Opportunity_Id from the Create Application Response to retrieve the stored business data for an opportunity and review the information for accuracy and completeness.

Opportunity_Id refers to the SFId value associated with the ObjectType: Opportunity. Similarly, Quote_Id refers to the SFId value associated with the ObjectType: Quote.

**Note:** Use the access_token in the Authorization section of the request.

## GET Application Status Sample

**GET {instance_url}/services/apexrest/merchant-application-status/{OPPORTUNITY_ID}**

### Wholesale PayFac Response

```text
200 OK
```

```json
{
    "Integration_Failure_Stage__c": "Error generating AppSeq",
    "Integration_Message__c": null,
    "Integration_Status__c": "Failed",
    "MID__c": "14471729",
    "Id": "0065900000KPVt2AAH"
}
```

### PayFac as a Service Response

```text
200 OK
```

```json
{
    "UW_Time_to_Approve_Decline__c": null,
    "Underwriting_Approval_Date_Timestamp__c": null,
    "Underwriting_Submission_Date_Timestamp__c": null,
    "Underwriting_Submission_Date__c": null,
    "Underwriting_Reason_Details__c": null,
    "Underwriting_Reason_Code__c": null,
    "Underwriter_Name__c": null,
    "Change_Notes__c": null,
    "AEX_Short_ID__c": null,
    "AEX_Document_ID__c": null,
    "Underwriting_Status__c": null,
    "Integration_Failure_Stage__c": null,
    "Integration_Message__c": null,
    "Integration_Status__c": "Failed",
    "MID__c": "14471729",
    "Id": "0065900000JzHFdAAN"
}
```

# Field Descriptions

## Business Entity and Business Owner Descriptions

Key parts of any merchant application include defining the *business entity type* and the *ownership role* of the beneficial owner(s).

**Business Entity Type**

| **Entity or Business Type** | **Information Required On:** | **Data Required** | **Notes** |
| ---------------------------------------------------------------- | ------------------------------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Sole Proprietorship (one single owner) | Sole Owner (principal) | Full Name<br />Physical Address<br />Social Security Number<br />DOB | Sole Proprietorships can only have one owner |
| Partnership, Limited Liability Company, Privately Held Corporation | Any Owner with 25% or more ownership interest, and Controlling Authority (one individual only) | Full Name<br />Physical Address<br />Social Security Number<br />DOB<br />% Ownership<br />Title | It is possible for an owner to also satisfy the obligation of Controlling Authority in the business. |
| Tax Exempt (e.g. NPO, Political Campaign) | Controlling Authority (one individual only) | Full Name<br />Physical Address<br />Social Security Number<br />DOB<br />Title | Tax Exempt entity must be granted tax exempt status from the IRS via a 501(c)(2)(3) form |
| Government | Controlling Authority (one individual only) | Full Name<br />Title | Applies to Federal, State, and Local |
| Publicly Traded Corporation | Controlling Authority (one individual only) | Full Name<br />Title | A US Public Corporation trades on a US Stock Exchange such as the NASDAQ or NYSE. UW must collect the Company's Ticker Symbol (can be entered on the notes section). |
| Financial Institution | Controlling Authority (one individual only) | Full Name<br />Title | A Corporation Entity type, whether Public or Private, can also be a Financial Institution. If the Financial Institution is regulated by a Federal or State regulator, only the full name and title is required on the Controlling Authority at the Financial Institution. |

**Ownership Roles**

| **Definitions** | **Descriptions** |
| ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Owner (Ownership Prong) | Each individual, if any, who, directly or indirectly, through any contract, arrangement, understanding, relationship or otherwise, owns 25% or more of the equity interest of a merchant. |
| Controlling Authority (Control Prong) | A single individual with significant responsibility for managing the merchant, such as an executive officer or senior manager (e.g., Chief Executive Officer, Chief Financial Officer, Chief Operating Officer, Managing Member, General Partner, President, Vice-President, Treasurer, or any other individual who regularly performs similar functions). |
| Signing Authority | A signing authority can be either an owner or a controlling authority (or both, if applicable). |

## Merchant Information

The Merchant Information section of the API collects demographic information about the merchant.

**Merchant Fields**

| **Custom UI Field Name** | **Salesforce Field Label** | **Salesforce Field API Name** | **Object** | **Data Type** | **Salesforce Data Type** | **Picklist Values** | **Length** | **Required WP** | **Required PaaS** |
| ------------------ | -------------------------- | -------------------------- | ------------------ | -------------- | -------------------------- | -------------------- | ---------------- | -------------------- | -------------------- |
| Legal Name | Legal Name | Legal_Name__c | Account | String | Text | | 100 | Yes | Yes |
| Federal Tax ID | Federal Tax ID | TAX_ID__c | Account | String | Text | | 16 | Yes | Yes |
| State of Formation/ Incorporation | State of Entity Formation/ Incorporation | State_of_Entity_Formation_Incorporation__c | Account | String | Picklist | | | Yes | Yes |
| Merchant DBA Name | Account Name | Name | Account | String | Name | | 255 | Yes | Yes |
| Legal Street Address | Legal Street | BillingStreet | Account | String | TextArea | | 255 | Yes | Yes |
| Legal City | Legal City | BillingCity | Account | String | String | | 40 | Yes | Yes |
| Legal Country | Legal Country Code | BillingCountryCode | Account | String | Picklist | *See Below | 80 | Yes | Yes |
| Legal State | Legal State/Province Code | BillingStateCode | Account | String | Picklist | *See Below | 80 | Yes | Yes |
| Legal Zip Code | Legal Zip/Postal Code | BillingPostalCode | Account | String | String | | 20 | Yes | Yes |
| Phone | Phone | Phone | Account | String | Phone | | 10 | Yes | Yes |
| Email Address | App Email | App_Email__c | Account | String | Text | | 100 | Yes | Yes |
| Website Address | Website | Website | Account | String | URL | | 255 | Yes | Yes |
| Contact First Name | First Name | FirstName | Contact | String | String | | 40 | Yes | Yes |
| Contact Last Name | Last Name | LastName | Contact | String | String | | 80 | Yes | Yes |
| Contact Title | Title | Title | Contact | String | String | | 128 | Yes | Yes |
| Contact Phone | Phone | Phone | Contact | String | Phone | | 10 | Yes | Yes |
| Customer Service Phone | Customer Service Phone | Customer_Service_Phone__c | Account | String | Phone | | 10 | Yes | Yes |
| Industry | Industry | Industry | Account | String | Picklist | | 255 | Yes | Yes |
| Industry Other | Industry Other | Industry_Other__c | Account | String | String | | 100 | Yes | Yes |
| Business Location | Location Type | Location_Type__c | Account | String | Picklist | | 255 | Yes | Yes |
| Business Location Other | Location Type | Location_Type__c | Account | String | Picklist | | 255 | Yes | Yes |
| Legal Entity Type | Ownership | Ownership | Account | String | Picklist | | 255 | Yes | Yes |
| Years at Location | Years at Location | Years_at_Location__c | Account | Integer | Double | | 4 | Yes | Yes |
| Date Established | Date Established | Date_Established__c | Account | Date | Date | | | Yes | Yes |
| Chain/Non-chain | Chain/Non Chain | Chain_Non_Chain__c | Account | String | Picklist | | 255 | Yes | Yes |
| Description | Description | Description | Account | String | Long text area | | 32000 | Yes | Yes |
| Ever accepted credit cards before? | Ever accepted credit cards before? | Ever_accepted_credit_card_before__c | Account | Boolean | Boolean | | | Yes | Yes |
| Merchant account canceled? | Ever had a merchant account canceled? | Ever_had_merchant_account_cancelled__c | Account | Boolean | Boolean | | | Yes | Yes |
| BET Table Details | BET Table | BET_Table_ID_API_Field__c | Quote Line Items | String | Lookup | | 255 | No | No |
| Association ID | Association ID | Association_ID__c | Account | String (Id) | Lookup | | 18 | No | No |
| MCC | MCC | MCC_API_Field__c | Account | String (Id) | Lookup | | 100 | Yes | Yes |
| Sponsorship | Sponsorship | Sponsorship__c | Account | Picklist | | KeyBank (default)<br />Synovus<br />Fresno First | - | No | No |
| Association ID API Field | Association API ID API Field | Association_ID_API_Field__c | Account | String | Text | | 10 | Yes | Yes |

**Location Codes**

`*BillingStateCode` and `ShippingStateCode` picklist values:

--None--, Alabama, Alaska, American Somoa, Arizona, Arkansas, Armed Forces Americas, Armed Forces Europe, Armed Forces Pacific, California, Colorado, Connecticut, Delaware, District of Columbia, Federated Micronesia, Florida, Georgia, Guam, Hawaii, Idaho, Illinois, Indiana, Iowa, Kansas, Kentucky, Louisiana, Maine, Marshall Islands, Maryland, Massachusetts, Michigan, Minnesota, Mississippi, Missouri, Montana, Nebraska, Nevada, New Hampshire, New Jersey, New Mexico, New York, North Carolina, North Dakota, Northern Mariana Islands, Ohio, Oklahoma, Oregon, Palau, Pennsylvania, Puerto Rico, Rhode Island, South Carolina, South Dakota, Tennessee, Texas, United States Minor Outlying Islands, US Virgin Islands, Utah, Vermont, Virginia, Washington, West Virginia, Wisconsin, Wyoming

`*BillingCountryCode` and `ShippingCountryCode` picklist values:

--None--, Afghanistan, Aland Islands, Albania, Algeria, Andorra, Angola, Anguilla, Antarctica, Antigua and Barbuda, Argentina, Armenia, Aruba, Australia, Austria, Azerbaijan, Bahamas, Bahrain, Bangladesh, Barbados, Belarus, Belgium, Belize, Benin, Bermuda, Bolivia (Plurinational State of), Bonaire, Sint Eustatius and Saba, Bosnia and Herzegovina, Botswana, Bouvet Island, Brazil, British Indian Ocean Territory, Brunei Darussalam, Bulgaria, Burkina Faso, Burundi, Cambodia, Cameroon, Canada, Cape Verde, Cayman Islands, Central African Republic, Chad, Chile, China, Chinese Taipei, Christmas Island, Cocos (Keeling) Islands, Colombia, Comoros, Congo, Congo (the Democratic Republic of the), Cook Islands, Costa Rica, Cote d'Ivoire, Croatia, Cuba, Curacao, Cyprus, Czech Republic, Denmark, Djibouti, Dominica, Dominican Republic, Ecuador, Egypt, El Salvador, Equatorial Guinea, Eritrea, Estonia, Ethiopia, Falkland Islands (Malvinas), Faroe Islands, Fiji, Finland, France, French Guiana, French Polynesia, French Southern Territories, Gabon, Gambia, Georgia, Germany, Ghana, Gibraltar, Greece, Greenland, Grenada, Guadeloupe, Guatemala, Guernsey, Guinea, Guinea-Bissau, Guyana, Haiti, Heard Island and McDonald Islands, Holy See (Vatican City State), Honduras, Hungary, Iceland, India, Indonesia, Iran (Islamic Republic of), Iraq, Ireland, Isle of Man, Israel, Italy, Jamaica, Japan, Jersey, Jordan, Kazakhstan, Kenya, Kiribati, Korea (Democratic People's Republic of), Korea (Republic of), Kuwait, Kyrgyzstan, Lao People's Democratic Republic, Latvia, Lebanon, Lesotho, Liberia, Libyan Arab Jamahiriya, Liechtenstein, Lithuania, Luxembourg, Macao, Macedonia (the former Yugoslav Republic of), Madagascar, Malawi, Malaysia, Maldives, Mali, Malta, Martinique, Mauritania, Mauritius, Mayotte, Mexico, Moldova (Republic of), Monaco, Mongolia, Montenegro, Montserrat, Morocco, Mozambique, Myanmar, Namibia, Nauru, Nepal, Netherlands, New Caledonia, New Zealand, Nicaragua, Niger, Nigeria, Niue, Norfolk Island, Norway, Oman, Pakistan, Palestinian Territory (Occupied), Panama, Papua New Guinea, Paraguay, Peru, Philippines, Pitcairn, Poland, Portugal, Qatar, Reunion, Romania, Rwanda, Saint Barthelemy, Saint Helena (Ascension and Tristan da Cunha), Saint Kitts and Nevis, Saint Lucia, Saint Martin (French part), Saint Pierre and Miquelon, Saint Vincent and the Grenadines, Samoa, San Marino, Sao Tome and Principe, Saudi Arabia, Senegal, Serbia, Seychelles, Sierra Leone, Singapore, Sint Maarten (Dutch part), Slovakia, Slovenia, Solomon Islands, Somalia, South Africa, South Georgia and the South Sandwich Islands, South Sudan, Spain, Sri Lanka, Sudan, Suriname, Svalbard and Jan Mayen, Swaziland, Sweden, Switzerland, Syrian Arab Republic, Tajikistan, Tanzania (United Republic of), Thailand, Timor-Leste, Togo, Tokelau, Tonga, Trinidad and Tobago, Tunisia, Turkey, Turkmenistan, Turks and Caicos Islands, Tuvalu, Uganda, Ukraine, United Arab Emirates, United Kingdom, United States, Uruguay, Uzbekistan, Vanuatu, Venezuela (Bolivarian Republic of), Viet Nam, Virgin Islands (British), Wallis and Futuna, Western Sahara, Yemen, Zambia, Zimbabwe

## Checking Account

**Checking Account Fields**

| **Custom UI Field Name** | **Salesforce Field Label** | **Salesforce Field API Name** | **Object** | **Data Type** | **Salesforce Data Type** | **Picklist Values** | **Length** | **Required WP** | **Required PaaS** |
| ------------------ | -------------------------- | -------------------------- | ------------------ | -------------- | -------------------------- | -------------------- | ---------------- | -------------------- | -------------------- |
| Bank Name | Bank Name | Bank_Name__c | Business Checking Account | String | String | | 255 | Yes | Yes |
| Bank Street Address | Bank Street Address | Bank_Street_Address__c | Business Checking Account | String | String | | 255 | Yes | Yes |
| Bank City | Bank City | Bank_City__c | Business Checking Account | String | String | | 100 | Yes | Yes |
| Bank State | Bank State | Bank_State__c | Business Checking Account | String | Picklist | AK, AL, AR, AZ, CA, CO, CT, DC, DE, FL, GA, HI, IA, ID, IL, IN, KS, KY, LA, MA, MD, ME, MI, MN, MO, MS, MT, NC, ND, NE, NH, NJ, NM, NV, NY, OH, OK, OR, PA, PR, RI, SC, SD, TN, TX, UT, VA, VT, WA, WI, WV, WY | 100 | Yes | Yes |
| Bank Zip Code | Bank Zipcode | Bank_Zipcode__c | Business Checking Account | String | String | | 20 | Yes | Yes |
| Checking Account # | Checking Account # | Checking_Account__c | Business Checking Account | String | String | | 100 | Yes | Yes |
| Transit Routing # | Transit Routing # | Transit_Routing__c | Business Checking Account | String | String | | 100 | Yes | Yes |
| Years Open | Years Open | Years_Open__c | Business Checking Account | Integer | Integer | | 18 | Yes | Yes |
| DDA Type | DDA Type | DDA_Type__c | Business Checking Account | String | Multi-Picklist | Deposit<br />Chargeback<br />Fees | 4099 | Yes | Yes |
| Checking Account Unique Field | Unique field to identify accounts. | Checking_Account_Unique_Field__c | Business Checking Account | String | String | | 3 | No | No |

## Beneficial Owners

**Beneficial Owner Fields**

| **Custom UI Field Name** | **Salesforce Field Label** | **Salesforce Field API Name** | **Object** | **Data Type** | **Salesforce Data Type** | **Picklist Values** | **Length** | **Required WP** | **Required PaaS** |
| ------------------ | -------------------------- | -------------------------- | ------------------ | -------------- | -------------------------- | -------------------- | ---------------- | -------------------- | -------------------- |
| First Name | First Name | FirstName | Contact | String | String | | 40 | Yes | Yes |
| Last Name | Last Name | LastName | Contact | String | String | | 80 | Yes | Yes |
| Email | Email | Email | Contact | String | String | | 80 | Yes | Yes |
| Date of Birth | Birthdate | Date_of_Birth__c | Contact | Date | Date | | 0 | Yes | Yes |
| Street Address | Mailing Street | MailingStreet | Contact | String | TextArea | | 255 | Yes | Yes |
| City | Mailing City | MailingCity | Contact | String | String | | 40 | Yes | Yes |
| State | Mailing State/Province | MailingStateCode | Contact | String | String | | 80 | Yes | Yes |
| Zip Code | Mailing Zip/Postal Cde | MailingPostalCode | Contact | String | String | | 20 | Yes | Yes |
| Social Security Number | SSN | SSN__c | Contact | String | String | | 9 | Yes | Yes |
| Contact Role | Contact Role | Contact_Role__c | Contact | String | Multipicklist | | 4099 | Yes | Yes |
| % Ownership | % Owned | Percentage_Owned__c | Contact | Double | Percent | | 16 | Yes | Yes |
| Title | Title | Title | Contact | String | String | | 128 | Yes | Yes |
| Country | Mailing Country Code | MailingCountryCode | Contact | String | Picklist | | 10 | Yes | Yes |

## Transaction Information

**Transaction Information Fields**

| **Custom UI Field Name** | **Salesforce Field Label** | **Salesforce Field API Name** | **Object** | **Data Type** | **Salesforce Data Type** | **Picklist Values** | **Length** | **Required WP** | **Required PaaS** |
| ------------------ | -------------------------- | -------------------------- | ------------------ | -------------- | -------------------------- | -------------------- | ---------------- | -------------------- | -------------------- |
| High Monthly Processing Volume | High Monthly Volume | High_Monthly_Volume__c | Opportunity | Double | Currency | | 10 | No | No |
| Average Ticket | Avg Ticket | Avg_Ticket__c | Opportunity | Double | Formula (Currency) | | 0 | No | No |
| High Ticket | High Ticket | High_Ticket__c | Opportunity | Double | Currency | | 16 | No | No |
| Visa/MC/Discover Volume | Visa/MC/Discover Volume | Visa_MC_Discover_Volume__c | Opportunity | Double | Currency | | 16 | No | No |
| Days to Ship/Deliver | Days to Ship/Deliver | Days_to_Ship_Deliver__c | Opportunity | String | Picklist | | 255 | No | No |
| Refund Policy | Refund Policy | Refund_Policy__c | Opportunity | String | Picklist | | 255 | No | No |

# API Validation Rules

The API performs a set of validation rules applied to any request received.

**Note:** If any validation errors occur, the entire request is rejected and no information is saved. See the table below for validation rules and the errors returned in the response.

**Account Rules**

| **Object** | **Type** | **Error** | **Validation Level** |
| --------------- | ------------------------- | ---------------------------------------- | -------------------- |
| Account | Name field is empty | Upsert failed. First exception on row 0; first error: REQUIRED_FIELD_MISSING, Required fields are missing: [Name]: [Name] | Standard |
| Account | Phone field should be 10 digits | Upsert failed. First exception on row 0; first error: FIELD_CUSTOM_VALIDATION_EXCEPTION, Phone/Fax number must be a 10-digit number or in (999) 999-9999 format.: [] | Standard |
| Account | Fax field should be 10 digits | Upsert failed. First exception on row 0; first error: FIELD_CUSTOM_VALIDATION_EXCEPTION, Phone/Fax number must be a 10-digit number or in (999) 999-9999 format.: [] | Standard |
| Account | Legal Zipcode field should be 5 digits | "Legal Address Zipcode: Enter Valid Legal Address Zipcode" | Standard |
| Account | DBA Address Zipcode field should be 5 digits | "DBA Address Zipcode: Enter valid DBA Address Zipcode" | Standard |
| Account | Mailing Zipcode should be 5 digits | "Mailing Address Zipcode: Enter valid Mailing Address Zipcode" | Standard |
| Account | App Email format should be valid email format | "Enter valid Email Address" | Standard |
| Account | Federal Tax ID field should be 9 digits | "Federal Tax ID: Enter valid Federal Tax ID" | Standard |
| Account | Legal Entity Type(Ownership) on Account should have a value when trying to create contacts/ Beneficial Owners. | "In order to add a Beneficial Owner you must select an Entity Type on the Merchant Information page" | Code |
| Account | Invalid Association ID | [-Entered value-] Association ID not found. | Code |
| Account | Invalid MCC | [-Entered value-] MCC not found | Code |
| Account | Invalid NAICS Category | [-Entered value-] NAICS Category not found | Code |
| Account | Invalid NAICS Sub-Category | [-Entered value-] NAICS Sub Category not found | Code |
| Account | Legal Name field is required (field type is Name) | "Legal Name: Enter valid Legal Name" | Standard |
| Account | Sponsorship field is required (field type is Picklist) | "Sponsorship is required" | Standard |

**Business Checking Account Rules**

| **Object** | **Type** | **Error** | **Validation Level** |
| --------------- | ------------------------- | ---------------------------------------- | -------------------- |
| Business Checking Account | DDA Type is Empty | Upsert failed. First exception on row 1; first error: REQUIRED_FIELD_MISSING, Required fields are missing: [DDA_Type__c] | Standard |
| Business Checking Account | Duplicate DDA Type Values (Not more than one Fee, Deposit and Chargeback DDA Type are allowed per Application) | "Message": "Only one Deposit Type account is allowed., Only one Chargeback Type account is allowed., Only one Fee Type account is allowed." | Code |
| Business Checking Account | Transit Routing Number should be of 9-digits | Upsert failed. First exception on row 0; first error: FIELD_CUSTOM_VALIDATION_EXCEPTION, Please enter a valid 9 digit number.: [Transit_Routing__c]" | Standard |

**Contact Rules**

| **Object** | **Type** | **Error** | **Validation Level** |
| --------------- | ------------------------- | ---------------------------------------- | -------------------- |
| Contact | SSN should be 9-digits | Upsert failed. First exception on row 1; first error: STRING_TOO_LONG, SSN: data value too large: *** encrypted text *** (max length=9): [SSN__c] | Standard |
| Contact | Only one Control Prong is allowed per Merchant Application. | Only one Control Prong Contact Roles allowed. | Code |
| Contact | Only one Authorized Signer is allowed per Merchant Application. | Only one Authorized Signer is allowed. | Code |
| Contact | Maximum of 4 Principals are allowed per Merchant Application. | Maximum 4.Principals are allowed. | Code |
| Contact | Only one Principal is allowed for a Sole Proprietorship | "Only 1 Principal allowed for Sole Proprietorship" | Standard |
| Contact | % Ownership is mandatory if the Contact Role has Principal in it. | % Owned: Principal owner must own 25% or more of the merchant | Code |
| Contact | % Ownership should have minimum of 25% when Principal was selected in Contact Role. | % Owned: Principal owner must own 25% or more of the merchant | Code |
| Contact | Sum of % Ownership for all the Contacts should not cross 100% (for Contacts with Contact Role as Principal). | Sum of % Owned should not be greater than 100% | Code |
| Contact | % Owned is not allowed for Non-Principal Contact Role. | % Owned is not allowed for Non-Principal Contact Role. | Code |
| Contact | Contact required fields based on Ownership field on Account | Required fields missing. (Refer sheet2 for the required contact field list based on Ownership field in Account.) | Code |
| Contact | Contact Role field is required (field type is Picklist) | "Contact Role is required" | Standard |

**Opportunity Rules**

| **Object** | **Type** | **Error** | **Validation Level** |
| --------------- | ------------------------- | ---------------------------------------- | -------------------- |
| Opportunity | 4 fields under "Card Acceptance Method" should sum up to 100% or can be left empty. | Card network volume fields must add up to 100% | Standard |
| Opportunity | 2 fields under "Customer Type" should sum up to 100% or can be left empty. | Customer Type fields must add up to 100%. | Standard |
| Opportunity | Account Type field is required (field type is Picklist) | "Account Type is required" | Standard |
| Opportunity | RecordTypeId is required as Direct Merchant (field type is RecordType) | "RecordTypeId is required as Direct Merchant" | Standard |

**Quote Line Item Rules**

| **Object** | **Type** | **Error** | **Validation Level** |
| --------------- | ------------------------- | ---------------------------------------- | -------------------- |
| Quote Line Items | Business_Hub_Account_Service_Monthly_Fee__c must be set to NULL, or to either 0 or 29.95 | "The monthly Business Hub-Account Service Monthly fee must be $29.95 or $0.00." | Standard |
| Quote Line Items | Only one field can be selected out of the below 3 fields in Quote Line Item/Fee Schedule Object:<br />- V/MC Diff Plus Plan<br />- V/MC Tiered Plan<br />- V/MC Pass Through Plan | Only One Fee schedule plan is allowed under Visa/Mastercard/Discover. | Standard |
| Quote Line Items | Only one checkbox can be selected out of the below 2 fields in Quote Line Item Object:<br />- Amex Bundled Plan<br />- Amex Pass Through Plan | Only One Fee schedule plan is allowed under American Express | Standard |
| Quote Line Items | If V/MC Diff. Plus Plan is checked, only the following list of fields can be submitted:<br />- V/MC Diff Plus Percentage<br />- V/MC Diff Plus Credit Qualified Per Item<br />- V/MC Diff Plus Credit Qualified Rate<br />- V/MC Diff Plus Debit Qualified Per Item<br />- V/MC Diff Plus Debit Qualified Rate | You cannot enter values in Visa/Mastercard/Discover Pass Through Plan fields and Visa/Mastercard/Discover Tiered Plan fields when Visa/Mastercard/Discover Diff Plus Plan is selected. | Standard |
| Quote Line Items | If V/MC Pass Through Plan is checked, only the following list of fields can be submitted:<br />- V/MC Pass Through Per Item<br />- V/MC Pass Through Rate | You cannot enter values in Visa/Mastercard/Discover Diff Plus Plan fields and Visa/Mastercard/Discover Tiered Plan fields when Visa/Mastercard/Discover Pass Through Plan is selected. | Standard |
| Quote Line Items | If V/MC Tiered Plan is checked, only the following list of fields can be submitted:<br />- V/MC Tiered Commercial Qual Per Item<br />- V/MC Tiered Commercial Qual Rate<br />- V/MC Tiered Credit Qualified Per Item<br />- V/MC Tiered Credit Qualified Rate<br />- V/MC Tiered Debit Qualified Per Item<br />- V/MC Tiered Debit Qualified Rate<br />- V/MC Tiered Mid Qual Per Item<br />- V/MC Tiered Mid Qual Rate<br />- V/MC Tiered Non Qual Per Item<br />- V/MC Tiered Non Qual Rate<br />- V/MC Tiered Rewards Rate<br />- V/MC Tiered Rewards Per Item | You cannot enter values in Visa/Mastercard/Discover Diff Plus Plan fields and Visa/Mastercard/Discover Pass Through Plan fields when Visa/Mastercard/Discover Tiered Plan is selected. | Standard |
| Quote Line Items | If Amex Pass Through Plan is checked, only the following list of fields can be submitted:<br />- Amex Pass Through Per Item<br />- Amex Pass Through Rate | You cannot enter values in American Express Bundled Plan fields when American Express Pass Through Plan is selected. | Standard |
| Quote Line Items | If Amex Bundled Plan is checked, only the following list of fields can be submitted:<br />- Amex Bundled Per Item<br />- Amex Bundled Rate | You cannot enter values in American Express Pass Through Plan fields when American Express Bundled Plan is selected. | Standard |

**Note:** If a field exceeds the maximum length, the value will be truncated. Fields included: Legal City, DBA City, Physical Address City, Legal Street, DBA Street, Physical Address Street, Legal Name, Account Name.

## Version History with Revisions and Changelog

| **Date** | **Change Description** |
| --------------- | ------------------------------------------------ |
| July 2026 | Updated source format. |
| March 2026 | Updated diagrams and address. |
| November 2023 | Added an explanation of Aggregate Net Funding. |
| October 2023 | Updated response examples. |
| September 2023 |  Beneficial Owner fields now required. |
| May 2023 |  Finalized content and formatting. |
| January 2023 |  Drafted Document |
