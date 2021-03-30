---
title: Cloud Partner-portal API-referentie-micro soft Commercial Marketplace
description: Beschrijving van, vereiste te gebruiken en lijst met Marketplace API-bewerkingen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 57d3d1c6bb14db3eb2ca499069934a628d2f7fea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92425784"
---
# <a name="cloud-partner-portal-api-reference"></a>Naslaginformatie over Cloud Partner-portal-API

> [!NOTE]
> De Cloud Partner-portal-Api's zijn geïntegreerd in en blijven werken in het partner centrum. De overgang introduceert kleine wijzigingen. Bekijk de [wijzigingen](#changes-to-cpp-apis-after-the-migration-to-partner-center) in de CCP-api's in dit document om te controleren of uw code blijft werken na het overstappen naar het partner centrum. CCP-Api's mogen alleen worden gebruikt voor bestaande producten die al zijn geïntegreerd vóór de overgang naar het partner centrum. nieuwe producten moeten de indienings-Api's van partner Center gebruiken.

Met de Cloud Partner-portal REST Api's kan werk belastingen, aanbiedingen en Publisher-profielen programmatisch worden opgehaald en gemanipuleerd. De Api's gebruiken Azure op rollen gebaseerd toegangs beheer (Azure RBAC) om de juiste machtigingen af te dwingen tijdens de verwerkings tijd.

Deze Naslag informatie bevat technische Details voor de Cloud Partner-portal REST-Api's. De payload-voor beelden in dit document zijn alleen ter referentie en zijn onderhevig aan wijzigingen wanneer er nieuwe functionaliteit wordt toegevoegd.

## <a name="prerequisites-and-considerations"></a>Vereisten en overwegingen

Voordat u de Api's kunt gebruiken, moet u het volgende controleren:

- Het artikel [vereisten](./cloud-partner-portal-api-prerequisites.md) vindt u informatie over het toevoegen van een service-principal aan uw account en het verkrijgen van een toegangs token voor Azure Active Directory (Azure AD) voor verificatie.
- De twee [gelijktijdigheids beheer](./cloud-partner-portal-api-concurrency-control.md) strategieën die beschikbaar zijn voor het aanroepen van deze api's.
- Aanvullende API- [overwegingen](./cloud-partner-portal-api-considerations.md), zoals versie beheer en fout afhandeling.

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>Wijzigingen in CCP-Api's na de migratie naar partner Center

| **API** | **Beschrijving wijziging** | **Impact** |
| ------- | ---------------------- | ---------- |
| POST publiceren, GoLive, annuleren | Voor gemigreerde aanbiedingen heeft de reactie header een andere indeling, maar blijft op dezelfde manier werken, waarbij een relatief pad wordt opgegeven om de bewerkings status op te halen. | Bij het verzenden van een van de bijbehorende POST-aanvragen voor een aanbieding heeft de locatie header een van de twee notaties, afhankelijk van de migratie status van de aanbieding:<ul><li>Niet-gemigreerde aanbiedingen<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Gemigreerde aanbiedingen<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| GET-bewerking | Voor aanbiedings typen waarvoor eerder het veld meldings-e in het antwoord wordt ondersteund, wordt dit veld afgeschaft en wordt het niet langer geretourneerd voor gemigreerde aanbiedingen. | Voor gemigreerde aanbiedingen worden er geen meldingen meer verzonden naar de lijst met e-mail berichten die in de aanvragen zijn opgegeven. In plaats daarvan wordt de API-service uitgelijnd met het e-mail proces voor meldingen in het partner centrum om e-mail berichten te verzenden. In het bijzonder worden meldingen verzonden naar het e-mail adres dat is ingesteld in het gedeelte contact gegevens van de verkoper van uw account instellingen in partner centrum om u te informeren over de voortgang van de bewerking.<br><br>Controleer het e-mail adres dat is ingesteld in het gedeelte contact gegevens van de verkoper van uw [account instellingen](https://partner.microsoft.com/dashboard/account/management) in partner centrum om te controleren of het juiste e-mail bericht is ontvangen voor meldingen.  |

## <a name="common-tasks"></a>Algemene taken

Deze referentie Details Api's om de volgende algemene taken uit te voeren.

### <a name="offers"></a>Aanbiedingen

- [Alle aanbiedingen ophalen](./cloud-partner-portal-api-retrieve-offers.md)
- [Een specifieke aanbieding ophalen](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [Aanbiedingsstatus ophalen](./cloud-partner-portal-api-retrieve-offer-status.md)
- [Een aanbieding maken](./cloud-partner-portal-api-creating-offer.md)
- [Een aanbieding publiceren](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operations

- [Bewerkingen ophalen](./cloud-partner-portal-api-retrieve-operations.md)
- [Bewerkingen annuleren](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Een app publiceren

- [Live gaan](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Overige taken

- [Prijzen voor aanbiedingen van virtuele machines instellen](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Problemen oplossen

- [Problemen met verificatiefouten oplossen](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
