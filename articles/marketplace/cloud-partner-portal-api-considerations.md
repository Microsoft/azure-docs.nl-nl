---
title: API-overwegingen-Azure Marketplace
description: Problemen met versie beheer, fout afhandeling en autorisatie bij het gebruik van Marketplace-Api's.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 0920bec160874f27c8b1b6f2132951b57719b31c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88035277"
---
# <a name="api-considerations"></a>Overwegingen voor API's

<a name="api-versioning"></a>API-versie beheer
--------------

> [!NOTE]
> De Cloud Partner-portal-Api's zijn geïntegreerd in en blijven werken in het partner centrum. De overgang introduceert kleine wijzigingen. Controleer de wijzigingen die worden vermeld in [Cloud Partner-Portal API-referentie](./cloud-partner-portal-api-overview.md) om ervoor te zorgen dat uw code blijft werken na het overstappen naar het partner centrum. CCP-Api's mogen alleen worden gebruikt voor bestaande producten die al zijn geïntegreerd vóór de overgang naar het partner centrum. nieuwe producten moeten de indienings-Api's van partner Center gebruiken.

Er kunnen meerdere versies van de API tegelijk beschikbaar zijn. Clients moeten aangeven welke versie ze willen gebruiken door de para meter op te geven `api-version` als onderdeel van de query teken reeks.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Het antwoord op een aanvraag met een onbekende of ongeldige API-versie is een HTTP-code 400. Deze fout retourneert de verzameling van bekende API-versies in de antwoord tekst.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Fouten
------

De API reageert op fouten met de bijbehorende HTTP-status codes en optioneel extra informatie in het antwoord dat als JSON is geserialiseerd.
Wanneer u een fout bericht ontvangt, met name een 400-klasse fout, moet u de aanvraag niet opnieuw uitvoeren voordat u de onderliggende oorzaak herstelt. Corrigeer bijvoorbeeld in het bovenstaande voor beeld van het antwoord de API-versie parameter voordat u de aanvraag opnieuw verzendt.

<a name="authorization-header"></a>Autorisatie-header
--------------------

Voor alle Api's in deze Naslag informatie moet u de autorisatie-header door geven, samen met het Bearer-token dat is verkregen van Azure Active Directory (Azure AD). Deze header moet een geldig antwoord ontvangen. Als deze niet aanwezig `401 Unauthorized` is, wordt er een fout geretourneerd. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
