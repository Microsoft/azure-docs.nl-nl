---
title: Problemen met hoog CPU-verbruik oplossen voor Azure Windows Virtual Machines
description: .
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: mnanda
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: 9971cc26-916f-4e49-83cd-71eca74804f0
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/24/2020
ms.author: mnanda
ms.openlocfilehash: 124650f4570608efabba3d8002c14ad06c4782ad
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571502"
---
# <a name="troubleshoot-high-cpu-issues-for-azure-windows-virtual-machines"></a>Problemen met hoog CPU-verbruik oplossen voor Azure Windows Virtual Machines

## <a name="summary"></a>Samenvatting

Prestatie problemen doen zich voor in verschillende besturings systemen of toepassingen en voor elk probleem is een unieke aanpak vereist om problemen op te lossen. De meeste van deze problemen zijn van groot belang voor CPU, geheugen, netwerken en invoer/uitvoer (I/O) als sleutel locaties waar het probleem optreedt. Elk van deze gebieden genereert verschillende symptomen (soms tegelijkertijd) en vereist een andere diagnose en oplossing.

Dit artikel bevat informatie over het gebruik van hoge CPU-problemen die optreden op Azure Virtual Machines (Vm's) waarop het Windows-besturings systeem wordt uitgevoerd.

### <a name="high-cpu-issues-on-azure-windows-vms"></a>Problemen met hoge CPU op Azure Windows-Vm's

Afgezien van de problemen met de I/O-en netwerk latentie, vereisen de probleem oplossing voor CPU en geheugen dezelfde hulpprogram ma's en stappen als voor on-premises servers. Een van de hulpprogram ma's die micro soft ondersteunt, is PerfInsights (beschikbaar voor zowel Windows als Linux). PerfInsights kan een diagnose van de best practices van Azure VM bieden in een gebruiks vriendelijk rapport. PerfInsights is ook een wrapper-hulp programma waarmee u perfmon-, Xperf-en netmon-gegevens kunt verzamelen, afhankelijk van de vlaggen die zijn geselecteerd in het hulp programma.

De meeste bestaande hulpprogram ma's voor probleem oplossing, zoals perfmon of ProcMon, die worden gebruikt voor on-premises servers, werken op Azure Windows-Vm's. PerfInsights is echter expliciet ontworpen voor Azure-Vm's om meer inzichten te bieden, waaronder aanbevolen procedures van Azure, SQL best practices, I/O-latentie grafieken met hoge resolutie, de CPU-en geheugen tabbladen, enzovoort.

Of de toepassing wordt uitgevoerd als User-Mode of kernelmodus, een thread van een actief proces vereist CPU-cycli om de code uit te voeren waarvan deze is opgebouwd. Veel problemen zijn direct gerelateerd aan de werk belasting. Het type werk belasting dat op de server is opgenomen, is afhankelijk van het CPU-gebruik.

#### <a name="common-factors"></a>Algemene factoren

De volgende factoren zijn gebruikelijk in een hoge CPU-situatie:

- Een recente code wijziging of-implementatie die voornamelijk van toepassing is op apps zoals Internet Information Services (IIS), micro soft share point, Microsoft SQL Server of toepassingen van derden.

- Een recente update die kan zijn gerelateerd aan een update op het niveau van het besturings systeem of op cumulatieve updates en oplossingen op toepassings niveau.

- Een query wijziging of verouderde indexen. SQL Server-en Oracle-gegevenslaag toepassingen hebben ook een optimalisatie van het query plan als een andere factor. Het wijzigen van gegevens of het ontbreken van geschikte indexen kan ertoe leiden dat verschillende query's meer reken intensief worden.

- Azure VM-specifiek. Er zijn bepaalde processen, zoals RDAgent en extensie-specifieke processen, zoals monitoring agent, MMA agent of Security client, die een hoog CPU-verbruik kunnen veroorzaken. Deze processen moeten worden weer gegeven vanuit het perspectief van een configuratie of bekende problemen.

## <a name="troubleshoot-the-issue"></a>Het probleem oplossen

Dit artikel richt zich op het isoleren van het problematische proces. Verdere analyse is specifiek voor het proces dat een hoog CPU-verbruik biedt.

Als het proces bijvoorbeeld is SQL Server (sqlservr.exe), worden de volgende stappen uitgevoerd om te analyseren welke query de meeste CPU-cycli in een bepaalde periode heeft gebruikt.

### <a name="scope-the-issue"></a>Het probleem bereiken

Hier volgen enkele vragen die u kunt stellen wanneer u het probleem oplost:

- Is er een patroon voor het probleem? Wordt het probleem met de hoge CPU bijvoorbeeld op een bepaald tijdstip elke dag, week of maand uitgevoerd? Als dat het geval is, kunt u dit probleem correleren aan een taak, rapport of gebruikers aanmelding?

- Is het hoge CPU-probleem begonnen na een recente code wijziging? Hebt u een update toegepast in Windows of in een toepassing?

- Is het hoge CPU-probleem begonnen na een wijziging in de werk belasting, zoals een toename van het aantal gebruikers, een hogere gegevens instroom of een groter aantal rapporten?

- Is voor Azure het hoge CPU-probleem in een van de volgende omstandigheden gestart?

  - Na een recente herimplementatie of opnieuw opstarten
  - Wanneer een SKU-of VM-type is gewijzigd
  - Wanneer een nieuwe uitbrei ding is toegevoegd
  - Nadat load balancer wijzigingen zijn aangebracht

### <a name="azure-caveats"></a>Voor behoud van Azure

Inzicht in uw werk belasting. Wanneer u een virtuele machine selecteert, kunt u het vCPU-aantal (virtuele CPU) te laag inschatten wanneer u de algemene kosten voor het hosten van uw apparaten bekijkt. Als uw werk belasting reken intensief is, kan het selecteren van een kleinere VM-SKU met een of twee Vcpu's werk belasting problemen veroorzaken. Test verschillende configuraties voor uw werk belasting om te bepalen wat de beste reken capaciteit is vereist.

Er zijn bepaalde VM-reeksen, zoals de reeks B (burst-modus), die worden aanbevolen voor kwaliteits controle (QA) en tests. Het gebruik van deze reeksen in de productie omgeving beperkt de computer capaciteit nadat de CPU-tegoeden zijn uitgeput.

Voor bekende toepassingen, zoals SQL Server, Oracle, RDS (Extern bureaublad-services), virtueel bureau blad, IIS of share point, zijn er artikelen van de best practices van Azure die aanbevelingen bevatten voor een minimale configuratie van deze werk belastingen.

### <a name="ongoing-high-cpu-issues"></a>Voortdurende problemen met hoge CPU

Als het probleem nu optreedt, kunt u de proces tracering het beste vastleggen om te bepalen wat het probleem veroorzaakt. U kunt bestaande hulpprogram ma's gebruiken die u hebt gebruikt voor on-premises Windows-servers om het proces te vinden. De volgende hulpprogram ma's worden aanbevolen door Azure-ondersteuning voor Azure-Vm's.

### <a name="perfinsights"></a>PerfInsights

PerfInsights is het aanbevolen hulp programma van Azure-ondersteuning voor prestatie problemen met de virtuele machine. Het is ontworpen voor het bedekken van best practices en speciale analyse tabbladen voor CPU-, geheugen-en I/O-grafieken met hoge resolutie. U kunt de OnDemand-service uitvoeren via de Azure Portal of vanuit de virtuele machine. U kunt de gegevens delen met het ondersteunings team van Azure.

#### <a name="run-perfinsights"></a>PerfInsights uitvoeren

PerfInsights is beschikbaar voor het [Windows](./how-to-use-perfinsights.md) -en [Linux](./how-to-use-perfinsights-linux.md) -besturings systeem. Dit zijn de opties voor Windows.

#### <a name="run-and-analyze-reports-through-azure-portal"></a>Rapporten uitvoeren en analyseren via Azure Portal

Wanneer deze is [geïnstalleerd via de Azure Portal](./performance-diagnostics.md), wordt in feite een extensie op de VM geïnstalleerd. Gebruikers kunnen ook PerfInsights als een uitbrei ding installeren door rechtstreeks naar [uitbrei dingen in de VM-Blade](./performance-diagnostics-vm-extension.md)te gaan en vervolgens de optie prestatie diagnostiek te kiezen.

#### <a name="azure-portal-option-1"></a>Azure Portal optie 1

Blader naar de VM-Blade en selecteer de optie **prestatie diagnostiek** . U wordt gevraagd om de optie (gebruik extensies) te installeren op de virtuele machine waarvoor u deze hebt geselecteerd.

  ![Diagnostische gegevens over prestaties installeren](./media/troubleshoot-high-cpu-issues-azure-windows-vm/1-install-performance-diagnostics.png)

#### <a name="azure-portal-option-2"></a>Azure Portal optie 2

Blader om **problemen op te sporen en op te lossen** in de VM-Blade en zoek naar prestatie problemen met de **virtuele machine**.

  ![Problemen met VM-prestaties oplossen](./media/troubleshoot-high-cpu-issues-azure-windows-vm/2-troubleshoot-vm-performance-issues.png)

Als u **problemen oplossen** selecteert, wordt het PerfInsights-installatie scherm geladen.

Als u installeren selecteert, biedt de installatie andere opties **voor** de verzameling.

  ![Prestatieanalyse](./media/troubleshoot-high-cpu-issues-azure-windows-vm/3-performance-analysis.png)

De genummerde opties in de scherm afbeelding hebben betrekking op de volgende opmerkingen:

1. Voor de optie **hoge CPU** selecteert u **prestatie analyse** of **Geavanceerd**.

2. Wanneer u hier symptomen toevoegt, worden deze toegevoegd aan het rapport, waarmee u informatie kunt delen met ondersteuning voor Azure.

3. Selecteer de duur van het verzamelen van gegevens. Voor de optie hoge CPU selecteert u ten minste 15 minuten of langer. In Azure Portal modus kunt u Maxi maal 15 minuten aan gegevens verzamelen. Voor langere Peri Oden van de verzameling moet u het programma uitvoeren als een uitvoerbaar bestand binnen de virtuele machine.

4. Als u door Azure-ondersteuning wordt gevraagd om deze gegevens te verzamelen, kunt u hier het ticket nummer toevoegen. Dit veld is optioneel.

5. Selecteer dit veld om de gebruiksrecht overeenkomst (EULA) te accepteren.

6. Selecteer dit veld als u van plan bent dit rapport beschikbaar te maken voor het ondersteunings team van Azure.

Het rapport wordt opgeslagen op een van de opslag accounts in uw abonnement. Het is beschikbaar om later weer te geven en te downloaden.

#### <a name="run-perfinsights-from-within-the-vm"></a>PerfInsights uitvoeren vanuit de VM

Deze methode kan worden gebruikt als u van plan bent om PerfInsights uit te voeren voor langere duur. Het [PerfInsights-artikel](./how-to-use-perfinsights.md#how-do-i-run-perfinsights) bevat een gedetailleerd overzicht van de verschillende opdrachten en vlaggen die nodig zijn om PerfInsights uit te voeren als uitvoerbaar bestand. Voor een hoog CPU-gebruik hebt u een van de volgende modi nodig:

- Geavanceerde scenario

  - `PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics`

- Scenario voor trage VM (prestaties)

  - `PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>`

De uitvoer van de opdracht bevindt zich in dezelfde map waarin u het uitvoer bare PerfInsights-bestand hebt opgeslagen.

#### <a name="what-to-look-for-in-the-report"></a>Wat u in het rapport moet zoeken

Nadat u het rapport hebt uitgevoerd, is de locatie van de inhoud afhankelijk van of het door de Azure Portal of als uitvoer bare bestand is uitgevoerd. Voor beide opties opent u de gegenereerde Logboekmap of Azure Portal downloadt u deze lokaal voor analyse.

### <a name="run-through-the-azure-portal"></a>De Azure Portal uitvoeren

  ![Prestatie diagnostiek met hoge impact](./media/troubleshoot-high-cpu-issues-azure-windows-vm/4-high-impact-performance-diagnostics.png)

  ![Rapport downloaden](./media/troubleshoot-high-cpu-issues-azure-windows-vm/5-download-report.png)

#### <a name="run-from-within-vm"></a>Uitvoeren vanuit in VM

De mapstructuur moet lijken op de volgende installatie kopieën:

  ![Uitvoer selecteren](./media/troubleshoot-high-cpu-issues-azure-windows-vm/6-select-output.png)

  ![Mapstructuur](./media/troubleshoot-high-cpu-issues-azure-windows-vm/7-folder-structure.png)

1. Aanvullende verzamelingen, zoals perfmon, Xperf, Netmon, SMB-logboeken, gebeurtenis logboeken, enzovoort, vindt u in de uitvoermap.

1. Het werkelijke rapport samen met analyse en aanbevelingen.

1. Voor zowel prestaties (VMslow) als Geavanceerd verzamelt het rapport **perfmon** -informatie voor de duur van de PerfInsights-uitvoering.

1. In de gebeurtenis logboeken ziet u een snelle weer gave van nuttige details op systeem niveau of proces fouten.

#### <a name="where-to-start"></a>Waar te beginnen

Open het PerfInsights-rapport. Op het tabblad **bevindingen** worden alle uitschieters in termen van Resource verbruik vastgelegd. Als er sprake is van een hoog CPU-gebruik, wordt het tabblad **resultaten** gecategoriseerd als een hoge impact of een gemiddelde impact.

  ![Resources van impact niveau](./media/troubleshoot-high-cpu-issues-azure-windows-vm/8-impact-level-resources.png)

Net als in het vorige voor beeld is PerfInsights 30 minuten uitgevoerd. Gedurende die tijd is het gemarkeerde proces de CPU aan de hogere zijde uitgeput. Als hetzelfde proces tijdens de verzamelings tijd werd uitgevoerd, is het niveau van de impact gewijzigd in **hoog**.

Als u de gebeurtenis **bevindingen** uitvouwt, ziet u enkele belang rijke gegevens. Op het tabblad worden de processen in aflopende volg orde weer gegeven, per **Gemiddeld CPU** -verbruik, en wordt aangegeven of het proces is gerelateerd aan het systeem, een micro soft-app (SQL, IIS) of een proces van derden.

#### <a name="more-details"></a>Meer informatie

Er is een toegewezen subtabblad onder **CPU** die kan worden gebruikt voor gedetailleerde patroon analyse, per kern geheugen of per proces.

Het tabblad **Top CPU-consumenten** heeft twee afzonderlijke gedeelten van belang en u kunt hier per processor statistieken weer geven. Het ontwerp van de toepassing is vaak Single-Threaded of pincodes voor één processor. In dit scenario worden een of enkele kern geheugens uitgevoerd om 100 procent, terwijl andere kernen op de verwachte niveaus worden uitgevoerd. Deze scenario's zijn complexere omdat de gemiddelde CPU op de server lijkt te worden uitgevoerd zoals verwacht, maar de processen die zijn vastgemaakt aan kernen met een hoog gebruik, zijn langzamer dan verwacht.

  ![hoog CPU-gebruik](./media/troubleshoot-high-cpu-issues-azure-windows-vm/9-high-cpu-usage.png)

De tweede sectie (net zo belang rijk) is de **meest voorkomende CPU-consumenten**. In deze sectie worden zowel de proces Details als het CPU-gebruiks patroon weer gegeven. De lijst wordt gesorteerd door hoge gemiddelde CPU-consumenten bovenaan te hebben.

  ![Gebruikers met lange en langdurige CPU-consumers](./media/troubleshoot-high-cpu-issues-azure-windows-vm/10-top-long-running-cpu-consumers.png)

Deze twee tabbladen moeten voldoende zijn om het pad in te stellen voor de volgende stappen voor probleem oplossing. Afhankelijk van het proces dat de hoge CPU-voor waarde inneemt, moet u de eerder gestelde vragen beantwoorden. Voor processen als SQL Server (sqlservr.exe) of IIS (w3wp.exe) is een specifiek inzoomen vereist voor query's of code wijzigingen die dit probleem veroorzaken. Voor systeem processen zoals WMI of Lsass.exe moet u een ander pad volgen.

Voor Azure VM-gerelateerde processen, zoals RDAgent-, OMS-en bewakings uitbreiding uitvoer bare bestanden, moet u mogelijk een nieuwe build of versie herstellen door hulp te krijgen van het ondersteunings team van Azure.

### <a name="perfmon"></a>Perfmon

**Perfmon** is een van de eerste hulp middelen voor het oplossen van problemen met een resource probleem op Windows Server. Er wordt geen duidelijk rapport met aanbevelingen of bevindingen verstrekt. In plaats daarvan moet de gebruiker de verzamelde gegevens verkennen en een specifiek filter gebruiken onder de verschillende item categorieën.

PerfInsights verzamelt perfmon als een extra logboek voor VMSlow en geavanceerde scenario's. Perfmon kan echter onafhankelijk worden verzameld en heeft de volgende extra voor delen:

- Het kan op afstand worden verzameld.

- Dit kan worden gepland via **taken**.

- Het kan worden verzameld voor langere duur of in de doorlopende modus met behulp van de functie roll-over.

Bekijk hetzelfde voor beeld dat wordt weer gegeven in PerfInsights om te zien hoe perfmon deze gegevens laat zien. De vereiste item categorieën zijn als volgt:

- Processor gegevens > percentage processor tijd > _Total

- >% ProcessorTime > alle instanties verwerken

#### <a name="where-to-start"></a>Waar te beginnen

De namen van de uitvoer bestanden van perfmon hebben een `.blg` uitbrei ding. U kunt deze bestanden onafhankelijk van elkaar of via PerfInsights verzamelen. Voor deze discussie gebruikt u de perfmon `.blg` die is opgenomen in PerfInsights-gegevens en die zijn verzameld volgens het vorige voor beeld.

Er zijn geen standaard rapporten die gereed zijn voor gebruik in Perfmon. Er zijn verschillende weer gaven die het grafiek type wijzigen, maar het proces filtering (of het werk dat nodig is om culprit processen te identificeren) is hand matig.

> [!NOTE]
> Het [hulp programma PAL](https://github.com/clinthuffman/PAL) kan `.blg` bestanden gebruiken en gedetailleerde rapporten genereren.

Selecteer de categorie **tellers toevoegen** om te beginnen.

1. Selecteer onder **beschik bare items** de teller **% ProcessorTime** in de categorie **processor informatie** .

1. Selecteer **_Total**, waarmee u de statistieken van alle gecombineerde kernen krijgt.

1. Selecteer **Toevoegen**. Het venster toont **% ProcessorTime** onder  **toegevoegde tellers**.

  ![Processor tijd toevoegen](./media/troubleshoot-high-cpu-issues-azure-windows-vm/11-add-processor-time.png)

Nadat de tellers zijn geladen, ziet u lijn trend grafieken in het tijds bestek van de verzameling. U kunt de tellers selecteren of wissen. Tot nu toe hebt u slechts één item toegevoegd.

  ![Instellingen voor prestatie meter](./media/troubleshoot-high-cpu-issues-azure-windows-vm/12-performance-monitor-1.png)

Elk prestatie meter item heeft **gemiddelde**, **minimale** en **maximale** waarden. Focus op zowel **gemiddelde** als **maximum** waarden, omdat de gemiddelde waarde kan variëren, afhankelijk van de duur van het verzamelen van gegevens. Als de activiteit met hoge CPU 10 minuten is waargenomen terwijl de totale verzameling 40 minuten was, zullen de gemiddelde waarden veel lager zijn.

In de vorige trend grafiek ziet u dat de **totale processor** ongeveer 15 minuten in de buurt van 80 procent is.

#### <a name="identify-the-process"></a>Het proces identificeren

We hebben vastgesteld dat de server een hoog CPU-verbruik voor een bepaalde periode had, maar dat het stuur programma nog niet is geïdentificeerd. In tegens telling tot het gebruik van PerfInsights moet u in dit geval hand matig zoeken naar het culprit-proces.

Voor deze taak moet u de **% ProcessorTime** -tellers die eerder zijn toegevoegd, wissen of verwijderen en vervolgens een nieuwe categorie toevoegen:

- >% ProcessorTime > alle instanties verwerken

Deze categorie laadt tellers voor alle processen die op dat moment worden uitgevoerd.

  ![Items toevoegen](./media/troubleshoot-high-cpu-issues-azure-windows-vm/13-add-counters.png)

Op een typische productie computer kunnen honderden of processen worden uitgevoerd. Daarom kan het enige tijd duren om alle items te wissen die een lage of vlakke trend grafiek hebben.

Als u dit proces wilt versnellen, gebruikt u de weer gave **histogram** en wijzigt u het weergave type van **regel** in **histogram**, waarmee u een staaf diagram krijgt. U vindt het eenvoudiger om de processen te kiezen die het hoge CPU-gebruik tijdens de verzamelings tijd ervaren.

Omdat er altijd een balk voor **totaal** wordt weer gegeven, kunt u zich richten op balken die een hoge mate van uitputting hebben. U kunt de andere balken verwijderen om de weer gave op te schonen. Vervolgens gaat u terug naar de **regel** weergave.

  ![Indica toren prestatie meter](./media/troubleshoot-high-cpu-issues-azure-windows-vm/14-performance-monitor-2.png)

Het is nu eenvoudiger om het culprit-proces te ondervangen. De **maximum** -en **minimum** waarden zijn standaard veelvouden van het aantal kernen op de server of threads van het proces.

  ![Resultaten van prestatie meter](./media/troubleshoot-high-cpu-issues-azure-windows-vm/15-performance-monitor-3.png)

De lijst met beschik bare hulpprogram ma's eindigt niet op PerfInsights voor Perfmon. U hebt toegang tot andere hulpprogram ma's, zoals **ProcessMonitor** (ProcMon) of **Xperf**. Er zijn veel hulpprogram ma's van derden beschikbaar die u kunt gebruiken als dat nodig is.

### <a name="azure-monitoring-tools"></a>Bewakingsprogramma's in Azure

Azure-Vm's hebben betrouw bare metrische gegevens, zoals CPU, netwerk-I/O en I/O-bytes. Voor geavanceerde metrische gegevens, zoals Azure Monitor, hoeft u slechts enkele selecties te maken voor het configureren en gebruiken van een opslag account dat u opgeeft.

#### <a name="basic-default-counters"></a>Basis tellers (standaard)

  ![Grafiek gegevens](./media/troubleshoot-high-cpu-issues-azure-windows-vm/16-chart-metrics.png)

#### <a name="enabling-azure-monitor"></a>Azure Monitor inschakelen

Nadat u Azure Monitor metrische gegevens hebt ingeschakeld, installeert de software een uitbrei ding op de virtuele machine en begint vervolgens met het verzamelen van nauw keurige meet waarden, waaronder perfmon-tellers.

  ![Opslagaccount voor diagnose](./media/troubleshoot-high-cpu-issues-azure-windows-vm/17-diagnostics-storage-account.png)

De categorieën **basis** tellers worden **standaard** ingesteld. U kunt echter ook een **aangepaste** verzameling instellen.

  ![Prestatiemeteritems](./media/troubleshoot-high-cpu-issues-azure-windows-vm/18-performance-counters.png)

Nadat de instellingen zijn ingeschakeld, kunt u deze **gast** items weer geven in het gedeelte **metrische gegevens** . U kunt ook **waarschuwingen** instellen (inclusief e-mail berichten) als de metrische gegevens een bepaalde drempel waarde bereiken.

  ![Metrische naam ruimte](./media/troubleshoot-high-cpu-issues-azure-windows-vm/19-metrics-namespace.png)

Zie [virtuele machines van Azure bewaken met Azure monitor](../../azure-monitor/vm/monitor-vm-azure.md)voor meer informatie over het gebruik van Azure monitor voor het beheren van Azure-vm's.

### <a name="reactive-troubleshooting"></a>Reactief probleem oplossen

Als het probleem al is opgetreden, moet u ontdekken wat de oorzaak van het probleem met hoge CPU in de eerste locatie heeft veroorzaakt. De heractieve koers kan lastig zijn. De modus voor het verzamelen van gegevens is niet zo handig, omdat het probleem al heeft plaatsgevonden.

Als dit probleem een eenmalige gebeurtenis is, kan het lastig zijn om te bepalen welke app het heeft veroorzaakt. Als de virtuele machine van Azure is geconfigureerd voor het gebruik van OMS of andere tracering voor diagnostische gegevens, kunt u nog steeds inzichten krijgen over wat het probleem heeft veroorzaakt.

Als u met een herhalend patroon werkt, verzamelt u de gegevens op het moment dat het probleem waarschijnlijk zal optreden volgende.

PerfInsights is nog niet voorzien van een functie voor het uitvoeren van een **taak** . Perfmon kan echter worden uitgevoerd en gepland via de opdracht regel.

### <a name="logman-command"></a>Logman-opdracht

De opdracht **logman create counter** wordt gebruikt om de perfmon-verzameling uit te voeren via de opdracht regel, om deze te plannen via **taak beheer**, of om deze op afstand uit te voeren.

Voor **beeld** (inclusief modus voor externe verzameling)

`Logman create counter LOGNAME -u DOMAIN\USERNAME * -f bincirc -v mmddhhmm -max 300 -c "\\SERVERNAME\LogicalDisk(*)\*" "\\SERVERNAME\Memory\*" "\\SERVERNAME\Network Interface(*)\*" "\\SERVERNAME\Paging File(*)\*" "\\SERVERNAME\PhysicalDisk(*)\*" "\\SERVERNAME\Process(*)\*" "\\SERVERNAME\Redirector\*" "\\SERVERNAME\Server\*" "\\SERVERNAME\System\*" "\\SERVERNAME\Terminal Services\*" "\\SERVERNAME\Processor(*)\*" "\\SERVERNAME\Cache\*" -si 00:01:00`

Logman.exe kan ook worden gestart vanaf een computer met een virtuele machine van Azure in hetzelfde VNET.

Zie [logman create counter](/windows-server/administration/windows-commands/logman-create-counter)voor meer informatie over deze para meters.

Nadat de prestatie gegevens zijn verzameld terwijl het probleem zich voordoet, zijn de resterende stappen voor het analyseren van de gegevens hetzelfde als hierboven beschreven.

## <a name="conclusion"></a>Conclusie

Voor een prestatie probleem is het beter om het probleem op te lossen. De opties op de verschillende VM-Sku's en verschillende schijf opslag opties moeten worden geëvalueerd door de focus op de werk belasting van de productie te houden. Het proces van het testen van oplossingen op verschillende Vm's kan u helpen om de beste beslissing te nemen.

Wanneer de gebruikers bewerkingen en hoeveelheid gegevens variëren, moet u altijd een buffer gebruiken in de functies computing, netwerken en I/O van de virtuele machine. Een plotselinge wijziging in de werk belasting heeft nu geen effect.

Als u verwacht dat de werk belasting snel toeneemt, gaat u naar een hogere SKU met meer reken kracht. Als de werk belasting reken intensief is, kiest u de VM-Sku's.