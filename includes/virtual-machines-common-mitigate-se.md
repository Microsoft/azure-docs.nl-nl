---
title: Include-bestand
description: Include-bestand
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/12/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: a9146099951aba223a7b201c1613e1ec0ba617d4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509451"
---
**Laatste document update**: 12 November 2019 10:00 am PST.

De openbaar making van een [nieuwe klasse van CPU-beveiligings problemen](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) die bekend zijn als speculatieve uitvoering van de aanvallen, heeft geleid tot vragen van klanten die meer duidelijkheid zoeken.  

Micro soft heeft oplossingen geïmplementeerd in al onze Cloud Services. De infra structuur die Azure uitvoert en de werk belastingen van de klant van elkaar isoleert, is beveiligd. Dit betekent dat een mogelijke aanvaller die dezelfde infra structuur gebruikt, uw toepassing niet kan aanvallen met behulp van deze beveiligings problemen.

Azure gebruikt zoveel mogelijk geheugen voor het behoud van het [onderhoud](../articles/virtual-machines/maintenance-and-updates.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json%252c%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json%253ftoc%253d%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#maintenance-that-doesnt-require-a-reboot) om de gevolgen van de klant te minimaliseren en de nood zaak voor het opnieuw opstarten te voor komen. Azure gaat verder met het gebruik van deze methoden bij het maken van systeemwerksysteem updates voor de host en het beschermen van onze klanten.

Meer informatie over hoe beveiliging is geïntegreerd in elk aspect van Azure is beschikbaar op de [Azure Security-documentatie](../articles/security/index.yml) site. 

> [!NOTE] 
> Sinds dit document voor het eerst werd gepubliceerd, zijn er meerdere varianten van deze beveiligings klasse vermeld. Micro soft blijft zwaar belegd in het beschermen van onze klanten en het leveren van hulp. Deze pagina wordt bijgewerkt, omdat er verdere oplossingen blijven worden uitgebracht. 
> 
> Op 12 november 2019 heeft Intel een technisch advies [gepubliceerd](https://software.intel.com/security-software-guidance/insights/deep-dive-intel-transactional-synchronization-extensions-intel-tsx-asynchronous-abort) over Intel® transactionele trans acties voor het asynchroon afbreken van een transactie [2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135)probleem (Intel® TSX). Dit beveiligings probleem is van invloed op Intel® Core®-processors en Intel® Xeon®-processors.  Microsoft Azure heeft updates van het besturings systeem uitgebracht en implementeert nieuwe micro code, omdat deze door Intel beschikbaar wordt gesteld om onze klanten te beschermen tegen deze nieuwe beveiligings lekken.   Azure werkt nauw samen met Intel om de nieuwe micro code te testen en te valideren vóór de officiële release op het platform. 
>
> **Klanten die niet-vertrouwde code in hun VM uitvoeren** , moeten maat regelen nemen om zich te beschermen tegen deze beveiligings lekken door hieronder te lezen. Lees hieronder meer informatie over alle beveiligings lekken van speculatieve uitvoering (micro soft Advisors adv [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)en [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Andere klanten dienen deze beveiligings lekken te evalueren van een verdediging in de diepte en overwegen de implicaties voor beveiliging en prestaties van de gekozen configuratie.
> 



## <a name="keeping-your-operating-systems-up-to-date"></a>Uw besturings systeem up-to-date houden

Hoewel een update van het besturings systeem niet vereist is om uw toepassingen die worden uitgevoerd op Azure, te isoleren van andere Azure-klanten, is het altijd een best practice om uw software up-to-date te houden. De meest recente beveiligings rollups voor Windows bevatten oplossingen voor verschillende beveiligings lekken van speculatieve uitvoering van scripts. Linux-distributies hebben ook meerdere updates uitgebracht om deze beveiligings problemen op te lossen. Hier volgen onze aanbevolen acties voor het bijwerken van uw besturings systeem:

| Aanbod | Aanbevolen actie  |
|----------|---------------------|
| Azure Cloud Services  | Schakel [automatisch bijwerken](../articles/cloud-services/cloud-services-how-to-configure-portal.md) in of zorg ervoor dat het nieuwste gast besturingssysteem wordt uitgevoerd. |
| Azure Linux Virtual Machines | Installeer updates van de provider van uw besturings systeem. Zie [Linux](#linux) verderop in dit document voor meer informatie. |
| Azure Windows Virtual Machines  | Installeer de meest recente beveiligings update.
| Andere Azure PaaS Services | Er is geen actie nodig voor klanten die deze services gebruiken. Azure houdt automatisch uw versies van het besturings systeem up-to-date. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Aanvullende richt lijnen voor het uitvoeren van niet-vertrouwde code 

Klanten die voor komen dat niet-vertrouwde gebruikers een wille keurige code uitvoeren, willen mogelijk enkele extra beveiligings functies implementeren in hun Azure-Virtual Machines of Cloud Services. Deze functies bieden bescherming tegen de inkomende vectoren voor het vrijgeven van informatie die door verschillende beveiligings lekken van speculatie worden beschreven.

Voor beelden van scenario's waarbij aanvullende beveiligings functies worden aanbevolen:

- U staat toe dat code die u niet vertrouwt, in uw virtuele machine wordt uitgevoerd.  
    - *U kunt bijvoorbeeld een van uw klanten toestaan een binair of script te uploaden dat u vervolgens in uw toepassing uitvoert*. 
- U kunt gebruikers die u niet vertrouwt, zich aanmelden bij uw virtuele machine met een account met beperkte bevoegdheden.   
    - *U kunt bijvoorbeeld een gebruiker met beperkte rechten zich aanmelden bij een van uw virtuele machines met behulp van extern bureau blad of SSH*.  
- U staat niet-vertrouwde gebruikers toegang toe tot virtuele machines die via geneste virtualisatie zijn geïmplementeerd.  
    - *Stel dat u de Hyper-V-host beheert, maar de virtuele machines toewijst aan niet-vertrouwde gebruikers*. 

Klanten die geen scenario met niet-vertrouwde code implementeren, hoeven deze aanvullende beveiligings functies niet in te scha kelen. 

## <a name="enabling-additional-security"></a>Extra beveiliging inschakelen 

U kunt aanvullende beveiligings functies in uw VM of Cloud service inschakelen als u niet-vertrouwde code uitvoert. Zorg er parallel voor dat uw besturings systeem up-to-date is om beveiligings functies in uw VM of Cloud service in te scha kelen

### <a name="windows"></a>Windows 

Het doel besturingssysteem moet up-to-date zijn om deze aanvullende beveiligings functies in te scha kelen. Hoewel er veel speculatieve uitvoering-kanaal problemen zijn ingeschakeld, moeten de extra functies die hier worden beschreven, hand matig worden ingeschakeld en kan dit de prestaties nadelig beïnvloeden. 


**Stap 1: Hyper-Threading uitschakelen op de virtuele machine** -klanten die niet-vertrouwde code uitvoeren op een Hyper-Threaded VM, moeten Hyper-Threading uitschakelen of verplaatsen naar een VM-grootte zonder Hyper Thread. Raadpleeg [dit document](../articles/virtual-machines/acu.md) voor een lijst met Hyper-Threaded VM-grootten (waarbij de verhouding van VCPU naar Core 2:1 is). Als u wilt controleren of Hyper-Threading voor uw virtuele machine is ingeschakeld, raadpleegt u het onderstaande script met behulp van de Windows-opdracht regel vanuit de VM.

Typ `wmic` om de interactieve interface in te voeren. Typ vervolgens het onderstaande om de hoeveelheid fysieke en logische processors op de virtuele machine weer te geven.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Als het aantal logische processors groter is dan fysieke processors (kern geheugens), wordt Hyper-Threading ingeschakeld.  Als u een Hyper-Threaded VM uitvoert, neemt u [contact op met de ondersteuning van Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) om Hyper-Threading uit te scha kelen.  Als Hyper-Threading is uitgeschakeld, **moet de ondersteuning voor de volledige VM opnieuw worden opgestart**. Raadpleeg het [aantal kern geheugens](#core-count) om te begrijpen waarom het aantal virtuele machines in de VM is gedaald.


**Stap 2**: Volg parallel met stap 1 de instructies in [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) om te controleren of de beveiligingen zijn ingeschakeld met behulp van de [SpeculationControl](https://aka.ms/SpeculationControlPS) Power shell-module.

> [!NOTE]
> Als u deze module eerder hebt gedownload, moet u de nieuwste versie installeren.
>


De uitvoer van het Power shell-script moet de onderstaande waarden hebben om ingeschakelde beveiligingen te valideren tegen deze beveiligings lekken:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
Windows OS support for TAA mitigation is enabled: True
```

Als de uitvoer wordt weer gegeven `MDS mitigation is enabled: False` , [neemt u contact op met Azure-ondersteuning](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) voor beschik bare beperkende opties.



**Stap 3**: Volg de instructies in [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) om beveiligingen in te scha kelen met behulp van de register sleutels om KVAS (kernel Virtual Address shadowing) en BTI-ondersteuning (vertakkings doel injectie) in te scha kelen `Session Manager` . Opnieuw opstarten is vereist.


**Stap 4**: voor implementaties die gebruikmaken van [geneste virtualisatie](../articles/virtual-machines/windows/nested-virtualization.md) (alleen D3 en E3): deze instructies zijn van toepassing in de virtuele machine die u als Hyper-V-host gebruikt.

1.  Volg de instructies in [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) om beveiliging in te scha kelen met behulp van de `MinVmVersionForCpuBasedMitigations` register sleutels.
2.  Stel het type Hyper Visor scheduler in op `Core` door de instructies [hier](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types)te volgen.


### <a name="linux"></a>Linux

<a name="linux"></a>Het inschakelen van de set aanvullende beveiligings functies in vereist dat het doel besturingssysteem volledig up-to-date is. Sommige beperkende factoren worden standaard ingeschakeld. In de volgende sectie worden de functies beschreven die standaard zijn uitgeschakeld en/of afhankelijk van de hardware-ondersteuning (micro code). Het inschakelen van deze functies kan invloed hebben op de prestaties. Raadpleeg de documentatie van uw besturings systeem provider voor verdere instructies


**Stap 1: Hyper-Threading uitschakelen op de virtuele machine** -klanten die niet-vertrouwde code uitvoeren op een Hyper-Threaded VM, moeten Hyper-Threading uitschakelen of verplaatsen naar een niet-Hyper-Threaded VM.  Raadpleeg [dit document](../articles/virtual-machines/acu.md) voor een lijst met Hyper-Threaded VM-grootten (waarbij de verhouding van VCPU naar Core 2:1 is). Als u wilt controleren of u een Hyper-Threaded VM uitvoert, voert u de `lscpu` opdracht uit in de Linux-VM. 

Als `Thread(s) per core = 2` , dan is Hyper-Threading ingeschakeld. 

Als de `Thread(s) per core = 1` Hyper-Threading is uitgeschakeld. 

 
Voorbeeld uitvoer voor een virtuele machine waarop Hyper-Threading is ingeschakeld: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

Als u een Hyper-Threaded VM uitvoert, neemt u [contact op met de ondersteuning van Azure](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) om Hyper-Threading uit te scha kelen.  Als Hyper-Threading is uitgeschakeld, **moet de ondersteuning voor de volledige VM opnieuw worden opgestart**. Raadpleeg het [aantal kern geheugens](#core-count) om te begrijpen waarom het aantal virtuele machines in de VM is gedaald.



**Stap 2**: Raadpleeg de documentatie van uw besturings systeem voor meer informatie over het oplossen van problemen met een van de hieronder genoemde kwets bare scripts voor het uitvoeren van een probleem:   
 
- [RedHat en CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Aantal kernen

Wanneer een Hyper-Threaded VM wordt gemaakt, wijst Azure 2 threads per kern toe. deze worden Vcpu's genoemd. Wanneer Hyper-Threading is uitgeschakeld, wordt door Azure een thread en Opper vlakken met single thread-kernen (fysieke kernen) verwijderd. De verhouding tussen vCPU en CPU is 2:1, dus als Hyper-Threading is uitgeschakeld, lijkt het aantal CPU'S in de virtuele machine te worden verminderd met de helft. Een D8_v3 VM is bijvoorbeeld een Hyper-Threaded VM die wordt uitgevoerd op 8 Vcpu's (2 threads per core x 4 kern geheugens).  Wanneer Hyper-Threading is uitgeschakeld, worden Cpu's naar 4 fysieke kernen verzonden met één thread per kern. 

## <a name="next-steps"></a>Volgende stappen

Dit artikel bevat richt lijnen voor de onderstaande speculatieve uitvoering-aanvallen die van invloed zijn op veel moderne processors:

[Spectre Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715-vertakkings doel-injectie (BTI)  
- CVE-2017-5754-kernel-pagina tabel isolatie (KPTI)
- CVE-2018-3639: speculatieve Store bypass (KPTI) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – Windows kernel Information – variant of Spectre variant 1
 
[L1-Terminal fout (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615-Intel software Guard Extensions (Intel SGX)
- CVE-2018-3620-besturings systemen (OS) en de modus systeem beheer (SMM)
- CVE-2018-3646: beïnvloedt Virtual Machine Manager (VMM)

[Gegevens bemonstering met microarchitectuur](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019-11091-gegevens steekproef cache geheugen (MDSUM) van microarchitectuur
- CVE-2018-12126-data sampling van buffer gegevens voor micro architectuur Store (MSBDS)
- CVE-2018-12127-microarchitectuur laad poort gegevens steekproef (MLPDS)
- CVE-2018-12130-data sampling van MFBDS-doorvoer buffers

Asynchrone afbreek bewerking voor transactionele synchronisatie-uitbrei dingen (Intel® TSX):  
- [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) – TSX-trans actie asynchroon afbreken (TAA)