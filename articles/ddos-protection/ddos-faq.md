---
title: Veelgestelde vragen over Azure DDoS Protection
description: Veelgestelde vragen over de Azure DDoS Protection Standard, waarmee u een verdediging kunt bieden tegen DDoS-aanvallen.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: yitoh
ms.openlocfilehash: 871ededce1db5e4c3179c187fc46a828cd157456
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886274"
---
# <a name="azure-ddos-protection-standard-frequent-asked-questions"></a>Veelgestelde vragen over Azure DDoS Protection

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure DDoS Protection Standard. 

## <a name="what-is-a-distributed-denial-of-service-ddos-attack"></a>Wat is een DDoS-aanval (Distributed Denial of service)?
Distributed Denial of service, of DDoS, is een type aanval waarbij een aanvaller meer aanvragen verzendt naar een toepassing dan de toepassing kan verwerken. Het resultaat is dat bronnen worden uitgeput, waardoor de beschik baarheid en de mogelijkheid van de toepassing voor klanten kan worden beïnvloed. In de afgelopen jaren heeft de branche een sterke toename van aanvallen gezien, met aanvallen met een meer geavanceerde en grotere omvang. DDoS-aanvallen kunnen worden gericht op elk eind punt dat openbaar bereikbaar is via internet.

## <a name="what-is-azure-ddos-protection-standard-service"></a>Wat is Azure DDoS Protection Standard-Service?
Azure DDoS Protection Standard, gecombineerd met de aanbevolen procedures voor het ontwerpen van toepassingen, biedt verbeterde DDoS-beperkings functies om te beschermen tegen DDoS-aanvallen. Het wordt automatisch afgestemd om uw specifieke Azure-resources in een virtueel netwerk te beveiligen. Beveiliging is eenvoudig in te scha kelen op een nieuw of bestaand virtueel netwerk en er zijn geen wijzigingen in de toepassing of resource. Het heeft verschillende voor delen ten opzichte van de Basic-service, inclusief logboek registratie, waarschuwingen en telemetrie. Zie [Azure DDoS Protection Standard-overzicht](ddos-protection-overview.md) voor meer informatie. 

## <a name="what-about-protection-at-the-service-layer-layer-7"></a>Hoe zit het met de beveiliging van de service laag (laag 7)?
Klanten kunnen Azure DDoS Protection Service in combi natie met [Application Gateway WAF SKU](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview) voor beveiliging gebruiken op de netwerklaag (laag 3 en 4, aangeboden door Azure DDoS Protection Service) en op de toepassingslaag (laag 7, aangeboden door Application Gateway WAF SKU).

## <a name="are-services-unsafe-in-azure-without-the-service"></a>Zijn er onveilige Services in azure zonder de service?
Services die op Azure worden uitgevoerd, worden inherent beschermd door Azure DDoS Protection Basic dat is geïmplementeerd om de infra structuur van Azure te beveiligen. De beveiliging die de infra structuur beveiligt, heeft echter een veel hogere drempel waarde dan de meeste toepassingen de capaciteit hebben om te kunnen omgaan, en biedt geen telemetrie of waarschuwingen, zodat een verkeers volume kan worden aangemerkt als onschadelijk door het platform. 

Door onboarding van de Azure DDoS Protection Standard-service krijgt de toepassing gerichte bewaking om aanvallen en specifieke drempel waarden voor toepassingen te detecteren. Een service wordt beveiligd met een profiel dat is afgestemd op het verwachte verkeers volume en biedt een veel betere bescherming tegen DDoS-aanvallen.

## <a name="what-are-the-supported-protected-resource-types"></a>Wat zijn de ondersteunde typen beveiligde bronnen?
Open bare Ip's in op ARM gebaseerde VNETs zijn momenteel het enige type beveiligde resource. PaaS Services (multi tenant) worden niet ondersteund op dit gegeven. Zie [Azure DDoS Protection standaard referentie architecturen](ddos-protection-reference-architectures.md) voor meer informatie.

## <a name="are-classicrdfe-protected-resources-supported"></a>Worden klassieke/RDFE beveiligde resources ondersteund?
In de preview-versie worden alleen beveiligde bronnen op basis van ARM ondersteund. Vm's in klassieke/RDFE-implementaties worden niet ondersteund. Er is momenteel geen ondersteuning gepland voor klassieke/RDFE-resources. Zie [Azure DDoS Protection standaard referentie architecturen](ddos-protection-reference-architectures.md) voor meer informatie.

## <a name="can-i-protect-my-on-premise-resources-using-ddos-protection"></a>Kan ik mijn on-premises resources beveiligen met behulp van DDoS Protection?
U moet de open bare eind punten van uw Service koppelen aan een VNet in azure om de DDoS-beveiliging in te scha kelen. Voor beelden van ontwerpen zijn:
- Websites (IaaS) in Azure en back-end-data bases in een on-premises Data Center. 
- Application Gateway in azure (DDoS Protection ingeschakeld op app gateway/WAF) en websites in on-premises data centers.

Zie [Azure DDoS Protection standaard referentie architecturen](ddos-protection-reference-architectures.md) voor meer informatie.

## <a name="can-i-register-a-domain-outside-of-azure-and-associate-that-to-a-protected-resource-like-vm-or-elb"></a>Kan ik een domein buiten Azure registreren en dit koppelen aan een beveiligde resource, zoals VM of ELB?
Voor de open bare IP-scenario's ondersteunt DDoS Protection-Service elke toepassing, ongeacht waar het gekoppelde domein is geregistreerd of gehost zolang het bijbehorende open bare IP-adres wordt gehost op Azure. 

## <a name="can-i-manually-configure-the-ddos-policy-applied-to-the-vnetspublic-ips"></a>Kan ik het DDoS-beleid dat is toegepast op de IP-adressen VNets/Public, hand matig configureren?
Nee, er is op dit moment geen beleids aanpassing beschikbaar.

## <a name="can-i-allowlistblocklist-specific-ip-addresses"></a>Kan ik specifieke IP-adressen allowlist/blokkerings lijst?
Nee, jammer hand matig configureren is op dit moment niet beschikbaar.

## <a name="how-can-i-test-ddos-protection"></a>Hoe kan ik DDoS Protection testen?
Zie [testen met behulp van simulaties](test-through-simulations.md).

## <a name="how-long-does-it-take-for-the-metrics-to-load-on-portal"></a>Hoe lang duurt het om de metrische gegevens te laden op de portal?
De metrische gegevens moeten binnen vijf minuten zichtbaar zijn op de portal. Als uw resource wordt aangevallen, worden er binnen 5-7 minuten andere metrische gegevens weer gegeven op de portal. 
    



