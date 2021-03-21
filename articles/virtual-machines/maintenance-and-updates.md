---
title: Onderhoud en updates
description: Overzicht van onderhoud en updates voor virtuele machines die worden uitgevoerd in Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: shants
ms.openlocfilehash: 51df72e31acaadc83f4c094b99fa938377e5f023
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499999"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Onderhoud voor virtuele machines in Azure

Azure werkt periodiek het platform bij om de betrouw baarheid, prestaties en beveiliging van de host-infra structuur voor virtuele machines te verbeteren. Het doel van deze updates behelst zowel het uitvoeren van softwarepatches voor onderdelen in de hostomgeving als het upgraden van netwerkonderdelen of het buiten gebruik stellen van hardware. 

Updates zijn zelden van invloed op de gehoste Vm's. Wanneer de updates een effect hebben, kiest Azure de methode van de minst meest impact op updates:

- Als de update niet opnieuw moet worden opgestart, wordt de virtuele machine onderbroken terwijl de host wordt bijgewerkt, of wordt de virtuele machine Live gemigreerd naar een al bijgewerkte host. 
- Als het onderhoud opnieuw moet worden opgestart, ontvangt u een melding van het geplande onderhoud. Azure biedt ook een tijd venster waarin u het onderhoud zelf kunt starten, op het moment dat u voor u werkt. Het venster voor zelf onderhoud is doorgaans 35 dagen, tenzij het onderhoud urgent is. Azure is investeren in technologieën om het aantal cases te verminderen waarvoor gepland platform onderhoud vereist dat de Vm's opnieuw worden opgestart. Zie voor instructies voor het beheren van gepland onderhoud geplande onderhouds meldingen verwerken met Azure [cli](maintenance-notifications-cli.md), [Power shell](maintenance-notifications-powershell.md) of [Portal](maintenance-notifications-portal.md).

Op deze pagina wordt beschreven hoe Azure beide soorten onderhoud uitvoert. Zie [de beschik baarheid van vm's voor Windows](./availability.md) of het bijbehorende artikel voor [Linux](./availability.md)beheren voor meer informatie over niet-geplande gebeurtenissen (uitval).

Binnen een VM kunt u meldingen ontvangen over aanstaande onderhoud door [gebruik te maken van Scheduled Events voor Windows](./windows/scheduled-events.md) of voor [Linux](./linux/scheduled-events.md).



## <a name="maintenance-that-doesnt-require-a-reboot"></a>Onderhoud waarvoor geen herstart nodig is

De meeste platform updates zijn niet van invloed op virtuele machines van klanten. Wanneer een niet-impact update niet mogelijk is, kiest Azure het update mechanisme dat het minst invloed heeft op virtuele machines van de klant. 

De meeste onderhoud van niet-nul-impact pauzeert de virtuele machine korter dan 10 seconden. In bepaalde gevallen maakt Azure gebruik van geheugen behoud van onderhouds mechanismen. Deze mechanismen pauzeren de virtuele machine tot 30 seconden en bewaren het geheugen in het RAM. De virtuele machine wordt vervolgens hervat en de klok wordt automatisch gesynchroniseerd. 

Het onderhoud van geheugen-behoud werkt meer dan 90 procent van de Azure-Vm's. Het werkt niet voor G, M, N en H-serie. Azure maakt steeds meer gebruik van Live-migratie technologieën en verbetert de onderhouds mechanismen van het geheugen om de onderbrekings duur te verminderen.  

Voor deze onderhouds bewerkingen die niet opnieuw moeten worden opgestart, wordt één fout domein tegelijk toegepast. Deze worden gestopt als er waarschuwings signalen worden ontvangen van de hulpprogram ma's voor het controleren van het platform. 

Deze typen updates kunnen van invloed zijn op sommige toepassingen. Wanneer de virtuele machine Live naar een andere host wordt gemigreerd, kan het even duren voordat de virtuele machine wordt onderbroken door bepaalde gevoelige workloads. Voor de voor bereiding op het onderhoud van de virtuele machine en het verminderen van de impact tijdens het onderhoud van Azure, kunt [u Scheduled Events voor Windows](./windows/scheduled-events.md) of [Linux](./linux/scheduled-events.md) gebruiken voor dergelijke toepassingen. 

Voor meer controle over alle onderhouds activiteiten, inclusief het bijwerken van nul of het opnieuw opstarten van updates, kunt u de functie onderhouds beheer gebruiken. U moet een met [Azure toegewezen hosts](./dedicated-hosts.md) of een [geïsoleerde VM](../security/fundamentals/isolation-choices.md)gebruiken. Onderhouds beheer biedt u de optie om alle platform updates over te slaan en de updates op uw gewenste tijdstip toe te passen binnen een 35-daagse venster. Zie [updates beheren met onderhouds beheer en de Azure cli](maintenance-control.md)voor meer informatie.


### <a name="live-migration"></a>Livemigratie

Livemigratie is een bewerking waarvoor geen herstart vereist is en waarmee het geheugen voor de virtuele machine wordt bewaard. Dit veroorzaakt een pauze ring of blok keren, meestal niet meer dan 5 seconden. Met uitzonde ring van G, M, N en H-serie, kunnen alle IaaS-Vm's (Infrastructure as a Service) in aanmerking komen voor Livemigratie. In aanmerking komende Vm's vertegenwoordigen meer dan 90 procent van de IaaS-Vm's die zijn geïmplementeerd op de Azure-vloot. 

De Livemigratie van het Azure-platform wordt gestart in de volgende scenario's:
- Gepland onderhoud
- Hardwarestoring
- Toewijzings optimalisaties

Bij sommige scenario's gepland onderhoud wordt gebruikgemaakt van Livemigratie, en u kunt Scheduled Events gebruiken om vooraf te weten wanneer Livemigratie wordt gestart.

Livemigratie kan ook worden gebruikt om Vm's te verplaatsen wanneer Azure Machine Learning algoritmen een dreigende hardwarestoring of als u de VM-toewijzingen wilt optimaliseren. Zie de [tolerantie van Azure-Vm's verbeteren met voorspellende machine learning en Livemigratie](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)voor meer informatie over voorspellende modellen die exemplaren van gedegradeerde hardware detecteren. Live-migratie meldingen worden weer gegeven in de Azure Portal in de monitor en Service Health logboeken, evenals in Scheduled Events als u deze services gebruikt.

## <a name="maintenance-that-requires-a-reboot"></a>Onderhoud waarvoor opnieuw opstarten is vereist

In de zeldzame gevallen waarin Vm's opnieuw moeten worden opgestart voor gepland onderhoud, wordt u hiervan op de hoogte gebracht. Gepland onderhoud bestaat uit twee fasen: de self-service fase en een geplande onderhouds fase.

Tijdens de *self-service fase*, die doorgaans vier weken duurt, start u het onderhoud op uw vm's. Als onderdeel van de self-service kunt u een query uitvoeren op elke virtuele machine om de status en het resultaat van uw laatste onderhouds aanvraag te bekijken.

Wanneer u self-service onderhoud start, wordt uw VM opnieuw geïmplementeerd op een al bijgewerkt knoop punt. Omdat de VM opnieuw wordt opgestart, gaat de tijdelijke schijf verloren en worden dynamische IP-adressen die zijn gekoppeld aan de virtuele netwerk interface, bijgewerkt.

Als er een fout optreedt tijdens self-service onderhoud, wordt de bewerking gestopt, wordt de VM niet bijgewerkt en krijgt u de mogelijkheid om het selfservice onderhoud opnieuw uit te voeren. 

Wanneer de self-service fase eindigt, begint de *geplande onderhouds fase* . Tijdens deze fase kunt u nog steeds een query uitvoeren voor de onderhouds fase, maar kunt u het onderhoud zelf niet starten.

Zie voor meer informatie over het beheren van onderhoud waarvoor opnieuw opstarten is vereist het **verwerken van geplande onderhouds meldingen** met behulp van de Azure [cli](maintenance-notifications-cli.md), [Power shell](maintenance-notifications-powershell.md) of [Portal](maintenance-notifications-portal.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Beschik baarheid van overwegingen tijdens gepland onderhoud 

Als u van plan bent te wachten tot de geplande onderhouds fase, zijn er enkele dingen die u moet overwegen om de hoogst mogelijke Beschik baarheid van uw Vm's te hand haven. 

#### <a name="paired-regions"></a>Gekoppelde regio's

Elke Azure-regio is gekoppeld aan een andere regio binnen dezelfde geografische omgeving. Samen maken ze een regio paar. Tijdens de geplande onderhouds fase worden in azure alleen de Vm's in één regio van een regio paar bijgewerkt. Tijdens het bijwerken van de virtuele machine in Noord-Centraal VS, werkt Azure de virtuele machine in Zuid-Centraal VS bijvoorbeeld niet bij. Tegelijkertijd met VS - oost kan er echter wel onderhoud plaatsvinden in andere regio's, zoals Europa - noord. Meer informatie over het werken met regio paren kan u helpen uw Vm's beter te verdelen over regio's. Zie [Azure Region-paren](../best-practices-availability-paired-regions.md)voor meer informatie.

#### <a name="availability-sets-and-scale-sets"></a>Beschikbaarheids sets en schaal sets

Bij het implementeren van een werk belasting op virtuele machines in azure, kunt u de virtuele machines binnen een *beschikbaarheidsset* maken om hoge Beschik baarheid voor uw toepassing te bieden. Met behulp van beschikbaarheids sets kunt u ervoor zorgen dat tijdens een storing of onderhouds gebeurtenis die opnieuw moet worden opgestart, ten minste één virtuele machine beschikbaar is.

Binnen een beschikbaarheidsset worden afzonderlijke Vm's verdeeld over Maxi maal 20 Update domeinen. Tijdens gepland onderhoud wordt slechts één update domein op een bepaald moment bijgewerkt. Update domeinen worden niet noodzakelijkerwijs opeenvolgend bijgewerkt. 

Virtuele-machine *schaal sets* vormen een Azure Compute-resource die u kunt gebruiken om een set identieke vm's te implementeren en te beheren als één resource. De schaalset wordt automatisch geïmplementeerd in de UDs, zoals virtuele machines in een beschikbaarheidsset. Net als bij beschikbaarheids sets, wanneer u schaal sets gebruikt, wordt slechts één UD bijgewerkt op een wille keurig tijdstip tijdens gepland onderhoud.

Zie [de beschik baarheid van uw vm's voor Windows](./availability.md) of het bijbehorende artikel voor [Linux](./availability.md)beheren voor meer informatie over het instellen van uw vm's voor maximale Beschik baarheid.

#### <a name="availability-zones"></a>Beschikbaarheidszones

Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Om voor tolerantie te zorgen, is er een minimum van drie afzonderlijke zones in alle ingeschakelde regio's. 

Een beschikbaarheids zone is een combi natie van een fout domein en een update domein. Als u drie of meer virtuele machines maakt voor drie zones in een Azure-regio, worden uw Vm's effectief gedistribueerd over drie fout domeinen en drie update domeinen. Het Azure-platform herkent deze verdeling over updatedomeinen om ervoor te zorgen dat virtuele machines in verschillende zones niet op hetzelfde moment worden bijgewerkt.

Elke infrastructuur update implementeert zone per zone, binnen één regio. Maar u kunt een implementatie uitvoeren in Zone 1 en tegelijkertijd een andere implementatie uitvoeren in Zone 2. Implementaties zijn niet allemaal geserialiseerd. Maar met één implementatie wordt slechts één zone tegelijk uitgedraaid om het risico te verminderen.

## <a name="next-steps"></a>Volgende stappen 

U kunt de [Azure cli](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) of de [Portal](maintenance-notifications-portal.md) gebruiken voor het beheren van gepland onderhoud.