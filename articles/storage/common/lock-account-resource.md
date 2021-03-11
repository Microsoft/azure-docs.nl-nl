---
title: Een Azure Resource Manager Lock Toep assen op een opslag account
titleSuffix: Azure Storage
description: Meer informatie over het Toep assen van een Azure Resource Manager vergrendeling op een opslag account.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9d80c0b8d4d913322c47d1ad278d6dbc033d2409
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620062"
---
# <a name="apply-an-azure-resource-manager-lock-to-a-storage-account"></a>Een Azure Resource Manager Lock Toep assen op een opslag account

Micro soft raadt u aan al uw opslag accounts te vergren delen met een Azure Resource Manager-vergren deling om te voor komen dat het opslag account onbedoeld of kwaad aardig wordt verwijderd. Er zijn twee soorten Azure Resource Manager resource vergrendelingen:

- Een **CannotDelete** -vergren deling voor komt dat gebruikers een opslag account verwijderen, maar de configuratie wel kunnen lezen en wijzigen.
- Een **alleen-lezen** vergrendeling voor komt dat gebruikers een opslag account verwijderen of de configuratie ervan wijzigen, maar wel de configuratie kunnen lezen.

Zie [resources vergren delen om wijzigingen te voor komen](../../azure-resource-manager/management/lock-resources.md)voor meer informatie over Azure Resource Manager sloten.

> [!IMPORTANT]
> Het vergren delen van een opslag account beveiligt niet dat de gegevens in dat account worden bijgewerkt of verwijderd.

## <a name="configure-an-azure-resource-manager-lock"></a>Een Azure Resource Manager vergrendeling configureren

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Voer de volgende stappen uit om een vergren deling van een opslag account met de Azure Portal te configureren:

1. Ga in Azure Portal naar uw opslagaccount.
1. Selecteer in de sectie **instellingen** de optie **vergren** delen.
1. Selecteer **Toevoegen**.
1. Geef een naam op voor de resource vergrendeling en geef het type vergren deling op. Voeg indien gewenst een opmerking over de vergren deling toe.

    :::image type="content" source="media/lock-account-resource/lock-storage-account.png" alt-text="Scherm afbeelding die laat zien hoe u een opslag account vergrendelt met een CannotDelete-vergren deling":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u een vergren deling wilt configureren voor een opslag account met Power shell, moet u eerst controleren of u de [AZ Power shell-module](https://www.powershellgallery.com/packages/Az)hebt geïnstalleerd. Vervolgens roept u de opdracht [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) aan en geeft u het type vergren deling op dat u wilt maken, zoals wordt weer gegeven in het volgende voor beeld:

```azurepowershell
New-AzResourceLock -LockLevel CanNotDelete `
    -LockName <lock> `
    -ResourceName <storage-account> `
    -ResourceType Microsoft.Storage/storageAccounts `
    -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een vergren deling wilt configureren voor een opslag account met Azure CLI, roept u de opdracht [AZ Lock Create](/cli/azure/lock#az_lock_create) aan en geeft u het type vergren deling op dat u wilt maken, zoals wordt weer gegeven in het volgende voor beeld:

```azurecli
az lock create \
    --name <lock> \
    --resource-group <resource-group> \
    --resource <storage-account> \
    --lock-type CanNotDelete \
    --resource-type Microsoft.Storage/storageAccounts
```

---

## <a name="authorizing-data-operations-when-a-readonly-lock-is-in-effect"></a>Het autoriseren van gegevens bewerkingen wanneer een alleen-lezen vergrendeling actief is

Wanneer een **alleen-lezen** vergrendeling wordt toegepast op een opslag account, wordt de bewerking [lijst sleutels](/rest/api/storagerp/storageaccounts/listkeys) geblokkeerd voor dat opslag account. De bewerking **lijst sleutels** is een HTTPS post-bewerking en alle post-bewerkingen worden voor komen wanneer een **alleen-lezen** vergrendeling voor het account is geconfigureerd. De bewerking **lijst sleutels** retourneert de toegangs sleutels voor het account, die vervolgens kunnen worden gebruikt voor het lezen en schrijven van gegevens in het opslag account.

Als een client in het bezit is van de toegangs sleutels voor het account op het moment dat de vergren deling wordt toegepast op het opslag account, kan die client de sleutels blijven gebruiken om toegang te krijgen tot gegevens. Clients die geen toegang tot de sleutels hebben, moeten echter Azure Active Directory (Azure AD)-referenties gebruiken om toegang te krijgen tot BLOB-of wachtrij gegevens in het opslag account.

Gebruikers van de Azure Portal kunnen worden beïnvloed wanneer een **alleen-lezen** vergrendeling wordt toegepast, als ze eerder toegang hebben gekregen tot BLOB of wachtrij gegevens in de portal met de toegangs sleutels voor het account. Nadat de vergren deling is toegepast, moeten gebruikers van de portal Azure AD-referenties gebruiken om toegang te krijgen tot BLOB-of wachtrij gegevens in de portal. Hiervoor moet aan een gebruiker ten minste twee RBAC-rollen zijn toegewezen: de rol van Azure Resource Manager lezer mini maal en een van de Azure Storage Data Access-rollen. Zie een van de volgende artikelen voor meer informatie:

- [Kies hoe u de toegang tot BLOB-gegevens in de Azure Portal wilt autoriseren](../blobs/authorize-data-operations-portal.md)
- [Kies hoe u de toegang tot de wachtrij gegevens in de Azure Portal wilt autoriseren](../queues/authorize-data-operations-portal.md)

Gegevens in Azure Files of de Table service kunnen ontoegankelijk worden voor clients die deze eerder met de account sleutels hebben geopend. Als best practice, als u een **alleen-lezen** vergrendeling op een opslag account moet Toep assen, kunt u de Azure Files en Table service werk belastingen verplaatsen naar een opslag account dat niet is vergrendeld met een **alleen-lezen** vergrendeling.

## <a name="next-steps"></a>Volgende stappen

[Resources vergren delen om wijzigingen te voor komen](../../azure-resource-manager/management/lock-resources.md)
