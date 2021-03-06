---
title: 'Quickstart: een Azure-sleutelkluis maken met behulp van de Azure CLI'
description: Quickstart voor het maken van een Azure-sleutelkluis met behulp van de Azure CLI
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 588bee2922ee44f3f89b5d252b5b4a6991d26b82
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815122"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Quickstart: een sleutelkluis maken met behulp van de Azure CLI

Azure Key Vault is een cloudservice die een beveiligd archief biedt voor [sleutels](../keys/index.yml), [geheimen](../secrets/index.yml) en [certificaten](../certificates/index.yml). Zie [Over Azure Key Vault](overview.md) voor meer informatie over Key Vault. Zie [Sleutels, geheimen en certificaten](about-keys-secrets-certificates.md) voor meer informatie over wat er kan worden opgeslagen in een sleutelkluis.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze quickstart is versie 2.0.4 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis gemaakt en deze vervolgens weer verwijderd. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees een [Overzicht van Azure Key Vault](overview.md)
- Raadpleeg het [Overzicht voor Azure Key Vault-beveiliging](security-features.md)
- Raadpleeg de naslaginformatie voor de [az keyvault-opdrachten van de Azure CLI](/cli/azure/keyvault)

