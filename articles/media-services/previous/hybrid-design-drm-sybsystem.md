---
title: Hybride ontwerp van DRM-subsysteem (en) met behulp van Azure Media Services | Microsoft Docs
description: In dit onderwerp wordt het hybride ontwerp van DRM-subsysteem (en) beschreven met behulp van Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: willzhan
ms.reviewer: juliako
ms.openlocfilehash: a48e761c4fb74802c6b1db63884ee192446720a4
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016267"
---
# <a name="hybrid-design-of-drm-subsystems"></a>Hybride ontwerp van DRM-subsystemen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

In dit onderwerp wordt het hybride ontwerp van DRM-subsysteem (en) beschreven met behulp van Azure Media Services.

## <a name="overview"></a>Overzicht

Azure Media Services biedt ondersteuning voor het volgende drie DRM-systeem:

* PlayReady
* Widevine (modulair)
* FairPlay

De DRM-ondersteuning omvat DRM-versleuteling (dynamische versleuteling) en licentie levering, met Azure Media Player ondersteuning voor alle drie DRMs als een browser speler-SDK.

Raadpleeg het document getiteld [CENC with multi-DRM en Access Control](media-services-cenc-with-multidrm-access-control.md)voor een gedetailleerde behandeling van het ontwerp en de implementatie van het CENC-subsysteem.

Hoewel we volledige ondersteuning bieden voor drie DRM-systemen, moeten klanten soms gebruikmaken van verschillende onderdelen van hun eigen infra structuur/subsystemen, naast Azure Media Services voor het bouwen van een hybride DRM-subsysteem.

Hieronder vindt u enkele veelgestelde vragen die door klanten worden gesteld:

* "Kan ik mijn eigen DRM-licentie servers gebruiken?" (In dit geval hebben klanten een DRM-licentie server farm met Inge sloten bedrijfs logica belegd).
* "Kan ik alleen uw DRM-licentie levering gebruiken in Azure Media Services zonder inhoud in AMS te hosten?"

## <a name="modularity-of-the-ams-drm-platform"></a>Modulariteit van het AMS DRM-platform

Als onderdeel van een uitgebreid video platform in de Cloud heeft Azure Media Services DRM een ontwerp met flexibiliteit en modulariteit. U kunt Azure Media Services gebruiken met een van de volgende combi naties die in de onderstaande tabel worden beschreven (een uitleg van de notatie die in de tabel wordt gebruikt). 

|**Inhoud hosten & oorsprong**|**Inhouds versleuteling**|**Levering van DRM-licentie**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Van derden|
|AMS|Van derden|AMS|
|AMS|Van derden|Van derden|
|Van derden|Van derden|AMS|

### <a name="content-hosting--origin"></a>Inhoud hosten & oorsprong

* AMS: video-Asset wordt gehost in AMS en streaming is via AMS streaming-eind punten (maar niet noodzakelijkerwijs dynamische pakketten).
* Van derden: video wordt gehost en geleverd op een streaming-platform van derden buiten AMS.

### <a name="content-encryption"></a>Inhouds versleuteling

* AMS: inhouds versleuteling wordt dynamisch uitgevoerd/op aanvraag door AMS dynamische versleuteling.
* Externe partij: inhouds versleuteling wordt uitgevoerd buiten AMS met behulp van een preprocessing-werk stroom.

### <a name="drm-license-delivery"></a>Levering van DRM-licentie

* AMS: DRM-licentie wordt geleverd door de AMS-licentie leverings service.
* Van derden: DRM-licentie wordt geleverd door een DRM-licentie server van derden buiten AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Configureren op basis van uw hybride scenario

### <a name="content-key"></a>Inhouds sleutel

Via de configuratie van een inhouds sleutel kunt u de volgende kenmerken beheren van zowel AMS Dynamic Encryption and AMS License delivery service:

* De inhouds sleutel die wordt gebruikt voor dynamische DRM-versleuteling.
* DRM-licentie-inhoud die moet worden geleverd door services voor levering van licenties: rechten, inhouds sleutel en beperkingen.
* Type beperking voor het **autorisatie beleid voor inhouds sleutels**: open, IP of Token beperking.
* Als het **token** type van de **beperking voor het autorisatie beleid voor inhouds sleutels wordt gebruikt**, moet aan de beperking van het autorisatie beleid voor de **inhouds sleutel** worden voldaan voordat een licentie wordt verleend.

### <a name="asset-delivery-policy"></a>Leverings beleid voor assets

Via de configuratie van een leverings beleid voor assets kunt u de volgende kenmerken beheren die worden gebruikt door AMS Dynamic packager en dynamische versleuteling van een AMS streaming-eind punt:

* De combi natie van het streaming-protocol en de DRM-versleuteling, zoals streepje onder CENC (PlayReady en Widevine), smooth streaming onder PlayReady, HLS onder Widevine of PlayReady.
* De standaard/Inge sloten Url's voor licentie levering voor elk van de betrokken DRMs.
* Of Url's voor het verkrijgen van licenties (LA_URLs) in een streepje-MPD of HLS-afspeel lijst een query reeks van de sleutel-ID (KID) bevatten voor respectievelijk Widevine en FairPlay.

## <a name="scenarios-and-samples"></a>Scenario's en voor beelden

Op basis van de uitleg in de vorige sectie gebruiken de volgende vijf hybride scenario's de verschillende configuratie combinaties voor het - **leverings beleid** voor de inhouds sleutel. de voor beelden in de laatste kolom volgen de tabel.

|**Inhoud hosten & oorsprong**|**DRM-versleuteling**|**Levering van DRM-licentie**|**Inhouds sleutel configureren**|**Leverings beleid voor assets configureren**|**Voorbeeld**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Ja|Ja|Voorbeeld 1|
|AMS|AMS|Van derden|Ja|Ja|Voorbeeld 2|
|AMS|Van derden|AMS|Ja|Nee|Voorbeeld 3|
|AMS|Van derden|Deel|Nee|Nee|Voorbeeld 4|
|Van derden|Van derden|AMS|Ja|Nee|    

In de voor beelden werkt PlayReady Protection voor zowel het streepje als het smooth streaming. De onderstaande video-Url's zijn smooth streaming Url's. Als u de bijbehorende streepje-Url's wilt ophalen, voegt u ' (Format = mpd-time-KVP) ' toe. U kunt de [Azure media test Player](https://aka.ms/amtest) gebruiken om te testen in een browser. Hiermee kunt u configureren welk streaming-protocol moet worden gebruikt, waaronder tech. IE11 en micro soft Edge in Windows 10 ondersteunen PlayReady tot en met EME. Zie [Details over het test programma](./offline-playready-streaming-windows-10.md)voor meer informatie.

### <a name="sample-1"></a>Voorbeeld 1

* Bron-URL (basis): `https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest` 
* PlayReady LA_URL (streepje & Smooth): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/` 
* Widevine LA_URL (streepje): `https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4` 
* FairPlay LA_URL (HLS): `https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8` 

### <a name="sample-2"></a>Voorbeeld 2

* Bron-URL (basis): https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (streepje & Smooth): `http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx` 

### <a name="sample-3"></a>Voorbeeld 3

* Bron-URL: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (streepje & Smooth): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/` 

### <a name="sample-4"></a>Voorbeeld 4

* Bron-URL: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (streepje & Smooth): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx` 

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="summary"></a>Samenvatting

Azure Media Services DRM-onderdelen zijn flexibeler, kunt u ze in een hybride scenario gebruiken door de beleids regels voor de levering van inhoud en assets goed te configureren, zoals beschreven in dit onderwerp.

## <a name="next-steps"></a>Volgende stappen
Media Services leer trajecten weer geven.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]