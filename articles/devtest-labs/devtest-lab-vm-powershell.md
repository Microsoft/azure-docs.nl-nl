---
title: Een virtuele machine maken in DevTest Labs met Azure PowerShell
description: Meer informatie over het gebruik van Azure DevTest Labs voor het maken en beheren van virtuele machines met Azure PowerShell.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f79b6ff92d633cf63477cddaabec918df352bec8
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499251"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Een virtuele machine maken met DevTest Labs met behulp van Azure PowerShell
In dit artikel wordt beschreven hoe u een virtuele machine in Azure DevTest Labs maakt met behulp van Azure PowerShell. U kunt Power shell-scripts gebruiken voor het automatiseren van het maken van virtuele machines in een lab in Azure DevTest Labs. 

## <a name="prerequisites"></a>Vereisten
Voordat u begint:

- [Een lab maken](devtest-lab-create-lab.md) als u geen bestaand Lab wilt gebruiken om het script of de opdrachten in dit artikel te testen. 
- [Installeer Azure PowerShell](/powershell/azure/install-az-ps) of gebruik de Azure Cloud shell die is geïntegreerd in de Azure Portal. 

## <a name="powershell-script"></a>PowerShell-script
In het voorbeeld script in deze sectie wordt de cmdlet [invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) gebruikt.  Deze cmdlet neemt de resource-ID van het lab, de naam van de actie die moet worden uitgevoerd ( `createEnvironment` ) en de para meters die nodig zijn om die actie uit te voeren. De para meters bevinden zich in een hash-tabel die alle eigenschappen van de beschrijving van de virtuele machine bevat. 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "expirationDate"          = "2019-12-01"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

Met de eigenschappen voor de virtuele machine in het bovenstaande script kunnen we een virtuele machine maken met Windows Server 2016 Data Center als het besturings systeem. Voor elk type virtuele machine is deze eigenschappen iets anders. In de sectie [virtuele machine definiëren](#define-virtual-machine) ziet u hoe u kunt bepalen welke eigenschappen in dit script moeten worden gebruikt.

De volgende opdracht geeft een voor beeld van het uitvoeren van het script dat is opgeslagen in een bestands naam: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Virtuele machine definiëren
In deze sectie wordt beschreven hoe u de eigenschappen kunt ophalen die specifiek zijn voor een type virtuele machine die u wilt maken. 

### <a name="use-azure-portal"></a>Azure Portal gebruiken
U kunt een Azure Resource Manager sjabloon genereren bij het maken van een virtuele machine in de Azure Portal. U hoeft het proces voor het maken van de virtuele machine niet te volt ooien. U kunt de stappen alleen volgen totdat u de sjabloon ziet. Dit is de beste manier om de benodigde JSON-beschrijving op te halen als u nog geen Lab-VM hebt gemaakt. 

1. Navigeer naar [Azure Portal](https://portal.azure.com).
2. Selecteer **alle services** in het navigatie menu links.
3. Zoek en selecteer **DevTest Labs** in de lijst met Services. 
4. Selecteer op de pagina **DevTest Labs** uw Lab in de lijst met Labs.
5. Op de start pagina van uw Lab selecteert u **+ toevoegen** op de werk balk. 
6. Selecteer een **basis installatie kopie** voor de virtuele machine. 
7. Selecteer de **Opties voor automatisering** onder aan de pagina boven de knop **verzenden** . 
8. U ziet de **Azure Resource Manager sjabloon** voor het maken van de virtuele machine. 
9. Het JSON-segment in de sectie **resources** bevat de definitie voor het afbeeldings type dat u eerder hebt geselecteerd. 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

In dit voor beeld ziet u hoe u een definitie van een Azure Market Place-installatie kopie krijgt. Op dezelfde manier kunt u een definitie van een aangepaste installatie kopie, een formule of een omgeving verkrijgen. Voeg de benodigde artefacten voor de virtuele machine toe en stel de vereiste geavanceerde instellingen in. Nadat u waarden hebt opgegeven voor de vereiste velden en eventuele optionele velden, voordat u de knop **Opties voor automatisering** selecteert.

### <a name="use-azure-rest-api"></a>Azure REST API gebruiken
De volgende procedure bevat stappen voor het ophalen van eigenschappen van een installatie kopie met behulp van de REST API: deze stappen werken alleen voor een bestaande virtuele machine in een lab. 

1. Ga naar de pagina [virtual machines-lijst](/rest/api/dtl/virtualmachines/list) , selecteer de knop **try it** . 
2. Selecteer uw **Azure-abonnement**.
3. Voer de **resource groep voor het lab in**.
4. Voer de **naam van het lab in**. 
5. Selecteer **Uitvoeren**.
6. U ziet de **Eigenschappen voor de installatie kopie** op basis van de virtuele machine die is gemaakt. 

## <a name="set-expiration-date"></a>Verval datum instellen
In scenario's zoals training, demo's en proef versies kunt u virtuele machines maken en deze automatisch na een vaste duur verwijderen, zodat u geen onnodige kosten hebt. U kunt een verval datum voor een virtuele machine instellen tijdens het maken met behulp van Power shell, zoals wordt weer gegeven in de sectie voor beeld [Power shell-script](#powershell-script) .

Hier volgt een voor beeld van een Power shell-script waarmee de verval datum wordt ingesteld voor alle bestaande Vm's in een Lab:

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>Volgende stappen
Zie de volgende inhoud: [Azure PowerShell documentatie voor Azure DevTest Labs](/powershell/module/az.devtestlabs/)
