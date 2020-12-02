---
title: Azure Attestation instellen met Azure CLI
description: Een attestation-provider instellen en configureren met Azure CLI.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: dee9e7596c0a30301d9e0453ef22a6dfe9541522
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "96020939"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Quickstart: Azure Attestation instellen met Azure CLI

Ga aan de slag met Azure Attestation door met behulp van Azure CLI attestation in te stellen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="get-started"></a>Aan de slag

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

1. Voer de opdracht [az attestation create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) uit om een attestation-provider te maken:

   ```azurecli
   az attestation create --resource-group attestationrg --name attestationProvider --location uksouth \
      --attestation-policy SgxDisableDebugMode --certs-input-path C:\test\policySignersCertificates.pem
   ```

   Met de parameter **--certs-input-path** wordt een reeks vertrouwde handtekeningsleutels opgegeven. Als u een bestandsnaam voor deze parameter opgeeft, moet de attestation-provider alleen worden geconfigureerd met beleidsregels in ondertekende JWT-indeling. Anders kan beleid worden geconfigureerd in tekstindeling of in een niet-ondertekende JWT-indeling. Zie [Basisconcepten](basic-concepts.md) voor meer informatie over JWT. Zie [Voorbeelden van een attestation-certificaat van beleidsondertekening](policy-signer-examples.md) voor voorbeelden van certificaten.

1. Voer de opdracht [az attestation show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) uit om eigenschappen van de attestation-provider op te halen, zoals status en AttestURI:

   ```azurecli
   az attestation show --resource-group attestationrg --name attestationProvider
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
az attestation delete --resource-group attestationrg --name attestationProvider
```

## <a name="policy-management"></a>Beleidsbeheer

Voor het beheren van beleid, heeft een Azure AD-gebruiker de volgende machtigingen nodig voor `Actions`:

- `Microsoft.Attestation/attestationProviders/attestation/read`
- `Microsoft.Attestation/attestationProviders/attestation/write`
- `Microsoft.Attestation/attestationProviders/attestation/delete`

Deze machtigingen kunnen worden toegewezen aan een AD-gebruiker via een rol zoals `Owner` (machtigingen voor jokertekens), `Contributor` (machtigingen voor jokertekens) of `Attestation Contributor` (alleen specifieke machtigingen voor Azure Attestation).  

Voor het lezen van beleid, heeft een Azure AD-gebruiker de volgende machtigingen nodig voor `Actions`:

- `Microsoft.Attestation/attestationProviders/attestation/read`

Deze machtiging kan worden toegewezen aan een AD-gebruiker via een rol zoals `Reader` (machtigingen voor jokertekens) of `Attestation Reader` (alleen specifieke machtigingen voor Azure Attestation).

Gebruik de opdrachten die hieronder worden beschreven om beheer van beleid voor een attestation-provider te kunnen bieden (één TEE per keer).

De opdracht [az attestation policy show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) retourneert het huidige beleid voor de opgegeven TEE:

```azurecli
az attestation policy show --resource-group attestationrg --name attestationProvider --tee SgxEnclave
```

> [!NOTE]
> De opdracht geeft beleid weer in zowel tekst- als JWT-indeling.

De volgende typen TEE worden ondersteund:

- `CyResComponent`
- `OpenEnclave`
- `SgxEnclave`
- `VSMEnclave`

Gebruik de opdracht [az attestation policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) om een nieuw beleid voor de opgegeven TEE in te stellen.

```azurecli
az attestation policy set --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --new-attestation-policy newAttestationPolicyname
```

Het attestation-beleid in JWT-indeling moet een claim met de naam `AttestationPolicy` bevatten. Een ondertekend beleid moet zijn ondertekend met een persoonlijke sleutel die overeenkomt met een van de bestaande certificaten van beleidsondertekening.

Zie [Voorbeelden van een attestation-beleid](policy-examples.md) voor voorbeelden van beleid.

Met de opdracht [az attestation policy reset](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_reset) wordt een nieuw beleid voor de opgegeven TEE ingesteld.

```azurecli
az attestation policy reset --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --policy-jws "eyJhbGciOiJub25lIn0.."
```

## <a name="policy-signer-certificates-management"></a>Beheer van certificaten voor beleidsondertekening

Gebruik de volgende opdrachten om de certificaten van beleidsondertekening voor een attestation-provider te beheren:

```azurecli
az attestation signer list --resource-group attestationrg --name attestationProvider

az attestation signer add --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."

az attestation signer remove --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."
```

Het certificaat van beleidsondertekening is een ondertekende JWT met een claim met de naam `maa-policyCertificate`. De waarde van de claim is een JWK die de vertrouwde ondertekeningssleutel bevat die moet worden toegevoegd. De JWT moet zijn ondertekend met een persoonlijke sleutel die overeenkomt met een van de bestaande certificaten van beleidsondertekening. Zie [Basisconcepten](basic-concepts.md) voor meer informatie over JWT en JWK.

Houd er rekening mee dat alle semantische manipulatie van het certificaat van beleidsondertekening moet worden uitgevoerd buiten Azure CLI. Wat Azure CLI betreft, is dit een eenvoudige tekenreeks.

Zie [Voorbeelden van een attestation-certificaat van beleidsondertekening](policy-signer-examples.md) voor voorbeelden van certificaten.

## <a name="next-steps"></a>Volgende stappen

- [Een Attestation-beleid ontwerpen en ondertekenen](author-sign-policy.md)
- [Attestation met een SGX-enclave implementeren met behulp van codevoorbeelden](/samples/browse/?expanded=azure&terms=attestation)
