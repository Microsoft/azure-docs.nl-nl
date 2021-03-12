---
title: Een autorisatie beleid voor de inhouds sleutel configureren met behulp van de Azure Portal | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een autorisatie beleid configureert voor een inhouds sleutel.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 68bd4bd2472e80a663294745368fb505767a230a
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008311"
---
# <a name="configure-a-content-key-authorization-policy"></a>Een autorisatie beleid voor de inhouds sleutel configureren

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Overzicht
 U kunt Azure Media Services gebruiken om MPEG-DASH-, Smooth Streaming-en HTTP Live Streaming (HLS)-stromen te leveren die worden beveiligd met Advanced Encryption Standard (AES) met behulp van 128-bits versleutelings sleutels of [PlayReady Digital Rights Management (DRM)](https://www.microsoft.com/playready/overview/). Met Media Services kunt u ook STREEPJES stromen leveren die zijn versleuteld met Widevine DRM. Zowel PlayReady als Widevine zijn versleuteld volgens de specificatie Common Encryption (ISO/IEC 23001-7 CENC).

Media Services biedt ook een service voor het leveren van belang rijke services waarmee clients AES-sleutels of PlayReady/Widevine-licenties kunnen verkrijgen om de versleutelde inhoud af te spelen.

In dit artikel wordt beschreven hoe u met behulp van de Azure Portal het autorisatie beleid voor de inhouds sleutel configureren. De sleutel kan later worden gebruikt om uw inhoud dynamisch te versleutelen. Op dit moment kunt u HLS-, MPEG-DASH-en Smooth Streaming-indelingen versleutelen. U kunt progressieve down loads niet versleutelen.

Wanneer een speler een stroom aanvraagt die dynamisch moet worden versleuteld, Media Services de geconfigureerde sleutel gebruiken om uw inhoud dynamisch te versleutelen met behulp van AES of DRM-versleuteling. Voor het ontsleutelen van de stream, wordt door de speler de sleutel van de sleutelleveringsservice aangevraagd. Om te bepalen of de gebruiker gemachtigd is om de sleutel op te halen, evalueert de service het autorisatie beleid dat u hebt opgegeven voor de sleutel.

Als u van plan bent meerdere inhouds sleutels te hebben of een andere URL wilt opgeven dan de Media Services key delivery service, gebruikt u de Media Services .NET SDK of REST Api's. Zie voor meer informatie:

* [Een autorisatie beleid voor inhouds sleutels configureren met behulp van de Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)
* [Configureer een autorisatie beleid voor de inhouds sleutel met behulp van de Media Services REST API](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Er zijn enkele overwegingen van toepassing
* Wanneer uw Media Services-account is gemaakt, wordt er een standaard streaming-eindpunt met de status Gestopt aan uw account toegevoegd. Als u uw inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eind punt de status ' running ' hebben. 
* Uw asset moet een set adaptieve bitsnelheid Mp4's of een adaptieve bitrate Smooth Streaming-bestanden bevatten. Zie [een Asset coderen](media-services-encode-asset.md)voor meer informatie.
* De key delivery service slaat ContentKeyAuthorizationPolicy en de bijbehorende objecten (beleids opties en-beperkingen) gedurende 15 minuten op. U kunt een ContentKeyAuthorizationPolicy maken en opgeven dat u een token beperking wilt gebruiken, deze wilt testen en vervolgens het beleid wilt bijwerken naar de beperking open. Dit proces duurt ongeveer 15 minuten voordat het beleid overschakelt naar de open-versie.
* Een Media Services streaming-eind punt stelt de waarde van het CORS Access-Control-Allow-Origin-header in het Preflight-antwoord in als het Joker teken \* . Deze waarde is goed geschikt voor de meeste spelers, waaronder Azure Media Player, Roku en JWPlayer, en andere. Sommige spelers die gebruikmaken van dash.js, werken echter niet omdat met de modus referenties ingesteld op ' include ' XMLHttpRequest in hun dash.js het Joker teken niet toestaan \* als de waarde voor Access-Control-Allow-Origin. Als u een tijdelijke oplossing hebt voor deze beperking in dash.js, kunt u, als u uw client vanuit één domein host, Media Services dat domein opgeven in de kopregel van het Preflight-antwoord. Open een ondersteunings ticket via de Azure Portal voor hulp.

## <a name="configure-the-key-authorization-policy"></a>Het sleutel autorisatie beleid configureren
Als u het sleutel autorisatie beleid wilt configureren, selecteert u de pagina **beveiliging van inhoud** .

Media Services ondersteunt meerdere manieren om gebruikers te verifiëren die belang rijke aanvragen indienen. Het autorisatie beleid voor inhouds sleutels kan open-, Token-of IP-autorisatie beperkingen hebben. (IP kan worden geconfigureerd met REST of de .NET SDK.)

### <a name="open-restriction"></a>Beperking openen
De beperking open houdt in dat het systeem de sleutel levert aan iedereen die een belang rijke aanvraag doet. Deze beperking kan handig zijn voor test doeleinden.

![Openpolicy][open_policy]

### <a name="token-restriction"></a>Token beperking
Als u het token beperkt beleid wilt kiezen, selecteert u de knop **token** .

Het token beperkt beleid moet vergezeld gaan van een token dat is uitgegeven door een beveiligings token service (STS). Media Services ondersteunt tokens in de indelingen simple web token ([SWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2)) en JSON Web token (JWT). Zie [JWT-verificatie](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)voor meer informatie.

Media Services biedt geen STS. U kunt een aangepaste STS maken om tokens te verlenen. De STS moet worden geconfigureerd om een token te maken dat is ondertekend met de opgegeven sleutel en claims uitgeven die u hebt opgegeven in de configuratie van de token beperking. Als het token geldig is en de claims in het token overeenkomen met die zijn geconfigureerd voor de inhouds sleutel, retourneert de Media Services key delivery service de versleutelings sleutel naar de client.

Wanneer u het token beperkt beleid configureert, moet u de para meters voor de primaire verificatie sleutel, verlener en doel groep opgeven. De primaire verificatie sleutel bevat de sleutel waarmee het token is ondertekend. De uitgever is de STS die het token uitgeeft. De doel groep (ook wel bereik genoemd) beschrijft de bedoeling van het token of de bron waarvan het token toegang verleent. Met de Media Services key delivery service wordt gecontroleerd of deze waarden in het token overeenkomen met de waarden in de sjabloon.

### <a name="playready"></a>PlayReady
Wanneer u uw inhoud beveiligt met PlayReady, is een van de dingen die u in uw autorisatie beleid moet opgeven, een XML-teken reeks die de PlayReady-licentie sjabloon definieert. Standaard is het volgende beleid ingesteld:

```xml
<PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
  <LicenseTemplates>
    <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
      <ContentKey i:type="ContentEncryptionKeyFromHeader" />
      <LicenseType>Nonpersistent</LicenseType>
      <PlayRight>
        <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
      </PlayRight>
    </PlayReadyLicenseTemplate>
  </LicenseTemplates>
</PlayReadyLicenseResponseTemplate>
```

U kunt de knop **beleid XML importeren** selecteren en een andere XML opgeven die voldoet aan het XML-schema dat is gedefinieerd in het overzicht van de [Media Services PlayReady-licentie sjabloon](media-services-playready-license-template-overview.md).

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png
