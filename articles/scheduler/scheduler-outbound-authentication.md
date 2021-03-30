---
title: Uitgaande verificatie
description: Meer informatie over het instellen of verwijderen van uitgaande verificatie voor Azure scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 0a8d79af9f45731971cb1be1f39fc193f9d0f0d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80878966"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Uitgaande verificatie voor Azure scheduler

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) vervangt Azure scheduler, die buiten gebruik wordt [gesteld](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Als u wilt blijven werken met de taken die u in scheduler hebt ingesteld, moet u zo snel mogelijk [naar Azure Logic apps worden gemigreerd](../scheduler/migrate-from-scheduler-to-logic-apps.md) . 
>
> Scheduler is niet meer beschikbaar in de Azure Portal, maar de [rest API](/rest/api/scheduler) en [Azure scheduler Power shell-cmdlets](scheduler-powershell-reference.md) blijven op dit moment beschikbaar, zodat u uw taken en taak verzamelingen kunt beheren.

Azure scheduler-taken moeten mogelijk services aanroepen waarvoor verificatie is vereist, zoals andere Azure-Services, Salesforce.com, Facebook en beveiligde aangepaste websites. Met de aangeroepen service kunt u bepalen of de scheduler-taak toegang heeft tot de aangevraagde resources. 

Scheduler ondersteunt deze verificatie modellen: 

* Verificatie van *client* certificaten bij gebruik van SSL/TLS-client certificaten
* *Basis* verificatie
* *Active Directory OAuth* -verificatie

## <a name="add-or-remove-authentication"></a>Verificatie toevoegen of verwijderen

* Als u verificatie wilt toevoegen aan een planner taak, voegt u, wanneer u de taak maakt of bijwerkt, het `authentication` onderliggende element JavaScript object Notation (JSON) toe aan het- `request` element. 

  Antwoorden retour neren nooit geheimen die worden door gegeven aan de Scheduler-service via een PUT-, PATCH-of POST-aanvraag in het- `authentication` object. 
  Antwoorden stellen geheime gegevens in op null of kunnen een openbaar token gebruiken dat de geverifieerde entiteit vertegenwoordigt. 

* Als u de verificatie van een planner taak wilt verwijderen, voert u een PUT-of PATCH-aanvraag voor de taak expliciet uit en stelt `authentication` u het object in op null. Het antwoord bevat geen verificatie-eigenschappen.

## <a name="client-certificate"></a>Client certificaat

### <a name="request-body---client-certificate"></a>Aanvraag tekst-client certificaat

Wanneer u verificatie toevoegt met behulp van het `ClientCertificate` model, geeft u deze extra elementen op in de hoofd tekst van de aanvraag.  

| Element | Vereist | Beschrijving |
|---------|----------|-------------|
| **verificatie** (bovenliggend element) | Het verificatie object voor het gebruik van een SSL/TLS-client certificaat |
| **type** | Ja | Het verificatie type. Voor SSL/TLS-client certificaten is de waarde `ClientCertificate` . |
| **pfx** | Ja | De met base64 gecodeerde inhoud van het PFX-bestand |
| **password** | Ja | Het wacht woord voor toegang tot het PFX-bestand |
||| 

### <a name="response-body---client-certificate"></a>Antwoord tekst-client certificaat 

Wanneer een aanvraag wordt verzonden met verificatie gegevens, bevat het antwoord deze verificatie-elementen.

| Element | Beschrijving | 
|---------|-------------| 
| **verificatie** (bovenliggend element) | Het verificatie object voor het gebruik van een SSL/TLS-client certificaat |
| **type** | Het verificatie type. Voor SSL/TLS-client certificaten is de waarde `ClientCertificate` . |
| **certificateThumbprint** |De vinger afdruk van het certificaat |
| **certificateSubjectName** |De DN-naam van het certificaat onderwerp |
| **certificateExpiration** | De verval datum van het certificaat |
||| 

### <a name="sample-rest-request---client-certificate"></a>Voor beeld van REST-aanvraag-client certificaat

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---client-certificate"></a>Voor beeld REST-antwoord-client certificaat

```json
HTTP/1.1 200 OKCache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="basic"></a>Basic

### <a name="request-body---basic"></a>Hoofd tekst van aanvraag-basis

Wanneer u verificatie toevoegt met behulp van het `Basic` model, geeft u deze extra elementen op in de hoofd tekst van de aanvraag.

| Element | Vereist | Beschrijving |
|---------|----------|-------------|
| **verificatie** (bovenliggend element) | Het verificatie object voor het gebruik van basis verificatie | 
| **type** | Ja | Het verificatie type. De waarde is voor basis verificatie `Basic` . | 
| **gebruikers** | Ja | De gebruikers naam voor verificatie | 
| **password** | Ja | Het wacht woord voor verificatie |
|||| 

### <a name="response-body---basic"></a>Antwoord tekst-basis

Wanneer een aanvraag wordt verzonden met verificatie gegevens, bevat het antwoord deze verificatie-elementen.

| Element | Beschrijving | 
|---------|-------------|
| **verificatie** (bovenliggend element) | Het verificatie object voor het gebruik van basis verificatie |
| **type** | Het verificatie type. De waarde is voor basis verificatie `Basic` . |
| **gebruikers** | De geverifieerde gebruikers naam |
||| 

### <a name="sample-rest-request---basic"></a>Voor beeld van REST-aanvraag-basis

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---basic"></a>Voor beeld REST-antwoord-basis

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"Http"
      },
      "recurrence":{  
         "frequency":"Minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"Enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="active-directory-oauth"></a>Active Directory OAuth

### <a name="request-body---active-directory-oauth"></a>Aanvraag tekst-Active Directory OAuth 

Wanneer u verificatie toevoegt met behulp van het `ActiveDirectoryOAuth` model, geeft u deze extra elementen op in de hoofd tekst van de aanvraag.

| Element | Vereist | Beschrijving |
|---------|----------|-------------|
| **verificatie** (bovenliggend element) | Ja | Het verificatie object voor het gebruik van ActiveDirectoryOAuth-verificatie |
| **type** | Ja | Het verificatie type. De waarde is voor ActiveDirectoryOAuth-verificatie `ActiveDirectoryOAuth` . |
| **tenant** | Ja | De Tenant-id voor de Azure AD-Tenant. Voer in Azure PowerShell om de Tenant-id voor de Azure AD-Tenant te vinden `Get-AzureAccount` . |
| **gericht** | Ja | Deze waarde is ingesteld op `https://management.core.windows.net/` . | 
| **clientId** | Ja | De client-id voor de Azure AD-toepassing | 
| **geheim** | Ja | Het geheim voor de client die het token aanvraagt | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Antwoord tekst-Active Directory OAuth

Wanneer een aanvraag wordt verzonden met verificatie gegevens, bevat het antwoord deze verificatie-elementen.

| Element | Beschrijving |
|---------|-------------|
| **verificatie** (bovenliggend element) | Het verificatie object voor het gebruik van ActiveDirectoryOAuth-verificatie |
| **type** | Het verificatie type. De waarde is voor ActiveDirectoryOAuth-verificatie `ActiveDirectoryOAuth` . | 
| **tenant** | De Tenant-id voor de Azure AD-Tenant |
| **gericht** | Deze waarde is ingesteld op `https://management.core.windows.net/` . |
| **clientId** | De client-id voor de Azure AD-toepassing |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>Voor beeld van REST-aanvraag-Active Directory OAuth

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "Http"
    },
    "recurrence": {
      "frequency": "Minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "Enabled"
  }
}
```

### <a name="sample-rest-response---active-directory-oauth"></a>Voor beeld REST-antwoord-Active Directory OAuth

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{
   "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type": "Microsoft.Scheduler/jobCollections/jobs",
   "name": "southeastasiajc/httpjob",
   "properties": {
      "startTime": "2015-05-14T14:10:00Z",
      "action": {  
         "request": {
            "uri": "https://mywebserviceendpoint.com",
            "method": "GET",
            "headers": {  
               "x-ms-version": "2013-03-01"
            },
            "authentication": {  
               "tenant": "microsoft.onmicrosoft.com",
               "audience": "https://management.core.windows.net/",
               "clientId": "dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type": "ActiveDirectoryOAuth"
            }
         },
         "type": "Http"
      },
      "recurrence": {  
         "frequency": "minute",
         "endTime": "2016-04-10T08:00:00Z",
         "interval": 1
      },
      "state": "Enabled",
      "status": {  
         "lastExecutionTime": "2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime": "2016-03-16T19:11:00Z",
         "executionCount": 5,
         "failureCount": 5,
         "faultedCount": 1
      }
   }
}
```

## <a name="next-steps"></a>Volgende stappen

* [Azure Scheduler-concepten en terminologie entiteitenhiërarchie](scheduler-concepts-terms.md)
* [Azure Scheduler-limieten, standaardwaarden en foutcodes](scheduler-limits-defaults-errors.md)
* [Naslaginformatie over REST API van Azure Scheduler](/rest/api/scheduler)
* [Naslaginformatie over Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md)