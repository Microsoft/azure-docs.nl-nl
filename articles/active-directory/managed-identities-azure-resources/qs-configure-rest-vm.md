---
title: Beheerde identiteiten configureren op Azure VM met behulp van REST - Azure AD
description: Stapsgewijze instructies voor het configureren van door het systeem en door de gebruiker toegewezen beheerde identiteiten op een Azure VM met behulp van CURL om REST API-aanroepen te maken.
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
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: f70229fabe4331adb7740cf09cf2bf8f3e3e4617
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587516"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Beheerde identiteiten voor Azure-resources op een Azure VM configureren met behulp van REST API-aanroepen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services een identiteit van een automatisch beheerd systeem in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben. 

In dit artikel leert u om met behulp van CURL het Azure Resource Manager REST-eindpunt aan te roepen en daarmee de volgende beheerde identiteiten uit te voeren voor bewerkingen van Azure-resources op een Azure VM:

- De door een systeem toegewezen beheerde identiteit op een Azure VM in- en uitschakelen
- De door een gebruiker toegewezen beheerde identiteit op een Azure VM toevoegen en verwijderen

Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="prerequisites"></a>Vereisten

- Zie [Wat zijn beheerde identiteiten voor Azure-resources?](overview.md) als u niet bekend met beheerde identiteiten voor Azure-resources. Zie [Beheerde identiteitstypen](overview.md#managed-identity-types) voor meer informatie over door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteitstypen.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u een door het systeem toegewezen beheerde identiteit op een Azure VM kunt inschakelen en uitschakelen waarbij u CURL gebruikt om het Azure Resource Manager REST-eindpunt aan te roepen.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een Azure-VM

Als u een Azure-VM wilt maken met de door het systeem toegewezen beheerde identiteit, moet uw account beschikken over de roltoewijzing [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Maak met [az group create](/cli/azure/group/#az-group-create) een [resourcegroep](../../azure-resource-manager/management/overview.md#terminology) voor insluiting en implementatie van uw VM en de bijbehorende bronnen. U kunt deze stap overslaan als u al een resourcegroep hebt die u in plaats daarvan wilt gebruiken:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Een [netwerkinterface](/cli/azure/network/nic#az-network-nic-create) voor uw VM maken:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Haal een Bearer-toegangstoken op om in de volgende stap in de autorisatie-header te gebruiken voor het maken van uw VM met een door een systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Maak in Azure Cloud Shell een VM door met behulp van CURL het Azure Resource Manager REST-eindpunt aan te roepen. In het volgende voorbeeld wordt een VM gemaakt met de naam *myVM* met een door een systeem toegewezen beheerde identiteit, zoals aangegeven in de aanvraagbody met de waarde `"identity":{"type":"SystemAssigned"}`. Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen toen u een Bearer-toegangstoken en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Aanvraagheaders**
   
   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.        | 
   
   **Aanvraagbody**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
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
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen op een bestaande Azure VM

Als u een door het systeem toegewezen beheerde identiteit wilt inschakelen op een VM die oorspronkelijk zonder deze identiteit werd ingericht, heeft uw account de roltoewijzing [Inzender van de virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nodig.  Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Haal een Bearer-toegangstoken op om in de volgende stap in de autorisatie-header te gebruiken voor het maken van uw VM met een door een systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Gebruik de volgende CURL-opdracht om het Azure Resource Manager REST-eindpunt aan te roepen, zodat u de door het systeem toegewezen beheerde identiteit kunt inschakelen op uw VM, zoals aangegeven in de aanvraagbody door de waarde `{"identity":{"type":"SystemAssigned"}` voor een VM met de naam *myVM*.  Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen toen u een Bearer-toegangstoken en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.
   
   > [!IMPORTANT]
   > U moet de door de gebruiker toegewezen beheerde identiteiten weergeven met behulp van de volgende CURL-opdracht, zodat u geen bestaande door de gebruiker aan de VM toegewezen beheerde identiteiten verwijdert: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Als u door de gebruiker toegewezen beheerde identiteiten hebt die zijn toegewezen aan de VM, zoals aangegeven in de `identity`-waarde in het antwoord, gaat u verder met stap 3 waarin wordt getoond hoe u door de gebruiker toegewezen beheerde identiteiten kunt behouden terwijl de door het systeem toegewezen beheerde identiteit wordt ingeschakeld op uw VM.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.        | 
   
   **Aanvraagbody**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Als u een door het systeem toegewezen beheerde identiteit wilt inschakelen op een VM met bestaande door de gebruiker toegewezen beheerde identiteiten, moet u `SystemAssigned` toevoegen aan de `type`-waarde.  
   
   Als aan uw VM bijvoorbeeld de door de gebruiker toegewezen beheerde identiteiten `ID1` en `ID2` zijn toegewezen, en u een door het systeem toegewezen beheerde identiteit wilt toevoegen aan de VM, gebruikt u de volgende CURL-aanroep. Vervang `<ACCESS TOKEN>` en `<SUBSCRIPTION ID>` door de waarden die van toepassing zijn op uw omgeving.

   In API-versie `2018-06-01` worden door de gebruiker toegewezen beheerde identiteiten opgeslagen in de `userAssignedIdentities`-waarde in een woordenlijstindeling, dit in tegenstelling tot de `identityIds`-waarde in een matrixindeling die wordt gebruikt in API-versie `2017-12-01`.
   
   **API VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.        | 

   **Aanvraagbody**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **API VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.        | 

   **Aanvraagbody**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Door het systeem toegewezen beheerde identiteit van een Azure-VM uitschakelen

Als u de door het systeem toegewezen beheerde identiteit op een VM wilt uitschakelen, moet uw account beschikken over de roltoewijzing [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Haal een Bearer-toegangstoken op om in de volgende stap in de autorisatie-header te gebruiken voor het maken van uw VM met een door een systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Werk de VM waarbij u met CURL het Azure Resource Manager REST-eindpunt aanroept om de door het systeem toegewezen beheerde identiteit uit te schakelen.  In het volgende voorbeeld wordt de door het systeem toegewezen beheerde identiteit uitgeschakeld zoals die in de aanvraagbody wordt geïdentificeerd door de waarde `{"identity":{"type":"None"}}` van een VM met de naam *myVM*.  Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen toen u een Bearer-toegangstoken en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.

   > [!IMPORTANT]
   > U moet de door de gebruiker toegewezen beheerde identiteiten weergeven met behulp van de volgende CURL-opdracht, zodat u geen bestaande door de gebruiker aan de VM toegewezen beheerde identiteiten verwijdert: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Als u door de gebruiker toegewezen beheerde identiteiten hebt die zijn toegewezen aan de VM, zoals aangegeven in de `identity`-waarde in het antwoord, gaat u verder met stap 3 waarin wordt getoond hoe u door de gebruiker toegewezen beheerde identiteiten kunt behouden terwijl de door het systeem toegewezen beheerde identiteit wordt uitgeschakeld op uw VM.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.        | 

   **Aanvraagbody**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Als u een door het systeem toegewezen beheerde identiteit wilt verwijderen uit een virtuele machine met door de gebruiker toegewezen beheerde identiteiten, verwijdert u `SystemAssigned` uit de `{"identity":{"type:" "}}`-waarde en behoudt u de `UserAssigned`-waarde en de `userAssignedIdentities`-woordenlijstwaarden als u **API-versie 2018-06-01** gebruikt. Als u **API-versie 2017-12-01** of eerder gebruikt, moet u de `identityIds`-matrix blijven gebruiken.

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u een door de gebruiker toegewezen beheerde identiteit kunt toevoegen aan/verwijderen uit een Azure VM door met behulp van CURL het Azure Resource Manager REST-eindpunt aan te roepen.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Door de gebruiker toegewezen beheerde identiteit inschakelen tijdens het maken van een Azure VM

Als u een door een gebruiker toegewezen identiteit wilt toewijzen aan een VM, moet uw account beschikken over de roltoewijzingen [Inzender van de virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) en [Operator van de beheerde identiteit](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Haal een Bearer-toegangstoken op om in de volgende stap in de autorisatie-header te gebruiken voor het maken van uw VM met een door een systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Een [netwerkinterface](/cli/azure/network/nic#az-network-nic-create) voor uw VM maken:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Haal een Bearer-toegangstoken op om in de volgende stap in de autorisatie-header te gebruiken voor het maken van uw VM met een door een systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Maak een door de gebruiker toegewezen beheerde identiteit met de volgende instructies: [Een door een gebruiker toegewezen beheerde identiteit maken](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Maak een VM met behulp van CURL om het Azure Resource Manager REST-eindpunt aan te roepen. In het volgende voorbeeld wordt een VM gemaakt met de naam *myVM* in de resourcegroep *myResourceGroup* met een door de gebruiker toegewezen beheerde identiteit `ID1`, zoals aangegeven in de aanvraagbody met de waarde `"identity":{"type":"UserAssigned"}`. Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen toen u een Bearer-toegangstoken en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.
 
   **API VERSION 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.        | 

   **Aanvraagbody**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
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
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **API VERSION 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.        | 

   **Aanvraagbody**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
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
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande VM van Azure

Als u een door een gebruiker toegewezen identiteit wilt toewijzen aan een VM, moet uw account beschikken over de roltoewijzingen [Inzender van de virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) en [Operator van de beheerde identiteit](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Haal een Bearer-toegangstoken op om in de volgende stap in de autorisatie-header te gebruiken voor het maken van uw VM met een door een systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Maak een door de gebruiker toegewezen beheerde identiteit met behulp van de hier vermelde instructies: [Een door de gebruiker toegewezen beheerde identiteit maken](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. U moet de identiteitstypen weergeven die aan de VM zijn toegewezen met behulp van de volgende CURL-opdracht, zodat u geen bestaande door de gebruiker of het systeem aan de VM toegewezen identiteiten verwijdert. Als u beheerde identiteiten hebt die zijn toegewezen aan de virtuele-machineschaalset, worden deze weergegeven onderin de `identity`-waarde.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.

    Als u door de gebruiker of het systeem toegewezen beheerde identiteiten hebt die zijn toegewezen aan de VM, zoals aangegeven in de `identity`-waarde in het antwoord, gaat u verder met stap 5 waarin wordt getoond hoe u door het systeem toegewezen beheerde identiteiten kunt behouden terwijl een door de gebruiker toegewezen beheerde identiteit op uw VM wordt toegevoegd.

4. Als u geen door de gebruiker of het systeem toegewezen beheerde identiteiten hebt die aan de VM zijn toegewezen, gebruikt u de volgende CURL-opdracht om het Azure Resource Manager REST-eindpunt aan te roepen voor het toewijzen van de eerste door de gebruiker toegewezen beheerde identiteit aan de VM.

   In het volgende voorbeeld wordt een door de gebruiker toegewezen beheerde identiteit `ID1` toegewezen aan een VM met de naam *myVM* in de resourcegroep *myResourceGroup*.  Vervang `<ACCESS TOKEN>` door de waarde die u in de vorige stap hebt ontvangen toen u een Bearer-toegangstoken en de `<SUBSCRIPTION ID>`-waarde hebt aangevraagd die van toepassing zijn voor uw omgeving.

   **API VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.        |
 
   **Aanvraagbody**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **API VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.        | 

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

5. Als u een bestaande door de gebruiker of door het systeem toegewezen beheerde identiteit hebt die is toegewezen aan uw VM:
   
   **API VERSION 2018-06-01**

   Voeg de door de gebruiker toegewezen beheerde identiteit toe aan de woordenlijstwaarde `userAssignedIdentities`.
    
   Als u bijvoorbeeld een door het systeem toegewezen beheerde identiteit en de momenteel door de gebruiker aan uw VM toegewezen beheerde identiteit `ID1` hebt en u de door de gebruiker toegewezen beheerde identiteit `ID2` wilt toevoegen:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.        | 

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

   Als u bijvoorbeeld een door het systeem toegewezen beheerde identiteit en de momenteel door de gebruiker aan uw VM toegewezen beheerde identiteit `ID1` hebt en u de door de gebruiker toegewezen beheerde identiteit `ID2` wilt toevoegen: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.        | 

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>De door de gebruiker toegewezen beheerde identiteit van een Azure VM verwijderen

Als u de door de gebruiker toegewezen identiteit van een VM wilt verwijderen, moet uw account beschikken over de roltoewijzing [Inzender voor virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

1. Haal een Bearer-toegangstoken op om in de volgende stap in de autorisatie-header te gebruiken voor het maken van uw VM met een door een systeem toegewezen beheerde identiteit.

   ```azurecli-interactive
   az account get-access-token
   ```

2. U moet de beheerde identiteiten weergeven met behulp van de volgende CURL-opdracht, zodat u geen bestaande door de gebruiker toegewezen beheerde identiteiten verwijdert die aan de VM toegewezen moeten blijven of de aan het systeem toegewezen beheerde identiteit verwijdert: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.
 
   Als u beheerde identiteiten hebt die aan de virtuele machine zijn toegewezen, worden deze in het antwoord in de `identity`-waarde weergegeven.

   Als bijvoorbeeld de door de gebruiker toegewezen beheerde identiteiten `ID1` en `ID2` zijn toegewezen aan uw VM en u wilt dat alleen `ID1` toegewezen blijft en dat de door het systeem toegewezen identiteit behouden blijft:
   
   **API VERSION 2018-06-01**

   Voeg `null` toe aan de door de gebruiker toegewezen beheerde identiteit die u wilt verwijderen:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.        | 

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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Aanvraagheaders**

   |Aanvraagheader  |Beschrijving  |
   |---------|---------|
   |*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
   |*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.        | 

   **Aanvraagbody**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Als uw VM zowel door het systeem toegewezen als door de gebruiker toegewezen beheerde identiteiten bevat, kunt u alle door de gebruiker toegewezen beheerde identiteiten verwijderen door te schakelen naar het gebruik van alleen door het systeem toegewezen beheerde identiteit met de volgende opdracht:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
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
    
Als uw VM alleen door de gebruiker toegewezen beheerde identiteiten bevat en u deze allemaal wilt verwijderen, gebruikt u de volgende opdracht:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Aanvraagheaders**

|Aanvraagheader  |Beschrijving  |
|---------|---------|
|*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
|*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.| 

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

- [Door de gebruiker toegewezen beheerde identiteiten maken, weergeven of verwijderen met behulp van REST API-aanroepen](how-to-manage-ua-identity-rest.md)
