---
title: 'Azure front deur: DDoS-beveiliging'
description: Deze pagina bevat informatie over de manier waarop Azure front-deur Standard/Premium u kan beschermen tegen DDoS-aanvallen
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: jodowns
ms.openlocfilehash: 9c67944717888439c0d6bd84e1615f51dd91fcac
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099237"
---
# <a name="ddos-protection-on-azure-front-door-standardpremium-preview"></a>DDoS-beveiliging op Azure front deur Standard/Premium (preview)

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

De voor deur van Azure heeft verschillende functies en kenmerken die kunnen helpen om DDoS-aanvallen (Distributed Denial of service) te voor komen. Deze functies kunnen voor komen dat kwaadwillende personen uw toepassing bereiken en de beschik baarheid en prestaties van uw toepassing beïnvloeden.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="integration-with-azure-ddos-protection-basic"></a>Integratie met Azure DDoS Protection Basic

De voor deur wordt beschermd door Azure DDoS Protection Basic. De functie is standaard geïntegreerd in het platform van de voor deur en zonder extra kosten. De volledige schaal en de capaciteit van het wereld wijd geïmplementeerde netwerk van de voor deur bieden bescherming tegen veelvoorkomende aanvallen in de netwerklaag via continu verkeer bewaking en real-time risico beperking. Basic DDoS Protection beschermt ook tegen de meest voorkomende, vaak voorkomende laag 7 DNS-query stromen en laag 3-en 4 volumetrische aanvallen die gericht zijn op open bare eind punten. Deze service heeft ook een bewezen spoor record in de bescherming van de Enter prise-en consumenten services van micro soft tegen grootschalige aanvallen. Zie [Azure DDoS Protection](../../security/fundamentals/ddos-best-practices.md)voor meer informatie.

## <a name="protocol-blocking"></a>Protocol blok keren

De voor deur accepteert alleen verkeer op de HTTP-en HTTPS-protocollen en verwerkt alleen geldige aanvragen met een bekende `Host` header. Dit gedrag helpt bij het oplossen van enkele veelvoorkomende DDoS-aanvals typen, waaronder volumetrische aanvallen die over diverse protocollen en poorten, DNS-versterking en aanvallen op TCP-verontreiniging komen.

## <a name="capacity-absorption"></a>Capaciteits absorptie

De voor deur is een enorme schaal bare, wereld wijd gedistribueerde service. We hebben veel klanten, waaronder de eigen grootschalige Cloud producten van micro soft, die elke seconde honderd duizenden aanvragen ontvangen. De voor deur bevindt zich aan de rand van het netwerk van Azure, waarbij grote volume aanvallen worden geabsorbeerd en geografisch worden geïsoleerd. Dit kan verhinderen dat kwaad aardig verkeer verder gaat dan de rand van het Azure-netwerk.

## <a name="caching"></a>Caching

[De cache mogelijkheden van front deur](concept-caching.md) kunnen worden gebruikt voor het beveiligen van back-endservers van grote verkeers volumes die worden gegenereerd door een aanval. Bronnen die in de cache zijn opgeslagen, worden geretourneerd vanaf de front-deur rand knooppunten zodat ze niet worden doorgestuurd naar uw back-end. Even verloop tijd van de korte cache (seconden of minuten) op dynamische reacties kan de belasting van back-upservices aanzienlijk verminderen. Zie [overwegingen voor caching](/azure/architecture/best-practices/caching) en [het cache-leggings patroon](/azure/architecture/patterns/cache-aside)voor meer informatie over het opslaan van concepten en patronen in de cache.

## <a name="web-application-firewall-waf"></a>Web Application Firewall (WAF)

[De Web Application firewall (WAF) van front-deur](../../web-application-firewall/afds/afds-overview.md) kan worden gebruikt om veel verschillende soorten aanvallen te verhelpen:

* Het gebruik van de beheerde regel set biedt beveiliging tegen veel voorkomende aanvallen.
* Verkeer van buiten een gedefinieerde geografische regio of binnen een bepaalde regio kan worden geblokkeerd of omgeleid naar een statische webpagina. Zie [geo-filtering](../../web-application-firewall/afds/waf-front-door-geo-filtering.md)voor meer informatie.
* IP-adressen en bereiken die u als schadelijk identificeert, kunnen worden geblokkeerd.
* Frequentie limiet kan worden toegepast om te voor komen dat IP-adressen uw service te vaak aanroepen.
* U kunt [aangepaste WAF-regels](../../web-application-firewall/afds/waf-front-door-custom-rules.md) maken om http-of https-aanvallen met bekende hand tekeningen automatisch te blok keren en te beperken.

## <a name="for-further-protection"></a>Voor verdere beveiliging

Als u verdere beveiliging nodig hebt, kunt u [Azure DDoS Protection standaard](../../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) inschakelen op het VNet waar uw back-ends worden geïmplementeerd. DDoS Protection Standard-klanten krijgen meer voor delen, waaronder:

* Kosten beveiliging
* SLA-garantie
* Toegang tot deskundigen van het DDoS Rapid Response-Team voor onmiddellijke ondersteuning tijdens een aanval.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een voor deur standaard/Premium](create-front-door-portal.md).
