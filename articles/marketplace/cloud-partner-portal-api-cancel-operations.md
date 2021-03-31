---
title: Bewerkings-API annuleren-micro soft Commercial Marketplace
description: De API voor het annuleren van een bewerking die momenteel wordt uitgevoerd voor de aanbieding
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 095d9cf68374ee81cafdb6e7a32df29a57d122cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87287922"
---
# <a name="cancel-operation"></a>Bewerking annuleren

> [!NOTE]
> De Cloud Partner-portal-Api's zijn geïntegreerd in en blijven werken in het partner centrum. De overgang introduceert kleine wijzigingen. Controleer de wijzigingen die worden vermeld in [Cloud Partner-Portal API-referentie](./cloud-partner-portal-api-overview.md) om ervoor te zorgen dat uw code blijft werken na het overstappen naar het partner centrum. CCP-Api's mogen alleen worden gebruikt voor bestaande producten die al zijn geïntegreerd vóór de overgang naar het partner centrum. nieuwe producten moeten de indienings-Api's van partner Center gebruiken.

Deze API annuleert een bewerking die momenteel wordt uitgevoerd op de aanbieding. Gebruik de Get [Operations API](./cloud-partner-portal-api-retrieve-operations.md) om een `operationId` aan te geven aan deze API. Annulering is doorgaans een synchrone bewerking, maar in sommige complexe scenario's is het mogelijk dat er een nieuwe bewerking moet worden uitgevoerd om een bestaand item te annuleren. In dit geval bevat de HTTP-antwoord tekst de locatie van de bewerking die moet worden gebruikt voor het opvragen van de status.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-para meters

--------------

|  **Naam**    |      **Beschrijving**                                  |    **Gegevenstype**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Uitgevers-id, bijvoorbeeld `contoso`         |   Tekenreeks          |
| offerId      |  Aanbiedings-id                                     |   Tekenreeks          |
| api-versie  |  Huidige versie van API                               |    Datum           |
|  |  |  |

## <a name="header"></a>Header
------

|  **Naam**              |  **Waarde**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Autorisatie         |  Draagt uw TOKEN |
|  |  |

## <a name="body-example"></a>Voor beeld van tekst
------------

### <a name="request"></a>Aanvraag

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Eigenschappen van aanvraag hoofdtekst

|  **Naam**                |  **Beschrijving**                                               |
|  --------                |  ---------------                                               |
|  melding-e-mail berichten     | Door komma's gescheiden lijst met e-mail-Id's die moeten worden geïnformeerd over de voortgang van de publicatie bewerking. |
|  |  |

### <a name="response"></a>Antwoord

#### <a name="migrated-offers"></a>Gemigreerde aanbiedingen

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Niet-gemigreerde aanbiedingen

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Reactieheader

|  **Naam**             |    **Waarde**                       |
|  ---------            |    ----------                      |
| Locatie    | Het relatieve pad om de status van deze bewerking op te halen. |
|  |  |

### <a name="response-status-codes"></a>Antwoord status codes

| **Code**  |  **Beschrijving**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | OK. De aanvraag is verwerkt en de bewerking is synchroon geannuleerd. |
|  202      | Afgewezen. De aanvraag is verwerkt en de bewerking wordt momenteel geannuleerd. De locatie van de annulerings bewerking wordt geretourneerd in de reactie header. |
|  400      | Ongeldige/onjuiste aanvraag. De hoofd tekst van het fout bericht kan meer informatie geven.  |
|  403      | Toegang verboden. De client heeft geen toegang tot de naam ruimte die in de aanvraag is opgegeven. |
|  404      | Niet gevonden. De opgegeven entiteit bestaat niet. |
|  |  |
