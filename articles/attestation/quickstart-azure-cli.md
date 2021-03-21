---
title: Azure Attestation instellen met Azure CLI
description: Een attestation-provider instellen en configureren met Azure CLI.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: ae283785b4d4dc80c6b9b6c3997aaf82c9ff0f2f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178708"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Quickstart: Azure Attestation instellen met Azure CLI

Ga aan de slag met het instellen van [Azure Attestation met Azure CLI](/cli/azure/ext/attestation/attestation).

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="get-started"></a>Aan de slag

1. Installeer deze extensie met behulp van de CLI-opdracht

   ```azurecli
   az extension add --name attestation
   ```
   
1. Controleer de versie

   ```azurecli
   az extension show --name attestation --query version
   ```

1. Gebruik de volgende opdracht om u aan te melden bij Azure:

   ```azurecli
   az login
   ```

1. Als dat nodig is, schakelt u over naar het abonnement voor Azure Attestation:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Registreer de resourceprovider Microsoft.Attestation in het abonnement met de opdracht [az provider register](/cli/azure/provider#az_provider_register):

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   Zie [Azure-resourceproviders en -typen](../azure-resource-manager/management/resource-providers-and-types.md) voor meer informatie over Azure-resourceproviders en het configureren en het beheren ervan.

   > [!NOTE]
   > U hoeft slechts één keer per abonnement een resourceprovider te registreren.

1. Maak een resource groep voor de Attestation-provider. U kunt andere Azure-resources in dezelfde resourcegroep plaatsen, met inbegrip van een virtuele machine met een client-toepassingsexemplaar). Voer de opdracht [az group create](/cli/azure/group#az_group_create) uit om een resourcegroep te maken, of gebruik een bestaande resourcegroep:

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>Maak en beheer een Attestation-provider

Hier vindt u de opdrachten die u kunt gebruiken om de attestation-provider te maken en beheren:

1. Voer de opdracht [az attestation create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) uit om een attestation-provider zonder beleidsondertekeningsvereiste te maken:

   ```azurecli
   az attestation create --name "myattestationprovider" --resource-group "MyResourceGroup" --location westus
   ```
   
1. Voer de opdracht [az attestation show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) uit om eigenschappen van de attestation-provider op te halen, zoals status en AttestURI:

   ```azurecli
   az attestation show --name "myattestationprovider" --resource-group "MyResourceGroup"
   ```

   Deze opdracht zorgt ervoor dat er waarden worden weergegeven zoals in de volgende uitvoer:

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

U kunt een attestation-provider verwijderen met behulp van de opdracht [az attestation delete](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete):

```azurecli
az attestation delete --name "myattestationprovider" --resource-group "sample-resource-group"
```

## <a name="policy-management"></a>Beleidsbeheer

Gebruik de opdrachten die hieronder worden beschreven, om beleidsbeheer voor een attestation-provider te kunnen bieden, één attestation-type per keer.

De opdracht [az attestation policy show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) retourneert het huidige beleid voor de opgegeven TEE:

```azurecli
az attestation policy show --name "myattestationprovider" --resource-group "MyResourceGroup" --attestation-type SGX-IntelSDK
```

> [!NOTE]
> De opdracht geeft beleid weer in zowel tekst- als JWT-indeling.

De volgende typen TEE worden ondersteund:

- `SGX-IntelSDK`
- `SGX-OpenEnclaveSDK`
- `TPM`

Gebruik de opdracht [az attestation policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) om nieuw beleid voor het opgegeven attestation-type in te stellen.

Beleid in de tekstindeling instellen voor een opgegeven attestation-type met behulp van het bestandspad:

```azurecli
az attestation policy set --name testatt1 --resource-group testrg --attestation-type SGX-IntelSDK --new-attestation-policy-file "{file_path}"
```

Beleid in de JWT-indeling instellen voor een opgegeven attestation-type met behulp van het bestandspad:

```azurecli
az attestation policy set --name "myattestationprovider" --resource-group "MyResourceGroup" \
--attestation-type SGX-IntelSDK -f "{file_path}" --policy-format JWT
```

## <a name="next-steps"></a>Volgende stappen

- [Een Attestation-beleid ontwerpen en ondertekenen](author-sign-policy.md)
- [Attestation met een SGX-enclave implementeren met behulp van codevoorbeelden](/samples/browse/?expanded=azure&terms=attestation)
