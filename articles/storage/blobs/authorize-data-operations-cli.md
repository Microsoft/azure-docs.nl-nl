---
title: Kies hoe u de toegang tot BLOB-gegevens wilt autoriseren met Azure CLI
titleSuffix: Azure Storage
description: Geef op hoe gegevens bewerkingen moeten worden geautoriseerd op BLOB-gegevens met de Azure CLI. U kunt gegevens bewerkingen autoriseren met behulp van Azure AD-referenties, met de toegangs sleutel voor het account of met een SAS-token (Shared Access Signature).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 06b37e8b25d932115384124a45156c801fb9708f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361669"
---
# <a name="choose-how-to-authorize-access-to-blob-data-with-azure-cli"></a>Kies hoe u de toegang tot BLOB-gegevens wilt autoriseren met Azure CLI

Azure Storage biedt uitbrei dingen voor Azure CLI, waarmee u kunt opgeven hoe u bewerkingen op BLOB-gegevens wilt autoriseren. U kunt gegevens bewerkingen op de volgende manieren autoriseren:

- Met een Azure Active Directory-beveiligingsprincipal (Azure AD). Micro soft raadt u aan Azure AD-referenties te gebruiken voor superieure beveiliging en gebruiks gemak.
- Met de toegangs sleutel voor het account of een SAS-token (Shared Access Signature).

## <a name="specify-how-data-operations-are-authorized"></a>Opgeven hoe gegevens bewerkingen worden geautoriseerd

Azure CLI-opdrachten voor het lezen en schrijven van BLOB-gegevens bevatten de optionele `--auth-mode` para meter. Geef deze para meter op om aan te geven hoe een gegevens bewerking moet worden geautoriseerd:

- Stel de `--auth-mode` para meter `login` in om u aan te melden met een Azure AD-beveiligings-principal (aanbevolen).
- Stel de `--auth-mode` para meter in op de verouderde `key` waarde om de toegangs sleutel voor het account op te halen die voor autorisatie moet worden gebruikt. Als u de `--auth-mode` para meter weglaat, probeert de Azure cli ook de toegangs sleutel op te halen.

Als u de `--auth-mode` para meter wilt gebruiken, moet u ervoor zorgen dat u Azure CLI-versie 2.0.46 of hoger hebt geïnstalleerd. Voer uit `az --version` om de geïnstalleerde versie te controleren.

> [!NOTE]
> Wanneer een opslag account is vergrendeld met een Azure Resource Manager **alleen-lezen** vergrendeling, is de bewerking [lijst sleutels](/rest/api/storagerp/storageaccounts/listkeys) niet toegestaan voor dat opslag account. **Lijst sleutels** is een post-bewerking en alle post-bewerkingen worden voor komen wanneer een **alleen-lezen** vergrendeling voor het account is geconfigureerd. Daarom moeten gebruikers gebruikers die de account sleutels niet al beschikken over Azure AD-referenties gebruiken voor toegang tot BLOB-gegevens, wanneer het account is vergrendeld met een **alleen-lezen** vergrendeling.

> [!IMPORTANT]
> Als u de `--auth-mode` para meter weglaat of instelt op `key` , probeert de Azure cli de toegangs sleutel voor het account voor autorisatie te gebruiken. In dit geval raadt micro soft u aan de toegangs sleutel op te geven op de opdracht of in de omgevings variabele **AZURE_STORAGE_KEY** . Zie de sectie [omgevings variabelen instellen voor autorisatie parameters](#set-environment-variables-for-authorization-parameters)voor meer informatie over omgevings variabelen.
>
> Als u de toegangs sleutel niet opgeeft, probeert de Azure CLI de Azure Storage Resource provider aan te roepen om deze voor elke bewerking op te halen. Het uitvoeren van veel gegevens bewerkingen die een aanroep van de resource provider vereisen, kan leiden tot vertraging. Zie [schaalbaarheids-en prestatie doelen voor de resource provider van Azure Storage](../common/scalability-targets-resource-provider.md)voor meer informatie over limieten voor resource providers.

## <a name="authorize-with-azure-ad-credentials"></a>Autoriseren met Azure AD-referenties

Wanneer u zich aanmeldt bij Azure CLI met Azure AD-referenties, wordt een OAuth 2,0-toegangs token geretourneerd. Dit token wordt automatisch door Azure CLI gebruikt voor het autoriseren van volgende gegevens bewerkingen op BLOB-of wachtrij opslag. Voor ondersteunde bewerkingen hoeft u geen account sleutel of SAS-token meer door te geven met de opdracht.

U kunt machtigingen voor BLOB-gegevens toewijzen aan een Azure AD-beveiligings-principal via Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Zie [Manage access rights to Azure Storage Data with Azure RBAC](../common/storage-auth-aad-rbac-portal.md)(Engelstalig) voor meer informatie over Azure-rollen in azure Storage.

### <a name="permissions-for-calling-data-operations"></a>Machtigingen voor het aanroepen van gegevens bewerkingen

De Azure Storage-extensies worden ondersteund voor bewerkingen op BLOB-gegevens. Welke bewerkingen u kunt aanroepen, is afhankelijk van de machtigingen die zijn verleend aan de Azure AD-beveiligings-principal waarmee u zich aanmeldt bij Azure CLI. Machtigingen voor het Azure Storage van containers worden toegewezen via Azure RBAC. Als u bijvoorbeeld de rol **Storage BLOB data Reader** hebt toegewezen, kunt u script opdrachten uitvoeren die gegevens uit een container lezen. Als u de rol **BLOB Gegevensinzender voor opslag** hebt toegewezen, kunt u script opdrachten uitvoeren die een container of de gegevens in het archief lezen, schrijven of verwijderen.

Zie [opslag bewerkingen aanroepen met OAuth-tokens](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)voor meer informatie over de vereiste machtigingen voor elke Azure Storage bewerking in een container.  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Voor beeld: een bewerking voor het maken van een container met Azure AD-referenties autoriseren

In het volgende voor beeld ziet u hoe u een container maakt op basis van Azure CLI met behulp van uw Azure AD-referenties. Als u de container wilt maken, moet u zich aanmelden bij de Azure CLI en hebt u een resource groep en een opslag account nodig. Zie [Quick Start: Create, down loads en List blobs with Azure cli](../blobs/storage-quickstart-blobs-cli.md)(Engelstalig) voor meer informatie over het maken van deze resources.

1. Voordat u de container maakt, moet u de rol [Storage Blob Data Contributor](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) aan uzelf toewijzen. Hoewel u de eigenaar van het account bent, hebt u expliciete machtigingen nodig om gegevens bewerkingen uit te voeren op het opslag account. Zie voor meer informatie over het toewijzen van Azure-functies [de Azure Portal gebruiken om een Azure-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > Het kan enkele minuten duren voordat Azure-roltoewijzingen worden doorgegeven.

1. Roep de opdracht [AZ storage container Create](/cli/azure/storage/container#az-storage-container-create) aan met de `--auth-mode` para meter ingesteld op `login` om de container te maken met uw Azure AD-referenties. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autoriseren met de toegangs sleutel voor het account

Als u beschikt over de account sleutel, kunt u een Azure Storage gegevens bewerking aanroepen. Over het algemeen is het gebruik van de account sleutel minder veilig. Als de account sleutel is aangetast, is het mogelijk dat alle gegevens in uw account worden aangetast.

In het volgende voor beeld ziet u hoe u een container maakt met behulp van de toegangs sleutel voor het account. Geef de account sleutel op en geef de `--auth-mode` para meter op met de `key` waarde:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!IMPORTANT]
> Wanneer een opslag account is vergrendeld met een Azure Resource Manager **alleen-lezen** vergrendeling, is de bewerking [lijst sleutels](/rest/api/storagerp/storageaccounts/listkeys) niet toegestaan voor dat opslag account. **Lijst sleutels** is een post-bewerking en alle post-bewerkingen worden voor komen wanneer een **alleen-lezen** vergrendeling voor het account is geconfigureerd. Daarom moeten gebruikers, wanneer het account is vergrendeld met een **alleen-lezen** vergrendeling, toegang hebben tot gegevens met Azure AD-referenties.

## <a name="authorize-with-a-sas-token"></a>Autoriseren met een SAS-token

Als u beschikt over een SAS-token, kunt u gegevens bewerkingen aanroepen die zijn toegestaan door de SAS. In het volgende voor beeld ziet u hoe u een container maakt met behulp van een SAS-token:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Omgevings variabelen instellen voor autorisatie parameters

U kunt autorisatie parameters opgeven in omgevings variabelen om te voor komen dat ze worden opgenomen in elke aanroep van een Azure Storage gegevens bewerking. De volgende tabel beschrijft de beschik bare omgevings variabelen.

| Omgevingsvariabele                  | Description                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    De naam van het opslagaccount. Deze variabele moet worden gebruikt in combi natie met de sleutel van het opslag account of een SAS-token. Als er geen van beide aanwezig is, probeert de Azure CLI de toegangs sleutel voor het opslag account op te halen met behulp van het geverifieerde Azure AD-account. Als een groot aantal opdrachten tegelijk wordt uitgevoerd, is het mogelijk dat de Azure Storage Resource provider beperkings limiet is bereikt. Zie [schaalbaarheids-en prestatie doelen voor de resource provider van Azure Storage](../common/scalability-targets-resource-provider.md)voor meer informatie over limieten voor resource providers.             |
|    AZURE_STORAGE_KEY                  |    De opslagaccountsleutel. Deze variabele moet worden gebruikt in combi natie met de naam van het opslag account.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    Een connection string die de sleutel voor het opslag account of een SAS-token bevat. Deze variabele moet worden gebruikt in combi natie met de naam van het opslag account.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    Een SAS-token (Shared Access Signature). Deze variabele moet worden gebruikt in combi natie met de naam van het opslag account.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    De autorisatie modus waarmee de opdracht moet worden uitgevoerd. Toegestane waarden zijn `login` (aanbevolen) of `key` . Als u opgeeft `login` , gebruikt Azure cli uw Azure AD-referenties om de gegevens bewerking te autoriseren. Als u de legacy `key` -modus opgeeft, probeert de Azure cli een query uit te voeren voor de toegangs sleutel van het account en de opdracht met de sleutel te autoriseren.    |

## <a name="next-steps"></a>Volgende stappen

- [Azure CLI gebruiken om een Azure-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens](../common/storage-auth-aad-rbac-cli.md)
- [Toegang tot Blob-en wachtrij gegevens toestaan met beheerde identiteiten voor Azure-resources](../common/storage-auth-aad-msi.md)