---
title: Azure front-deur-URL-omleiding | Microsoft Docs
description: Dit artikel helpt u inzicht te krijgen in de manier waarop Azure front-deur URL-omleiding ondersteunt voor hun routerings regels.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 61077c7900530fd4c5be64054bedd9c5d087fe77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91442055"
---
# <a name="url-redirect"></a>URL-omleiding
Azure front deur kan verkeer omleiden op elk van de volgende niveaus: protocol, hostnaam, pad, query teken reeks. Deze functies kunnen worden geconfigureerd voor afzonderlijke micro Services, omdat de omleiding op basis van het pad is gebaseerd op paden. Hierdoor kan de configuratie van de toepassing worden vereenvoudigd door het resource gebruik te optimaliseren en worden nieuwe omleidings scenario's ondersteund, waaronder globale en op pad gebaseerde omleiding.
</br>

:::image type="content" source="./media/front-door-url-redirect/front-door-url-redirect.png" alt-text="URL-omleiding voor Azure front-deur":::

## <a name="redirection-types"></a>Type omleiding
Een omleidings type stelt de antwoord status code voor de clients in om het doel van de omleiding te begrijpen. De volgende typen omleiding worden ondersteund:

- **301 (permanent verplaatst)**: geeft aan dat aan de doel resource een nieuwe permanente URI is toegewezen. In toekomstige verwijzingen naar deze resource wordt een van de Inge sloten Uri's gebruikt. Gebruik 301-status code voor HTTP-naar-HTTPS-omleiding. 
- **302 (gevonden)**: geeft aan dat de doel resource zich tijdelijk onder een andere URI bevindt. Omdat de omleiding altijd kan worden gewijzigd, moet de client de ingangs-URI voor toekomstige aanvragen blijven gebruiken.
- **307 (tijdelijke omleiding)**: geeft aan dat de doel resource zich tijdelijk onder een andere URI bevindt. De gebruikers agent mag de aanvraag methode niet wijzigen als deze een automatische omleiding naar die URI doet. Omdat de omleiding na verloop van tijd kan worden gewijzigd, moet de client de oorspronkelijke ingangs-URI van de aanvraag blijven gebruiken voor toekomstige aanvragen.
- **308 (permanente omleiding)**: geeft aan dat aan de doel resource een nieuwe permanente URI is toegewezen. Alle toekomstige verwijzingen naar deze resource moeten een van de Inge sloten Uri's gebruiken.

## <a name="redirection-protocol"></a>Omleidings Protocol
U kunt het protocol instellen dat voor omleiding wordt gebruikt. De meest voorkomende use cases van de omleidings functie is het instellen van HTTP-naar-HTTPS-omleiding.

- **Alleen https**: Stel het protocol in op https alleen als u het verkeer van http naar https wilt omleiden. De Azure front-deur raadt u aan om de omleiding altijd in te stellen op HTTPS.
- **Alleen http**: stuurt de inkomende aanvraag om naar http. Gebruik deze waarde alleen als u het verkeer HTTP wilt laten, niet-versleuteld.
- **Overeenkomende aanvraag: met** deze optie wordt het protocol bijgehouden dat door de binnenkomende aanvraag wordt gebruikt. Een HTTP-aanvraag blijft dus HTTP en een HTTPS-aanvraag blijft HTTPS post-omleiding.

## <a name="destination-host"></a>Doelhost
Als onderdeel van het configureren van een omleidings routering kunt u de hostnaam of het domein voor de omleidings aanvraag ook wijzigen. U kunt dit veld instellen om de hostnaam in de URL voor de omleiding te wijzigen of op een andere manier de hostnaam van de inkomende aanvraag te behouden. Met dit veld kunt u dus alle aanvragen omleiden die zijn verzonden `https://www.contoso.com/*` naar `https://www.fabrikam.com/*` .

## <a name="destination-path"></a>Doelpad
Als u het pad van een URL als onderdeel van de omleiding wilt vervangen, kunt u dit veld instellen met de nieuwe waarde voor het pad. Als dat niet het geval is, kunt u ervoor kiezen om de padwaarde als onderdeel van de omleiding te behouden. Met dit veld kunt u dus alle aanvragen omleiden die worden verzonden `https://www.contoso.com/\*` naar  `https://www.contoso.com/redirected-site` .

## <a name="query-string-parameters"></a>Query reeks parameters
U kunt ook de query reeks parameters in de omgeleide URL vervangen. Als u een bestaande query reeks wilt vervangen door de URL van de inkomende aanvraag, stelt u dit veld in op vervangen en stelt u vervolgens de juiste waarde in. Als dat niet het geval is, kunt u de oorspronkelijke reeks query reeksen behouden door het veld in te stellen op ' behouden '. Met dit veld kunt u bijvoorbeeld alle verkeer omleiden dat wordt verzonden naar `https://www.contoso.com/foo/bar` naar `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

## <a name="destination-fragment"></a>Doelfragment
Het doel fragment is het gedeelte van de URL na ' # ', dat door de browser wordt gebruikt om op een specifieke sectie van een webpagina te worden gegrond. U kunt dit veld instellen om een fragment toe te voegen aan de omleidings-URL.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
