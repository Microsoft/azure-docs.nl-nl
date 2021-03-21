---
title: Grootte van virtuele machines voor Azure Cloud Services (klassiek) | Microsoft Docs
description: Geeft een lijst van de verschillende grootten van virtuele machines (en Id's) voor web-en werk rollen van Azure Cloud service.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: a31c9c4747ccc72f0552ebe2f8daf85ef5fba8a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015298"
---
# <a name="sizes-for-cloud-services-classic"></a>Grootten voor Cloud Services (klassiek)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

In dit onderwerp worden de beschik bare grootten en opties voor Cloud service-rolinstanties (webrollen en werk rollen) beschreven. Het biedt ook overwegingen bij de implementatie bij het plannen van het gebruik van deze resources. Elke grootte heeft een ID die u in het [service definitie bestand](cloud-services-model-and-package.md#csdef)plaatst. De prijzen voor elke grootte zijn beschikbaar op de pagina met [Cloud Services prijzen](https://azure.microsoft.com/pricing/details/cloud-services/) .

> [!NOTE]
> Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md) voor meer informatie over verwante Azure-limieten.
>
>

## <a name="sizes-for-web-and-worker-role-instances"></a>Groottes voor web-en worker-instanties
Er zijn meerdere standaardgrootten waaruit u in Azure kunt kiezen. Enkele overwegingen voor een aantal van deze grootten zijn:

* Virtuele machines uit de D-serie zijn ontworpen voor het uitvoeren van toepassingen die meer rekenvermogen en tijdelijke schijfprestaties vereisen. Virtuele machines uit de D-serie hebben snellere processors, een hogere geheugen-naar-core-snelheid en een SSD (solid-state drive) voor de tijdelijke schijf. Voor meer informatie leest u de aankondiging in de Azure-blog [New D-Series Virtual Machine Sizes](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) (Nieuwe grootten van virtuele machines uit de D-serie).
* De Dv3-serie, dv2-serie, een follow-on voor de oorspronkelijke D-serie, bevat een krachtigere CPU. De CPU van de Dv2-serie is ongeveer 35% sneller dan de CPU van de D-serie. Deze is gebaseerd op de nieuwste generatie Intel Xeon® E5-2673 v3-processor van 2,4 GHz (Haswell). Met Intel Turbo Boost Technology 2.0 kunnen ze maar liefst 3,1 GHz bereiken. De Dv2-serie heeft dezelfde geheugen- en schijfconfiguraties als de D-serie.
* Virtuele machines uit de G-serie bieden het meeste geheugen en worden uitgevoerd op hosts met een processor uit de Intel Xeon E5 V3-familie.
* De Vm's uit de A-serie kunnen worden geïmplementeerd op diverse hardwaretypen en processors. De grootte wordt op basis van de hardware beperkt om consistente processor prestaties te bieden voor het actieve exemplaar, ongeacht de hardware waarop het wordt geïmplementeerd. Om de fysieke hardware te bepalen waarop deze grootte is geïmplementeerd, vraagt u vanuit de virtuele machine gegevens over de virtuele hardware op.
* De A0-grootte wordt overgeschreven naar de fysieke hardware. Alleen bij deze specifieke grootte kunnen implementaties van andere klanten invloed hebben op de prestaties van uw uitgevoerde workload. De relatieve prestaties worden hieronder beschreven, zoals de verwachte basislijn, met een variabiliteit van ongeveer 15 procent.

De grootte van de virtuele machine heeft invloed op de prijs. De grootte heeft ook invloed op de verwerkings-, geheugen- en opslagcapaciteit van de virtuele machine. Opslagkosten worden afzonderlijk berekend op basis van het aantal pagina's dat in het opslagaccount is gebruikt. Zie [Cloud Services Prijs informatie](https://azure.microsoft.com/pricing/details/cloud-services/) en [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie.

De volgende overwegingen kunnen u helpen bij het kiezen van een grootte:

* Grootten uit de A8-A11- en H-serie worden ook wel *rekenintensieve exemplaren* genoemd. De hardware waarop deze grootten worden uitgevoerd, is ontworpen en geoptimaliseerd voor rekenintensieve en netwerkintensieve toepassingen, waaronder HPC-clustertoepassingen (high-performance computing), modellerings- en simulatietoepassingen. De A8-A11-serie gebruikt Intel Xeon E5-2670 @ 2,6 GHZ en de H-serie gebruikt Intel Xeon E5-2667 v3 @ 3,2 GHz. Zie [High Performance Compute VM sizes](../virtual-machines/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(Engelstalig) voor gedetailleerde informatie en overwegingen over het gebruik van deze grootten.
* De Dv3-serie, dv2-serie, D-serie, G-serie zijn ideaal voor toepassingen die snellere Cpu's, betere prestaties van de lokale schijf of een hogere hoeveelheid geheugen vereisen. Ze bieden een krachtige combinatie voor vele toepassingen op bedrijfsniveau.
* Sommige fysieke hosts in Azure-datacenters bieden mogelijk geen ondersteuning voor grotere VM-formaten, zoals A5 – A11. Als gevolg hiervan kan het fout bericht de **virtuele machine {machine name} niet configureren** , of kan de **virtuele machine {machine naam} niet worden gemaakt** wanneer de grootte van een bestaande virtuele machine wordt gewijzigd in een nieuwe omvang. maken van een nieuwe virtuele machine in een virtueel netwerk dat is gemaakt vóór 16 april 2013; of toevoegen van een nieuwe virtuele machine aan een bestaande Cloud service. Zie [fout: ' het configureren van de virtuele machine is mislukt '](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) in het ondersteunings forum voor tijdelijke oplossingen voor elk implementatie scenario.
* Het is ook mogelijk dat uw abonnement het aantal kernen beperkt dat u in een bepaalde groottefamilie mag implementeren. Neem contact op met ondersteuning van Azure als u een quotum wilt verhogen.

## <a name="performance-considerations"></a>Prestatieoverwegingen
We hebben het concept van de Azure Compute Unit (ACU) ontwikkeld om een manier te bieden voor het vergelijken van de prestaties van Compute (CPU) voor Azure-Sku's en om te bepalen welke SKU het meest waarschijnlijk is om te voldoen aan uw prestatie behoeften.  ACU is momenteel gestandaardiseerd op 100 voor een kleine virtuele machine (Standard_A1). Alle andere SKU's geven vervolgens weer hoeveel sneller die SKU een standaardbenchmark ongeveer kan uitvoeren.

> [!IMPORTANT]
> De ACU is slechts een richtlijn. De resultaten voor uw workload kunnen verschillen.
>
>

<br>

| SKU-familie | ACU/kern |
| --- | --- |
| [ExtraSmall](#a-series) |50 |
| [Kleine ExtraLarge](#a-series) |100 |
| [A5-7](#a-series) |100 |
| [A8-A11](#a-series) |225* |
| [Een v2](#av2-series) |100 |
| [!](#d-series) |160 |
| [D v2](#dv2-series) |210 - 250* |
| [D v3](#dv3-series) |160-190 * |
| [E v3](#ev3-series) |160-190 * |
| [Kg](#g-series) |180 - 240* |
| [HxBxD](#h-series) |290 - 300* |

ACU's die met een * zijn gemarkeerd, maken gebruik van Intel® Turbo-technologie om de CPU-frequentie te verhogen en nóg betere prestaties te leveren. Hoe groot die extra prestaties zijn, is afhankelijk van de VM-grootte, de workload en de andere workloads die op dezelfde host worden uitgevoerd.

## <a name="size-tables"></a>Groottetabellen
In de volgende tabellen ziet u de grootten en de capaciteiten die ze bieden.

* De opslagcapaciteit wordt weergegeven in GiB-eenheden of 1024^3 bytes. Wanneer u schijven die zijn gemeten in GB (1000^3 bytes), vergelijkt met schijven die zijn gemeten in GiB (1024^3), moet u voor ogen houden dat de capaciteit in GiB kleiner lijkt te zijn. 1023 GiB is bijvoorbeeld gelijk aan 1098,4 GB
* De schijfdoorvoer wordt gemeten in I/O-bewerkingen per seconde (IOPS) en MBps, waarbij MBps = 10^6 bytes per seconde.
* Gegevensschijven kunnen in de modus met of zonder caching werken. Voor schijfbewerkingen met gegevenscaching is de cachemodus van de host ingesteld op **ReadOnly** of **ReadWrite**. Voor schijfbewerkingen zonder gegevenscaching is de cachemodus van de host ingesteld op **Geen**.
* De maximale netwerkbandbreedte is de maximale geaggregeerde bandbreedte die is toegekend en toegewezen per VM-type. De maximale bandbreedte geeft richtlijnen voor het selecteren van het juiste type virtuele machine om ervoor te zorgen dat er voldoende netwerkcapaciteit beschikbaar is. Wanneer u overstapt tussen laag, gemiddeld, hoog en zeer hoog, neemt de door Voer dienovereenkomstig toe. De werkelijke netwerkprestaties zijn afhankelijk van talloze factoren, waaronder de netwerk- en toepassingsbelastingen en de instellingen van het toepassingsnetwerk.

## <a name="a-series"></a>A-serie
| Grootte            | CPU-kernen | Geheugen: GiB  | Tijdelijke opslag: GiB       | Max. aantal NIC's/netwerkbandbreedte |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| ExtraSmall      | 1         | 0,768        | 20                   | 1/laag |
| Klein           | 1         | 1,75         | 225                  | 1/gemiddeld |
| Normaal          | 2         | 3,5          | 490                  | 1/gemiddeld |
| Groot           | 4         | 7            | 1000                 | 2/hoog |
| ExtraLarge      | 8         | 14           | 2040                 | 4/hoog |
| A5              | 2         | 14           | 490                  | 1/gemiddeld |
| A6              | 4         | 28           | 1000                 | 2/hoog |
| A7              | 8         | 56           | 2040                 | 4/hoog |

## <a name="a-series---compute-intensive-instances"></a>A-serie: rekenintensieve exemplaren
Zie [High Performance Compute VM sizes](../virtual-machines/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(Engelstalig) voor informatie en overwegingen over het gebruik van deze grootten.

| Grootte            | CPU-kernen | Geheugen: GiB  | Tijdelijke opslag: GiB       | Max. aantal NIC's/netwerkbandbreedte |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| A8             |8          | 56           | 1817                 | 2/hoog |
| A9             |16         | 112          | 1817                 | 4/zeer hoog |
| A10             |8          | 56           | 1817                 | 2/hoog |
| A11             |16         | 112          | 1817                 | 4/zeer hoog |

\*RDMA-compatibel

## <a name="av2-series"></a>Av2-serie

| Grootte            | CPU-kernen | Geheugen: GiB  | Tijdelijke opslag (SSD): GiB       | Max. aantal NIC's/netwerkbandbreedte |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_A1_v2  | 1         | 2            | 10                   | 1/gemiddeld                 |
| Standard_A2_v2  | 2         | 4            | 20                   | 2/gemiddeld                 |
| Standard_A4_v2  | 4         | 8            | 40                   | 4/hoog                     |
| Standard_A8_v2  | 8         | 16           | 80                   | 8/hoog                     |
| Standard_A2m_v2 | 2         | 16           | 20                   | 2/gemiddeld                 |
| Standard_A4m_v2 | 4         | 32           | 40                   | 4/hoog                     |
| Standard_A8m_v2 | 8         | 64           | 80                   | 8/hoog                     |


## <a name="d-series"></a>D-serie
| Grootte            | CPU-kernen | Geheugen: GiB  | Tijdelijke opslag (SSD): GiB       | Max. aantal NIC's/netwerkbandbreedte |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1     | 1         | 3,5          | 50                   | 1/gemiddeld |
| Standard_D2     | 2         | 7            | 100                  | 2/hoog |
| Standard_D3     | 4         | 14           | 200                  | 4/hoog |
| Standard_D4     | 8         | 28           | 400                  | 8/hoog |
| Standard_D11    | 2         | 14           | 100                  | 2/hoog |
| Standard_D12    | 4         | 28           | 200                  | 4/hoog |
| Standard_D13    | 8         | 56           | 400                  | 8/hoog |
| Standard_D14    | 16        | 112          | 800                  | 8/zeer hoog |

## <a name="dv2-series"></a>Dv2-serie
| Grootte            | CPU-kernen | Geheugen: GiB  | Tijdelijke opslag (SSD): GiB       | Max. aantal NIC's/netwerkbandbreedte |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1_v2  | 1         | 3,5          | 50                   | 1/gemiddeld |
| Standard_D2_v2  | 2         | 7            | 100                  | 2/hoog |
| Standard_D3_v2  | 4         | 14           | 200                  | 4/hoog |
| Standard_D4_v2  | 8         | 28           | 400                  | 8/hoog |
| Standard_D5_v2  | 16        | 56           | 800                  | 8/zeer hoog |
| Standard_D11_v2 | 2         | 14           | 100                  | 2/hoog |
| Standard_D12_v2 | 4         | 28           | 200                  | 4/hoog |
| Standard_D13_v2 | 8         | 56           | 400                  | 8/hoog |
| Standard_D14_v2 | 16        | 112          | 800                  | 8/zeer hoog |
| Standard_D15_v2 | 20        | 140          | 1000                | 8/zeer hoog |

## <a name="dv3-series"></a>Dv3-serie

| Grootte            | CPU-kernen | Geheugen: GiB   | Tijdelijke opslag (SSD): GiB       | Max. aantal NIC's/netwerkbandbreedte |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_D2_v3  | 2         | 8             | 50                   | 2/gemiddeld |
| Standard_D4_v3  | 4         | 16            | 100                  | 2/hoog |
| Standard_D8_v3  | 8         | 32            | 200                  | 4/hoog |
| Standard_D16_v3 | 16        | 64            | 400                  | 8/zeer hoog |
| Standard_D32_v3 | 32        | 128           | 800                  | 8/zeer hoog |
| Standard_D48_v3 | 48        | 192           | 1200                 | 8/zeer hoog |
| Standard_D64_v3 | 64        | 256           | 1600                 | 8/zeer hoog |

## <a name="ev3-series"></a>Ev3-serie

| Grootte            | CPU-kernen | Geheugen: GiB   | Tijdelijke opslag (SSD): GiB       | Max. aantal NIC's/netwerkbandbreedte |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_E2_v3  | 2         | 16            | 50                   | 2/gemiddeld |
| Standard_E4_v3  | 4         | 32            | 100                  | 2/hoog |
| Standard_E8_v3  | 8         | 64            | 200                  | 4/hoog |
| Standard_E16_v3 | 16        | 128           | 400                  | 8/zeer hoog |
| Standard_E32_v3 | 32        | 256           | 800                  | 8/zeer hoog |
| Standard_E48_v3 | 48        | 384           | 1200                 | 8/zeer hoog |
| Standard_E64_v3 | 64        | 432           | 1600                 | 8/zeer hoog |


## <a name="g-series"></a>G-serie
| Grootte            | CPU-kernen | Geheugen: GiB  | Tijdelijke opslag (SSD): GiB       | Max. aantal NIC's/netwerkbandbreedte |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_G1     | 2         | 28           | 384                  |1/hoog |
| Standard_G2     | 4         | 56           | 768                  |2/hoog |
| Standard_G3     | 8         | 112          | 1536                |4/zeer hoog |
| Standard_G4     | 16        | 224          | 3072                |8/zeer hoog |
| Standard_G5     | 32        | 448          | 6144                |8/zeer hoog |

## <a name="h-series"></a>H-serie
Virtuele Azure-machines uit de H-serie zijn de volgende generatie HPC-VM's, gericht op intensieve rekenbehoeften, zoals moleculaire modellering en numerieke stromingsleer. Deze 8-en 16-core-Vm's zijn gebouwd op basis van de Intel Haswell E5-2667 v3-processor technologie met DDR4 geheugen en lokale SSD-opslag.

Naast een zeer hoge CPU-kracht biedt de H-serie ook verschillende opties voor RDMA-netwerken met lage latentie met gebruik van FDR InfiniBand, evenals verschillende geheugenconfiguraties om geheugenintensieve rekenvereisten te ondersteunen.

| Grootte            | CPU-kernen | Geheugen: GiB  | Tijdelijke opslag (SSD): GiB       | Max. aantal NIC's/netwerkbandbreedte |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_H8     | 8         | 56           | 1000                 | 8/hoog |
| Standard_H16    | 16        | 112          | 2000                 | 8/zeer hoog |
| Standard_H8m    | 8         | 112          | 1000                 | 8/hoog |
| Standard_H16m   | 16        | 224          | 2000                 | 8/zeer hoog |
| Standard_H16r*  | 16        | 112          | 2000                 | 8/zeer hoog |
| Standard_H16mr* | 16        | 224          | 2000                 | 8/zeer hoog |

\*RDMA-compatibel

## <a name="configure-sizes-for-cloud-services"></a>Groottes voor Cloud Services configureren
U kunt de grootte van de virtuele machine van een rolinstantie opgeven als onderdeel van het service model dat wordt beschreven in het [service definitie bestand](cloud-services-model-and-package.md#csdef). De grootte van de rol bepaalt het aantal CPU-kernen, de geheugen capaciteit en de grootte van het lokale bestands systeem dat wordt toegewezen aan een actief exemplaar. Kies de grootte van de rol op basis van de resource vereiste van uw toepassing.

Hier volgt een voor beeld van het instellen van de rolgrootte die moet worden Standard_D2 voor een webrole-instantie:

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="changing-the-size-of-an-existing-role"></a>De grootte van een bestaande rol wijzigen

Als de aard van de wijzigingen in de werk belasting of nieuwe VM-grootten beschikbaar komen, kunt u de grootte van uw rol wijzigen. Hiervoor moet u de VM-grootte in het service definitie bestand wijzigen (zoals hierboven wordt weer gegeven), uw Cloud service opnieuw verpakken en implementeren.

>[!TIP]
> U kunt verschillende VM-grootten voor uw rol gebruiken in verschillende omgevingen (bijvoorbeeld testen versus productie). Een manier om dit te doen is door meerdere service definitie bestanden (. csdef) in uw project te maken en vervolgens verschillende Cloud service pakketten per omgeving te maken tijdens uw geautomatiseerde build met het CSPack-hulp programma. Zie [Wat is het Cloud Services-model en hoe kan ik het inpakken?](cloud-services-model-and-package.md) voor meer informatie over de elementen van een cloud services-pakket en hoe u deze kunt maken?
>
>

## <a name="get-a-list-of-sizes"></a>Een lijst met grootten ophalen
U kunt Power shell of de REST API gebruiken om een lijst met grootten weer te geven. De REST API wordt [hier](/previous-versions/azure/reference/dn469422(v=azure.100))beschreven. De volgende code is een Power shell-opdracht waarmee alle beschik bare grootten voor Cloud Services worden weer geven. 

```powershell
Get-AzureRoleSize | where SupportedByWebWorkerRoles -eq $true | select InstanceSize, RoleSizeLabel
```

## <a name="next-steps"></a>Volgende stappen
* Zie voor meer informatie: [Azure subscription and service limits, quotas, and constraints](../azure-resource-manager/management/azure-subscription-service-limits.md) (Limieten van Azure-abonnementen en -services, quota en beperkingen).
* Meer informatie [over de maximale grootte van Compute-vm's](../virtual-machines/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) voor HPC-workloads.
