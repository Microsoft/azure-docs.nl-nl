---
title: URL-omleiding en URL herschrijven met Azure front deur Standard/Premium (preview)
description: Dit artikel helpt u inzicht te krijgen in de manier waarop Azure front-deur URL-omleiding ondersteunt en het herschrijven van URL'S met behulp van een Azure front-deur regelset.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 382a4c040c7a519462ee3e35119b9471031e0724
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099171"
---
# <a name="url-redirect-and-url-rewrite-with-azure-front-door-standardpremium-preview"></a>URL-omleiding en URL herschrijven met Azure front deur Standard/Premium (preview)

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

Dit artikel helpt u te begrijpen hoe de standaard/Premium van Azure front-deur ondersteuning biedt voor URL-omleiding en het herschrijven van URL'S in een regel reeks.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="url-redirect"></a>URL-omleiding

Azure front deur kan verkeer omleiden op elk van de volgende niveaus: protocol, hostnaam, pad, query reeks en fragment. Deze functies kunnen worden geconfigureerd voor afzonderlijke micro Services, omdat de omleiding is gebaseerd op het pad. Met URL-omleiding kunt u de toepassings configuratie vereenvoudigen door het resource gebruik te optimaliseren en worden nieuwe omleidings scenario's ondersteund, waaronder globale en op pad gebaseerde omleiding.

U kunt URL-omleiding configureren via de regel instellingen.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-redirect.png" alt-text="Scherm opname van het maken van een URL-omleiding met een regel set." lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-redirect-expanded.png":::

### <a name="redirection-types"></a>Type omleiding
Een omleidings type stelt de antwoord status code voor de clients in om het doel van de omleiding te begrijpen. De volgende typen omleiding worden ondersteund:

* **301 (permanent verplaatst)**: geeft aan dat aan de doel resource een nieuwe permanente URI is toegewezen. In toekomstige verwijzingen naar deze resource wordt een van de Inge sloten Uri's gebruikt. Gebruik 301-status code voor HTTP-naar-HTTPS-omleiding.
* **302 (gevonden)**: geeft aan dat de doel resource zich tijdelijk onder een andere URI bevindt. Omdat de omleiding altijd kan worden gewijzigd, moet de client de ingangs-URI voor toekomstige aanvragen blijven gebruiken.
* **307 (tijdelijke omleiding)**: geeft aan dat de doel resource zich tijdelijk onder een andere URI bevindt. De gebruikers agent mag de aanvraag methode niet wijzigen als deze een automatische omleiding naar die URI doet. Omdat de omleiding na verloop van tijd kan worden gewijzigd, moet de client de oorspronkelijke ingangs-URI van de aanvraag blijven gebruiken voor toekomstige aanvragen.
* **308 (permanente omleiding)**: geeft aan dat aan de doel resource een nieuwe permanente URI is toegewezen. Alle toekomstige verwijzingen naar deze resource moeten een van de Inge sloten Uri's gebruiken.

### <a name="redirection-protocol"></a>Omleidings Protocol
U kunt het protocol instellen dat voor omleiding wordt gebruikt. De meest voorkomende use cases van de omleidings functie is het instellen van HTTP-naar-HTTPS-omleiding.

* **Alleen https**: Stel het protocol in op https alleen als u het verkeer van http naar https wilt omleiden. De Azure front-deur raadt u aan om de omleiding altijd in te stellen op HTTPS.
* **Alleen http**: stuurt de inkomende aanvraag om naar http. Gebruik deze waarde alleen als u het verkeer HTTP wilt laten, niet-versleuteld.
* **Overeenkomende aanvraag: met** deze optie wordt het protocol bijgehouden dat door de binnenkomende aanvraag wordt gebruikt. Een HTTP-aanvraag blijft dus HTTP en een HTTPS-aanvraag blijft HTTPS post-omleiding.

### <a name="destination-host"></a>Doelhost
Als onderdeel van het configureren van een omleidings routering kunt u de hostnaam of het domein voor de omleidings aanvraag ook wijzigen. U kunt dit veld instellen om de hostnaam in de URL voor de omleiding te wijzigen of op een andere manier de hostnaam van de inkomende aanvraag te behouden. Met dit veld kunt u dus alle aanvragen omleiden die zijn verzonden `https://www.contoso.com/*` naar `https://www.fabrikam.com/*` .

### <a name="destination-path"></a>Doelpad
Als u het pad van een URL als onderdeel van de omleiding wilt vervangen, kunt u dit veld instellen met de nieuwe waarde voor het pad. Als dat niet het geval is, kunt u ervoor kiezen om de padwaarde als onderdeel van de omleiding te behouden. Met dit veld kunt u dus alle aanvragen omleiden die worden verzonden `https://www.contoso.com/\*` naar  `https://www.contoso.com/redirected-site` .

### <a name="query-string-parameters"></a>Query reeks parameters
U kunt ook de query reeks parameters in de omgeleide URL vervangen. Als u een bestaande query reeks wilt vervangen door de URL van de inkomende aanvraag, stelt u dit veld in op vervangen en stelt u vervolgens de juiste waarde in. Als dat niet het geval is, kunt u de oorspronkelijke reeks query reeksen behouden door het veld in te stellen op ' behouden '. Met dit veld kunt u bijvoorbeeld alle verkeer omleiden dat wordt verzonden naar `https://www.contoso.com/foo/bar` naar `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

### <a name="destination-fragment"></a>Doelfragment
Het doel fragment is het gedeelte van de URL na ' # ', dat door de browser wordt gebruikt om op een specifieke sectie van een webpagina te worden gegrond. U kunt dit veld instellen om een fragment toe te voegen aan de omleidings-URL.

## <a name="url-rewrite"></a>URL opnieuw genereren

Azure front-deur ondersteunt het herschrijven van URL'S voor het herschrijven van het pad van een aanvraag die naar uw oorsprong wordt verzonden. Als u de URL opnieuw schrijft, kunt u voor waarden toevoegen om ervoor te zorgen dat de URL of de opgegeven headers alleen worden herschreven als aan bepaalde voor waarden wordt voldaan. Deze voorwaarden zijn gebaseerd op de informatie in de aanvraag en het antwoord.

Met deze functie kunt u gebruikers omleiden naar verschillende oorsprong op basis van scenario, apparaattype en gewenst bestands type.

U kunt URL-omleiding configureren via de regel instellingen.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite.png" alt-text="Scherm opname van het opnieuw schrijven van een URL met een regel instelling." lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite-expanded.png":::

### <a name="source-pattern"></a>Bron patroon

Bron patroon is het URL-pad in de bron aanvraag dat moet worden vervangen. Op dit moment gebruikt het bron patroon een overeenkomst op basis van voor voegsels. Als u wilt dat alle URL-paden overeenkomen, gebruikt u een slash (/) als bron patroon waarde.

### <a name="destination"></a>Doel

U kunt het doelpad definiëren dat moet worden gebruikt voor herschrijven. Het doelpad overschrijft het bron patroon.

### <a name="preserve-unmatched-path"></a>Niet-overeenkomend pad behouden

Met niet-overeenkomende paden behouden kunt u het resterende pad na het bron patroon toevoegen aan het nieuwe pad.

Als ik bijvoorbeeld niet- **overeenkomend pad behouden Stel in op Ja**.
* Als de binnenkomende aanvraag is `www.contoso.com/sub/1.jpg` , wordt het bron patroon ingesteld op `/` , wordt de bestemming ingesteld op `/foo/` , en wordt de inhoud van `/foo/sub/1` . jpg van de oorsprong opgehaald.

* Als de binnenkomende aanvraag is `www.contoso.com/sub/image/1.jpg` , wordt het bron patroon ingesteld op `/sub/` , wordt de bestemming ingesteld op `/foo/` , wordt de inhoud vanuit `/foo/image/1.jpg` de oorsprong opgehaald.

Bijvoorbeeld, als ik niet- **overeenkomende paden behouden op Nee** stel.
* Als de binnenkomende aanvraag is `www.contoso.com/sub/image/1.jpg` , wordt het bron patroon ingesteld op `/sub/` , wordt de bestemming ingesteld op `/foo/2.jpg` , wordt de inhoud altijd vanuit de oorsprong geleverd, ongeacht de `/foo/2.jpg` paden die worden gevolgd in `wwww.contoso.com/sub/` .

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Azure front deur Standard/Premium-regelset](concept-rule-set.md).
