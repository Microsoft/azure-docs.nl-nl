---
title: Groottes van Azure VM-GPU | Microsoft Docs
description: Geeft een lijst van de verschillende geoptimaliseerde GPU-grootten die beschikbaar zijn voor virtuele machines in Azure. Bevat informatie over het aantal Vcpu's, gegevens schijven en Nic's en de opslag doorvoer en netwerk bandbreedte voor grootten in deze serie.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 09d62ea5f2db77c14e8faff44de7fb3ce759c6fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709732"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Grootten van virtuele machines geoptimaliseerd voor GPU

Geoptimaliseerde VM-grootten zijn gespecialiseerde virtuele machines die beschikbaar zijn met één, meerdere of gedeeltelijke Gpu's. Deze grootten zijn ontworpen voor computerintensieve, grafisch intensieve en visualisatie werk belastingen. Dit artikel bevat informatie over het aantal en het type Gpu's, Vcpu's, gegevens schijven en Nic's. Opslag doorvoer en netwerk bandbreedte worden ook voor elke grootte in deze groepering opgenomen.

- De grootten van de [NCv3-serie](ncv3-series.md) en de [NC T4_v3-serie](nct4-v3-series.md) zijn geoptimaliseerd voor computerintensieve toepassingen met GPU-snelheid. Enkele voor beelden zijn CUDA en OpenCL toepassingen en simulaties, AI en diep gaande lessen. De NC T4 v3-serie is gericht op het afleiden van workloads met NVIDIA Tesla T4 GPU en AMD EPYC2 Rome-processor. De NCv3-serie is gericht op High-Performance Computing en AI-workloads met de Tesla V100 GPU van NVIDIA.

- De grootte van de [NDv2-serie](ndv2-series.md) is gericht op het opschalen en uitschalen van diep gaande trainings toepassingen. De NDv2-serie gebruikt de NVIDIA Volta V100 en de Intel Xeon Platinum 8168-processor (Skylake).

- De grootte van de [nv-](nv-series.md) en [NVv3-serie](nvv3-series.md) is geoptimaliseerd en ontworpen voor externe visualisatie, streaming, games, code ring en VDI-scenario's met behulp van frameworks zoals OpenGL en DirectX. Deze Vm's worden ondersteund door de NVIDIA Tesla M60 GPU.

- [NVv4-serie](nvv4-series.md) VM-grootten geoptimaliseerd en ontworpen voor VDI en externe visualisatie. Met gepartitioneerde Gpu's biedt NVv4 de juiste grootte voor werk belastingen waarvoor kleinere GPU-resources zijn vereist. Deze Vm's worden ondersteund door de AMD Radeon instinct MI25 GPU. NVv4 Vm's ondersteunen momenteel alleen Windows-gast besturingssystemen.

## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

Als u gebruik wilt maken van de GPU-mogelijkheden van Vm's in de Azure N-serie, moeten de NVIDIA-of AMD GPU-Stuur Programma's zijn geïnstalleerd.

- Voor virtuele machines die worden ondersteund door NVIDIA-Gpu's, [worden de juiste](./extensions/hpccompute-gpu-windows.md) NVIDIA-CUDA of raster Stuur Programma's geïnstalleerd. De uitbrei ding installeren of beheren met de Azure Portal of hulpprogram ma's, zoals Azure PowerShell of Azure Resource Manager sjablonen. Zie de [documentatie over NVIDIA GPU-Stuur Programma's](./extensions/hpccompute-gpu-windows.md) voor ondersteunde besturings systemen en implementaties tappen. Zie [extensies en functies van virtuele Azure-machines](./extensions/overview.md)voor algemene informatie over VM-extensies.

   U kunt ook de NVIDIA GPU-Stuur Programma's hand matig installeren. Zie [NVIDIA GPU-Stuur Programma's installeren op vm's uit de n-serie waarop Windows wordt uitgevoerd](./windows/n-series-driver-setup.md) of [Installeer NVIDIA GPU-Stuur Programma's op vm's met n-serie waarop Linux wordt uitgevoerd](./linux/n-series-driver-setup.md) voor ondersteunde besturings systemen, stuur Programma's, installatie en verificaties tappen.

- Voor virtuele machines die worden ondersteund door AMD-Gpu's, worden met de extensie van het [AMD GPU-stuur programma](./extensions/hpccompute-amd-gpu-windows.md) de juiste AMD-Stuur Programma's De uitbrei ding installeren of beheren met de Azure Portal of hulpprogram ma's, zoals Azure PowerShell of Azure Resource Manager sjablonen. Zie [extensies en functies van virtuele Azure-machines](./extensions/overview.md)voor algemene informatie over VM-extensies.

   U kunt ook hand matig AMD GPU-Stuur Programma's installeren. Zie [AMD GPU-Stuur Programma's installeren op vm's met N-serie waarop Windows wordt uitgevoerd](./windows/n-series-amd-driver-setup.md) voor ondersteunde besturings systemen, stuur Programma's, installatie en verificaties tappen.

## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

- Zie [producten beschikbaar per regio](https://azure.microsoft.com/regions/services/)voor de beschik baarheid van virtuele machines uit de N-serie.

- Vm's uit de N-serie kunnen alleen worden geïmplementeerd in het Resource Manager-implementatie model.

- Vm's uit de N-serie verschillen in het type Azure Storage ze voor hun schijven ondersteunen. NC-en NV-Vm's bieden alleen ondersteuning voor VM-schijven die worden ondersteund door de standaard Disk Storage (HDD). Alle andere GPU-Vm's ondersteunen VM-schijven die worden ondersteund door Standard Disk Storage en Premium Disk Storage (SSD).

- Als u meer dan een paar virtuele machines uit de N-serie wilt implementeren, kunt u een abonnement op basis van betalen naar gebruik of andere aankoop opties overwegen. Als u een [gratis account van Azure](https://azure.microsoft.com/free/) gebruikt, kunt u slechts een paar Azure Compute-resources van Azure gebruiken.

- Mogelijk moet u het quotum voor kernen (per regio) in uw Azure-abonnement verhogen en de afzonderlijke quota voor NC-, NCv2-, NCv3-, ND-, NDv2-, NV-of NVv2-kernen verhogen. Als u een quotum toename wilt aanvragen, opent u gratis [een aanvraag voor een online klant ondersteuning](../azure-portal/supportability/how-to-create-azure-support-request.md) . De standaard limieten kunnen variëren, afhankelijk van de categorie van uw abonnement.

## <a name="other-sizes"></a>Andere grootten

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Geoptimaliseerd voor geheugen](sizes-memory.md)
- [Geoptimaliseerd voor opslag](sizes-storage.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
