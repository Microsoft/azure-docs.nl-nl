---
title: Toepassing implementeren in een cluster in PowerShell
description: Azure PowerShell-voorbeeldscript - Een toepassing implementeren in een Service Fabric-cluster.
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
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 1d25ede5ae871eddd965594224b518ec42525451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791287"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Een toepassing implementeren naar een Service Fabric-cluster

Met dit voorbeeldscript wordt een toepassingspakket naar een installatiekopieopslag van een cluster gekopieerd, wordt het toepassingstype in het cluster geregistreerd, wordt het overbodige toepassingspakket verwijderd en wordt een toepassingsexemplaar van het toepassingstype gemaakt.  Als er standaardservices zijn gedefinieerd in het toepassingsmanifest van het doeltoepassingstype, worden die services op dit moment gemaakt. Pas de parameters zo nodig aan. 

Installeer indien nodig de Service Fabric PowerShell-module met de [Service Fabric-SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Nadat het voorbeeldscript is uitgevoerd, kan het script in [Een toepassing verwijderen](service-fabric-powershell-remove-application.md) worden gebruikt om het toepassingsexemplaar te verwijderen, de registratie van het toepassingstype op te heffen en het toepassingspakket te verwijderen uit de installatiekopieopslag.

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster)| Hiermee wordt een verbinding gemaakt met een Service Fabric-cluster. |
|[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) | Hiermee wordt een toepassingspakket gekopieerd naar de installatiekopieopslag van het cluster.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype)| Hiermee worden een toepassingstype en -versie in het cluster geregistreerd. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication)| Hiermee wordt een toepassing van een geregistreerd toepassingstype gemaakt. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage) | Hiermee wordt een Service Fabric-toepassingspakket verwijderd uit de installatiekopieopslag.|

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure PowerShell](/powershell/azure/service-fabric/overview) voor meer informatie over de Service Fabric PowerShell-module.

Meer PowerShell-voorbeelden voor Azure Service Fabric vindt u in de [voorbeelden van Azure PowerShell](../service-fabric-powershell-samples.md).
