---
title: Go Live API-Azure Marketplace
description: De Go Live API initieert het Live-aanbiedings proces van de aanbieding.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: d612b796f85c9eaab1600c55cde7e79acb49f352
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87292935"
---
# <a name="go-live"></a>Live gaan

> [!NOTE]
> De Cloud Partner-portal-Api's zijn geïntegreerd in en blijven werken in het partner centrum. De overgang introduceert kleine wijzigingen. Controleer de wijzigingen die worden vermeld in [Cloud Partner-Portal API-referentie](./cloud-partner-portal-api-overview.md) om ervoor te zorgen dat uw code blijft werken na het overstappen naar het partner centrum. CCP-Api's mogen alleen worden gebruikt voor bestaande producten die al zijn geïntegreerd vóór de overgang naar het partner centrum. nieuwe producten moeten de indienings-Api's van partner Center gebruiken.

Deze API start het proces voor het pushen van een app naar productie. Deze bewerking wordt meestal langdurig uitgevoerd. Deze aanroep gebruikt de lijst met e-mail meldingen van de bewerking [Publish](./cloud-partner-portal-api-publish-offer.md) API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>URI-para meters
--------------

|  **Naam**      |   **Beschrijving**                                                           | **Gegevenstype** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | De uitgevers-id van de aanbieding die u wilt ophalen, bijvoorbeeld `contoso`       |  Tekenreeks       |
| offerId        | Id van de aanbieding die moet worden opgehaald                                   |  Tekenreeks       |
| api-versie    | Nieuwste versie van de API                                                   |  Datum         |
|  |  |  |

## <a name="header"></a>Header
------

|  **Naam**       |     **Waarde**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Autorisatie   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Voor beeld van tekst

### <a name="response"></a>Antwoord

#### <a name="migrated-offers"></a>Gemigreerde aanbiedingen

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Niet-gemigreerde aanbiedingen

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Reactieheader

|  **Naam**             |      **Waarde**                                                            |
|  --------             |      ----------                                                           |
| Locatie    |  Het relatieve pad om de status van deze bewerking op te halen            |
|  |  |

### <a name="response-status-codes"></a>Antwoord status codes

| **Code** |  **Beschrijving**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` -De aanvraag is geaccepteerd. Het antwoord bevat een locatie voor het bijhouden van de bewerkings status. |
|  400     | `Bad/Malformed request` -Er zijn aanvullende fout gegevens gevonden in de hoofd tekst van het antwoord. |
|  404     |  `Not found` -De opgegeven entiteit bestaat niet.                                       |
|  |  |
