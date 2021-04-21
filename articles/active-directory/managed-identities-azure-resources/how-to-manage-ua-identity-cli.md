---
title: Door de gebruiker toegewezen beheerde identiteit beheren - Azure CLI - Azure AD
description: Stapsgewijs instructies voor het maken, opsn en verwijderen van een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure CLI.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: a26f13b71ae96f4d6593cb4a4d9107f8ef6c207c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784871"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Een door de gebruiker toegewezen beheerde identiteit maken, op een lijst zetten of verwijderen met behulp van de Azure CLI


Beheerde identiteiten voor Azure-resources bieden Azure-services een beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken om te verifiëren bij services die Azure AD-verificatie ondersteunen, zonder referenties in uw code te hoeven gebruiken. 

In dit artikel leert u hoe u een door de gebruiker toegewezen beheerde identiteit maakt, vermeldt en verwijdert met behulp van Azure CLI.

Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="prerequisites"></a>Vereisten

- Zie [Wat zijn beheerde identiteiten voor Azure-resources?](overview.md) als u niet bekend met beheerde identiteiten voor Azure-resources. Zie [Beheerde identiteitstypen](overview.md#managed-identity-types) voor meer informatie over door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteitstypen.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!NOTE]   
> Als u gebruikersmachtigingen wilt wijzigen wanneer u een app-service-principal gebruikt met behulp van CLI, moet u de service-principal aanvullende machtigingen in Azure AD Graph API als delen van CLI GET-aanvragen uitvoeren op de Graph API. Anders ontvangt u mogelijk het bericht 'Onvoldoende bevoegdheden om de bewerking te voltooien'. Hiervoor gaat u naar de app-registratie in Azure Active Directory, selecteert u uw app, klikt u op API-machtigingen, schuift u omlaag en selecteert u Azure Active Directory Graph. Selecteer hier Toepassingsmachtigingen en voeg vervolgens de juiste machtigingen toe. 

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken 

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, heeft uw account de roltoewijzing [Inzender voor beheerde](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) identiteit nodig.

Gebruik de [opdracht az identity create](/cli/azure/identity#az_identity_create) om een door de gebruiker toegewezen beheerde identiteit te maken. De parameter geeft de resourcegroep aan waar de door de gebruiker toegewezen beheerde identiteit moet worden gemaakt en de `-g` parameter geeft de naam `-n` op. Vervang de `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` parameterwaarden en door uw eigen waarden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lijst met door de gebruiker toegewezen beheerde identiteiten

Als u een door de gebruiker toegewezen beheerde [](../../role-based-access-control/built-in-roles.md#managed-identity-operator) identiteit wilt zien/lezen, moet uw account de roltoewijzing Operator voor beheerde identiteit of Inzender voor [beheerde](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) identiteit hebben.

Gebruik de opdracht [az identity list](/cli/azure/identity#az_identity_list) om door de gebruiker toegewezen beheerde identiteiten weer te geven. Vervang de `<RESOURCE GROUP>` door uw eigen waarde:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

In het JSON-antwoord hebben door de gebruiker toegewezen beheerde identiteiten `"Microsoft.ManagedIdentity/userAssignedIdentities"` een waarde geretourneerd voor sleutel, `type` .

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, heeft uw account de roltoewijzing Inzender [voor beheerde](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) identiteit nodig.

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, gebruikt u [de opdracht az identity delete.](/cli/azure/identity#az_identity_delete)  De -n parameter geeft u de naam en de -g parameter geeft u de resourcegroep waar de door de gebruiker toegewezen beheerde identiteit is gemaakt. Vervang de `<USER ASSIGNED IDENTITY NAME>` parameters en door uw eigen `<RESOURCE GROUP>` waarden:

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Als u een door de gebruiker toegewezen beheerde identiteit verwijdert, wordt de verwijzing niet verwijderd uit een resource aan wie deze is toegewezen. Verwijder deze uit VM/VMSS met behulp van de `az vm/vmss identity remove` opdracht

## <a name="next-steps"></a>Volgende stappen

Zie az identity voor een volledige lijst met Azure [CLI-identiteitsopdrachten.](/cli/azure/identity)

Zie Beheerde identiteiten configureren voor Azure-resources op een [Azure-VM](qs-configure-cli-windows-vm.md#user-assigned-managed-identity) met behulp van Azure CLI voor meer informatie over het toewijzen van een door de gebruiker toegewezen beheerde identiteit aan een Azure-VM
