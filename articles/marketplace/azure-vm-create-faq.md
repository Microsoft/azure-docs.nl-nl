---
title: Veelgestelde vragen over VM in azure Marketplace
description: Er zijn algemene vragen aangetroffen bij het maken van een virtuele machine in azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 03/10/2021
ms.openlocfilehash: a74170af61c05d07a189b5ceb61dc0c9b7e14298
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200427"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Veelgestelde vragen over VM in azure Marketplace

Deze veelgestelde vragen zijn van belang voor veelvoorkomende problemen die kunnen optreden bij het maken van een virtuele machine (VM)-aanbieding in azure Marketplace.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hoe kan ik een virtueel particulier netwerk (VPN) configureren om met mijn Vm's te werken?

Als u het Azure Resource Manager-implementatie model gebruikt, hebt u drie opties:

- [Een op een route gebaseerde VPN-gateway maken met behulp van de Azure Portal](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Een op een route gebaseerde VPN-gateway maken met behulp van Azure PowerShell](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Een op een route gebaseerde VPN-gateway maken met behulp van CLI](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Wat is micro soft-ondersteunings beleid voor het uitvoeren van micro soft-server software op Vm's op basis van Azure?

Meer informatie vindt u op de [micro soft-server software ondersteuning voor Microsoft Azure virtuele machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Hoe kan ik in een virtuele machine de aangepaste script extensie in de opstart taak beheren?

Zie [aangepaste script extensie voor Windows](../virtual-machines/extensions/custom-script-windows.md)voor meer informatie over het gebruik van de aangepaste script extensie met behulp van de module Azure PowerShell, Azure Resource Manager sjablonen en stappen voor probleem oplossing op Windows-systemen.

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Worden 32-bits toepassingen of services ondersteund in azure Marketplace?

Nee. De ondersteunde besturings systemen en standaard services voor virtuele Azure-machines zijn allemaal 64 bits. Hoewel de meeste 64-bits besturings systemen 32-bits versies van toepassingen ondersteunen voor achterwaartse compatibiliteit, is het gebruik van 32-bits toepassingen als onderdeel van uw VM-oplossing niet-ondersteund en sterk afgeraden. Maak uw toepassing opnieuw als een 64-bits project.

Raadpleeg deze artikelen voor meer informatie:

- [Het uitvoeren van 32-bits toepassingen](/windows/desktop/WinProg64/running-32-bit-applications)
- [Ondersteuning voor 32-bits besturingssystemen in virtuele Azure-machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Ondersteuning van Microsoft-serversoftware voor virtuele Microsoft Azure-machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Fout: VHD is al geregistreerd bij de opslag plaats van de installatie kopie als de resource

Telkens wanneer ik een installatie kopie van mijn Vhd's probeer te maken, krijg ik de fout ' VHD is al geregistreerd bij de opslag plaats van de installatie kopie als de resource ' in Azure PowerShell. Ik heb geen installatie kopie gemaakt vóór en ik heb een installatie kopie met deze naam in azure gevonden. Hoe los ik dit op?

Dit probleem wordt doorgaans weer gegeven als u een virtuele machine hebt gemaakt op basis van een virtuele harde schijf met een vergren deling. Controleer of er geen VM is toegewezen van deze VHD en voer de bewerking opnieuw uit. Als dit probleem zich blijft voordoen, opent u een ondersteunings ticket. Zie [ondersteuning voor Partner Center](support.md).

## <a name="how-do-i-create-a-vm-from-a-generalized-vhd"></a>Hoe kan ik een VM maken op basis van een gegeneraliseerde VHD?

### <a name="prepare-an-azure-resource-manager-template"></a>Een Azure Resource Manager sjabloon voorbereiden

In deze sectie wordt beschreven hoe u een installatie kopie van een door de gebruiker gedefinieerde virtuele machine (VM) maakt en implementeert. U kunt dit doen door VHD-installatie kopieën van besturings systeem en gegevens schijven te voorzien van een door Azure geïmplementeerde virtuele harde schijf. Met deze stappen implementeert u de virtuele machine met gegeneraliseerde VHD.

1. Meld u aan bij de Azure-portal.
2. Upload uw gegeneraliseerde virtuele harde schijf van het besturings systeem en de Vhd's met gegevens schijven naar uw Azure Storage-account.
3. Op de start pagina selecteert u een resource maken, zoekt u naar "sjabloon implementatie" en selecteert u maken.
4. Kies uw eigen sjabloon bouwen in de editor.

   :::image type="content" source="media/vm/template-deployment.png" alt-text="Toont de selectie van een sjabloon":::

5. Plak de volgende JSON-sjabloon in de editor en selecteer Opslaan.
 ```json
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


6. Geef de parameter waarden op voor de weer gegeven eigenschappen pagina's met aangepaste implementatie.

 **TABEL 1**

| **ResourceGroupName** | **De naam van de bestaande Azure-resource groep. Gebruik normaal gesp roken dezelfde RG als uw sleutel kluis.** |
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
|

7. Nadat u deze waarden hebt opgegeven, selecteert u kopen.

De implementatie van Azure wordt gestart. Er wordt een nieuwe virtuele machine gemaakt met de opgegeven onbeheerde VHD in het opgegeven pad van het opslag account. U kunt de voortgang van de Azure Portal volgen door Virtual Machines aan de linkerkant van de portal te selecteren. Wanneer de virtuele machine wordt gemaakt, wordt de status gewijzigd van gestart in actief.

Voor de VM-implementatie van generatie 2 gebruikt u deze sjabloon:
 ```json
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


### <a name="deploy-an-azure-vm-using-powershell"></a>Een Azure-VM implementeren met behulp van Power shell

Kopieer en bewerk het volgende script om waarden voor de variabelen en op te geven `$storageaccount` `$vhdUrl` . Voer deze uit om een Azure VM-resource te maken op basis van uw bestaande gegeneraliseerde VHD.

```powershell
# storage account of existing generalized VHD
$storageaccount = "testwinrm11815"
# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName"
```


## <a name="next-steps"></a>Volgende stappen

- [Problemen oplossen met VM-certificering](azure-vm-create-certification-faq.md)
