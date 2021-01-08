---
title: Een installatie kopie van een virtuele Azure-machine voor Azure Marketplace testen
description: Meer informatie over het testen en verzenden van een Azure virtual machine-aanbieding in azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: github-2407
ms.author: krsh
ms.date: 10/15/2020
ms.openlocfilehash: a9698981b1a658664bfc14886628bbfd0a4a64d2
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97976925"
---
# <a name="test-a-virtual-machine-image"></a>Een installatie kopie van een virtuele machine testen

In dit artikel wordt beschreven hoe u een installatie kopie van een virtuele machine (VM) in de commerciële Marketplace kunt testen en verzenden om te controleren of deze voldoet aan de meest recente publicatie vereisten voor Azure Marketplace.

Voer de volgende stappen uit voordat u uw VM-aanbieding verzendt:

- Implementeer een Azure-VM met behulp van uw gegeneraliseerde installatie kopie. Zie [een virtuele machine maken met behulp van een goedgekeurde basis](azure-vm-create-using-approved-base.md) of [Maak een virtuele machine met behulp van uw eigen installatie kopie](azure-vm-create-using-own-image.md).
- Voer validaties uit.

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Een Azure-VM implementeren met behulp van uw gegeneraliseerde installatie kopie

In deze sectie wordt beschreven hoe u een gegeneraliseerde virtuele harde schijf (VHD)-installatie kopie implementeert voor het maken van een nieuwe Azure VM-resource. Voor dit proces gebruiken we de meegeleverde Azure Resource Manager sjabloon en Azure PowerShell script.

### <a name="prepare-an-azure-resource-manager-template"></a>Een Azure Resource Manager sjabloon voorbereiden

In deze sectie wordt beschreven hoe u een installatie kopie van een door de gebruiker gedefinieerde virtuele machine (VM) maakt en implementeert. U kunt dit doen door VHD-installatie kopieën van besturings systeem en gegevens schijven te voorzien van een door Azure geïmplementeerde virtuele harde schijf. Met deze stappen implementeert u de virtuele machine met gegeneraliseerde VHD.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Upload uw gegeneraliseerde virtuele harde schijf van het besturings systeem en de Vhd's met gegevens schijven naar uw Azure Storage-account.
3. Op de start pagina selecteert u **een resource maken**, zoekt u naar "sjabloon implementatie" en selecteert u **maken**.
4. Kies **uw eigen sjabloon bouwen in de editor**.

    :::image type="content" source="media/vm/template-deployment.png" alt-text="Toont de selectie van een sjabloon.":::

5. Plak de volgende JSON-sjabloon in de editor en selecteer **Opslaan**.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "String"
        },
        "userStorageContainerName": {
            "defaultValue": "vhds",
            "type": "String"
        },
        "dnsNameForPublicIP": {
            "type": "String"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "String"
        },
        "adminPassword": {
            "defaultValue": "",
            "type": "SecureString"
        },
        "osType": {
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ],
            "type": "String"
        },
        "subscriptionId": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "vmSize": {
            "type": "String"
        },
        "publicIPAddressName": {
            "type": "String"
        },
        "vmName": {
            "type": "String"
        },
        "virtualNetworkName": {
            "type": "String"
        },
        "nicName": {
            "type": "String"
        },
        "vhdUrl": {
            "type": "String",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
    "variables": {
        "addressPrefix": "10.0.0.0/16",
        "subnet1Name": "Subnet-1",
        "subnet2Name": "Subnet-2",
        "subnet1Prefix": "10.0.0.0/24",
        "subnet2Prefix": "10.0.1.0/24",
        "publicIPAddressType": "Dynamic",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
        "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
        "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
        "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2015-06-15",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2015-06-15",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnet1Name')]",
                        "properties": {
                            "addressPrefix": "[variables('subnet1Prefix')]"
                        }
                    },
                    {
                        "name": "[variables('subnet2Name')]",
                        "properties": {
                            "addressPrefix": "[variables('subnet2Prefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2015-06-15",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnet1Ref')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2015-06-15",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[parameters('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "name": "[concat(parameters('vmName'),'-osDisk')]",
                        "osType": "[parameters('osType')]",
                        "caching": "ReadWrite",
                        "image": {
                            "uri": "[parameters('vhdUrl')]"
                        },
                        "vhd": {
                            "uri": "[variables('osDiskVhdName')]"
                        },
                        "createOption": "FromImage"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                        }
                    ]
                }
            }
        }
    ]
}
```

<br>

6. Geef de parameter waarden op voor de weer gegeven eigenschappen pagina's met aangepaste implementatie.

    | ResourceGroupName | De naam van de bestaande Azure-resource groep. Gebruik normaal gesp roken dezelfde RG als uw sleutel kluis. |
    | --- | --- |
    | TemplateFile | Volledige padnaam naar het bestand VHDtoImage.jsop. |
    | userStorageAccountName | Naam van het opslagaccount. |
    | dnsNameForPublicIP | DNS-naam voor het open bare IP-adres. moet een kleine letter zijn. |
    | subscriptionId | Azure-abonnements-id. |
    | Locatie | Standaard geografische locatie van Azure van de resource groep. |
    | vmName | De naam van de virtuele machine. |
    | vhdUrl | Het webadres van de virtuele harde schijf. |
    | vmSize | Grootte van het exemplaar van de virtuele machine. |
    | publicIPAddressName | De naam van het open bare IP-adres. |
    | virtualNetworkName | De naam van het virtuele netwerk. |
    | nicName | De naam van de netwerk interface kaart voor het virtuele netwerk. |
    | adminUserName | De gebruikers naam van het Administrator-account. |
    | adminPassword | Beheerders wachtwoord. |


7. Nadat u deze waarden hebt opgegeven, selecteert u **kopen**.

De implementatie van Azure wordt gestart. Er wordt een nieuwe virtuele machine gemaakt met de opgegeven onbeheerde VHD in het opgegeven pad van het opslag account. U kunt de voortgang van de Azure Portal volgen door **virtual machines** aan de linkerkant van de portal te selecteren. Wanneer de virtuele machine wordt gemaakt, wordt de status gewijzigd van gestart in actief.

#### <a name="for-generation-2-vm-deployment-use-this-template"></a>Voor de VM-implementatie van generatie 2 gebruikt u deze sjabloon:

```JSON
{
   "$schema":"https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{
      "userStorageAccountName":{
         "type":"String"
      },
      "userStorageContainerName":{
         "type":"String",
         "defaultValue":"vhds-86350-720-f4efbbb2-611b-4cd7-ad1e-afdgfuadfluad"
      },
      "dnsNameForPublicIP":{
         "type":"String"
      },
      "adminUserName":{
         "defaultValue":"isv",
         "type":"String"
      },
      "adminPassword":{
         "type":"securestring",
         "defaultValue":"Certcare@86350"
      },
      "osType":{
         "type":"string",
         "defaultValue":"linux",
         "allowedValues":[
            "windows",
            "linux"
         ]
      },
      "subscriptionId":{
         "type":"string"
      },
      "location":{
         "type":"string"
      },
      "vmSize":{
         "type":"string"
      },
      "publicIPAddressName":{
         "type":"string"
      },
      "vmName":{
         "type":"string"
      },
      "vNetNewOrExisting":{
         "defaultValue":"existing",
         "allowedValues":[
            "new",
            "existing"
         ],
         "type":"String",
         "metadata":{
            "description":"Specify whether to create a new or existing virtual network for the VM."
         }
      },
      "virtualNetworkName":{
         "type":"String",
         "defaultValue":""
      },
      "SubnetName":{
         "defaultValue":"subnet-5",
         "type":"String"
      },
      "SubnetPrefix":{
         "defaultValue":"10.0.5.0/24",
         "type":"String"
      },
      "nicName":{
         "type":"string"
      },
      "vhdUrl":{
         "type":"string",
         "metadata":{
            "description":"VHD Url..."
         }
      },
      "Datadisk1":{
         "type":"string",
         "metadata":{
            "description":"datadisk1 Url..."
         }
      },
      "Datadisk2":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      },
      "Datadisk3":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      }
   },
   "variables":{
      "addressPrefix":"10.0.0.0/16",
      "subnet1Name":"[parameters('SubnetName')]",
      "subnet1Prefix":"[parameters('SubnetPrefix')]",
      "publicIPAddressType":"Static",
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
      "subnet1Ref":"[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
      "osDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]",
      "dataDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'datadisk')]",
      "imageName":"[concat(parameters('vmName'), '-image')]"
   },
   "resources":[
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/publicIPAddresses",
         "name":"[parameters('publicIPAddressName')]",
         "location":"[parameters('location')]",
         "properties":{
            "publicIPAllocationMethod":"[variables('publicIPAddressType')]",
            "dnsSettings":{
               "domainNameLabel":"[parameters('dnsNameForPublicIP')]"
            }
         }
      },
      {
         "type":"Microsoft.Compute/images",
         "apiVersion":"2019-12-01",
         "name":"[variables('imageName')]",
         "location":"[parameters('location')]",
         "properties":{
            "storageProfile":{
               "osDisk":{
                  "osType":"[parameters('osType')]",
                  "osState":"Generalized",
                  "blobUri":"[parameters('vhdUrl')]",
                  "storageAccountType":"Standard_LRS"
               },
               "dataDisks":[
                  {
                     "lun":0,
                     "blobUri":"[parameters('Datadisk1')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":1,
                     "blobUri":"[parameters('Datadisk2')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":2,
                     "blobUri":"[parameters('Datadisk3')]",
                     "storageAccountType":"Standard_LRS"
                  }
               ]
            },
            "hyperVGeneration":"V2"
         }
      },
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/virtualNetworks",
         "name":"[parameters('virtualNetworkName')]",
         "location":"[parameters('location')]",
         "properties":{
            "addressSpace":{
               "addressPrefixes":[
                  "[variables('addressPrefix')]"
               ]
            },
            "subnets":[
               {
                  "name":"[variables('subnet1Name')]",
                  "properties":{
                     "addressPrefix":"[variables('subnet1Prefix')]"
                  }
               }
            ]
         },
         "condition":"[equals(parameters('vNetNewOrExisting'), 'new')]"
      },
      {
         "apiVersion":"2016-09-01",
         "type":"Microsoft.Network/networkInterfaces",
         "name":"[parameters('nicName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
         ],
         "properties":{
            "ipConfigurations":[
               {
                  "name":"ipconfig1",
                  "properties":{
                     "privateIPAllocationMethod":"Dynamic",
                     "publicIPAddress":{
                        "id":"[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                     },
                     "subnet":{
                        "id":"[variables('subnet1Ref')]"
                     }
                  }
               }
            ]
         }
      },
      {
         "apiVersion":"2019-12-01",
         "type":"Microsoft.Compute/virtualMachines",
         "name":"[parameters('vmName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]",
            "[variables('imageName')]"
         ],
         "properties":{
            "hardwareProfile":{
               "vmSize":"[parameters('vmSize')]"
            },
            "osProfile":{
               "computername":"[parameters('vmName')]",
               "adminUsername":"[parameters('adminUsername')]",
               "adminPassword":"[parameters('adminPassword')]"
            },
            "storageProfile":{
               "imageReference":{
                  "id":"[resourceId('Microsoft.Compute/images', variables('imageName'))]"
               },
               "dataDisks":[
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk0')]",
                     "lun":0,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk1')]",
                     "lun":1,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk2')]",
                     "lun":2,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  }
               ],
               "osDisk":{
                  "name":"[concat(parameters('vmName'),'-OSDisk')]",
                  "createOption":"FromImage",
                  "managedDisk":{
                     "storageAccountType":"Standard_LRS"
                  }
               }
            },
            "networkProfile":{
               "networkInterfaces":[
                  {
                     "id":"[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                  }
               ]
            }
         }
      }
   ]
}
```

### <a name="deploy-an-azure-vm-using-powershell"></a>Een Azure-VM implementeren met behulp van Power shell

Kopieer en bewerk het volgende script om waarden voor de variabelen en op te geven `$storageaccount` `$vhdUrl` . Voer deze uit om een Azure VM-resource te maken op basis van uw bestaande gegeneraliseerde VHD.

```PowerShell
# storage account of existing generalized VHD
$storageaccount = "testwinrm11815"
# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd
```

## <a name="run-validations"></a>Validaties uitvoeren

Er zijn twee manieren om validaties uit te voeren op de geïmplementeerde installatie kopie.

### <a name="use-certification-test-tool-for-azure-certified"></a>Certificerings test hulpprogramma gebruiken voor Azure Certified

#### <a name="download-and-run-the-certification-test-tool"></a>Het hulp programma voor certificerings test downloaden en uitvoeren

Het certificerings test hulpprogramma voor Azure Certified wordt uitgevoerd op een lokale Windows-computer, maar test een op Azure gebaseerde Windows-of Linux-VM. Het verklaart dat uw VM-installatie kopie van uw gebruiker kan worden gebruikt met Microsoft Azure en dat aan de richt lijnen en vereisten rond het voorbereiden van uw VHD is voldaan.

1. Down load en installeer het meest recente [certificerings test programma voor Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299).
2. Open het certificerings programma en selecteer vervolgens **nieuwe test starten**.
3. Voer in het scherm test informatie een **test naam** in voor de test uitvoering.
4. Selecteer het platform voor uw virtuele machine, ofwel **Windows Server** of **Linux**. De platform keuze is van invloed op de resterende opties.
5. Als uw virtuele machine gebruikmaakt van deze database service, schakelt u het selectie vakje **testen voor Azure SQL database** in.

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>Het certificerings hulpprogramma verbinden met een VM-installatie kopie

1. Selecteer de SSH-verificatie modus: wachtwoord verificatie of sleutel bestands verificatie.
2. Als u verificatie op basis van wacht woorden gebruikt, voert u waarden in voor de **DNS-naam van de virtuele machine**, de **gebruikers naam** en het **wacht woord**. U kunt ook het standaard SSH-poort nummer wijzigen.

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="Toont de selectie van VM-test gegevens.":::

3. Als u gebruikmaakt van verificatie op basis van een sleutel bestand, voert u waarden in voor de DNS-naam van de virtuele machine, de gebruikers naam en de locatie van de persoonlijke sleutel. U kunt ook een wachtwoordzin toevoegen of het standaard-SSH-poort nummer wijzigen.
4. Voer de volledig gekwalificeerde VM-DNS-naam in (bijvoorbeeld MyVMName.Cloudapp.net).
5. Voer de **gebruikers naam** en het **wacht woord** in.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Toont de selectie van de gebruikers naam en het wacht woord van de VM.":::

6. Selecteer **Next**.

#### <a name="run-a-certification-test"></a>Een certificerings test uitvoeren

Nadat u de parameter waarden voor uw VM-installatie kopie in het certificerings programma hebt opgegeven, selecteert u verbinding testen om een geldige verbinding met uw virtuele machine te maken. Nadat een verbinding is geverifieerd, selecteert u **volgende** om de test te starten. Wanneer de test is voltooid, worden de resultaten weer gegeven in een tabel. In de kolom Status wordt voor elke test weer gegeven (pass/fail/Warning). Als een van de tests mislukt, is uw installatie kopie niet gecertificeerd. In dit geval raadpleegt u de vereisten en fout berichten, brengt u de voorgestelde wijzigingen aan en voert u de test opnieuw uit.

Nadat de geautomatiseerde test is voltooid, geeft u aanvullende informatie over de VM-installatie kopie op de twee tabbladen van het vragenlijst scherm, algemene evaluatie en aanpassing van de kernel en selecteert u volgende.

In het laatste scherm kunt u meer informatie opgeven, zoals SSH-toegangs gegevens voor een Linux VM-installatie kopie, en een uitleg voor eventuele mislukte beoordelingen als u op zoek bent naar uitzonde ringen.

Selecteer ten slotte rapport genereren om de test resultaten en logboek bestanden voor de uitgevoerde test cases samen met uw antwoorden op de vragen lijst te downloaden. 
> [!Note]
> Enkele uitgevers hebben scenario's waarbij Vm's moeten worden vergrendeld omdat ze software hebben, zoals firewalls die zijn geïnstalleerd op de virtuele machine. In dit geval kunt u het [gecertificeerde test programma](https://aka.ms/AzureCertificationTestTool) hier downloaden en het rapport verzenden bij Partner Center- [ondersteuning](https://aka.ms/marketplacepublishersupport).

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Power shell gebruiken voor het gebruik van de Self-Test-API

### <a name="on-linux-os"></a>Op Linux-besturings systeem

De API aanroepen in Power shell:

1. Gebruik de Invoke-WebRequest opdracht om de API aan te roepen.
2. De methode is post en het inhouds type is JSON, zoals wordt weer gegeven in het volgende code voorbeeld en scherm opname.
3. Geef de hoofd tekst parameters op in JSON-indeling.

In dit volgende voor beeld ziet u een Power shell-aanroep voor de API:

```POWERSHELL
$accesstoken = "token"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$DNSName = "<Machine DNS Name>"
$UserName = "<User ID>"
$Password = "<Password>"
$OS = "Linux"
$PortNo = "22"
$CompanyName = "ABCD"
$AppID = "<Application ID>"
$TenantId = "<Tenant ID>"

$body = @{
   "DNSName" = $DNSName
   "UserName" = $UserName
   "Password" = $Password
   "OS" = $OS
   "PortNo" = $PortNo
   "CompanyName" = $CompanyName
   "AppID" = $AppID
   "TenantId" = $TenantId
} | ConvertTo-Json

$body

$uri = "URL"

$res = (Invoke-WebRequest -Method "Post" -Uri $uri -Body $body -ContentType "application/json" -Headers $headers).Content
```

<br>Hier volgt een voor beeld van het aanroepen van de API in Power shell:

[![Scherm voorbeeld voor het aanroepen van de API in Power shell.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>In het vorige voor beeld kunt u de JSON ophalen en parseren om de volgende details op te halen:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>In dit voorbeeld scherm, dat toont `$res.Content` , worden de details van de test resultaten weer gegeven in JSON-indeling:

[![Scherm voorbeeld voor het aanroepen van de API in Power shell met details van test resultaten.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Hier volgt een voor beeld van resultaten van JSON-testen die worden weer gegeven in een online JSON-Viewer (zoals [code beautify](https://codebeautify.org/jsonviewer) of [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Meer test resultaten in een online JSON-viewer.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>Op Windows-besturings systeem

De API aanroepen in Power shell:

1. Gebruik de Invoke-WebRequest opdracht om de API aan te roepen.
2. De methode is post en het inhouds type is JSON, zoals wordt weer gegeven in het volgende code voorbeeld en voor beeld scherm.
3. Maak de hoofd tekst-para meters in JSON-indeling.

Dit code voorbeeld toont een Power shell-aanroep van de API:

```PowerShell
$accesstoken = "Get token for your Client AAD App"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$Body = @{ 
   "DNSName" = "XXXX.westus.cloudapp.azure.com"
   "UserName" = "XXX"
   "Password" = "XXX@123456"
   "OS" = "Windows"
   "PortNo" = "5986"
   "CompanyName" = "ABCD"
   "AppID" = "XXXX-XXXX-XXXX"
   "TenantId" = "XXXX-XXXX-XXXX"
} | ConvertTo-Json

$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

Deze voorbeeld schermen tonen voor beeld voor het aanroepen van de API in Power shell:

**Met SSH-sleutel**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Het aanroepen van de API in Power shell met een SSH-sleutel.":::

**Met wacht woord**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Het aanroepen van de API in Power shell met een wacht woord.":::

<br>In het vorige voor beeld kunt u de JSON ophalen en parseren om de volgende details op te halen:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>In dit scherm ziet `$res.Content` u de details van de test resultaten in JSON-indeling:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="Details van test resultaten in JSON-indeling.":::

<br>Hier volgt een voor beeld van test resultaten die worden weer gegeven in een online JSON-Viewer (zoals [code beautify](https://codebeautify.org/jsonviewer) of [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Test resultaten in een online JSON-viewer.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>KRUL gebruiken om de Self-Test-API in Linux-besturings systeem te verbruiken

In dit voor beeld wordt krul gebruikt om een POST-API-aanroep naar Azure Active Directory en de Self-Host virtuele machine te maken.

1. Een Azure AD-token aanvragen om te verifiëren bij een self-host-VM

   Zorg ervoor dat de juiste waarden worden vervangen in het krul-verzoek.

   ```
   curl --location --request POST 'https://login.microsoftonline.com/{TENANT_ID}/oauth2/token' \
   --header 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'grant_type=client_credentials' \
   --data-urlencode 'client_id={CLIENT_ID} ' \
   --data-urlencode 'client_secret={CLIENT_SECRET}' \
   --data-urlencode 'resource=https://management.core.windows.net'
   ```
   Hier volgt een voor beeld van het antwoord van de aanvraag:
   ```JSON
   {
       "token_type": "Bearer",
       "expires_in": "86399",
       "ext_expires_in": "86399",
       "expires_on": "1599663998",
       "not_before": "1599577298",
       "resource": "https://management.core.windows.net",
       "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJS…"
   }
   ```

2. Een aanvraag indienen voor de zelf test-VM

   Zorg ervoor dat het Bearer-token en de-para meters worden vervangen door de juiste waarden.

   ```
   curl --location --request POST 'https://isvapp.azurewebsites.net/selftest-vm' \
   --header 'Content-Type: application/json' \
   --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJS…' \
   --data-raw '{
       "DNSName": "avvm1.eastus.cloudapp.azure.com",
       "UserName": "azureuser",
       "Password": "SECURE_PASSWORD_FOR_THE_SSH_INTO_VM",
       "OS": "Linux",
       "PortNo": "22",
       "CompanyName": "COMPANY_NAME",
       "AppId": "CLIENT_ID_SAME_AS_USED_FOR_AAD_TOKEN ",
       "TenantId": "TENANT_ID_SAME_AS_USED_FOR_AAD_TOKEN"
   }'
   ```

   Voorbeeld reactie van de API-aanroep zelf test-VM
   ```JSON
   {
       "TrackingId": "9bffc887-dd1d-40dd-a8a2-34cee4f4c4c3",
       "Response": "{\"SchemaVersion\":1,\"AppCertificationCategory\":\"Microsoft Single VM Certification\",\"ProviderID\":\"050DE427-2A99-46D4-817C-5354D3BF2AE8\",\"OSName\":\"Ubuntu 18.04\",\"OSDistro\":\"Ubuntu 18.04.5 LTS\",\"KernelVersion\":\"5.4.0-1023-azure\",\"KernelFullVersion\":\"Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\\n\",\"OSVersion\":\"18.04\",\"CreatedDate\":\"09/08/2020 01:13:47\",\"TestResult\":\"Pass\",\"APIVersion\":\"1.5\",\"Tests\":[{\"TestID\":\"48\",\"TestCaseName\":\"Bash History\",\"Description\":\"Bash history files should be cleared before creating the VM image.\",\"Result\":\"Passed\",\"ActualValue\":\"No file Exist\",\"RequiredValue\":\"1024\"},{\"TestID\":\"39\",\"TestCaseName\":\"Linux Agent Version\",\"Description\":\"Azure Linux Agent Version 2.2.41 and above should be installed.\",\"Result\":\"Passed\",\"ActualValue\":\"2.2.49\",\"RequiredValue\":\"2.2.41\"},{\"TestID\":\"40\",\"TestCaseName\":\"Required Kernel Parameters\",\"Description\":\"Verifies the following kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300\",\"Result\":\"Warning\",\"ActualValue\":\"Missing Parameter: rootdelay=300\\r\\nMatched Parameter: console=ttyS0,earlyprintk=ttyS0\",\"RequiredValue\":\"console=ttyS0#earlyprintk=ttyS0#rootdelay=300\"},{\"TestID\":\"41\",\"TestCaseName\":\"Swap Partition on OS Disk\",\"Description\":\"Verifies that no Swap partitions are created on the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"No. of Swap Partitions: 0\",\"RequiredValue\":\"swap\"},{\"TestID\":\"42\",\"TestCaseName\":\"Root Partition on OS Disk\",\"Description\":\"It is recommended that a single root partition is created for the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"Root Partition: 1\",\"RequiredValue\":\"1\"},{\"TestID\":\"44\",\"TestCaseName\":\"OpenSSL Version\",\"Description\":\"OpenSSL Version should be >=0.9.8.\",\"Result\":\"Passed\",\"ActualValue\":\"1.1.1\",\"RequiredValue\":\"0.9.8\"},{\"TestID\":\"45\",\"TestCaseName\":\"Python Version\",\"Description\":\"Python version 2.6+ is highly recommended. \",\"Result\":\"Passed\",\"ActualValue\":\"2.7.17\",\"RequiredValue\":\"2.6\"},{\"TestID\":\"46\",\"TestCaseName\":\"Client Alive Interval\",\"Description\":\"It is recommended to set ClientAliveInterval to 180. On the application need, it can be set between 30 to 235. \\nIf you are enabling the SSH for your end users this value must be set as explained.\",\"Result\":\"Warning\",\"ActualValue\":\"120\",\"RequiredValue\":\"ClientAliveInterval 180\"},{\"TestID\":\"49\",\"TestCaseName\":\"OS Architecture\",\"Description\":\"Only 64-bit operating system should be supported.\",\"Result\":\"Passed\",\"ActualValue\":\"x86_64\\n\",\"RequiredValue\":\"x86_64,amd64\"},{\"TestID\":\"50\",\"TestCaseName\":\"Security threats\",\"Description\":\"Identifies OS with recent high profile vulnerability that may need patching.  Ignore warning if system was patched as appropriate.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04\",\"RequiredValue\":\"OS impacted by GHOSTS\"},{\"TestID\":\"51\",\"TestCaseName\":\"Auto Update\",\"Description\":\"Identifies if Linux Agent Auto Update is enabled or not.\",\"Result\":\"Passed\",\"ActualValue\":\"# AutoUpdate.Enabled=y\\n\",\"RequiredValue\":\"Yes\"},{\"TestID\":\"52\",\"TestCaseName\":\"SACK Vulnerability patch verification\",\"Description\":\"Checks if the running Kernel Version has SACK vulnerability patch.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04.5 LTS,Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\",\"RequiredValue\":\"Yes\"}]}"
   }
   ```

## <a name="next-steps"></a>Volgende stappen

- [SAS-URI ophalen](azure-vm-get-sas-uri.md)
