---
title: Kiezen hoe u toegang tot wachtrijgegevens autor wilt toestaan met Azure CLI
titleSuffix: Azure Storage
description: Geef op hoe u gegevensbewerkingen voor wachtrijgegevens autoreert met de Azure CLI. U kunt gegevensbewerkingen autor toestemming geven met behulp van Azure AD-referenties, met de toegangssleutel voor het account of met een SAS-token (Shared Access Signature).
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozgun
ms.date: 02/10/2021
ms.topic: how-to
ms.service: storage
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b3ac012da97194134f58d061dd9d84e945db554
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774463"
---
# <a name="choose-how-to-authorize-access-to-queue-data-with-azure-cli"></a>Kiezen hoe u toegang tot wachtrijgegevens autor wilt toestaan met Azure CLI

Azure Storage biedt extensies voor Azure CLI waarmee u kunt opgeven hoe u bewerkingen voor wachtrijgegevens wilt autoreren. U kunt gegevensbewerkingen op de volgende manieren autor toestemming geven:

- Met een Azure Active Directory (Azure AD)-beveiligingsprincipaal. Microsoft raadt u aan Azure AD-referenties te gebruiken voor betere beveiliging en gebruiksgemak.
- Met de toegangssleutel voor het account of een SAS-token (Shared Access Signature).

## <a name="specify-how-data-operations-are-authorized"></a>Opgeven hoe gegevensbewerkingen worden geautoriseerd

Azure CLI-opdrachten voor het lezen en schrijven van wachtrijgegevens bevatten de optionele `--auth-mode` parameter . Geef deze parameter op om aan te geven hoe een gegevensbewerking moet worden geautoriseerd:

- Stel de `--auth-mode` parameter in op om u aan te melden met behulp van een Azure `login` AD-beveiligingsprincipaal (aanbevolen).
- Stel de parameter in op de verouderde waarde om de toegangssleutel voor het account op `--auth-mode` te halen voor `key` autorisatie. Als u de `--auth-mode` parameter weglaten, probeert de Azure CLI ook de toegangssleutel op te halen.

Als u de parameter wilt gebruiken, moet u ervoor zorgen dat `--auth-mode` u Azure CLI v2.0.46 of hoger hebt geïnstalleerd. Voer `az --version` uit om de geïnstalleerde versie te controleren.

> [!NOTE]
> Wanneer een opslagaccount is vergrendeld met een Azure Resource Manager [](/rest/api/storagerp/storageaccounts/listkeys) alleen-lezenvergrendeling, is de bewerking Sleutels opsloten niet toegestaan voor dat opslagaccount.  **List Keys** is een POST-bewerking en alle POST-bewerkingen worden voorkomen wanneer een **ReadOnly-vergrendeling** is geconfigureerd voor het account. Daarom moeten gebruikers die de accountsleutels nog niet hebben, Azure AD-referenties gebruiken om toegang te krijgen tot wachtrijgegevens wanneer het account is vergrendeld met een **ReadOnly-vergrendeling.**

> [!IMPORTANT]
> Als u de parameter weggeeft of in stelt op , probeert de Azure CLI de toegangssleutel voor het account te gebruiken `--auth-mode` `key` voor autorisatie. In dit geval raadt Microsoft u aan de toegangssleutel op te geven via de opdracht of in de `AZURE_STORAGE_KEY` omgevingsvariabele. Zie de sectie Omgevingsvariabelen instellen voor autorisatieparameters voor meer informatie over [omgevingsvariabelen.](#set-environment-variables-for-authorization-parameters)
>
> Als u de toegangssleutel niet op geeft, probeert de Azure CLI de resourceprovider Azure Storage op te halen voor elke bewerking. Het uitvoeren van veel gegevensbewerkingen waarvoor een aanroep van de resourceprovider is vereist, kan leiden tot een beperking. Zie Schaalbaarheids- en prestatiedoelen voor de resourceprovider Azure Storage meer informatie over [resourceproviderlimieten.](../common/scalability-targets-resource-provider.md)

## <a name="authorize-with-azure-ad-credentials"></a>Autor maken met Azure AD-referenties

Wanneer u zich met Azure AD-referenties bij Azure CLI aanmeldt, wordt een OAuth 2.0-toegangsteken geretourneerd. Dat token wordt automatisch door Azure CLI gebruikt om volgende gegevensbewerkingen te autoreren voor Queue Storage. Voor ondersteunde bewerkingen hoeft u geen accountsleutel of SAS-token meer door te geven met de opdracht .

U kunt machtigingen voor het in de wachtrij plaatsen van gegevens toewijzen aan een Azure AD-beveiligingsprincipaal via op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC). Zie Toegangsrechten beheren voor Azure Storage gegevens met [Azure RBAC](../common/storage-auth-aad-rbac-portal.md)voor meer informatie over Azure-rollen in Azure Storage.

### <a name="permissions-for-calling-data-operations"></a>Machtigingen voor het aanroepen van gegevensbewerkingen

De Azure Storage worden ondersteund voor bewerkingen op wachtrijgegevens. Welke bewerkingen u kunt aanroepen, is afhankelijk van de machtigingen die zijn verleend aan de Azure AD-beveiligingsprincipaal waarmee u zich bij Azure CLI kunt aanmelden. Machtigingen voor wachtrijen worden toegewezen via Azure RBAC. Als u bijvoorbeeld de rol  Gegevenslezer opslagwachtrij hebt toegewezen, kunt u scriptopdrachten uitvoeren die gegevens uit een wachtrij lezen. Als de rol  Inzender voor opslagwachtrijgegevens aan u is toegewezen, kunt u scriptopdrachten uitvoeren voor het lezen, schrijven of verwijderen van een wachtrij of de gegevens die ze bevatten.

Zie Opslagbewerkingen aanroepen met [OAuth-tokens](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)voor meer informatie over de machtigingen die zijn vereist voor elke Azure Storage bewerking in een wachtrij.  

### <a name="example-authorize-an-operation-to-create-a-queue-with-azure-ad-credentials"></a>Voorbeeld: Een bewerking voor het maken van een wachtrij met Azure AD-referenties machtigen

In het volgende voorbeeld ziet u hoe u een wachtrij maakt vanuit Azure CLI met behulp van uw Azure AD-referenties. Als u de wachtrij wilt maken, moet u zich aanmelden bij de Azure CLI en hebt u een resourcegroep en een opslagaccount nodig.

1. Voordat u de wachtrij maakt, moet u de rol [Inzender voor opslagwachtrijgegevens](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) aan uzelf toewijzen. Hoewel u de accounteigenaar bent, hebt u expliciete machtigingen nodig om gegevensbewerkingen uit te voeren op het opslagaccount. Zie Use the Azure Portal to assign an Azure role for access to blob and queue data (De azure-rol gebruiken om een Azure-rol toe te wijzen voor toegang [tot blob- en wachtrijgegevens)](../common/storage-auth-aad-rbac-portal.md)voor meer informatie over het toewijzen van Azure-rollen.

    > [!IMPORTANT]
    > Het kan enkele minuten duren voordat Azure-roltoewijzingen worden doorgegeven.

1. Roep de [`az storage queue create`](/cli/azure/storage/queue#az_storage_queue_create) opdracht aan met de parameter ingesteld op om de wachtrij te maken met uw Azure `--auth-mode` `login` AD-referenties. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

    ```azurecli
    az storage queue create \
        --account-name <storage-account> \
        --name sample-queue \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autor maken met de toegangssleutel van het account

Als u de accountsleutel hebt, kunt u elke Azure Storage aanroepen. Over het algemeen is het gebruik van de accountsleutel minder veilig. Als de accountsleutel is gecompromitteerd, kunnen alle gegevens in uw account worden aangetast.

In het volgende voorbeeld ziet u hoe u een wachtrij maakt met behulp van de toegangssleutel voor het account. Geef de accountsleutel op en geef de `--auth-mode` parameter op met de waarde `key` :

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Autor toestemming geven met een SAS-token

Als u een SAS-token hebt, kunt u gegevensbewerkingen aanroepen die zijn toegestaan door de SAS. In het volgende voorbeeld ziet u hoe u een wachtrij maakt met behulp van een SAS-token:

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Omgevingsvariabelen instellen voor autorisatieparameters

U kunt autorisatieparameters opgeven in omgevingsvariabelen om te voorkomen dat ze bij elke aanroep van een Azure Storage worden op te nemen. In de volgende tabel worden de beschikbare omgevingsvariabelen beschreven.

| Omgevingsvariabele | Description |
|--|--|
| **AZURE_STORAGE_ACCOUNT** | De naam van het opslagaccount. Deze variabele moet worden gebruikt in combinatie met de sleutel van het opslagaccount of een SAS-token. Als geen van beide aanwezig is, probeert de Azure CLI de toegangssleutel voor het opslagaccount op te halen met behulp van het geverifieerde Azure AD-account. Als een groot aantal opdrachten tegelijk wordt uitgevoerd, kan Azure Storage beperkingslimiet van de resourceprovider worden bereikt. Zie Schaalbaarheids- en prestatiedoelen voor de resourceprovider Azure Storage meer informatie over [resourceproviderlimieten.](../common/scalability-targets-resource-provider.md) |
| **AZURE_STORAGE_KEY** | De opslagaccountsleutel. Deze variabele moet worden gebruikt in combinatie met de naam van het opslagaccount. |
| **AZURE_STORAGE_CONNECTION_STRING** | Een connection string die de sleutel van het opslagaccount of een SAS-token bevat. Deze variabele moet worden gebruikt in combinatie met de naam van het opslagaccount. |
| **AZURE_STORAGE_SAS_TOKEN** | Een SAS-token (Shared Access Signature). Deze variabele moet worden gebruikt in combinatie met de naam van het opslagaccount. |
| **AZURE_STORAGE_AUTH_MODE** | De autorisatiemodus waarmee de opdracht moet worden uitgevoerd. Toegestane waarden zijn `login` (aanbevolen) of `key` . Als u `login` opgeeft, gebruikt de Azure CLI uw Azure AD-referenties om de gegevensbewerking te autoreren. Als u de verouderde modus opgeeft, probeert de Azure CLI de toegangssleutel van het account op te vragen en de opdracht met de `key` sleutel te autoreren. |

## <a name="next-steps"></a>Volgende stappen

- [Azure CLI gebruiken om een Azure-rol toe te wijzen voor toegang tot blob- en wachtrijgegevens](../common/storage-auth-aad-rbac-cli.md)
- [Toegang verlenen tot blob- en wachtrijgegevens met beheerde identiteiten voor Azure-resources](../common/storage-auth-aad-msi.md)
