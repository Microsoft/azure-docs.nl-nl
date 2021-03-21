---
title: Beheerde identiteiten configureren op Azure VM met behulp van Azure CLI - Azure AD
description: Stapsgewijze instructies voor het configureren van door het systeem en de gebruiker toegewezen beheerde identiteiten op een Azure VM met behulp van Azure CLI.
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
ms.openlocfilehash: 90b18c2556796f23be0c1135b0bad0d53368a46d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590950"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Beheerde identiteiten voor Azure-resources op een virtuele Azure-machine configureren met behulp van Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben. 

In dit artikel leert u om met behulp van Azure CLI de volgende beheerde identiteiten uit te voeren voor bewerkingen van Azure-resources op een virtuele Azure-machine:

- De door een systeem toegewezen beheerde identiteit op een Azure VM in- en uitschakelen
- De door een gebruiker toegewezen beheerde identiteit op een Azure VM toevoegen en verwijderen

Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="prerequisites"></a>Vereisten

- Zie [Wat zijn beheerde identiteiten voor Azure-resources?](overview.md) als u niet bekend met beheerde identiteiten voor Azure-resources. Zie [Beheerde identiteitstypen](overview.md#managed-identity-types) voor meer informatie over door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteitstypen.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u de door het systeem toegewezen beheerde identiteit op een Azure VM in- en uitschakelt met behulp van Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een Azure-VM

Als u een Azure-VM wilt maken met de door het systeem toegewezen beheerde identiteit, moet uw account beschikken over de roltoewijzing [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Maak met [az group create](/cli/azure/group/#az-group-create) een [resourcegroep](../../azure-resource-manager/management/overview.md#terminology) voor insluiting en implementatie van uw VM en de bijbehorende bronnen. U kunt deze stap overslaan als u al een resourcegroep hebt die u in plaats daarvan wilt gebruiken:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. Maak een VM met [az vm create](/cli/azure/vm/#az-vm-create). In het volgende voorbeeld wordt een VM met de naam *myVM* gemaakt met een door het systeem toegewezen beheerde identiteit, zoals is aangevraagd met de parameter `--assign-identity`. Met de parameters `--admin-username` en `--admin-password` worden de naam van de gebruiker met beheerdersrechten en het wachtwoord van het account voor aanmelding bij de virtuele machine opgegeven. Werk deze waarden bij met waarden die geschikt zijn voor uw omgeving: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen op een bestaande virtuele Azure-machine

Als u de door het systeem toegewezen beheerde identiteit op een VM wilt inschakelen, moet uw account beschikken over de roltoewijzing [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login](/cli/azure/reference-index#az-login). Gebruik een account die is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat.

   ```azurecli-interactive
   az login
   ```

2. Gebruik [az vm identity assign](/cli/azure/vm/identity/) met de opdracht `identity assign` om de door het systeem toegewezen identiteit in te schakelen voor een bestaande virtuele machine:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Door het systeem toegewezen identiteit van een Azure VM uitschakelen

Als u de door het systeem toegewezen beheerde identiteit op een VM wilt uitschakelen, moet uw account beschikken over de roltoewijzing [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

Gebruik de volgende opdracht als u een virtuele machine hebt die geen door het systeem toegewezen identiteit meer nodig heeft, maar die nog steeds door de gebruiker toegewezen identiteiten nodig heeft:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Gebruik de volgende opdracht als u een virtuele machine hebt die geen door het systeem toegewezen identiteit meer nodig heeft en geen door de gebruiker toegewezen identiteiten heeft:

> [!NOTE]
> De waarde `none` is hoofdlettergevoelig. Deze moet uit kleine letters bestaan. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u een door een gebruiker toegewezen beheerde identiteit kunt toevoegen aan/verwijderen van een virtuele Azure-machine met behulp van Azure CLI. Als u uw door de gebruiker toegewezen beheerde identiteit maakt in een andere RG dan uw virtuele machine. U moet de URL van uw beheerde identiteit gebruiken om deze aan uw virtuele machine toe te wijzen.
i.e. --identities "/subscriptions/<SUBID>/resourcegroups/<RESROURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER_ASSIGNED_ID_NAME>"

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Door de gebruiker toegewezen beheerde identiteit inschakelen tijdens het maken van een Azure VM

Als u een door een gebruiker toegewezen identiteit wilt toewijzen aan een VM tijdens het maken ervan, moet uw account beschikken over de roltoewijzingen [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) en [Operator beheerde identiteit](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. U kunt deze stap overslaan als u al een resourcegroep hebt die u wilt gebruiken. Maak met [az group create](/cli/azure/group/#az-group-create) een [resourcegroep](~/articles/azure-resource-manager/management/overview.md#terminology) voor insluiting en implementatie van uw door de gebruiker beheerde identiteit. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<LOCATION>` door uw eigen waarden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Maak een door de gebruiker toegewezen beheerde identiteit met [az identity create](/cli/azure/identity#az-identity-create).  De parameter `-g` geeft de resourcegroep aan waarin de door de gebruiker toegewezen beheerde identiteit wordt gemaakt en de parameter `-n` geeft de naam ervan aan.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   Het antwoord bevat details voor de door de gebruiker toegewezen beheerde identiteit die is gemaakt, vergelijkbaar met de volgende. De waarde van de resource-id die is toegewezen aan de door de gebruiker toegewezen beheerde identiteit, wordt in de volgende stap gebruikt.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
       "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
       "location": "westcentralus",
       "name": "<USER ASSIGNED IDENTITY NAME>",
       "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
       "resourceGroup": "<RESOURCE GROUP>",
       "tags": {},
       "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
       "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Maak een VM met [az vm create](/cli/azure/vm/#az-vm-create). In het volgende voorbeeld wordt een VM gemaakt die is gekoppeld aan de nieuwe door de gebruiker toegewezen identiteit, zoals aangegeven door de parameter `--assign-identity`. Vervang de parameterwaarden `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` en `<USER ASSIGNED IDENTITY NAME>` door uw eigen waarden. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande VM van Azure

Als u een door een gebruiker toegewezen identiteit wilt toewijzen aan een VM, moet uw account beschikken over de roltoewijzingen [Inzender van de virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) en [Operator van de beheerde identiteit](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Maak een door de gebruiker toegewezen identiteit met [az identity create](/cli/azure/identity#az-identity-create).  De parameter `-g` geeft de resourcegroep aan waarin de door de gebruiker toegewezen identiteit wordt gemaakt en de parameter `-n` geeft de naam ervan aan. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<USER ASSIGNED IDENTITY NAME>` door uw eigen waarden:

    > [!IMPORTANT]
    > Het is momenteel niet mogelijk om door de gebruiker toegewezen beheerde identiteiten te maken met speciale tekens (zoals een onderstrepingsteken) in de naam. Gebruik hiervoor alfanumerieke tekens. Controleer later op updates.  Zie [Veelgestelde vragen en bekende problemen](known-issues.md) voor meer informatie

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Het antwoord bevat details voor de door de gebruiker toegewezen beheerde identiteit die is gemaakt, vergelijkbaar met de volgende. 

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Wijs de door de gebruiker toegewezen identiteit toe aan de VM met [az vm identity assign](/cli/azure/vm). Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VM NAME>` door uw eigen waarden. De `<USER ASSIGNED IDENTITY NAME>` is de resource-eigenschap `name` van de door de gebruiker toegewezen beheerde identiteit, zoals deze in de vorige stap is gemaakt. Als u uw door de gebruiker toegewezen beheerde identiteit maakt in een andere RG dan uw virtuele machine. U moet de URL van uw beheerde identiteit gebruiken.

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>De door de gebruiker toegewezen beheerde identiteit van een Azure VM verwijderen

Als u de door de gebruiker toegewezen identiteit van een VM wilt verwijderen, moet uw account beschikken over de roltoewijzing [Inzender voor virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor). 

Als dit de enige door de gebruiker toegewezen beheerde identiteit is die is toegewezen aan de virtuele machine, wordt `UserAssigned` verwijderd uit de waarde voor het identiteitstype.  Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VM NAME>` door uw eigen waarden. De `<USER ASSIGNED IDENTITY>` wordt de eigenschap `name` van de door de gebruiker toegewezen identiteit, die u kunt vinden in de sectie identiteit van de virtuele machine met behulp van `az vm identity show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Gebruik de volgende opdracht als uw virtuele machine geen door het systeem toegewezen beheerde identiteit heeft en u alle door de gebruiker toegewezen identiteiten wilt verwijderen:

> [!NOTE]
> De waarde `none` is hoofdlettergevoelig. Deze moet uit kleine letters bestaan.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Als uw virtuele machine zowel door het systeem toegewezen als door de gebruiker toegewezen identiteiten bevat, kunt u alle door de gebruiker toegewezen identiteiten verwijderen door over te schakelen naar het gebruik van alleen door het systeem toegewezen identiteiten. Gebruik de volgende opdracht:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Volgende stappen
- [Overzicht van beheerde identiteiten voor Azure-resources](overview.md)
- Zie voor de volledige quickstarts voor het maken van virtuele Azure-machines: 
  - [Een virtuele Windows-machine maken met CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Een virtuele Linux-machine maken met CLI](../../virtual-machines/linux/quick-create-cli.md)
