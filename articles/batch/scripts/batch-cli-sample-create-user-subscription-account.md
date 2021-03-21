---
title: Azure CLI-voorbeeldscript - Batch-account maken - gebruikersabonnement
description: Met dit script wordt een Azure Batch-account gemaakt in gebruikersabonnementmodus. In dit account worden rekenknooppunten toegewezen aan uw abonnement.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: c9b8ba2ef782dcdc99cb18698175b8b53a53f0dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93076772"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>CLI-voorbeeld: Een Batch-account maken in gebruikersabonnementmodus

Met dit script wordt een Azure Batch-account gemaakt in gebruikersabonnementmodus. Een account dat rekenknooppunten in uw abonnement toewijst moet worden geauthenticeerd via een Azure Active Directory-token. De toegewezen rekenknooppunten tellen mee voor het vCPU-quotum (kernen) van uw abonnement. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Voor deze zelfstudie is versie 2.0.20 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.  

## <a name="example-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [az role assignment create](/cli/azure/role) | Hiermee maakt u een nieuwe roltoewijzing voor een gebruiker, groep of service-principal. |
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az keyvault create](/cli/azure/keyvault#az-keyvault-create) | Hiermee wordt een sleutelkluis verwijderd. |
| [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) | Hiermee wordt het beveiligingsbeleid van de opgegeven sleutelkluis bijgewerkt. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Hiermee wordt de Batch-account gemaakt.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Hiermee wordt authenticatie uitgevoerd met het opgegeven Batch-account voor verdere interactie met de CLI.  |
| [az group delete](/cli/azure/group#az-group-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.
