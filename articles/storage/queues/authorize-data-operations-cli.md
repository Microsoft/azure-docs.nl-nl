---
title: Kies hoe u de toegang tot de wachtrij gegevens wilt autoriseren met Azure CLI
titleSuffix: Azure Storage
description: Geef op hoe gegevens bewerkingen moeten worden geautoriseerd voor wachtrij gegevens met de Azure CLI. U kunt gegevens bewerkingen autoriseren met behulp van Azure AD-referenties, met de toegangs sleutel voor het account of met een SAS-token (Shared Access Signature).
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozgun
ms.date: 11/13/2020
ms.topic: how-to
ms.service: storage
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 01b78fa3250f371cfc4d713668531664ef8c139e
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587601"
---
# <a name="choose-how-to-authorize-access-to-queue-data-with-azure-cli"></a>Kies hoe u de toegang tot de wachtrij gegevens wilt autoriseren met Azure CLI

Azure Storage biedt uitbrei dingen voor Azure CLI waarmee u kunt opgeven hoe u bewerkingen op wachtrij gegevens wilt autoriseren. U kunt gegevens bewerkingen op de volgende manieren autoriseren:

- Met een Azure Active Directory-beveiligingsprincipal (Azure AD). Micro soft raadt u aan Azure AD-referenties te gebruiken voor superieure beveiliging en gebruiks gemak.
- Met de toegangs sleutel voor het account of een SAS-token (Shared Access Signature).

## <a name="specify-how-data-operations-are-authorized"></a>Opgeven hoe gegevens bewerkingen worden geautoriseerd

Azure CLI-opdrachten voor het lezen en schrijven van wachtrij gegevens bevatten de optionele `--auth-mode` para meter. Geef deze para meter op om aan te geven hoe een gegevens bewerking moet worden geautoriseerd:

- Stel de `--auth-mode` para meter `login` in om u aan te melden met een Azure AD-beveiligings-principal (aanbevolen).
- Stel de `--auth-mode` para meter in op de verouderde `key` waarde om de toegangs sleutel voor het account op te halen die voor autorisatie moet worden gebruikt. Als u de `--auth-mode` para meter weglaat, probeert de Azure cli ook de toegangs sleutel op te halen.

Als u de `--auth-mode` para meter wilt gebruiken, zorg er dan voor dat u Azure cli v 2.0.46 of hoger hebt geïnstalleerd. Voer uit `az --version` om de geïnstalleerde versie te controleren.

> [!IMPORTANT]
> Als u de `--auth-mode` para meter weglaat of instelt op `key` , probeert de Azure cli de toegangs sleutel voor het account voor autorisatie te gebruiken. In dit geval raadt micro soft u aan de toegangs sleutel op te geven op de opdracht of in de `AZURE_STORAGE_KEY` omgevings variabele. Zie de sectie [omgevings variabelen instellen voor autorisatie parameters](#set-environment-variables-for-authorization-parameters)voor meer informatie over omgevings variabelen.
>
> Als u de toegangs sleutel niet opgeeft, probeert de Azure CLI de Azure Storage Resource provider aan te roepen om deze voor elke bewerking op te halen. Het uitvoeren van veel gegevens bewerkingen die een aanroep van de resource provider vereisen, kan leiden tot vertraging. Zie [schaalbaarheids-en prestatie doelen voor de resource provider van Azure Storage](../common/scalability-targets-resource-provider.md)voor meer informatie over limieten voor resource providers.

## <a name="authorize-with-azure-ad-credentials"></a>Autoriseren met Azure AD-referenties

Wanneer u zich aanmeldt bij Azure CLI met Azure AD-referenties, wordt een OAuth 2,0-toegangs token geretourneerd. Dit token wordt automatisch door Azure CLI gebruikt voor het autoriseren van volgende gegevens bewerkingen op Blob Storage of Queue Storage. Voor ondersteunde bewerkingen hoeft u geen account sleutel of SAS-token meer door te geven met de opdracht.

U kunt machtigingen voor de wachtrij gegevens toewijzen aan een Azure AD-beveiligings-principal via Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Zie [Manage access rights to Azure Storage Data with Azure RBAC](../common/storage-auth-aad-rbac-portal.md)(Engelstalig) voor meer informatie over Azure-rollen in azure Storage.

### <a name="permissions-for-calling-data-operations"></a>Machtigingen voor het aanroepen van gegevens bewerkingen

De Azure Storage-extensies worden ondersteund voor bewerkingen op wachtrij gegevens. Welke bewerkingen u kunt aanroepen, is afhankelijk van de machtigingen die zijn verleend aan de Azure AD-beveiligings-principal waarmee u zich aanmeldt bij Azure CLI. Machtigingen voor wacht rijen worden toegewezen via Azure RBAC. Als u bijvoorbeeld de rol **gegevens lezer van opslag wachtrij** hebt toegewezen, kunt u script opdrachten uitvoeren die gegevens uit een wachtrij lezen. Als u de rol **gegevens Inzender voor de opslag wachtrij** hebt toegewezen, kunt u script opdrachten uitvoeren waarmee een wachtrij of de gegevens die ze bevatten, worden gelezen, geschreven of verwijderd.

Zie [Storage-bewerkingen aanroepen met OAuth-tokens](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)voor meer informatie over de vereiste machtigingen voor elke Azure Storage bewerking in een wachtrij.  

### <a name="example-authorize-an-operation-to-create-a-queue-with-azure-ad-credentials"></a>Voor beeld: een bewerking voor het maken van een wachtrij met Azure AD-referenties autoriseren

In het volgende voor beeld ziet u hoe u een wachtrij maakt vanuit Azure CLI met behulp van uw Azure AD-referenties. Als u de wachtrij wilt maken, moet u zich aanmelden bij de Azure CLI en hebt u een resource groep en een opslag account nodig.

1. Voordat u de wachtrij maakt, moet u de rol van [BLOB voor gegevens opslag](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) aan uzelf toewijzen. Hoewel u de eigenaar van het account bent, hebt u expliciete machtigingen nodig om gegevens bewerkingen uit te voeren op het opslag account. Zie voor meer informatie over het toewijzen van Azure-functies [de Azure Portal gebruiken om een Azure-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > Het kan enkele minuten duren voordat Azure-roltoewijzingen worden doorgegeven.

1. Roep de [`az storage queue create`](/cli/azure/storage/queue#az-storage-queue-create) opdracht met de `--auth-mode` para meter ingesteld op `login` om de wachtrij te maken met uw Azure AD-referenties. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

    ```azurecli
    az storage queue create \
        --account-name <storage-account> \
        --name sample-queue \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autoriseren met de toegangs sleutel voor het account

Als u beschikt over de account sleutel, kunt u een Azure Storage gegevens bewerking aanroepen. Over het algemeen is het gebruik van de account sleutel minder veilig. Als de account sleutel is aangetast, is het mogelijk dat alle gegevens in uw account worden aangetast.

In het volgende voor beeld ziet u hoe u een wachtrij maakt met behulp van de toegangs sleutel voor het account. Geef de account sleutel op en geef de `--auth-mode` para meter op met de `key` waarde:

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Autoriseren met een SAS-token

Als u beschikt over een SAS-token, kunt u gegevens bewerkingen aanroepen die zijn toegestaan door de SAS. In het volgende voor beeld ziet u hoe u een wachtrij maakt met behulp van een SAS-token:

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Omgevings variabelen instellen voor autorisatie parameters

U kunt autorisatie parameters opgeven in omgevings variabelen om te voor komen dat ze worden opgenomen in elke aanroep van een Azure Storage gegevens bewerking. De volgende tabel beschrijft de beschik bare omgevings variabelen.

| Omgevingsvariabele | Beschrijving |
|--|--|
| **AZURE_STORAGE_ACCOUNT** | De naam van het opslagaccount. Deze variabele moet worden gebruikt in combi natie met de sleutel van het opslag account of een SAS-token. Als er geen van beide aanwezig is, probeert de Azure CLI de toegangs sleutel voor het opslag account op te halen met behulp van het geverifieerde Azure AD-account. Als een groot aantal opdrachten tegelijk wordt uitgevoerd, is het mogelijk dat de Azure Storage Resource provider beperkings limiet is bereikt. Zie [schaalbaarheids-en prestatie doelen voor de resource provider van Azure Storage](../common/scalability-targets-resource-provider.md)voor meer informatie over limieten voor resource providers. |
| **AZURE_STORAGE_KEY** | De opslagaccountsleutel. Deze variabele moet worden gebruikt in combi natie met de naam van het opslag account. |
| **AZURE_STORAGE_CONNECTION_STRING** | Een connection string die de sleutel voor het opslag account of een SAS-token bevat. Deze variabele moet worden gebruikt in combi natie met de naam van het opslag account. |
| **AZURE_STORAGE_SAS_TOKEN** | Een SAS-token (Shared Access Signature). Deze variabele moet worden gebruikt in combi natie met de naam van het opslag account. |
| **AZURE_STORAGE_AUTH_MODE** | De autorisatie modus waarmee de opdracht moet worden uitgevoerd. Toegestane waarden zijn `login` (aanbevolen) of `key` . Als u opgeeft `login` , gebruikt Azure cli uw Azure AD-referenties om de gegevens bewerking te autoriseren. Als u de legacy `key` -modus opgeeft, probeert de Azure cli een query uit te voeren voor de toegangs sleutel van het account en de opdracht met de sleutel te autoriseren. |

## <a name="next-steps"></a>Volgende stappen

- [Azure CLI gebruiken om een Azure-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens](../common/storage-auth-aad-rbac-cli.md)
- [Toegang tot Blob-en wachtrij gegevens toestaan met beheerde identiteiten voor Azure-resources](../common/storage-auth-aad-msi.md)
