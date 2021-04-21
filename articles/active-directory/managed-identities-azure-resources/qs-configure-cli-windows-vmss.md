---
title: Beheerde identiteiten configureren op een virtuele-machineschaalset - Azure CLI - Azure AD
description: Stapsgewijze instructies voor het configureren van door het systeem en de gebruiker toegewezen beheerde identiteiten op een Azure virtuele-machineschaalset met behulp van Azure CLI.
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
ms.openlocfilehash: 0e6e7e1724247c0e6d2b9db2fdf6980e8ef553c7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788183"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Beheerde identiteiten voor Azure-resources configureren op een virtuele-machineschaalset met Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben. 

In dit artikel leert u hoe u de volgende beheerde identiteiten uitvoert voor bewerkingen van Azure-resources op een virtuele-machineschaalset van Azure met behulp van de Azure CLI:
- De door het systeem toegewezen beheerde identiteit inschakelen en uitschakelen op een virtuele-machineschaalset van Azure
- Een door de gebruiker toegewezen beheerde identiteit toevoegen aan en verwijderen uit een virtuele-machineschaalset van Azure

Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="prerequisites"></a>Vereisten

- Zie [Wat zijn beheerde identiteiten voor Azure-resources?](overview.md) als u niet bekend met beheerde identiteiten voor Azure-resources. Zie [Beheerde identiteitstypen](overview.md#managed-identity-types) voor meer informatie over door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteitstypen.

- Voor het uitvoeren van de beheerbewerkingen in dit artikel zijn voor uw account de volgende toewijzingen van op rollen gebaseerd toegangsbeheer van Azure nodig:

  - [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) voor het maken van een virtuele-machineschaalset en het inschakelen en verwijderen van een door het systeem en/of de gebruiker toegewezen beheerde identiteit op/uit een virtuele-machineschaalset.

  - De rol van [inzender voor beheerde identiteit](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) voor het maken van een door de gebruiker toegewezen beheerde identiteit.

  - De rol van [operator voor beheerde identiteit](../../role-based-access-control/built-in-roles.md#managed-identity-operator) voor het toewijzen/verwijderen van een door de gebruiker toegewezen beheerde identiteit aan/uit een virtuele-machine schaalset.

  > [!NOTE]
  > Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-map.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u de door het systeem toegewezen beheerde identiteit voor een virtuele-machineschaalset van Azure in- en uitschakelt met behulp van Azure Portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een virtuele-machineschaalset van Azure

Doe het volgende om een virtuele-machineschaalset te maken met de door het systeem toegewezen beheerde identiteit ingeschakeld:

1. Maak met [az group create](/cli/azure/group/#az_group_create) een [resourcegroep](../../azure-resource-manager/management/overview.md#terminology) voor de insluiting en implementatie van uw virtuele-machineschaalset en de bijbehorende resources. U kunt deze stap overslaan als u al een resourcegroep hebt die u in plaats daarvan wilt gebruiken:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. [Maak](/cli/azure/vmss/#az_vmss_create) een virtuele-machineschaalset. In het volgende voorbeeld wordt een virtuele-machineschaalset met de naam *myVMSS* gemaakt met een door het systeem toegewezen beheerde identiteit, zoals is aangevraagd met de parameter `--assign-identity`. Met de parameters `--admin-username` en `--admin-password` worden de naam van de gebruiker met beheerdersrechten en het wachtwoord van het account voor aanmelding bij de virtuele machine opgegeven. Werk deze waarden bij met waarden die geschikt zijn voor uw omgeving: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Een door het systeem toegewezen beheerde identiteit inschakelen op een bestaande virtuele-machineschaalset van Azure

Als u de door het systeem toegewezen beheerde identiteit moet [Inschakelen](/cli/azure/vmss/identity/#az_vmss_identity_assign) op een bestaande virtuele-machineschaalset van Azure:

```azurecli-interactive
az vmss identity assign -g myResourceGroup -n myVMSS
```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Een door het systeem toegewezen beheerde identiteit uitschakelen vanuit een virtuele-machineschaalset van Azure

Gebruik de volgende opdracht als u een virtuele-machineschaalset hebt die geen door het systeem toegewezen beheerde identiteit meer nodig heeft, maar nog wel de door de gebruiker toegewezen beheerde identiteiten nodig heeft:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Gebruik de volgende opdracht als u een virtuele machine hebt die geen door het systeem toegewezen beheerde identiteit meer nodig heeft en er geen door de gebruiker toegewezen beheerde identiteiten zijn:

> [!NOTE]
> De waarde `none` is hoofdlettergevoelig. Deze moet uit kleine letters bestaan. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u een door een gebruiker toegewezen beheerde identiteit kunt inschakelen en verwijderen met behulp van Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Door de gebruiker toegewezen beheerde identiteit inschakelen tijdens het maken van een virtuele-machineschaalset

In deze sectie wordt uitgelegd hoe u een schaalset voor virtuele machines kunt maken en een door de gebruiker toegewezen beheerde identiteit kunt toewijzen aan de virtuele-machineschaalset. Als u al een virtuele-machineschaalset hebt die u wilt gebruiken, kunt u deze sectie overslaan en doorgaan met de volgende.

1. U kunt deze stap overslaan als u al een resourcegroep hebt die u wilt gebruiken. Maak met [az group create](/cli/azure/group/#az_group_create) een [resourcegroep](~/articles/azure-resource-manager/management/overview.md#terminology) voor insluiting en implementatie van uw door de gebruiker beheerde identiteit. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<LOCATION>` door uw eigen waarden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Maak een door de gebruiker toegewezen beheerde identiteit met [az identity create](/cli/azure/identity#az_identity_create).  De parameter `-g` geeft de resourcegroep aan waarin de door de gebruiker toegewezen beheerde identiteit wordt gemaakt en de parameter `-n` geeft de naam ervan aan. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<USER ASSIGNED IDENTITY NAME>` door uw eigen waarden:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Het antwoord bevat details voor de door de gebruiker toegewezen beheerde identiteit die is gemaakt, vergelijkbaar met de volgende. De resourcewaarde `id` die is toegewezen aan de door de gebruiker toegewezen beheerde identiteit, wordt in de volgende stap gebruikt.

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

3. [Maak](/cli/azure/vmss/#az_vmss_create) een virtuele-machineschaalset. In het volgende voorbeeld wordt een virtuele-machineschaalset gemaakt met de nieuwe door de gebruiker toegewezen beheerde identiteit, zoals aangegeven door de parameter `--assign-identity`. Vervang de parameterwaarden `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` en `<USER ASSIGNED IDENTITY>` door uw eigen waarden. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Een door een gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande virtuele-machineschaalset

1. Maak een door de gebruiker toegewezen beheerde identiteit met [az identity create](/cli/azure/identity#az_identity_create).  De parameter `-g` geeft de resourcegroep aan waarin de door de gebruiker toegewezen beheerde identiteit wordt gemaakt en de parameter `-n` geeft de naam ervan aan. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<USER ASSIGNED IDENTITY NAME>` door uw eigen waarden:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Het antwoord bevat details voor de door de gebruiker toegewezen beheerde identiteit die is gemaakt, vergelijkbaar met de volgende.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. U kunt de door de gebruiker toegewezen beheerde identiteit [toewijzen](/cli/azure/vmss/identity) aan uw virtuele-machineschaalset. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VIRTUAL MACHINE SCALE SET NAME>` door uw eigen waarden. `<USER ASSIGNED IDENTITY>` is de resource-eigenschap `name` van de door de gebruiker toegewezen identiteit, zoals deze in de vorige stap is gemaakt:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit uit een virtuele-machineschaalset van Azure verwijderen

Als u een door de gebruiker toegewezen beheerde identiteit uit een virtuele-machineschaalset wilt [verwijderen](/cli/azure/vmss/identity#az_vmss_identity_remove), gebruikt u `az vmss identity remove`. Als dit de enige door de gebruiker toegewezen beheerde identiteit is die is toegewezen aan de virtuele-machineschaalset, wordt `UserAssigned` verwijderd uit de waarde voor het identiteitstype.  Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VIRTUAL MACHINE SCALE SET NAME>` door uw eigen waarden. De `<USER ASSIGNED IDENTITY>` wordt de eigenschap `name` van de door de gebruiker toegewezen beheerde identiteit, die u kunt vinden in de identiteitssectie van de virtuele-machineschaalset met behulp van `az vmss identity show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Gebruik de volgende opdracht als uw virtuele-machineschaalset geen door het systeem toegewezen beheerde identiteit heeft en u alle door de gebruiker toegewezen beheerde identiteiten wilt verwijderen:

> [!NOTE]
> De waarde `none` is hoofdlettergevoelig. Deze moet uit kleine letters bestaan.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Als uw virtuele-machineschaalset zowel door het systeem toegewezen als door de gebruiker toegewezen beheerde identiteiten bevat, kunt u alle door de gebruiker toegewezen identiteiten verwijderen door over te schakelen naar het gebruik van alleen de door het systeem toegewezen beheerde identiteit. Gebruik de volgende opdracht:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-resources](overview.md)
- Voor de volledige quickstart voor het maken van Azure virtuele-machineschaalsets, zie [Een virtuele-machineschaalset maken met CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)
