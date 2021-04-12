---
title: 'Kenmerken van een sleutel in Azure Key Vault maken en ophalen: Azure CLI'
description: Quickstart waarin wordt getoond hoe u een sleutel instelt in Azure Key Vault en daaruit ophaalt met behulp van Azure CLI
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4141e60370b397e799664b7d42384bbeb096bd05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99071168"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>Quickstart: Een sleutel instellen in Azure Key Vault en daaruit ophalen met behulp van Azure CLI

In deze quickstart maakt u een sleutelkluis in Azure Key Vault met behulp van de Azure CLI. Azure Key Vault is een cloudservice die werkt als een beveiligd geheimenarchief. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. U kunt het [Overzicht](../general/overview.md) raadplegen voor meer informatie over Key Vault. Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources met behulp van opdrachten of scripts. Nadat u dat hebt gedaan, slaat u een sleutel op.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze quickstart is versie 2.0.4 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-key-to-key-vault"></a>Een sleutel toevoegen aan Key Vault

Als u een sleutel wilt toevoegen aan de kluis, hoeft u maar een paar extra stappen uit te voeren. Deze sleutel zou kunnen worden gebruikt door een toepassing. 

Typ de onderstaande opdrachten om de sleutel **ExampleKey** te maken:

```azurecli
az keyvault key create --vault-name "<your-unique-keyvault-name>" -n ExampleKey --protection software
```

U kunt nu naar deze sleutel die u aan Azure Key Vault hebt toegevoegd, verwijzen met behulp van de URI ervan. Gebruik **' https://<your-unique-sleutel kluis-name>. Vault.Azure.net/Keys/ExampleKey '** om de huidige versie op te halen. 

Een eerder opgeslagen sleutel weergeven:

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "<your-unique-keyvault-name>"
```

U hebt nu een sleutelkluis gemaakt, een sleutel opgeslagen en deze vervolgens opgehaald.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis gemaakt en daar een sleutel in opgeslagen. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Raadpleeg de naslaginformatie voor de [az keyvault-opdrachten van de Azure CLI](/cli/azure/keyvault)
- Raadpleeg het [Overzicht voor Key Vault-beveiliging](../general/security-overview.md)
