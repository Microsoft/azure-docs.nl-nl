---
title: Azure Service Fabric-infra structuur als best practices voor code
description: Best practices en ontwerp overwegingen voor het beheren van Azure Service Fabric als infra structuur als code.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: b765d92778df40caec0864dc6f547324216fdb07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102611977"
---
# <a name="infrastructure-as-code"></a>Infrastructure als code

Maak in een productie scenario Azure Service Fabric-clusters met behulp van Resource Manager-sjablonen. Resource Manager-sjablonen bieden meer controle over de bron eigenschappen en zorgen ervoor dat u een consistent resource model hebt.

Voorbeeld sjablonen voor Resource Manager zijn beschikbaar voor Windows en Linux in de [Azure-voor beelden op github](https://github.com/Azure-Samples/service-fabric-cluster-templates). Deze sjablonen kunnen worden gebruikt als uitgangs punt voor uw cluster sjabloon. Down load `azuredeploy.json` en `azuredeploy.parameters.json` Bewerk ze om te voldoen aan uw aangepaste vereisten.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

`azuredeploy.json` `azuredeploy.parameters.json` Gebruik de volgende Azure cli-opdrachten om de en sjablonen te implementeren die u hierboven hebt gedownload:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az deployment group create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Een resource maken met behulp van Power shell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Azure Service Fabric-resources

U kunt toepassingen en services implementeren op uw Service Fabric-cluster via Azure Resource Manager. Zie [toepassingen en services beheren als Azure Resource Manager resources](./service-fabric-application-arm-resource.md) voor meer informatie. Hieronder vindt u best practice Service Fabric toepassingsspecifieke resources die moeten worden meegenomen in de resources van de Resource Manager-sjabloon.

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Als u uw toepassing wilt implementeren met behulp van Azure Resource Manager, moet u eerst [een sfpkg](./service-fabric-package-apps.md#create-an-sfpkg) service Fabric toepassings pakket maken. Het volgende python-script is een voor beeld van het maken van een sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Automatische upgrade configuratie van het besturings systeem van de virtuele machine van Azure 
Het upgraden van uw virtuele machines is een door de gebruiker geïnitieerde bewerking. het wordt aanbevolen dat u de Schaalset voor de virtuele machine gebruikt voor het [automatisch bijwerken van het besturings systeem](service-fabric-patch-orchestration-application.md) voor Azure service Fabric clusters host patch management; Patch Orchestration Application is een alternatieve oplossing die is bedoeld voor wanneer deze buiten Azure wordt gehost, maar POA kan worden gebruikt in azure, waarbij de overhead van het hosten van POA in azure een gemeen schappelijke reden is om de voor keur te geven aan een automatische upgrade van het besturings systeem van de virtuele machine over POA. Hieronder vindt u de eigenschappen van de Resource Manager-sjabloon voor de berekening van de virtuele machine Scale set om automatische upgrades van besturings systemen in te scha kelen:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Wanneer u automatische OS-upgrades gebruikt met Service Fabric, wordt de nieuwe installatie kopie van het besturings systeem samen met één update domein per keer uitgedraaid om hoge Beschik baarheid van de services die in Service Fabric worden uitgevoerd, te onderhouden. Als u automatische besturingssysteem upgrades wilt gebruiken in Service Fabric moet uw cluster zijn geconfigureerd voor het gebruik van de Silver duurzaamheid-laag of hoger.

Zorg ervoor dat de volgende register sleutel is ingesteld op false om te voor komen dat uw Windows-hostcomputers niet-gecoördineerde updates initiëren: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

Hieronder vindt u de eigenschappen van de Resource Manager-sjabloon voor het berekenen van de naam van de compute virtual machine set om de register sleutel WindowsUpdate in te stellen op False
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Upgrade configuratie van Azure Service Fabric cluster
Hier volgt de Service Fabric eigenschap cluster resource manager-sjabloon voor het inschakelen van automatische upgrade:
```json
"upgradeMode": "Automatic",
```
Als u het cluster hand matig wilt upgraden, downloadt u de cab/deb-distributie naar een virtuele cluster machine en roept u de volgende Power shell aan:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Volgende stappen

* Een cluster maken op Vm's of computers met Windows Server: [service Fabric cluster maken voor Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Een cluster maken op Vm's of computers met Linux: [een Linux-cluster maken](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md)
