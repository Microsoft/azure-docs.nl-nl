---
title: Bescherm uw inhoud met Azure Media Services | Microsoft Docs
description: Dit artikel geeft een overzicht van de beveiliging van inhoud met Azure Media Services v2.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 2922586d586dbbb98ae85fb86f3f76889a75aa6b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067398"
---
# <a name="content-protection-overview"></a>Overzicht van inhoudsbeveiliging

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

U kunt Azure Media Services gebruiken om uw media te beveiligen vanaf het moment dat de computer de opslag, verwerking en levering verlaat. Met Media Services kunt u uw live en on-demand inhoud dynamisch versleutelen met Advanced Encryption Standard (AES-128) of een van de drie belangrijkste Digital Rights Management-systemen (DRM): Microsoft PlayReady, Google Widevine en Apple FairPlay. Media Services biedt ook een service voor het leveren van AES-sleutels en DRM-licenties (PlayReady, Widevine en FairPlay) aan geautoriseerde klanten. 

In de volgende afbeelding ziet u de werk stroom voor Media Services beveiliging van inhoud: 

![Beveiligen met PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

In dit artikel worden de concepten en terminologie beschreven die relevant zijn voor de beveiliging van inhoud met Media Services. Het artikel bevat ook koppelingen naar artikelen waarin wordt uitgelegd hoe u inhoud kunt beveiligen. 

## <a name="dynamic-encryption"></a>Dynamische versleuteling

U kunt Media Services gebruiken om uw inhoud dynamisch te versleutelen met AES Clear Key of DRM-versleuteling door gebruik te maken van PlayReady, Widevine of FairPlay. Als inhoud is versleuteld met een AES-sleutel en via HTTPS wordt verzonden, wordt deze niet duidelijk totdat de client wordt bereikt. 

Elke versleutelings methode ondersteunt de volgende streaming-protocollen:
 
- AES: MPEG-DASH, Smooth Streaming en HLS
- PlayReady: MPEG-DASH, Smooth Streaming en HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Het versleutelen van progressieve down loads wordt niet ondersteund. 

Als u een Asset wilt versleutelen, moet u een versleutelings inhouds sleutel aan uw asset koppelen en ook een autorisatie beleid voor de sleutel configureren. U kunt inhouds sleutels opgeven of automatisch genereren door Media Services.

U moet ook het leverings beleid van het activum configureren. Als u een opslag versleutelde Asset wilt streamen, moet u opgeven hoe u deze wilt leveren door het beleid voor de levering van assets te configureren.

Wanneer een stream wordt aangevraagd door een speler, gebruikt Media Services de opgegeven sleutel om uw inhoud dynamisch te versleutelen met AES clear key of DRM-versleuteling. Voor het ontsleutelen van de stroom vraagt de speler de sleutel aan bij Media Services key delivery service. Om te bepalen of de gebruiker gemachtigd is om de sleutel op te halen, evalueert de service het autorisatie beleid dat u hebt opgegeven voor de sleutel.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 Clear-sleutel versus DRM
Klanten vragen vaak of ze AES-versleuteling of een DRM-systeem moeten gebruiken. Het belangrijkste verschil tussen de twee systemen is dat met AES-versleuteling de inhouds sleutel wordt verzonden naar de client in een niet-versleutelde indeling (' in de Clear '). Als gevolg hiervan kan de sleutel voor het versleutelen van de inhoud worden weer gegeven in een netwerk tracering op de client als tekst zonder opmaak. AES-128 Clear Key Encryption is geschikt voor gebruik waarbij de viewer een vertrouwde partij is (bijvoorbeeld het versleutelen van bedrijfs Video's die binnen een bedrijf worden gedistribueerd om door werk nemers te worden weer gegeven).

PlayReady, Widevine en FairPlay bieden allemaal een hoger coderings niveau vergeleken met AES-128 Clear Key encryption. De inhouds sleutel wordt in een versleutelde indeling verzonden. Daarnaast wordt ontsleuteling afgehandeld in een beveiligde omgeving op het niveau van het besturings systeem, waar het moeilijker is voor een kwaadwillende gebruiker om aan te vallen. DRM wordt aanbevolen voor use-cases waarbij de viewer mogelijk geen vertrouwde partij is en u het hoogste beveiligings niveau nodig hebt.

## <a name="storage-encryption"></a>Storage-versleuteling
U kunt opslag versleuteling gebruiken om uw ongecodeerde inhoud lokaal te versleutelen met behulp van AES 256-bits versleuteling. Vervolgens kunt u het uploaden naar Azure Storage, waar het wordt opgeslagen op rest. Assets die zijn beveiligd met opslag versleuteling, worden automatisch niet-versleuteld en in een versleuteld bestands systeem geplaatst vóór de code ring. De activa kunnen eventueel opnieuw worden versleuteld voordat ze worden geüpload als nieuwe uitvoer activa. Het primaire gebruiks voorbeeld voor opslag versleuteling is wanneer u uw invoer bestanden met een hoge kwaliteit wilt beveiligen met een sterke versleuteling van de schijf.

Als u een met opslag versleutelde Asset wilt leveren, moet u het leverings beleid van het activum configureren zodat Media Services weet hoe u uw inhoud wilt leveren. Voordat uw asset kan worden gestreamd, ontsleutelt de streaming-server uw inhoud en streamt deze met behulp van het opgegeven leverings beleid (bijvoorbeeld AES, algemene versleuteling of geen versleuteling).

## <a name="types-of-encryption"></a>Typen versleuteling
PlayReady en Widevine gebruiken algemene versleuteling (AES-plaatsings modus). FairPlay maakt gebruik van AES-versleuteling voor de CBC-modus. AES-128 Clear sleutel versleuteling maakt gebruik van envelop versleuteling.

## <a name="licenses-and-keys-delivery-service"></a>Licenties en sleutels bezorgings service
Media Services biedt een belang rijke leverings service voor het leveren van DRM-licenties (PlayReady, Widevine, FairPlay) en AES-sleutels aan geautoriseerde clients. U kunt [de Azure Portal](media-services-portal-protect-content.md), de rest API of de Media Services SDK voor .net gebruiken om autorisatie-en verificatie beleid te configureren voor uw licenties en sleutels.

## <a name="control-content-access"></a>Toegang tot inhoud beheren
U kunt bepalen wie toegang heeft tot uw inhoud door het autorisatie beleid voor de inhouds sleutel te configureren. Het autorisatie beleid voor inhouds sleutels ondersteunt open of Token beperking.

### <a name="open-authorization"></a>Autorisatie openen
Met een open autorisatie beleid wordt de inhouds sleutel verzonden naar een wille keurige client (geen beperking).

### <a name="token-authorization"></a>Token autorisatie
Met een autorisatie beleid waarvoor een token beperking geldt, wordt de inhouds sleutel alleen verzonden naar een client die een geldig JSON Web Token (JWT) of een eenvoudig webtoken (SWT) in de sleutel/licentie aanvraag levert. Dit token moet worden uitgegeven door een beveiligings token service (STS). U kunt Azure Active Directory als een STS gebruiken of een aangepaste STS implementeren. De STS moet worden geconfigureerd om een token te maken dat is ondertekend met de opgegeven sleutel en claims uitgeven die u hebt opgegeven in de configuratie van de token beperking. De Media Services key delivery service retourneert de aangevraagde sleutel/licentie voor de client als het token geldig is en de claims in het token overeenkomen met de aanvragen die zijn geconfigureerd voor de sleutel/licentie.

Wanneer u het token beperkt beleid configureert, moet u de para meters voor de primaire verificatie sleutel, verlener en doel groep opgeven. De primaire verificatie sleutel bevat de sleutel waarmee het token is ondertekend. De uitgever is de beveiligde token service waarmee het token wordt uitgegeven. De doel groep, ook wel bereik genoemd, beschrijft de bedoeling van het token of de bron waarvan het token toegang verleent. Met de Media Services key delivery service wordt gecontroleerd of deze waarden in het token overeenkomen met de waarden in de sjabloon.

### <a name="token-replay-prevention"></a>Token replay voor komen

Met de functie voor het voor *komen van tokens* kunnen Media Services klanten een limiet instellen voor het aantal keren dat hetzelfde token kan worden gebruikt om een sleutel of licentie aan te vragen. De klant kan een claim van het type `urn:microsoft:azure:mediaservices:maxuses` in het token toevoegen, waarbij de waarde het aantal keren is dat het token kan worden gebruikt om een licentie of sleutel te verkrijgen. Bij alle volgende aanvragen met dezelfde token voor de sleutel levering wordt een niet-geautoriseerde reactie geretourneerd. Zie hoe u de claim toevoegt in het [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601)-voor beeld.
 
#### <a name="considerations"></a>Overwegingen

* Klanten moeten controle hebben over het genereren van tokens. De claim moet in het token zelf worden geplaatst.
* Wanneer u deze functie gebruikt, worden aanvragen met tokens waarvan de verval tijd meer dan één uur duurt, verwijderd met een niet-geautoriseerde reactie.
* Tokens worden uniek geïdentificeerd door hun hand tekening. Elke wijziging in de payload (bijvoorbeeld bijwerken naar de verloop tijd of de claim) wijzigt de hand tekening van het token en telt als een nieuw token dat de levering van sleutels nog niet eerder is.
* Afspelen mislukt als het token de `maxuses` waarde die is ingesteld door de klant heeft overschreden.
* Deze functie kan worden gebruikt voor alle bestaande beveiligde inhoud (alleen het token dat is uitgegeven moet worden gewijzigd).
* Deze functie werkt met zowel de JWT-als de SWT.

## <a name="streaming-urls"></a>Streaming-Url's
Als uw asset is versleuteld met meer dan één DRM-bestand, gebruikt u een versleutelings code in de streaming-URL: (Format = ' 3u8-AAPL ', Encryption = ' xxx ').

De volgende overwegingen zijn van toepassing:

* Er kan niet meer dan één versleutelings type worden opgegeven.
* Het versleutelings type hoeft niet te worden opgegeven in de URL als er slechts één versleuteling is toegepast op de Asset.
* Het versleutelings type is niet hoofdletter gevoelig.
* De volgende versleutelings typen kunnen worden opgegeven:

  * **Cenc**: voor PlayReady of Widevine (algemene versleuteling)
  * **cbcs-AAPL**: voor FAIRPLAY (AES CBC-versleuteling)
  * **CBC**: voor AES-envelop versleuteling

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="next-steps"></a>Volgende stappen
In de volgende artikelen worden de volgende stappen beschreven om aan de slag te gaan met inhouds beveiliging:

* [Beveiligen met opslag versleuteling](media-services-rest-storage-encryption.md)
* [Beveiligen met AES-versleuteling](media-services-playready-license-template-overview.md)
* [Beveiligen met PlayReady en/of Widevine](media-services-protect-with-playready-widevine.md)
* [Beveiligen met FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Verwante koppelingen

* [Verificatie JWT-token](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Azure Media Services op OWIN MVC gebaseerde app integreren met Azure Active Directory en de levering van de inhouds sleutel beperken op basis van de JWT-claims](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
