---
title: Cast Labs gebruiken om Widevine-licenties te leveren aan Azure Media Services | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Azure Media Services (AMS) kunt gebruiken voor het leveren van een stroom die dynamisch wordt versleuteld door AMS met zowel PlayReady als Widevine DRMs.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: willzhan
ms.openlocfilehash: 576ac636f166e2daebbb9919d6666fea913a17be
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017049"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>castLabs gebruiken om Widevine-licenties te leveren aan Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Overzicht

In dit artikel wordt beschreven hoe u Azure Media Services (AMS) kunt gebruiken voor het leveren van een stroom die dynamisch wordt versleuteld door AMS met zowel PlayReady als Widevine DRMs. De PlayReady-licentie is afkomstig van Media Services PlayReady-licentie server en de Widevine-licentie wordt geleverd door de **cast Labs** -licentie server.

Voor het afspelen van inhoud die is beveiligd met CENC (PlayReady en/of Widevine), kunt u  [Azure Media Player](https://aka.ms/azuremediaplayer)gebruiken. Zie het [amp-document](https://amp.azure.net/libs/amp/latest/docs/) voor meer informatie.

In het volgende diagram ziet u een architectuur op hoog niveau Azure Media Services-en cast Labs-integratie.

![integration](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Standaard configuratie van het systeem

* Media-inhoud wordt opgeslagen in AMS.
* Sleutel-Id's van inhouds sleutels worden opgeslagen in zowel cast Labs als AMS.
* Cast Labs en AMS hebben beide token verificatie ingebouwd. In de volgende secties worden verificatie tokens besproken. 
* Wanneer een client aanvragen voor het streamen van de video, wordt de inhoud dynamisch versleuteld met **common Encryption** (CENC) en dynamisch verpakt door AMS naar Smooth streaming en streepje. We leveren ook PlayReady M2TS-elementaire stroom versleuteling voor HLS streaming-protocol.
* PlayReady-licentie wordt opgehaald uit de AMS-licentie server en de Widevine-licentie wordt opgehaald van de cast Labs-licentie server. 
* Media Player bepaalt automatisch welke licentie moet worden opgehaald op basis van de functionaliteit van het client platform. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Het genereren van het verificatie token voor het verkrijgen van een licentie

Cast Labs en AMS ondersteunen JWT (JSON Web Token)-token indeling die wordt gebruikt voor het autoriseren van een licentie. 

### <a name="jwt-token-in-ams"></a>JWT-token in AMS

In de volgende tabel wordt JWT-token in AMS beschreven. 

| Verlener | De uitgevende teken reeks van de gekozen Secure token service (STS) |
| --- | --- |
| Doelgroep |Reeks publiek van de gebruikte STS |
| Claims |Een set claims |
| NotBefore |De geldigheid van het token starten |
| Verloopt |Eind geldigheid van het token |
| SigningCredentials |De sleutel die wordt gedeeld tussen PlayReady-licentie server, cast Labs-licentie server en STS, kan een symmetrische of asymmetrische sleutel zijn. |

### <a name="jwt-token-in-castlabs"></a>JWT-token in cast Labs

In de volgende tabel wordt JWT-token in cast Labs beschreven. 

| Naam | Beschrijving |
| --- | --- |
| optData |Een JSON-teken reeks met informatie over u. |
| CRT |Een JSON-teken reeks met informatie over de Asset, de licentie gegevens en de weergave rechten. |
| iat |De huidige datum/tijd in de epoche. |
| jti |Een unieke id over dit token (elke token kan slechts één keer worden gebruikt in het cast Labs-systeem). |

## <a name="sample-solution-setup"></a>Installatie van de voorbeeld oplossing

De [voorbeeld oplossing](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) bestaat uit twee projecten:

* Een console-app die kan worden gebruikt om DRM-beperkingen op een al opgenomen asset in te stellen voor zowel PlayReady als Widevine.
* Een webtoepassing die tokens doorwerkt die kan worden gezien als een zeer vereenvoudigde versie van een STS.

De console toepassing gebruiken:

1. Wijzig de app.config voor het instellen van AMS-referenties, cast Labs-referenties, STS-configuratie en gedeelde sleutel.
2. Upload een asset in AMS.
3. Haal de UUID op uit de geüploade Asset en wijzig regel 32 in het Program.cs-bestand:
   
      var objIAsset = _context. Assets. where (x => x.Id = = "NB: Cid: UUID: dac53a5d-1500-80bd-b864-f1e4b62594cf"). FirstOrDefault ();
4. Gebruik een AssetId voor het benoemen van de asset in het cast Labs-systeem (regel 44 in het Program.cs-bestand).
   
   U moet AssetId instellen voor **cast Labs**; Dit moet een unieke alfanumerieke teken reeks zijn.
5. Voer het programma uit.

De Web Application (STS) gebruiken:

1. Wijzig de web.config in Setup cast Labs handelaar-ID, de STS-configuratie en de gedeelde sleutel.
2. Implementeren naar Azure websites.
3. Ga naar de website.

## <a name="playing-back-a-video"></a>Een video afspelen

Als u een video wilt afspelen die is versleuteld met een gemeen schappelijke versleuteling (PlayReady en/of Widevine), kunt u de [Azure Media Player](https://aka.ms/azuremediaplayer)gebruiken. Wanneer u de console-app uitvoert, worden de inhouds sleutel-ID en de manifest-URL geechod.

1. Open een nieuw tabblad en start uw STS: http://[yourStsName]. azurewebsites. net/API/token/AssetID/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Ga naar [Azure Media Player](https://aka.ms/azuremediaplayer).
3. Plak de streaming-URL.
4. Klik op het selectie vakje **Geavanceerde opties** .
5. Selecteer PlayReady en/of Widevine in de vervolg keuzelijst **beveiliging** .
6. Plak het token dat u hebt ontvangen van uw STS in het tekstvak token. 
   
   De castLab-licentie server heeft het voor voegsel ' Bearer = ' vóór het token niet nodig. Verwijder deze voordat u het token verzendt.
7. Werk de speler bij.
8. De video moet worden afgespeeld.

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

