---
title: Galerie-installatie kopieën delen via tenants
description: Meer informatie over het delen van VM-installatie kopieën in azure-tenants met behulp van gedeelde afbeeldings galerieën met Azure CLI.
author: axayjo
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: f0192a6eb60c72c9f7ac605db492f4e5f97b76ed
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779257"
---
# <a name="share-gallery-vm-images-across-azure-tenants-using-the-azure-cli"></a>Galerie-VM-installatie kopieën delen in azure-tenants met behulp van Azure CLI

Met de galerie met gedeelde afbeeldingen kunt u afbeeldingen delen met behulp van Azure RBAC. U kunt Azure RBAC gebruiken om installatie kopieën te delen binnen uw Tenant, en zelfs voor personen buiten uw Tenant. Zie de [Galerie delen](./shared-images-portal.md#share-the-gallery)voor meer informatie over deze eenvoudige optie voor delen.

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> U kunt de portal niet gebruiken om een virtuele machine te implementeren op basis van een installatie kopie in een andere Azure-Tenant. Als u een virtuele machine wilt maken op basis van een installatie kopie die tussen tenants wordt gedeeld, moet u de Azure CLI of [Power shell](../windows/share-images-across-tenants.md)gebruiken.

## <a name="create-a-vm-using-azure-cli"></a>Een virtuele machine maken met behulp van Azure CLI

Meld u aan bij de service-principal voor Tenant 1 met behulp van de appID, de app-sleutel en de ID van Tenant 1. U kunt gebruiken `az account show --query "tenantId"` voor het ophalen van de Tenant-id's, indien nodig.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Meld u aan bij de service-principal voor Tenant 2 met behulp van de appID, de app-sleutel en de ID van Tenant 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Maak de VM. Vervang de informatie in het voor beeld door uw eigen.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt, kunt u [problemen met gedeelde afbeeldings galerieën oplossen](../troubleshooting-shared-images.md).
