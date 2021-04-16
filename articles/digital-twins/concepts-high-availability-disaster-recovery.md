---
title: Hoge beschikbaarheid en herstel na noodgevallen
titleSuffix: Azure Digital Twins
description: Beschrijft de Functies van Azure Azure Digital Twins die u helpen bij het bouwen van azure IoT-oplossingen met hoge beschikbare mogelijkheden voor herstel na noodgevallen.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 41edef58910fe2b772831ef083e5aca8bb52a321
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482265"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure Digital Twins hoge beschikbaarheid en herstel na noodherstel

Een belangrijk aandachtspunt voor flexibele IoT-oplossingen is bedrijfscontinuïteit en herstel na noodherstel. Ontwerpen voor hoge **beschikbaarheid (HA)** en herstel na noodherstel **(DR)** kan u helpen bij het definiëren en bereiken van de juiste uptime-doelen voor uw oplossing.

In dit artikel worden de ha- en DR-functies besproken die specifiek worden aangeboden door de Azure Digital Twins service.

Azure Digital Twins ondersteunt deze functieopties:
* *Ha binnen regio'* s: ingebouwde redundantie om te zorgen voor uptime van de service
* *DR voor verschillende regio's:* failover naar een geografisch gekoppelde Azure-regio in het geval van een onverwachte datacentrumfout

U kunt ook de sectie [*Best practices voor algemene*](#best-practices) Azure-richtlijnen over ontwerpen voor ha/dr bekijken.

## <a name="intra-region-ha"></a>Ha binnen regio's
 
Azure Digital Twins biedt intraregio-HA door redundantie binnen de service te implementeren. Dit wordt weerspiegeld in de [service-SLA](https://azure.microsoft.com/support/legal/sla/digital-twins) voor uptime. **Er is geen extra werk vereist voor de ontwikkelaars van een Azure Digital Twins om te profiteren van deze ha-functies.** Hoewel Azure Digital Twins redelijk hoge uptimegarantie biedt, kunnen er nog steeds tijdelijke fouten worden verwacht, net als bij elk gedistribueerd computingplatform. Het juiste beleid voor opnieuw proberen moet worden ingebouwd in de onderdelen die communiceren met een cloudtoepassing om tijdelijke fouten op te kunnen.

## <a name="cross-region-dr"></a>Regio-overschrijdende DR

Er kunnen enkele zeldzame situaties zijn waarin een datacenter uitgebreide storingen ervaart als gevolg van stroomstoringen of andere gebeurtenissen in de regio. Dergelijke gebeurtenissen zijn zeldzaam en tijdens dergelijke fouten kan de hierboven beschreven mogelijkheid voor een intra-regio-ha mogelijk niet helpen. Azure Digital Twins lost dit op met door Microsoft geïnitieerde failover.

**Door Microsoft geïnitieerde failover** wordt in zeldzame situaties door Microsoft onder meer gebruikt om een failover uit te Azure Digital Twins van een betrokken regio naar de bijbehorende geografisch gekoppelde regio. Dit proces is een standaardoptie (zonder mogelijkheid voor gebruikers om zich af te geven) en vereist geen tussenkomst van de gebruiker. Microsoft behoudt zich het recht voor om te bepalen wanneer deze optie wordt gebruikt. Dit mechanisme vereist geen toestemming van de gebruiker voordat er een failed over-overing van het exemplaar van de gebruiker wordt gedaan.

>[!NOTE]
> Sommige Azure-services bieden ook een extra optie, de zogenaamde door de klant geïnitieerde **failover,** waarmee klanten alleen voor hun exemplaar een failover kunnen initiëren, zoals het uitvoeren van een dr-oefening. Dit mechanisme wordt momenteel **niet ondersteund** door Azure Digital Twins. 

## <a name="monitor-service-health"></a>Servicestatus bewaken

Wanneer Azure Digital Twins exemplaren worden overgenomen en hersteld, kunt u het proces bewaken met behulp van [Azure Service Health](../service-health/service-health-overview.md) hulpprogramma. Service Health de status van uw Azure-services in verschillende regio's en abonnementen bij, en deelt communicatie die invloed heeft op de service over storingen en downtimes.

Tijdens een failovergebeurtenis kunnen Service Health een indicatie geven van wanneer uw service uit staat en wanneer er een back-up van wordt gemaakt.

Als u de Service Health weergeven...
1. [Navigeer Service Health](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues) in de Azure Portal (u kunt deze koppeling gebruiken of zoeken met behulp van de portalzoekbalk).
1. Gebruik het menu links om over te schakelen naar de *pagina Statusgeschiedenis.*
1. Zoek naar een *probleemnaam die* begint **met Azure Digital Twins** en selecteer deze.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="Schermopname van de Azure Portal met de pagina Statusgeschiedenis. Er is een lijst met verschillende problemen van de afgelopen dagen en een probleem met de naam 'Azure Digital Twins - Europa - west - Mitigated' is gemarkeerd." lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. Bekijk het tabblad Samenvatting voor algemene informatie over *de* storing.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="Op de pagina Statusgeschiedenis is het tabblad Samenvatting gemarkeerd. Op het tabblad wordt algemene informatie weergegeven, zoals de resource die is beïnvloed, de regio en het abonnement." lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. Zie het tabblad Probleemupdates voor meer informatie en updates over *het probleem gedurende een* periode.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="Op de pagina Statusgeschiedenis is het tabblad Probleemupdates gemarkeerd. Op het tabblad worden verschillende vermeldingen weergegeven met de huidige status van een dag geleden." lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


Houd er rekening mee dat de informatie die in dit hulpprogramma wordt weergegeven, niet specifiek is voor één Azure Digital-exemplaar. Nadat u Service Health hebt gebruikt om te begrijpen wat er gebeurt met de Azure Digital Twins-service in een bepaalde regio of een bepaald abonnement, kunt u de bewaking een stap verder gaan door het [hulpprogramma Resource](troubleshoot-resource-health.md) health te gebruiken om in te zoomen op specifieke exemplaren en te zien of deze worden beïnvloed.

## <a name="best-practices"></a>Aanbevolen procedures

Zie de volgende Azure-richtlijnen voor dit onderwerp voor best practices voor ha/dr: 
* In het artikel [*Azure Business Continuity Technical Guidance (Technische*](/azure/architecture/framework/resiliency/overview) richtlijnen voor Azure Business Continuity) wordt een algemeen framework beschreven om u te helpen nadenken over bedrijfscontinuïteit en herstel na noodherstel. 
* Het [*document Herstel na nood en*](/azure/architecture/framework/resiliency/backup-and-recovery) hoge beschikbaarheid voor Azure-toepassingen bevat architectuur richtlijnen voor strategieën voor Azure-toepassingen voor hoge beschikbaarheid (HA) en herstel na noodherstel (DR).

## <a name="next-steps"></a>Volgende stappen 

Lees meer over hoe u aan de slag gaat Azure Digital Twins oplossingen:
 
* [*Wat is Azure Digital Twins?*](overview.md)
* [*Quickstart: Een voorbeeldscenario verkennen*](quickstart-azure-digital-twins-explorer.md)