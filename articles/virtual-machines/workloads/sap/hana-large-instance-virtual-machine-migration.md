---
title: Migreren van SAP HANA op Azure (grote exemplaren) naar Azure virtual machines | Microsoft Docs
description: SAP HANA op Azure (grote exemplaren) migreren naar Azure virtual machines
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 25eae9f9ba0e28a5aa069972c8c7d5eb2877545f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967683"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA voor migratie van grote Azure-instanties naar Azure Virtual Machines
In dit artikel worden mogelijke scenario's voor grootschalige implementaties in azure beschreven en wordt de aanpak voor planning en migratie met een geminimaliseerde overgangs downtime geboden

## <a name="overview"></a>Overzicht
Sinds de aankondiging van de Azure large instances voor SAP HANA (HLI) in september 2016, hebben veel klanten deze hardware als een service-aanbieding voor hun in-Memory reken platform aangenomen.  In de afgelopen jaren is de Azure VM-grootte-uitbrei ding gecombineerd met de ondersteuning van HANA scale-out-implementatie de meeste vraag naar ERP-data bases van de onderneming.  We beginnen klanten om de interesse te bekijken om hun SAP HANA werk belasting van fysieke servers naar virtuele Azure-machines te migreren.
Deze hand leiding is geen stapsgewijze configuratie document. Hierin worden de algemene implementatie modellen beschreven en wordt geadviseerd voor de planning en migratie.  Het doel is om de nodige aandachtspunten voor de voor bereiding aan te roepen om de overgangs downtime te minimaliseren.

## <a name="assumptions"></a>Aannames
In dit artikel worden de volgende veronderstellingen aangebracht:
- De enige beoordeelde rente is een homogene HANA data base Compute Service-migratie van Hana large instance (HLI) naar Azure VM zonder aanzienlijke software-upgrade of patching. Deze kleine updates omvatten het gebruik van een recentere versie van het besturings systeem of HANA-versie expliciet vermeld zoals ondersteund door de relevante SAP-opmerkingen. 
- Alle updates/upgrade-activiteiten moeten worden uitgevoerd vóór of na de migratie.  Bijvoorbeeld SAP HANA MCOS conversie naar MDC-implementatie. 
- De migratie benadering die de minste downtime zou bieden, is SAP HANA systeem replicatie. Andere migratie methoden maken geen deel uit van het bereik van dit document.
- Deze richt lijnen zijn van toepassing op zowel Rev3-als Rev4-Sku's van HLI.
- De HANA-implementatie architectuur blijft in de eerste plaats ongewijzigd tijdens de migratie.  Dat wil zeggen dat een systeem met één exemplaar DR op dezelfde manier blijft op het doel.
- Klanten hebben de Service Level Agreement (SLA) van de doel architectuur (to-to-to-to-to-to-to-to-to-out) 
- Commerciële voor waarden tussen HLIs en Vm's verschillen. Klanten moeten het gebruik van hun Vm's controleren voor kosten beheer.
- Klanten begrijpen dat HLI een speciaal reken platform is terwijl Vm's worden uitgevoerd op een gedeelde, nog geïsoleerde infra structuur.
- Klanten hebben gevalideerd dat doel-Vm's uw beoogde architectuur ondersteunen. Zie de [SAP Hana hardware-map](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)voor een overzicht van alle ondersteunde VM-sku's die voor SAP Hana implementatie zijn gecertificeerd.
- Klanten hebben het ontwerp en migratie plan gevalideerd.
- Plan de virtuele machine voor herstel na nood gevallen samen met de primaire site.  Klanten kunnen de HLI niet gebruiken als het DR-knoop punt voor de primaire site die na de migratie op Vm's wordt uitgevoerd.
- Klanten hebben de vereiste back-upbestanden gekopieerd naar doel-Vm's, op basis van de vereisten voor bedrijfs herstel en naleving. Met back-ups van de virtuele machine kan herstel naar een bepaald tijdstip worden toegestaan tijdens de overgangs periode.
- Voor HSR HA moeten klanten het STONITH-apparaat instellen en configureren per SAP HANA HA-hand leidingen voor [SLES](./high-availability-guide-suse-pacemaker.md) en [RHEL](./high-availability-guide-rhel-pacemaker.md).  Het is niet vooraf geconfigureerd zoals de HLI-case.
- Deze migratie aanpak heeft geen betrekking op de HLI Sku's met Optane-configuratie.

## <a name="deployment-scenarios"></a>Implementatiescenario's
Algemene implementatie modellen met HLI-klanten worden in de volgende tabel samenvatten.  Migratie naar Azure Vm's voor alle HLI-scenario's is mogelijk.  Om te profiteren van aanvullende Azure-Services die beschikbaar zijn, is het mogelijk dat er kleine architectuur wijzigingen vereist zijn.

| Scenario-ID | HLI-scenario | Migreren naar VM-Verbatim? | Opmerkingen |
| --- | --- | --- | --- |
| 1 | [Eén knoop punt met één SID](./hana-supported-scenario.md#single-node-with-one-sid) | Ja | - |
| 2 | [Eén knoop punt met MCOS](./hana-supported-scenario.md#single-node-mcos) | Ja | - |
| 3 | [Eén knoop punt met behulp van opslag replicatie met DR](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication) | Nee | Opslag replicatie is niet beschikbaar met Azure Virtual platform, wijzig de huidige oplossing voor nood herstel naar HSR of Backup/Restore |
| 4 | [Eén knoop punt met DR (Multipurpose) met behulp van storage-replicatie](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication) | Nee | Opslag replicatie is niet beschikbaar met Azure Virtual platform, wijzig de huidige oplossing voor nood herstel naar HSR of Backup/Restore |
| 5 | [HSR met STONITH voor hoge Beschik baarheid](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability) | Ja | Geen vooraf geconfigureerde SBD voor doel-Vm's.  Selecteer en implementeer een STONITH-oplossing.  Mogelijke opties: Azure omheinings agent (ondersteund voor zowel [RHEL](./high-availability-guide-rhel-pacemaker.md), [SLES](./high-availability-guide-suse-pacemaker.md)), SBD |
| 6 | [HA met HSR, DR met opslag replicatie](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication) | Nee | Opslag replicatie voor nood gevallen vervangen door HSR of Backup/Restore |
| 7 | [Automatische failover van host (1 + 1)](./hana-supported-scenario.md#host-auto-failover-11) | Ja | ANF gebruiken voor gedeelde opslag met Azure-Vm's |
| 8 | [Uitschalen met stand-by](./hana-supported-scenario.md#scale-out-with-standby) | Ja | BW/4HANA met M128s, M416s, M416ms Vm's met behulp van ANF alleen voor opslag |
| 9 | [Uitschalen zonder stand-by](./hana-supported-scenario.md#scale-out-without-standby) | Ja | BW/4HANA met M128s-, M416s-, M416ms-Vm's (met of zonder gebruik van ANF voor opslag) |
| 10 | [Uitschalen met behulp van de opslag replicatie met DR](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication) | Nee | Opslag replicatie voor nood gevallen vervangen door HSR of Backup/Restore |
| 11 | [Eén knoop punt met DR met behulp van HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr) | Ja | - |
| 12 | [HSR van één knoop punt naar DR (kosten geoptimaliseerd)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized) | Ja | - |
| 13 | [HA en DR met HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr) | Ja | - |
| 14 | [HA en DR met HSR (kosten geoptimaliseerd)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Ja | - |
| 15 | [Uitschalen met DR met behulp van HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr) | Ja | BW/4HANA met M128s. M416s, M416ms Vm's (met of zonder gebruik van ANF voor opslag) |


## <a name="source-hli-planning"></a>Bron (HLI) planning
Bij het uitvoeren van een HLI-server hebben zowel het Service beheer van micro soft als klanten de planning van de berekenings-, netwerk-, opslag-en OS-specifieke instellingen door lopen om de SAP HANA-data base uit te voeren.  Vergelijk bare planningen moeten worden uitgevoerd voor de migratie naar Azure VM.

### <a name="sap-hana-housekeeping"></a>SAP HANA housekeeping 
Het is een goede manier om de inhoud van de Data Base op te ruimen, zodat ongewenste, verouderde gegevens of verlopen logboeken worden gemigreerd naar de nieuwe data base.  Housekeeping omvat in het algemeen het verwijderen of archiveren van oude, verlopen of inactieve gegevens.  Deze acties voor gegevens hygiëne moeten worden getest in niet-productie systemen om de geldigheid van de gegevens te valideren voordat het productie gebruik wordt uitgevoerd.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Netwerk verbinding toestaan voor nieuwe Vm's en, of virtueel netwerk 
In de HLI-implementatie van een klant is het netwerk ingesteld op basis van de informatie die is beschreven in het artikel [SAP Hana (grote exemplaren) netwerk architectuur](./hana-network-architecture.md). Ook wordt netwerk verkeer routering uitgevoerd op de wijze die wordt beschreven in de sectie ' route ring in azure '.
- Bij het instellen van een nieuwe VM als het migratie doel als deze wordt geplaatst in het bestaande virtuele netwerk met IP-adresbereiken die al zijn toegestaan om verbinding te maken met de HLI, is er geen verdere connectiviteits update vereist.
- Als de nieuwe virtuele machine van Azure wordt geplaatst in een nieuwe Microsoft Azure Virtual Network, zich mogelijk in een andere regio bevindt, en is gekoppeld aan het bestaande virtuele netwerk, kunnen de ExpressRoute-service sleutel en de resource-ID van de oorspronkelijke HLI-inrichting worden gebruikt om toegang te verlenen voor dit nieuwe IP-adres bereik van een virtueel netwerk.  Coördineer met micro soft service management om het virtuele netwerk naar de HLI-connectiviteit in te scha kelen.  Opmerking: om de netwerk latentie tussen de toepassing en de database lagen te minimaliseren, moeten zowel de toepassing als de database lagen zich in hetzelfde virtuele netwerk bezien.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Bestaande app Layer Availability set, Beschikbaarheidszones en proximity placement Group (PPG)
Het huidige implementatie model wordt uitgevoerd om te voldoen aan bepaalde serviceniveau doelstellingen.  Zorg er bij deze overstap voor dat de doel infrastructuur voldoet aan de ingestelde doel stellingen of deze overschrijdt.  
Meer waarschijnlijk dan niet, klanten SAP-toepassings servers worden in een beschikbaarheidsset geplaatst.  Als het huidige implementatie service niveau voldoende is en 
- Als de doel-VM de hostnaam van de logische naam van de HLI afneemt, werkt het bijwerken van de DNS-adres omzetting (Domain Name Service) die verwijst naar het IP van de virtuele machine zonder SAP-profielen bij te werken.
- Als u geen gebruik maakt van PPG, moet u ervoor zorgen dat u alle toepassings-en database servers in dezelfde zone plaatst om de netwerk latentie te minimaliseren.
- Als u PPG gebruikt, raadpleegt u de sectie van dit document: ' doel planning, Beschikbaarheidsset, Beschikbaarheidszones en proximity placement Group (PPG) '.

### <a name="storage-replication-discontinuance-process-if-used"></a>Proces voor het door gaan van de opslag replicatie (indien gebruikt)
Als opslag replicatie wordt gebruikt als de nood herstel oplossing, moet deze worden beëindigd (niet gepland) nadat de SAP-toepassing is afgesloten.  Daarnaast zijn de laatste SAP HANA-catalogus, het logboek bestand en de back-ups van gegevens gerepliceerd naar de externe opslag volumes van DR.  Dit doet u als een voorzorgsmaatregel voor het geval er een nood situatie optreedt tijdens de fysieke server naar een VM-overgang van Azure.

### <a name="data-backups-preservation-consideration"></a>Overweging bij het behoud van gegevens back-ups
Na de uitsnede van SAP HANA op Azure VM, zijn alle op moment opnamen gebaseerde gegevens of logboek back-ups op de HLI niet eenvoudig toegankelijk of kunnen ze zo nodig naar een virtuele machine worden gemigreerd. In de vroege overgangs periode is het raadzaam om, voordat de back-up op basis van Azure voldoende geschiedenis maakt om te voldoen aan de vereisten voor herstel naar een bepaald tijdstip, back-ups op bestands niveau te maken naast moment opnamen op de HLI, dagen of weken voor de functie voor knippen.  Deze back-ups worden gekopieerd naar een Azure Storage-account dat toegankelijk is voor de nieuwe SAP HANA VM.
Naast het maken van een back-up van de HLI, is het verstandig om volledige back-ups te maken van het SAP-landschap, dat eenvoudig toegankelijk is voor het geval een terugdraai actie nodig is.

### <a name="adjusting-system-monitoring"></a>Systeem bewaking aanpassen 
Klanten gebruiken veel verschillende hulpprogram ma's voor het bewaken en verzenden van waarschuwings meldingen voor systemen binnen hun SAP-landschap.  Dit item is alleen een aanroep-out voor de juiste actie om wijzigingen voor de controle op te nemen en de ontvangers van waarschuwings meldingen zo nodig bij te werken.

### <a name="microsoft-operations-team-involvement"></a>Betrokkenheid van micro soft Operations-team 
Open een ticket uit het Azure Portal op basis van de bestaande instantie van HLI.  Zodra het ondersteunings ticket is gemaakt, neemt een ondersteunings technicus via e-mail contact met u op.  

### <a name="engage-microsoft-account-team"></a>Microsoft-account team benaderen
Plan migratie bijna op het tijdstip van de vernieuwings tijd van het HLI-contract om onnodige kosten voor de reken resource te minimaliseren. Voor het buiten gebruik stellen van de HLI Blade is het vereist om de contract beëindiging en de werkelijke afsluiting van de eenheid te coördineren.

## <a name="destination-planning"></a>Doel planning
Stel een nieuwe infra structuur in om ervoor te zorgen dat de nieuwe toevoeging in het grote schema van dingen zal worden uitgevoerd.  Hieronder vindt u enkele belang rijke punten voor contemplation.

### <a name="resource-availability-in-the-target-region"></a>Beschik baarheid van resources in de doel regio 
De huidige implementatie regio van de SAP-toepassings servers bevindt zich doorgaans dicht bij de bijbehorende HLIs.  HLIs worden echter aangeboden op minder locaties dan de beschik bare Azure-regio's.  Bij de migratie van de fysieke HLI naar Azure VM is het ook een goed idee om de afstand van alle gerelateerde services voor prestatie optimalisatie te verfijnen.  Als u dit doet, is het belang rijk om ervoor te zorgen dat de gekozen regio alle vereiste resources heeft.  Bijvoorbeeld de beschik baarheid van een bepaalde VM-serie of het aanbod van Azure-zones voor het instellen van maximale Beschik baarheid.

### <a name="virtual-network"></a>Virtueel netwerk 
Klanten moeten kiezen of de nieuwe HANA-data base moet worden uitgevoerd in een bestaand virtueel netwerk of om een nieuwe te maken.  De primaire beslissende factor is de huidige netwerk indeling voor het SAP-landschap.  Ook wanneer de infra structuur van de ene zone naar de twee zones wordt geïmplementeerd en PPG gebruikt, wordt de architectuur wijziging opgelegd. Zie het artikel [Azure PPG voor optimale netwerk latentie met SAP-toepassing](./sap-proximity-placement-scenarios.md)voor meer informatie.   

### <a name="security"></a>Beveiliging
Of de nieuwe SAP HANA VM in een nieuw of bestaand vnet/subnet staat, een nieuwe bedrijfskritische service die moet worden beveiligd.  Access Control conform het beveiligings beleid van het bedrijf moet worden geëvalueerd en geïmplementeerd voor deze nieuwe service klasse.

### <a name="vm-sizing-recommendation"></a>Aanbeveling voor VM-grootte
Deze migratie is ook een kans om de grootte van uw HANA Compute-engine naar rechts te wijzigen.  De ene kan HANA- [systeem weergaven](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) gebruiken in combi natie met Hana Studio om inzicht te krijgen in het verbruik van systeem bronnen, waardoor het mogelijk is om de efficiëntie van bestedingen te verlagen.

### <a name="storage"></a>Storage 
Opslag prestaties zijn een van de factoren die van invloed zijn op de gebruikers ervaring van de SAP-toepassing.  Op basis van een bepaalde VM-SKU is er een minimale opslag indeling gepubliceerd [SAP Hana opslag configuraties van virtuele Azure-machines](./hana-vm-operations-storage.md). We raden u aan deze minimale specificaties te controleren en te vergelijken met de bestaande HLI-systeem statistieken om te zorgen voor voldoende i/o-capaciteit en prestaties voor de nieuwe HANA-VM.

Als u PPG voor de nieuwe HANA-VM en de bijbehorende servers configureert, dient u een ondersteunings ticket in om de co-locatie van de opslag en de virtuele machine te controleren en te controleren. Omdat uw back-upoplossing mogelijk moet worden gewijzigd, moeten de opslag kosten ook worden gestart om te voor komen dat operationele uitgaven aflopen.

### <a name="storage-replication-for-disaster-recovery"></a>Opslag replicatie voor herstel na nood gevallen
Met HLI werd opslag replicatie aangeboden als de standaard optie voor herstel na nood gevallen. Deze functie is niet de standaard optie voor SAP HANA op Azure VM. Overweeg HSR, back-ups maken/herstellen of andere ondersteunde oplossingen die voldoen aan de behoeften van uw bedrijf.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Beschikbaarheids sets, Beschikbaarheidszones en proximity-plaatsings groepen 
Als u de afstand tussen de toepassingslaag en SAP HANA om de netwerk latentie mini maal te beperken, moet u de nieuwe data base-VM en de huidige SAP-toepassings servers in een PPG plaatsen. Raadpleeg [proximity placementing Group](./sap-proximity-placement-scenarios.md) voor meer informatie over hoe Azure-beschikbaarheidsset en Beschikbaarheidszones werken met PPG voor SAP-implementaties.
Als leden van het doel-HANA-systeem in meer dan één Azure-zone zijn geïmplementeerd, moeten klanten een duidelijke weer gave hebben van het latentie Profiel van de gekozen zones. De plaatsing van SAP-systeem onderdelen is optimaal met betrekking tot de Proximal-afstand tussen de SAP-toepassing en de data base.  Met het [hulp programma latentie test](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) van de open bare Domein beschikbaarheids zone kunt u de meting eenvoudiger maken.  


### <a name="backup-strategy"></a>Back-upstrategie
Veel klanten maken al gebruik van back-upoplossing van derden voor SAP HANA op HLI.  In dat geval moeten alleen een extra beveiligde VM en HANA-data bases worden geconfigureerd.  Doorlopende HLI-back-uptaken kunnen nu worden ongepland als de machine na de migratie buiten gebruik wordt gesteld.
Azure Backup voor SAP HANA op VM is nu algemeen beschikbaar.  Zie de volgende koppelingen voor gedetailleerde informatie over: [back-ups maken](../../../backup/backup-azure-sap-hana-database.md), [herstellen](../../../backup/sap-hana-db-restore.md), [beheren](../../../backup/sap-hana-db-manage.md) SAP Hana back-up in virtuele machines van Azure.

### <a name="dr-strategy"></a>DR-strategie
Als uw serviceniveau doelstellingen een langere herstel tijd bieden, is een eenvoudige back-up naar Blob-opslag en herstel op locatie of herstellen naar een nieuwe VM de eenvoudigste en minst dure DR-strategie.  
Net als het platform voor grote instanties waarbij HANA DR normaal gesp roken wordt uitgevoerd met HSR; In azure VM is HSR ook de meest natuurlijke en systeem eigen SAP HANA DR-oplossing.  Ongeacht of de bron implementatie één exemplaar of geclusterd is, is een replica van de bron infrastructuur vereist in de DR-regio.
Deze DR-replica wordt geconfigureerd nadat de migratie van de primaire HLI naar de VM is voltooid.  De DR HANA DB wordt geregistreerd bij de primaire SAP HANA op het VM-exemplaar als een secundaire replicatie site.  

### <a name="sap-application-server-connectivity-destination-change"></a>Wijziging van connectiviteits doel voor SAP-toepassings server
De migratie van de HSR resulteert in een nieuwe HANA DB-host en daarom een nieuwe data base-hostnaam voor de toepassingslaag, moeten SAP-profielen worden aangepast aan de nieuwe hostnaam.  Als de naam omzetting is voltooid, is er geen profiel wijziging vereist.

### <a name="operating-system"></a>Besturingssysteem
De installatie kopieën van het besturings systeem voor HLI en VM, ondanks hetzelfde release niveau, zijn SLES 12 SP4 bijvoorbeeld niet identiek. Klanten moeten de vereiste pakketten, hotfixes, patches, kernel en beveiligings oplossingen op de HLI valideren om dezelfde pakketten op het doel te installeren.  Het wordt ondersteund om HSR te gebruiken om vanaf een ouder besturings systeem te repliceren naar een VM met een nieuwere versie van het besturings systeem.  Controleer de specifieke ondersteunde versies door [SAP-opmerking 2763388](https://launchpad.support.sap.com/#/notes/2763388)te bekijken.

### <a name="new-sap-license-request"></a>Nieuwe SAP-licentie aanvraag
Een eenvoudige aanroep om een nieuwe SAP-licentie voor het nieuwe HANA-systeem aan te vragen, nu dat is gemigreerd naar Vm's.

### <a name="service-level-agreement-sla-differences"></a>Verschillen in de Service Level Agreement (SLA)
De auteurs zouden het verschil van de beschik baarheid van de SLA tussen HLI en Azure VM kunnen aanroepen.  Zo bieden geclusterde HLIs HA-paren een Beschik baarheid van 99,99%. Als u dezelfde SLA wilt uitvoeren, moet een virtuele machine in beschikbaarheids zones worden geïmplementeerd. In dit [artikel](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) wordt de beschik baarheid van gekoppelde implementatie architecturen beschreven, zodat klanten hun doel infrastructuur dienovereenkomstig kunnen plannen.


## <a name="migration-strategy"></a>Migratiestrategie
In dit document hebben we alleen de HANA-systeem replicatie benadering voor de migratie van HLI naar Azure VM.  Afhankelijk van de doel opslag oplossing die is geïmplementeerd, verschilt het proces enigszins. De stappen op hoog niveau worden hieronder beschreven.

### <a name="vm-with-premiumultra-disks-for-data"></a>VM met Premium/Ultra-disks voor gegevens
Voor virtuele machines die zijn geïmplementeerd met Premium of Ultra disk, is de standaard configuratie van SAP HANA systeem replicatie van toepassing op het instellen van HSR.  In het [SAP Help-artikel](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) vindt u een overzicht van de stappen voor het instellen van systeem replicatie, het nemen van een secundair systeem, het uitvoeren van een failback naar de primaire en het uitschakelen van systeem replicatie.  Voor het doel van de migratie zijn alleen de stappen voor het instellen, het overnemen en uitschakelen van replicaties vereist.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>VM met ANF voor gegevens-en logboek volumes
Op hoog niveau moeten de nieuwste HLI-opslag momentopnamen van de volledige gegevens en logboek volumes worden gekopieerd naar Azure Storage waar ze toegankelijk zijn en kunnen worden hersteld door de doel-HANA-VM.  Het kopieer proces kan worden uitgevoerd met alle systeem eigen Linux-Kopieer hulpprogramma's.  

> [!IMPORTANT]
> Kopiëren en gegevens overdracht kan uren duren, afhankelijk van de grootte van de HANA-data base en de netwerk bandbreedte.  Het meren deel van het kopieer proces moet vóór de uitval tijd van de primaire HANA DB worden uitgevoerd.

### <a name="mcos-to-mdc-conversion"></a>Conversie van MCOS naar MDC
Het implementatie model voor meerdere onderdelen in één systeem (MCOS) wordt gebruikt door een aantal van onze HLI-klanten.  De motivatie was het omzeilen van de beperking van de MDC-opslag momentopname (Multiple data bases container) van eerdere SAP HANA versies.  In het MCOS-model worden meerdere onafhankelijke instanties van de SAP HANA gestapeld op één HLI-Blade.  Het gebruik van HSR voor de migratie werkt prima, maar resulteert in meerdere HANA Vm's met één Tenant database.  Met deze eind status wordt een busier-landschap gemaakt dat mogelijk is voor een klant met accustom.  Met SAP HANA 2,0 standaard implementatie die wordt MDC, is een levensvatbaar alternatief voor het uitvoeren van [Hana-Tenant verplaatsing](https://launchpad.support.sap.com/#/notes/2472478) na de HSR-migratie.  Met dit proces worden deze onafhankelijke HANA-data bases samengevoegd in cotenants in één enkele HANA-container.  

### <a name="application-layer-consideration"></a>Overweging voor de toepassingslaag
De DB-server wordt weer gegeven als Center van een SAP-systeem.  Alle toepassings servers moeten zich in de buurt van de SAP HANA DB bevinden.  In sommige gevallen, wanneer een nieuw gebruik van PPG gewenst is, wordt het verplaatsen van bestaande toepassings servers naar de PPG waar de HANA-VM mogelijk is vereist.  Het maken van nieuwe toepassings servers kan eenvoudiger worden aangemerkt als u al een implementatie sjabloon hebt.  
Als bestaande toepassings servers en de nieuwe HANA-VM optimaal zijn gevonden, moeten er geen nieuwe toepassings servers worden gebouwd, tenzij extra capaciteit is vereist.  
Als er een nieuwe infra structuur is gebouwd om de beschik baarheid van de service te verbeteren, kunnen de bestaande toepassings servers onnodig worden en moet deze worden afgesloten en worden verwijderd.
Als de hostnaam van de doel-VM is gewijzigd en verschilt van de HLI-hostnaam, moeten SAP-toepassings server profielen worden aangepast zodat deze naar de nieuwe host wijzen.  Als alleen het IP-adres van de HANA DB is gewijzigd, is er een update van de DNS-record nodig om binnenkomende verbindingen met de nieuwe HANA-VM aan te leiden.

### <a name="acceptance-test"></a>Acceptatie test
Hoewel de migratie van HLI naar VM geen aanmerkelijke wijzigingen in de inhoud van de Data Base verzorgt in vergelijking met een heterogene migratie, raden we u aan om de belangrijkste functies en het prestatie aspect van de nieuwe instellingen te valideren.  

### <a name="cutover-plan"></a>Cutover-plan
Hoewel deze migratie direct vooruit gaat, is het echter wel het buiten gebruik stellen van een bestaande data base.  Zorgvuldige planning om het bron systeem te bewaren met de bijbehorende inhoud en back-upkopieën zijn kritiek als terugval nood zakelijk is.  Een goede planning biedt een spoedigere terugboeking.   


## <a name="post-migration"></a>Na de migratie
De migratie taak wordt pas uitgevoerd als er een door de HLI afhankelijke services of verbindingen zijn losgekoppeld om ervoor te zorgen dat de gegevens integriteit behouden blijft.  Sluit ook overbodige services af.  In deze sectie vindt u enkele belang rijke items.

### <a name="decommissioning-the-hli"></a>De HLI buiten gebruik stellen
Zorg ervoor dat er na een geslaagde migratie van de HANA-Data Base naar Azure VM geen productieve zakelijke trans acties worden uitgevoerd op de HLI-data base.  Het bewaren van de HLI die gedurende een bepaalde tijd wordt uitgevoerd, is echter een veilige manier om zo nodig sneller te herstellen.  Alleen dan moet de HLI worden buiten gebruik gesteld.  Klanten moeten hun HLI-toezeg gingen met micro soft contractueel sluiten door contact op te nemen met hun micro soft-vertegenwoordigers.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Verwijder alle proxy's (bijvoorbeeld iptables, BIGIP) die zijn geconfigureerd voor HLI 
Als een proxy service zoals de IPTables wordt gebruikt voor het routeren van on-premises verkeer naar en van de HLI, is deze niet langer nodig na de geslaagde migratie naar de virtuele machine.  Deze connectiviteit service moet echter worden bewaard zolang de HLI-Blade nog steeds wordt verzorgd.  Sluit de service alleen af nadat de HLI is volledig buiten gebruik gesteld.

### <a name="remove-global-reach-for-hli"></a>Global Reach voor HLI verwijderen 
Global Reach wordt gebruikt om verbinding te maken met de ExpressRoute-gateway van klanten met de HLI ExpressRoute-gateway.  Hierdoor kan het on-premises verkeer van klanten de HLI rechtstreeks bereiken, zonder dat hiervoor een proxy service wordt gebruikt.  Deze verbinding is na de migratie niet meer nodig na het ontbreken van de HLI-eenheid.  Net als bij de service IPTables proxy moet GlobalReach ook worden bewaard totdat de HLI volledig uit bedrijf is genomen.

### <a name="operating-system-subscription--movereuse"></a>Abonnement van het besturings systeem: verplaatsen/hergebruik
Wanneer de VM-servers zijn opgelopen en de HLI-Blades buiten gebruik worden gesteld, kunnen de besturingssysteem abonnementen worden vervangen of opnieuw worden gebruikt om te voor komen dat de OS-licenties dubbel worden betaald.



## <a name="next-steps"></a>Volgende stappen
Zie deze artikelen:
- [SAP Hana infrastructuur configuraties en-bewerkingen op Azure](./hana-vm-operations.md).
- [SAP-workloads op Azure: controle lijst voor planning en implementatie](./sap-deployment-checklist.md).
