---
title: Geschiedenis van Azure Site Recovery Deployment Planner-versie
description: Bekende problemen met verschillende Site Recovery Deployment Planner versies en de bijbehorende release datums.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 6/4/2020
ms.author: dapatil
ms.openlocfilehash: feb4f6a24653aca8da825af90341c8016255e8b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86133797"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Geschiedenis van Azure Site Recovery Deployment Planner-versie

In dit artikel vindt u een overzicht van alle versies van Azure Site Recovery Deployment Planner, samen met de oplossingen, bekende beperkingen in elke release-datum.

## <a name="version-252"></a>Versie 2,52

**Release datum: 4 juni 2020**

**Dit**

- Ondersteuning toegevoegd voor vCenter 7,0
- Er is ondersteuning toegevoegd voor de volgende besturings systemen:

    - SUSE Linux Enter prise 15 (met alle secundaire versies)
    - Red Hat Enterprise Linux 8 (met alle secundaire versies)


## <a name="version-251"></a>Versie 2,51

**Release datum: 22 augustus 2019**

**Dit**

- Het probleem met de kosten aanbeveling is opgelost met Deployment Planner versie 2,5

## <a name="version-25"></a>Versie 2,5

**Release datum: 29 juli 2019**

**Dit**

- Voor virtuele VMware-machines en fysieke machines wordt de aanbeveling bijgewerkt op basis van de replicatie naar Managed Disks.
- Er is ondersteuning toegevoegd voor Windows 10 (x64), Windows 8,1 (x64), Windows 8 (x64), Windows 7 (x64) SP1 of hoger

## <a name="version-24"></a>Versie 2,4

**Release datum: 17 april 2019**

**Dit**

- Verbeterde compatibiliteit van besturings systemen, met name bij het verwerken van op lokalisatie gebaseerde fouten.
- Er zijn Vm's met Maxi maal 20 Mbps gegevens wijzigings snelheden (verloop) toegevoegd aan de controle lijst voor compatibiliteit.
- Foutberichten verbeterd
- Er is ondersteuning toegevoegd voor vCenter 6,7.
- Er is ondersteuning toegevoegd voor Windows Server 2019 en Red Hat Enterprise Linux-werk station (RHEL).



## <a name="version-23"></a>Versie 2,3

**Release datum: 3 december 2018**

**Dit**

- Er is een probleem opgelost waardoor de Deployment Planner geen rapport kan genereren met de beschik bare doel locatie en het gegeven abonnement.

## <a name="version-22"></a>Versie 2,2 

**Release datum: 25 april 2018**

**Dit**

- GetVMList bewerkingen:
  - Er is een probleem opgelost dat ertoe leidt dat GetVMList mislukt als de opgegeven map niet bestaat. Nu wordt de standaard directory gemaakt of wordt de map gemaakt die is opgegeven in de para meter uitvoer bestand.
  - Er zijn meer gedetailleerde oorzaken voor het mislukken van de GetVMList toegevoegd.
- Informatie over het VM-type is toegevoegd als een kolom in het werk blad compatibele Vm's van het Deployment Planner-rapport.
- Herstel na nood gevallen voor Hyper-V naar Azure:
  - Uitgesloten Vm's met gedeelde Vhd's en PassThrough-schijven van profile ring. De Startprofiling-bewerking toont de lijst met uitgesloten Vm's in de-console.
  - Vm's met meer dan 64 schijven zijn toegevoegd aan de lijst met niet-compatibele Vm's.
  - De compressie factor voor de initiële replicatie (IR) en de Delta replicatie (DR) is bijgewerkt.
  - Er is beperkte ondersteuning toegevoegd voor SMB-opslag.

## <a name="version-21"></a>Versie 2.1

**Release datum: 3 januari 2018**

**Dit**

- Het Excel-rapport is bijgewerkt.
- Opgeloste fouten in de GetThroughput-bewerking.
- U hebt de optie toegevoegd om het aantal Vm's dat moet worden profiel, te beperken of het rapport te genereren. De standaard limiet is 1.000 Vm's.
- Nood herstel van VMware naar Azure:
  - Er is een probleem opgelost met een Windows Server 2016-VM in de niet-compatibele tabel. 
  - Bijgewerkte compatibiliteits berichten voor Windows-Vm's voor Extensible Firmware Interface (EFI).
- VMware naar Azure en Hyper-V naar Azure bijgewerkt, limiet voor VM-gegevens verloop per VM. 
- Verbeterde betrouw baarheid van het parseren van bestanden met virtuele machines.

## <a name="version-201"></a>Versie 2.0.1

**Release datum: 7 december 2017**

**Dit**

- Aanbeveling toegevoegd om de netwerk bandbreedte te optimaliseren.

## <a name="version-20"></a>Versie 2,0

**Release datum: 28 november 2017**

**Dit**

- Er is ondersteuning toegevoegd voor Hyper-V naar Azure-herstel na nood gevallen.
- De kosten calculator is toegevoegd.
- Controle van versie van besturings systeem toegevoegd voor VMware naar Azure herstel na nood gevallen om te bepalen of de virtuele machine compatibel of niet compatibel is met de beveiliging. Het hulp programma gebruikt de versie teken reeks van het besturings systeem die wordt geretourneerd door de vCenter-Server voor die VM. Het is de versie van het gast besturingssysteem die de gebruiker heeft geselecteerd tijdens het maken van de virtuele machine in VMware.

**Bekende beperkingen:**

- Voor Hyper-V naar Azure herstel na nood gevallen kan de VM een naam hebben die de volgende tekens bevat:,,, `,` `"` `[` `]` en ``` ` ``` niet wordt ondersteund. Als het rapport is profileeerd, mislukt het genereren van rapporten of heeft dit een onjuist resultaat.
- Voor de nood herstel van VMware naar Azure wordt de VM met een naam die een komma bevat niet ondersteund. Als het rapport is profileeerd, mislukt het genereren van rapporten of heeft dit een onjuist resultaat.

## <a name="version-131"></a>Versie 1.3.1

**Release datum: 19 juli 2017** 

**Dit**

- Er is ondersteuning toegevoegd voor grote schijven (> 1 TB) in het genereren van rapporten. Nu kunt u Deployment Planner gebruiken om replicatie te plannen voor virtuele machines met een grotere schijf grootte dan 1 TB (Maxi maal 4095 GB).
Meer informatie over [ondersteuning voor grote schijven in Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>Versie 1,3

**Release datum: 9 mei 2017**

**Dit**

- Er is ondersteuning toegevoegd voor beheerde schijven bij het genereren van rapporten. Het aantal virtuele machines dat kan worden geplaatst op één opslag account, wordt berekend op basis van het geval dat de beheerde schijf is geselecteerd voor failover/testfailover.

## <a name="version-12"></a>Versie 1,2

**Release datum: 7 april 2017**

**Dit**

- Het toegevoegde opstart type (BIOS of EFI) controleert voor elke VM of de virtuele machine compatibel of niet compatibel is met de beveiliging.
- Er zijn informatie toegevoegd over het type besturings systeem voor elke virtuele machine op de werk bladen compatibele Vm's en incompatibele Vm's.
- Er is ondersteuning toegevoegd voor de GetThroughput-bewerking voor de regio's Amerikaanse overheid en China Microsoft Azure.
- Er zijn meer controles op vereisten toegevoegd voor vCenter en ESXi Server.
- Er is een probleem opgelost waarbij een onjuist rapport wordt gegenereerd wanneer de land instellingen worden ingesteld op niet-Engels.

## <a name="version-11"></a>Versie 1,1

**Release datum: 9 maart 2017**

**Dit**

- Er is een probleem opgelost waardoor het profileren van virtuele machines niet mogelijk is wanneer er twee of meer virtuele machines met dezelfde naam of hetzelfde IP-adres zijn op verschillende vCenter ESXi-hosts.
- Er is een probleem opgelost waardoor het kopiëren en zoeken is uitgeschakeld voor de compatibele Vm's en niet-compatibele Vm's-werk bladen.

## <a name="version-10"></a>Versie 1,0

**Release datum: 23 februari 2017**

**Bekende beperkingen:**

- Ondersteunt alleen scenario's voor VMware naar Azure voor herstel na nood gevallen. Gebruik het [hulp programma hyper-v capacity planner](./hyper-v-deployment-planner-overview.md)voor hyper-v naar Azure-scenario's voor herstel na nood gevallen.
- Biedt geen ondersteuning voor de GetThroughput-bewerking voor de regio's Amerikaanse overheid en China Microsoft Azure.
- Het hulp programma cann't het profiel Vm's als de vCenter-Server twee of meer virtuele machines met dezelfde naam of hetzelfde IP-adres op verschillende ESXi-hosts heeft.
In deze versie slaat het hulpprogramma profilering over bij namen of IP-adressen van virtuele machines die dubbel voorkomen in het bestand dat is opgegeven voor VMListFile. Een tijdelijke oplossing is de virtuele machines te profileren met behulp van een ESXi-host in plaats van de vCenter-server. Zorg ervoor dat u één exemplaar voor elke ESXi-host uitvoert.
