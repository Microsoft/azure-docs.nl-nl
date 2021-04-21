---
title: Uw tenant-id vinden - Azure Active Directory
description: Instructies voor het vinden en Azure Active Directory tenant-id in een bestaand Azure-abonnement.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: cba823775849fdad8407c7bb697a53761e8ccbcd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764347"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>Uw tenant-id Azure Active Directory vinden

Azure-abonnementen hebben een vertrouwensrelatie met Azure Active Directory (Azure AD). Azure AD wordt vertrouwd om gebruikers, services en apparaten voor het abonnement te verifiëren. Aan elk abonnement is een tenant-id gekoppeld en er zijn een aantal manieren waarop u de tenant-id voor uw abonnement kunt vinden.

## <a name="find-tenant-id-through-the-azure-portal"></a>Tenant-id zoeken via de Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
 
1. Selecteer **Azure Active Directory**.

1. Selecteer **Eigenschappen**.

1. Schuif vervolgens omlaag naar het **veld Tenant-id.** Uw tenant-id staat in het vak.

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory - Eigenschappen - Tenant-id - Veld Tenant-id":::

## <a name="find-tenant-id-with-powershell"></a>Tenant-id zoeken met PowerShell

U kunt de tenant ook programmatisch vinden. Gebruik de cmdlet om Azure PowerShell tenant-id te `Get-AzTenant` vinden.

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
Zie voor meer informatie deze naslaginformatie Azure PowerShell cmdlet voor [Get-AzTenant](/powershell/module/az.accounts/get-aztenant).


## <a name="find-tenant-id-with-cli"></a>Tenant-id zoeken met CLI
Als u een opdrachtregelinterface wilt gebruiken om de tenant-id te vinden, kunt u dit doen met [Azure CLI](/cli/azure/install-azure-cli) of [Microsoft 365 CLI.](https://pnp.github.io/cli-microsoft365/) 

Gebruik voor Azure CLI een van de opdrachten **az login,** **az account list** of az account tenant **list,** zoals wordt weergegeven in het volgende voorbeeld. Let op **de eigenschap tenantId** voor elk van uw abonnementen in de uitvoer van elke opdracht.

```azurecli-interactive
az login
az account list
az account tenant list
```

Zie az [login](/cli/azure/reference-index#az_login) command reference, [az account](/cli/azure/ext/account/account) command reference of az account [tenant](/cli/azure/ext/account/account/tenant) command reference voor meer informatie.


Voor Microsoft 365 CLI gebruikt u de **tenant-id** van de cmdlet, zoals wordt weergegeven in het volgende voorbeeld:
 
```cli
m365 tenant id get
```

Zie de naslaginformatie over de [tenant Microsoft 365-id voor het verkrijgen](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/) van opdrachten voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

- Als u een nieuwe Azure AD-tenant wilt maken, gaat u [naar Quickstart: Een nieuwe tenant maken in Azure Active Directory](active-directory-access-create-new-tenant.md).

- Zie Een Azure-abonnement koppelen of toevoegen aan uw tenant voor meer informatie over het koppelen of toevoegen [van een Azure Active Directory tenant.](active-directory-how-subscriptions-associated-directory.md)

- Zie Find the user object ID (De object-id van de gebruiker zoeken) voor meer informatie over het vinden van de [object-id.](/partner-center/find-ids-and-domain-names#find-the-user-object-id)
