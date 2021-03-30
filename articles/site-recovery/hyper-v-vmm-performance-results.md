---
title: Replicatie van Hyper-V-VM'S naar een secundaire site met VMM testen met behulp van Azure Site Recovery
description: Dit artikel bevat informatie over prestatie tests voor de replicatie van virtuele Hyper-V-machines in VMM-Clouds naar een secundaire site met behulp van Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: 6c8219214e7053dcf6b119f6cd5dc97daaa355f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92327634"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Test resultaten voor Hyper-V-replicatie naar een secundaire site


In dit artikel vindt u de resultaten van het testen van de prestaties bij het repliceren van virtuele Hyper-V-machines in System Center Virtual Machine Manager-Clouds (VMM) naar een secundair Data Center.

## <a name="test-goals"></a>Test doelen

Het doel van testen is om te onderzoeken hoe Site Recovery tijdens stabiele status replicatie uitvoert.

- Stabiele status replicatie vindt plaats wanneer virtuele machines de initiële replicatie hebben voltooid en wijzigingen in de Delta worden gesynchroniseerd.
- Het is belang rijk om prestaties te meten met behulp van een stabiele status, omdat het de status is van de meeste Vm's, tenzij er onverwachte storingen optreden.
- De test implementatie bestaat uit twee on-premises sites, met een VMM-server in elke site. Deze test implementatie is een typische implementatie van een hoofd kantoor/filiaal, met hoofd kantoor dat fungeert als de primaire site en het filiaal als de secundaire of herstel site.

## <a name="what-we-did"></a>Wat we hebben gedaan

Dit is wat we in de test fase hebben gedaan:

1. Vm's gemaakt met VMM-sjablonen.
2. Gestarte Vm's en vastgelegde gegevens over de basislijn prestaties over 12 uur.
3. Er zijn Clouds gemaakt op de primaire en herstel VMM-servers.
4. Geconfigureerde replicatie in Site Recovery, inclusief toewijzing tussen bron-en herstel Clouds.
5. Ingeschakelde beveiliging voor Vm's en toestemming geven om de initiële replicatie te volt ooien.
6. Er is een aantal uur gewacht op systeem stabilisatie.
7. Vastgelegde prestatie gegevens over 12 uur, waarbij alle Vm's in de verwachte replicatie status voor die 12 uur bleven.
8. De Delta tussen de basislijn prestaties van de basis lijn en de metrische gegevens over de replicatie prestaties gemeten.


## <a name="primary-server-performance"></a>Prestaties van primaire server

* Met Hyper-V replica (gebruikt door Site Recovery) worden wijzigingen in een logboek bestand asynchroon bijgehouden, met minimale opslag overhead op de primaire server.
* Hyper-V replica maakt gebruik van de door het systeem opgeslagen geheugen cache om de belasting van IOPS voor het bijhouden van nummers te minimaliseren. Er worden in het geheugen schrijf bewerkingen naar de VHDX opgeslagen en deze worden in het logboek bestand geplaatst voordat het logboek naar de herstel site wordt verzonden. Het leegmaken van de schijf gebeurt ook als de schrijf bewerkingen een vooraf vastgestelde limiet bereiken.
* In de onderstaande afbeelding ziet u de overhead van de stabiele status IOPS voor replicatie. We kunnen zien dat de belasting van IOPS als gevolg van replicatie ongeveer 5% is. Dit is zeer laag.

  ![Grafiek met de stabiele status IOPS-overhead voor replicatie.](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V replica gebruikt geheugen op de primaire server om de schijf prestaties te optimaliseren. Zoals in de volgende grafiek wordt weer gegeven, is de geheugen overhead op alle servers in het primaire cluster rand nummer. De weer gegeven geheugen overhead is het percentage geheugen dat wordt gebruikt door replicatie, vergeleken met het totale geïnstalleerde geheugen op de Hyper-V-Server.

![Primaire resultaten](./media/hyper-v-vmm-performance-results/IC744914.png)

De Hyper-V-replica heeft een minimale CPU-overhead. Zoals weer gegeven in de grafiek, ligt de overhead van de replicatie binnen het bereik van 2-3%.

![De grafiek die de overhead van de replicatie weergeeft, bevindt zich in het bereik van 2-3%.](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Secundaire server prestaties

Hyper-V replica maakt gebruik van een kleine hoeveelheid geheugen op de herstel server om het aantal opslag bewerkingen te optimaliseren. De grafiek bevat een samen vatting van het geheugen gebruik op de herstel server. De weer gegeven geheugen overhead is het percentage geheugen dat wordt gebruikt door replicatie, vergeleken met het totale geïnstalleerde geheugen op de Hyper-V-Server.

![Grafiek met een samen vatting van het geheugen gebruik op de herstel server.](./media/hyper-v-vmm-performance-results/IC744916.png)

De hoeveelheid I/O-bewerkingen op de herstel site is een functie van het aantal schrijf bewerkingen op de primaire site. Laten we eens kijken naar de totale I/O-bewerkingen op de herstel site in vergelijking met de totale I/O-bewerkingen en schrijf bewerkingen op de primaire site. In de grafieken ziet u dat het totale aantal IOPS op de herstel site

* Ongeveer 1,5 keer de schrijf-IOPS op de primaire.
* Rond 37% van het totale aantal IOPS op de primaire site.

![Grafiek met een vergelijking van IOPS op primaire en secundaire sites.](./media/hyper-v-vmm-performance-results/IC744917.png)

![Secundaire resultaten](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Effect op het netwerk gebruik

Een gemiddelde van 275 MB per seconde van de netwerk bandbreedte wordt gebruikt tussen de primaire en herstel knooppunten (waarbij compressie is ingeschakeld), tegen een bestaande band breedte van 5 GB per seconde.

![Resultaat netwerk gebruik](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Gevolgen voor de prestaties van de virtuele machine

Een belang rijke overweging is de impact van de replicatie van productie-workloads die worden uitgevoerd op de virtuele machines. Als de primaire site adequaat is ingericht voor replicatie, is er geen gevolgen voor de werk belastingen. Het lichte traceer mechanisme van de Hyper-V-replica zorgt ervoor dat werk belastingen die worden uitgevoerd op de virtuele machines, niet worden beïnvloed tijdens de replicatie van de stabiele status. Dit wordt geïllustreerd in de volgende grafieken.

In deze grafiek worden IOPS weer gegeven die worden uitgevoerd door virtuele machines die verschillende werk belastingen uitvoeren, vóór en nadat de replicatie is ingeschakeld. U kunt zien dat er geen verschil is tussen de twee.

![Resultaten van replica-effect](./media/hyper-v-vmm-performance-results/IC744920.png)

In de volgende grafiek ziet u de door Voer van virtuele machines met verschillende werk belastingen, vóór en na de replicatie. U kunt zien dat replicatie geen aanzienlijke gevolgen heeft.

![Resultaten replica-effecten](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Conclusie

De resultaten laten duidelijk zien dat Site Recovery, gekoppeld aan Hyper-V replica, goed schaalbaar is met minimale overhead voor een groot cluster. Site Recovery biedt eenvoudige implementatie, replicatie, beheer en bewaking. Hyper-V replica biedt de benodigde infra structuur voor geslaagde replicatie schaling. 

## <a name="test-environment-details"></a>Details van test omgeving

### <a name="primary-site"></a>Primaire site

* De primaire site heeft een cluster met vijf Hyper-V-servers, waarop 470 virtuele machines worden uitgevoerd.
* De Vm's voeren verschillende werk belastingen uit en alle hebben Site Recovery beveiliging ingeschakeld.
* Opslag voor het cluster knooppunt wordt verschaft door een iSCSI-SAN. Model – Hitachi HUS130.
* Elke cluster server heeft vier netwerk kaarten (Nic's) van één Gbps.
* Twee van de netwerk kaarten zijn verbonden met een particulier iSCSI-netwerk en twee zijn verbonden met een extern bedrijfs netwerk. Een van de externe netwerken is gereserveerd voor cluster communicatie.

![Primaire hardwarevereisten](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Model | Processor | Aantal processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V-servers in het cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 heeft 256 |Dell™ PowerEdge™ R820 |Intel (R) Xeon (R) CPU E5-4620 0 \@ 2.20 GHz |4 |I Gbps x 4 |Windows Server Data Center 2012 R2 (x64) + Hyper-V-rol |
| VMM-server |2 | | |2 |1 Gbps |Windows Server-Data Base 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Secundaire site

* De secundaire site heeft een failovercluster met zes knoop punten.
* Opslag voor het cluster knooppunt wordt verschaft door een iSCSI-SAN. Model – Hitachi HUS130.

![Primaire hardware-specificatie](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Model | Processor | Aantal processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V-servers in het cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell™ PowerEdge™ R720 |Intel (R) Xeon (R) CPU E5-2630 0 \@ 2.30 GHz |2 |I Gbps x 4 |Windows Server Data Center 2012 R2 (x64) + Hyper-V-rol |
| ESTLAB-HOST17 |128 |Dell™ PowerEdge™ R820 |Intel (R) Xeon (R) CPU E5-4620 0 \@ 2.20 GHz |4 | |Windows Server Data Center 2012 R2 (x64) + Hyper-V-rol |
| ESTLAB-HOST24 |256 |Dell™ PowerEdge™ R820 |Intel (R) Xeon (R) CPU E5-4620 0 \@ 2.20 GHz |2 | |Windows Server Data Center 2012 R2 (x64) + Hyper-V-rol |
| VMM-server |2 | | |2 |1 Gbps |Windows Server-Data Base 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Server werkbelastingen

* Voor test doeleinden zijn de gepickte workloads meestal in bedrijfs scenario's voor ondernemingen.
* We gebruiken [IOMeter](http://www.iometer.org) met het werk belasting kenmerk dat in de tabel voor simulatie is samenvatten.
* Alle IOMeter-profielen zijn ingesteld om wille keurige bytes te schrijven voor het simuleren van niet-hoofdletter schrijf patronen voor werk belastingen.

| Workload | I/O-grootte (KB) | % Toegang | % Lezen | Openstaande I/O's | I/O-patroon |
| --- | --- | --- | --- | --- | --- |
| Bestandsserver |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |Alle 100% wille keurig |
| SQL Server (volume 1)<br />SQL Server (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% wille keurig<br />100% sequentieel |
| Exchange |32 |100% |67% |8 |100% wille keurig |
| Werk station/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Beide 100% wille keurig |
| Web-Bestands server |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Alle 75% wille keurig |

### <a name="vm-configuration"></a>VM-configuratie

* 470 Vm's op het primaire cluster.
* Alle Vm's met VHDX-schijf.
* Virtuele machines waarop werk belastingen worden uitgevoerd, worden in de tabel samenvatten. Alle zijn met VMM-sjablonen gemaakt.

| Workload | Aantal Vm's | Mini maal RAM-geheugen (GB) | Maximum-RAM (GB) | Grootte van logische schijf (GB) per VM | Maximum aantal IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Bestandsserver |50 |1 |2 |552 |22 |
| VDI |149 |5 |1 |80 |6 |
| Webserver |149 |5 |1 |80 |6 |
| Totaal |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Site Recovery instellingen

* Site Recovery is geconfigureerd voor on-premises naar on-premises beveiliging
* Op de VMM-server zijn vier Clouds geconfigureerd, met de Hyper-V-cluster servers en de bijbehorende Vm's.

| Primaire VMM-Cloud | Beveiligde virtuele machines | Replicatie frequentie | Aanvullende herstel punten |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 minuten |Geen |
| PrimaryCloudRpo30s |47 |30 seconden |Geen |
| PrimaryCloudRpo30sArp1 |47 |30 seconden |1 |
| PrimaryCloudRpo5m |235 |5 minuten |Geen |

### <a name="performance-metrics"></a>Metrische gegevens voor prestaties

De tabel bevat een overzicht van de prestatie gegevens en prestatie meter items die in de implementatie zijn gemeten.

| Metrisch | Prestatiemeteritem |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Beschikbaar geheugen |\Memory\Available mega bytes |
| IOPS |\PhysicalDisk (_Total) \Gelezen-overdrachten per seconde |
| Gelezen VM-bewerkingen (IOPS) per seconde |\Hyper-V ( \<VHD> ) \Read-bewerkingen per seconde |
| Bewerkingen voor het schrijven van VM'S (IOPS) per seconde |\Hyper-V ( \<VHD> ) \Write-bewerkingen/S |
| Lees doorvoer van VM |\Hyper-V ( \<VHD> ) \Read bytes per seconde |
| Schrijf doorvoer VM |\Hyper-V ( \<VHD> ) \Write bytes per seconde |

## <a name="next-steps"></a>Volgende stappen

[Replicatie instellen](hyper-v-vmm-disaster-recovery.md)
