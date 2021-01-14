---
title: Door de gebruiker toegewezen beheerde identiteit beheren-Azure CLI-Azure AD
description: Stapsgewijze instructies voor het maken, weer geven en verwijderen van een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure CLI.
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
ms.openlocfilehash: 68f305156645d69049519cd383f06b28ab9b5e03
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184863"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Een door de gebruiker toegewezen beheerde identiteit maken, weer geven of verwijderen met behulp van de Azure CLI


Beheerde identiteiten voor Azure-resources bieden Azure-Services met een beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij services die ondersteuning bieden voor Azure AD-verificatie, zonder dat er referenties in uw code nodig zijn. 

In dit artikel leert u hoe u een door de gebruiker toegewezen beheerde identiteit kunt maken, weer geven en verwijderen met behulp van Azure CLI.

Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="prerequisites"></a>Vereisten

- Zie [Wat zijn beheerde identiteiten voor Azure-resources?](overview.md) als u niet bekend met beheerde identiteiten voor Azure-resources. Zie [Beheerde identiteitstypen](overview.md#managed-identity-types) voor meer informatie over door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteitstypen.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!NOTE]   
> Als u de gebruikers machtigingen wilt wijzigen wanneer u een app Service-Principal gebruikt met CLI, moet u de Service-Principal extra machtigingen opgeven in azure AD Graph API als delen van CLI GET-aanvragen uitvoeren voor de Graph API. Als dat niet het geval is, kunt u het bericht ' onvoldoende bevoegdheden om de bewerking te volt ooien ' ontvangen. Als u dit wilt doen, gaat u naar de registratie van de app in Azure Active Directory, selecteert u uw app, klikt u op API-machtigingen, schuift u omlaag en selecteert u Azure Active Directory Graph. Van daaruit selecteert u toepassings machtigingen en voegt u vervolgens de juiste machtigingen toe. 

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken 

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, moet uw account beschikken over de rol toewijzing [beheerde identiteits bijdrage](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Gebruik de opdracht [AZ Identity Create](/cli/azure/identity#az-identity-create) om een door de gebruiker toegewezen beheerde identiteit te maken. Met de `-g` para meter wordt de resource groep opgegeven waarin de door de gebruiker toegewezen beheerde identiteit moet worden gemaakt `-n` . de para meter geeft de naam op. Vervang de `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` para meters en door uw eigen waarden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Door de gebruiker toegewezen beheerde identiteiten weer geven

Als u een door de gebruiker toegewezen beheerde identiteit wilt weer geven/lezen, moet uw account beschikken over de rol [Managed Identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) of [Managed id contributor](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Als u door de gebruiker toegewezen beheerde identiteiten wilt weer geven, gebruikt u de opdracht [AZ ID List](/cli/azure/identity#az-identity-list) . Vervang de `<RESOURCE GROUP>` door uw eigen waarde:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

In het JSON-antwoord hebben door de gebruiker toegewezen beheerde identiteiten een `"Microsoft.ManagedIdentity/userAssignedIdentities"` waarde geretourneerd voor de sleutel, `type` .

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, moet uw account beschikken over de rol toewijzing [beheerde identiteits bijdrage](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, gebruikt u de opdracht [AZ Identity delete](/cli/azure/identity#az-identity-delete) .  De para meter-n specificeert de naam en de-g para meter geeft de resource groep op waarin de door de gebruiker toegewezen beheerde identiteit is gemaakt. Vervang de `<USER ASSIGNED IDENTITY NAME>` `<RESOURCE GROUP>` para meters en waarden door uw eigen waarden:

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Als u een door de gebruiker toegewezen beheerde identiteit verwijdert, wordt de verwijzing niet verwijderd uit een resource waaraan deze is toegewezen. Verwijder deze uit de VM-VMSS met behulp van de `az vm/vmss identity remove` opdracht

## <a name="next-steps"></a>Volgende stappen

Zie [AZ Identity](/cli/azure/identity)voor een volledige lijst met Azure cli-identiteits opdrachten.

Voor informatie over het toewijzen van een door de gebruiker toegewezen beheerde identiteit aan een Azure-VM raadpleegt u [beheerde identiteiten voor Azure-resources configureren op een virtuele Azure-machine met behulp van Azure cli](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)
