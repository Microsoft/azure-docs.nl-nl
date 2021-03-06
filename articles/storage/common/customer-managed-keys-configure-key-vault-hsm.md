---
title: Versleuteling configureren met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault beheerde HSM (preview-versie)
titleSuffix: Azure Storage
description: Meer informatie over het configureren van Azure Storage versleuteling met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault Managed HSM (preview) met behulp van Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/05/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: f9be9272a898ad48f3553d4c5e48952e1fcdde81
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218635"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>Versleuteling configureren met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault beheerde HSM (preview-versie)

Azure Storage versleutelt alle gegevens in een opslag account in rust. Standaard worden gegevens versleuteld met door micro soft beheerde sleutels. Voor extra controle over versleutelings sleutels kunt u uw eigen sleutels beheren. Door de klant beheerde sleutels moeten worden opgeslagen in Azure Key Vault of Key Vault beheerde hardware security model (HSM) (preview). Een door Azure Key Vault beheerde HSM is een door FIPS 140-2 gevalideerde HSM.

In dit artikel wordt beschreven hoe u versleuteling configureert met door de klant beheerde sleutels die zijn opgeslagen in een beheerde HSM met behulp van Azure CLI. Zie [Configure Encryption with door de klant beheerde sleutels die zijn opgeslagen in azure Key Vault](customer-managed-keys-configure-key-vault.md)voor meer informatie over het configureren van versleuteling met door de klant beheerde sleutels die zijn opgeslagen in een sleutel kluis.

> [!IMPORTANT]
>
> Versleuteling met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault beheerde HSM is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of nog niet beschikbaar zijn.
>
> Azure Key Vault en Azure Key Vault beheerde HSM ondersteunen dezelfde Api's en beheer interfaces voor configuratie.

## <a name="assign-an-identity-to-the-storage-account"></a>Een identiteit toewijzen aan het opslag account

Wijs eerst een door het systeem toegewezen beheerde identiteit toe aan het opslag account. U gebruikt deze beheerde identiteit om de machtigingen voor het opslag account te verlenen voor toegang tot de beheerde HSM. Zie [Wat zijn beheerde identiteiten voor Azure-resources?](../../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over door het systeem toegewezen beheerde identiteiten.

Als u een beheerde identiteit wilt toewijzen met behulp van Azure CLI, roept u de [Update AZ Storage account](/cli/azure/storage/account#az-storage-account-update)aan. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>Een rol aan het opslag account toewijzen voor toegang tot de beheerde HSM

Wijs vervolgens de **beheerde HSM crypto-service versleutelings** functie toe aan de beheerde identiteit van het opslag account, zodat het opslag account machtigingen heeft voor de beheerde HSM. Micro soft raadt u aan de roltoewijzing te bereiken voor het niveau van de afzonderlijke sleutel om de zo weinig mogelijk privileges toe te kennen aan de beheerde identiteit.

Voor het maken van de roltoewijzing voor het opslag account roept u de roltoewijzing [AZ Key kluis Create](/cli/azure/role/assignment#az_role_assignment_create). Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden.
  
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

Configureer ten slotte Azure Storage versleuteling met door de klant beheerde sleutels om een sleutel te gebruiken die is opgeslagen in de beheerde HSM. Ondersteunde sleutel typen zijn onder meer RSA-HSM-sleutels van grootte 2048, 3072 en 4096. Installeer Azure CLI 2.12.0 of hoger om versleuteling te configureren voor het gebruik van een door de klant beheerde sleutel in een beheerde HSM. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) voor meer informatie.

Als u de sleutel versie automatisch wilt bijwerken voor een door de klant beheerde sleutel, laat u de sleutel versie weg wanneer u versleuteling configureert met door de klant beheerde sleutels voor het opslag account. Roep [AZ Storage account update](/cli/azure/storage/account#az_storage_account_update) aan om de versleutelings instellingen van het opslag account bij te werken, zoals wordt weer gegeven in het volgende voor beeld. Neem de `--encryption-key-source parameter` en stel deze in op `Microsoft.Keyvault` om door de klant beheerde sleutels voor het account in te scha kelen. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden.

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

Als u de versie voor een door de klant beheerde sleutel hand matig wilt bijwerken, neemt u de sleutel versie op wanneer u versleuteling voor het opslag account configureert:

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Wanneer u de sleutel versie hand matig bijwerkt, moet u de versleutelings instellingen van het opslag account bijwerken om de nieuwe versie te gebruiken. Eerst moet u een query uitvoeren voor de sleutel kluis-URI door het aanroepen van [AZ Key kluis show](/cli/azure/keyvault#az-keyvault-show), en voor de sleutel versie door het aanroepen van [AZ sleutel kluis Key List-versies](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Roep vervolgens [AZ Storage account update](/cli/azure/storage/account#az-storage-account-update) aan om de versleutelings instellingen van het opslag account bij te werken om de nieuwe versie van de sleutel te gebruiken, zoals wordt weer gegeven in het vorige voor beeld.

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor inactieve gegevens](storage-service-encryption.md)
- [Door de klant beheerde sleutels voor Azure Storage versleuteling](customer-managed-keys-overview.md)
