---
title: Configuratie van batch-pool migreren van Cloud Services naar Virtual Machines
description: Meer informatie over hoe u uw pool configuratie kunt bijwerken naar de nieuwste en aanbevolen configuratie
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: a176c4df1737a340a546b4ab7926447cd821350d
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200565"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machine"></a>De batch pool configuratie migreren van Cloud Services naar de virtuele machine

Op dit moment kunnen batch-Pools worden gemaakt met behulp van [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) of [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration). U wordt aangeraden alleen de configuratie van de virtuele machine te gebruiken, aangezien deze configuratie alle batch-mogelijkheden ondersteunt.

Cloud Services-configuratie Pools bieden geen ondersteuning voor een aantal van de huidige batch-functies en bieden geen ondersteuning voor nieuwe functies. U kunt geen nieuwe ' CloudServiceConfiguration-groepen maken of nieuwe knoop punten toevoegen aan bestaande Pools [na 29 februari 2024](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/).

Als uw batch-oplossingen momenteel ' cloudServiceConfiguration-groepen gebruiken, wordt aangeraden om zo snel mogelijk een wijziging aan te brengen in ' virtualMachineConfiguration '. Zo kunt u profiteren van alle mogelijkheden van de batch, zoals een uitgebreide [selectie van VM-serie](batch-pool-vm-sizes.md), Linux vm's, [containers](batch-docker-container-workloads.md), [Azure Resource Manager virtuele netwerken](batch-virtual-network.md)en [schijf versleuteling van knoop punten](disk-encryption.md).

## <a name="create-a-pool-using-virtual-machine-configuration"></a>Een pool maken met virtuele-machine configuratie

U kunt niet overstappen op een bestaande actieve groep waarbij ' cloudServiceConfiguration ' wordt gebruikt om ' virtualMachineConfiguration ' te gebruiken. In plaats daarvan moet u nieuwe groepen maken. Wanneer u de nieuwe ' virtualMachineConfiguration-groepen hebt gemaakt en al uw taken en taken hebt gerepliceerd, kunt u de oude ' cloudServiceConfiguration'pools die u niet meer gebruikt, verwijderen.

Met alle batch-Api's, opdracht regel Programma's, het Azure Portal en de Batch Explorer-gebruikers interface kunt u groepen maken met behulp van ' virtualMachineConfiguration '.

Zie de [.net-zelf studie](tutorial-parallel-dotnet.md) of de [python-zelf studie](tutorial-parallel-python.md)voor een overzicht van het proces voor het maken van groepen die virtualMachineConfiguration gebruiken.

## <a name="pool-configuration-differences"></a>Verschillen in groeps configuratie

Enkele van de belangrijkste verschillen tussen de twee configuraties zijn:

- cloudServiceConfiguration-groeps knooppunten gebruiken alleen Windows-besturings systemen. ' virtualMachineConfiguration-Pools kunnen Linux-of Windows-besturings systeem gebruiken.
- Vergeleken met groepen met ' cloudServiceConfiguration ', hebben groepen van ' virtualMachineConfiguration ' een uitgebreidere set mogelijkheden, zoals container ondersteuning, gegevens schijven en schijf versleuteling.
- Het starten en verwijderen van de groep en het knoop punt kunnen enigszins verschillen tussen de groepen ' cloudServiceConfiguration ' en ' virtualMachineConfiguration '.
- groeps knooppunten van het virtualMachineConfiguration gebruiken beheerde besturingssysteem schijven. Het [beheerde schijf type](../virtual-machines/disks-types.md) dat voor elk knoop punt wordt gebruikt, is afhankelijk van de grootte van de virtuele machine die u voor de groep hebt gekozen. Als de VM-grootte is opgegeven voor de groep, bijvoorbeeld ' Standard_D2s_v3 ', wordt een Premium SSD gebruikt. Als er een VM-grootte van een ' niet-s ' is opgegeven, bijvoorbeeld ' Standard_D2_v3 ', wordt een standaard HDD gebruikt.

   > [!IMPORTANT]
   > Net als bij Virtual Machines en Virtual Machine Scale Sets wordt de door het besturings systeem beheerde schijf die voor elk knoop punt wordt gebruikt, de kosten in rekening gebracht. Dit is een aanvulling op de kosten van de Vm's. Er zijn geen besturingssysteem schijf kosten voor cloudServiceConfiguration-knoop punten, omdat de besturingssysteem schijf is gemaakt op de lokale SSD-knoop punten.

## <a name="azure-data-factory-custom-activity-pools"></a>Aangepaste activiteiten groepen Azure Data Factory

Azure Batch groepen kunnen worden gebruikt om Data Factory aangepaste activiteiten uit te voeren. De groepen cloudServiceConfiguration die worden gebruikt om aangepaste activiteiten uit te voeren, moeten worden verwijderd en nieuwe ' virtualMachineConfiguration-groepen worden gemaakt.

Volg de volgende procedures bij het maken van uw nieuwe groepen om Data Factory aangepaste activiteiten uit te voeren:

- Pauzeer alle pijp lijnen voordat u de nieuwe groepen maakt en verwijder de oude, zodat er geen uitvoeringen worden onderbroken.
- Dezelfde groeps-id kan worden gebruikt om wijzigingen aan gekoppelde service configuraties te voor komen.
- Pijp lijnen hervatten wanneer er nieuwe groepen zijn gemaakt.

Zie [Azure batch gekoppelde service](../data-factory/compute-linked-services.md#azure-batch-linked-service) en [aangepaste activiteiten in een Data Factory pijp lijn](../data-factory/transform-data-using-dotnet-custom-activity.md) voor meer informatie over het gebruik van Azure batch om Data Factory aangepaste activiteiten uit te voeren.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [pool configuraties](nodes-and-pools.md#configurations).
- Meer informatie over [Aanbevolen procedures voor Pools](best-practices.md#pools).
- Zie de REST API referentie voor het [toevoegen van Pools](/rest/api/batchservice/pool/add) en [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration).