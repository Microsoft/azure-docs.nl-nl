---
title: Azure CLI gebruiken om een SAS voor gebruikersdelegatie te maken voor een container of blob
titleSuffix: Azure Storage
description: Meer informatie over het maken van een SAS voor gebruikersdelegatie met Azure Active Directory referenties met behulp van Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ec434b9b6da3b3b80a3afddbb432ddeece2b389
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788543"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>Een SAS voor gebruikersdelegatie maken voor een container of blob met de Azure CLI

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In dit artikel wordt beschreven hoe Azure Active Directory (Azure AD)-referenties kunt gebruiken om een SAS voor gebruikersdelegatie te maken voor een container of blob met de Azure CLI.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>De nieuwste versie van de Azure CLI installeren

Als u de Azure CLI wilt gebruiken om een SAS te beveiligen met Azure AD-referenties, moet u eerst zorgen dat u de nieuwste versie van Azure CLI hebt geïnstalleerd. Zie Azure CLI installeren voor meer informatie over het installeren [van de Azure CLI.](/cli/azure/install-azure-cli)

Als u een SAS voor gebruikersdelegatie wilt maken met behulp van de Azure CLI, moet u versie 2.0.78 of hoger hebben geïnstalleerd. Gebruik de opdracht om de geïnstalleerde versie te `az --version` controleren.

## <a name="sign-in-with-azure-ad-credentials"></a>Aanmelden met Azure AD-referenties

Meld u aan bij de Azure CLI met uw Azure AD-referenties. Zie [Aanmelden met de Azure CLI](/cli/azure/authenticate-azure-cli) voor meer informatie.

## <a name="assign-permissions-with-azure-rbac"></a>Machtigingen toewijzen met Azure RBAC

Als u een SAS voor gebruikersdelegatie wilt maken op Azure PowerShell, moet aan het Azure AD-account dat wordt gebruikt om u aan te melden bij Azure CLI een rol worden toegewezen die de actie **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** bevat. Met deze machtiging kan dat Azure AD-account de sleutel voor *gebruikersdelegatie aanvragen.* De sleutel voor gebruikersdelegatie wordt gebruikt om de SAS voor gebruikersdelegatie te ondertekenen. De rol die de actie **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** biedt, moet worden toegewezen op het niveau van het opslagaccount, de resourcegroep of het abonnement.

Als u niet voldoende machtigingen hebt om Azure-rollen toe te wijzen aan een Azure AD-beveiligingsprincipaal, moet u mogelijk de accounteigenaar of beheerder vragen om de benodigde machtigingen toe te wijzen.

In het volgende  voorbeeld wordt de rol Bijdrager voor opslagblobgegevens toegewezen, waaronder de actie **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Het bereik van de rol ligt op het niveau van het opslagaccount.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Zie Ingebouwde [Azure-rollen](../../role-based-access-control/built-in-roles.md)voor meer informatie over de ingebouwde rollen met de actie **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.**

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Azure AD-referenties gebruiken om een SAS te beveiligen

Wanneer u een SAS voor gebruikersdelegatie maakt met de Azure CLI, wordt de sleutel voor gebruikersdelegatie die wordt gebruikt om de SAS te ondertekenen impliciet voor u gemaakt. De begintijd en verlooptijd die u voor de SAS opgeeft, worden ook gebruikt als begintijd en verlooptijd voor de sleutel voor gebruikersdelegatie.

Omdat het maximale interval waarover de sleutel voor gebruikersdelegatie geldig is, 7 dagen na de begindatum is, moet u een verlooptijd voor de SAS opgeven die binnen 7 dagen na de begintijd ligt. De SAS is ongeldig nadat de sleutel voor gebruikersdelegatie is verlopen, dus een SAS met een verlooptijd van meer dan 7 dagen is nog steeds slechts 7 dagen geldig.

Bij het maken van een SAS voor gebruikersdelegatie `--auth-mode login` zijn de `--as-user parameters` en vereist. Geef *de aanmelding* voor de parameter op, zodat aanvragen aan Azure Storage worden geautoriseerd met uw Azure `--auth-mode` AD-referenties. Geef de `--as-user` parameter op om aan te geven dat de geretourneerde SAS een SAS voor gebruikersdelegatie moet zijn.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Een SAS voor gebruikersdelegatie maken voor een container

Als u een SAS voor gebruikersdelegatie voor een container wilt maken met de Azure CLI, roept u de [opdracht az storage container generate-sas](/cli/azure/storage/container#az_storage_container_generate_sas) aan.

Ondersteunde machtigingen voor een SAS voor gebruikersdelegatie voor een container zijn onder andere Toevoegen, Maken, Verwijderen, Lijst, Lezen en Schrijven. Machtigingen kunnen singly of gecombineerd worden opgegeven. Zie Create a user delegation SAS (Een SAS voor gebruikersdelegatie maken) voor [meer informatie over deze machtigingen.](/rest/api/storageservices/create-user-delegation-sas)

In het volgende voorbeeld wordt een SAS-token voor gebruikersdelegatie voor een container retourneert. Vergeet niet om de tijdelijke aanduidingswaarden tussen vierkante haken te vervangen door uw eigen waarden:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

Het geretourneerde SAS-token voor gebruikersdelegatie is vergelijkbaar met:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Een SAS voor gebruikersdelegatie maken voor een blob

Als u een SAS voor gebruikersdelegatie voor een blob wilt maken met de Azure CLI, roept u de [opdracht az storage blob generate-sas](/cli/azure/storage/blob#az_storage_blob_generate_sas) aan.

Ondersteunde machtigingen voor een SAS voor gebruikersdelegatie in een blob zijn onder andere Toevoegen, Maken, Verwijderen, Lezen en Schrijven. Machtigingen kunnen singly of gecombineerd worden opgegeven. Zie Create a user delegation SAS (Een SAS voor gebruikersdelegatie maken) voor [meer informatie over deze machtigingen.](/rest/api/storageservices/create-user-delegation-sas)

De volgende syntaxis retourneert een SAS voor gebruikersdelegatie voor een blob. In het voorbeeld wordt de `--full-uri` parameter opgegeven, die de blob-URI retourneert waaraan het SAS-token is toegevoegd. Vergeet niet om de tijdelijke aanduidingswaarden tussen vierkante haken te vervangen door uw eigen waarden:

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user \
    --full-uri
```

De geretourneerde SAS-URI voor gebruikersdelegatie is vergelijkbaar met:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> Een SAS voor gebruikersdelegatie biedt geen ondersteuning voor het definiëren van machtigingen met een opgeslagen toegangsbeleid.

## <a name="revoke-a-user-delegation-sas"></a>Een SAS voor gebruikersdelegatie intrekken

Als u een SAS voor gebruikersdelegatie wilt intrekken vanuit de Azure CLI, roept u de [opdracht az storage account revoke-delegation-keys](/cli/azure/storage/account#az_storage_account_revoke_delegation_keys) aan. Met deze opdracht worden alle sleutels voor gebruikersdelegatie ingetrokken die zijn gekoppeld aan het opgegeven opslagaccount. Alle handtekeningen voor gedeelde toegang die aan deze sleutels zijn gekoppeld, worden ongeldig gemaakt.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Zowel de gebruikersdelegatiesleutel als de Azure-roltoewijzingen worden in de cache opgeslagen door Azure Storage, zodat er mogelijk een vertraging is tussen het initiëren van het intrekkingsproces en wanneer een bestaande SAS voor gebruikersdelegatie ongeldig wordt.

## <a name="next-steps"></a>Volgende stappen

- [Een SAS voor gebruikersdelegatie maken (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Bewerking sleutel voor gebruikersdelegatie op te halen](/rest/api/storageservices/get-user-delegation-key)
