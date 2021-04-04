---
title: Zelfstandig cluster opschonen
description: In deze zelfstudie leert u hoe u AWS- of Azure-resources voor uw zelfstandige Service Fabric-cluster kunt verwijderen.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 0d46e9068a311594f779411c3ccee2b408febb3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842883"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Zelfstudie: Zelfstandig cluster opschonen

Zelfstandige Service Fabric-clusters geven u de optie uw eigen omgeving voor het hosten van Service Fabric te kiezen. In deze reeks zelfstudies maakt u een zelfstandig cluster dat in AWS of Azure wordt gehost. Vervolgens implementeert u een toepassing in het cluster.

Deze zelfstudie is deel vier een serie. In dit deel van de reeks wordt uitgelegd hoe u de AWS- of Azure-resources kunt verwijderen die u hebt gemaakt voor het hosten van uw Service Fabric-cluster.

In dit artikel leert u:

> [!div class="checklist"]
> * Een Service Fabric-cluster verwijderen
> * Uw AWS- of Azure-resources verwijderen

## <a name="remove-a-service-fabric-cluster"></a>Een Service Fabric-cluster verwijderen

1. Ga met behulp van RDP naar de VM die u hebt gebruikt voor de installatie van Service Fabric.
2. Open PowerShell.
3. Ga naar de uitgepakte map uit de tweede zelfstudie.
4. Voer de volgende opdracht uit om het Service Fabric-cluster te verwijderen:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Voer `Y` in wanneer u daarom wordt gevraagd. Als de opdracht is gelukt, ziet de uitvoer er als volgt uit (maar dan met uw eigen IP-adressen):

  ```powershell
  Best Practices Analyzer completed successfully.
  Removing configuration from machine 172.31.21.141
  Removing configuration from machine 172.31.27.1
  Removing configuration from machine 172.31.20.163
  Configuration removed from machine 172.31.21.141
  Configuration removed from machine 172.31.27.1
  Configuration removed from machine 172.31.20.163
  The cluster is successfully removed.
  ```

## <a name="delete-aws-resources"></a>AWS-resources verwijderen

1. Meld u aan bij uw AWS-account.
2. Ga naar de EC2-console.
3. Selecteer de drie knooppunten die u hebt gemaakt in deel een van de zelfstudie.
4. Selecteer **Actions** > **Instance State** > **Terminate**.

## <a name="delete-azure-resources"></a>Azure-resources verwijderen

1. Meld u aan bij Azure Portal.
2. Ga naar de sectie **Virtual Machines**.
3. Schakel de selectievakjes in bij de drie knooppunten die u hebt gemaakt in deel een van de zelfstudie.
4. Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u de resources kunt verwijderen die u in de vorige stappen hebt gemaakt.

> [!div class="checklist"]
> * Uw resources opschonen

> [!div class="nextstepaction"]
> [Terug naar het begin](service-fabric-tutorial-standalone-create-infrastructure.md)
