---
title: Overzicht van het beleid voor Azure Web Application firewall (WAF)
description: Dit artikel bevat een overzicht van het globale Web Application firewall (WAF), per site-en per-URI-beleid.
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 11/20/2020
ms.author: victorh
ms.openlocfilehash: 59ca0b85ba2aff29bdb2ad3379c1054041d2b4cb
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518733"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Overzicht van het beleid voor Azure Web Application firewall (WAF)

Web Application firewall-beleid bevat alle WAF-instellingen en-configuraties. Dit omvat uitsluitingen, aangepaste regels, beheerde regels, enzovoort. Deze beleids regels worden vervolgens gekoppeld aan een toepassings gateway (globaal), een listener (per site) of een op een pad gebaseerde regel (per URI) die ze van kracht kunnen laten worden.

Er is geen limiet voor het aantal beleids regels dat u kunt maken. Wanneer u een beleid maakt, moet dit zijn gekoppeld aan een toepassings gateway om van kracht te worden. Het kan worden gekoppeld aan elke combi natie van toepassings gateways, listeners en regels op basis van een pad.

## <a name="global-waf-policy"></a>Beleid voor globale WAF

Wanneer u een WAF-beleid globaal koppelt, wordt elke site achter uw Application Gateway WAF beveiligd met dezelfde beheerde regels, aangepaste regels, uitsluitingen en andere geconfigureerde instellingen.

Als u één beleid wilt Toep assen op alle sites, kunt u het beleid koppelen aan de toepassings gateway. Zie voor meer informatie [Web Application firewall-beleids regels maken voor Application Gateway](create-waf-policy-ag.md) om een WAF-beleid te maken en toe te passen met behulp van de Azure Portal. 

## <a name="per-site-waf-policy"></a>WAF-beleid per site

Met per-site beleidsregels van WAF kunt u meerdere sites met verschillende beveiligingseisen beveiligen achter één WAF door gebruik te maken van per-site beleid. Als er zich bijvoorbeeld vijf sites achter uw WAF bevinden, kunt u vijf afzonderlijke WAF-beleidsregels (één voor elke listener) opstellen om de uitsluitingen, aangepaste regels, beheerde regelsets en alle andere WAF-instellingen per site aan te passen.

Stel dat er op uw toepassingsgateway een globaal beleid is toegepast. Vervolgens past u een ander beleid toe op een listener op die toepassingsgateway. Het beleid van de listener is dan alleen van kracht voor die listener. Het globale beleid van de toepassingsgateway is nog steeds van toepassing op alle andere listeners en op pad gebaseerde regels waaraan geen specifiek beleid is toegewezen.

## <a name="per-uri-policy"></a>Beleid per URI

Voor nog meer aanpassing van het URI-niveau, kunt u een WAF-beleid koppelen aan een op pad gebaseerde regel. Als er bepaalde pagina's binnen één site zijn waarvoor verschillende beleids regels zijn vereist, kunt u wijzigingen aanbrengen in het WAF-beleid dat alleen van invloed is op een opgegeven URI. Dit kan van toepassing zijn op een betaling of aanmeldings pagina, of andere Uri's die een nog specifiek WAF-beleid nodig hebben dan de andere sites achter uw WAF.

Net als bij per-site WAF-beleid worden minder specifieke beleids regels genegeerd. Dit betekent dat een beleid per URI op een URL-pad-map overschrijft elk per site of globaal WAF-beleid hierboven.

### <a name="example"></a>Voorbeeld

Stel dat u drie sites hebt: contoso.com, fabrikam.com en adatum.com die zich achter dezelfde toepassings gateway bevindt. U wilt dat een WAF wordt toegepast op alle drie de sites, maar u hebt extra beveiliging nodig met adatum.com, omdat klanten producten bezoeken, zoeken en kopen.

U kunt een globaal beleid Toep assen op de WAF, met enkele basis instellingen, uitsluitingen of aangepaste regels, indien nodig om te voor komen dat bepaalde valse positieven verkeer blok keren. In dit geval zijn er geen globale SQL-injectie regels actief, omdat fabrikam.com en contoso.com statische pagina's zijn zonder SQL-back-end. Zodat u deze regels in het globale beleid kunt uitschakelen.

Dit globale beleid is geschikt voor contoso.com en fabrikam.com, maar u moet voorzichtig zijn met adatum.com waar aanmeldings gegevens en-betalingen worden afgehandeld. U kunt een beleid per site Toep assen op de adatum-listener en de SQL-regels blijven actief. Stel dat er een cookie is die een bepaald verkeer blokkeert. u kunt dus een uitsluiting voor die cookie maken om de fout-positieve waarde te stoppen. 

De adatum.com/payments-URI is waar u voorzichtig moet zijn. Pas een ander beleid op die URI toe en zorg ervoor dat alle regels ingeschakeld blijven, en verwijder ook alle uitsluitingen.

In dit voor beeld hebt u een globaal beleid dat van toepassing is op twee sites. U hebt een beleid per site dat van toepassing is op één site en vervolgens per URI-beleid dat van toepassing is op één specifieke regel op basis van een pad. Zie [WAF-beleid per site configureren met behulp van Azure PowerShell](per-site-policies.md) voor de bijbehorende Power shell voor dit voor beeld.

## <a name="existing-waf-configurations"></a>Bestaande WAF-configuraties

Alle nieuwe WAF-instellingen van de firewall voor webtoepassingen (aangepaste regels, configuraties van beheerde regel sets, uitsluitingen, enzovoort) bestaan in een WAF-beleid. Als u een bestaande WAF hebt, zijn deze instellingen mogelijk nog aanwezig in de WAF-configuratie. Voor meer informatie over het overstappen op het nieuwe WAF-beleid, [migreert u WAF config naar een WAF-beleid](./migrate-policy.md). 


## <a name="next-steps"></a>Volgende stappen

- [Maak per-site-en per-URI-beleid met behulp van Azure PowerShell](per-site-policies.md).
