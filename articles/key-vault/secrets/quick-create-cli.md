---
title: 'Quick Start: een geheim instellen en ophalen uit Azure Key Vault'
description: Snelstart waarin wordt getoond hoe u een geheim uit Azure Key Vault instelt en ophaalt met behulp van Azure CLI
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: c2c6d106a198445fdbd08fbaeb7d03472b688b62
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071372"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Quickstart: een geheim uit Azure Key Vault instellen en ophalen met behulp van Azure CLI

In deze quickstart maakt u een sleutelkluis in Azure Key Vault met behulp van de Azure CLI. Azure Key Vault is een cloudservice die werkt als een beveiligd geheimenarchief. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. U kunt het [Overzicht](../general/overview.md) raadplegen voor meer informatie over Key Vault. Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources met behulp van opdrachten of scripts. Nadat u dat hebt gedaan, gaat u een geheim opslaan.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze quickstart is versie 2.0.4 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-secret-to-key-vault"></a>Een geheim toevoegen aan Key Vault

Als u een geheim wilt toevoegen aan de kluis, hoeft u maar een paar extra stappen uit te voeren. Dit wachtwoord zou kunnen worden gebruikt door een toepassing. Het wachtwoord krijgt de naam **ExamplePassword** en slaat daarin de waarde van **hVFkk965BuUv** op.

Typ de onderstaande opdrachten om een geheim te maken in de sleutelkluis met de naam **ExamplePassword** waarin de waarde **hVFkk965BuUv** wordt opgeslagen:

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "ExamplePassword" --value "hVFkk965BuUv"
```

U kunt nu verwijzen naar dit wachtwoord dat u aan Azure Key Vault hebt toegevoegd met behulp van de bijbehorende URI. Gebruik **' https://<your-unique-sleutel kluis-name>. Vault.Azure.net/Secrets/ExamplePassword '** om de huidige versie op te halen.

Als u de waarde in het geheim als tekst zonder opmaak wilt weergeven:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "<your-unique-keyvault-name>"
```

U hebt nu een sleutelkluis gemaakt, een geheim opgeslagen en dat vervolgens opgehaald.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis gemaakt en daar een geheim in opgeslagen. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Raadpleeg de naslaginformatie voor de [az keyvault-opdrachten van de Azure CLI](/cli/azure/keyvault)
- Raadpleeg het [Overzicht voor Key Vault-beveiliging](../general/security-overview.md)
