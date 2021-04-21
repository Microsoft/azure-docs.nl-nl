---
title: 'Quickstart: Een & weergeven Azure Key Vault met Azure CLI'
description: In deze quickstart wordt uitgelegd hoe u een certificaat instelt en ophaalt uit Azure Key Vault met behulp van de Azure CLI
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: dc3abe43c2cdf4d0ee58ac9aed570a2c507b0d7f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815266"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-cli"></a>Quickstart: Een certificaat uit Azure Key Vault instellen en ophalen met behulp van de Azure CLI

In deze quickstart maakt u een sleutelkluis in Azure Key Vault met behulp van de Azure CLI. Azure Key Vault is een cloudservice die werkt als een beveiligd geheimenarchief. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. U kunt het [Overzicht](../general/overview.md) raadplegen voor meer informatie over Key Vault. Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources met behulp van opdrachten of scripts. Nadat u dat hebt gedaan, gaat u een certificaat opslaan.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze quickstart is versie 2.0.4 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-certificate-to-key-vault"></a>Een certificaat toevoegen aan Key Vault

Als u een certificaat wilt toevoegen aan de kluis, hoeft u maar een paar extra stappen uit te voeren. Dit certificaat kan worden gebruikt door een toepassing. 

Typ de onderstaande opdrachten om een zelfondertekend certificaat te maken met het standaardbeleid met de naam **ExampleCertificate**:

```azurecli
az keyvault certificate create --vault-name "<your-unique-keyvault-name>" -n ExampleCertificate -p "$(az keyvault certificate get-default-policy)"
```

U kunt nu verwijzen naar dit certificaat dat u aan Azure Key Vault hebt toegevoegd met behulp van de bijbehorende URI. Gebruik **https://<your-unique-keyvault-name>.vault.azure.net/certificates/ExampleCertificate'** om de huidige versie op te halen. 

Een eerder opgeslagen certificaat weergeven:

```azurecli

az keyvault certificate show --name "ExampleCertificate" --vault-name "<your-unique-keyvault-name>"
```

U hebt nu een sleutelkluis gemaakt, een certificaat opgeslagen en dat vervolgens opgehaald.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Key Vault gemaakt en daar een certificaat in opgeslagen. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Raadpleeg de naslaginformatie voor de [az keyvault-opdrachten van de Azure CLI](/cli/azure/keyvault)
- Raadpleeg het [Overzicht voor Key Vault-beveiliging](../general/security-features.md)
