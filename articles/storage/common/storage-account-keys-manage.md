---
title: Toegangssleutels voor accounts beheren
titleSuffix: Azure Storage
description: Meer informatie over het weergeven, beheren en roteren van toegangssleutels voor uw opslagaccount.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/24/2020
ms.author: tamram
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 82d272f22295a5b68d1e8de3fb5a70c45d4c14a3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791207"
---
# <a name="manage-storage-account-access-keys"></a>Toegangssleutels voor opslagaccounts beheren

Wanneer u een opslagaccount maakt, genereert Azure twee toegangssleutels voor een 512-bits opslagaccount. Deze sleutels kunnen worden gebruikt om toegang tot gegevens in uw opslagaccount te autorisatie via gedeelde sleutelautorisatie.

Microsoft raadt u aan om Azure Key Vault te gebruiken voor het beheren van uw toegangssleutels, en dat u uw sleutels regelmatig roteert en opnieuw wilt laten regenereren. Met Azure Key Vault kunt u uw sleutels eenvoudig roteren zonder onderbreking van uw toepassingen. U kunt uw sleutels ook handmatig roteren.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>Toegangssleutels voor het account weergeven

U kunt de toegangssleutels voor uw account weergeven en kopiëren met Azure Portal, PowerShell of Azure CLI. De Azure Portal biedt ook een connection string voor uw opslagaccount dat u kunt kopiëren.

# <a name="portal"></a>[Portal](#tab/azure-portal)

U kunt de toegangssleutels of gegevens van uw opslagaccount connection string van de Azure Portal:

1. Navigeer naar uw opslagaccount in [Azure Portal](https://portal.azure.com).
1. Selecteer onder **Instellingen** de optie **Toegangssleutels**. De toegangssleutels van uw account worden weergegeven, evenals de volledige verbindingsreeks voor elke sleutel.
1. Zoek de **sleutelwaarde** onder **key1** en klik op de **knop Kopiëren** om de accountsleutel te kopiëren.
1. U kunt ook het volledige connection string. Zoek de waarde van de **Verbindingsreeks** onder **key1** en klik op de knop **Kopiëren** om de verbindingsreeks te kopiëren.

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="Schermopname die laat zien hoe u toegangssleutels in de Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u de toegangssleutels voor uw account wilt ophalen met PowerShell, roept u [de opdracht Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey) aan.

In het volgende voorbeeld wordt de eerste sleutel opgehaald. Gebruik in plaats van om de tweede sleutel `Value[1]` op te `Value[0]` halen. Vergeet niet om de waarden van de tijdelijke aanduiding tussen haakjes te vervangen door uw eigen waarden.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de toegangssleutels voor uw account met Azure CLI wilt noemen, roept u de [opdracht az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) aan, zoals wordt weergegeven in het volgende voorbeeld. Vergeet niet om de waarden van de tijdelijke aanduiding tussen vierkante haken te vervangen door uw eigen waarden. 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

U kunt een van de twee sleutels gebruiken om toegang te krijgen tot Azure Storage, maar in het algemeen is het een goed idee om de eerste sleutel te gebruiken en het gebruik van de tweede sleutel te reserveren voor wanneer u sleutels roteert.

Als u de toegangssleutels van een account wilt weergeven of lezen, moet de gebruiker een servicebeheerder zijn of moet aan een Azure-rol worden toegewezen die de **Microsoft.Storage/storageAccounts/listkeys/action** bevat. Sommige ingebouwde Azure-rollen die deze actie bevatten, zijn de rollen **Eigenaar,** **Inzender** en Servicerol Sleuteloperator voor **opslagaccounts.** Zie Klassieke abonnementsbeheerdersrollen, Azure-rollen en Azure AD-rollen voor meer informatie over de rol [Servicebeheerder.](../../role-based-access-control/rbac-and-directory-admin-roles.md) Zie de sectie Opslag in ingebouwde Azure-rollen  voor [Azure RBAC](../../role-based-access-control/built-in-roles.md#storage)voor gedetailleerde informatie over ingebouwde rollen voor Azure Storage.

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Gebruik Azure Key Vault om uw toegangssleutels te beheren

Microsoft raadt u aan om Azure Key Vault toegangssleutels te beheren en te roteren. Uw toepassing heeft veilig toegang tot uw sleutels in Key Vault, zodat u kunt voorkomen dat ze worden opgeslagen met uw toepassingscode. Zie de volgende artikelen voor Key Vault informatie over het gebruik van Key Vault voor sleutelbeheer:

- [Opslagaccountsleutels beheren met Azure Key Vault en PowerShell](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Sleutels voor opslagaccounts beheren met Azure Key Vault en de Azure CLI](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Toegangssleutels handmatig roteren

Microsoft raadt u aan uw toegangssleutels periodiek te roteren om uw opslagaccount veilig te houden. Gebruik, indien mogelijk, Azure Key Vault om uw toegangssleutels te beheren. Als u geen gebruik Key Vault, moet u uw sleutels handmatig roteren.

Er worden twee toegangssleutels toegewezen, zodat u uw sleutels kunt roteren. Als u twee sleutels hebt, zorgt u ervoor dat uw toepassing tijdens Azure Storage toegangssleutels behoudt.

> [!WARNING]
> Het opnieuw maken van uw toegangssleutels kan van invloed zijn op alle toepassingen of Azure-services die afhankelijk zijn van de sleutel van het opslagaccount. Clients die gebruikmaken van de accountsleutel voor toegang tot het opslagaccount, moeten worden bijgewerkt om de nieuwe sleutel te kunnen gebruiken, waaronder mediaservices, cloud-, desktop- en mobiele toepassingen en grafische gebruikersinterfacetoepassingen voor Azure Storage, [zoals Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

# <a name="portal"></a>[Portal](#tab/azure-portal)

De toegangssleutels voor uw opslagaccount in de Azure Portal:

1. Werk de verbindingsreeksen in uw toepassingscode bij om te verwijzen naar de secundaire toegangssleutel voor het opslagaccount.
1. Navigeer naar uw opslagaccount in [Azure Portal](https://portal.azure.com).
1. Selecteer onder **Instellingen** de optie **Toegangssleutels**.
1. Als u de primaire toegangssleutel voor uw opslagaccount opnieuw wilt maken, selecteert u de **knop Opnieuw** maken naast de primaire toegangssleutel.
1. Werk de verbindingsreeksen in uw code bij, zodat deze verwijzen naar de nieuwe primaire toegangssleutel.
1. Genereer de secundaire toegangssleutel op dezelfde manier opnieuw.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Toegangssleutels voor uw opslagaccount roteren met PowerShell:

1. Werk de verbindingsreeksen in uw toepassingscode bij om te verwijzen naar de secundaire toegangssleutel voor het opslagaccount.
1. Roep de [opdracht New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) aan om de primaire toegangssleutel opnieuw te maken, zoals wordt weergegeven in het volgende voorbeeld:

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. Werk de verbindingsreeksen in uw code bij, zodat deze verwijzen naar de nieuwe primaire toegangssleutel.
1. Genereer de secundaire toegangssleutel op dezelfde manier opnieuw. Als u de secundaire sleutel opnieuw wilt maken, gebruikt `key2` u als sleutelnaam in plaats van `key1` .

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Toegangssleutels voor uw opslagaccount roteren met Azure CLI:

1. Werk de verbindingsreeksen in uw toepassingscode bij om te verwijzen naar de secundaire toegangssleutel voor het opslagaccount.
1. Roep de [opdracht az storage account keys renew](/cli/azure/storage/account/keys#az_storage_account_keys_renew) aan om de primaire toegangssleutel opnieuw te maken, zoals wordt weergegeven in het volgende voorbeeld:

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. Werk de verbindingsreeksen in uw code bij, zodat deze verwijzen naar de nieuwe primaire toegangssleutel.
1. Genereer de secundaire toegangssleutel op dezelfde manier opnieuw. Als u de secundaire sleutel opnieuw wilt maken, gebruikt `key2` u als sleutelnaam in plaats van `key1` .

---

> [!NOTE]
> Microsoft raadt u aan slechts één van de sleutels in al uw toepassingen tegelijk te gebruiken. Als u op sommige plaatsen sleutel 1 en sleutel 2 op andere plaatsen gebruikt, kunt u uw sleutels niet roteren zonder dat een toepassing de toegang verliest.

Als u de toegangssleutels van een account wilt roteren, moet de gebruiker een servicebeheerder zijn of moet aan een Azure-rol worden toegewezen die de **Microsoft.Storage/storageAccounts/regeneratekey/action** bevat. Sommige ingebouwde Azure-rollen die deze actie bevatten, zijn de servicerollen **Eigenaar,** **Inzender** en Sleuteloperator **voor opslagaccounts.** Zie Klassieke abonnementsbeheerdersrollen, Azure-rollen en Azure AD-rollen voor meer informatie over de rol [Servicebeheerder.](../../role-based-access-control/rbac-and-directory-admin-roles.md) Zie de sectie Opslag in ingebouwde Azure-rollen  voor Azure RBAC voor gedetailleerde informatie over [ingebouwde Azure-rollen](../../role-based-access-control/built-in-roles.md#storage)voor Azure Storage.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Storage-account](storage-account-overview.md)
- [Een opslagaccount maken](storage-account-create.md)
