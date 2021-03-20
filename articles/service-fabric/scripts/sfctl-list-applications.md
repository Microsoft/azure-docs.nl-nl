---
title: Toepassing noemen op een cluster in sfctl
description: Voorbeeld van Service Fabric CLI-script - toepassingen opvragen die zijn ingericht in een Service Fabric-cluster.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 04/13/2018
ms.author: atsenthi
ms.custom: ''
ms.openlocfilehash: 41685e53eb0915f54bdc2d678191e5b767990dde
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75610264"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Toepassingen opvragen die worden uitgevoerd in een Service Fabric-cluster

Dit voorbeeldscript maakt verbinding met een Service Fabric-cluster en geeft vervolgens een lijst weer met alle ingerichte toepassingen.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Service Fabric CLI-documentatie ](../service-fabric-cli.md)voor meer informatie.

Meer Service Fabric CLI-voorbeelden voor Azure Service Fabric zijn te vinden in de [Voorbeelden van Azure Service Fabric CLI](../samples-cli.md).
