---
title: Een Azure Service Fabric-cluster sjabloon maken
description: Meer informatie over het maken van een resource manager-sjabloon voor een Service Fabric cluster. Configureer beveiliging, Azure Key Vault en Azure Active Directory (Azure AD) voor client verificatie.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: b028b5c1f32733146c071e50b24202e5bad945c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260485"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Een Service Fabric-cluster maken met Resource Manager-sjabloon

Een [Azure service Fabric-cluster](service-fabric-deploy-anywhere.md) is een met het netwerk verbonden reeks virtuele machines waarop uw micro services worden geïmplementeerd en beheerd. Een Service Fabric cluster dat in azure wordt uitgevoerd, is een Azure-resource die wordt geïmplementeerd, beheerd en bewaakt met behulp van de Resource Manager.  In dit artikel wordt beschreven hoe u een resource manager-sjabloon maakt voor een Service Fabric cluster dat in azure wordt uitgevoerd.  Wanneer de sjabloon is voltooid, kunt u [het cluster implementeren op Azure](service-fabric-cluster-creation-via-arm.md).

Cluster beveiliging wordt geconfigureerd wanneer het cluster voor het eerst wordt ingesteld en kan later niet worden gewijzigd. Lees [service Fabric scenario's voor cluster beveiliging][service-fabric-cluster-security]voordat u een cluster instelt. In azure gebruikt Service Fabric x509-certificaat om uw cluster en de bijbehorende eind punten te beveiligen, clients te verifiëren en gegevens te versleutelen. Azure Active Directory wordt ook aanbevolen om de toegang tot beheer eindpunten te beveiligen. U moet Azure AD-tenants en-gebruikers maken voordat u het cluster maakt.  Lees voor meer informatie [Azure AD instellen om clients te verifiëren](service-fabric-cluster-creation-setup-aad.md).

Voordat u een productie cluster implementeert om productie werkbelastingen uit te voeren, moet u eerst de [controle lijst voor productie voorbereiding](service-fabric-production-readiness-checklist.md)lezen.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>Het Resource Manager-sjabloon maken
Voorbeeld sjablonen voor Resource Manager zijn beschikbaar in de [Azure-voor beelden op github](https://github.com/Azure-Samples/service-fabric-cluster-templates). Deze sjablonen kunnen worden gebruikt als uitgangs punt voor uw cluster sjabloon.

In dit artikel worden de voorbeeld sjabloon en sjabloon parameters voor het [beveiligde cluster met vijf knoop punten][service-fabric-secure-cluster-5-node-1-nodetype] gebruikt. Down load *azuredeploy.jsop* en *azuredeploy.parameters.jsop* uw computer en open beide bestanden in uw favoriete tekst editor.

> [!NOTE]
> Voor nationale Clouds (Azure Government, Azure China, Azure Duitsland) moet u ook het volgende toevoegen `fabricSettings` aan uw sjabloon: `AADLoginEndpoint` , `AADTokenEndpointFormat` en `AADCertEndpointFormat` .

## <a name="add-certificates"></a>Certificaten toevoegen
U voegt certificaten toe aan een cluster resource manager-sjabloon door te verwijzen naar de sleutel kluis die de certificaat sleutels bevat. Voeg deze sleutel-kluis parameters en waarden toe aan een resource manager-sjabloon parameter bestand (*azuredeploy.parameters.jsaan*).

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Alle certificaten toevoegen aan de osProfile voor de virtuele-machine schaalset
Elk certificaat dat in het cluster is geïnstalleerd, moet worden geconfigureerd in de sectie **osProfile** van de resource Scale set (micro soft. Compute/virtualMachineScaleSets). Met deze actie wordt de resource provider geïnstrueerd het certificaat op de Vm's te installeren. Deze installatie omvat zowel het cluster certificaat als alle toepassings beveiligings certificaten die u voor uw toepassingen wilt gebruiken:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

### <a name="configure-the-service-fabric-cluster-certificate"></a>Het Service Fabric-cluster certificaat configureren

Het cluster verificatie certificaat moet worden geconfigureerd in zowel de Service Fabric cluster resource (micro soft. ServiceFabric/clusters) als de Service Fabric-extensie voor virtuele-machine schaal sets in de resource van de virtuele-machine schaalset. Met deze indeling kan de Service Fabric resource provider deze configureren voor gebruik voor cluster verificatie en Server verificatie voor beheer eindpunten.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>De certificaat gegevens voor de virtuele-machine Scale set-resource toevoegen

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt_',variables('vmNodeType0Name'))]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Voeg de certificaat gegevens toe aan de Service Fabric cluster resource

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Azure Active Directory-configuratie toevoegen voor het gebruik van Azure Active Directory voor clienttoegang

U voegt de Azure AD-configuratie toe aan een cluster resource manager-sjabloon door te verwijzen naar de sleutel kluis die de certificaat sleutels bevat. Voeg deze Azure AD-para meters en-waarden toe in een resource manager-sjabloon parameter bestand (*azuredeploy.parameters.jsaan*). 

> [!NOTE]
> In Linux moeten Azure AD-tenants en gebruikers worden gemaakt voordat ze het cluster kunnen maken.  Lees voor meer informatie [Azure AD instellen om clients te verifiëren](service-fabric-cluster-creation-setup-aad.md).

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

## <a name="populate-the-parameter-file-with-the-values"></a>Vul het parameter bestand in met de waarden

Gebruik tot slot de uitvoer waarden van de sleutel kluis en Azure AD Power shell-opdrachten om het parameter bestand in te vullen.

Als u van plan bent de Azure service Fabric RM Power shell-modules te gebruiken, hoeft u de cluster certificaat gegevens niet in te vullen. Als u wilt dat het systeem het zelfondertekende certificaat voor de cluster beveiliging genereert, hoeft u deze alleen als NULL te gebruiken. 

> [!NOTE]
> Voor de RM-modules om deze lege parameter waarden op te halen en in te vullen, worden de para meters met een naam die veel overeenkomen met de onderstaande namen

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

Als u toepassings certificaten gebruikt of een bestaand cluster gebruikt dat u hebt geüpload naar de sleutel kluis, moet u deze gegevens ophalen en vullen.

De RM-modules kunnen de Azure AD-configuratie niet voor u genereren, dus als u van plan bent om Azure AD voor client toegang te gebruiken, moet u deze invullen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

## <a name="test-your-template"></a>Uw sjabloon testen
Gebruik de volgende Power shell-opdracht om uw Resource Manager-sjabloon te testen met een parameter bestand:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Als u problemen ondervindt en cryptische berichten ontvangt, gebruikt u '-debug ' als een optie.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

In het volgende diagram ziet u waar uw sleutel kluis en Azure AD-configuratie in uw Resource Manager-sjabloon passen.

![Resource Manager-afhankelijkheids toewijzing][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Volgende stappen
Nu u een sjabloon voor uw cluster hebt, leert u hoe u [het cluster implementeert in azure](service-fabric-cluster-creation-via-arm.md).  Als u dit nog niet hebt gedaan, leest u de [controle lijst voor productie voorbereiding](service-fabric-production-readiness-checklist.md) voordat u een productie cluster implementeert.

Zie voor meer informatie over de JSON-syntaxis en de eigenschappen voor de resources die in dit artikel zijn geïmplementeerd:

* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: ../azure-resource-manager/templates/deploy-powershell.md

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
