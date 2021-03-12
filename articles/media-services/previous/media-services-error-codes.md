---
title: Fout codes Azure Media Services | Microsoft Docs
description: U kunt HTTP-fout codes van de service ontvangen, afhankelijk van problemen zoals verificatie tokens die verlopen zijn voor acties die niet worden ondersteund in Media Services. Dit artikel bevat een overzicht van Azure Media Services v2 API-fout codes.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 10ade5c8f34d806ca5706ccaaf4b7ff2e05ba657
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013411"
---
# <a name="azure-media-services-error-codes"></a>Foutcodes van Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Wanneer u Microsoft Azure Media Services gebruikt, kunt u HTTP-fout codes van de service ontvangen, afhankelijk van problemen zoals verificatie tokens die niet worden ondersteund in Media Services. Hier volgt een lijst met **HTTP-fout codes** die kunnen worden geretourneerd door Media Services en de mogelijke oorzaken hiervoor.  

## <a name="400-bad-request"></a>400 Ongeldige aanvraag
De aanvraag bevat ongeldige gegevens en is geweigerd om een van de volgende redenen:

* Er is een niet-ondersteunde API-versie opgegeven. Zie voor de meest recente versie de [installatie van Media Services rest API ontwikkeling](media-services-rest-how-to-use.md).
* De API-versie van Media Services is niet opgegeven. Zie [Media Services Operations rest API Reference](/rest/api/media/operations/azure-media-services-rest-api-reference)(Engelstalig) voor meer informatie over het opgeven van de API-versie.
  
  > [!NOTE]
  > Als u de .NET-of Java-Sdk's gebruikt om verbinding te maken met Media Services, wordt de API-versie voor u opgegeven wanneer u probeert een actie uit te voeren op Media Services.
  > 
  > 
* Er is een niet-gedefinieerde eigenschap opgegeven. De naam van de eigenschap bevindt zich in het fout bericht. Alleen de eigenschappen die lid zijn van een bepaalde entiteit kunnen worden opgegeven. Zie [Azure Media Services rest API verwijzing](/rest/api/media/operations/azure-media-services-rest-api-reference) voor een lijst met entiteiten en de bijbehorende eigenschappen.
* Er is een ongeldige eigenschaps waarde opgegeven. De naam van de eigenschap bevindt zich in het fout bericht. Zie de vorige koppeling voor geldige eigenschaps typen en de bijbehorende waarden.
* Er ontbreekt een eigenschaps waarde en deze is vereist.
* Onderdeel van de opgegeven URL bevat een ongeldige waarde.
* Er is een poging gedaan om een eigenschap WriteOnce bij te werken.
* Er is een poging gedaan een taak te maken die een invoer element heeft met een primaire AssetFile die niet is opgegeven of niet kan worden bepaald.
* Er is een poging gedaan om een SAS-Locator bij te werken. SAS-Locators kunnen alleen worden gemaakt of verwijderd. Streaming-Locators kunnen worden bijgewerkt. Zie voor meer informatie [Locators](/rest/api/media/operations/locator).
* Er is een niet-ondersteunde bewerking of query verzonden.

## <a name="401-unauthorized"></a>401 Onbevoegd
De aanvraag kan niet worden geverifieerd (voordat deze kan worden geautoriseerd) vanwege een van de volgende redenen:

* Verificatie header ontbreekt.
* Ongeldige waarde voor verificatie-header.
  * Het token is verlopen. 
  * Het token bevat een ongeldige hand tekening.

## <a name="403-forbidden"></a>403 Verboden
De aanvraag is niet toegestaan vanwege een van de volgende redenen:

* Het Media Services-account is niet gevonden of is verwijderd.
* Het Media Services-account is uitgeschakeld en het aanvraag type is niet HTTP GET. Met Service bewerkingen wordt ook een 403-antwoord geretourneerd.
* Het verificatie token bevat niet de referentie gegevens van de gebruiker: AccountName en/of SubscriptionId. U vindt deze informatie in de Media Services UI-extensie voor uw Media Services-account in de Azure Beheerportal.
* Kan geen toegang krijgen tot de resource.
  
  * Er is geprobeerd een MediaProcessor te gebruiken die niet beschikbaar is voor uw Media Services-account.
  * Er is een poging gedaan om een door Media Services gedefinieerde JobTemplate bij te werken.
  * Er is een poging gedaan om een andere Locator van het Media Services-account te overschrijven.
  * Er is een poging gedaan om een andere ContentKey van het account Media Services te overschrijven.
* De resource kan niet worden gemaakt vanwege een service quotum dat is bereikt voor het Media Services-account. Zie [quota's en beperkingen](media-services-quotas-and-limitations.md)voor meer informatie over de service quota's.

## <a name="404-not-found"></a>404 Niet gevonden
De aanvraag is niet toegestaan op een resource om een van de volgende redenen:

* Er is een poging gedaan om een entiteit die niet bestaat, bij te werken.
* Er is een poging gedaan om een entiteit te verwijderen die niet bestaat.
* Er is een poging gedaan om een entiteit te maken die is gekoppeld aan een entiteit die niet bestaat.
* Er is een poging gedaan om een entiteit te verkrijgen die niet bestaat.
* Er is een poging gedaan een opslag account op te geven dat niet is gekoppeld aan het Media Services-account.  

## <a name="409-conflict"></a>409 Conflict
De aanvraag is niet toegestaan vanwege een van de volgende redenen:

* Meer dan één AssetFile heeft de opgegeven naam binnen de Asset.
* Er is een poging gedaan om een tweede primaire AssetFile te maken binnen de Asset.
* Er is geprobeerd een ContentKey te maken met de opgegeven id die al wordt gebruikt.
* Er is een poging gedaan om een Locator te maken met de opgegeven id die al wordt gebruikt.
* Meer dan één IngestManifestFile heeft de opgegeven naam in de IngestManifest.
* Er is geprobeerd om een tweede ContentKey te koppelen aan de door opslag versleutelde Asset.
* Er is geprobeerd om hetzelfde ContentKey te koppelen aan de Asset.
* Er is een poging gedaan om een Locator te maken voor een Asset waarvan de opslag container ontbreekt of niet langer is gekoppeld aan de Asset.
* Er is een poging gedaan om een Locator te maken voor een Asset waarvoor al vijf locators in gebruik zijn. (Azure Storage dwingt de limiet van vijf Shared Access-beleids regels af op één opslag container.)
* Het koppelen van het opslag account van een Asset aan een IngestManifestAsset is niet hetzelfde als het opslag account dat wordt gebruikt door de bovenliggende IngestManifest.  

## <a name="500-internal-server-error"></a>500 Interne serverfout
Tijdens de verwerking van de aanvraag wordt door Media Services een fout aangetroffen waardoor de verwerking niet kan worden voortgezet. Dit kan een van de volgende oorzaken hebben:

* Het maken van een activum of taak is mislukt omdat de service quotum gegevens van het Media Services-account tijdelijk niet beschikbaar zijn.
* Het maken van een activa-of IngestManifest Blob-opslag container mislukt omdat de gegevens van het opslag account van het account tijdelijk niet beschikbaar zijn.
* Andere onverwachte fout.

## <a name="503-service-unavailable"></a>503 Service niet beschikbaar
De server kan momenteel geen aanvragen ontvangen. Deze fout kan zijn veroorzaakt door een uitzonderlijk hoog aantal aanvragen bij de service. Het resourceverbruik wordt met het beperkingsmechanisme van Media Services beperkt voor toepassingen die een uitzonderlijk hoog aantal aanvragen doen bij de service.

> [!NOTE]
> Controleer het foutbericht en de tekenreeks voor de foutcode voor meer gedetailleerde informatie over de reden waarom u de 503-fout hebt ontvangen. Deze fout duidt niet altijd op beperking.
> 
> 

Mogelijke status beschrijvingen zijn:

* ' De server is bezet. Eerdere uitvoeringen van dit type aanvraag duurde meer dan {0} seconden. "
* ' De server is bezet. Meer dan {0} aanvragen per seconde kunnen worden beperkt. "
* ' De server is bezet. Meer dan {0} aanvragen binnen {1} enkele seconden kunnen worden beperkt. "

Als u deze fout wilt afhandelen, raden we u aan om exponentiële back-out-logica te gebruiken. Dit betekent dat het gebruik van geleidelijk meer wacht tijden voor opeenvolgende fout reacties.  Zie voor meer informatie het [toepassings blok voor tijdelijke fout verwerking](/previous-versions/msp-n-p/hh680905(v=pandp.50)).

> [!NOTE]
> Als u [Azure Media Services SDK voor .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master)gebruikt, is de logica voor opnieuw proberen voor de 503-fout geïmplementeerd door de SDK.  
> 
> 

## <a name="see-also"></a>Zie ook
[Fout codes voor Media Services beheer](/rest/api/media/)

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
