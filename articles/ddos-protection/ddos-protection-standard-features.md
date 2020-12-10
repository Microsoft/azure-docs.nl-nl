---
title: Azure DDoS Protection functies
description: Azure DDoS Protection-functies leren
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 104c9dcd3b7fd931e4f54841c9de9d17cfd72353
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937318"
---
# <a name="azure-ddos-protection-standard-features"></a>Azure DDoS Protection Standard-functies

De volgende secties bevatten een overzicht van de belangrijkste functies van de Azure DDoS Protection Standard-Service.

## <a name="always-on-traffic-monitoring"></a>Bewaking over altijd verkeer

DDoS Protection Standard bewaakt het werkelijke verkeers gebruik en vergelijkt deze met de drempel waarden die zijn gedefinieerd in het DDoS-beleid. Wanneer de drempel waarde voor verkeer wordt overschreden, wordt de DDoS-beperking automatisch gestart. Wanneer het verkeer onder de drempel waarde komt, wordt de risico beperking verwijderd.

![Azure DDoS Protection standaard beperking](./media/ddos-protection-overview/mitigation.png)

Tijdens de risico beperking wordt het verkeer dat wordt verzonden naar de beveiligde bron, omgeleid door de DDoS Protection-Service en worden er diverse controles uitgevoerd, zoals de volgende controles:

- Zorg ervoor dat de pakketten voldoen aan de Internet specificaties en niet zijn beschadigd.
- Communiceer met de client om te bepalen of het verkeer mogelijk een vervalst pakket is (bijvoorbeeld: SYN auth of SYN cookie of door een pakket voor de bron te verwijderen om het opnieuw te verzenden).
- Aantal pakketten beperken als er geen andere afdwing methode kan worden uitgevoerd.

DDoS-beveiliging blokkeert aanvalsverkeer en stuurt het resterende verkeer door naar de beoogde bestemming. Binnen een paar minuten na detectie van aanvallen wordt u op de hoogte gebracht met behulp van metrische gegevens van Azure Monitor. Door logboek registratie in DDoS Protection standaard-telemetrie te configureren, kunt u de logboeken schrijven naar beschik bare opties voor toekomstige analyse. Metrische gegevens in Azure Monitor voor DDoS Protection standaard worden 30 dagen bewaard.

## <a name="adaptive-real-time-tuning"></a>Adaptieve real time tuning

De Azure DDoS Protection Basic-service helpt klanten te beschermen en te voor komen dat ze van invloed zijn op andere klanten. Als een service bijvoorbeeld is ingericht voor een typisch volume van legitiem binnenkomend verkeer dat kleiner is dan de frequentie van de *Activering* van het beleid voor de hele infrastructuur DDoS Protection, kan een DDoS-aanval op de resources van die klant niet worden opgemerkt. Meer in het algemeen, de complexiteit van recente aanvallen (bijvoorbeeld multi-vector DDoS) en het toepassingsspecifieke gedrag van tenants aanroepen voor een aangepast beveiligings beleid per klant. Deze aanpassing wordt door de service uitgevoerd met behulp van twee inzichten:

- Automatisch leren van verkeers patronen per klant (per IP) voor laag 3 en 4.

- Het minimaliseren van valse positieven, waarbij rekening wordt gehouden met de schaal van Azure, waarmee IT een aanzienlijke hoeveelheid verkeer kan absorberen.

![Diagram van de werking van DDoS Protection Standard, met omcirkeld beleid genereren](./media/ddos-best-practices/image-5.png)

## <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS Protection telemetrie, bewaking en waarschuwingen

DDoS Protection Standard maakt voor de duur van een DDoS-aanval uitgebreide telemetrie mogelijk via [Azure monitor](../azure-monitor/overview.md) . U kunt waarschuwingen configureren voor elk van de Azure Monitor metrische gegevens die DDoS Protection gebruikt. U kunt logboek registratie integreren met Splunk (Azure Event Hubs), Azure Monitor-logboeken en Azure Storage voor geavanceerde analyse via de diagnostische-interface voor Azure Monitor.

### <a name="ddos-mitigation-policies"></a>DDoS-beperkings beleid

Selecteer in de Azure Portal **Monitor**  >  **metrische gegevens** controleren. Selecteer de resource groep in het deel venster **metrische gegevens** , selecteer een resource type voor het **open bare IP-adres** en selecteer uw open bare Azure IP-adres. DDoS-metrische gegevens worden weer gegeven in het deel venster **beschik bare metrische gegevens** .

DDoS Protection Standard geldt voor elk openbaar IP-adres van de beveiligde bron, in het virtuele netwerk waarop DDoS is ingeschakeld, drie opties voor het beperken van de oplossing (TCP SYN, TCP en UDP). U kunt de drempel waarden voor het beleid weer geven door de metrische **inkomende pakketten te selecteren om de DDoS-beperking te activeren**.

![Diagram beschik bare metrische gegevens en metrische gegevens](./media/ddos-best-practices/image-7.png)

De beleids drempels worden automatisch geconfigureerd via een machine learning-gebaseerd netwerk verkeer profile ring. DDoS-beperking treedt alleen op voor een IP-adres onder een aanval wanneer de drempel waarde voor het beleid wordt overschreden.

### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Metric voor een IP-adres onder DDoS-aanval

Als het open bare IP-adres zich onder een aanval bevindt, wordt de waarde voor de metrische gegevens **onder DDoS-aanval of niet** gewijzigd in 1 als DDoS Protection een risico op het risico verkeer uitvoert.

!["Onder DDoS-aanval of niet" metrisch en diagram](./media/ddos-best-practices/image-8.png)

U kunt het beste een waarschuwing configureren voor deze metrische gegevens. U krijgt dan een melding wanneer er een actieve DDoS-beperking is uitgevoerd op uw open bare IP-adres.

Zie [Azure DDoS Protection Standard beheren met de Azure Portal](manage-ddos-protection.md)voor meer informatie.

## <a name="web-application-firewall-for-resource-attacks"></a>Web Application Firewall voor bron aanvallen

Wat specifiek is voor bron aanvallen op de toepassingslaag, moet u een Web Application Firewall (WAF) configureren om webtoepassingen te beveiligen. Een WAF inspecteert inkomend webverkeer om SQL-injecties, cross-site scripting, DDoS en andere Layer 7-aanvallen te blok keren. Azure biedt [WAF als een functie van Application Gateway](../web-application-firewall/ag/ag-overview.md) voor gecentraliseerde beveiliging van uw webtoepassingen van veelvoorkomende aanvallen en beveiligings problemen. Er zijn andere WAF-aanbiedingen beschikbaar van Azure-partners die mogelijk beter geschikt zijn voor uw behoeften via [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Zelfs firewalls voor webtoepassingen zijn gevoelig voor volumetrische aanvallen en status afputting. We raden u ten zeerste aan om DDoS Protection Standard in te scha kelen op het virtuele WAF-netwerk om te helpen beschermen tegen maat-en protocol aanvallen. Zie de sectie met [DDoS Protection referentie architecturen](ddos-protection-reference-architectures.md) voor meer informatie.

## <a name="protection-planning"></a>Beveiliging plannen

Planning en voor bereiding zijn essentieel om te begrijpen hoe een systeem tijdens een DDoS-aanval wordt uitgevoerd. Het ontwerpen van een respons plan voor incident beheer maakt deel uit van deze inspanning.

Als u DDoS Protection Standard hebt, moet u ervoor zorgen dat deze is ingeschakeld op het virtuele netwerk van Internet gerichte eind punten. Het configureren van DDoS-waarschuwingen helpt u voortdurend te kijken naar mogelijke aanvallen op uw infra structuur. 

Bewaak uw toepassingen onafhankelijk. Het normale gedrag van een toepassing begrijpen. Bereid u voor op een besluit te nemen als de toepassing niet naar verwachting functioneert tijdens een DDoS-aanval. 

Meer informatie over hoe uw services reageren op een aanval door te [testen via simulaties](test-through-simulations.md).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van een DDoS-beschermings plan](manage-ddos-protection.md).