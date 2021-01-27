---
title: Beheerde identiteit in de taak ACR
description: Een beheerde identiteit inschakelen voor Azure-resources in een Azure Container Registry taak, zodat de taak toegang kan krijgen tot andere Azure-resources, waaronder andere persoonlijke container registers.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: 8f2749a18a5ac6aed0822553d59beaacc9060228
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915944"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Een door Azure beheerde identiteit gebruiken in ACR-taken 

Een [beheerde identiteit inschakelen voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) in een [ACR-taak](container-registry-tasks-overview.md), zodat de taak toegang kan krijgen tot andere Azure-resources, zonder dat hiervoor referenties moeten worden verstrekt of beheerd. Gebruik bijvoorbeeld een beheerde identiteit om een taak stap in te scha kelen om container installatie kopieën naar een ander REGI ster te halen of te pushen.

In dit artikel leert u hoe u de Azure CLI gebruikt om een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit in te scha kelen voor een ACR-taak. U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken. Als u het lokaal wilt gebruiken, is versie 2.0.68 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Voor de afbeeldings doeleinden gebruikt u de voor beeld-opdrachten in dit artikel [AZ ACR Task Create][az-acr-task-create] om een basis taak voor het bouwen van een installatie kopie te maken waarmee een beheerde identiteit wordt ingeschakeld. Zie voor voorbeeld scenario's voor toegang tot beveiligde resources van een ACR-taak met behulp van een beheerde identiteit:

* [Verificatie tussen meerdere registers](container-registry-tasks-cross-registry-authentication.md)
* [Toegang krijgen tot externe resources met geheimen die zijn opgeslagen in Azure Key Vault](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Waarom een beheerde identiteit gebruiken?

Een beheerde identiteit voor Azure-resources biedt geselecteerde Azure-Services met een automatisch beheerde identiteit in Azure Active Directory. U kunt een ACR-taak met een beheerde identiteit configureren zodat de taak toegang kan krijgen tot andere beveiligde Azure-resources zonder dat referenties in de taak stappen worden door gegeven.

Beheerde identiteiten zijn van twee typen:

* Door de *gebruiker toegewezen identiteiten*, die u kunt toewijzen aan meerdere resources en die zo lang als gewenst blijven. Door de gebruiker toegewezen identiteiten zijn momenteel beschikbaar als preview-versie.

* Een door het *systeem toegewezen identiteit*, die uniek is voor een specifieke resource, zoals een ACR-taak en de laatste tijd voor de levens duur van die resource.

U kunt een of beide typen identiteiten inschakelen in een ACR-taak. Verleen de identiteit toegang tot een andere resource, net zoals elke beveiligingsprincipal. Wanneer de taak wordt uitgevoerd, wordt de identiteit gebruikt voor toegang tot de resource in elke taak stap waarvoor toegang is vereist.

## <a name="steps-to-use-a-managed-identity"></a>Stappen voor het gebruik van een beheerde identiteit

Volg deze stappen op hoog niveau om een beheerde identiteit met een ACR-taak te gebruiken.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (optioneel) een door de gebruiker toegewezen identiteit maken

Als u van plan bent een door de gebruiker toegewezen identiteit te gebruiken, gebruikt u een bestaande identiteit of maakt u de identiteit met behulp van de Azure CLI-of andere Azure-hulpprogram ma's. Gebruik bijvoorbeeld de opdracht [AZ Identity Create][az-identity-create] . 

Als u van plan bent om alleen een door het systeem toegewezen identiteit te gebruiken, slaat u deze stap over. Wanneer u de ACR-taak maakt, maakt u een door het systeem toegewezen identiteit.

### <a name="2-enable-identity-on-an-acr-task"></a>2. identiteit inschakelen voor een ACR-taak

Wanneer u een ACR-taak maakt, kunt optioneel een door de gebruiker toegewezen identiteit, een door het systeem toegewezen identiteit of beide inschakelen. Geef bijvoorbeeld de `--assign-identity` para meter door als u de opdracht [AZ ACR Task Create][az-acr-task-create] uitvoert in de Azure cli.

Om een door het systeem toegewezen identiteit in te scha kelen, `--assign-identity` zonder waarde of `assign-identity [system]` . Met de volgende voorbeeld opdracht wordt een Linux-taak gemaakt vanuit een open bare GitHub-opslag plaats die de `hello-world` installatie kopie bouwt en een door het systeem toegewezen beheerde identiteit mogelijk maakt:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Als u een door de gebruiker toegewezen identiteit wilt inschakelen, geeft u `--assign-identity` een waarde op voor de *resource-id* van de identiteit. Met de volgende voorbeeld opdracht wordt een Linux-taak gemaakt vanuit een open bare GitHub-opslag plaats die de `hello-world` installatie kopie bouwt en een door de gebruiker toegewezen beheerde identiteit maakt:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

U kunt de resource-ID van de identiteit ophalen door de opdracht [AZ Identity show][az-identity-show] uit te voeren. De resource-ID voor de ID *myUserAssignedIdentity* in de resource groep *myResourceGroup* heeft de volgende vorm: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Ken de identiteits machtigingen toe om toegang te krijgen tot andere Azure-resources

Geef, afhankelijk van de vereisten van uw taak, de identiteits machtigingen voor toegang tot andere Azure-resources. Enkele voorbeelden:

* Wijs de beheerde identiteit een rol toe met pull, push en pull of andere machtigingen voor een doel container register in Azure. Zie [Azure container Registry rollen en machtigingen](container-registry-roles.md)voor een volledige lijst met register rollen. 
* Wijs de beheerde identiteit een rol toe voor het lezen van geheimen in een Azure-sleutel kluis.

Gebruik de [Azure cli](../role-based-access-control/role-assignments-cli.md) -of andere Azure-hulpprogram ma's om op rollen gebaseerde toegang tot resources te beheren. Voer bijvoorbeeld de opdracht [AZ Role Assignment Create][az-role-assignment-create] uit om de identiteit van een rol toe te wijzen aan de resource. 

In het volgende voor beeld wordt een beheerde identiteit toegewezen om uit een container register te halen. Met de opdracht wordt de *Principal-id* van de taak identiteit en de *resource-id* van het doel register opgegeven.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (optioneel) referenties toevoegen aan de taak

Als uw taak referenties nodig heeft om installatie kopieën naar een ander aangepast REGI ster te halen of te pushen of om toegang te krijgen tot andere bronnen, voegt u referenties toe aan de taak. Voer de opdracht [AZ ACR taak Credential add][az-acr-task-credential-add] uit om referenties toe te voegen en geef de `--use-identity` para meter door om aan te geven dat de identiteit toegang heeft tot de referenties. 

Als u bijvoorbeeld referenties wilt toevoegen voor een door het systeem toegewezen identiteit voor verificatie met Azure container Registry *targetregistry*, geeft u het volgende door `use-identity [system]` :

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Als u referenties wilt toevoegen voor een door de gebruiker toegewezen identiteit voor verificatie met de register *targetregistry*, geeft u `use-identity` een waarde van de *client-id* van de identiteit door. Bijvoorbeeld:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

U kunt de client-ID van de identiteit ophalen door de opdracht [AZ Identity show][az-identity-show] uit te voeren. De client-ID is een GUID van het formulier `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` .

### <a name="5-run-the-task"></a>5. Voer de taak uit

Voer de taak uit nadat u een taak hebt geconfigureerd met een beheerde identiteit. Als u bijvoorbeeld een van de taken wilt testen die in dit artikel zijn gemaakt, moet u deze hand matig activeren met de opdracht [AZ ACR Task run][az-acr-task-run] . Als u extra, geautomatiseerde taak triggers hebt geconfigureerd, wordt de taak uitgevoerd wanneer deze automatisch wordt geactiveerd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit kunt inschakelen en gebruiken voor een ACR-taak. Zie voor scenario's voor toegang tot beveiligde resources van een ACR-taak met behulp van een beheerde identiteit:

* [Verificatie tussen meerdere registers](container-registry-tasks-cross-registry-authentication.md)
* [Toegang krijgen tot externe resources met geheimen die zijn opgeslagen in Azure Key Vault](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
