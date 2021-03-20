---
title: Partnering met Azure DDoS Protection Standard
description: Inzicht in de mogelijkheden van partner ondersteuning die worden ingeschakeld door Azure DDoS Protection Standard.
ms.service: ddos-protection
documentationcenter: na
author: yitoh
mms.devlang: na
ms.topic: how-to
ms.date: 08/28/2020
ms.author: kumud
ms.openlocfilehash: a7a21dcaea22ed0d1c077d0c42ddb5b4bd50d24f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539329"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Partnering met Azure DDoS Protection Standard
In dit artikel worden de mogelijkheden voor partner ondersteuning beschreven die worden ingeschakeld door de Azure DDoS Protection Standard. Dit artikel is bedoeld om product managers en rollen van bedrijfs ontwikkeling inzicht te geven in de investerings paden en om inzicht te krijgen in de verwerkings waarde van de partner.

## <a name="background"></a>Achtergrond
DDoS-aanvallen (Distributed Denial of service) vormen een van de belangrijkste Beschik baarheid en beveiligings problemen die worden gefactureerd door klanten die hun toepassingen naar de Cloud verplaatsen. Omdat Extortion en Hacktivisme de gebruikelijke motivaties achter DDoS-aanvallen zijn, zijn ze steeds groter geworden in het type, de schaal en de frequentie van het aantal keren dat ze relatief eenvoudig en goed koop kunnen worden gestart.

Azure DDoS Protection biedt tegen de meest geavanceerde DDoSe bedreigingen de wereld wijde schaal van Azure-netwerken. De service biedt verbeterde DDoS-beperkings mogelijkheden voor toepassingen en resources die in virtuele netwerken zijn geïmplementeerd.

Technologie partners kunnen hun resources van hun klanten systeem eigen beveiligen met Azure DDoS Protection Standard om de beschik baarheid en betrouw baarheid te verhelpen vanwege DDoS-aanvallen.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Inleiding tot Azure DDoS Protection Standard
Azure DDoS Protection Standard biedt verbeterde DDoS-oplossings mogelijkheden voor aanvallen op laag 3 en laag 4 DDoS. Hier volgen de belangrijkste functies van DDoS Protection Standard-Service.

### <a name="adaptive-real-time-tuning"></a>Adaptieve real-time tuning
Voor elke beveiligde toepassing worden met Azure DDoS Protection standaard de drempel waarden voor het DDoS-beperkings beleid automatisch afgestemd op basis van de patronen van het verkeers Profiel van de toepassing. Deze aanpassing wordt door de service uitgevoerd met behulp van twee inzichten:

- Automatisch leren van verkeers patronen per klant (per IP) voor laag 3 en 4.
- Het minimaliseren van valse positieven, waarbij rekening wordt gehouden met de schaal van Azure, waarmee IT een aanzienlijke hoeveelheid verkeer kan absorberen.

![Adaptieve real time tuning](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Aanvals analyses, telemetrie, bewaking en waarschuwingen
Azure DDoS Protection identificeert en vermindert DDoS-aanvallen zonder tussen komst van de gebruiker.

- Als de beveiligde resource zich in het abonnement bevindt dat onder Azure Security Center wordt behandeld, stuurt DDoS Protection standaard automatisch een waarschuwing naar Security Center wanneer een DDoS-aanval wordt gedetecteerd en gereduceerd op basis van de beveiligde toepassing.
- Als u een melding wilt ontvangen wanneer er een actieve beperking is voor een beveiligd openbaar IP-adres, kunt u ook [een waarschuwing configureren](alerts.md) voor de metriek onder DDoS-aanval of niet.
- U kunt er ook voor kiezen om waarschuwingen te maken voor de andere DDoS-metrische gegevens en [telemetrie van aanvallen te configureren](telemetry.md) om inzicht te krijgen in de schaal van de aanval, het verkeer dat wordt neergezet, de aanvals vectoren, de belangrijkste inzenders en andere gegevens.

![Metrische gegevens van DDoS](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS Rapid Response (DRR)
DDoS Protection Standard-klanten hebben toegang tot het [snelle respons team](ddos-rapid-response.md) tijdens een actieve aanval. DRR kan helpen bij het onderzoeken van een aanval tijdens een aanval en na een aanvals analyse.

### <a name="sla-guarantee-and-cost-protection"></a>SLA-garantie en kosten beveiliging
DDoS Protection Standard-service wordt gedekt door een SLA van 99,99% en kosten bescherming biedt resource-tegoed voor uitschalen tijdens een gedocumenteerde aanval. Zie [Sla voor Azure DDoS Protection](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/)voor meer informatie.

## <a name="featured-partner-scenarios"></a>Scenario's voor aanbevolen partners
Hier volgen de belangrijkste voor delen die u kunt afleiden door te integreren met de Azure DDoS Protection Standard:

- De aangeboden services van partners (load balancer, Web Application Firewall, firewall, enz.) aan hun klanten worden automatisch beveiligd (wit met een label) door Azure DDoS Protection standaard in de back-end.
- Partners hebben toegang tot Azure DDoS Protection Standard-aanvals analyse en telemetrie die ze kunnen integreren met hun eigen producten, en bieden een uniforme klant ervaring.  
- Partners hebben toegang tot ondersteuning voor DDoS snelle reacties, zelfs als Azure Rapid Response geen problemen ondervindt voor DDoS.
- Beveiligde toepassingen van partners worden ondersteund door een DDoS SLA-garantie en kosten beveiliging in het geval van DDoS-aanvallen.

## <a name="technical-integration-overview"></a>Overzicht van technische integratie
Azure DDoS Protection standaard verkoop kansen voor partners worden beschikbaar gesteld via Azure Portal, Api's en CLI/PS.

### <a name="integrate-with-ddos-protection-standard"></a>Integreren met DDoS Protection Standard
De volgende stappen zijn vereist voor partners voor het configureren van integratie met Azure DDoS Protection Standard:
1. Maak een DDoS Protection Plan in uw gewenste partner abonnement. Zie [een DDoS Standard-beveiligings plan maken](manage-ddos-protection.md#create-a-ddos-protection-plan)voor stapsgewijze instructies.
   > [!NOTE]
   > Er moet slechts één DDoS Protection Plan worden gemaakt voor een bepaalde Tenant. 
2. Implementeer een service met een openbaar eind punt in uw (partner) abonnementen, zoals load balancer, firewalls en Web Application Firewall. 
3. Schakel Azure DDoS Protection standaard in op het virtuele netwerk van de service met open bare eind punten die gebruikmaken van DDoS Protection Plan dat in de eerste stap is gemaakt. Zie [DDoS Standard-beveiligings plan inschakelen](manage-ddos-protection.md#enable-ddos-protection-for-an-existing-virtual-network) voor stpe-by-Step-instructies
   > [!IMPORTANT] 
   > Nadat Azure DDoS Protection standaard is ingeschakeld in een virtueel netwerk, worden alle open bare IP-adressen binnen dat virtuele netwerk automatisch beveiligd. De oorsprong van deze open bare Ip's kan binnen Azure (client abonnement) of buiten Azure vallen. 
4. U kunt eventueel Azure DDoS Protection standaard-telemetrie en aanvals analyses integreren in uw toepassingsspecifiek dash board dat specifiek is voor de klant. Zie [DDoS Protection-telemetrie weer geven en configureren](telemetry.md)voor meer informatie over het gebruik van telemetrie. 

### <a name="onboarding-guides-and-technical-documentation"></a>Gidsen en technische documentatie voor onboarding

- [Azure DDoS Protection product pagina](https://azure.microsoft.com/services/ddos-protection/)
- [Documentatie over Azure DDoS Protection](ddos-protection-overview.md)
- [Naslag informatie over Azure DDoS Protection-API](/rest/api/virtualnetwork/ddosprotectionplans)
- [Naslag informatie over Azure Virtual Network API](/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Hulp vragen

- Neem contact op met de [Azure-beveiligings Community](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)als u vragen hebt over de integratie van toepassingen, services of producten met Azure DDoS Protection Standard.
- Volg de discussies op [stack overflow](https://stackoverflow.com/tags/azure-ddos/).

### <a name="get-to-market"></a>Ga naar de markt

- Het primaire programma voor het samen werken met micro soft is de [Microsoft Partner Network](https://partner.microsoft.com/). – Microsoft Graph beveiligings integraties vallen binnen het [ISV-nummer (MPN Independent Software Vendor)](https://partner.microsoft.com/saas-solution-guide) .
- [Micro soft intelligent Security Association](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) is het programma dat specifiek is voor micro soft-beveiligings partners om uw beveiligings producten te verrijken en de detectie van de klant te verbeteren van uw integraties naar micro soft-beveiligings producten.

## <a name="next-steps"></a>Volgende stappen
Bestaande partner integraties weer geven:

- [Barracuda WAF-as-a-Service](https://www.barracuda.com/waf-as-a-service)
- [Azure Cloud WAF van Radware](https://www.radware.com/resources/microsoft-azure/)
