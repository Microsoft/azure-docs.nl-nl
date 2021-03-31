---
title: Shared Acces Signature-tokens in code ophalen | Azure Key Vault
description: De functie voor beheerde opslagaccounts biedt een naadloze integratie tussen Azure Key Vault en een Azure-opslagaccount. In dit voorbeeld wordt de Azure SDK voor .NET gebruikt om SAS-tokens te beheren.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a0202c5259ccebedf03ade217f57b6305b9fa1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94444925"
---
# <a name="create-sas-definition-and-fetch-shared-access-signature-tokens-in-code"></a>SAS-definitie maken en gedeelde toegangstokens ophalen in code

U kunt uw opslagaccount met SAS-tokens (Shared Access Signature) beheren die zijn opgeslagen in uw sleutelkluis. Zie [Beperkte toegang verlenen tot Azure Storage-resources via SAS](../../storage/common/storage-sas-overview.md) voor meer informatie.

> [!NOTE]
> U kunt het beste [Op rollen gebaseerd toegangsbeheer (Azure RBAC)](../../storage/common/storage-auth-aad.md) gebruiken om uw opslagaccount te beveiligen voor betere beveiliging en het gebruiksgemak van gedeelde-sleutelverificatie.

In dit artikel vindt u voorbeelden van .NET-code waarmee een SAS-definitie wordt gemaakt en SAS-tokens worden opgehaald. Bekijk ons [ShareLink](/samples/azure/azure-sdk-for-net/share-link/)-voorbeeld voor volledige informatie, inclusief de gegenereerde client voor met Key Vault beheerde opslagaccounts. Voor informatie over het maken en opslaan van SAS-tokens, raadpleegt u [Sleutels voor opslagaccounts beheren met Key Vault en de Azure CLI](overview-storage-keys.md) of [Sleutels voor opslagaccounts beheren met Key Vault en Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Codevoorbeelden

In het volgende voorbeeld maken we een SAS-sjabloon:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

Met deze sjabloon kunnen we een SAS-definitie maken met behulp van 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

Nadat de SAS-definitie is gemaakt, kunt u SAS-tokens zoals geheimen ophalen met behulp van een `SecretClient`. De geheime naam moet vooraf worden gegaan door de naam van het opslagaccount, gevolgd door een streepje:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

Als uw handtekeningtoken voor gedeelde toegang bijna is verlopen, kunt u hetzelfde geheim nogmaals ophalen om een nieuw token te genereren.

Voor meer informatie over hoe u SAS-tokens uit de Key Vault gebruikt om Azure Storage-services te openen, raadpleegt u [Een account-SAS gebruiken voor toegang tot de Blob service](../../storage/common/storage-account-sas-create-dotnet.md#use-an-account-sas-from-a-client)

> [!NOTE]
> Uw app moet worden voorbereid om de SAS te vernieuwen als deze een 403 vanuit Storage ontvangt, zodat u een inbreuk op een sleutel kunt afhandelen waardoor een snellere rotatie nodig is dan de normale rotatieperiode. 

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Beperkte toegang verlenen tot Azure Storage-resources via SAS](../../storage/common/storage-sas-overview.md).
- Meer informatie over [Sleutels voor opslagaccounts beheren met Key Vault en de Azure CLI](overview-storage-keys.md) of [Azure PowerShell](overview-storage-keys-powershell.md).
- Zie [Voorbeelden van sleutels voor beheerde opslagaccounts](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)