---
title: Een specifieke aanbiedings-API ophalen-Azure Marketplace
description: API om de opgegeven aanbieding binnen de naam ruimte van de uitgever op te halen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 9f3ba6b2f13b9f2bb1d538db84723e3a9baaef12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87271838"
---
# <a name="retrieve-a-specific-offer"></a>Een specifieke aanbieding ophalen

> [!NOTE]
> De Cloud Partner-portal-Api's zijn geïntegreerd in en blijven werken in het partner centrum. De overgang introduceert kleine wijzigingen. Controleer de wijzigingen die worden vermeld in [Cloud Partner-Portal API-referentie](./cloud-partner-portal-api-overview.md) om ervoor te zorgen dat uw code blijft werken na het overstappen naar het partner centrum. CCP-Api's mogen alleen worden gebruikt voor bestaande producten die al zijn geïntegreerd vóór de overgang naar het partner centrum. nieuwe producten moeten de indienings-Api's van partner Center gebruiken.

Hiermee wordt de opgegeven aanbieding binnen de naam ruimte van de uitgever opgehaald.  

U kunt ook een bepaalde versie van de aanbieding ophalen of de aanbieding in concept-, weer gave-of productie sleuven ophalen. Als er geen sleuf is opgegeven, is de standaard waarde `draft` . Als u probeert een aanbieding op te halen die niet is voor bereid of gepubliceerd, resulteert dit in een `404 Not Found` fout.

> [!WARNING]
> De geheime waarden voor velden van het type geheim worden niet opgehaald door deze API.

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```

## <a name="uri-parameters"></a>URI-para meters

| **Naam**    | **Beschrijving**                                                                          | **Gegevenstype** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| publisherId | publisherId. Bijvoorbeeld contoso                                                        | Tekenreeks        |
| offerId     | De GUID die de aanbieding uniek identificeert.                                                 | Tekenreeks        |
| versie     | De versie van de aanbieding die wordt opgehaald. Standaard wordt de meest recente versie van de aanbieding opgehaald. | Geheel getal       |
| slotId      | De sleuf van waaruit de aanbieding wordt opgehaald, kan een van de volgende zijn:      <br/>  - `Draft` (standaard) haalt de aanbiedings versie op die momenteel in concept wordt aangeboden.  <br/>  -  `Preview` haalt de aanbiedings versie op die momenteel in preview is.     <br/>  -  `Production` haalt de aanbiedings versie op die momenteel in productie is.          |      vaste |
| api-versie | Nieuwste versie van API                                                                    | Datum          |
|  |  |  |

## <a name="header"></a>Header

|  **Naam**          |   **Waarde**            |
|  ---------------   |  --------------        |
|  Content-Type      | `application/json`     |
|  Autorisatie     | `Bearer YOUR_TOKEN`    |
|  |  |

## <a name="body-example"></a>Voor beeld van tekst

### <a name="response"></a>Antwoord

``` json
{
    "offerTypeId": "microsoft-azure-virtualmachines",
    "publisherId": "contoso",
    "status": "failed",
    "id": "059afc24-07de-4126-b004-4e42a51816fe",
    "version": 5,
    "definition": {
        "displayText": "Contoso Virtual Machine Offer",
        "offer": {
            "microsoft-azure-marketplace-testdrive.enabled": false,
            "microsoft-azure-marketplace-testdrive.videos": [],
            "microsoft-azure-marketplace.title": "Contoso App",
            "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
            "microsoft-azure-marketplace.allowedSubscriptions": [
                "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
            ],
            "microsoft-azure-marketplace.usefulLinks": [
            {
                "linkTitle": "Contoso App for Azure",
                "linkUrl": "https://azuremarketplace.microsoft.com"
            }
            ],
                "microsoft-azure-marketplace.categoryMap": [
                {
                "categoryL1": "analytics",
                "categoryL2-analytics": [
                "visualization-and-reporting"
                ]
                },
                {
                "categoryL1": "ai-plus-machine-learning",
                "categoryL2-ai-plus-machine-learning": [
                "bot-services",
                "cognitive-services",
                "other"
                ]
                }
            ],
            "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
            "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.screenshots": [],
            "microsoft-azure-marketplace.videos": [],
            "microsoft-azure-marketplace.leadDestination": "None",
            "microsoft-azure-marketplace.tableLeadConfiguration": {},
            "microsoft-azure-marketplace.blobLeadConfiguration": {},
            "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
            "microsoft-azure-marketplace.crmLeadConfiguration": {},
            "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
            "microsoft-azure-marketplace.marketoLeadConfiguration": {},
            "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
            "microsoft-azure-marketplace.termsOfUse": "Terms of use",
            "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
            "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.supportContactName": "Jon Doe",
            "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.publicAzureSupportUrl": "",
            "microsoft-azure-marketplace.fairfaxSupportUrl": ""
            },
            "plans": [
            {
                "planId": "contososkuidentifier",
                "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
                "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                "microsoft-azure-virtualmachines.cloudAvailability": [
                    "PublicAzure"
                ],
                "microsoft-azure-virtualmachines.certificationsFairfax": [],
                "virtualMachinePricing": {
                    "isByol": true,
                    "freeTrialDurationInMonths": 0
                },
                "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                "microsoft-azure-virtualmachines.windowsOSType": "Other",
                "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                "microsoft-azure-virtualmachines.recommendedVMSizes": [
                    "a0-basic",
                    "a0-standard",
                    "a1-basic",
                    "a1-standard",
                    "a2-basic",
                    "a2-standard"
                ],
                "microsoft-azure-virtualmachines.openPorts": [],
                "microsoft-azure-virtualmachines.vmImages": {
                    "1.0.1": {
                    "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                    "lunVhdDetails": []
                    }
                },
                "regions": [
                    "DZ",
                    "AR"
                ]
            }
            ]
        },
        "changedTime": "2017-06-07T06:15:39.7349221Z"
    }
}
```

### <a name="response-body-properties"></a>Eigenschappen van antwoord tekst

|  **Naam**       |   **Beschrijving**                                                                                                               |
|  -------------  |   -----------------------------------------------------------------------------------------------------                         |
|  offerTypeId    | Hiermee wordt het type aanbieding aangegeven                                                                                                    |
|  publisherId    | De unieke id van de uitgever                                                                                              |
|  status         | De status van de aanbieding. Voor een lijst met mogelijke waarden, zie de status van de [aanbieding](#offer-status) hieronder.                                  |
|  Id             | GUID die de aanbieding uniek identificeert                                                                                         |
|  versie        | Huidige versie van de aanbieding. De versie-eigenschap kan niet worden gewijzigd door de client. Deze wordt na elke publicatie verhoogd.    |
|  definitie     | Werkelijke definitie van de werk belasting                                                                                               |
|  changedTime    | UTC-datum/tijd waarop de aanbieding voor het laatst is gewijzigd                                                                                   |
|  |  |

### <a name="response-status-codes"></a>Antwoord status codes

| **Code**  | **Beschrijving**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` -De aanvraag is verwerkt en alle aanbiedingen onder de uitgever zijn geretourneerd naar de client.               |
|  400      | `Bad/Malformed request` -De hoofd tekst van het fout bericht bevat mogelijk meer informatie.                                                 |
|  403      | `Forbidden` -De client heeft geen toegang tot de opgegeven naam ruimte.                                                        |
|  404      | `Not found` -De opgegeven entiteit bestaat niet. De client moet de publisherId, offerId en versie controleren (indien opgegeven).      |
|  |  |

### <a name="offer-status"></a>Status van aanbieding

|  **Naam**                   |   **Beschrijving**                             |
| --------------------------- |  -------------------------------------------- |
|  NeverPublished             | De aanbieding is nooit gepubliceerd.               |
|  NotStarted                 | De aanbieding is nieuw, maar is niet gestart.              |
|  WaitingForPublisherReview  | Aanbieding wacht op goed keuring van de uitgever.      |
|  Wordt uitgevoerd                    | Verzen ding van aanbieding wordt verwerkt.          |
|  Geslaagd                  | Het verzenden van aanbiedingen is voltooid.    |
|  Geannuleerd                   | Het verzenden van het aanbod is geannuleerd.                |
|  Mislukt                     | Kan de aanbieding niet verzenden.                      |
|  |  |
