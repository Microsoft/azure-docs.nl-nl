---
title: Uw Tenant-ID zoeken-Azure Active Directory
description: Instructies voor het zoeken en Azure Active Directory van de Tenant-ID naar een bestaand Azure-abonnement.
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
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32526a31a9d52fbfac57f1d384b25e0939b5e297
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172870"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>Uw Azure Active Directory Tenant-ID zoeken

Azure-abonnementen hebben een vertrouwens relatie met Azure Active Directory (Azure AD). Azure AD wordt vertrouwd voor het verifiëren van gebruikers, services en apparaten voor het abonnement. Aan elk abonnement is een Tenant-ID gekoppeld en er zijn een aantal manieren om de Tenant-ID voor uw abonnement te vinden.

## <a name="find-tenant-id-through-the-azure-portal"></a>Tenant-ID zoeken via de Azure Portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
 
1. Selecteer **Azure Active Directory**.

1. Selecteer **Eigenschappen**.

1. Schuif vervolgens omlaag naar het veld **Tenant-id** . Uw Tenant-ID wordt in het vak weer.

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory-Properties-Tenant-ID-veld Tenant-ID":::

## <a name="find-tenant-id-with-powershell"></a>Tenant-ID zoeken met Power shell

U kunt de Tenant ook programmatisch vinden. Als u de Tenant-ID met Azure PowerShell wilt zoeken, gebruikt u de cmdlet `Get-AzTenant` .

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
Zie voor meer informatie deze Azure PowerShell-cmdlet-verwijzing voor [Get-AzTenant](/powershell/module/az.accounts/get-aztenant).


## <a name="find-tenant-id-with-cli"></a>Tenant-ID zoeken met CLI
Als u een opdracht regel interface wilt gebruiken om de Tenant-ID te vinden, kunt u dit doen met [Azure cli](/cli/azure/install-azure-cli) of [Microsoft 365 cli](https://pnp.github.io/cli-microsoft365/). 

Gebruik voor Azure CLI een van de opdrachten **AZ login**, **AZ account list** of **AZ account Tenant List** , zoals wordt weer gegeven in het volgende voor beeld. Let op de eigenschap **tenantId** voor elk van uw abonnementen in de uitvoer van elke opdracht.

```azurecli-interactive
az login
az account list
az account tenant list
```

Zie voor meer informatie [AZ login](/cli/azure/reference-index#az_login) opdracht verwijzing, [AZ account](/cli/azure/ext/account/account) Command Reference of [AZ account Tenant](/cli/azure/ext/account/account/tenant) opdracht verwijzing.


Voor Microsoft 365 CLI gebruikt u de **Tenant-id** van de cmdlet, zoals weer gegeven in het volgende voor beeld:
 
```cli
m365 tenant id get
```

Zie voor meer informatie de Microsoft 365 [-Tenant-id](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/) opdracht verwijzing ophalen.


## <a name="next-steps"></a>Volgende stappen

- Als u een nieuwe Azure AD-Tenant wilt maken, raadpleegt u [Quick Start: een nieuwe Tenant maken in azure Active Directory](active-directory-access-create-new-tenant.md).

- Zie [een Azure-abonnement koppelen aan of toevoegen aan uw Azure Active Directory-Tenant](active-directory-how-subscriptions-associated-directory.md)voor meer informatie over het koppelen of toevoegen van een abonnement aan een Tenant.

- Zie [de gebruikers object-id zoeken](/partner-center/find-ids-and-domain-names#find-the-user-object-id)voor meer informatie over het vinden van de object-id.