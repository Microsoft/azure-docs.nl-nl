---
title: Afhandeling van uitzonde ringen & scenario voor fout registratie
description: Praktijk gebruik en scenario voor geavanceerde uitzonde ring bij het verwerken en registreren van fouten in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: hedidin
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 07/29/2016
ms.openlocfilehash: fdf5f25ae6f89ccc06c95ee1be021691dab0047a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000348"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Scenario: Uitzonderingen en foutregistratie voor logische apps afhandelen

In dit scenario wordt beschreven hoe u een logische app kunt uitbreiden om de verwerking van uitzonde ringen beter te ondersteunen. We hebben een echt gebruiks voorbeeld gebruikt om de vraag te beantwoorden: "ondersteunt Azure Logic Apps uitzonde ring en fout afhandeling?"

> [!NOTE]
> Het huidige Azure Logic Apps schema biedt een standaard sjabloon voor antwoord op actie. Deze sjabloon bevat zowel interne validatie-als fout reacties die worden geretourneerd door een API-app.

## <a name="scenario-and-use-case-overview"></a>Overzicht van scenario-en use-cases

Dit is het verhaal van het gebruik voor dit scenario: 

Een bekende organisatie in de gezondheids zorg waarmee we een Azure-oplossing ontwikkelen die een patiënt Portal maakt met behulp van micro soft Dynamics CRM Online. Ze moeten afspraak records verzenden tussen de Dynamics CRM Online patiënten-Portal en Sales Force. We hebben u gevraagd om de [HL7 FHIR](https://www.hl7.org/implement/standards/fhir/) -standaard voor alle patiënten-records te gebruiken.

Het project heeft twee belang rijke vereisten:  

* Een methode voor het vastleggen van records die zijn verzonden vanuit de Dynamics CRM Online-Portal
* Een manier om fouten weer te geven die zijn opgetreden in de werk stroom

> [!TIP]
> Zie [integratie gebruikers groep](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "Integratie gebruikers groep")voor een video op hoog niveau over dit project.

## <a name="how-we-solved-the-problem"></a>Hoe we het probleem hebben opgelost

We hebben [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/ "Azure Cosmos DB") als opslag plaats voor het logboek en de fout records gekozen (Cosmos DB verwijst naar records als documenten). Omdat Azure Logic Apps een standaard sjabloon voor alle antwoorden heeft, hoeven we geen aangepast schema te maken. We kunnen een API-app maken om zowel fout-als logboek records in te **voegen** en op te **vragen** . Er kan ook een schema worden gedefinieerd voor elk in de API-app.  

Een andere vereiste was het opschonen van records na een bepaalde datum. Cosmos DB heeft een eigenschap met de naam [time to Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Time to Live") (TTL), die ons toestemming heeft verleend om voor elke record of verzameling een **time to Live** waarde in te stellen. Met deze mogelijkheid is de nood zaak om records in Cosmos DB hand matig te verwijderen, verwijderd.

> [!IMPORTANT]
> Voor het volt ooien van deze zelf studie moet u een Cosmos DB-Data Base en twee verzamelingen (logboek registratie en fouten) maken.

## <a name="create-the-logic-app"></a>De logische app maken

De eerste stap is het maken van de logische app en het openen van de app in Logic app Designer. In dit voor beeld maken we gebruik van bovenliggende en onderliggende Logic apps. We gaan ervan uit dat we het bovenliggende item al hebben gemaakt en één onderliggende logische app gaan maken.

Omdat we de record die u van Dynamics CRM Online komt, wilt registreren, beginnen we bovenaan. We moeten een **aanvraag** trigger gebruiken omdat de bovenliggende logische app dit onderliggende knoop punt activeert.

### <a name="logic-app-trigger"></a>Trigger voor logische app

We gebruiken een **aanvraag** trigger zoals wordt weer gegeven in het volgende voor beeld:

``` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

```


## <a name="steps"></a>Stappen

We moeten de bron (aanvraag) van de patiënt record in de online portal van Dynamics CRM registreren.

1. U moet een nieuw afspraak record ophalen uit Dynamics CRM Online.

   De trigger die afkomstig is van CRM biedt ons **de CRM-PatentId**, het **record type**, de **nieuwe of bijgewerkte record** (nieuwe of Booleaanse waarde van de update) en **SalesforceId**. De **SalesforceId** kan null zijn omdat deze alleen wordt gebruikt voor een update.
   De CRM-record wordt opgehaald via de CRM- **PatientID** en het **record type**.

2. Vervolgens moeten we de **InsertLogEntry** -bewerking van de SQL API-app voor Azure Cosmos DB toevoegen, zoals hier wordt weer gegeven in Logic app Designer.

   **Logboek vermelding invoegen**

   ![Scherm afbeelding van de ontwerp functie voor logische apps met de configuratie-instellingen voor InsertLogEntry.](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Fout vermelding invoegen**

   ![Scherm afbeelding van de ontwerp functie voor logische apps met de configuratie-instellingen voor CreateErrorRecord.](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Controleren op fout bij maken van record**

   ![Scherm opname van het CreateErrorRecord in de ontwerp functie voor logische apps waarin de velden worden weer gegeven voor het maken van een fout vermelding.](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Bron code van logische app

> [!NOTE]
> De volgende voor beelden zijn alleen voor beelden. Omdat deze zelf studie is gebaseerd op een implementatie die nu in productie is, kan de waarde van een **bron knooppunt** geen eigenschappen weer geven die betrekking hebben op het plannen van een afspraak. > 

### <a name="logging"></a>Logboekregistratie

In het volgende code voorbeeld van de logische app ziet u hoe u logboek registratie kunt afhandelen.

#### <a name="log-entry"></a>Logboek vermelding

Hier volgt de logische app-bron code voor het invoegen van een logboek vermelding.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Logboek aanvraag

Dit is het bericht van de logboek aanvraag dat is verzonden naar de API-app.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}"
        }
    }

```


#### <a name="log-response"></a>Antwoord in logboek registreren

Dit is het antwoord bericht in het logboek van de API-app.

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "/"0400fc2f-0000-0000-0000-575b3ff00000/"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Laten we nu eens kijken naar de stappen voor het afhandelen van fouten.

### <a name="error-handling"></a>Foutafhandeling

In het volgende code voorbeeld van de logische app ziet u hoe u fout afhandeling kunt implementeren.

#### <a name="create-error-record"></a>Fout record maken

Hier volgt de logische app-bron code voor het maken van een fout record.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}             
```

#### <a name="insert-error-into-cosmos-db--request"></a>Fout invoegen in Cosmos DB--aanvraag

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MasterID_mp__c/":/"/",/"C_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"ONY_ID__c/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-cosmos-db--response"></a>Fout invoegen in Cosmos DB--antwoord

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "/"0c00eaac-0000-0000-0000-575b3fdc0000/"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/":/"DO - Degree level is DO/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MterID_mp__c/":/"/",/"Medicis_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"XXXXXXX/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Fout bericht van Sales Force

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="return-the-response-back-to-parent-logic-app"></a>Het antwoord terugsturen naar de bovenliggende logische app

Nadat u het antwoord hebt ontvangen, kunt u het antwoord terugsturen naar de bovenliggende logische app.

#### <a name="return-success-response-to-parent-logic-app"></a>Retour resultaat van geslaagde reactie op bovenliggende logische app

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "    Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-parent-logic-app"></a>Fout bericht retour neren naar bovenliggende logische app

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="cosmos-db-repository-and-portal"></a>Cosmos DB opslagplaats en Portal

Onze oplossing heeft mogelijkheden toegevoegd met [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db).

### <a name="error-management-portal"></a>Portal voor fout beheer

Als u de fouten wilt weer geven, kunt u een MVC-Web-app maken om de fout records van Cosmos DB weer te geven. De bewerkingen **lijst**, **Details**, **bewerken** en **verwijderen** zijn opgenomen in de huidige versie.

> [!NOTE]
> Bewerkings bewerking: Cosmos DB vervangt het hele document. De records die worden weer gegeven in de **lijst** -en **detail** weergaven, zijn alleen voor beelden. Dit zijn niet echt patiënten-afspraak records.

Hier volgen enkele voor beelden van de details van de MVC-app die zijn gemaakt met de eerder beschreven aanpak.

#### <a name="error-management-list"></a>Lijst met fouten beheer
![Foutenlijst](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Detail weergave voor fouten beheer
![Foutdetails](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portal voor logboek beheer

Als u de logboeken wilt weer geven, hebt u ook een MVC-Web-App gemaakt. Hier volgen enkele voor beelden van de details van de MVC-app die zijn gemaakt met de eerder beschreven aanpak.

#### <a name="sample-log-detail-view"></a>Voorbeeld logboek detail weergave
![Detail weergave logboek](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Details van API-app

#### <a name="logic-apps-exception-management-api"></a>API voor uitzonde ringen beheer Logic Apps

Onze open-source Azure Logic Apps-API-app voor uitzonde ringen beheer biedt functionaliteit zoals hier wordt beschreven-er zijn twee controllers:

* **ErrorController** voegt een fout record (document) in een Azure Cosmos DB verzameling in.
* **LogController** Hiermee wordt een logboek record (document) in een Azure Cosmos DB verzameling ingevoegd.

> [!TIP]
> Beide controllers gebruiken `async Task<dynamic>` bewerkingen, waardoor bewerkingen tijdens runtime kunnen worden opgelost, zodat we het Azure Cosmos DB schema in de hoofd tekst van de bewerking kan maken. 
> 

Elk document in Azure Cosmos DB moet een unieke ID hebben. We gebruiken `PatientId` en toevoegen een tijds tempel dat is geconverteerd naar een Unix-Time Stamp waarde (double). De waarde voor het verwijderen van de Fractie waarde wordt afgekapt.

U kunt de bron code van de fout controller-API van [github](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs)weer geven.

De API wordt vanuit een logische app aangeroepen met behulp van de volgende syntaxis:

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

De expressie in het voor gaande code voorbeeld controleert op de *Create_NewPatientRecord* status **mislukt**.

## <a name="summary"></a>Samenvatting

* U kunt eenvoudig logboek registratie en fout afhandeling implementeren in een logische app.
* U kunt Azure Cosmos DB als opslag plaats gebruiken voor logboek-en fout records (documenten).
* U kunt MVC gebruiken om een portal te maken om logboek-en fout records weer te geven.

### <a name="source-code"></a>Broncode

De bron code voor de API-toepassing voor Logic Apps Exception Management is beschikbaar in deze [github-opslag plaats](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API voor uitzonde ringen voor logische apps").

## <a name="next-steps"></a>Volgende stappen

* [Meer voor beelden en scenario's van een logische app weer geven](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Logische apps bewaken](../logic-apps/monitor-logic-apps.md)
* [Implementatie van logische apps automatiseren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
