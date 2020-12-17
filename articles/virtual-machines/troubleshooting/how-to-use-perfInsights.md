---
title: PerfInsights gebruiken in Microsoft Azure | Microsoft Docs
description: Meer informatie over het gebruik van PerfInsights voor het oplossen van problemen met Windows VM-prestaties.
services: virtual-machines-windows
documentationcenter: ''
author: anandhms
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 9e298bf39446024f384b9af142fe3000e936bb6d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656849"
---
# <a name="how-to-use-perfinsights-in-azure"></a>PerfInsights gebruiken in azure

[PerfInsights](https://aka.ms/perfinsightsdownload) is een hulp programma voor zelf ondersteuning waarmee de diagnostische gegevens worden verzameld en geanalyseerd, en biedt een rapport voor het oplossen van prestatie problemen met virtuele Windows-machines in Azure. PerfInsights kan worden uitgevoerd op virtuele machines als zelfstandig hulp programma, rechtstreeks vanuit de portal door gebruik te maken van [prestatie diagnostiek voor Azure virtual machines](performance-diagnostics.md)of door de [VM-extensie Azure performance Diagnostics](performance-diagnostics-vm-extension.md)te installeren.

Als u prestatie problemen ondervindt met virtuele machines, moet u dit hulp programma uitvoeren voordat u contact opneemt met de ondersteuning.

## <a name="supported-troubleshooting-scenarios"></a>Ondersteunde scenario's voor probleem oplossing

PerfInsights kunnen diverse soorten informatie verzamelen en analyseren. In de volgende secties worden algemene scenario's besproken.

### <a name="quick-performance-analysis"></a>Analyse van snelle prestaties

In dit scenario worden de schijf configuratie en andere belang rijke informatie verzameld, waaronder:

-   Gebeurtenislogboeken

-   Netwerk status voor alle binnenkomende en uitgaande verbindingen

-   Configuratie-instellingen voor netwerk en firewall

-   Taak lijst voor alle toepassingen die momenteel worden uitgevoerd op het systeem

-   Configuratie-instellingen van Microsoft SQL Server-Data Base (als de virtuele machine is geïdentificeerd als een server waarop SQL Server wordt uitgevoerd)

-   Betrouwbaarheids tellers voor opslag

-   Belang rijke hotfixes voor Windows

-   Geïnstalleerde filter Stuur Programma's

Dit is een passieve verzameling informatie die niet van invloed is op het systeem. 

>[!Note]
>Dit scenario wordt automatisch opgenomen in elk van de volgende scenario's:

### <a name="benchmarking"></a>Benchmarking

In dit scenario wordt de [Diskspd](https://github.com/Microsoft/diskspd) Bench Mark-test (IOPS en MBPS) uitgevoerd voor alle stations die zijn gekoppeld aan de virtuele machine. 

> [!Note]
> Dit scenario kan van invloed zijn op het systeem en mag niet worden uitgevoerd op een live productie systeem. Voer, indien nodig, dit scenario uit in een speciaal onderhouds venster om eventuele problemen te voor komen. Een verhoogde werk belasting die wordt veroorzaakt door een tracering of Bench Mark-test kan een nadelige invloed hebben op de prestaties van de virtuele machine.
>

### <a name="performance-analysis"></a>Analyse van prestaties

In dit scenario wordt een [prestatie meter item](/windows/win32/perfctrs/performance-counters-portal) tracering uitgevoerd op basis van de prestatie meter items die zijn opgegeven in de RuleEngineConfig.jsvoor het bestand. Als de virtuele machine wordt geïdentificeerd als een server waarop SQL Server wordt uitgevoerd, wordt een prestatie meter item tracering uitgevoerd. Dit doet u door gebruik te maken van de prestatie meter items die worden gevonden in de RuleEngineConfig.jsin het bestand. Dit scenario omvat ook gegevens over prestatie diagnostiek.

### <a name="azure-files-analysis"></a>Azure Files analyse

In dit scenario wordt een speciale opname voor prestatie meter items uitgevoerd met een netwerk tracering. De vastleg ging bevat alle items van het Server Message Block (SMB)-client shares. Hieronder volgen enkele belang rijke prestatie meter items voor SMB-client delen die deel uitmaken van de vastleg ging:

| **Type**     | **Teller SMB-client shares** |
|--------------|-------------------------------|
| IOPS         | Gegevens aanvragen per seconde             |
|              | Lees aanvragen per seconde             |
|              | Schrijf aanvragen per seconde            |
| Latentie      | Gemiddeld aantal seconden/gegevens aanvraag         |
|              | Gemiddeld aantal seconden per Lees bewerking                 |
|              | Gemiddeld aantal seconden per schrijf bewerking                |
| I/o-grootte      | Gemiddeld aantal bytes/gegevens aanvraag       |
|              | Gem. bytes/Lees bewerking               |
|              | Gemiddeld aantal bytes/schrijven              |
| Doorvoer   | Gegevens bytes per seconde                |
|              | Gelezen bytes/sec                |
|              | Geschreven bytes/sec               |
| Wachtrijlengte | Gem. wachtrij lengte Lees bewerking        |
|              | Gem. wachtrij lengte schrijf bewerking       |
|              | Gem. lengte van gegevens wachtrij        |

### <a name="advanced-performance-analysis"></a>Geavanceerde prestatie analyse

Wanneer u een geavanceerde prestatie analyse uitvoert, selecteert u traceringen die parallel moeten worden uitgevoerd. Als u wilt, kunt u deze allemaal uitvoeren (prestatie meter item, Xperf, netwerk en StorPort).  

> [!Note]
> Dit scenario kan van invloed zijn op het systeem en mag niet worden uitgevoerd op een live productie systeem. Voer, indien nodig, dit scenario uit in een speciaal onderhouds venster om eventuele problemen te voor komen. Een verhoogde werk belasting die wordt veroorzaakt door een tracering of Bench Mark-test kan een nadelige invloed hebben op de prestaties van de virtuele machine.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Wat voor soort informatie wordt verzameld door PerfInsights?

Informatie over Windows VM, schijven of opslag groepen configuratie, prestatie meter items, logboeken en verschillende traceringen worden verzameld. Dit is afhankelijk van het prestatie scenario dat u gebruikt. De volgende lijst bevat de details:

| Verzamelde gegevens | Analyse van snelle prestaties | Benchmarking | Analyse van prestaties | Azure Files analyse | Geavanceerde prestatie analyse |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|
| Informatie uit gebeurtenis logboeken       | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Systeemgegevens                | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Volume toewijzing                        | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Schijf toewijzing                          | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Actieve taken                     | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Betrouwbaarheids tellers voor opslag      | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Opslag gegevens               | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Fsutil-uitvoer                     | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Stuur programma-informatie filteren                | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Netstat-uitvoer                    | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Netwerkconfiguratie             | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Firewallconfiguratie            | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| SQL Server-configuratie          | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Traceringen van prestatie diagnostiek *  | Ja                        | Ja                                | Ja                      | Ja                  | Ja                  |
| Tracering van prestatie meter items * *      |                            |                                    | Ja                      |                      | Ja                  |
| Tracering SMB-teller * *              |                            |                                    |                          | Ja                  |                      |
| Teller tracering SQL Server * *       |                            |                                    | Ja                      |                      | Ja                  |
| Xperf tracering                       |                            |                                    |                          |                      | Ja                  |
| StorPort-tracering                    |                            |                                    |                          |                      | Ja                  |
| Netwerk tracering                     |                            |                                    |                          | Ja                  | Ja                  |
| Diskspd-Bench Mark-tracering * * _       |                            | Ja                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-_"></a>Prestatie diagnostiek tracering (_)

Voert een op regels gebaseerde engine op de achtergrond uit om gegevens te verzamelen en doorlopende prestatie problemen vast te stellen. De volgende regels worden momenteel ondersteund:

- HighCpuUsage-regel: detecteert hoge CPU-gebruiks perioden, en toont de belangrijkste consumers van het CPU-gebruik tijdens deze peri Oden.
- HighDiskUsage-regel: detecteert hoge gebruiks tijden voor schijven op fysieke schijven en toont de meest voorkomende consumenten gebruikers tijdens deze peri Oden.
- HighResolutionDiskMetric-regel: Hiermee worden de metrische gegevens voor IOPS, door Voer en I/O-latentie per 50 milliseconden voor elke fysieke schijf weer gegeven. Hiermee kunt u snel de schijfruimte beperkings punten identificeren.
- HighMemoryUsage-regel: detecteert gebruiks perioden in hoog geheugen en toont de prestaties van het hoogste geheugen gebruik tijdens die peri Oden.

> [!NOTE] 
> Op dit moment worden Windows-versies met de .NET Framework 4,5 of hogere versies ondersteund.

### <a name="performance-counter-trace-"></a>Prestatie meter item tracering (* *)

Verzamelt de volgende prestatie meter items:

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk en \LogicalDisk
- \Cache\Dirty-pagina's, \Cache\Lazy schrijf bewerkingen per seconde, \Server\Pool niet-wisselbaar, mislukte fouten en \Server\Pool met pagina fouten
- Geselecteerde items onder \Network interface, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network-adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \Network QoS Policy\Packets, \Per processor netwerk interface kaart activiteit en \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>Voor SQL Server exemplaren
- \SQL-server: buffer beheer, \SQLServer: resource groep statistieken en \SQLServer: SQL-statistieken \
- \SQLServer: Locks, \SQLServer: General, Statistics
- \SQLServer: toegangs methoden

#### <a name="for-azure-files"></a>Voor Azure Files
\SMB-client shares

### <a name="diskspd-benchmark-trace-_"></a>Diskspd Bench Mark-tracering (* * _)
Diskspd I/O-workload testen (besturingssysteem schijf [schrijven] en groeps stations [lezen/schrijven])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Het PerfInsights-hulp programma uitvoeren op uw VM

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Wat moet ik weten voordat ik het hulp programma uitvoer? 

#### <a name="tool-requirements"></a>Hulp programma vereisten

-  Dit hulp programma moet worden uitgevoerd op de VM met het prestatie probleem. 

-  De volgende besturings systemen worden ondersteund: _ Windows Server 2019
   * Windows Server 2016
   * Windows Server 2012 R2
   * Windows Server 2012
   * Windows Server 2008 R2
   * Windows 10
   * Windows 8.1
   * Windows 8

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Mogelijke problemen wanneer u het hulp programma uitvoert op productie-Vm's

-  Het hulp programma kan de prestaties van de virtuele machine nadelig beïnvloeden voor het vergelijkings scenario of het scenario met geavanceerde prestaties dat is geconfigureerd voor het gebruik van Xperf of Diskspd. Deze scenario's mogen niet worden uitgevoerd in een live productie omgeving.

-  Zorg ervoor dat de I/O-werk belasting niet wordt veroorzaakt door een andere achtergrond activiteit voor het benchmarking-scenario of het scenario voor geavanceerde prestaties dat is geconfigureerd voor het gebruik van Diskspd.

-  Het hulp programma maakt standaard gebruik van het tijdelijke opslag station voor het verzamelen van gegevens. Als tracering gedurende langere tijd ingeschakeld blijft, kan de hoeveelheid verzamelde gegevens relevant zijn. Dit kan de beschik baarheid van ruimte op de tijdelijke schijf verminderen en kan daarom van invloed zijn op elke toepassing die afhankelijk is van dit station.

### <a name="how-do-i-run-perfinsights"></a>Hoe kan ik PerfInsights uitvoeren? 

U kunt PerfInsights op een virtuele machine uitvoeren door de [VM-extensie Azure-prestatie diagnostiek](performance-diagnostics-vm-extension.md)te installeren. U kunt deze ook uitvoeren als een zelfstandig hulp programma. 

**PerfInsights installeren en uitvoeren vanuit de Azure Portal**

Zie voor meer informatie over deze optie [Azure performance diagnostische VM-extensie installeren](performance-diagnostics-vm-extension.md#install-the-extension).  

**PerfInsights uitvoeren in de zelfstandige modus**

Voer de volgende stappen uit om het PerfInsights-hulp programma uit te voeren:


1. Down load [PerfInsights.zip](https://aka.ms/perfinsightsdownload).

2. Deblokkeren van het PerfInsights.zip-bestand. U doet dit door met de rechter muisknop op het PerfInsights.zip bestand te klikken en **Eigenschappen** te selecteren. Selecteer op het tabblad **Algemeen** de optie **blok kering opheffen** en selecteer vervolgens **OK**. Dit zorgt ervoor dat het hulp programma wordt uitgevoerd zonder dat er extra beveiligings aanwijzingen zijn.  

    ![Scherm opname van PerfInsights-eigenschappen, waarbij de blok kering is gemarkeerd](media/how-to-use-perfInsights/pi-unlock-file.png)

3.  Vouw het gecomprimeerde PerfInsights.zip bestand uit in het tijdelijke station (standaard is dit de D-station). 

4.  Open Windows-opdracht prompt als beheerder en voer PerfInsights.exe uit om de beschik bare commandline-para meters weer te geven.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Scherm opname van de uitvoer van PerfInsights commandline](media/how-to-use-perfInsights/pi-commandline.png)
    
    De basis syntaxis voor het uitvoeren van PerfInsights-scenario's is:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    U kunt het onderstaande voor beeld gebruiken om een scenario voor prestatie analyse uit te voeren gedurende 5 minuten:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    U kunt het volgende voor beeld gebruiken om het geavanceerde scenario met traceringen voor Xperf en prestatie meter items gedurende 5 minuten uit te voeren:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    U kunt het onderstaande voor beeld gebruiken om een scenario voor prestatie analyse uit te voeren gedurende 5 minuten en het zip-bestand te uploaden naar het opslag account:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    U kunt alle beschik bare scenario's en opties opzoeken met behulp van de opdracht **/List** :
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Voordat u een scenario uitvoert, wordt de gebruiker door PerfInsights gevraagd om de diagnostische gegevens te delen en de gebruiksrecht overeenkomst te accepteren. Gebruik de optie **/AcceptDisclaimerAndShareDiagnostics** om deze prompts over te slaan. 
    >
    >Als u een actief ondersteunings ticket hebt met micro soft en PerfInsights wilt uitvoeren volgens de aanvraag van de ondersteunings technicus waarmee u werkt, moet u ervoor zorgen dat u het nummer van het ondersteunings ticket opgeeft met de optie **/SR** .
    >
    >PerfInsights probeert standaard zichzelf bij te werken naar de nieuwste versie, indien beschikbaar. Gebruik de para meter **/SkipAutoUpdate** of **/sau** om de automatische update over te slaan.  
    >
    >Als de duur schakelaar **/d** niet is opgegeven, wordt u door PerfInsights gevraagd het probleem op te lossen tijdens het uitvoeren van vmslow, Azure files en geavanceerde scenario's. 

Wanneer de traceringen of bewerkingen zijn voltooid, wordt een nieuw bestand weer gegeven in dezelfde map als PerfInsights. De naam van het bestand is **PerformanceDiagnostics \_ jjjj-mm-dd \_hh-mm-ss-fff.zip.** U kunt dit bestand naar de ondersteunings agent verzenden voor analyse of het rapport openen in het zip-bestand om de bevindingen en aanbevelingen te bekijken.

## <a name="review-the-diagnostics-report"></a>Het diagnostische rapport controleren

In het bestand **PerformanceDiagnostics \_ jjjj-mm-dd \_hh-mm-ss-fff.zip** kunt u een HTML-rapport vinden waarin de resultaten van PerfInsights worden beschreven. Als u het rapport wilt bekijken, vouwt u het bestand **PerformanceDiagnostics \_ jjjj-mm-dd \_hh-mm-ss-fff.zip** uit en opent u vervolgens het bestand **PerfInsights Report.html** .

Selecteer het tabblad **bevindingen** .

![Scherm afbeelding van het tabblad Overzicht van het PerfInsights-rapport. ](media/how-to-use-perfInsights/pi-finding-tab.png)
 ![ Scherm afbeelding van het tabblad opslag van het PerfInsights-rapport.](media/how-to-use-perfInsights/pi-findings.png)

> [!NOTE] 
> Bevindingen die zijn gecategoriseerd als hoog, zijn bekende problemen die prestatie problemen kunnen veroorzaken. Bevindingen die zijn gecategoriseerd als medium, vertegenwoordigen niet-optimale configuraties die niet noodzakelijkerwijs prestatie problemen veroorzaken. Bevindingen die zijn gecategoriseerd als laag, zijn alleen informatieve instructies.

Bekijk de aanbevelingen en koppelingen voor alle belang rijke en middel grote bevindingen. Meer informatie over hoe deze prestaties kunnen beïnvloeden en ook over de aanbevolen procedures voor de prestaties geoptimaliseerde configuraties.

### <a name="storage-tab"></a>Tabblad opslag

In het gedeelte **bevindingen** worden verschillende bevindingen en aanbevelingen met betrekking tot opslag weer gegeven.

In de secties **schijf toewijzing** en **volume toewijzing** wordt beschreven hoe logische volumes en fysieke schijven aan elkaar zijn gerelateerd.

In het perspectief fysieke schijf (schijf toewijzing) toont de tabel alle logische volumes die op de schijf worden uitgevoerd. In het volgende voor beeld voert **PhysicalDrive2** twee logische volumes uit die zijn gemaakt op meerdere partities (J en H):

![Scherm afbeelding van het tabblad schijf](media/how-to-use-perfInsights/pi-disk-tab.png)

In het perspectief volume (volume toewijzing) tonen de tabellen alle fysieke schijven onder elk logisch volume. U ziet dat voor RAID/dynamische schijven een logisch volume op meerdere fysieke schijven kan worden uitgevoerd. In het volgende voor beeld is *C: \\ koppelen* een koppel punt geconfigureerd als *SpannedDisk* op de fysieke schijven 2 en 3:

![Scherm afbeelding van het tabblad volume](media/how-to-use-perfInsights/pi-volume-tab.png)

### <a name="sql-tab"></a>Tabblad SQL

Als de doel-VM een SQL Server instanties host, ziet u een extra tabblad in het rapport met de naam **SQL**:

![Scherm afbeelding van het tabblad SQL](media/how-to-use-perfInsights/pi-sql-tab.png)

Deze sectie bevat een tabblad met **resultaten** en aanvullende tabbladen voor elk van de SQL Server exemplaren die worden gehost op de VM.

Het tabblad **bevindingen** bevat een lijst met alle SQL-gerelateerde prestatie problemen die zijn gevonden, samen met de aanbevelingen.

In het volgende voor beeld wordt **PhysicalDrive0** (uitvoeren van station C) weer gegeven. Dit komt doordat de **modeldev** -en **Modellog** -bestanden zich bevinden op het station C en ze zijn van verschillende typen (zoals gegevens bestand en transactie logboek).

![Scherm opname van logboek gegevens](media/how-to-use-perfInsights/pi-log-info.png)

De tabbladen voor specifieke exemplaren van SQL Server bevatten een algemene sectie waarin basis informatie over het geselecteerde exemplaar wordt weer gegeven. De tabbladen bevatten ook extra secties voor geavanceerde informatie, waaronder instellingen, configuraties en gebruikers opties.

### <a name="diagnostic-tab"></a>Tabblad Diagnostische gegevens
Het tabblad **Diagnostische** gegevens bevat informatie over de belangrijkste CPU-, schijf-en geheugen gebruikers op de computer voor de duur van de uitvoering van PerfInsights. U vindt ook informatie over kritieke patches die mogelijk ontbreken in het systeem, de taken lijst en belang rijke systeem gebeurtenissen. 

## <a name="references-to-the-external-tools-used"></a>Verwijzingen naar de externe hulpprogram ma's die worden gebruikt

### <a name="diskspd"></a>Diskspd

Diskspd is een test programma voor het laden van opslag en prestaties van micro soft. Zie [Diskspd](https://github.com/Microsoft/diskspd)voor meer informatie.

### <a name="xperf"></a>Xperf

Xperf is een opdracht regel programma voor het vastleggen van traceringen vanuit de Windows Performance Toolkit. Zie [Windows Performance Toolkit – Xperf](/archive/blogs/ntdebugging/windows-performance-toolkit-xperf)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

U kunt Diagnostische logboeken en rapporten uploaden naar Microsoft Ondersteuning voor verdere beoordeling. Ondersteuning kan aanvragen dat u de uitvoer verzendt die door PerfInsights wordt gegenereerd om te helpen bij het oplossen van het probleem.

Op de volgende scherm afbeelding wordt een bericht weer gegeven dat vergelijkbaar is met wat u kunt ontvangen:

![Scherm opname van het voorbeeld bericht van Microsoft Ondersteuning](media/how-to-use-perfInsights/pi-support-email.png)

Volg de instructies in het bericht voor toegang tot de werk ruimte bestands overdracht. Voor extra beveiliging moet u uw wacht woord wijzigen bij het eerste gebruik.

Nadat u zich hebt aangemeld, vindt u een dialoog venster voor het uploaden van de **PerformanceDiagnostics \_ jjjj-mm-dd \_hh-mm-ss-fff.zip** bestand dat door PerfInsights is verzameld.
