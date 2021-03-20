---
title: Analyseer het Deployment Planner rapport voor VMware-nood herstel met Azure Site Recovery
description: In dit artikel wordt beschreven hoe u het door de herstel Deployment Planner gegenereerde rapport kunt analyseren voor VMware-nood herstel op Azure met behulp van Azure Site Recovery.
author: rajeswari-mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: ramamill
ms.openlocfilehash: 7e2db720bb37a25b8511bd1c42c0c18e139aa216
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93186600"
---
# <a name="analyze-the-deployment-planner-report-for-vmware-disaster-recovery-to-azure"></a>Analyseer het Deployment Planner rapport voor VMware-nood herstel naar Azure

Het gegenereerde Microsoft Excel-rapport bevat de volgende werkbladen:
## <a name="on-premises-summary"></a>Samenvatting on-premises
Het werkblad Samenvatting on-premises biedt een overzicht van de geprofileerde VMware-omgeving.

![Samenvatting van de on-premises van VMware-omgeving](media/site-recovery-vmware-deployment-planner-analyze-report/on-premises-summary-v2a.png)

**Begindatum** en **Einddatum** zijn de begin- en einddatums van de profileringsgegevens die in aanmerking komen voor het genereren van rapporten. De begindatum is standaard de datum waarop de profilering start en de einddatum de datum waarop de profilering stopt. Dit kunnen de waarden voor StartDate en EndDate zijn als het rapport met deze parameters wordt gegenereerd.

**Totale aantal dagen van profilering**: het totale aantal dagen van profilering tussen de begin- en einddatum waarvoor het rapport wordt gegenereerd.

**Aantal compatibele virtuele machine**: het totale aantal compatibele virtuele machines waarvoor de vereiste netwerkbandbreedte, het vereiste aantal Azure-opslagaccounts, Microsoft Azure-kerngeheugens, configuratieservers en aanvullende processervers wordt berekend.

**Totale aantal schijven voor alle compatibele virtual machines**: het aantal dat wordt gebruikt als een van de invoerwaarden om het aantal configuratieservers en aanvullende processenservers te bepalen dat in de implementatie moet worden gebruikt.

**Gemiddeld aantal schijven per compatibele virtuele machine**: het gemiddelde aantal schijven dat voor alle compatibele virtuele machines is berekend.

**Gemiddelde schijfgrootte (GB)**: de gemiddelde schijfgrootte die voor alle compatibele virtuele machines is berekend.

**Gewenste RPO (minuten)**: de standaard-RPO-waarde of de waarde die tijdens het genereren van het rapport voor de parameter DesiredRPO wordt doorgegeven om de vereiste bandbreedte in te schatten.

**Gewenste bandbreedte (Mbps)**: de waarde die u tijdens het genereren van het rapport voor de parameter Bandwidth hebt doorgegeven om de haalbare RPO in te schatten.

**Waargenomen typisch gegevensverloop per dag (GB)**: het gemiddelde gegevensverloop dat voor alle profileringsdagen is vastgesteld. Dit aantal wordt gebruikt als een van de invoerwaarden om het aantal configuratieservers en aanvullende processenservers te bepalen dat in de implementatie moet worden gebruikt.

## <a name="recommendations"></a>Aanbevelingen

De aanbevelingen voor het eigenschappenvenster van de VMware naar Azure rapport heeft de volgende gegevens aan de hand van de geselecteerde gewenste RPO:

![Aanbevelingen voor VMware naar Azure rapport](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

### <a name="profiled-data"></a>Geprofileerde gegevens
![De weergave met geprofileerde gegevens in de implementatieplanner](media/site-recovery-vmware-deployment-planner-analyze-report/profiled-data-v2a.png)

**Periode geprofileerde gegevens**: de periode waarin de profilering is uitgevoerd. Standaard gebruikt het hulpprogramma voor de berekening alle geprofileerde gegevens, tenzij het rapport wordt gegenereerd voor een bepaalde periode (tijdens het genereren van het rapport zijn dan waarden opgegeven voor StartDate en EndDate).

**Servernaam**: de naam of het IP-adres van de VMware vCenter of ESXi-host met de virtuele machines waarvoor het rapport wordt gegenereerd.

**Gewenste RPO**: het gewenste beoogde herstelpunt (RPO) voor uw implementatie. Standaard wordt de vereiste netwerkbandbreedte berekend voor de RPO-waarden van 15, 30 en 60 minuten. Op basis van de selectie worden de betrokken waarden bijgewerkt op het blad. Als u de *para desiredrpoinmin* -para meter hebt gebruikt tijdens het genereren van het rapport, wordt die waarde weer gegeven in het gewenste RPO-resultaat.

### <a name="profiling-overview"></a>Overzicht van profilering

![Resultaten van profilering in de implementatieplanner](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-v2a.png)

**Totale aantal geprofileerde virtuele machines**: het totale aantal virtuele machines waarvan de geprofileerde gegevens beschikbaar zijn. Als het bestand dat is opgegeven bij VMListFile namen van virtuele machines bevat die niet zijn geprofileerd, worden deze virtuele machines niet meegenomen in het rapport en uitgesloten van het totale aantal geprofileerde virtuele machines.

**Compatibele virtuele machines**: het aantal virtuele machines dat op Azure kan worden beveiligd met Site Recovery. Dit is het totale aantal compatibele virtuele machines waarvoor de vereiste netwerkbandbreedte, het aantal opslagaccounts, het aantal Azure-kerngeheugens, en het aantal configuratieservers en aanvullende processervers wordt berekend. De details van elke compatibele virtuele machine zijn beschikbaar in de sectie 'Compatibele VM's'.

**Niet-compatibele virtuele machines**: het aantal geprofileerde virtuele machines dat niet compatibel is met beveiliging met Site Recovery. De redenen voor incompatibiliteit worden vermeld in de sectie 'Niet-compatibele VM's'. Als het bestand dat is opgegeven bij VMListFile namen bevat van virtuele machines die niet zijn geprofileerd, worden die virtuele machines uitgesloten van het aantal niet-compatibele virtuele machines. Deze virtuele machines worden weergegeven als 'Gegevens niet gevonden' aan het einde van de sectie 'Niet-compatibele VM's'.

**Gewenste RPO**: het gewenste beoogde herstelpunt (Recovery Point Objective [RPO]) in minuten. Het rapport wordt gegenereerd voor drie RPO-waarden: 15 (standaard), 30 en 60 minuten. De aanbeveling voor bandbreedte in het rapport wordt gewijzigd op basis van uw selectie in de vervolgkeuzelijst Gewenste RPO in de rechterbovenhoek van het blad. Als u het rapport hebt gegenereerd met een aangepaste waarde voor de parameter  *-DesiredRPO*, wordt deze aangepaste waarde als standaardwaarde weergegeven in de vervolgkeuzelijst Gewenste RPO.

### <a name="required-network-bandwidth-mbps"></a>Vereiste netwerkbandbreedte (Mbps)

![Vereiste netwerkbandbreedte in de implementatieplanner](media/site-recovery-vmware-deployment-planner-analyze-report/required-network-bandwidth-v2a.png)

**Altijd aan de RPO voldoen:** de aanbevolen bandbreedte in Mbps die moet worden toegewezen om altijd te voldoen aan uw gewenste RPO. Deze bandbreedte moet worden toegewezen voor onveranderlijke replicatie van verschillen bij al uw compatibele virtuele machines om eventuele RPO-schendingen te voorkomen.

**90% van de tijd aan de RPO voldoen**: als u vanwege de kosten voor een breedbandverbinding of om een andere reden niet de benodigde bandbreedte kunt toepassen om altijd aan de RPO te voldoen, kunt u een lager bedrag voor bandbreedte kiezen waarmee u 90% van de tijd aan de RPO kunt voldoen. Voor inzicht in de gevolgen van het instellen van deze lagere bandbreedte bevat het rapport een wat-als-analyse van het aantal en de duur van de RPO-schendingen die u kunt verwachten.

**Behaalde doorvoer:** de doorvoer van de server waar u de GetThroughput-opdracht hebt uitgevoerd naar de Microsoft Azure-regio waar het opslagaccount zich bevindt. Deze waarde geeft het geschatte niveau aan dat kan worden behaald als u de compatibele virtuele machines beveiligt met Site Recovery, op voorwaarde dat de kenmerken voor opslag en netwerk van uw configuratieserver of processerver hetzelfde blijven als die van de server waarop u het hulpprogramma hebt uitgevoerd.

Voor replicatie moet u de aanbevolen bandbreedte gebruiken om de RPO 100 procent van de tijd te halen. Als u na het instellen van de bandbreedte geen toename ziet in de gerapporteerde behaalde doorvoer, doet u het volgende:

1. Controleer of er een netwerk-QoS (Quality of Service) is die de doorvoer van Site Recovery beperkt.

2. Controleer of de kluis van Site Recovery zich in de dichtstbijzijnde fysieke ondersteunde Microsoft Azure-regio bevindt voor minimale netwerklatentie.

3. Controleer de kenmerken van de lokale opslag om te bepalen of u de hardware kunt verbeteren (bijvoorbeeld SSD in plaats van HDD).

4. Wijzig de Site Recovery-instellingen op de processerver om [de voor replicatie gebruikte netwerkbandbreedte te vergroten](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Als u het hulpprogramma uitvoert op een configuratieserver of processerver die al virtuele machines beveiligt, voert u het hulpprogramma een paar keer uit. De bereikte doorvoer verandert afhankelijk van het gegevensverloop op dat moment.

Voor alle Enterprise-implementaties van Site Recovery wordt het gebruik van [ExpressRoute](https://aka.ms/expressroute) aanbevolen.

### <a name="required-storage-accounts"></a>Vereiste opslagaccounts
De volgende grafiek geeft het totale aantal opslagaccounts aan (Standard Storage en Premium Storage) dat is vereist voor het beveiligen van alle compatibele virtuele machines. Zie de sectie 'VM-opslagplaatsing' voor meer informatie over welk opslagaccount u moet gebruiken voor elke virtuele machine. Als u v 2.5 van Deployment Planner gebruikt, wordt in deze aanbeveling alleen het aantal standaard cache opslag accounts weer gegeven dat nodig is voor replicatie, omdat de gegevens rechtstreeks naar Managed Disks worden geschreven.

![Vereiste opslagaccounts in de implementatieplanner](media/site-recovery-vmware-deployment-planner-analyze-report/required-storage-accounts-v2a.png)

### <a name="required-number-of-azure-cores"></a>Vereiste aantal Azure-kerngeheugens
Dit is het totale aantal kerngeheugens dat moet worden ingesteld vóór failover of testfailover van de compatibele virtuele machines. Als er in het abonnement onvoldoende kerngeheugens beschikbaar zijn, kan Site Recovery geen virtuele machines maken op het moment van een failover of testfailover.

![Vereiste aantal Azure-kerngeheugens in de implementatieplanner](media/site-recovery-vmware-deployment-planner-analyze-report/required-cores-v2a.png)

### <a name="required-on-premises-infrastructure"></a>Vereiste on-premises infrastructuur
Het totale aantal configuratieservers en aanvullende processervers dat moet worden geconfigureerd voor het beveiligen van alle compatibele virtuele machines. Afhankelijk van de ondersteunde [aanbevelingen voor de configuratieserver](/en-in/azure/site-recovery/site-recovery-plan-capacity-vmware#size-recommendations-for-the-configuration-server), kan het hulpprogramma extra servers aanbevelen. De aanbeveling wordt gebaseerd op wat het grootst is: het gegevensverloop per dag of het maximum aantal beveiligde virtuele machines (uitgaande van gemiddeld drie schijven per virtuele machine), ongeacht wat het eerste wordt bereikt op de configuratieserver of de aanvullende processerver. De details van het totale verloop per dag en het totale aantal beveiligde schijven vindt u in de sectie Samenvatting van on-premises.

![Vereiste on-premises infrastructuur in de implementatieplanner](media/site-recovery-vmware-deployment-planner-analyze-report/required-on-premises-components-v2a.png)

### <a name="what-if-analysis"></a>Wat-als-analyses
Deze analyse beschrijft hoeveel schendingen zich kunnen voordoen tijdens de profileringsperiode wanneer u een lagere bandbreedte instelt om 90% van de tijd aan de RPO te voldoen. Op elke dag kunnen er een of meer RPO-schendingen optreden. De grafiek toont de piek-RPO van de dag.
Op basis van deze analyse kunt u besluiten of het aantal RPO-schendingen voor alle dagen en de hoogste RPO per dag acceptabel zijn in combinatie met de opgegeven lagere bandbreedte. Als u dat vindt, kunt u de lagere bandbreedte toewijzen voor replicatie. Zo niet, dan stelt u de hogere bandbreedte in zoals voorgesteld om altijd aan de RPO te voldoen.

![Wat-als analyse in de implementatieplanner](media/site-recovery-vmware-deployment-planner-analyze-report/what-if-analysis-v2a.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>Aanbevolen VM-batchgrootte voor de initiële replicatie
In deze sectie ziet u een aanbeveling voor het aantal virtuele machines die gelijktijdig kunnen worden beveiligd om de initiële replicatie binnen 72 uur met de voorgestelde bandbreedte af te ronden om gedurende de ingestelde tijd altijd te voldoen aan de gewenste RPO. Deze waarde is kunt u aanpassen. Gebruik hiervoor de parameter *GoalToCompleteIR* tijdens het genereren van het rapport.

In de grafiek ziet u het bereik met bandbreedtewaarden en de berekende grootte voor de batch virtuele machines waarmee de initiële replicatie binnen 72 uur kan worden voltooid, uitgaande van de gemiddelde grootte van de gedetecteerde virtuele machines binnen de volledige set compatibele virtuele machines.

In de openbare preview bevat het rapport geen informatie over de virtuele machines die in een batch moeten worden opgenomen. U kunt de schijfgrootte uit de sectie 'Compatibele VM's' gebruiken om de grootte van elke virtuele machine te bepalen en vervolgens virtuele machines voor een batch te selecteren. U kunt ook virtuele machines selecteren op basis van bekende workloadkenmerken. De tijd die een initiële replicatie kost, wijzigt proportioneel op basis van de daadwerkelijke schijfgrootte van de virtuele machine, de gebruikte schijfruimte en de beschikbare netwerkdoorvoer.

![Aanbevolen VM-batchgrootte](media/site-recovery-vmware-deployment-planner-analyze-report/ir-batching-v2a.png)

### <a name="cost-estimation"></a>Kostenraming
De grafiek toont de weergave Samenvatting van de geschatte totale noodherstel (DR) kosten voor Azure van uw gekozen doelregio en de valuta die u hebt opgegeven voor het genereren van rapporten.

![Samenvatting kostenramingen](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

De samenvatting helpt u bij het begrijpen van de kosten die u nodig hebt om te betalen voor opslag, rekensnelheid, netwerk en licentie wanneer u alle compatibele virtuele machines naar Azure met Azure Site Recovery beveiligt. De kosten worden berekend op basis van compatibele virtuele machines en niet op basis van alle geprofileerde virtuele machines.  

U kunt de kosten maandelijks of jaarlijks weergeven. Meer informatie over [ondersteunde doelregio's](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) en [ondersteunde valuta's](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).

**Kosten per onderdelen** De totale kosten voor Noodherstel zijn onderverdeeld in vier onderdelen: Compute, Storage, netwerk en Azure Site Recovery-licentiekosten. De kosten worden berekend op basis van het verbruik dat zal plaatsvinden tijdens de replicatie, en bij Noodherstel de tijd voor de rekensnelheid, opslag (premium en standard), de ExpressRoute/VPN die is geconfigureerd tussen de lokale site en Azure, en Azure Site Recovery-licentie.

**Kosten per statussen** De kosten voor een totaal noodherstel (DR) zijn is gebaseerd op twee verschillende statussen: Replicatie en Noodherstel.

**Replicatiekosten**: de kosten die zullen worden gemaakt tijdens de replicatie. Dit dekt de kosten van opslag, netwerk en de Azure Site Recovery-licentie.

**Kosten voor DR-oefeningen**: de kosten die zullen worden gemaakt tijdens de testfailovers. Azure Site Recovery laat virtuele machines draaien tijdens de testfailover. De details voor DR-kosten zijn de kosten voor de berekenings- en opslagkosten van de actieve virtuele machines.

**Azure-opslagkosten per maand-jaar** Dit toont de totale opslagkosten die zullen worden gemaakt voor premium en standard-opslag voor replicatie en details voor DR.
U vindt een gedetailleerde kostenanalyse per virtuele machine op het werkblad [Kostenraming](site-recovery-vmware-deployment-planner-cost-estimation.md).

### <a name="growth-factor-and-percentile-values-used"></a>Groeifactor en gebruikte percentielwaarden
Deze sectie onder aan het werkblad toont de percentielwaarde die voor alle prestatiemeteritems van de geprofileerde virtuele machines wordt gebruikt (standaard is dit het 95e percentiel), plus de groeifactor in procenten die in alle berekeningen wordt gebruikt (standaard 30%).

![Groeifactor en gebruikte percentielwaarden](media/site-recovery-vmware-deployment-planner-analyze-report/growth-factor-v2a.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Aanbevelingen met beschikbare bandbreedte als invoer

![Aanbevelingen met beschikbare bandbreedte als invoer](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-bandwidth-input-v2a.png)

Het is mogelijk dat u om wat voor reden dan ook niet meer dan x Mbps bandbreedte kunt instellen voor Site Recovery-replicatie. Met het hulpprogramma kunt u de beschikbare bandbreedte opgeven (dit doet u tijdens het genereren van het rapport met behulp van de parameter -Bandwidth) en zo de haalbare RPO in minuten achterhalen. Aan de hand van deze haalbare RPO-waarde kunt u beslissen of u extra bandbreedte wilt instellen of bereid bent u te beperken tot een noodhersteloplossing voor deze RPO.

![Haalbare RPO voor 500 Mbps bandbreedte](media/site-recovery-vmware-deployment-planner-analyze-report/achievable-rpo-v2a.png)

## <a name="vm-storage-placement"></a>VM-opslagplaatsing

>[!Note]
>Deployment Planner v 2.5 wordt de opslag locatie aanbevolen voor machines die rechtstreeks naar beheerde schijven zullen worden gerepliceerd.

![VM-opslagplaatsing](media/site-recovery-vmware-deployment-planner-analyze-report/vm-storage-placement-v2a.png)

**Replicatie opslag type**: een standaard-of Premium-beheerde schijf, die wordt gebruikt voor het repliceren van alle bijbehorende vm's die worden vermeld in de kolom **vm's die moeten worden geplaatst** .

**Type logboek opslag account**: alle replicatie logboeken worden opgeslagen in een standaard-opslag account.

**Voorgesteld voor voegsel voor opslag account**: het voorgestelde voor voegsel met drie tekens dat kan worden gebruikt voor het benoemen van het cache-opslag account. U kunt uw eigen voorvoegsel gebruiken, maar de suggestie van het hulpprogramma volgt de [naamgevingsregels voor partities voor opslagaccounts](/en-in/azure/storage/blobs/storage-performance-checklist).

**Voorgestelde naam van het logboek account**: de naam van het opslag account na het toevoegen van het voorgestelde voor voegsel. Vervang de naam tussen de punthaken (< en >) door uw aangepaste invoer.

**Plaatsing samen vatting**: een samen vatting van de schijven die nodig zijn voor beveiligde virtuele machines per opslag type. Het bevat het totale aantal Vm's, de totale ingerichte grootte op alle schijven en het totale aantal schijven.

**Te plaatsen virtuele machines**: een lijst van alle virtuele machines die in het opgegeven opslagaccount moeten worden geplaatst voor optimale prestaties en optimaal gebruik.

## <a name="compatible-vms"></a>Compatibele VM's
![Excel-werkblad met compatibele VM's](media/site-recovery-vmware-deployment-planner-analyze-report/compatible-vms-v2a.png)

**VM-naam**: de naam of het IP-adres van de virtuele machine dat wordt gebruikt in het bestand dat is opgegeven voor VMListFile wanneer een rapport wordt gegenereerd. Deze kolom bevat ook de schijven (VMDK's) die aan de virtuele machines zijn gekoppeld. Om onderscheid te maken tussen virtuele machines van vCenter met dubbele namen of IP-adressen, bevatten de namen de naam van de ESXi-host. De vermelde ESXi-host is de host waar de virtuele machine werd geplaatst op het moment van detectie door het hulpprogramma tijdens de profileringsperiode.

**VM-compatibiliteit**: waarden zijn **Ja** en **Ja \* *_. _* ja** \* is voor exemplaren waarin de virtuele machine geschikt is voor [Premium-ssd's](../virtual-machines/disks-types.md). Het hoge geprofileerde verloop of de IOPS-schijf komt overeen met categorie P20 of P30, maar de grootte van de schijf zorgt ervoor dat P10 of P20 wordt toegewezen. Het opslagaccount bepaalt aan welk schijftype voor Premium Storage een schijf wordt toegewezen, op basis van de grootte. Bijvoorbeeld:
* <128 GB is een P10.
* 128 GB tot 256 GB is een P15
* 256 GB tot 512 GB is een P20.
* 512 GB tot 1024 GB is een P30.
* 1025 GB tot 2048 GB is een P40.
* 2049 GB tot 4095 GB is een P50.

Als bijvoorbeeld de eigenschappen van de werk belasting van een schijf in de categorie P20 of P30 worden geplaatst, maar de grootte ervan wordt toegewezen aan een lager type Premium-opslag schijf, markeert het hulp programma die virtuele machine als **Ja** \* . Het hulpprogramma adviseert ook om ofwel de grootte van de bronschijf te wijzigen zodat deze overeenkomt met het schijftype voor Premium Storage of de post-failover van het doelschijftype te wijzigen.

**Opslagtype**: Standard of Premium.

**Asrseeddisk (beheerde schijf) gemaakt voor replicatie**: de naam van de schijf die wordt gemaakt wanneer u replicatie inschakelt. De gegevens en de bijbehorende moment opnamen worden opgeslagen in Azure.

**Piek R/W IOPS (met groeifactor)**: de piekworkload-IOPS voor lezen/schrijven op de schijf (standaard het 95e percentiel), inclusief de toekomstige groeifactor (standaard 30%). De totale IOPS voor lezen/schrijven van een virtuele machine is niet altijd de som van de IOPS voor lezen/schrijven van de afzonderlijke schijven van de virtuele machine. De piek-IOPS voor lezen/schrijven van de virtuele machine is namelijk de piek van de som van de IOPS voor lezen/schrijven van de afzonderlijke schijven voor elke minuut van de profileringsperiode.

**Piek voor gegevensverloop in Mbps (met groeifactor)**: het piekverloop op de schijf (standaard het 95e percentiel), inclusief de toekomstige groeifactor (standaard 30%). Houd er rekening mee dat de totale gegevensverloopwaarde van de virtuele machine niet altijd de som is van het gegevensverloop van de afzonderlijke schijven van de virtuele machine. De piekwaarde voor het gegevensverloop van de virtuele machine is namelijk de piek van de som van het gegevensverloop van de individuele schijven voor elke minuut van de profileringsperiode.

**Azure VM-grootte**: de ideale toegewezen grootte voor virtuele machines van Azure Cloud Services voor deze on-premises virtuele machine. De toewijzing is gebaseerd op het geheugen, het aantal schijven/kerngeheugens/NIC’s en de IOPS voor lezen/schrijven van de on-premises virtuele machine. De aanbeveling is altijd de laagste Azure VM-grootte die overeenkomt met alle kenmerken van de on-premises virtuele machine.

**Aantal schijven**: het totale aantal schijven (VMDK's) van de virtuele machine.

**Schijfgrootte (GB)**: de totale ingestelde grootte van alle schijven van de virtuele machine. Het hulpprogramma toont ook de schijfgrootte voor de afzonderlijke schijven in de virtuele machine.

**Kerngeheugens**: het aantal CPU-kerngeheugens van de virtuele machine.

**Geheugen (MB)**: het RAM-geheugen van de virtuele machine.

**NIC's**: het aantal NIC's van de virtuele machine.

**Opstarttype**: het opstarttype van de virtuele machine. Dit kan BIOS of EFI zijn.  Op dit moment biedt Azure Site Recovery ondersteuning voor Windows Server EFI-VM's (Windows Server 2012, 2012 R2 en 2016), als het aantal partities in de opstartschijf minder is dan 4 en de opstartsector 512 bytes groot is. Als u EFI-VM's wilt beveiligen, moet Azure Site Recovery Mobility Service versie 9.13 of hoger zijn. Alleen failover wordt ondersteund voor EFI-VM's. Failback wordt niet ondersteund.  

**Type besturingssysteem**: het type besturingssysteem van de virtuele machine. Dit kan Windows of Linux zijn, of een ander besturingssysteem op basis van de sjabloon die in VMware vSphere is gekozen tijdens het maken van de VM.  

## <a name="incompatible-vms"></a>Niet-compatibele VM's

![Excel-werkblad met niet-compatibele VM's
](media/site-recovery-vmware-deployment-planner-analyze-report/incompatible-vms-v2a.png)

**VM-naam**: de naam of het IP-adres van de virtuele machine dat wordt gebruikt in het bestand dat is opgegeven voor VMListFile wanneer een rapport wordt gegenereerd. Deze kolom bevat ook de VMDK's die aan de virtuele machines zijn gekoppeld. Om onderscheid te maken tussen virtuele machines van vCenter met dubbele namen of IP-adressen, bevatten de namen de naam van de ESXi-host. De vermelde ESXi-host is de host waar de virtuele machine werd geplaatst op het moment van detectie door het hulpprogramma tijdens de profileringsperiode.

**VM-compatibiliteit**: geeft aan waarom de virtuele machine niet compatibel is voor gebruik met Site Recovery. De redenen worden voor elke niet-compatibele schijf van de virtuele machine beschreven. Op basis van gepubliceerde [opslaglimieten](/en-in/azure/storage/common/scalability-targets-standard-account) kan dit een van de volgende redenen zijn:

* Verkeerde grootte van de gegevens schijf of onjuiste grootte van de besturingssysteem schijf. [Controleer](vmware-physical-azure-support-matrix.md#azure-vm-requirements) de ondersteunings limieten. 
* Totale grootte van virtuele machine (replicatie + TFO) overschrijdt de ondersteunde limiet voor opslagaccounts (35 TB). Dit probleem treedt meestal op wanneer één schijf in de virtuele machine een prestatiekenmerk heeft dat groter is dan de maximaal ondersteunde limieten voor Standard-opslag van Azure of Site Recovery. De virtuele machine komt dan in aanmerking voor Premium Storage. De maximaal ondersteunde grootte van een Premium Storage-account is echter 35 TB en één beveiligde virtuele machine kan niet worden beveiligd via meerdere opslagaccounts. Houd er ook rekening mee dat bij het uitvoeren van een testfailover op een beveiligde virtuele machine, deze wordt uitgevoerd in het opslagaccount waarin ook de replicatie plaatsvindt. Stel in dit geval 2 x de grootte van de schijf in om replicatie mogelijk te maken en het testen van failover parallel uit te voeren.

* De bron-IOPS is groter dan de ondersteunde IOPS-limiet voor opslag van 7500 per schijf.

* De bron-IOPS is groter dan de ondersteunde IOPS-limiet voor opslag van 80.000 per schijf.

* Het gemiddelde gegevens verloop overschrijdt de ondersteunde Site Recovery gegevens verloop limiet van 20 MB/s voor de gemiddelde I/O-grootte voor de schijf.

* Het piekgegevensverloop voor alle schijven in de virtuele machine overschrijdt de maximaal ondersteunde limiet voor het piekgegevensverloop van Site Recovery van 54 MB/s per virtuele machine.

* De gemiddelde effectieve schrijf-IOPS overschrijdt de ondersteunde IOPS-limiet van Site Recovery van 840 voor schijven.

* De berekende opslag voor momentopnamen overschrijdt de ondersteunde opslaglimiet van 10 TB voor momentopnamen.

* Totaal gegevensverloop per dag overschrijdt het ondersteunde verloop per daglimiet van 2 TB per processerver.


**Piek R/W IOPS (met groeifactor)**: de piekworkload-IOPS op de schijf (standaard het 95e percentiel) met inbegrip van de toekomstige groeifactor (standaard 30%). De totale IOPS voor lezen/schrijven van een virtuele machine is niet altijd de som van de IOPS voor lezen/schrijven van de afzonderlijke schijven van de virtuele machine. De piek-IOPS voor lezen/schrijven van de virtuele machine is namelijk de piek van de som van de IOPS voor lezen/schrijven van de afzonderlijke schijven voor elke minuut van de profileringsperiode.

**Piekgegevensverloop in Mbps (met groeifactor)**: het piekverloop op de schijf (standaard het 95e percentiel) met inbegrip van de toekomstige groeifactor (standaard 30%). Houd er rekening mee dat de totale gegevensverloopwaarde van de virtuele machine niet altijd de som is van het gegevensverloop van de afzonderlijke schijven van de virtuele machine. De piekwaarde voor het gegevensverloop van de virtuele machine is namelijk de piek van de som van het gegevensverloop van de individuele schijven voor elke minuut van de profileringsperiode.

**Aantal schijven**: het totale aantal VMDK's van de virtuele machine.

**Schijfgrootte (GB)**: de totale ingestelde grootte van alle schijven van de virtuele machine. Het hulpprogramma toont ook de schijfgrootte voor de afzonderlijke schijven in de virtuele machine.

**Kerngeheugens**: het aantal CPU-kerngeheugens van de virtuele machine.

**Geheugen (MB)**: de hoeveelheid RAM-geheugen van de virtuele machine.

**NIC's**: het aantal NIC's van de virtuele machine.

**Opstarttype**: het opstarttype van de virtuele machine. Dit kan BIOS of EFI zijn.  Op dit moment biedt Azure Site Recovery ondersteuning voor Windows Server EFI-VM's (Windows Server 2012, 2012 R2 en 2016), als het aantal partities in de opstartschijf minder is dan 4 en de opstartsector 512 bytes groot is. Als u EFI-VM's wilt beveiligen, moet Azure Site Recovery Mobility Service versie 9.13 of hoger zijn. Alleen failover wordt ondersteund voor EFI-VM's. Failback wordt niet ondersteund.

**Type besturings systeem**: dit is het type besturings systeem van de virtuele machine. Dit kan Windows of Linux zijn, of een ander besturingssysteem op basis van de sjabloon die in VMware vSphere is gekozen tijdens het maken van de VM.

## <a name="azure-site-recovery-limits"></a>Azure Site Recovery-limieten
De volgende tabel bevat de Azure Site Recovery-limieten. Deze limieten zijn gebaseerd op onze tests, maar dekken niet alle mogelijke toepassings-I/O-combinaties. De werkelijke resultaten kunnen variëren op basis van uw toepassings-I/O-combinatie. Voor optimale resultaten, zelfs na het plannen van de implementatie, is het altijd beter om toepassingen uitgebreid te testen met behulp van een testfailover. Zo krijgt u een nauwkeurig inzicht in de prestaties van de applicatie.

**Doel van replicatie opslag** | **Gemiddelde I/O-grootte van bronschijf** |**Gemiddeld gegevens verloop van bron schijf** | **Totale gegevens verloop van bron schijf per dag**
---|---|---|---
Standard Storage | 8 kB | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 8 kB  | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 16 kB | 4 MB/s |  336 GB per schijf
Premium P10 of P15 schijf | 32 kB of meer | 8 MB/s | 672 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 8 kB    | 5 MB/s | 421 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 16 kB of meer | 20 MB/s | 1684 GB per schijf

**brongegevensverloop** | **Maximum limiet**
---|---
Piekgegevensverloop over alle schijven op een VM | 54 MB/s
Maximumgegevensverloop per dag dat wordt ondersteund door een processerver | 2 TB

Dit zijn gemiddelden uitgaande van een I/O-overlapping van 30%. Site Recovery kan een hogere doorvoer verwerken op basis van overlappingsverhouding, grotere schrijfgrootten en daadwerkelijk workload-I/O-gedrag. De bovenstaande waarden zijn gebaseerd op een typische backlog van ongeveer vijf minuten. Dat wil zeggen dat de gegevens na het uploaden binnen vijf minuten worden verwerkt en er een herstelpunt is gemaakt.


## <a name="cost-estimation"></a>Kostenraming
Meer informatie over [kostenraming](site-recovery-vmware-deployment-planner-cost-estimation.md).


## <a name="next-steps"></a>Volgende stappen
Meer informatie over [kostenraming](site-recovery-vmware-deployment-planner-cost-estimation.md).