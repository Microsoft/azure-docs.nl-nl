---
title: DC-serie-Azure Virtual Machines
description: Specificaties voor de Vm's van de DC-serie.
author: susaxen
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: 677f4df0873f8b72d40dd373035111e2e0002491
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549227"
---
# <a name="dcsv2-series"></a>DCsv2-serie


De DCsv2-serie kan u helpen de vertrouwelijkheid en integriteit van uw gegevens en code te beschermen tijdens de verwerking in de open bare Cloud. Deze machines worden ondersteund door de nieuwste generatie Intel XEON E-2288G-processor met SGX-technologie. Met de Intel Turbo Boost-technologie kunnen deze machines tot wel 5.0 GHz gaan. Met DCsv2-serie-instanties kunnen klanten veilige, op enclave gebaseerde toepassingen bouwen om hun code en gegevens te beschermen terwijl ze worden gebruikt.

Voor beelden van use cases zijn: vertrouwelijk delen van gegevens, fraude detectie, anti-Money witwassen, Block Chain, analyse van vertrouwelijk gebruik, intelligentie analyse en vertrouwelijke machine learning.

[Premium Storage](premium-storage-performance.md): ondersteund *<br> 
 [Premium Storage caching](premium-storage-performance.md): ondersteunde <br> 
 [Livemigratie](maintenance-and-updates.md): niet-ondersteunde updates voor het <br> 
 [geheugen](maintenance-and-updates.md): niet ondersteund ondersteuning voor het <br> 
 [genereren van vm's](generation-2.md): generatie 2 <br> 
 [versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): ondersteund (* vereist een minimum van 4 vCPU *) <br>
[Tijdelijke besturingssysteem schijven](ephemeral-os-disks.md): ondersteund <br>

* Met uitzonde ring van Standard_DC8_v2 <br>

| Grootte             | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Maximum aantal Nic's/verwachte netwerk bandbreedte (MBps) | EPC-geheugen (MiB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128                                                                                         | 2   | 168                                         |

- Vm's uit de DCsv2-serie zijn [twee vm's van de tweede generatie](./generation-2.md#creating-a-generation-2-vm) en ondersteunen alleen `Gen2` installatie kopieën.
- Momenteel beschikbaar in de regio's die [hier](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)worden weer gegeven.
- Vorige generatie van vertrouwelijk Compute-Vm's: [gelijkstroom reeks](sizes-previous-gen.md#preview-dc-series)
- DCsv2 Vm's maken met behulp van de [Azure Portal](./linux/quick-create-portal.md) of [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)



## <a name="other-sizes-and-information"></a>Andere grootten en informatie

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerd voor geheugen](sizes-memory.md)
- [Geoptimaliseerd voor opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

Prijs calculator: [prijs calculator](https://azure.microsoft.com/pricing/calculator/)

Meer informatie over schijven typen: [schijf typen](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
