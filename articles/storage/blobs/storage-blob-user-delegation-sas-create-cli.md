---
title: Azure CLI gebruiken om een gebruikers delegering SA'S te maken voor een container of BLOB
titleSuffix: Azure Storage
description: Meer informatie over het maken van een gebruiker met Azure Active Directory referenties met behulp van Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 536cd01fbcf2c5d18a8c12030b709427d9bb91b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98703603"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>Een SAS voor gebruikers overdracht maken voor een container of BLOB met de Azure CLI

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In dit artikel wordt beschreven hoe u Azure Active Directory (Azure AD)-referenties gebruikt om een gebruikers delegering SA'S te maken voor een container of BLOB met de Azure CLI.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>De nieuwste versie van de Azure CLI installeren

Als u de Azure CLI wilt gebruiken om een SAS met Azure AD-referenties te beveiligen, moet u eerst controleren of u de meest recente versie van Azure CLI hebt geïnstalleerd. Zie [de Azure cli installeren](/cli/azure/install-azure-cli)voor meer informatie over het installeren van de Azure cli.

Als u een gebruikers delegering SA'S wilt maken met behulp van de Azure CLI, moet u versie 2.0.78 of hoger hebben geïnstalleerd. Gebruik de opdracht om de geïnstalleerde versie te controleren `az --version` .

## <a name="sign-in-with-azure-ad-credentials"></a>Aanmelden met Azure AD-referenties

Meld u aan bij de Azure CLI met uw Azure AD-referenties. Zie [Aanmelden met de Azure CLI](/cli/azure/authenticate-azure-cli) voor meer informatie.

## <a name="assign-permissions-with-azure-rbac"></a>Machtigingen toewijzen met Azure RBAC

Als u een gebruikers delegering SA'S wilt maken op basis van Azure PowerShell, moet aan het Azure AD-account dat wordt gebruikt om u aan te melden bij Azure CLI een rol worden toegewezen die de actie **micro soft. Storage/Storage accounts/blobServices/generateUserDelegationKey** bevat. Met deze machtiging is het mogelijk dat Azure AD-account de *gebruikers delegerings sleutel* aanvraagt. De sleutel voor gebruikers overdracht wordt gebruikt voor het ondertekenen van de SA'S van de gebruikers delegering. De rol voor het opgeven van de actie **micro soft. Storage/Storage accounts/blobServices/generateUserDelegationKey** moet worden toegewezen op het niveau van het opslag account, de resource groep of het abonnement.

Als u onvoldoende machtigingen hebt voor het toewijzen van Azure-functies aan een Azure AD-beveiligingsprincipal, moet u mogelijk de eigenaar of beheerder van het account vragen de benodigde machtigingen toe te wijzen.

In het volgende voor beeld wordt de rol **Storage BLOB data Inzender** toegewezen, inclusief de actie **micro soft. Storage/Storage accounts/blobServices/generateUserDelegationKey** . De rol is bereik op het niveau van het opslag account.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Zie [ingebouwde rollen van Azure](../../role-based-access-control/built-in-roles.md)voor meer informatie over de ingebouwde rollen die de actie **micro soft. Storage/Storage accounts/blobServices/generateUserDelegationKey** bevatten.

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Azure AD-referenties gebruiken voor het beveiligen van een SAS

Wanneer u een SAS voor gebruikers overdracht maakt met de Azure CLI, wordt de sleutel voor gebruikers overdracht die wordt gebruikt om de SA'S te ondertekenen, impliciet voor u gemaakt. De begin tijd en verloop tijd die u opgeeft voor de SA'S, worden ook gebruikt als de begin tijd en verloop tijd voor de gebruikers delegerings sleutel.

Omdat het maximale interval waarover de gebruikers overdracht-sleutel geldig is, zeven dagen na de begin datum is, moet u een verloop tijd opgeven voor de SA'S die binnen zeven dagen na de begin tijd vallen. De SAS is ongeldig nadat de gebruikers delegerings sleutel is verlopen, waardoor een SAS met een verloop tijd van meer dan zeven dagen nog geldig is gedurende 7 dagen.

Wanneer u een SAS voor gebruikers overdracht maakt `--auth-mode login` , `--as-user parameters` zijn de en vereist. Geef de *aanmeldings naam* op voor de `--auth-mode` para meter zodat aanvragen voor Azure Storage worden geautoriseerd met uw Azure AD-referenties. Geef de `--as-user` para meter op om aan te geven dat de geretourneerde sa's een SAS voor gebruikers overdracht moeten zijn.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Een SAS voor gebruikers overdracht maken voor een container

Als u een gebruikers delegering SAS wilt maken voor een container met de Azure CLI, roept u de opdracht [AZ storage container generate-SAS](/cli/azure/storage/container#az-storage-container-generate-sas) aan.

Ondersteunde machtigingen voor een gebruikers delegering SA'S voor een container zijn: toevoegen, maken, verwijderen, lijst, lezen en schrijven. Machtigingen kunnen afzonderlijk of gecombineerd worden opgegeven. Zie [een gebruiker delegering Sa's maken](/rest/api/storageservices/create-user-delegation-sas)voor meer informatie over deze machtigingen.

In het volgende voor beeld wordt een SAS-token voor gebruikers overdracht voor een container geretourneerd. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

Het geretourneerde SAS-token voor gebruikers overdracht is vergelijkbaar met:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Een SAS voor gebruikers overdracht maken voor een BLOB

Als u een gebruikers delegering SA'S wilt maken voor een blob met de Azure CLI, roept u de opdracht [AZ Storage BLOB generate-SAS](/cli/azure/storage/blob#az-storage-blob-generate-sas) aan.

Ondersteunde machtigingen voor een gebruikers delegering SA'S voor een BLOB zijn: toevoegen, maken, verwijderen, lezen en schrijven. Machtigingen kunnen afzonderlijk of gecombineerd worden opgegeven. Zie [een gebruiker delegering Sa's maken](/rest/api/storageservices/create-user-delegation-sas)voor meer informatie over deze machtigingen.

De volgende syntaxis retourneert een gebruikers delegering SA'S voor een blob. In het voor beeld wordt de `--full-uri` para meter opgegeven, die de BLOB-URI retourneert waaraan het SAS-token is toegevoegd. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

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

De geretourneerde SAS-URI voor gebruikers overdracht is vergelijkbaar met:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> Een door de gebruiker overgedragen SA'S biedt geen ondersteuning voor het definiëren van machtigingen met een opgeslagen toegangs beleid.

## <a name="revoke-a-user-delegation-sas"></a>Een SAS voor gebruikers overdracht intrekken

Als u een gebruikers delegering SA'S wilt intrekken vanuit Azure CLI, roept u de opdracht [AZ Storage account REVOKE-Delegation-Keys](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) aan. Met deze opdracht worden alle sleutels voor gebruikers overdracht die zijn gekoppeld aan het opgegeven opslag account ingetrokken. Shared Access signatures die zijn gekoppeld aan deze sleutels, worden ongeldig gemaakt.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Zowel de gebruikers overdracht sleutel als Azure-roltoewijzingen worden in de cache opgeslagen door Azure Storage. er kan dus een vertraging optreden wanneer u het proces van het intrekken initieert en wanneer een bestaande SA'S van de gebruikers overdracht ongeldig wordt.

## <a name="next-steps"></a>Volgende stappen

- [Een SAS (REST API) voor gebruikers overdracht maken](/rest/api/storageservices/create-user-delegation-sas)
- [Sleutel bewerking voor gebruikers overdracht ophalen](/rest/api/storageservices/get-user-delegation-key)
