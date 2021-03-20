---
title: Azure Data Lake Analytics beheren met Azure SDK voor Node.js
description: In dit artikel wordt beschreven hoe u de Azure SDK voor Node.js gebruikt om een Data Lake Analytics-accounts, gegevensbronnen, taken en gebruikers te beheren.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/05/2016
ms.custom: devx-track-js
ms.openlocfilehash: ca1c865b31739e7163b0db26cb666a8a42edbaa2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92218456"
---
# <a name="manage-azure-data-lake-analytics-using-azure-sdk-for-nodejs"></a>Azure Data Lake Analytics beheren met Azure SDK voor Node.js
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

In dit artikel wordt beschreven hoe u Azure Data Lake Analytics, gegevensbronnen, gebruikers en taken beheert met behulp van een app die is geschreven met Azure SDK voor Node.js. 

De volgende versies worden ondersteund:
* **Node.js versie: 0.10.0 of hoger**
* **REST-API-versie voor Account: 2015-10-01-preview**
* **REST-API-versie voor Catalog: 2015-10-01-preview**
* **REST-API-versie voor Job: 2016-03-20-preview**

## <a name="features"></a>Functies
* Accountbeheer: maken, ophalen, weergeven, bijwerken en verwijderen.
* Taakbeheer: verzenden, ophalen, weergeven en annuleren.
* Catalogusbeheer: ophalen en weergeven.

## <a name="how-to-install"></a>Installeren
```bash
npm install azure-arm-datalake-analytics
```

## <a name="authenticate-using-azure-active-directory"></a>Verifiëren met Azure Active Directory
 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-analytics-client"></a>De Data Lake Analytics-client maken
```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var accountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## <a name="create-a-data-lake-analytics-account"></a>Een Data Lake Analytics-account maken
```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-jobs"></a>Een lijst met taken ophalen
```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-databases-in-the-data-lake-analytics-catalog"></a>Een lijst met databases ophalen in de Data Lake Analytics-catalogus ophalen
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Zie ook
* [Microsoft Azure SDK voor Node.js](https://github.com/azure/azure-sdk-for-node)
