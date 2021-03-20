---
title: Een Service Fabric-cluster maken met behulp van Azure Resource Manager-sjabloon
description: In deze quickstart maakt u een Azure Service Fabric-testcluster met behulp van Azure Resource Manager-sjabloon.
author: erikadoyle
ms.service: service-fabric
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: edoyle
ms.date: 07/29/2020
ms.openlocfilehash: 359b527733ee8eebf7e1e7d12c40a0c74ec1c9bd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87460300"
---
# <a name="quickstart-create-a-service-fabric-cluster-using-arm-template"></a>Quickstart: Een Service Fabric-cluster maken met behulp van ARM-sjabloon

Azure Service Fabric is een platform voor gedistribueerde systemen waarmee u gemakkelijk schaalbare en betrouwbare microservices en containers verpakt, implementeert en beheert. Een *Service Fabric-cluster* is een met het netwerk verbonden reeks virtuele machines waarop uw microservices worden geïmplementeerd en beheerd. In dit artikel wordt beschreven hoe u een Service Fabric-testcluster in Azure implementeert met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Dit Windows-cluster met vijf knooppunten wordt beveiligd met een zelfondertekend certificaat en is daarom alleen bedoeld voor instructies (in plaats van productieworkloads). We gebruiken Azure PowerShell om het sjabloon te implementeren. Naast Azure PowerShell kunt u ook de Azure-portal, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-portal.md) voor meer informatie over andere implementatiemethoden.

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-secure-cluster-5-node-1-nodetype%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

### <a name="install-service-fabric-sdk-and-powershell-modules"></a>Installeer Service Fabric SDK en PowerShell-modules

U hebt het volgende nodig om deze quickstart te voltooien:

* Installeer de [Service Fabric SDK en PowerShell-module](service-fabric-get-started.md).

* Installeer [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="download-the-sample-template-and-certificate-helper-script"></a>De voorbeeldsjabloon en het helperscript voor het certificaat downloaden

Kloon of download de opslagplaats voor [Azure Resource Manager-quickstartsjablonen](https://github.com/Azure/azure-quickstart-templates). U kunt ook lokaal de volgende bestanden kopiëren die we gebruiken vanuit de map *service-Fabric-Secure-cluster-5-node-1-nodeType*:

* [New-ServiceFabricClusterCertificate.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/scripts/New-ServiceFabricClusterCertificate.ps1)
* [azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json)
* [azuredeploy.parameters.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.parameters.json)

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure en geef het abonnement op dat moet worden gebruikt voor het maken van uw Service Fabric-cluster.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

### <a name="create-a-self-signed-certificate-stored-in-key-vault"></a>Maak een zelfondertekend certificaat opgeslagen in Key Vault

Service Fabric gebruikt X.509-certificaten om [een cluster te beveiligen ](./service-fabric-cluster-security.md) en beveiligingsfuncties van toepassingen te bieden en [Key Vault](../key-vault/general/overview.md) om die certificaten te beheren. Voor het maken van een cluster is een clustercertificaat vereist om de communicatie tussen knooppunten mogelijk te maken. Voor het maken van deze testcluster voor de quickstart maken we een zelfondertekend certificaat voor clusterverificatie. Voor productieworkloads zijn certificaten vereist die zijn gemaakt met een correct geconfigureerde service voor een Windows-servercertificaat of een van een goedgekeurde certificeringsinstantie (CA).

```powershell
# Designate unique (within cloudapp.azure.com) names for your resources
$resourceGroupName = "SFQuickstartRG"
$keyVaultName = "SFQuickstartKV"

# Create a new resource group for your Key Vault and Service Fabric cluster
New-AzResourceGroup -Name $resourceGroupName -Location SouthCentralUS

# Create a Key Vault enabled for deployment
New-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -Location SouthCentralUS -EnabledForDeployment

# Generate a certificate and upload it to Key Vault
.\scripts\New-ServiceFabricClusterCertificate.ps1
```

In het script wordt u gevraagd om het volgende te doen (zorg dat u *CertDNSName* en *KeyVaultName* in de onderstaande voorbeeldwaarden wijzigt):

* **Wachtwoord:** Password!1
* **CertDNSName:** *sfquickstart*.southcentralus.cloudapp.azure.com
* **KeyVaultName:** *SFQuickstartKV*
* **KeyVaultSecretName:** clustercert

Wanneer het script is voltooid, worden de parameterwaarden verstrekt die nodig zijn voor het implementeren van de sjabloon. Zorg ervoor dat u deze in de volgende variabelen opslaat, omdat deze nodig zijn om de clustersjabloon te implementeren:

```powershell
$sourceVaultId = "<Source Vault Resource Id>"
$certUrlValue = "<Certificate URL>"
$certThumbprint = "<Certificate Thumbprint>"
```

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/). De sjabloon voor dit artikel is te lang om hier weer te geven. Als u de sjabloon wilt weergeven, raadpleegt u het bestand [azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json).

Er zijn meerdere Azure-resources gedefinieerd in de sjabloon:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)
* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)

Zie [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/?sort=Popular&term=service+fabric) als u meer sjablonen wilt vinden die gerelateerd zijn aan Azure Service Fabric.

### <a name="customize-the-parameters-file"></a>Pas het parameterbestand aan

Open *azuredeploy.parameters.json* en bewerk de parameterwaarden zodat:

* **clustername** overeenkomt met de waarde die u hebt opgegeven voor *CertDNSName* bij het maken van uw clustercertificaat
* **adminUserName** is een andere waarde dan de standaard *GEN-UNIQUE*-token
* **adminPassword** is een andere waarde dan de standaard *GEN-PASSWORD*-token
* **certificateThumbprint**, **sourceVaultResourceId** en **certificateUrlValue** zijn alle lege teken reeksen (`""`)

Bijvoorbeeld:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "sfquickstart"
    },
    "adminUsername": {
      "value": "testadm"
    },
    "adminPassword": {
      "value": "Password#1234"
    },
    "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultResourceId": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    }
  }
}
```

## <a name="deploy-the-template"></a>De sjabloon implementeren

Sla de paden van uw ARM-sjabloon en parameterbestanden op in variabelen en implementeer vervolgens de sjabloon.

```powershell
$templateFilePath = "<full path to azuredeploy.json>"
$parameterFilePath = "<full path to azuredeploy.parameters.json>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $certThumbprint `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultResourceId $sourceVaultId `
    -Verbose
```

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Zodra de implementatie is voltooid, gaat u naar de `managementEndpoint` waarde in de uitvoer en opent u het adres in een webbrowser om uw cluster in [Service Fabric Explorer](./service-fabric-visualizing-your-cluster.md) te bekijken.

![Service Fabric Explorer geeft het nieuwe cluster weer](./media/quickstart-cluster-template/service-fabric-explorer.png)

U kunt ook het Service Fabric Explorer-eindpunt vinden op de blade Service Explorer-resouce in de Azure-portal.

![Blade Service Fabric-resource met Service Fabric Explorer-eindpunt](./media/quickstart-cluster-template/service-fabric-explorer-endpoint-azure-portal.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resourcegroep niet meer nodig hebt, verwijdert u deze. Hierdoor worden ook de resources in de resourcegroep verwijderd.

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

Verwijder vervolgens het clustercertificaat uit uw lokale archief. Lijst met geïnstalleerde certificaten voor het vinden van de vingerafdruk voor uw cluster:

```powershell
Get-ChildItem Cert:\CurrentUser\My\
```

Verwijder vervolgens het certificaat:

```powershell
Get-ChildItem Cert:\CurrentUser\My\{THUMBPRINT} | Remove-Item
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het maken van een aangepaste Azure Service Fabric-clustersjabloon:

> [!div class="nextstepaction"]
> [Een Service Fabric-cluster maken met Resource Manager-sjabloon](service-fabric-cluster-creation-create-template.md)
