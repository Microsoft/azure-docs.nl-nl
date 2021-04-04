---
title: Veelgestelde vragen over SMB-prestaties voor Azure NetApp Files | Microsoft Docs
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
ms.openlocfilehash: 9a07c6ae48cdca68a95db7770d90076eb8f10f95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91929453"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Veelgestelde vragen over SMB-prestaties voor Azure NetApp Files

In dit artikel vindt u antwoorden op veelgestelde vragen over de aanbevolen procedures voor SMB-prestaties voor Azure NetApp Files.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Is SMB meerdere kanalen ingeschakeld in SMB-shares? 

Ja, SMB meerdere kanalen is standaard ingeschakeld, een wijziging die in eerste januari 2020 is geplaatst. Voor alle SMB-shares die al dating op bestaande SMB-volumes, is de functie ingeschakeld en voor alle nieuw gemaakte volumes is de functie ook ingeschakeld op het moment dat deze wordt gemaakt. 

Elke SMB-verbinding die tot stand is gebracht voordat de functie wordt ingeschakeld, moet opnieuw worden ingesteld om gebruik te kunnen maken van de SMB-functie voor meerdere kanalen. Als u het opnieuw wilt instellen, kunt u de SMB-share loskoppelen en opnieuw verbinding maken.

## <a name="is-rss-supported"></a>Wordt RSS ondersteund?

Ja, Azure NetApp Files ondersteunt het schalen van de ontvangst zijde (RSS).

Als SMB meerdere kanalen is ingeschakeld, brengt een SMB3-client via een netwerk interface kaart (NIC) meer TCP-verbindingen tot stand met de Azure NetApp Files SMB-server. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Welke Windows-versies ondersteunen SMB meerdere kanalen?

SMB meerdere kanalen sinds Windows 2012 wordt ondersteund om de beste prestaties mogelijk te maken.  Zie [SMB meerdere kanalen](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) en [de basis beginselen van SMB meerdere kanalen](/archive/blogs/josebda/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0) implementeren voor meer informatie. 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Ondersteunt mijn virtuele Azure-machine RSS?

Als u wilt weten of de Nic's van uw virtuele Azure-machine RSS ondersteunen, voert u de opdracht `Get-SmbClientNetworkInterface` als volgt uit en controleert u het veld `RSS Capable` : 

![Scherm opname van de RSS-uitvoer voor de virtuele machine van Azure.](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Ondersteunt Azure NetApp Files SMB direct?

Nee, Azure NetApp Files biedt geen ondersteuning voor SMB direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Wat is het voor deel van SMB meerdere kanalen? 

Met de functie SMB meerdere kanalen kan een SMB3-client een pool met verbindingen tot stand brengen met een netwerk interface kaart (NIC) of meerdere Nic's en deze gebruiken om aanvragen voor één SMB-sessie te verzenden. In tegens telling tot ontwerp, SMB1 en SMB2 moet de client één verbinding tot stand brengen en alle SMB-verkeer voor een bepaalde sessie over die verbinding verzenden. Deze enkele verbinding beperkt de algehele protocol prestaties die kunnen worden bereikt vanaf één client.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Moet ik meerdere Nic's op mijn client voor SMB configureren?

Nee. De SMB-client komt overeen met het aantal NIC'S dat door de SMB-server wordt geretourneerd.  Elk opslag volume is toegankelijk vanuit één en slechts één opslag eindpunt.  Dit betekent dat er slechts één NIC wordt gebruikt voor een bepaalde SMB-relatie.  

`Get-SmbClientNetworkInterace`De virtuele machine heeft twee netwerk interfaces:--15 en 12, zoals hieronder wordt weer gegeven.  Zoals wordt weer gegeven onder de volgende opdracht `Get-SmbMultichannelConnection` , hoewel er twee met RSS geschikte nic's zijn, wordt alleen interface 12 gebruikt in combi natie met de SMB-share; interface 15 wordt niet gebruikt.

![Screeshot die de uitvoer voor RSS-compatibele NIC'S toont.](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Worden NIC-teams ondersteund in azure?

NIC-koppeling wordt niet ondersteund in Azure. Hoewel meerdere netwerk interfaces worden ondersteund op virtuele machines van Azure, vertegenwoordigen ze een logische in plaats van een fysieke construct. Daarom bieden ze geen fout tolerantie.  De band breedte die beschikbaar is voor een virtuele machine van Azure wordt ook berekend voor de machine zelf en niet voor afzonderlijke netwerk interfaces.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Wat zijn de prestaties zoals voor SMB meerdere kanalen?

De volgende tests en grafieken tonen de kracht van SMB meerdere kanalen voor workloads met één exemplaar.

### <a name="random-io"></a>Wille keurige I/O  

Als SMB meerdere kanalen is uitgeschakeld op de client, zijn er zuivere 4 KiB-Lees-en schrijf tests uitgevoerd met behulp van FIO en een GiB-werkset van 40.  De SMB-share is losgekoppeld van elke test, met stappen van het aantal SMB-client verbindingen per RSS-netwerk interface-instellingen van,,,, `1` `4` `8` `16` `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` . De tests laten zien dat de standaard instelling van `4` voldoende is voor I/O-intensieve workloads, waardoor het effect kan worden verhoogd `8` `16` . 

De opdracht heeft `netstat -na | findstr 445` aangetoond dat er extra verbindingen tot stand zijn gebracht met stappen van `1` naar `4` `8` en tot `16` .  Er zijn vier CPU-kernen voor SMB tijdens elke test volledig gebruikt, zoals bevestigd door de prestatie `Per Processor Network Activity Cycles` Statistieken (niet opgenomen in dit artikel).

![Grafiek met een wille keurige I/O-vergelijking van SMB meerdere kanalen.](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

De virtuele machine van Azure heeft geen invloed op I/O-opslag limieten voor SMB (of NFS).  Zoals weer gegeven in de volgende grafiek, heeft het D32ds-exemplaar type een beperkt aantal van 308.000 voor opslag-IOPS in de cache en 51.200 voor niet-opgeslagen opslag-IOPS.  In het bovenstaande diagram ziet u echter veel meer I/O over SMB.

![Grafiek waarin de test voor wille keurige I/O-vergelijking wordt weer gegeven.](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>Sequentiële IO 

Tests die vergelijkbaar zijn met de tests voor wille keurige I/O's die eerder zijn beschreven, zijn uitgevoerd met 64-KiB sequentiële I/O. Hoewel de toename van het aantal client verbindingen per RSS-netwerk interface meer dan 4 heeft geen merkbaar effect op wille keurige I/O, is hetzelfde niet van toepassing op opeenvolgende I/O's. Zoals in het volgende diagram wordt weer gegeven, is elke toename gekoppeld aan een overeenkomstige toename van de Lees doorvoer. De schrijf doorvoer bleef plat vanwege de beperkingen van de netwerk bandbreedte die worden geplaatst door Azure voor elk type exemplaar/grootte. 

![Grafiek waarin de vergelijking van de doorvoer test wordt weer gegeven.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure plaatst netwerk frequentie limieten voor elk type virtuele machine/grootte. De frequentie limiet wordt alleen opgelegd voor uitgaand verkeer. Het aantal Nic's dat aanwezig is op een virtuele machine heeft geen invloed op de totale hoeveelheid band breedte die beschikbaar is voor de machine.  Het D32ds-exemplaar type heeft bijvoorbeeld een ingestelde netwerk limiet van 16.000 Mbps (2.000 MiB/s).  Zoals in de sequentiële grafiek hierboven wordt weer gegeven, is de limiet van invloed op het uitgaande verkeer (schrijf bewerkingen), maar niet op meerkanaalse Lees bewerkingen.

![Grafiek waarin de opeenvolgende I/O-vergelijking test wordt weer gegeven.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="what-performance-is-expected-with-a-single-instance-with-a-1-tb-dataset"></a>Welke prestaties worden verwacht met één exemplaar met een gegevensset van 1 TB?

Om meer inzicht te krijgen in werk belastingen met mixen voor lezen/schrijven, worden in de volgende twee grafieken de prestaties weer gegeven van een single, Ultra service-volume van 50 TB met een gegevensset van 1 TB en met SMB meerdere kanalen van 4. Er is een optimale IODepth van 16 gebruikt en er zijn flexibele i/o-para meters (FIO) gebruikt om het volledige gebruik van de netwerk bandbreedte () te garanderen `numjobs=16` .

In het volgende diagram ziet u de resultaten voor wille keurige I/O van 4 KB, met één VM-exemplaar en een lezen/schrijven-mix van 10% intervallen:

![Grafiek met een wille keurige i/o-test voor Windows 2019 Standard _D32ds_v4 4 KB.](../media/azure-netapp-files/smb-performance-standard-4k-random-io.png)

In het volgende diagram ziet u de resultaten voor sequentiële I/O's:

![Grafiek waarin Windows 2019 Standard _D32ds_v4 64 KB sequentiële door Voer wordt weer gegeven.](../media/azure-netapp-files/smb-performance-standard-64k-throughput.png)

## <a name="what-performance-is-expected-when-scaling-out-using-5-vms-with-a-1-tb-dataset"></a>Welke prestaties worden er verwacht bij het uitschalen van vijf Vm's met een gegevensset van 1 TB?

Deze tests met vijf Vm's gebruiken dezelfde test omgeving als de enkele virtuele machine, waarbij elk proces naar een eigen bestand schrijft.

In het volgende diagram ziet u de resultaten voor wille keurige I/O:

![Grafiek met de Windows 2019 Standard _D32ds_v4-IO-test met 5 exemplaren van 4 KB-randio.](../media/azure-netapp-files/smb-performance-standard-4k-random-io-5-instances.png)

In het volgende diagram ziet u de resultaten voor sequentiële I/O's:

![Grafiek met de Windows 2019 Standard _D32ds_v4 64K 5-opeenvolgende door Voer voor instanties.](../media/azure-netapp-files/smb-performance-standard-64k-throughput-5-instances.png)

## <a name="how-do-you-monitor-hyper-v-ethernet-adapters-and-ensure-that-you-maximize-network-capacity"></a>Hoe bewaakt u Hyper-V Ethernet-adapters en zorgt u ervoor dat u de netwerk capaciteit maximaliseert?  

Een strategie die wordt gebruikt om met FIO te testen, is om in te stellen `numjobs=16` . Hiermee splitst u elke taak in 16 specifieke instanties om de Microsoft Hyper-V netwerk adapter te maximaliseren.

U kunt op elk van de adapters in Windows prestatie meter controleren door **prestatie meter te selecteren > items toe te voegen > netwerk Interface > Microsoft Hyper-V netwerk adapter**.

![Scherm opname van de prestatie meter die de item interface toevoegen weergeeft.](../media/azure-netapp-files/smb-performance-performance-monitor-add-counter.png)

Nadat u gegevens verkeer hebt uitgevoerd op uw volumes, kunt u uw adapters bewaken in de prestatie meter van Windows. Als u niet al deze 16 virtuele adapters gebruikt, is het mogelijk dat u de capaciteit van uw netwerk bandbreedte niet optimaal kunt benutten.

![Scherm opname van de uitvoer van de prestatie meter.](../media/azure-netapp-files/smb-performance-performance-monitor-output.png)

## <a name="is-accelerated-networking-recommended"></a>Wordt versneld netwerken aanbevolen?

Voor maximale prestaties kunt u het beste zo snel mogelijk [netwerken](../virtual-network/create-vm-accelerated-networking-powershell.md) configureren. Houd rekening met de volgende aandachtspunten:  

* Met de Azure Portal is versneld netwerken standaard ingeschakeld voor virtuele machines die deze functie ondersteunen.  Andere implementatie methoden, zoals Ansible en vergelijk bare configuratie hulpprogramma's, zijn echter niet mogelijk.  Fout bij het inschakelen van versneld netwerken kan de prestaties van een machine hobble.  
* Als versneld netwerken niet zijn ingeschakeld op de netwerk interface van een virtuele machine vanwege een gebrek aan ondersteuning voor een exemplaar type of-grootte, blijft het uitgeschakeld met grotere instantie typen. In die gevallen hebt u hand matige interventie nodig.

## <a name="are-jumbo-frames-supported"></a>Worden Jumbo frames ondersteund?

Jumbo-frames worden niet ondersteund met virtuele machines van Azure.

## <a name="is-smb-signing-supported"></a>Wordt SMB-ondertekening ondersteund? 

Het SMB-protocol biedt de basis voor het delen van bestanden en printers en andere netwerk bewerkingen, zoals extern Windows-beheer. Ter voorkoming van man-in-the-middle-aanvallen waardoor SMB-pakketten in-transit worden gewijzigd, ondersteunt het SMB-protocol de digitale ondertekening van SMB-pakketten. 

SMB-ondertekening wordt ondersteund voor alle SMB-protocol versies die worden ondersteund door Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Wat is de invloed van de prestaties van SMB-ondertekening?  

SMB-ondertekening heeft een deleterious-effect op SMB-prestaties. De digitale ondertekening van elk pakket verbruikt onder andere mogelijke oorzaken van de prestaties van de uitvoering van extra CPU-aan client zijde, zoals hieronder wordt weer gegeven. In dit geval is core 0 verantwoordelijk voor SMB, met inbegrip van SMB-ondertekening.  Een vergelijking met het aantal sequentiële Lees doorvoer van niet-meerkanaals in de vorige sectie laat zien dat SMB-ondertekening de algehele door Voer van 875MiB/s naar ongeveer 250MiB/s vermindert. 

![Grafiek waarin de prestaties van de SMB-ondertekening worden weer gegeven.](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Volgende stappen  

- [Veelgestelde vragen over Azure NetApp Files](azure-netapp-files-faqs.md)
- Zie de [Azure NetApp files: beheerde bestands shares voor bedrijven voor SMB-workloads](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) over het gebruik van SMB-bestands shares met Azure NetApp files.