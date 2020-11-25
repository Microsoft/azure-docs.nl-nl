---
title: Het Hyper-V Deployment Planner-rapport in Azure Site Recovery analyseren
description: In dit artikel wordt beschreven hoe u een rapport analyseert dat is gegenereerd door de Azure Site Recovery Deployment Planner voor nood herstel van virtuele Hyper-V-machines naar Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/21/2019
ms.author: mayg
ms.openlocfilehash: f230445ecdb046c2b631e89567df71e1d09c3234
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999226"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report"></a>Het rapport van de Azure Site Recovery Deployment Planner analyseren
In dit artikel worden de werkbladen in het Excel-rapport behandeld dat door de Azure Site Recovery-implementatieplanner voor Hyper-V naar Azure is gegenereerd.

## <a name="on-premises-summary"></a>Samenvatting on-premises
Het werkblad Samenvatting on-premises biedt een overzicht van de geprofileerde Hyper-V-omgeving.

![Samenvatting on-premises](media/hyper-v-deployment-planner-analyze-report/on-premises-summary-h2a.png)

**Begin datum** en **eind datum**: de begin-en eind datums van de profilerings gegevens die worden overwogen voor het genereren van rapporten. De begindatum is standaard de datum waarop de profilering start en de einddatum de datum waarop de profilering stopt. Deze informatie kunnen de waarden voor 'StartDate' en 'EndDate' zijn als het rapport met deze parameters wordt gegenereerd.

**Totale aantal dagen van profilering**: het totale aantal dagen van profilering tussen de begin- en einddatum waarvoor het rapport wordt gegenereerd.

**Aantal compatibele virtuele machine**: het totale aantal compatibele virtuele machines waarvoor de vereiste netwerkbandbreedte, het vereiste aantal opslagaccounts en Azure-kerngeheugens wordt berekend.

**Totale aantal schijven voor alle compatibele virtuele machines**: het totale aantal schijven voor alle compatibele virtuele machines.

**Gemiddeld aantal schijven per compatibele virtuele machine**: het gemiddelde aantal schijven dat voor alle compatibele virtuele machines is berekend.

**Gemiddelde schijfgrootte (GB)**: de gemiddelde schijfgrootte die voor alle compatibele virtuele machines is berekend.

**Gewenste RPO (minuten)**: de standaard Recovery Point Objective of de waarde die is door gegeven voor de para meter ' DesiredRPO ' op het moment dat het rapport wordt gegenereerd om de vereiste band breedte te schatten.

**Gewenste bandbreedte (Mbps)**: de waarde die u tijdens het genereren van het rapport voor de parameter 'Bandwidth' hebt doorgegeven om het haalbare beoogde herstelpunt (RPO) in te schatten.

**Waargenomen typisch gegevensverloop per dag (GB)**: het gemiddelde gegevensverloop dat voor alle profileringsdagen is vastgesteld.

## <a name="recommendations"></a>Aanbevelingen 
Het werkblad met de aanbevelingen voor het Hyper-V naar Azure-rapport heeft de volgende gegevens aan de hand van de geselecteerde gewenste RPO:

![Rapport Aanbevelingen voor Hyper-V naar Azure](media/hyper-v-deployment-planner-analyze-report/Recommendations-h2a.png)

### <a name="profile-data"></a>Profielgegevens
![Profielgegevens](media/hyper-v-deployment-planner-analyze-report/profile-data-h2a.png)

**Periode geprofileerde gegevens**: de periode waarin de profilering is uitgevoerd. Het hulpprogramma neemt standaard alle geprofileerde gegevens in de berekening op. Als u voor het genereren van het rapport de optie StartDate en EndDate hebt gebruikt, wordt het rapport gegenereerd voor die specifieke periode. 

**Aantal profileeerde hyper-v-servers**: het aantal hyper-v-servers waarvan de vm's worden gegenereerd. Selecteer het aantal om de naam van de Hyper-V-servers weer te geven. Het werkblad On-premises opslagvereiste wordt geopend, waar alle servers samen met hun opslagvereisten worden weergegeven. 

**Gewenste RPO**: het gewenste beoogde herstelpunt (RPO) voor uw implementatie. Standaard wordt de vereiste netwerkbandbreedte berekend voor de RPO-waarden van 15, 30 en 60 minuten. Op basis van de selectie worden de betrokken waarden bijgewerkt op het blad. Als u tijdens het genereren van het rapport de parameter DesiredRPOinMin hebt gebruikt, wordt de betreffende waarde weergegeven bij het gewenste RPO-resultaat.

### <a name="profiling-overview"></a>Overzicht van profilering
![Overzicht van profilering](media/hyper-v-deployment-planner-analyze-report/profiling-overview-h2a.png)

**Totale aantal geprofileerde virtuele machines**: het totale aantal virtuele machines waarvan de geprofileerde gegevens beschikbaar zijn. Als het bestand dat is opgegeven bij VMListFile namen van virtuele machines bevat die niet zijn geprofileerd, worden deze virtuele machines niet meegenomen in het rapport en uitgesloten van het totale aantal geprofileerde virtuele machines.

**Compatibele virtuele machines**: het aantal virtuele machines dat op Azure kan worden beveiligd met Azure Site Recovery. Dit is het totale aantal compatibele virtuele machines waarvoor de vereiste netwerkbandbreedte, het aantal opslagaccounts en het aantal Azure-kerngeheugens wordt berekend. De details van elke compatibele virtuele machine zijn beschikbaar in de sectie 'Compatibele VM's'.

**Niet-compatibele virtuele machines**: het aantal geprofileerde virtuele machines dat niet compatibel is met beveiliging met Site Recovery. De redenen voor incompatibiliteit worden vermeld in de sectie 'Niet-compatibele VM's'. Als het bestand dat is opgegeven bij VMListFile namen bevat van virtuele machines die niet zijn geprofileerd, worden die virtuele machines uitgesloten van het aantal niet-compatibele virtuele machines. Deze virtuele machines worden weergegeven als 'Gegevens niet gevonden' aan het einde van de sectie 'Niet-compatibele VM's'.

**Gewenste RPO**: het gewenste beoogde herstelpunt (Recovery Point Objective [RPO]) in minuten. Het rapport wordt gegenereerd voor drie RPO-waarden: 15 (standaard), 30 en 60 minuten. De aanbeveling voor bandbreedte in het rapport wordt gewijzigd op basis van uw selectie in de vervolgkeuzelijst **Gewenste RPO** in de rechterbovenhoek van het blad. Als u het rapport hebt gegenereerd met een aangepaste waarde voor de parameter -DesiredRPO, wordt deze aangepaste waarde als standaardwaarde weergegeven in de vervolgkeuzelijst **Gewenst RPO**.

### <a name="required-network-bandwidth-mbps"></a>Vereiste netwerkbandbreedte (Mbps)
![Vereiste netwerkbandbreedte](media/hyper-v-deployment-planner-analyze-report/required-network-bandwidth-h2a.png)

**Altijd aan de RPO voldoen:** de aanbevolen bandbreedte in Mbps die moet worden toegewezen om 100 procent van de tijd uw gewenste RPO te halen. Deze bandbreedte moet worden toegewezen voor onveranderlijke replicatie van verschillen bij al uw compatibele virtuele machines om eventuele RPO-schendingen te voorkomen.

**90% van de tijd aan de RPO voldoen**: wellicht kunt u vanwege bandbreedteprijzen of andere redenen de bandbreedte niet instellen om altijd de RPO te halen. Als dit het geval is, kunt u een lagere bandbreedte-instelling gebruiken die 90% van de tijd aan de RPO kan voldoen. Voor inzicht in de gevolgen van het instellen van deze lagere bandbreedte bevat het rapport een wat-als-analyse van het aantal en de duur van de RPO-schendingen die u kunt verwachten.

**Behaalde doorvoer:** de doorvoer van de server waar u de GetThroughput-opdracht uitvoert naar de Azure-regio waar het opslagaccount zich bevindt. Dit doorvoernummer geeft het geschatte niveau aan dat u kunt bereiken wanneer u de compatibele VM's beschermt met behulp van Site Recovery. De Hyper-V-serveropslag en de netwerkeigenschappen moeten dezelfde blijven als die van de server vanaf waar u het hulpprogramma uitvoert.

Voor alle Enterprise-implementaties van Site Recovery wordt het gebruik van [ExpressRoute](https://aka.ms/expressroute) aanbevolen.

### <a name="required-storage-accounts"></a>Vereiste opslagaccounts
De volgende grafiek geeft het totale aantal opslagaccounts aan (Standard Storage en Premium Storage) dat is vereist voor het beveiligen van alle compatibele virtuele machines. Zie de sectie 'VM-opslagplaatsing' voor meer informatie over welk opslagaccount u moet gebruiken voor elke virtuele machine.

![Vereiste Azure-opslagaccounts](media/hyper-v-deployment-planner-analyze-report/required-storage-accounts-h2a.png)

### <a name="required-number-of-azure-cores"></a>Vereiste aantal Azure-kerngeheugens
Dit is het totale aantal kerngeheugens dat moet worden ingesteld vóór failover of testfailover van de compatibele virtuele machines. Als er in het abonnement onvoldoende kerngeheugens beschikbaar zijn, kan Site Recovery geen virtuele machines maken op het moment van een failover of testfailover.

![Vereiste aantal Azure-kerngeheugens](media/hyper-v-deployment-planner-analyze-report/required-number-of-azure-cores-h2a.png)


### <a name="additional-on-premises-storage-requirement"></a>Extra on-premises opslagvereiste

De totale vrije opslagruimte op Hyper-V-servers die is vereist om de initiële replicatie en replicatie van verschillen uit te kunnen voeren, zonder dat de replicatie van virtuele machines tot ongewenste downtime voor uw productietoepassingen leidt. In [On-premises opslagvereiste](#on-premises-storage-requirement) vindt u meer informatie over elk van de volumevereisten. 

Bekijk [On-premises opslagvereiste](#why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication) als u wilt weten waarom vrije ruimte is vereist voor de replicatie.

![On-premises opslagvereiste en kopieerfrequentie](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-and-copy-frequency-h2a.png)

### <a name="maximum-copy-frequency"></a>Maximale kopieerfrequentie
De aanbevolen maximale kopieerfrequentie moet voor de replicatie worden ingesteld om de gewenste RPO te bereiken. De standaard is vijf minuten. U kunt de kopieerfrequentie instellen op 30 seconden voor een betere RPO.

### <a name="what-if-analysis"></a>Wat-als-analyses
![Wat als-analyse](media/hyper-v-deployment-planner-analyze-report/what-if-analysis-h2a.png) Deze analyse beschrijft hoeveel schendingen zich kunnen voordoen tijdens de profileringsperiode wanneer u een lagere bandbreedte instelt om 90 procent van de tijd aan de RPO te voldoen. Op elke dag kunnen er een of meer RPO-schendingen optreden. De grafiek toont de piek-RPO van de dag. Op basis van deze analyse kunt u besluiten of het aantal RPO-schendingen voor alle dagen en de hoogste RPO per dag acceptabel zijn in combinatie met de opgegeven lagere bandbreedte. U kunt, indien gewenst, een lagere bandbreedte toewijzen voor de replicatie. Als dit niet gewenst is, wijst u zoals voorgesteld een grotere bandbreedte toe om altijd de RPO te halen. 

### <a name="recommendation-for-successful-initial-replication"></a>Aanbeveling voor een geslaagde initiële replicatie
In deze sectie wordt het aantal batches besproken waarin de virtuele machines moeten worden beveiligd, evenals de minimale bandbreedte die is vereist voor een geslaagde initiële replicatie (IR). 

![IR-batchverwerking](media/hyper-v-deployment-planner-analyze-report/ir-batching-h2a.png)

VM's moeten worden beveiligd in de opgegeven batchvolgorde. Elke batch heeft een specifieke lijst met virtuele machines. Virtuele machines in batch 1 moeten worden beveiligd vóór die in batch 2. Virtuele machines in batch 2 moeten worden beveiligd vóór die in batch 3, enzovoort. Nadat de initiële replicatie van de virtuele machines in batch 1 is voltooid, kunt u replicatie voor de virtuele machines in batch 2 inschakelen. Op dezelfde manier kunt u, nadat de initiële replicatie van de VM's in batch 2 is voltooid, de replicatie voor de VM's in batch 3 inschakelen, enzovoorts. 

Als de volgorde van de batch niet wordt gevolgd, is er mogelijk onvoldoende bandbreedte voor de initiële replicatie beschikbaar voor de VM's die later worden beveiligd. Het resultaat is dat VM's de initiële replicatie nooit voltooien, of dat er een aantal beveiligde VM's in de hersynchronisatiemodus gaat. IR batchverwerking voor het geselecteerde RPO-werkblad bevat gedetailleerde informatie over welke virtuele machines moeten worden opgenomen in elke batch.

Dit diagram toont de bandbreedtedistributie voor initiële replicatie en replicatie van verschillen voor alle batches in de opgegeven batchvolgorde. Wanneer u de eerste batch VM's beveiligt, is volledige bandbreedte beschikbaar voor de initiële replicatie. Nadat de initiële replicatie voor de eerste batch is voltooid, is een deel van de bandbreedte voor de replicatie van verschillen vereist. De resterende bandbreedte is beschikbaar voor de initiële replicatie van de tweede batch VM's. 

De balk voor batch 2 toont de bandbreedte die is vereist voor de replicatie van verschillen voor de virtuele machines in batch 1, evenals de bandbreedte die beschikbaar is voor de initiële replicatie voor de virtuele machines in batch 2. Hetzelfde geldt voor de balk voor batch 3 met de bandbreedte die is vereist voor de replicatie van verschillen voor eerdere batches (virtuele machines in batch 1 en 2) en de bandbreedte die beschikbaar is voor de initiële replicatie voor batch 3, enzovoort. Nadat de initiële replicatie van alle batches is voltooid, toont de laatste balk de bandbreedte die vereist is voor de replicatie van verschillen voor alle beveiligde virtuele machines.

**Waarom moet ik eerste replicaties via batchverwerking uitvoeren?**
De tijd die een initiële replicatie kost, is gebaseerd op de schijfgrootte van de virtuele machine, de gebruikte schijfruimte en de beschikbare netwerkdoorvoer. De details hiervan zijn te vinden op het werkblad IR-batchverwerking van een geselecteerde RPO.

### <a name="cost-estimation"></a>Kostenraming
De grafiek toont de weergave Samenvatting van de geschatte totale kosten voor noodherstel (DR) voor Azure van de gekozen doelregio en de valuta die u hebt opgegeven voor het genereren van rapporten.

![Samenvatting kostenramingen](media/hyper-v-deployment-planner-analyze-report/cost-estimation-summary-h2a.png)

De samenvatting helpt u bij het begrijpen van de kosten die u nodig hebt om te betalen voor opslag, rekensnelheid, netwerk en licentie wanneer u alle compatibele virtuele machines naar Azure met behulp van Site Recovery beveiligt. De kosten worden berekend op basis van compatibele virtuele machines en niet op basis van alle geprofileerde virtuele machines. 
 
U kunt de kosten maandelijks of jaarlijks weergeven. Meer informatie over [ondersteunde doelregio's](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) en [ondersteunde valuta's](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Kosten per onderdelen**: de totale kosten voor noodherstel zijn onderverdeeld in vier onderdelen: compute, opslag, netwerk en Site Recovery-licentiekosten. De kosten worden berekend op basis van het verbruik tijdens de replicatie en de tijd van de noodherstelanalyse. Rekenkosten, opslag (Premium en Standard), de ExpressRoute/VPN die is geconfigureerd tussen de on-premises site en Azure, en de Site Recovery-licentie worden gebruikt voor de berekeningen.

**Kosten per status**: de kosten voor een totaal noodherstel worden gecategoriseerd op basis van twee verschillende statussen: replicatie en noodherstelanalyse. 

**Replicatiekosten**: de kosten die worden gemaakt tijdens de replicatie. Dit zijn de kosten voor de opslag, het netwerk en de Site Recovery-licentie. 

**Kosten voor noodherstelanalyse**: de kosten die worden gemaakt tijdens testfailovers. Site Recovery laat virtuele machines draaien tijdens de testfailover. De details voor DR-kosten zijn de kosten voor de berekenings- en opslagkosten van de actieve VM's. 

**Kosten voor Azure Storage per maand/jaar**: het staafdiagram toont de totale opslagkosten die worden gemaakt voor Premium- en Standard-opslag voor replicatie en noodherstelanalyse. U vindt een gedetailleerde kostenanalyse per virtuele machine op het werkblad [Kostenraming](hyper-v-deployment-planner-cost-estimation.md).

### <a name="growth-factor-and-percentile-values-used"></a>Groeifactor en gebruikte percentielwaarden
Deze sectie onder aan het werkblad toont de percentielwaarde die voor alle prestatiemeteritems van de geprofileerde virtuele machines wordt gebruikt (standaard is dit het 95e percentiel). Het toont ook de groeifactor in procenten die in alle berekeningen wordt gebruikt (standaard 30 procent).

![Groeifactor en gebruikte percentielwaarden](media/hyper-v-deployment-planner-run/growth-factor-max-percentile-value.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Aanbevelingen met beschikbare bandbreedte als invoer
![Overzicht van profilering met invoer voor de bandbreedte](media/hyper-v-deployment-planner-analyze-report/profiling-overview-bandwidth-input-h2a.png)

Het is mogelijk dat u om wat voor reden dan ook niet meer dan x Mbps bandbreedte kunt instellen voor Site Recovery-replicatie. U kunt het hulpprogramma gebruiken om de beschikbare bandbreedte op te geven (dit doet u tijdens het genereren van het rapport met behulp van de parameter -Bandwidth) en zo de haalbare RPO in minuten achterhalen. Aan de hand van deze haalbare RPO-waarde kunt u beslissen of u extra bandbreedte wilt inrichten of dat u tevreden bent met een noodhersteloplossing voor deze RPO.

![Haalbare RPO](media/hyper-v-deployment-planner-analyze-report/achivable-rpo-h2a.PNG)

## <a name="vm-storage-placement-recommendation"></a>Aanbeveling voor VM-opslagplaatsing 
![VM-opslagplaatsing](media/hyper-v-deployment-planner-analyze-report/vm-storage-placement-h2a.png)

**Type schijfopslag**: het type opslagaccount (Standard Storage of Premium Storage) dat wordt gebruikt voor het repliceren van alle bijbehorende virtuele machines in de kolom **Te plaatsen VM's**.

**Voorgestelde voorvoegsel**: het voorgestelde voorvoegsel, bestaande uit drie tekens, dat kan worden gebruikt voor de naam van het opslagaccount. U kunt uw eigen voorvoegsel gebruiken, maar de suggestie van het hulpprogramma volgt de [naamgevingsregels voor partities voor opslagaccounts](/en-in/azure/storage/blobs/storage-performance-checklist).

**Voorgestelde accountnaam**: de naam van het opslagaccount na toevoeging van het voorgestelde voorvoegsel. Vervang de naam tussen de punthaken (< en >) door uw aangepaste invoer.

**Logboekopslagaccount**: alle replicatielogboeken worden opgeslagen in een opslagaccount van het type Standard. In het geval van virtuele machines die repliceren naar een Premium Storage-account moet u een extra opslagaccount van het type Standard instellen voor het opslaan van logboeken. Eén Standard-account voor het opslaan van logboeken kan worden gebruikt door meerdere Premium-opslagaccounts voor replicatie. Virtuele machines die worden gerepliceerd naar een Standard-opslagaccount gebruiken hetzelfde account voor het opslaan van logboeken.

**Voorgestelde naam logboekaccount**: de naam van het account voor het opslaan van logboeken na toevoeging van het voorgestelde voorvoegsel. Vervang de naam tussen de punthaken (< en >) door uw aangepaste invoer.

**Plaatsing samenvatting**: een samenvatting van de belasting van het totale aantal virtuele machines in het opslagaccount tijdens replicatie en testfailover of failover. De samenvatting omvat:

* Het totaalaantal VM's dat is toegewezen aan het opslagaccount. 
* Het totaalaantal IOPS voor lezen/schrijven over alle VM's die in dit opslagaccount worden geplaatst.
* Het totaalaantal schrijf-IOPS (replicatie).
* De totale installatiegrootte over alle schijven.
* Het totaalaantal schijven.

**Te plaatsen VM's**: een lijst van alle virtuele machines die in het opgegeven opslagaccount moeten worden geplaatst voor optimale prestaties en optimaal gebruik.

## <a name="compatible-vms"></a>Compatibele VM's
Het Excel-rapport dat is gegenereerd door de Site Recovery-implementatieplanner bevat alle gegevens over de compatibele VM's op het werkblad 'Compatibele VM's'.

![Compatibele VM's](media/hyper-v-deployment-planner-analyze-report/compatible-vms-h2a.png)

**VM-naam**: de naam van de virtuele machine, die wordt gebruikt in het bestand dat is opgegeven voor VMListFile wanneer een rapport wordt gegenereerd. Deze kolom bevat ook de schijven (VHD's) die aan de virtuele machines zijn gekoppeld. De namen zijn onder andere de namen van de Hyper-V-hosts waar de virtuele machines zijn geplaatst toen ze door het hulpprogramma werden gedetecteerd tijdens de profileringsperiode.

**VM-compatibiliteit**: de mogelijke waarden zijn **Ja** en **Ja**\*. **Ja** \* is voor instanties waarin de virtuele machine geschikt is voor [Premium-ssd's](../virtual-machines/disks-types.md). Hier past het geprofileerde hoge verloop of IOPS bij een grotere Premium-schijfgrootte dan bij de grootte die is toegewezen aan de schijf. Het opslagaccount bepaalt aan welk schijftype voor Premium Storage een schijf wordt toegewezen, op basis van de grootte: 
* <128 GB is een P10.
* 128 GB tot 256 GB is een P15.
* 256 GB tot 512 GB is een P20.
* 512 GB tot 1024 GB is een P30.
* 1025 GB tot 2048 GB is een P40.
* 2049 GB tot 4095 GB is een P50.

Als bijvoorbeeld de eigenschappen van de werk belasting van een schijf in de categorie P20 of P30 worden geplaatst, maar de grootte ervan wordt toegewezen aan een lager type Premium-opslag schijf, markeert het hulp programma die virtuele machine als **Ja** \* . Het hulpprogramma adviseert ook om ofwel de grootte van de bronschijf te wijzigen zodat deze overeenkomt met het schijftype voor Premium Storage of de post-failover van het doelschijftype te wijzigen.

**Opslagtype**: Standard of Premium.

**Voorgestelde voorvoegsel**: het voorvoegsel van drie tekens van het opslagaccount.

**Opslagaccount**: de naam die gebruikmaakt van het voorgestelde voorvoegsel voor het opslagaccount.

**Piek IOPS voor lezen/schrijven (met groeifactor)**: de piekworkload-IOPS voor lezen/schrijven op de schijf (standaard het 95e percentiel) samen met de toekomstige groeifactor (standaard 30 procent). De totale IOPS voor lezen/schrijven van een VM is niet altijd de som van de IOPS voor lezen/schrijven van afzonderlijke schijven van de VM. De piek IOPS voor lezen/schrijven van de VM is de piek van de som van de IOPS voor lezen/schrijven van afzonderlijke schijven van de VM gedurende elke minuut van de profileringsperiode.

**Piek voor gegevensverloop in Mb/s (met groeifactor)**: het piekverloop op de schijf (standaard het 95e percentiel), samen met de toekomstige groeifactor (standaard 30 procent). De totale gegevensverloop van de VM is niet altijd de som van de gegevensverloop van afzonderlijke schijven van de VM. De piekgegevensverloop van de VM is de piek van de som van de verloop van afzonderlijke schijven van de VM gedurende elke minuut van de profileringsperiode.

**Azure VM-grootte**: de ideale toegewezen grootte voor virtuele machines van Azure Cloud Services voor deze on-premises VM. De toewijzing is gebaseerd op het geheugen, het aantal schijven/kerngeheugens/NIC's en de IOPS voor lezen/schrijven van de on-premises VM. De aanbeveling is altijd de laagste Azure VM-grootte die overeenkomt met alle kenmerken van de on-premises virtuele machine.

**Aantal schijven**: het totale aantal schijven (VHD's) op de virtuele machine.

**Schijf grootte (GB)**: de totale grootte van alle schijven van de virtuele machine. Het hulpprogramma toont ook de schijfgrootte voor de afzonderlijke schijven in de virtuele machine.

**Kerngeheugens**: het aantal CPU-kerngeheugens van de virtuele machine.

**Geheugen (MB)**: het RAM-geheugen van de virtuele machine.

**NIC's**: het aantal NIC's van de virtuele machine.

**Opstarttype**: het opstarttype van de virtuele machine. Dit kan BIOS of EFI zijn.

## <a name="incompatible-vms"></a>Niet-compatibele VM's
Het Excel-rapport dat is gegenereerd door de Site Recovery-implementatieplanner bevat alle gegevens over de niet-compatibele VM's op het werkblad 'Niet-compatibele VM's'.

![Niet-compatibele VM's](media/hyper-v-deployment-planner-analyze-report/incompatible-vms-h2a.png)

**VM-naam**: de naam van de virtuele machine, die wordt gebruikt in het bestand dat is opgegeven voor VMListFile wanneer een rapport wordt gegenereerd. Deze kolom bevat ook de schijven (VHD's) die aan de virtuele machines zijn gekoppeld. De namen zijn onder andere de namen van de Hyper-V-hosts waar de virtuele machines zijn geplaatst toen ze door het hulpprogramma werden gedetecteerd tijdens de profileringsperiode.

**VM-compatibiliteit**: geeft aan waarom de virtuele machine niet compatibel is voor gebruik met Site Recovery. De redenen worden voor elke niet-compatibele schijf van de virtuele machine beschreven. Op basis van gepubliceerde [opslaglimieten](/en-in/azure/storage/common/scalability-targets-standard-account) kan dit een van de volgende redenen zijn:

* De grootte van de schijf is groter dan 4095 GB. Azure Storage biedt momenteel geen ondersteuning voor gegevensschijven groter dan 4095 GB.

* De besturingssysteemschijf is groter dan 2047 GB voor generatie 1-virtuele machines (BIOS-opstarttype). Site Recovery biedt geen ondersteuning voor besturingssysteemschijven groter dan 2047 GB voor virtuele machines van generatie 1.

* De besturingssysteemschijf is groter dan 300 GB voor generatie 2-virtuele machines (EFI-opstarttype). Site Recovery biedt geen ondersteuning voor besturingssysteemschijven groter dan 300 GB voor virtuele machines van generatie 2.

* Een VM-naam met één of meer van de volgende tekens wordt niet ondersteund: “” [] `. Het hulpprogramma kan de geprofileerde gegevens van VM's die deze tekens in hun namen hebben, niet ophalen. 

* Een VHD wordt gedeeld door twee of meer virtuele machines. Azure biedt geen ondersteuning voor VM's met een gedeelde VHD.

* Een VM met Virtual Fiber Channel wordt niet ondersteund. Site Recovery biedt geen ondersteuning voor VM's met Virtual Fiber Channel.

* Een Hyper-V-cluster bevat geen replicatiebroker. Site Recovery biedt geen ondersteuning voor een VM in een Hyper-V-cluster als de Hyper-V Replicabroker niet voor het cluster is geconfigureerd.

* Een VM heeft geen hoge beschikbaarheid. Site Recovery biedt geen ondersteuning voor een VM van een Hyper-V-clusterknooppunt waarvan VHD's op de lokale schijf in plaats van op de clusterschijf worden opgeslagen. 

* Totale grootte van een VM (replicatie + testfailover) overschrijdt de ondersteunde limiet voor opslagaccounts (35 TB). Dit probleem treedt meestal op wanneer één schijf in de virtuele machine een prestatiekenmerk heeft dat groter is dan de maximaal ondersteunde limieten voor Standard-opslag van Azure of Site Recovery. De virtuele machine komt dan in aanmerking voor Premium Storage. De maximale ondersteunde grootte van een Premium-opslagaccount is echter 35 TB. Een enkele beschermde VM kan niet worden beschermd in meerdere opslagaccounts. 

    Als een testfailover op een beveiligde virtuele machine wordt uitgevoerd en een niet-beheerde schijf voor testfailover is geconfigureerd, wordt deze uitgevoerd in het opslagaccount waarin ook de replicatie plaatsvindt. In dit geval is dezelfde extra hoeveelheid opslagruimte vereist als die voor replicatie. Hiermee zorgt u ervoor dat de voortgang van een replicatie en een testfailover parallel kunnen plaatsvinden. Wanneer een beheerde schijf is geconfigureerd voor de testfailover, hoeft er geen extra ruimte te worden gereserveerd voor de testfailover-VM.

* De bron-IOPS is groter dan de ondersteunde IOPS-limiet voor opslag van 7500 per schijf.

* De bron-IOPS is groter dan de ondersteunde IOPS-limiet voor opslag van 80.000 per VM.

* Het gemiddelde gegevens verloop van de bron-VM overschrijdt de ondersteunde Site Recovery gegevens verloop limiet van 20 MB/s voor de gemiddelde I/O-grootte.

* De gemiddelde effectieve schrijf-IOPS van de bron-VM overschrijdt de ondersteunde IOPS-limiet van Site Recovery van 840 voor schijven.

* De berekende opslag voor momentopnamen overschrijdt de ondersteunde opslaglimiet van 10 TB voor momentopnamen.

**Piek IOPS voor lezen/schrijven (met groeifactor)**: de piekworkload-IOPS op de schijf (standaard het 95e percentiel), samen met de toekomstige groeifactor (standaard 30%). De piek IOPS voor lezen/schrijven van de VM is niet altijd de som van de IOPS voor lezen/schrijven van afzonderlijke schijven van de VM. De IOPS voor lezen/schrijven van de VM is de piek van de som van de IOPS voor lezen/schrijven van afzonderlijke schijven van de VM gedurende elke minuut van de profileringsperiode.

**Piek voor gegevensverloop (Mb/s) (met groeifactor)**: het piekverloop op de schijf (standaard het 95e percentiel), samen met de toekomstige groeifactor (standaard 30 procent). Onthoud dat de totale gegevensverloop van de VM niet altijd de som is van de gegevensverlopen van afzonderlijke schijven van de VM. De piekgegevensverloop van de VM is de piek van de som van de verloop van afzonderlijke schijven van de VM gedurende elke minuut van de profileringsperiode.

**Aantal schijven**: het totale aantal VHD's van de virtuele machine.

**Schijf grootte (GB)**: de totale installatie grootte van alle schijven van de virtuele machine. Het hulpprogramma toont ook de schijfgrootte voor de afzonderlijke schijven in de virtuele machine.

**Kerngeheugens**: het aantal CPU-kerngeheugens van de virtuele machine.

**Geheugen (MB)**: de hoeveelheid RAM-geheugen van de virtuele machine.

**NIC's**: het aantal NIC's van de virtuele machine.

**Opstarttype**: het opstarttype van de virtuele machine. Dit kan BIOS of EFI zijn.

## <a name="azure-site-recovery-limits"></a>Azure Site Recovery-limieten
De volgende tabel bevat de Site Recovery-limieten. Deze limieten zijn gebaseerd op tests, maar dekken niet alle mogelijke toepassings-I/O-combinaties. De werkelijke resultaten kunnen variëren op basis van uw toepassings-I/O-combinatie. Voor optimale resultaten, zelfs na het plannen van de implementatie, moet u toepassingen uitgebreid testen met behulp van een testfailover. Zo krijgt u een nauwkeurig inzicht in de prestaties van de applicatie.
 
**Doel van replicatie opslag** | **Gemiddelde I/O-grootte van bron-VM** |**Gemiddeld gegevens verloop van bron-VM** | **Totale gegevensverloop van bron-VM per dag**
---|---|---|---
Standard Storage | 8 kB | 2 MB/s per VM | 168 GB per VM
Premium Storage | 8 kB  | 5 MB/s per VM | 421 GB per VM
Premium Storage | 16 kB of hoger| 20 MB/s per VM | 1684 GB per VM

Deze limieten zijn gemiddelden uitgaande van een I/O-overlapping van 30%. Site Recovery kan een hogere doorvoer verwerken op basis van overlappingsverhouding, grotere schrijfgrootten en daadwerkelijk workload-I/O-gedrag. De bovenstaande waarden zijn gebaseerd op een typische backlog van ongeveer vijf minuten. Dat wil zeggen dat de gegevens na het uploaden binnen vijf minuten worden verwerkt en er een herstelpunt is gemaakt.

## <a name="on-premises-storage-requirement"></a>On-premises opslagvereiste

Het werkblad toont de totale vrije ruimte opslagvereiste voor elk volume van de Hyper-V-servers (waar de VHD's zich bevinden) die nodig is voor een geslaagde initiële replicatie en voor replicatie van verschillen. Voordat u replicatie inschakelt, moet u de vereiste opslagruimte aan de volumes toevoegen om ervoor te zorgen dat de replicatie geen ongewenste downtime van uw productietoepassingen veroorzaakt. 

  De Site Recovery-implementatieplanner bepaalt de optimale vereiste opslagruimte op basis van de grootte van de VHD's en de netwerkbandbreedte die voor replicatie wordt gebruikt.

![On-premises opslagvereiste](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-requirement-h2a.png)

### <a name="why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication"></a>Waarom heb ik op de Hyper-V-server vrije ruimte nodig voor de replicatie?
* Wanneer u de replicatie van een virtuele machine inschakelt, maakt Site Recovery voor de initiële replicatie een momentopname van elke VHD van de virtuele machine. Tijdens de initiële replicatie worden nieuwe wijzigingen door de toepassing maar de schijven geschreven. Site Recovery houdt deze wijzigingen in de logboekbestanden bij en daarvoor is extra opslagruimte nodig. Totdat de initiële replicatie is voltooid, worden de logboekbestanden lokaal opgeslagen. 

    Als er onvoldoende ruimte beschikbaar is voor de logboekbestanden en de momentopname (AVHDX), gaat de replicatie over in de hersynchronisatiemodus en wordt de replicatie nooit voltooid. In het ergste geval hebt u voor de initiële replicatie 100 procent van de VHD-grootte aan extra vrije ruimte nodig.
* Nadat de initiële replicatie is voltooid, begint de replicatie van verschillen. Site Recovery houdt de wijzigingen als gevolg van de replicatie van verschillen bij in de logboekbestanden die zijn opgeslagen op het volume waar de VHD's van de virtuele machine zich bevinden. Deze logboekbestanden worden gerepliceerd naar Azure met de frequentie die voor kopiëren is ingesteld. Op basis van de beschikbare netwerkbandbreedte kan het even duren voordat de logboekbestanden naar Azure zijn gerepliceerd. 

    Als er onvoldoende vrije ruimte is om de logboekbestanden op te slaan, wordt de replicatie onderbroken. De replicatiestatus van de VM meldt dat er 'synchronisatie is vereist'.
* Als er onvoldoende netwerkbandbreedte is om de logboekbestanden naar Azure te pushen, stapelen de logboekbestanden zich op het volume op. In het ergste geval, waarbij de grootte van logboekbestanden tot 50 procent van de grootte van de VHD toeneemt, gaat de replicatie van de VM over in een status met het bericht 'hersynchronisatie vereist'. In het ergste geval hebt u voor de replicatie van verschillen 50 procent van de VHD-grootte aan extra vrije ruimte nodig.

**Hyper-V-host**: de lijst met geprofileerde Hyper-V-servers. Als een server deel uitmaakt van een Hyper-V-cluster, worden alle clusterknooppunten gegroepeerd.

**Volume (VHD-pad)**: elk volume van een Hyper-V-host waar VHD's/VHDX-en aanwezig zijn. 

**Vrije beschikbare (GB)**: de hoeveelheid vrije ruimte op het volume.

**Totale opslag ruimte die is vereist op het volume (GB)**: de totale vrije opslag ruimte die op het volume is vereist voor een geslaagde initiële replicatie en replicatie van verschillen. 

**Totaal aan extra opslagruimte dat moet worden ingericht op het volume voor een geslaagde replicatie (GB)**: dit is de aanbevolen hoeveelheid totale extra ruimte die moet worden ingericht op het volume voor een geslaagde initiële replicatie en replicatie van verschillen.

## <a name="initial-replication-batching"></a>Initiële replicatie via batchverwerking 

### <a name="why-do-i-need-initial-replication-batching"></a>Waarom moet ik eerste replicaties via batchverwerking uitvoeren?
Als alle VM's tegelijkertijd worden beschermd, is er veel meer vrije opslagruimte vereist. Als er onvoldoende opslag beschikbaar is, gaat de replicatie van de VM's in de hersynchronisatiemodus. Bovendien is er veel meer netwerkbandbreedte nodig om een initiële replicatie van alle virtuele machines te kunnen voltooien. 

### <a name="initial-replication-batching-for-a-selected-rpo"></a>Initiële replicatie via batchverwerking voor een geselecteerde RPO
Dit werkblad bevat de detailweergave van elke batch voor IR. Voor elke RPO wordt een afzonderlijk werkblad IR-batchverwerking gemaakt. 

Nadat u de aanbeveling voor lokale vereiste opslag voor elk volume hebt opgevolgd, wordt de belangrijkste informatie die u nodig hebt voor replicatie vermeld in de lijst met virtuele machines die parallel kunnen worden beveiligd. Deze VM's zijn gegroepeerd in een batch en er kunnen meerdere batches zijn. Beveilig de virtuele machines in de volgorde van de opgegeven batches. Bescherm eerst de VM's in Batch 1. Nadat de initiële replicatie is voltooid, beschermt u de VM's in Batch 2, enzovoorts. De lijst met batches en bijbehorende virtuele machines kunt u via dit blad verkrijgen. 

![Batchverwerkingdetails van IR](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo-h2a.png)

![Aanvullende batchverwerkingdetails IR](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo2-h2a.png)

### <a name="each-batch-provides-the-following-information"></a>Elke batch biedt de volgende informatie 
**Hyper-V-host**: de Hyper-V-host van de virtuele machine die moet worden beveiligd.

**Virtuele machine**: de VM die moet worden beveiligd. 

**Opmerkingen**: als voor een bepaald volume van een virtuele machine een actie is vereist, wordt hier een opmerking weergegeven. Als er bijvoorbeeld niet voldoende vrije ruimte beschikbaar is op een volume, wordt hier de opmerking 'Extra opslag toevoegen om deze VM te beveiligen' weergegeven.

**Volume (VHD-pad)**: de naam van het volume waar de vhd's van de virtuele machine zich bevinden. 

**Vrije ruimte beschikbaar op het volume (GB)**: de vrije schijf ruimte die beschikbaar is op het volume voor de virtuele machine. Bij het berekenen van de beschikbare vrije ruimte op de volumes, wordt rekening gehouden met de schijfruimte die wordt gebruikt voor de replicatie van verschillen voor de virtuele machines in de vorige batches waarvan de VHD's zich op hetzelfde volume bevinden. 

Bijvoorbeeld, VM1, VM2 en VM3 bevinden zich op een volume zoals E:\VHDpath. Vóór de replicatie is er 500 GB aan vrije ruimte op het volume. VM1 maakt deel uit van batch 1, VM2 maakt deel uit van batch 2 en VM3 maakt deel uit van batch 3. Voor VM1 is er 500 G aan vrije ruimte beschikbaar. Voor VM2 is 500 vrije ruimte beschikbaar, de schijfruimte die is vereist voor een replicatie van verschillen voor VM1. Als er voor VM1 300 GB ruimte nodig is voor een replicatie van verschillen, dan bedraagt de vrije ruimte die beschikbaar is voor VM2 500 GB – 300 GB = 200 GB. Voor een replicatie van verschillen van VM2 zou er dus 300 GB nodig zijn. De hoeveelheid vrije ruimte voor VM3 is 200 GB - 300 GB =-100 GB.

**Opslagruimte vereist op het volume (GB) voor een initiële replicatie**: de vrije opslagruimte die op het volume is vereist om een initiële replicatie voor de virtuele machine te kunnen uitvoeren.

**Opslag vereist op het volume voor Delta replicatie (GB)**: de vrije opslag ruimte die is vereist op het volume voor de virtuele machine voor replicatie van verschillen.

**Extra opslag vereist op basis van een tekort dat een replicatiefout zou veroorzaken (GB)**: de extra vereiste opslagruimte op het volume voor de virtuele machine. Dit is de maximale hoeveelheid opslagruimte, minus de beschikbare ruimte op het volume, die is vereist voor een initiële replicatie en een replicatie van verschillen.

**Minimale bandbreedte vereist voor initiële replicatie (Mbps)**: de minimale bandbreedte die is vereist voor de initiële replicatie voor de virtuele machine.

**Minimale band breedte die is vereist voor de replicatie van verschillen (Mbps)**: de minimale band breedte die is vereist voor de replicatie van verschillen voor de virtuele machine.

### <a name="network-utilization-details-for-each-batch"></a>Gegevens over het netwerkgebruik voor elke batch 
Elke tabel bevat een samenvatting van het netwerkgebruik van de batch.

**Beschik bare band breedte voor batch**: de band breedte die beschikbaar is voor de batch na het nadenken van de band breedte van de voor gaande batch replicatie.

**Geschatte bandbreedte beschikbaar voor initiële replicatie van batch**: de bandbreedte die beschikbaar is voor een initiële replicatie van de virtuele machines van de batch. 

**Verbruikte bandbreedte initiële replicatie van verschillen van batch**: de bandbreedte die beschikbaar is voor een replicatie van verschillen van de virtuele machines van de batch. 

**Geschatte tijd voor de initiële replicatie voor batch (UU: mm)**: de geschatte tijd voor de initiële replicatie in uren: minuten.



## <a name="next-steps"></a>Volgende stappen
Meer informatie over [kostenraming](hyper-v-deployment-planner-cost-estimation.md).