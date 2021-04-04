---
title: Een toepassing implementeren op een beheerde Service Fabric-cluster via PowerShell (preview)
description: In deze zelfstudie maakt u verbinding met een beheerde Service Fabric-cluster en implementeert u een toepassing via PowerShell.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 36a91d2852bcda5f958441b48ef4721d6ccc83c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91410201"
---
# <a name="tutorial-deploy-an-app-to-a-service-fabric-managed-cluster-preview"></a>Zelfstudie: Een app implementeren naar een beheerde Service Fabric-cluster (preview)

In deze reeks zelfstudies wordt het volgende besproken:

> [!div class="checklist"]
> * [Een beheerde Service Fabric-cluster implementeren](tutorial-managed-cluster-deploy.md)
> * [Het uitbreiden van een beheerde Service Fabric-cluster](tutorial-managed-cluster-scale.md)
> * [Het toevoegen en verwijderen van knooppunten in een beheerd Service Fabric-cluster](tutorial-managed-cluster-add-remove-node-type.md)
> * Een toepassing implementeren op een beheerde Service Fabric-cluster

In dit deel van de reeks wordt het volgende uitgelegd:

> [!div class="checklist"]
> * Verbinding maken met uw beheerde Service Fabric-cluster
> * Een toepassing uploaden naar een cluster
> * Een toepassing instantiëren in een cluster
> * Een toepassing verwijderen uit een cluster

## <a name="prerequisites"></a>Vereisten

* Een beheerde Service Fabric-cluster (zie [*Een beheerd cluster implementeren*](tutorial-managed-cluster-deploy.md)).

## <a name="connect-to-your-cluster"></a>Verbinding maken met uw cluster

Als u verbinding wilt maken met uw cluster, hebt u de vingerafdruk van het clustercertificaat nodig. U kunt deze waarde vinden in de uitvoer met clustereigenschappen van uw resource-implementatie of door een query uit te voeren op de clustereigenschappen van een bestaande resource.

De volgende opdracht kan worden gebruikt om een query uit te voeren op uw clusterresource voor de vingerafdruk van het clustercertificaat.

```powershell
$serverThumbprint = (Get-AzResource -ResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ServiceFabric/managedclusters/mysfcluster).Properties.clusterCertificateThumbprint
```

Met de vingerafdruk van het clustercertificaat bent u klaar om verbinding te maken met uw cluster.

```powershell
$connectionEndpoint = "mysfcluster.eastus2.cloudapp.azure.com:19000"
Connect-ServiceFabricCluster -ConnectionEndpoint $connectionEndpoint -KeepAliveIntervalInSec 10 `
      -X509Credential `
      -ServerCertThumbprint $serverThumbprint  `
      -FindType FindByThumbprint `
      -FindValue $clientThumbprint `
      -StoreLocation CurrentUser `
      -StoreName My

```

### <a name="upload-an-application-package"></a>Een toepassingspakket uploaden

In deze zelfstudie wordt gebruikgemaakt van het voorbeeld van de [Service Fabric-stemtoepassing](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy). Zie [Service Fabric toepassingen implementeren en verwijderen](service-fabric-deploy-remove-applications.md)voor meer informatie over de implementatie verwijderen Service Fabric-toepassingen via PowerShell.

> [!NOTE]
> In de beheerde Service Fabric-clusterpreview kunt u toepassingen niet rechtstreeks vanuit Visual Studio publiceren.

U moet eerst [de toepassing inpakken voor implementatie](service-fabric-package-apps.md). Voor deze zelfstudie volgt u de stappen voor het inpakken van een toepassing vanuit Visual Studio. Het is belangrijk dat het pad waar de toepassing is verpakt noteert, aangezien het gebruikt zal worden voor het onderstaand pad.

Zodra het toepassingspakket is gemaakt, kunt u het toepassingspakket uploaden naar uw cluster. Werk de `$path`-waarde bij om het pad voor uw toepassingspakket weer te geven en voer het volgende uit:

```powershell
$path = "C:\Users\<user>\Documents\service-fabric-dotnet-quickstart\Voting\pkg\Debug"
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage
Register-ServiceFabricApplicationType -ApplicationPathInImageStore Debug
```

### <a name="create-an-application"></a>Een app maken

U kunt een toepassing instantiëren vanuit elke toepassingstypeversie die is geregistreerd met behulp van de cmdlet New-ServiceFabricApplication. De naam van elke toepassing moet beginnen met het schema 'fabric:' en moet uniek zijn voor elke instantie van de toepassing. Sstandaardservices gedefinieerd in het toepassingsmanifest van het doeltoepassingstype worden ook aangemaakt.

```powershell
New-ServiceFabricApplication fabric:/Voting VotingType 1.0.0
```

Zodra deze bewerking is voltooid, worden de instanties van de toepassing die worden uitgevoerd in de Service Fabric Explorer weergegeven.

### <a name="remove-an-application"></a>Een toepassing verwijderen

Wanneer een instantie van een toepassing niet meer nodig is, kunt u deze met behulp van de cmdlet `Remove-ServiceFabricApplication` permanent verwijderen, waardoor ook alle services die deel uitmaken van de toepassing permanent worden verwijderd.

```powershell
Remove-ServiceFabricApplication fabric:/Voting
```

## <a name="next-steps"></a>Volgende stappen

In deze stap hebben we een app geïmplementeerd naar een beheerde Service Fabric-cluster (preview). Voor meer informatie over beheerde Service Fabric-clusters, zie:

> [!div class="nextstepaction"]
> [Veelgesteld vragen over beheerde Service Fabric-clusters](faq-managed-cluster.md)
