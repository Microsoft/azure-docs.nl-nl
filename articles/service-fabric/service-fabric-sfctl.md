---
title: Azure Service Fabric CLI-sfctl
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten en subgroepen.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fc317345155a6807a20d342e2cefd0701b20f180
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86257050"
---
# <a name="sfctl"></a>sfctl
Opdrachten voor het beheren van Service Fabric clusters en entiteiten. Deze versie is compatibel met Service Fabric 7,0-runtime.

Opdrachten volgen het patroon van de zelfstandig naam woord. Zie subgroepen voor meer informatie.

## <a name="subgroups"></a>Subgroepen
|Subgroep|Beschrijving|
| --- | --- |
| [toepassing](service-fabric-sfctl-application.md) | Toepassingen en toepassings typen maken, verwijderen en beheren. |
| [chaos](service-fabric-sfctl-chaos.md) | Starten, stoppen en rapporteren op de chaos-test service. |
| [cluster](service-fabric-sfctl-cluster.md) | Service Fabric clusters selecteren, beheren en uitvoeren. |
| [Compose](service-fabric-sfctl-compose.md) | Docker opstellen toepassingen maken, verwijderen en beheren. |
| [verpakking](service-fabric-sfctl-container.md) | Container gerelateerde opdrachten uitvoeren op een cluster knooppunt. |
| [evenementen](service-fabric-sfctl-events.md) | Gebeurtenissen ophalen uit het gebeurtenissen archief (als de Event Store-service al is geïnstalleerd). |
| [ontbreekt](service-fabric-sfctl-is.md) | Query's uitvoeren en opdrachten verzenden naar de infrastructuur service. |
| [strekt](service-fabric-sfctl-mesh.md) | Service Fabric-mesh-toepassingen verwijderen en beheren. |
| [node](service-fabric-sfctl-node.md) | De knoop punten beheren die een cluster vormen. |
| [partitie](service-fabric-sfctl-partition.md) | Zoek en beheer partities voor elke service. |
| [eigenschap](service-fabric-sfctl-property.md) | Eigenschappen voor opslaan en opvragen onder Service Fabric namen. |
| [exacte](service-fabric-sfctl-replica.md) | De replica's beheren die deel uitmaken van service partities. |
| [rpm](service-fabric-sfctl-rpm.md) | Query's uitvoeren en opdrachten verzenden naar de service reparatie beheer. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Zelfstandige Service Fabric-clusters beheren. |
| [service](service-fabric-sfctl-service.md) | Services, service typen en service pakketten maken, verwijderen en beheren. |
| [instellingen](service-fabric-sfctl-settings.md) | Instellingen lokaal configureren voor dit exemplaar van sfctl. |
| [store](service-fabric-sfctl-store.md) | Voer elementaire bewerkingen op bestands niveau uit in het cluster installatie kopie archief. |

## <a name="next-steps"></a>Volgende stappen
- [Stel](service-fabric-cli.md) de service Fabric cli in.
- Meer informatie over het gebruik van de Service Fabric CLI met behulp van de [voorbeeld scripts](./scripts/sfctl-upgrade-application.md).
