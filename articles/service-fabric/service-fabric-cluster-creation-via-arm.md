---
title: Een Azure Service Fabric-cluster maken
description: Meer informatie over het instellen van een beveiligd Service Fabric-cluster in azure met behulp van Azure Resource Manager.  U kunt een cluster maken met behulp van een standaard sjabloon of uw eigen cluster sjabloon gebruiken.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: d4daa75fd9383bc19da1b09104ebddff5712c5b4
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791808"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Een Service Fabric-cluster maken met behulp van Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure-portal](service-fabric-cluster-creation-via-portal.md)
>
>

Een [Azure service Fabric-cluster](service-fabric-deploy-anywhere.md) is een met het netwerk verbonden reeks virtuele machines waarop uw micro services worden geïmplementeerd en beheerd.  Een Service Fabric cluster dat wordt uitgevoerd in Azure is een Azure-resource en wordt geïmplementeerd met behulp van de Azure Resource Manager. In dit artikel wordt beschreven hoe u een beveiligd Service Fabric-cluster in azure kunt implementeren met behulp van Resource Manager. U kunt een standaard cluster sjabloon of een aangepaste sjabloon gebruiken.  Als u nog geen aangepaste sjabloon hebt, kunt u [er informatie over maken](service-fabric-cluster-creation-create-template.md).

Het type beveiliging dat is gekozen om het cluster te beveiligen (bijvoorbeeld: Windows-identiteit, x509 enz.) moet worden opgegeven voor de eerste keer dat het cluster wordt gemaakt en kan daarna niet meer worden gewijzigd. Lees [service Fabric scenario's voor cluster beveiliging][service-fabric-cluster-security]voordat u een cluster instelt. In azure gebruikt Service Fabric x509-certificaat om uw cluster en de bijbehorende eind punten te beveiligen, clients te verifiëren en gegevens te versleutelen. Azure Active Directory wordt ook aanbevolen om de toegang tot beheer eindpunten te beveiligen. Lees voor meer informatie [Azure AD instellen om clients te verifiëren](service-fabric-cluster-creation-setup-aad.md).

Als u een productie cluster maakt voor het uitvoeren van productie werkbelastingen, raden we u aan eerst de [controle lijst voor productie voorbereiding](service-fabric-production-readiness-checklist.md)te lezen.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten 
In dit artikel gebruikt u de modules Service Fabric RM Power shell of Azure CLI voor het implementeren van een cluster:

* [Azure PowerShell 4,1 en hoger][azure-powershell]
* [Azure CLI-versie 2,0 en hoger][azure-CLI]

U kunt hier de referentie documentatie voor de Service Fabric-modules vinden:
* [Az.ServiceFabric](/powershell/module/az.servicefabric)
* [AZ SF CLI-module](/cli/azure/sf)

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Voordat u een van de opdrachten in dit artikel uitvoert, meldt u zich eerst aan bij Azure.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Een nieuw cluster maken met een door het systeem gegenereerd zelfondertekend certificaat

Gebruik de volgende opdrachten om een cluster te maken dat is beveiligd met een door het systeem gegenereerd zelfondertekend certificaat. Met deze opdracht wordt een primair cluster certificaat ingesteld dat wordt gebruikt voor de beveiliging van het cluster en om beheerders toegang in te stellen voor het uitvoeren van beheer bewerkingen met dat certificaat.  Zelfondertekende certificaten zijn handig voor het beveiligen van test clusters.  Productie clusters moeten worden beveiligd met een certificaat van een certificerings instantie (CA).

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>De standaard cluster sjabloon gebruiken die in de module wordt geleverd

Gebruik de volgende opdracht om snel een cluster te maken door minimale para meters op te geven met behulp van de standaard sjabloon.

De sjabloon die wordt gebruikt, is beschikbaar in de sjabloon voor [beelden van Azure service Fabric: Windows Temp](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) late en [Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Met de volgende opdracht kunt u Windows-of Linux-clusters maken. u moet het besturings systeem dienovereenkomstig opgeven. De Power shell/CLI-opdrachten voeren ook het certificaat uit in de opgegeven *CertificateOutputFolder*. Zorg echter dat de map certificaat al is gemaakt. De opdracht neemt ook andere para meters op, zoals de VM-SKU.

> [!NOTE]
> De volgende Power shell-opdracht werkt alleen met de `Az` module Azure PowerShell. Als u de huidige versie van Azure Resource Manager Power shell-versie wilt controleren, voert u de volgende Power shell-opdracht ' Get-module AZ ' uit. Volg [deze koppeling](/powershell/azure/install-Az-ps) om uw Azure Resource Manager Power shell-versie bij te werken. 
>
>

Implementeer het cluster met behulp van Power shell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

Implementeer het cluster met behulp van Azure CLI:

```azurecli
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

### <a name="use-your-own-custom-template"></a>Uw eigen aangepaste sjabloon gebruiken

Als u een aangepaste sjabloon wilt maken die aan uw behoeften voldoet, wordt u ten zeerste aangeraden te beginnen met een van de sjablonen die beschikbaar zijn in de voor [beelden van Azure service Fabric-sjablonen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Meer informatie over het [aanpassen van uw cluster sjabloon][customize-your-cluster-template].

Als u al een aangepaste sjabloon hebt, controleert u of alle drie aan het certificaat gerelateerde para meters in de sjabloon en het parameter bestand de volgende naam hebben en waarden zijn null als volgt:

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Implementeer het cluster met behulp van Power shell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Implementeer het cluster met behulp van Azure CLI:

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath
```

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Een nieuw cluster maken met uw eigen X. 509-certificaat

Gebruik de volgende opdracht om het cluster te maken als u een certificaat hebt dat u wilt gebruiken om uw cluster te beveiligen met.

Als dit een door de certificerings instantie ondertekend certificaat is dat u ook voor andere doel einden gebruikt, is het raadzaam om een afzonderlijke resource groep specifiek voor uw sleutel kluis op te geven. We raden u aan de sleutel kluis in een eigen resource groep te plaatsen. Met deze actie kunt u de berekenings-en opslag Resource groepen verwijderen, met inbegrip van de resource groep die uw Service Fabric cluster bevat, zonder dat u uw sleutels en geheimen kwijtraakt. **De resource groep die de sleutel kluis bevat, *moet zich in dezelfde regio bevinden* als de cluster die deze gebruikt.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Gebruik het standaard vijf knoop punt, één knooppunt type sjabloon die in de module wordt geleverd
De sjabloon die wordt gebruikt, is beschikbaar in de sjabloon Azure-voor [beelden: Windows-sjabloon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) en [Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Implementeer het cluster met behulp van Power shell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

Implementeer het cluster met behulp van Azure CLI:

```azurecli
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

### <a name="use-your-own-custom-cluster-template"></a>Uw eigen aangepaste cluster sjabloon gebruiken
Als u een aangepaste sjabloon wilt maken die aan uw behoeften voldoet, wordt u ten zeerste aangeraden te beginnen met een van de sjablonen die beschikbaar zijn in de voor [beelden van Azure service Fabric-sjablonen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Meer informatie over het [aanpassen van uw cluster sjabloon][customize-your-cluster-template].

Als u al een aangepaste sjabloon hebt, controleert u of alle drie aan het certificaat gerelateerde para meters in de sjabloon en het parameter bestand een naam hebben, en waarden zijn null als volgt.

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Implementeer het cluster met behulp van Power shell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Implementeer het cluster met behulp van Azure CLI:

```azurecli
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Een verwijzing gebruiken naar een geheim dat is geüpload naar een sleutel kluis

Als u een bestaande sleutel kluis wilt gebruiken, moet de sleutel kluis zijn [ingeschakeld voor implementatie](../key-vault/general/manage-with-cli2.md#bkmk_KVperCLI) zodat de compute resource provider certificaten kan ophalen en installeren op cluster knooppunten.

Implementeer het cluster met behulp van Power shell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretID -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Implementeer het cluster met behulp van Azure CLI:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier $secretID  \
    --template-file $templateFilePath --parameter-file $parameterFilePath 
```

## <a name="next-steps"></a>Volgende stappen
Op dit moment hebt u een beveiligd cluster dat in azure wordt uitgevoerd. Vervolgens [maakt u verbinding met uw cluster](service-fabric-connect-to-secure-cluster.md) en leert u hoe u [toepassings geheimen kunt beheren](service-fabric-application-secret-management.md).

Zie [micro soft. ServiceFabric/clusters-sjabloon verwijzing](/azure/templates/microsoft.servicefabric/clusters)voor de JSON-syntaxis en eigenschappen voor het gebruik van een sjabloon.

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
