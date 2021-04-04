---
title: Problemen met de installatie van uw lokale Azure Service Fabric-cluster oplossen
description: In dit artikel vindt u een aantal suggesties voor het oplossen van problemen met uw lokale ontwikkel cluster
ms.topic: conceptual
ms.date: 02/23/2018
ms.openlocfilehash: 20948cd1626c02d73fb6e9ef096b552bbab627fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575904"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Problemen met de instellingen van uw lokale ontwikkelingscluster oplossen
Als u een probleem ondervindt tijdens de interactie met uw lokale Azure Service Fabric Development-cluster, raadpleegt u de volgende suggesties voor mogelijke oplossingen.

## <a name="cluster-setup-failures"></a>Fouten bij het instellen van het cluster
### <a name="cannot-clean-up-service-fabric-logs"></a>Kan Service Fabric-logboeken niet opruimen
#### <a name="problem"></a>Probleem
Tijdens het uitvoeren van het DevClusterSetup-script ziet u de volgende fout:

```output
Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
At line:1 char:1 + .\DevClusterSetup.ps1
+ ~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
+ FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1
```

#### <a name="solution"></a>Oplossing
Sluit het huidige Power shell-venster en open een nieuw Power shell-venster als beheerder. U kunt het script nu uitvoeren.

## <a name="cluster-connection-failures"></a>Verbindings fouten van cluster

### <a name="type-initialization-exception"></a>Uitzonde ring voor type initialisatie
#### <a name="problem"></a>Probleem
Wanneer u verbinding maakt met het cluster in Power shell, ziet u de fout TypeInitializationException voor System. Fabric. common. AppTrace.

#### <a name="solution"></a>Oplossing
De padvariabele is niet juist ingesteld tijdens de installatie. Meld u af bij Windows en meld u weer aan. Hiermee vernieuwt u het pad.

### <a name="cluster-connection-fails-with-object-is-closed"></a>De cluster verbinding is mislukt, omdat het object is gesloten
#### <a name="problem"></a>Probleem
Een aanroep van Connect-ServiceFabricCluster mislukt met een fout als de volgende:

```output
Connect-ServiceFabricCluster : The object is closed.
At line:1 char:1
+ Connect-ServiceFabricCluster
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
+ FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster
```

#### <a name="solution"></a>Oplossing
Sluit het huidige Power shell-venster en open een nieuw Power shell-venster als beheerder.

### <a name="fabric-connection-denied-exception"></a>Uitzonde ring bij geweigerde infrastructuur verbinding
#### <a name="problem"></a>Probleem
Wanneer u fouten opspoort vanuit Visual Studio, wordt er een FabricConnectionDeniedException-fout weer gegeven.

#### <a name="solution"></a>Oplossing
Deze fout treedt meestal op wanneer u een service-hostproces hand matig probeert te starten.

Zorg ervoor dat er geen service projecten zijn ingesteld als opstart projecten in uw oplossing. Alleen Service Fabric toepassings projecten moeten worden ingesteld als opstart projecten.

> [!TIP]
> Als na de installatie het lokale cluster op een abnormale manier werkt, kunt u het opnieuw instellen met behulp van de systeem systeemvak-toepassing lokaal Cluster beheer. Hiermee wordt het bestaande cluster verwijderd en een nieuwe ingesteld. Houd er rekening mee dat alle geïmplementeerde toepassingen en bijbehorende gegevens worden verwijderd.
> 
> 

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het cluster en het oplossen van problemen met systeem status rapporten](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualisern van uw cluster met Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

