---
title: Veelgestelde vragen over SMB-prestaties voor Azure NetApp Files| Microsoft Docs
description: Antwoorden op veelgestelde vragen over SMB-prestaties voor Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: 1ec58b056bd610773500c8ace1fb12d268b980e0
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726714"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Veelgestelde vragen over SMB-prestaties voor Azure NetApp Files

In dit artikel vindt u antwoorden op veelgestelde vragen over de best practices voor SMB-prestaties voor Azure NetApp Files.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Is SMB meerdere kanalen ingeschakeld in SMB-shares? 

Ja, SMB meerdere kanalen is standaard ingeschakeld, een wijziging die begin januari 2020 is ingesteld. Op alle SMB-shares die voorafgaat aan bestaande SMB-volumes, is de functie ingeschakeld en op alle nieuw gemaakte volumes is de functie ook ingeschakeld op het moment dat deze wordt gemaakt. 

Een SMB-verbinding die tot stand is gebracht voordat de functie wordt ingeschakeld, moet opnieuw worden ingesteld om te profiteren van de SMB-functionaliteit voor meerdere kanalen. Als u de SMB-share opnieuw wilt instellen, kunt u de verbinding met de SMB-share verbreken en opnieuw verbinden.

## <a name="is-rss-supported"></a>Wordt RSS ondersteund?

Ja, Azure NetApp Files ondersteuning voor RSS (Receive Side Scaling).

Als SMB meerdere kanalen is ingeschakeld, brengt een SMB3-client meerdere TCP-verbindingen tot stand met de Azure NetApp Files SMB-server via een netwerkinterfacekaart (NIC) die geschikt is voor één RSS. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Welke Windows-versies ondersteunen SMB meerdere kanalen?

Windows heeft SMB meerdere kanalen ondersteund sinds Windows 2012 om de beste prestaties mogelijk te maken.  Zie [SMB meerdere kanalen implementeren en](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) De [basisbeginselen van SMB meerdere kanalen voor](/archive/blogs/josebda/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0) meer informatie. 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Ondersteunt mijn virtuele Azure-machine RSS?

Als u wilt zien of de NIC's van uw virtuele Azure-machine RSS ondersteunen, moet u de opdracht als `Get-SmbClientNetworkInterface` volgt uitvoeren en het veld `RSS Capable` controleren: 

![Schermopname van RSS-uitvoer voor virtuele Azure-machine.](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Biedt Azure NetApp Files ondersteuning voor SMB Direct?

Nee, Azure NetApp Files biedt geen ondersteuning voor SMB Direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Wat is het voordeel van SMB meerdere kanalen? 

Met de functie SMB meerdere kanalen kan een SMB3-client een groep verbindingen tot stand brengen via één netwerkinterfacekaart (NIC) of meerdere NIC's en deze gebruiken om aanvragen voor één SMB-sessie te verzenden. Daarentegen vereisen SMB1 en SMB2 dat de client één verbinding tot stand kan brengen en al het SMB-verkeer voor een bepaalde sessie via die verbinding moet verzenden. Deze ene verbinding beperkt de algehele protocolprestaties die kunnen worden bereikt vanuit één client.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Moet ik meerdere NIC's op mijn client configureren voor SMB?

Nee. De SMB-client komt overeen met het aantal NIC's dat door de SMB-server wordt geretourneerd.  Elk opslagvolume is toegankelijk vanaf één en slechts één opslag eindpunt.  Dit betekent dat er slechts één NIC wordt gebruikt voor een bepaalde SMB-relatie.  

Zoals de uitvoer hieronder `Get-SmbClientNetworkInterace` laat zien, heeft de virtuele machine 2 netwerkinterfaces: 15 en 12.  Zoals wordt weergegeven in de volgende opdracht , wordt, ondanks dat er twee RSS-NIC's zijn, alleen interface 12 gebruikt in verbinding met de `Get-SmbMultichannelConnection` SMB-share; interface 15 wordt niet gebruikt.

![Screeshot met uitvoer voor RSS-NIC's.](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Wordt NIC-team ondersteund in Azure?

NIC-team wordt niet ondersteund in Azure. Hoewel meerdere netwerkinterfaces worden ondersteund op virtuele Azure-machines, vertegenwoordigen ze een logische in plaats van een fysieke constructie. Als zodanig bieden ze geen fouttolerantie.  Ook wordt de bandbreedte die beschikbaar is voor een virtuele Azure-machine berekend voor de machine zelf en niet voor een afzonderlijke netwerkinterface.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Hoe ziet de prestaties eruit voor SMB meerdere kanalen?

In de volgende tests en grafieken ziet u de kracht van SMB meerdere kanalen op workloads met één exemplaar.

### <a name="random-io"></a>Willekeurige I/O  

Omdat SMB meerdere kanalen op de client is uitgeschakeld, zijn er vier lees- en schrijftests van KiB uitgevoerd met behulp van FIO en een werkset van 40 GiB.  De SMB-share is losgekoppeld tussen elke test, met verhogingen van het aantal SMB-clientverbindingen per instellingen voor de RSS-netwerkinterface `1` van , , , , `4` `8` `16` `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` . De tests laten zien dat de standaardinstelling van voldoende is voor `4` I/O-intensieve werkbelastingen; oplopend tot `8` en had een `16` verwaarloosbaar effect. 

De opdracht heeft aangetoond dat er extra verbindingen tot stand zijn `netstat -na | findstr 445` gebracht met stappen van naar en naar `1` `4` `8` `16` .  Tijdens elke test zijn vier CPU-kernen volledig gebruikt voor SMB, zoals bevestigd door de perfmonstatistiek `Per Processor Network Activity Cycles` (niet opgenomen in dit artikel).)

![Grafiek met een willekeurige I/O-vergelijking van SMB meerdere kanalen.](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

De virtuele Azure-machine heeft geen invloed op I/O-limieten voor SMB-opslag (noch NFS).  Zoals u in de volgende grafiek kunt zien, heeft het exemplaartype D32ds een beperkte snelheid van 308.000 voor opslag-IOPS in cache en 51.200 voor niet-gecachede opslag-IOPS.  De bovenstaande grafiek toont echter aanzienlijk meer I/O via SMB.

![Grafiek met een willekeurige I/O-vergelijkingstest.](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>Sequentiële IO 

Tests die vergelijkbaar zijn met de eerder beschreven willekeurige I/O-tests zijn uitgevoerd met sequentiële I/O-tests van 64 KiB. Hoewel de toename van het aantal clientverbindingen per RSS-netwerkinterface groter dan 4' geen merkbare invloed had op willekeurige I/O, geldt hetzelfde niet voor sequentiële I/O. Zoals in de volgende grafiek wordt weergegeven, is elke toename gekoppeld aan een overeenkomstige toename van de leesdoorvoer. De schrijfdoorvoer blijft gelijk vanwege de netwerkbandbreedtebeperkingen die Azure voor elk type of elke instantiegrootte heeft. 

![Grafiek met vergelijking van doorvoertests.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure plaatst netwerkfrequentielimieten voor elk type of elke grootte van de virtuele machine. De snelheidslimiet wordt alleen voor uitgaand verkeer opgelegd. Het aantal NIC's op een virtuele machine heeft geen invloed op de totale hoeveelheid bandbreedte die beschikbaar is voor de machine.  Het exemplaartype D32ds heeft bijvoorbeeld een netwerklimiet van 16.000 Mbps (2000 MiB/s).  Zoals de bovenstaande sequentiële grafiek laat zien, is de limiet van invloed op het uitgaande verkeer (schrijfingen), maar niet op meerdere kanalen.

![Grafiek met een sequentiële I/O-vergelijkingstest.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="what-performance-is-expected-with-a-single-instance-with-a-1-tb-dataset"></a>Welke prestaties worden verwacht met één exemplaar met een gegevensset van 1 TB?

De volgende twee grafieken tonen de prestaties van één cloudvolume op Ultra-serviceniveau van 50 TB met een gegevensset van 1 TB en met SMB meerdere kanalen van 4 om meer gedetailleerd inzicht te bieden in workloads met combinatie van lezen/schrijven. Er is een optimale IODepth van 16 gebruikt en FIO-parameters (Flexible IO) zijn gebruikt om ervoor te zorgen dat de netwerkbandbreedte volledig wordt gebruikt ( `numjobs=16` ).

In het volgende diagram ziet u de resultaten voor 4.000 willekeurige I/O's, met één VM-exemplaar en een combinatie van lezen/schrijven met intervallen van 10%:

![Grafiek met windows 2019 standaard _D32ds_v4 4K willekeurige I/O-test.](../media/azure-netapp-files/smb-performance-standard-4k-random-io.png)

In de volgende grafiek ziet u de resultaten voor sequentiële I/O:

![Grafiek met windows 2019 standard _D32ds_v4 64K sequentiële doorvoer.](../media/azure-netapp-files/smb-performance-standard-64k-throughput.png)

## <a name="what-performance-is-expected-when-scaling-out-using-5-vms-with-a-1-tb-dataset"></a>Welke prestaties worden verwacht bij het uitschalen met vijf VM's met een gegevensset van 1 TB?

Deze tests met vijf VM's gebruiken dezelfde testomgeving als de ene VM, waarbij elk proces naar een eigen bestand schrijft.

In het volgende diagram ziet u de resultaten voor willekeurige I/O:

![Grafiek met windows 2019 standard _D32ds_v4 4K 5-instance randio IO-test.](../media/azure-netapp-files/smb-performance-standard-4k-random-io-5-instances.png)

In de volgende grafiek ziet u de resultaten voor sequentiële I/O:

![Grafiek met windows 2019 standard _D32ds_v4 64K 5-instance sequentiële doorvoer.](../media/azure-netapp-files/smb-performance-standard-64k-throughput-5-instances.png)

## <a name="how-do-you-monitor-hyper-v-ethernet-adapters-and-ensure-that-you-maximize-network-capacity"></a>Hoe bewaakt u Hyper-V-ethernetadapters en zorgt u ervoor dat u de netwerkcapaciteit maximaliseert?  

Een strategie die wordt gebruikt bij het testen met FIO is het instellen `numjobs=16` van . Als u dit doet, wordt elke taak in 16 specifieke exemplaren gevorkt om de Microsoft Hyper-V netwerkadapter te maximaliseren.

U kunt controleren op activiteit op elk van de adapters in Windows Performance Monitor door Prestatiemeter te selecteren > Tellers toevoegen > Netwerkinterface > Microsoft Hyper-V **Netwerkadapter.**

![Schermopname van de interface Prestatiemeter-teller toevoegen.](../media/azure-netapp-files/smb-performance-performance-monitor-add-counter.png)

Nadat u gegevensverkeer in uw volumes hebt uitgevoerd, kunt u uw adapters bewaken in De prestatiemeter van Windows. Als u niet al deze 16 virtuele adapters gebruikt, is het mogelijk dat u de capaciteit van uw netwerkbandbreedte niet maximaliseert.

![Schermopname met prestatiemeteruitvoer.](../media/azure-netapp-files/smb-performance-performance-monitor-output.png)

## <a name="is-accelerated-networking-recommended"></a>Wordt versneld netwerken aanbevolen?

Voor maximale prestaties is het raadzaam om waar mogelijk versneld [netwerken](../virtual-network/create-vm-accelerated-networking-powershell.md) te configureren. Houd rekening met de volgende overwegingen:  

* De Azure Portal standaard versneld netwerken in voor virtuele machines die deze functie ondersteunen.  Andere implementatiemethoden, zoals Ansible en vergelijkbare configuratiehulpprogramma's, zijn dat echter mogelijk niet.  Als u versneld netwerken niet inschakelen, kunnen de prestaties van een computer worden gehookd.  
* Als versneld netwerken niet is ingeschakeld op de netwerkinterface van een virtuele machine vanwege het gebrek aan ondersteuning voor een exemplaartype of -grootte, blijft dit uitgeschakeld bij grotere instantietypen. In die gevallen hebt u handmatige interventie nodig.

## <a name="are-jumbo-frames-supported"></a>Worden er frames ondersteund?

Frames van het team worden niet ondersteund met virtuele Azure-machines.

## <a name="is-smb-signing-supported"></a>Wordt SMB-ondertekening ondersteund? 

Het SMB-protocol biedt de basis voor bestands- en afdrukdeling en andere netwerkbewerkingen, zoals extern Windows-beheer. Ter voorkoming van man-in-the-middle-aanvallen waardoor SMB-pakketten in-transit worden gewijzigd, ondersteunt het SMB-protocol de digitale ondertekening van SMB-pakketten. 

SMB-ondertekening wordt ondersteund voor alle SMB-protocolversies die worden ondersteund door Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Wat is de invloed op de prestaties van SMB-ondertekening?  

SMB-ondertekening heeft een schadelijk effect op de prestaties van SMB. Naast andere mogelijke oorzaken van prestatievermindering, verbruikt de digitale ondertekening van elk pakket extra CPU aan de clientzijde, zoals de onderstaande perfmon-uitvoer laat zien. In dit geval lijkt Core 0 verantwoordelijk voor SMB, inclusief SMB-ondertekening.  Een vergelijking met de niet-meerdere kanalen sequentiële leesdoorvoer in de vorige sectie laat zien dat SMB-ondertekening de totale doorvoer vermindert van 875MiB/s tot ongeveer 250MiB/s. 

![Grafiek met de invloed van de prestaties van SMB-ondertekening.](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)

## <a name="what-is-the-anticipated-impact-of-smb-encryption-on-client-workloads"></a>Wat is de verwachte impact van SMB-versleuteling op clientworkloads?

Zie [Veelgestelde vragen over SMB-versleuteling.](azure-netapp-files-faqs.md#smb_encryption_impact)

## <a name="next-steps"></a>Volgende stappen  

- [Veelgestelde vragen over Azure NetApp Files](azure-netapp-files-faqs.md)
- Zie de Azure NetApp Files: Managed Enterprise File Shares for SMB Workloads (Beheerde enterprise-bestands shares voor [SMB-workloads)](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) over het gebruik van SMB-bestands shares met Azure NetApp Files.