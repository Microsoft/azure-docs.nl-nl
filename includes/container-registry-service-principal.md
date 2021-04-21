---
title: bestand opnemen
description: bestand opnemen
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 12/14/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 592f62eaf1627733f8cf20460b57e3f474f17963
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800148"
---
## <a name="create-a-service-principal"></a>Een service-principal maken

Als u een service-principal wilt maken met toegang [](../articles/cloud-shell/overview.md) tot uw containerregister, moet u het volgende script uitvoeren in de Azure Cloud Shell of een lokale installatie van [de Azure CLI.](/cli/azure/install-azure-cli) Het script is opgemaakt voor de Bash-shell.

Voordat u het script gaat uitvoeren, moet `ACR_NAME` u de variabele bijwerken met de naam van het containerregister. De `SERVICE_PRINCIPAL_NAME` waarde moet uniek zijn binnen uw Azure Active Directory tenant. Als u een foutmelding `'http://acr-service-principal' already exists.` '' krijgt, geeft u een andere naam op voor de service-principal.

U kunt eventueel de waarde wijzigen in de `--role` opdracht az ad sp [create-for-rbac][az-ad-sp-create-for-rbac] als u verschillende machtigingen wilt verlenen. Zie [ACR-rollen](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md)en -machtigingen voor een volledige lijst met rollen.

Nadat u het script hebt uitgevoerd, noteer dan de id en het wachtwoord van **de** **service-principal.** Zodra u de referenties hebt, kunt u uw toepassingen en services configureren voor verificatie bij uw containerregister als de service-principal.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Een bestaande service-principal gebruiken

Als u registertoegang wilt verlenen aan een bestaande service-principal, moet u een nieuwe rol toewijzen aan de service-principal. Net als bij het maken van een nieuwe service-principal kunt u onder andere pull-, push- en pull-toegang en eigenaarstoegang verlenen.

In het volgende script wordt de [opdracht az role assignment create][az-role-assignment-create] gebruikt om *pull-machtigingen* te verlenen aan een service-principal die u in de variabele `SERVICE_PRINCIPAL_ID` opgeeft. Pas de `--role` waarde aan als u een ander toegangsniveau wilt verlenen.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
