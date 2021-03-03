---
title: Uw beheerde Service Fabric-cluster configureren (preview-versie)
description: Meer informatie over het configureren van uw Service Fabric Managed cluster voor automatische upgrades van besturings systemen, NSG-regels en meer.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 44b1b949fe314231cb44f190c31b53903e47a904
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732629"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Configuratie opties voor Service Fabric Managed cluster (preview)

Naast het selecteren van de [service Fabric Managed cluster SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus) bij het maken van uw cluster, zijn er een aantal andere manieren om dit te configureren. In de huidige preview kunt u het volgende doen:

* [Netwerk opties](how-to-managed-cluster-networking.md) configureren voor uw cluster
* Een [extensie voor virtuele-machine schaal sets](how-to-managed-cluster-vmss-extension.md) toevoegen aan een knooppunt type
* [Beheerde identiteit](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) configureren voor de knooppunt typen
* [Automatische upgrades van besturings systemen](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades) voor uw knoop punten inschakelen
* Schakel de [versleuteling van besturings systemen en gegevens schijven](how-to-enable-managed-cluster-disk-encryption.md) in op uw knoop punten

## <a name="enable-automatic-os-image-upgrades"></a>Automatische upgrade van besturings systeem-installatie kopieën inschakelen

U kunt ervoor kiezen om automatische installatie kopieën van besturings systemen in te scha kelen naar de virtuele machines waarop uw beheerde cluster knooppunten worden uitgevoerd. Hoewel de virtuele-machine Scale set resources namens u worden beheerd met Service Fabric beheerde clusters, is het uw keuze om automatische installatie kopieën van besturings systemen in te scha kelen voor uw cluster knooppunten. Net als bij [klassieke service Fabric](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) clusters worden beheerde cluster knooppunten niet standaard bijgewerkt, om onbedoelde onderbrekingen voor uw cluster te voor komen.

Automatische upgrades van besturings systemen inschakelen:

* Gebruik de `2021-01-01-preview` (of latere) versie van *micro soft. ServiceFabric/Managedclusters* en *micro soft. ServiceFabric/managedclusters/nodetypes* resources
* Stel de eigenschap van het cluster `enableAutoOSUpgrade` in op *True*
* Stel de eigenschap resource van het cluster nodeTypes `vmImageVersion` in op de *nieuwste*

Bijvoorbeeld:

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

Wanneer deze functie is ingeschakeld, worden er in het beheerde Cluster installatie kopieën van besturings systemen in het beheer van Service Fabric gestart. Als er een nieuwe versie van het besturings systeem beschikbaar is, worden de cluster knooppunt typen (virtuele-machine schaal sets) een voor een bijgewerkt. Service Fabric runtime-upgrades worden alleen uitgevoerd nadat er is bevestigd dat er geen installatie kopieën van het besturings systeem voor het cluster knooppunt worden bijgewerkt.

Als een upgrade mislukt, wordt Service Fabric na 24 uur opnieuw geprobeerd, gedurende een maximum van drie nieuwe pogingen. Net als bij klassiek (onbeheerd) Service Fabric upgrades, kunnen beschadigde apps of knoop punten de upgrade van de installatie kopie van het besturings systeem blok keren.

Zie [automatische besturingssysteem installatie kopieën met virtuele-machine schaal sets van Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)voor meer informatie over het bijwerken van installatie kopieën.

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Service Fabric beheerde clusters](overview-managed-cluster.md)

[Sjablonen voor Service Fabric-clusters](https://github.com/Azure-Samples/service-fabric-cluster-templates)
