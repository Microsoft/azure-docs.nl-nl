---
title: Toegangssleutels voor accounts beheren
titleSuffix: Azure Storage
description: Meer informatie over het weer geven, beheren en draaien van de toegangs sleutels van uw opslag account.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/24/2020
ms.author: tamram
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e5ea94fea00771b64634d6c28a7879fabb195f09
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89069656"
---
# <a name="manage-storage-account-access-keys"></a>Toegangs sleutels voor opslag accounts beheren

Wanneer u een opslag account maakt, genereert Azure 2 512-bits toegangs sleutels voor opslag accounts. Deze sleutels kunnen worden gebruikt om toegang te verlenen tot gegevens in uw opslag account via de verificatie van de gedeelde sleutel.

Micro soft raadt u aan Azure Key Vault te gebruiken voor het beheren van uw toegangs sleutels en om uw sleutels regel matig te draaien en opnieuw te genereren. Met Azure Key Vault kunt u eenvoudig uw sleutels zonder onderbreking naar uw toepassingen draaien. U kunt uw sleutels ook hand matig draaien.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>Toegangs sleutels voor accounts weer geven

U kunt de toegangs sleutels van uw account weer geven en kopiëren met de Azure Portal, Power shell of Azure CLI. De Azure Portal biedt ook een connection string voor uw opslag account dat u kunt kopiëren.

# <a name="portal"></a>[Portal](#tab/azure-portal)

De toegangs sleutels of connection string van uw opslag account weer geven en kopiëren vanuit de Azure Portal:

1. Navigeer naar uw opslag account in de [Azure Portal](https://portal.azure.com).
1. Selecteer onder **Instellingen** de optie **Toegangssleutels**. De toegangssleutels van uw account worden weergegeven, evenals de volledige verbindingsreeks voor elke sleutel.
1. Zoek de **sleutel** waarde onder **key1** en klik op de knop **kopiëren** om de account sleutel te kopiëren.
1. U kunt ook de hele connection string kopiëren. Zoek de waarde van de **Verbindingsreeks** onder **key1** en klik op de knop **Kopiëren** om de verbindingsreeks te kopiëren.

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="Scherm afbeelding die laat zien hoe toegangs sleutels in de Azure Portal worden weer gegeven":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Roep de [Get-AzStorageAccountKey-](/powershell/module/az.Storage/Get-azStorageAccountKey) opdracht aan om de toegangs sleutels van uw account op te halen met Power shell.

In het volgende voor beeld wordt de eerste sleutel opgehaald. Als u de tweede sleutel wilt ophalen, gebruikt u `Value[1]` in plaats van `Value[0]` . Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de toegangs sleutels van uw account wilt weer geven met Azure CLI, roept u de opdracht [AZ Storage account Keys List](/cli/azure/storage/account/keys#az-storage-account-keys-list) aan, zoals wordt weer gegeven in het volgende voor beeld. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden. 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

U kunt een van de twee sleutels gebruiken om toegang te krijgen tot Azure Storage, maar in het algemeen is het een goed idee om de eerste sleutel te gebruiken en het gebruik van de tweede toets te reserveren wanneer u sleutels draait.

Als u de toegangs sleutels van een account wilt bekijken of lezen, moet de gebruiker een service beheerder zijn of moet er een Azure-rol worden toegewezen die de **micro soft. Storage/Storage accounts/listkeys ophalen/Action** bevat. Sommige ingebouwde rollen van Azure die deze actie bevatten, zijn de rol rollen van de operator **eigenaar**, **Inzender** en **opslag account** . Zie voor meer informatie over de rol van service beheerder [klassieke abonnements beheerders rollen, Azure-rollen en Azure AD-rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md). Zie de sectie **opslag** in [ingebouwde Azure-rollen voor Azure RBAC](../../role-based-access-control/built-in-roles.md#storage)voor meer informatie over ingebouwde rollen voor Azure Storage.

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Azure Key Vault gebruiken om uw toegangs sleutels te beheren

Micro soft raadt u aan Azure Key Vault te gebruiken om uw toegangs sleutels te beheren en te draaien. Uw toepassing kan veilig toegang krijgen tot uw sleutels in Key Vault, zodat u ze niet kunt opslaan met de code van uw toepassing. Raadpleeg de volgende artikelen voor meer informatie over het gebruik van Key Vault voor sleutel beheer:

- [Sleutels voor opslag accounts beheren met Azure Key Vault en Power shell](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Sleutels voor opslagaccounts beheren met Azure Key Vault en de Azure CLI](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Toegangs sleutels hand matig draaien

Micro soft raadt u aan uw toegangs sleutels regel matig te draaien om uw opslag account veilig te houden. Gebruik, indien mogelijk, Azure Key Vault om uw toegangs sleutels te beheren. Als u Key Vault niet gebruikt, moet u de sleutels hand matig draaien.

Er zijn twee toegangs sleutels toegewezen zodat u uw sleutels kunt draaien. Als u twee sleutels hebt, zorgt u ervoor dat uw toepassing gedurende het proces toegang tot Azure Storage houdt.

> [!WARNING]
> Het opnieuw genereren van de toegangs sleutels kan invloed hebben op alle toepassingen of Azure-Services die afhankelijk zijn van de sleutel van het opslag account. Clients die gebruikmaken van de account sleutel voor toegang tot het opslag account moeten worden bijgewerkt om de nieuwe sleutel te gebruiken, inclusief Media Services, Cloud, desktop-en mobiele toepassingen en Graphical User Interface toepassingen voor Azure Storage, zoals [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

# <a name="portal"></a>[Portal](#tab/azure-portal)

De toegangs sleutels van uw opslag account in de Azure Portal draaien:

1. Werk de verbindings reeksen in uw toepassings code bij om te verwijzen naar de secundaire toegangs sleutel voor het opslag account.
1. Navigeer naar uw opslag account in de [Azure Portal](https://portal.azure.com).
1. Selecteer onder **Instellingen** de optie **Toegangssleutels**.
1. Als u de primaire toegangs sleutel voor uw opslag account opnieuw wilt genereren, selecteert u de knop **opnieuw genereren** naast de primaire toegangs sleutel.
1. Werk de verbindingsreeksen in uw code bij, zodat deze verwijzen naar de nieuwe primaire toegangssleutel.
1. Genereer de secundaire toegangssleutel op dezelfde manier opnieuw.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

De toegangs sleutels van uw opslag account met Power shell draaien:

1. Werk de verbindings reeksen in uw toepassings code bij om te verwijzen naar de secundaire toegangs sleutel voor het opslag account.
1. Roep de opdracht [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) aan om de primaire toegangs sleutel opnieuw te genereren, zoals wordt weer gegeven in het volgende voor beeld:

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. Werk de verbindingsreeksen in uw code bij, zodat deze verwijzen naar de nieuwe primaire toegangssleutel.
1. Genereer de secundaire toegangssleutel op dezelfde manier opnieuw. Als u de secundaire sleutel opnieuw wilt genereren, gebruikt u `key2` als sleutel naam in plaats van `key1` .

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De toegangs sleutels van uw opslag account met Azure CLI draaien:

1. Werk de verbindings reeksen in uw toepassings code bij om te verwijzen naar de secundaire toegangs sleutel voor het opslag account.
1. Roep de opdracht [AZ Storage account Keys renew](/cli/azure/storage/account/keys#az-storage-account-keys-renew) uit om de primaire toegangs sleutel opnieuw te genereren, zoals wordt weer gegeven in het volgende voor beeld:

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. Werk de verbindingsreeksen in uw code bij, zodat deze verwijzen naar de nieuwe primaire toegangssleutel.
1. Genereer de secundaire toegangssleutel op dezelfde manier opnieuw. Als u de secundaire sleutel opnieuw wilt genereren, gebruikt u `key2` als sleutel naam in plaats van `key1` .

---

> [!NOTE]
> Micro soft raadt u aan om op hetzelfde moment slechts één van de sleutels in al uw toepassingen te gebruiken. Als u Key 1 op sommige locaties en sleutel 2 in andere gebruikt, kunt u de sleutels niet draaien zonder dat de toepassing de toegang verliest.

Als u de toegangs sleutels van een account wilt draaien, moet de gebruiker een service beheerder zijn of moet aan een Azure-rol zijn toegewezen die de **micro soft. Storage/Storage accounts/regeneratekey/Action** bevat. Sommige ingebouwde rollen van Azure die deze actie bevatten, zijn de rol rollen van de operator **eigenaar**, **Inzender** en **opslag account** . Zie voor meer informatie over de rol van service beheerder [klassieke abonnements beheerders rollen, Azure-rollen en Azure AD-rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md). Zie de sectie **opslag** in [ingebouwde Azure-rollen voor Azure RBAC](../../role-based-access-control/built-in-roles.md#storage)voor meer informatie over ingebouwde rollen van Azure voor Azure Storage.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Storage-account](storage-account-overview.md)
- [Een opslagaccount maken](storage-account-create.md)
