---
title: Beheerde identiteiten configureren op een virtuele-machineschaalset met behulp van REST - Azure Active Directory
description: Stapsgewijze instructies voor het configureren van door het systeem en de gebruiker toegewezen beheerde identiteiten op een Azure virtuele-machineschaalset met behulp van CURL om REST API-aanroepen te maken.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb14a6a82535ac5a7d36213b082d08aedbb44da5
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090617"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Beheerde identiteiten voor Azure-resources configureren op een virtuele-machineschaalset met behulp van REST API-aanroepen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services een identiteit met een automatisch beheerd systeem in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben. 

In dit artikel leert u om met behulp van CURL het Azure Resource Manager REST-eindpunt aan te roepen en daarmee de volgende beheerde identiteiten uit te voeren voor bewerkingen van Azure-resources op een virtuele-machineschaalset:

- De door het systeem toegewezen beheerde identiteit inschakelen en uitschakelen op een virtuele-machineschaalset van Azure
- Een door de gebruiker toegewezen beheerde identiteit toevoegen aan en verwijderen uit een virtuele-machineschaalset van Azure

Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="prerequisites"></a>Vereisten

- Zie [Wat zijn beheerde identiteiten voor Azure-resources?](overview.md) als u niet bekend met beheerde identiteiten voor Azure-resources. Zie [Beheerde identiteitstypen](overview.md#managed-identity-types) voor meer informatie over door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteitstypen.

- Voor het uitvoeren van de beheerbewerkingen in dit artikel zijn voor uw account de volgende Azure-roltoewijzingen nodig:

  - [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) voor het maken van een virtuele-machineschaalset en het inschakelen en verwijderen van een door het systeem en/of de gebruiker toegewezen beheerde identiteit op/uit een virtuele-machineschaalset.

  - De rol van [inzender voor beheerde identiteit](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) voor het maken van een door de gebruiker toegewezen beheerde identiteit.

  - De rol van [operator voor beheerde identiteit](../../role-based-access-control/built-in-roles.md#managed-identity-operator) voor het toewijzen/verwijderen van een door de gebruiker toegewezen identiteit aan/uit een virtuele-machine schaalset.

  > [!NOTE]
  > Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-map.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u een door het systeem toegewezen beheerde identiteit op een virtuele-machineschaalset kunt inschakelen en uitschakelen waarbij u CURL gebruikt om het Azure Resource Manager REST-eindpunt aan te roepen.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een virtuele-machineschaalset

Als u een virtuele-machineschaalset wilt maken waarvoor een door het systeem toegewezen beheerde identiteit is ingeschakeld, moet u een virtuele-machineschaalset maken en een toegangstoken ophalen om met CURL het Resource Manager-eindpunt aan te roepen met de waarde van het door het systeem toegewezen beheerde identiteitstype.

1. Maak met [az group create](/cli/azure/group/#az-group-create) een [resourcegroep](../../azure-resource-manager/management/overview.md#terminology) voor de insluiting en implementatie van uw virtuele-machineschaalset en de bijbehorende resources. U kunt deze stap overslaan als u al een resourcegroep hebt die u in plaats daarvan wilt gebruiken:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Maak een [netwerkinterface](/cli/azure/network/nic#az-network-nic-create) voor uw virtuele-machineschaalset:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Haal een Bearer-toegangstoken op dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw virtuele-machineschaalset te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Maak in Azure Cloud Shell een virtuele-machineschaalset door met behulp van CURL het Azure Resource Manager REST-eindpunt aan te roepen. In het volgende voorbeeld wordt een virtuele-machineschaalset gemaakt met de naam *myVMSS* in de resourcegroep *myResourceGroup* met een door het systeem toegewezen beheerde identiteit, zoals aangegeven in de aanvraagbody met de waarde `"identity":{"type":"SystemAssigned"}`. Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen toen u een Bearer-toegangstoken en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken. | 

   **Aanvraagbody**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
             "storageProfile":{
                "imageReference":{
                   "sku":"2016-Datacenter",
                   "publisher":"MicrosoftWindowsServer",
                   "version":"latest",
                   "offer":"WindowsServer"
                },
                "osDisk":{
                   "caching":"ReadWrite",
                   "managedDisk":{
                      "storageAccountType":"Standard_LRS"
                   },
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }  
   ```  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Een door het systeem toegewezen beheerde identiteit inschakelen op een bestaande virtuele-machineschaalset

Als u een door het systeem toegewezen beheerde identiteit wilt inschakelen op een bestaande virtuele-machineschaalset, moet u een toegangstoken verkrijgen en vervolgens met CURL het Resource Manager REST-eindpunt aanroepen om het identiteitstype bij te werken.

1. Haal een Bearer-toegangstoken op dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw virtuele-machineschaalset te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Gebruik de volgende CURL-opdracht om het Azure Resource Manager REST-eindpunt aan te roepen, zodat u de door het systeem toegewezen beheerde identiteit kunt inschakelen voor de virtuele-machineschaalset, zoals aangegeven in de aanvraagbody door de waarde `{"identity":{"type":"SystemAssigned"}` voor een virtuele-machineschaalset met de naam *myVMSS*.  Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen toen u een Bearer-toegangstoken en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.
   
   > [!IMPORTANT]
   > U moet de door de gebruiker toegewezen beheerde identiteiten weergeven met behulp van de volgende CURL-opdracht `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`, zodat u geen bestaande door de gebruiker aan de virtuele-machineschaalset toegewezen beheerde identiteiten verwijdert. Als u door de gebruiker toegewezen beheerde identiteiten hebt die zijn toegewezen aan de virtuele-machineschaalset, zoals aangegeven in de `identity`-waarde in het antwoord, gaat u verder met stap 3 waarin wordt getoond hoe u door de gebruiker toegewezen beheerde identiteiten kunt behouden terwijl de door het systeem toegewezen beheerde identiteit wordt ingeschakeld op uw virtuele-machineschaalset.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken. | 

   **Aanvraagbody**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned"
       }
    }
   ```

3. Als u een door het systeem toegewezen beheerde identiteit wilt inschakelen op een virtuele-machineschaalset met bestaande door de gebruiker toegewezen beheerde identiteiten, moet u `SystemAssigned` toevoegen aan de `type`-waarde.  
   
   Als aan uw virtuele-machineschaalset bijvoorbeeld de door de gebruiker toegewezen beheerde identiteiten `ID1` en `ID2` zijn toegewezen, en u een de door het systeem toegewezen beheerde identiteit wilt toevoegen aan de virtuele-machineschaalset, gebruikt u de volgende CURL-aanroep. Vervang `<ACCESS TOKEN>` en `<SUBSCRIPTION ID>` door de waarden die van toepassing zijn op uw omgeving.

   In API-versie `2018-06-01` worden door de gebruiker toegewezen beheerde identiteiten opgeslagen in de `userAssignedIdentities`-waarde in een woordenlijstindeling, dit in tegenstelling tot de `identityIds`-waarde in een matrixindeling die wordt gebruikt in API-versie `2017-12-01`.
   
   **API VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken. |
 
   **Aanvraagbody**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```
   
   **API VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken. | 

   **Aanvraagbody**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Een door het systeem toegewezen beheerde identiteit uitschakelen vanuit een virtuele-machineschaalset

Als u een door het systeem toegewezen identiteit wilt uitschakelen op een bestaande virtuele-machineschaalset, moet u een toegangstoken verkrijgen en vervolgens met CURL het Resource Manager REST-eindpunt aanroepen om het identiteitstype bij te werken naar `None`.

1. Haal een Bearer-toegangstoken op dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw virtuele-machineschaalset te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Werk de virtuele-machineschaalset waarbij u met CURL het Azure Resource Manager REST-eindpunt aanroept om de door het systeem toegewezen beheerde identiteit uit te schakelen.  In het volgende voorbeeld wordt een door het systeem toegewezen beheerde identiteit zoals aangegeven in de aanvraagbody met de waarde `{"identity":{"type":"None"}}` uitgeschakeld vanuit een virtuele-machineschaalset met de naam *myVMSS*.  Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen toen u een Bearer-toegangstoken en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.

   > [!IMPORTANT]
   > U moet de door de gebruiker toegewezen beheerde identiteiten weergeven met behulp van de volgende CURL-opdracht `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`, zodat u geen bestaande door de gebruiker aan de virtuele-machineschaalset toegewezen beheerde identiteiten verwijdert. Als u een door de gebruiker aan de virtuele-machineschaalset toegewezen beheerde identiteit hebt, gaat u verder met stap 3 waarin wordt getoond hoe u door de gebruiker toegewezen beheerde identiteiten kunt behouden terwijl de door het systeem toegewezen beheerde identiteit wordt verwijderd van uw virtuele-machineschaalset.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken. | 

   **Aanvraagbody**

   ```JSON
    {
       "identity":{
          "type":"None"
       }
    }
   ```

   Als u een door het systeem toegewezen beheerde identiteit wilt verwijderen uit een virtuele-machineschaalset met door de gebruiker toegewezen beheerde identiteiten, verwijdert u `SystemAssigned` uit de `{"identity":{"type:" "}}`-waarde en behoudt u de `UserAssigned`-waarde en de `userAssignedIdentities`-woordenlijstwaarden als u **API-versie 2018-06-01** gebruikt. Als u **API-versie 2017-12-01** of eerder gebruikt, moet u de `identityIds`-matrix blijven gebruiken.

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u een door de gebruiker toegewezen beheerde identiteit kunt toevoegen aan/verwijderen uit een virtuele-machineschaalset, door met behulp van CURL het Azure Resource Manager REST-eindpunt aan te roepen.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Door de gebruiker toegewezen beheerde identiteit inschakelen tijdens het maken van een virtuele-machineschaalset

1. Haal een Bearer-toegangstoken op dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw virtuele-machineschaalset te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Maak een [netwerkinterface](/cli/azure/network/nic#az-network-nic-create) voor uw virtuele-machineschaalset:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Haal een Bearer-toegangstoken op dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw virtuele-machineschaalset te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Maak een door de gebruiker toegewezen beheerde identiteit met de hier vermelde instructies: [Maak een door de gebruiker toegewezen beheerde identiteit](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Maak een virtuele-machineschaalset door met behulp van CURL het Azure Resource Manager REST-eindpunt aan te roepen. In het volgende voorbeeld wordt een virtuele-machineschaalset gemaakt met de naam *myVMSS* in de resourcegroep *myResourceGroup* met een door de gebruiker toegewezen beheerde identiteit `ID1`, zoals aangegeven in de aanvraagbody met de waarde `"identity":{"type":"UserAssigned"}`. Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen toen u een Bearer-toegangstoken en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.
 
   **API VERSION 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken. | 

   **Aanvraagbody**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
             "storageProfile":{
                "imageReference":{
                   "sku":"2016-Datacenter",
                   "publisher":"MicrosoftWindowsServer",
                   "version":"latest",
                   "offer":"WindowsServer"
                },
                "osDisk":{
                   "caching":"ReadWrite",
                   "managedDisk":{
                      "storageAccountType":"Standard_LRS"
                   },
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```   

   **API VERSION 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken. |
 
   **Aanvraagbody**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
             "storageProfile":{
                "imageReference":{
                   "sku":"2016-Datacenter",
                   "publisher":"MicrosoftWindowsServer",
                   "version":"latest",
                   "offer":"WindowsServer"
                },
                "osDisk":{
                   "caching":"ReadWrite",
                   "managedDisk":{
                      "storageAccountType":"Standard_LRS"
                   },
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande virtuele-machineschaalset van Azure

1. Haal een Bearer-toegangstoken op dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw virtuele-machineschaalset te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Maak een door de gebruiker toegewezen beheerde identiteit met behulp van de hier vermelde instructies: [Een door de gebruiker toegewezen beheerde identiteit maken](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. U moet de identiteitstypen weergeven die aan de virtuele-machineschaalset zijn toegewezen met behulp van de volgende CURL-opdracht, zodat u geen bestaande door de gebruiker of het systeem aan de virtuele-machineschaalset toegewezen identiteiten verwijdert. Als u beheerde identiteiten hebt die zijn toegewezen aan de virtuele-machineschaalset, worden deze weergegeven in de `identity`-waarde.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken. |   
 

4. Als u geen door de gebruiker of het systeem toegewezen beheerde identiteiten hebt die aan de virtuele-machineschaalset zijn toegewezen, gebruikt u de volgende CURL-opdracht om het Azure Resource Manager REST-eindpunt aan te roepen voor het toewijzen van de eerste door de gebruiker toegewezen beheerde identiteit aan de virtuele-machineschaalset.  Als u door de gebruiker of het systeem toegewezen beheerde identiteiten hebt die zijn toegewezen aan de virtuele-machineschaalset, gaat u verder met stap 5 waarin wordt getoond hoe u meerdere door de gebruiker toegewezen beheerde identiteiten aan een virtuele-machineschaalset kunt toevoegen terwijl u tevens de door het systeem toegewezen beheerde identiteit behoudt.

   In het volgende voorbeeld wordt een door de gebruiker toegewezen beheerde identiteit `ID1` toegewezen aan een virtuele-machineschaalset met de naam *myVMSS* in de resourcegroep *myResourceGroup*.  Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen toen u een Bearer-toegangstoken en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.

   **API VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken. | 

   **Aanvraagbody**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ``` 
    
   **API VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken. | 

   **Aanvraagbody**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```  

5. Als u een bestaande door de gebruiker of door het systeem toegewezen beheerde identiteit hebt die is toegewezen aan uw virtuele-machineschaalset:
   
   **API VERSION 2018-06-01**

   Voeg de door de gebruiker toegewezen beheerde identiteit toe aan de woordenlijstwaarde `userAssignedIdentities`.

   Als u bijvoorbeeld een door het systeem toegewezen beheerde identiteit en de momenteel door de gebruiker aan uw virtuele-machineschaalset toegewezen beheerde identiteit `ID1` hebt en u de door de gebruiker toegewezen beheerde identiteit `ID2` wilt toevoegen:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken. | 

   **Aanvraagbody**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **API VERSION 2017-12-01**

   Bewaar de door de gebruiker toegewezen beheerde identiteiten die u in de matrixwaarde `identityIds` wilt houden waarbij u de nieuwe door de gebruiker toegewezen beheerde identiteit toevoegt.

   Als u bijvoorbeeld een door het systeem toegewezen identiteit en de momenteel door de gebruiker aan uw virtuele-machineschaalset toegewezen beheerde identiteit `ID1` hebt en u hieraan de door de gebruiker toegewezen beheerde identiteit `ID2` wilt toevoegen:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

    **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken. | 

   **Aanvraagbody**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit uit een virtuele-machineschaalset verwijderen

1. Haal een Bearer-toegangstoken op dat u in de volgende stap in de autorisatie-header gaat gebruiken om uw virtuele-machineschaalset te maken met een door het systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. U moet de beheerde identiteiten weergeven met behulp van de volgende CURL-opdracht, zodat u geen bestaande door de gebruiker toegewezen beheerde identiteiten verwijdert die aan de virtuele-machineschaalset toegewezen moeten blijven of de aan het systeem toegewezen beheerde identiteit verwijdert:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken. |
   
   Als u beheerde identiteiten hebt die aan de virtuele machine zijn toegewezen, worden deze in het antwoord in de `identity`-waarde weergegeven. 
    
   Als bij u bijvoorbeeld de door de gebruiker toegewezen beheerde identiteiten `ID1` en `ID2` zijn toegewezen aan uw virtuele-machineschaalset, en u wilt dat alleen `ID1` blijft toegewezen en dat de door het systeem toegewezen beheerde identiteit behouden blijft:

   **API VERSION 2018-06-01**

   Voeg `null` toe aan de door de gebruiker toegewezen beheerde identiteit die u wilt verwijderen:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken. | 

   **Aanvraagbody**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **API VERSION 2017-12-01**

   Bewaar alleen de door de gebruiker toegewezen beheerde identiteit(en) die u in de `identityIds`-matrix wilt behouden:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken. | 

   **Aanvraagbody**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Als uw virtuele-machineschaalset zowel door het systeem toegewezen als door de gebruiker toegewezen beheerde identiteiten bevat, kunt u alle door de gebruiker toegewezen beheerde identiteiten verwijderen door te schakelen naar het gebruik van alleen het door het systeem toegewezen beheerde identiteiten met de volgende opdracht:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Aanvraagheaders**

|Aanvraagheader  |Beschrijving  |
|---------|---------|
|*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
|*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken. | 

**Aanvraagbody**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Als uw virtuele-machineschaalset alleen door de gebruiker toegewezen beheerde identiteiten bevat en u deze allemaal wilt verwijderen, gebruikt u de volgende opdracht:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Aanvraagheaders**

|Aanvraagheader  |Beschrijving  |
|---------|---------|
|*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
|*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken. | 

**Aanvraagbody**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Volgende stappen

Zie voor informatie over het maken, weergeven of verwijderen van door de gebruiker toegewezen beheerde identiteiten met behulp van REST:

- [Een door de gebruiker toegewezen beheerde identiteit maken, weergeven of verwijderen met REST API-aanroepen](how-to-manage-ua-identity-rest.md)
