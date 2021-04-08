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
ms.openlocfilehash: f2d2b655e80f5b9694fb1948b136aac918312ca9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102245088"
---
## <a name="create-a-service-principal"></a>Een service-principal maken

Als u een service-principal met toegang tot uw container register wilt maken, voert u het volgende script uit in de [Azure Cloud shell](../articles/cloud-shell/overview.md) of een lokale installatie van de [Azure cli](/cli/azure/install-azure-cli). Het script is geformatteerd voor de bash-shell.

Voordat u het script uitvoert, moet `ACR_NAME` u de variabele bijwerken met de naam van het container register. De `SERVICE_PRINCIPAL_NAME` waarde moet uniek zijn binnen uw Azure Active Directory-Tenant. Als er een fout bericht wordt weer gegeven `'http://acr-service-principal' already exists.` , geeft u een andere naam op voor de Service-Principal.

U kunt desgewenst de waarde wijzigen `--role` in de opdracht [AZ AD SP create-for-RBAC][az-ad-sp-create-for-rbac] als u andere machtigingen wilt verlenen. Zie [ACR-rollen en-machtigingen](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md)voor een volledige lijst met rollen.

Nadat u het script hebt uitgevoerd, noteert u de id en het **wacht woord** van de service **-** principal. Wanneer u de referenties hebt, kunt u uw toepassingen en services zo configureren dat deze bij het container register worden geverifieerd als de Service-Principal.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Een bestaande Service-Principal gebruiken

Als u register toegang tot een bestaande Service-Principal wilt verlenen, moet u een nieuwe rol toewijzen aan de Service-Principal. Net als bij het maken van een nieuwe Service-Principal kunt u onder andere pull-, push-en pull-en eigendoms toegang verlenen.

Het volgende script maakt gebruik van de opdracht [AZ Role Assignment][az-role-assignment-create] om *pull* -machtigingen te verlenen aan een service-principal die u opgeeft in de `SERVICE_PRINCIPAL_ID` variabele. Pas de `--role` waarde aan als u een ander toegangs niveau wilt verlenen.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
