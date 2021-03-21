---
title: VM-grootten en-installatie kopieën kiezen voor Pools
description: Kiezen uit de beschik bare VM-grootten en versies van besturings systemen voor reken knooppunten in Azure Batch Pools
ms.topic: conceptual
ms.date: 03/08/2021
ms.custom: seodec18
ms.openlocfilehash: 42b8743fac6a6c64e98271490f0bfc4671fa7698
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455192"
---
# <a name="choose-a-vm-size-and-image-for-compute-nodes-in-an-azure-batch-pool"></a>Een VM-grootte en-afbeelding kiezen voor reken knooppunten in een Azure Batch pool

Wanneer u een knooppunt grootte voor een Azure Batch groep selecteert, kunt u kiezen uit vrijwel alle VM-grootten die beschikbaar zijn in Azure. Azure biedt tal van grootten voor Linux-en Windows-Vm's voor verschillende werk belastingen.

## <a name="supported-vm-series-and-sizes"></a>Ondersteunde VM-reeksen en-groottes

Er zijn enkele uitzonde ringen en beperkingen voor het kiezen van een VM-grootte voor de batch-pool:

- Sommige VM-reeksen of VM-grootten worden niet ondersteund in batch.
- Sommige VM-grootten zijn beperkt en moeten specifiek worden ingeschakeld voordat ze kunnen worden toegewezen.

### <a name="pools-in-virtual-machine-configuration"></a>Groepen in de configuratie van de virtuele machine

Batch-Pools in de virtuele-machine configuratie ondersteunen bijna alle [VM-grootten](../virtual-machines/sizes.md). Raadpleeg de volgende tabel voor meer informatie over ondersteunde grootten en beperkingen.

| VM-reeks  | Ondersteunde grootten |
|------------|---------|
| Basic A | Alle grootten *behalve* Basic_A0 (a0) |
| A | Alle grootten *behalve* Standard_A0, Standard_A8, Standard_A9, Standard_A10, Standard_A11 |
| Av2 | Alle grootten |
| B | Niet ondersteund |
| DC | Niet ondersteund |
| Dv2, DSv2 | Alle grootten |
| Dv3, Dsv3 | Alle grootten |
| Dav4, Dasv4 | Alle grootten |
| Ddv4, Ddsv4 |  Alle grootten |
| Dv4, Dsv4 | Niet ondersteund |
| Ev3, Esv3 | Alle grootten, met uitzonde ring van E64is_v3 |
| Eav4, Easv4 | Alle grootten |
| Edv4, Edsv4 |  Alle grootten |
| Ev4, Esv4 | Niet ondersteund |
| F, FS | Alle grootten |
| Fsv2 | Alle grootten |
| G, GS | Alle grootten |
| H | Alle grootten |
| HB | Alle grootten |
| HBv2 | Alle grootten |
| KM | Alle grootten |
| Ls | Alle grootten |
| Lsv2 | Alle grootten |
| M | Alle grootten |
| Mv2<sup>1</sup> | Alle grootten |
| NC | Alle grootten |
| NCv2 | Alle grootten |
| NCv3 | Alle grootten |
| NCasT4_v3 | Alle grootten |
| ND | Alle grootten |
| NDv2 | Geen-nog niet beschikbaar |
| NV | Alle grootten |
| NVv3 | Alle grootten |
| NVv4 | Alle grootten |
| SAP HANA | Niet ondersteund |

<sup>1</sup> deze VM-serie kan alleen worden gebruikt met VM-installatie kopieën van generatie 2.

### <a name="using-generation-2-vm-images"></a>VM-installatie kopieën van generatie 2 gebruiken

Sommige VM-reeksen, zoals [Mv2](../virtual-machines/mv2-series.md), kunnen alleen worden gebruikt met [VM-installatie kopieën van generatie 2](../virtual-machines/generation-2.md). VM-installatie kopieën van de 2e generatie zijn opgegeven als een VM-installatie kopie, met behulp van de eigenschap SKU van de configuratie [' imageReference '](/rest/api/batchservice/pool/add#imagereference) ; de teken reeksen ' SKU ' hebben een achtervoegsel zoals '-G2 ' of '-Gen2 '. Gebruik de [lijst met ondersteunde installatie](/rest/api/batchservice/account/listsupportedimages) kopieën, [Power shell](/powershell/module/az.batch/get-azbatchsupportedimage)of [Azure cli](/cli/azure/batch/pool/supported-images), voor een lijst met VM-installatie kopieën die worden ondersteund door batch, inclusief installatie kopieën van de 2e generatie.

### <a name="pools-in-cloud-service-configuration"></a>Groepen in de Cloud service configuratie

Batch-Pools in de Cloud service configuratie ondersteunen alle [VM-grootten voor Cloud Services](../cloud-services/cloud-services-sizes-specs.md) **, met uitzonde ring** van de volgende opties:

| VM-reeks  | Niet-ondersteunde grootten |
|------------|-------------------|
| A-serie   | Extra klein       |
| Av2-serie | Standard_A1_v2, Standard_A2_v2 Standard_A2m_v2 |

## <a name="size-considerations"></a>Overwegingen over grootte

- **Toepassings vereisten** : Houd rekening met de kenmerken en vereisten van de toepassing die u op de knoop punten uitvoert. Houd ook rekening met het feit of bijvoorbeeld de toepassing meerdere threads heeft en hoeveel geheugen deze gebruikt. Zo kunt u gemakkelijker de meest geschikte en voordeligste knooppuntgrootte bepalen. Voor [mpi-werk belastingen](batch-mpi.md) of CUDA-toepassingen met meerdere instanties kunt u respectievelijk gespecialiseerde VM-grootten met [HPC](../virtual-machines/sizes-hpc.md) of [GPU-](../virtual-machines/sizes-gpu.md) capaciteit overwegen. Zie voor meer informatie [gebruik van RDMA-compatibele of GPU-ingeschakelde instanties in batch-Pools](batch-pool-compute-intensive-sizes.md).

- **Taken per knoop punt** : het is gebruikelijk om een knooppunt grootte te selecteren, ervan uitgaande dat er één taak tegelijk op een knoop punt wordt uitgevoerd. Het kan echter handig zijn om meerdere taken (en dus meerdere toepassings exemplaren) parallel op reken knooppunten [uit te voeren](batch-parallel-node-tasks.md) tijdens de taak uitvoering. In dit geval is het gebruikelijk om een multicore-knooppunt grootte te kiezen die geschikt is voor de verhoogde vraag van parallelle taak uitvoering.

- **Laad niveaus voor verschillende taken** : alle knoop punten in een groep hebben dezelfde grootte. Als u toepassingen met verschillende systeemvereisten en/of workloadniveaus wilt uitvoeren, moet u afzonderlijke pools maken.

- **Beschik baarheid van regio** : een VM-serie of-grootte is mogelijk niet beschikbaar in de regio's waar u uw batch-accounts maakt. Zie [producten beschikbaar per regio](https://azure.microsoft.com/regions/services/)om te controleren of er een grootte beschikbaar is.

- **Quota's** : de [kernen Quota's](batch-quota-limit.md#resource-quotas) in uw batch-account kunnen het aantal knoop punten van een bepaalde grootte beperken die u aan een batch-pool kunt toevoegen. Als dat nodig is, kunt u [een quotum verhoging aanvragen](batch-quota-limit.md#increase-a-quota).

- **Groeps configuratie** : in het algemeen hebt u meer opties voor VM-grootte wanneer u een groep in de virtuele-machine configuratie maakt, vergeleken met de configuratie van de Cloud service.

## <a name="supported-vm-images"></a>Ondersteunde VM-installatiekopieën

Gebruik een van de volgende Api's om een lijst op te halen met Windows-en Linux VM-installatie kopieën die momenteel worden ondersteund door batch, met inbegrip van de SKU-Id's van de node-agent voor elke installatie kopie:

- Batch-service REST API: [ondersteunde installatie kopieën weer geven](/rest/api/batchservice/account/listsupportedimages)
- Power shell: [Get-AzBatchSupportedImage](/powershell/module/az.batch/get-azbatchsupportedimage)
- Azure CLI: [AZ-batch pool ondersteund-installatie kopieën](/cli/azure/batch/pool/supported-images)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Werkstroom van de batch-service en primaire resources](batch-service-workflow-features.md) als pools, knooppunten, jobs en taken.
- Zie voor meer informatie over het gebruik van Compute-intensieve VM-grootten [RDMA-compatibele of GPU-compatibele instanties in batch-Pools gebruiken](batch-pool-compute-intensive-sizes.md).
