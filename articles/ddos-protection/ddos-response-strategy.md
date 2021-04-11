---
title: Onderdelen van een DDoS-reactiestrategie
description: Meer informatie over hoe u Azure DDoS Protection Standard kunt gebruiken om te reageren op DDoS-aanvallen.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: c028185a0753bc4da966ebc56bb792e57cac1f77
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103042"
---
# <a name="components-of-a-ddos-response-strategy"></a>Onderdelen van een DDoS-reactiestrategie

Een DDoS-aanval die gericht is op Azure-resources, vereist meestal een minimale tussen komst van een gebruikers oogpunt. Als onderdeel van een strategie voor incident reacties is het niet meer mogelijk om de impact op de bedrijfs continuïteit te beperken.

## <a name="microsoft-threat-intelligence"></a>Micro soft Threat Intelligence

Micro soft heeft een uitgebreid netwerk met bedreigings informatie. Dit netwerk maakt gebruik van de collectieve kennis van een uitgebreide beveiligings community die ondersteuning biedt voor micro soft onlineservices, micro soft-partners en relaties binnen de Internet beveiligings community. 

Als kritieke infrastructuur provider ontvangt micro soft vroegtijdige waarschuwingen over bedreigingen. Micro soft verzamelt bedreigings informatie van de onlineservices en van zijn algemene klanten database. Micro soft brengt al deze bedreigings intelligentie weer in de Azure DDoS Protection-producten.

De micro soft-eenheid voor digitale misdrijven (DCU) voert ook aanstootgevende strategieën uit op botnets. Botnets zijn een gemeen schappelijke bron van opdracht en beheer voor DDoS-aanvallen.

## <a name="risk-evaluation-of-your-azure-resources"></a>Risico-evaluatie van uw Azure-resources

Het is belang rijk om het bereik van uw risico van een DDoS-aanval voortdurend te begrijpen. Stel u regel matig in:

- Welke nieuwe open bare Azure-resources moeten worden beschermd?

- Is er een Single Point of Failure in de service? 

- Hoe kunnen services worden geïsoleerd om de impact van een aanval te beperken terwijl services beschikbaar blijven voor geldige klanten?

- Zijn er virtuele netwerken waar DDoS Protection Standard moet worden ingeschakeld, maar niet? 

- Zijn mijn Services actief/actief met failover over meerdere regio's?

Het is essentieel dat u het normale gedrag van een toepassing begrijpt en bereidt om te reageren als de toepassing niet naar verwachting functioneert tijdens een DDoS-aanval. Beschikken over monitors die zijn geconfigureerd voor uw bedrijfskritische toepassingen die het client gedrag simuleren, en u waarschuwen wanneer relevante afwijkingen worden gedetecteerd. Raadpleeg de [Aanbevolen procedures voor bewaking en diagnose](/azure/architecture/best-practices/monitoring#monitoring-and-diagnostics-scenarios) om inzicht te krijgen in de status van uw toepassing.

[Azure-toepassing Insights](../azure-monitor/app/app-insights-overview.md) is een UITBREID bare apm-service (Application Performance Management) voor webontwikkelaars op meerdere platforms. Gebruik Application Insights om uw Live Web-app te bewaken. Er worden automatisch prestatie afwijkingen gedetecteerd. Het bevat hulpprogram ma's voor analyse waarmee u problemen kunt vaststellen en inzicht kunt krijgen in wat gebruikers met uw app doen. Het is bedoeld om u te helpen de prestaties en bruikbaarheid continu te verbeteren.

## <a name="customer-ddos-response-team"></a>DDoS Response Team van de klant

Het maken van een DDoS-antwoord team is een belang rijke stap in het snel en effectief reageren op een aanval. Identificeer contact personen in uw organisatie die zowel de planning als de uitvoering nagaan. Dit DDoS-antwoord team moet de Azure DDoS Protection Standard-Service grondig begrijpen. Zorg ervoor dat het team een aanval kan identificeren en beperken door te coördineren met interne en externe klanten, waaronder het micro soft-ondersteunings team. 

We raden u aan om simulatie oefeningen te gebruiken als een normaal onderdeel van de planning van de beschik baarheid van uw service en voor het plannen van de continuïteit. deze oefeningen moeten schaal tests omvatten. Zie [testen door simulaties](test-through-simulations.md) om te leren hoe u DDoS test verkeer kunt simuleren op basis van uw open bare Azure-eind punten.

## <a name="alerts-during-an-attack"></a>Waarschuwingen tijdens een aanval

Azure DDoS Protection Standard identificeert en vermindert DDoS-aanvallen zonder tussen komst van de gebruiker. Als u een melding wilt ontvangen wanneer er een actieve beperking is voor een beveiligd openbaar IP-adres, kunt u [waarschuwingen configureren](alerts.md).

### <a name="when-to-contact-microsoft-support"></a>Wanneer u contact opneemt met micro soft ondersteuning

Azure DDoS Protection Standard-klanten hebben toegang tot het DRR-team (DDoS Rapid Response), die u kunnen helpen bij het onderzoeken van aanvallen tijdens een aanval en na een aanvals analyse. Zie [DDoS snelle reactie](ddos-rapid-response.md) voor meer informatie, waaronder wanneer u het DRR-team moet benaderen.

## <a name="post-attack-steps"></a>Stappen na aanval

Het is altijd een goede strategie om een postmortem na een aanval uit te voeren en de DDoS-respons strategie zo nodig aan te passen. Aandachtspunten:

- Is er een onderbreking van de service of gebruikers ervaring vanwege een gebrek aan schaal bare architectuur?

- Welke toepassingen of services hebben het meest geleden?

- Hoe effectief was de DDoS-respons strategie en hoe kan deze worden verbeterd?

Als u vermoedt dat u een DDoS-aanval ondervindt, kunt u uw normale Azure-ondersteunings kanalen escaleren.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van een DDoS-beschermings plan](manage-ddos-protection.md).