---
title: Voorbeeld van de implementatie van Azure Service Fabric CLI (sfctl) met behulp van een script
description: Een toepassing implementeren naar een Azure Service Fabric-cluster met Azure Service Fabric CLI
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 04/16/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: ff40dc62b4dcd622156a78518bbdcb6b9b430644
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75526597"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster-using-the-service-fabric-cli"></a>Een toepassing implementeren naar een Service Fabric-cluster met behulp van de Service Fabric-CLI

Met dit voorbeeldscript wordt een toepassingspakket naar een installatieopslag van een cluster gekopieerd, wordt het toepassingstype in het cluster geregistreerd en wordt een instantie van het toepassingstype gemaakt. Dit is het moment waarop ook alle eventuele standaardservices worden gemaakt.

Installeer indien nodig ook [Service Fabric CLI](../service-fabric-cli.md).

## <a name="sample-script"></a>Voorbeeldscript

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Wanneer dit is gebeurd, kan het [verwijderscript](cli-remove-application.md) worden gebruikt om de toepassing te verwijderen. Met het verwijderscript wordt de instantie van de toepassing verwijderd, de registratie van het toepassingstype ongedaan gemaakt en het toepassingspakket verwijderd uit de installatiekopieopslag.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Service Fabric CLI-documentatie ](../service-fabric-cli.md)voor meer informatie.

Meer Service Fabric CLI-voorbeelden voor Azure Service Fabric zijn te vinden in de [Voorbeelden van Azure Service Fabric CLI](../samples-cli.md).
