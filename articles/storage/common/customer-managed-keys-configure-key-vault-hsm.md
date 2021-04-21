---
title: Versleuteling configureren met door de klant beheerde sleutels die zijn Azure Key Vault beheerde HSM (preview)
titleSuffix: Azure Storage
description: Meer informatie over het configureren Azure Storage versleuteling met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault Beheerde HSM (preview) met behulp van Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: f9b40c934cb428a31a3feb77195518d5351818d7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785357"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>Versleuteling configureren met door de klant beheerde sleutels die zijn Azure Key Vault beheerde HSM (preview)

Azure Storage versleutelt alle gegevens in een opslagaccount at rest. Gegevens worden standaard versleuteld met door Microsoft beheerde sleutels. Voor extra controle over versleutelingssleutels kunt u uw eigen sleutels beheren. Door de klant beheerde sleutels moeten worden opgeslagen in Azure Key Vault of Key Vault Managed Hardware Security Model (HSM) (preview). Een Azure Key Vault beheerde HSM is een met FIPS 140-2 Niveau 3 gevalideerde HSM.

In dit artikel wordt beschreven hoe u versleuteling configureert met door de klant beheerde sleutels die zijn opgeslagen in een beheerde HSM met behulp van Azure CLI. Zie Versleuteling configureren met door de klant beheerde sleutels die zijn opgeslagen in een sleutelkluis voor meer informatie over het configureren van versleuteling met door de klant beheerde sleutels die zijn opgeslagen [in Azure Key Vault.](customer-managed-keys-configure-key-vault.md)

> [!IMPORTANT]
>
> Versleuteling met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault Beheerde HSM is momenteel in **PREVIEW.** Zie de Aanvullende gebruiksvoorwaarden voor [Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor juridische voorwaarden die van toepassing zijn op Azure-functies die bètaversies, preview-functies of anderszins nog niet algemeen beschikbaar zijn.
>
> Azure Key Vault en Azure Key Vault beheerde HSM ondersteunen dezelfde API's en beheerinterfaces voor configuratie.

## <a name="assign-an-identity-to-the-storage-account"></a>Een identiteit toewijzen aan het opslagaccount

Wijs eerst een door het systeem toegewezen beheerde identiteit toe aan het opslagaccount. U gebruikt deze beheerde identiteit om het opslagaccount toegang te verlenen tot de beheerde HSM. Zie Wat zijn beheerde identiteiten voor Azure-resources? voor meer informatie over door het systeem toegewezen [beheerde identiteiten.](../../active-directory/managed-identities-azure-resources/overview.md)

Als u een beheerde identiteit wilt toewijzen met behulp van Azure CLI, roept u [az storage account update aan.](/cli/azure/storage/account#az_storage_account_update) Vergeet niet om de tijdelijke aanduidingen tussen haakjes te vervangen door uw eigen waarden:

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>Een rol toewijzen aan het opslagaccount voor toegang tot de beheerde HSM

Wijs vervolgens de rol Crypto Service Encryption van de beheerde **HSM** toe aan de beheerde identiteit van het opslagaccount, zodat het opslagaccount machtigingen heeft voor de beheerde HSM. Microsoft raadt u aan de roltoewijzing te beperken tot het niveau van de afzonderlijke sleutel om zo weinig mogelijk bevoegdheden te verlenen aan de beheerde identiteit.

Als u de roltoewijzing voor het opslagaccount wilt maken, roept [u az key vault role assignment create aan.](/cli/azure/role/assignment#az_role_assignment_create) Vergeet niet om de waarden van de tijdelijke aanduiding tussen haakjes te vervangen door uw eigen waarden.
  
```azurecli
storage_account_principal = $(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault role assignment create \
    --hsm-name <hsm-name> \
    --role "Managed HSM Crypto Service Encryption" \
    --assignee $storage_account_principal \
    --scope /keys/<key-name>
```

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>Versleuteling configureren met een sleutel in de beheerde HSM

Configureer tot slot Azure Storage versleuteling met door de klant beheerde sleutels om een sleutel te gebruiken die is opgeslagen in de beheerde HSM. Ondersteunde sleuteltypen zijn RSA-HSM-sleutels van grootten 2048, 3072 en 4096. Zie Een HSM-sleutel maken voor meer informatie over het maken van een sleutel in een [beheerde HSM.](../../key-vault/managed-hsm/key-management.md#create-an-hsm-key)

Installeer Azure CLI 2.12.0 of hoger om versleuteling te configureren voor het gebruik van een door de klant beheerde sleutel in een beheerde HSM. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) voor meer informatie.

Als u de sleutelversie voor een door de klant beheerde sleutel automatisch wilt bijwerken, laat u de sleutelversie weg wanneer u versleuteling configureert met door de klant beheerde sleutels voor het opslagaccount. Roep [az storage account update aan om](/cli/azure/storage/account#az_storage_account_update) de versleutelingsinstellingen van het opslagaccount bij te werken, zoals wordt weergegeven in het volgende voorbeeld. Neem de `--encryption-key-source parameter` op en stel deze in op om door de klant `Microsoft.Keyvault` beheerde sleutels voor het account in teschakelen. Vergeet niet om de waarden van de tijdelijke aanduiding tussen vierkante haken te vervangen door uw eigen waarden.

```azurecli
hsmurl = $(az keyvault show \
    --hsm-name <hsm-name> \
    --query properties.hsmUri \
    --output tsv)

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Als u de versie voor een door de klant beheerde sleutel handmatig wilt bijwerken, moet u de sleutelversie opnemen wanneer u versleuteling voor het opslagaccount configureert:

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Wanneer u de sleutelversie handmatig bijwerkt, moet u de versleutelingsinstellingen van het opslagaccount bijwerken om de nieuwe versie te gebruiken. Zoek eerst naar de sleutelkluis-URI door [az keyvault show](/cli/azure/keyvault#az_keyvault_show)aan te roepen en voor de sleutelversie door [az keyvault key list-versions aan te roepen.](/cli/azure/keyvault/key#az_keyvault_key_list_versions) Roep vervolgens [az storage account update aan om](/cli/azure/storage/account#az_storage_account_update) de versleutelingsinstellingen van het opslagaccount bij te werken om de nieuwe versie van de sleutel te gebruiken, zoals wordt weergegeven in het vorige voorbeeld.

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor inactieve gegevens](storage-service-encryption.md)
- [Door de klant beheerde sleutels voor Azure Storage versleuteling](customer-managed-keys-overview.md)
