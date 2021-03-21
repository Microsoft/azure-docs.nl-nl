---
title: Een beheerde identiteits toegang toewijzen aan een resource met behulp van Azure CLI-Azure AD
description: Stapsgewijze instructies voor het toewijzen van een beheerde identiteit aan één resource, toegang tot een andere resource met behulp van Azure CLI.
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
ms.openlocfilehash: e3b06ce76ae77aa62b20b707a736e8e20e5f6c45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99090041"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Een beheerde identiteits toegang tot een resource toewijzen met behulp van Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zodra u een Azure-resource met een beheerde identiteit hebt geconfigureerd, kunt u de beheerde identiteit toegang geven tot een andere resource, net zoals elke beveiligingsprincipal. In dit voor beeld ziet u hoe u met Azure CLI een Azure-virtuele machine of beheerde identiteits toegang voor virtuele machines kunt geven aan een Azure-opslag account.

Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="prerequisites"></a>Vereisten

- Zie [Wat zijn beheerde identiteiten voor Azure-resources?](overview.md) als u niet bekend met beheerde identiteiten voor Azure-resources. Zie [Beheerde identiteitstypen](overview.md#managed-identity-types) voor meer informatie over door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteitstypen.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Azure RBAC gebruiken om een beheerde identiteits toegang toe te wijzen aan een andere resource

Nadat u de beheerde identiteit hebt ingeschakeld voor een Azure-resource, zoals een [virtuele machine van Azure](qs-configure-cli-windows-vm.md) of een [schaalset voor virtuele machines van Azure](qs-configure-cli-windows-vmss.md): 

1. In dit voor beeld geven we een virtuele Azure-machine toegang tot een opslag account. Eerst gebruiken we [AZ Resource List](/cli/azure/resource/#az-resource-list) om de Service-Principal op te halen voor de virtuele machine met de naam myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Voor een schaalset voor virtuele Azure-machines is de opdracht hetzelfde, maar u krijgt de service-principal voor de virtuele-machine schaalset ' DevTestVMSS ':
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. Wanneer u de Service-Principal-ID hebt, gebruikt u [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create) om de virtuele machine of de virtuele-machine schaalset ' lezer ' toegang te geven tot een opslag account met de naam ' myStorageAcct ':

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-resources](overview.md)
- Als u beheerde identiteit wilt inschakelen op een virtuele machine van Azure, raadpleegt u [beheerde identiteiten voor Azure-resources configureren op een Azure-VM met behulp van Azure cli](qs-configure-cli-windows-vm.md).
- Zie [beheerde identiteiten voor Azure-resources configureren op een schaalset voor virtuele machines met behulp van Azure cli](qs-configure-cli-windows-vmss.md)om beheerde identiteit in te scha kelen op een virtuele machine schaalset van Azure.
