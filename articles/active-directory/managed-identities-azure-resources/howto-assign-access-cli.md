---
title: Toegang tot een beheerde identiteit toewijzen aan een resource met behulp van Azure CLI - Azure AD
description: Stapsgewijs instructies voor het toewijzen van een beheerde identiteit aan één resource, toegang tot een andere resource met behulp van Azure CLI.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1e1fa22cc36df00b098274002b6bd444be4140ff
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783283"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Toegang tot een beheerde identiteit toewijzen aan een resource met behulp van Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zodra u een Azure-resource met een beheerde identiteit hebt geconfigureerd, kunt u de beheerde identiteit toegang geven tot een andere resource, net als elke beveiligingsprincipaal. In dit voorbeeld ziet u hoe u de beheerde identiteit van een virtuele Azure-machine of virtuele-machineschaalset toegang geeft tot een Azure-opslagaccount met behulp van Azure CLI.

Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="prerequisites"></a>Vereisten

- Zie [Wat zijn beheerde identiteiten voor Azure-resources?](overview.md) als u niet bekend met beheerde identiteiten voor Azure-resources. Zie [Beheerde identiteitstypen](overview.md#managed-identity-types) voor meer informatie over door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteitstypen.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Azure RBAC gebruiken om toegang tot een beheerde identiteit toe te wijzen aan een andere resource

Nadat u een beheerde identiteit hebt ingeschakeld voor een Azure-resource, zoals een virtuele [Azure-machine](qs-configure-cli-windows-vm.md) of [een virtuele-machineschaalset van Azure:](qs-configure-cli-windows-vmss.md) 

1. In dit voorbeeld geven we een virtuele Azure-machine toegang tot een opslagaccount. Eerst gebruiken we [az resource list om](/cli/azure/resource/#az_resource_list) de service-principal op te halen voor de virtuele machine met de naam myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Voor een virtuele-machineschaalset van Azure is de opdracht hetzelfde, behalve hier, krijgt u de service-principal voor de virtuele-machineschaalset met de naam 'DevTestVMSS':
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. Zodra u de service-principal-id hebt, gebruikt u [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) om de virtuele machine of virtuele-machineschaalset 'Lezer' toegang te geven tot een opslagaccount met de naam 'myStorageAcct':

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-resources](overview.md)
- Zie Beheerde identiteiten configureren voor Azure-resources op een virtuele Azure-machine met behulp van Azure CLI als u beheerde identiteiten wilt [inschakelen op een virtuele Azure-machine.](qs-configure-cli-windows-vm.md)
- Zie Beheerde identiteiten configureren voor Azure-resources op een [virtuele-machineschaalset](qs-configure-cli-windows-vmss.md)met behulp van Azure CLI als u beheerde identiteiten wilt inschakelen op een virtuele-machineschaalset van Azure.
