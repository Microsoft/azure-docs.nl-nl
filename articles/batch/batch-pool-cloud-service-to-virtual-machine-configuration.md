---
title: Configuratie van batch-pool migreren van Cloud Services naar Virtual Machines
description: Meer informatie over hoe u uw pool configuratie kunt bijwerken naar de nieuwste en aanbevolen configuratie
ms.topic: how-to
ms.date: 1/6/2021
ms.openlocfilehash: d987a185efb6593fd541dd14fa74b6c4d3ca41be
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234304"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machines"></a>Configuratie van batch-pool migreren van Cloud Services naar Virtual Machines

Batch-Pools kunnen worden gemaakt met behulp van [cloudServiceConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) of [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration). ' virtualMachineConfiguration ' is de aanbevolen configuratie, omdat deze alle batch-mogelijkheden ondersteunt. cloudServiceConfiguration-groepen bieden geen ondersteuning voor alle functies en er zijn geen nieuwe functies gepland.

Als u ' cloudServiceConfiguration-groepen gebruikt, wordt u ten zeerste aangeraden om de groep ' virtualMachineConfiguration ' te gebruiken. Zo kunt u profiteren van alle mogelijkheden van de batch, zoals een uitgebreide [selectie van VM-serie](batch-pool-vm-sizes.md), Linux vm's, [containers](batch-docker-container-workloads.md), [Azure Resource Manager virtuele netwerken](batch-virtual-network.md)en [schijf versleuteling van knoop punten](disk-encryption.md).

In dit artikel wordt beschreven hoe u migreert naar ' virtualMachineConfiguration '.

## <a name="new-pools-are-required"></a>Nieuwe groepen zijn vereist

Bestaande actieve Pools kunnen niet worden bijgewerkt vanuit cloudServiceConfiguration naar virtualMachineConfiguration. nieuwe Pools moeten worden gemaakt. Het maken van Pools met ' virtualMachineConfiguration ' wordt ondersteund door alle batch-Api's, opdracht regel Programma's, Azure Portal en de Batch Explorer-gebruikers interface.

**De [.net](tutorial-parallel-dotnet.md) -en [python](tutorial-parallel-python.md) -zelf studies bieden voor beelden van het maken van een pool met behulp van ' virtualMachineConfiguration '.**

## <a name="pool-configuration-differences"></a>Verschillen in groeps configuratie

Bij het bijwerken van de groeps configuratie moet rekening worden gehouden met het volgende:

- groeps knooppunten ' cloudServiceConfiguration ' zijn altijd Windows OS, ' virtualMachineConfiguration ' kunnen bestaan uit Linux-of Windows-besturings systemen.
- Vergeleken met groepen met ' cloudServiceConfiguration ', hebben groepen van ' virtualMachineConfiguration ' een uitgebreidere set mogelijkheden, zoals container ondersteuning, gegevens schijven en schijf versleuteling.
- groeps knooppunten van het virtualMachineConfiguration gebruiken beheerde besturingssysteem schijven. Het [beheerde schijf type](../virtual-machines/disks-types.md) dat voor elk knoop punt wordt gebruikt, is afhankelijk van de grootte van de virtuele machine die u voor de groep hebt gekozen. Als de VM-grootte is opgegeven voor de groep, bijvoorbeeld ' Standard_D2s_v3 ', wordt een Premium SSD gebruikt. Als er een VM-grootte van een ' niet-s ' is opgegeven, bijvoorbeeld ' Standard_D2_v3 ', wordt een standaard HDD gebruikt.

   > [!IMPORTANT]
   > Net als bij Virtual Machines en Virtual Machine Scale Sets wordt de door het besturings systeem beheerde schijf die voor elk knoop punt wordt gebruikt, de kosten in rekening gebracht. Dit is een aanvulling op de kosten van de Vm's. Er zijn geen kosten voor de besturingssysteem schijf voor cloudServiceConfiguration-knoop punten omdat de besturingssysteem schijf is gemaakt op de lokale SSD-knoop punten.

- Het starten en verwijderen van de groep en het knoop punt kunnen enigszins verschillen tussen de groepen ' cloudServiceConfiguration ' en ' virtualMachineConfiguration '.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [pool configuraties](nodes-and-pools.md#configurations).
- Meer informatie over [Aanbevolen procedures voor Pools](best-practices.md#pools).
- REST API verwijzing voor de toevoeging van een [pool](https://docs.microsoft.com/rest/api/batchservice/pool/add) en [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration).
