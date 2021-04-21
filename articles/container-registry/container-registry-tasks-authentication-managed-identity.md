---
title: Beheerde identiteit in ACR-taak
description: Schakel een beheerde identiteit in voor Azure-resources in een Azure Container Registry om de taak toegang te geven tot andere Azure-resources, inclusief andere persoonlijke containerregisters.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: 19d63861a98884ff2f5103946c19e2226c4b14b7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781159"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Een door Azure beheerde identiteit gebruiken in ACR-taken 

Schakel een beheerde identiteit in voor [Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) in een [ACR-taak,](container-registry-tasks-overview.md)zodat de taak toegang heeft tot andere Azure-resources, zonder referenties op te geven of te beheren. Gebruik bijvoorbeeld een beheerde identiteit om een taakstap in te stellen voor het pullen of pushen van container-afbeeldingen naar een ander register.

In dit artikel leert u hoe u de Azure CLI gebruikt om een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit in te stellen voor een ACR-taak. U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken. Als u deze lokaal wilt gebruiken, is versie 2.0.68 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Ter illustratie gebruiken de voorbeeldopdrachten in dit artikel [az acr task create][az-acr-task-create] om een eenvoudige build-taak voor een afbeelding te maken die een beheerde identiteit mogelijk maakt. Zie voor voorbeeldscenario's voor toegang tot beveiligde resources vanuit een ACR-taak met behulp van een beheerde identiteit:

* [Verificatie tussen meerdere registers](container-registry-tasks-cross-registry-authentication.md)
* [Toegang tot externe resources met geheimen die zijn opgeslagen in Azure Key Vault](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Waarom een beheerde identiteit gebruiken?

Een beheerde identiteit voor Azure-resources biedt geselecteerde Azure-services een automatisch beheerde identiteit in Azure Active Directory. U kunt een ACR-taak configureren met een beheerde identiteit, zodat de taak toegang heeft tot andere beveiligde Azure-resources, zonder referenties door te geven in de taakstappen.

Beheerde identiteiten zijn van twee typen:

* *Door de gebruiker toegewezen identiteiten,* die u aan meerdere resources kunt toewijzen en zo lang als u wilt kunt blijven gebruiken. Door de gebruiker toegewezen identiteiten zijn momenteel beschikbaar als preview-versie.

* Een *door het systeem toegewezen identiteit,* die uniek is voor een specifieke resource, zoals een ACR-taak, en die de levensduur van die resource begaat.

U kunt een of beide typen identiteiten inschakelen in een ACR-taak. Verleen de identiteit toegang tot een andere resource, net als elke beveiligingsprincipaal. Wanneer de taak wordt uitgevoerd, wordt de identiteit gebruikt voor toegang tot de resource in taakstappen waarvoor toegang is vereist.

## <a name="steps-to-use-a-managed-identity"></a>Stappen voor het gebruik van een beheerde identiteit

Volg deze stappen op hoog niveau om een beheerde identiteit te gebruiken met een ACR-taak.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (optioneel) Een door de gebruiker toegewezen identiteit maken

Als u van plan bent om een door de gebruiker toegewezen identiteit te gebruiken, gebruikt u een bestaande identiteit of maakt u de identiteit met behulp van de Azure CLI of andere Azure-hulpprogramma's. Gebruik bijvoorbeeld de [opdracht az identity create.][az-identity-create] 

Als u alleen een door het systeem toegewezen identiteit wilt gebruiken, slaat u deze stap over. U maakt een door het systeem toegewezen identiteit wanneer u de ACR-taak maakt.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Identiteit inschakelen voor een ACR-taak

Wanneer u een ACR-taak maakt, kunt u eventueel een door de gebruiker toegewezen identiteit, een door het systeem toegewezen identiteit of beide inschakelen. Geef bijvoorbeeld de `--assign-identity` parameter door wanneer u de opdracht az [acr task create][az-acr-task-create] in de Azure CLI hebt uitgevoerd.

Als u een door het systeem toegewezen identiteit wilt inschakelen, kunt u `--assign-identity` doorgeven zonder waarde of `assign-identity [system]` . Met de volgende voorbeeldopdracht wordt een Linux-taak gemaakt op basis van een openbare GitHub-opslagplaats waarmee de installatie afbeelding wordt gebouwd en een door het systeem `hello-world` toegewezen beheerde identiteit wordt gebruikt:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Als u een door de gebruiker toegewezen identiteit wilt inschakelen, moet u `--assign-identity` doorgeven met de waarde van de *resource-id* van de identiteit. Met de volgende voorbeeldopdracht maakt u een Linux-taak vanuit een openbare GitHub-opslagplaats waarmee de afbeelding wordt gebouwd en een door de gebruiker toegewezen `hello-world` beheerde identiteit wordt gebruikt:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

U kunt de resource-id van de identiteit krijgen door de opdracht [az identity show uit te][az-identity-show] voeren. De resource-id voor de id *myUserAssignedIdentity* in resourcegroep *myResourceGroup* heeft de volgende vorm: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. De identiteit machtigingen verlenen voor toegang tot andere Azure-resources

Afhankelijk van de vereisten van uw taak, verleent u de identiteit machtigingen voor toegang tot andere Azure-resources. Enkele voorbeelden:

* Wijs de beheerde identiteit een rol toe met pull-, push- en pull-machtigingen of andere machtigingen voor een doelcontainerregister in Azure. Zie rollen en machtigingen voor een volledige [lijst Azure Container Registry registerrollen.](container-registry-roles.md) 
* Wijs de beheerde identiteit een rol toe om geheimen in een Azure-sleutelkluis te lezen.

Gebruik de [Azure CLI](../role-based-access-control/role-assignments-cli.md) of andere Azure-hulpprogramma's om op rollen gebaseerde toegang tot resources te beheren. Voer bijvoorbeeld de opdracht [az role assignment create][az-role-assignment-create] uit om de identiteit een rol toe te wijzen aan de resource. 

In het volgende voorbeeld worden aan een beheerde identiteit de machtigingen toegewezen om gegevens op te halen uit een containerregister. Met de opdracht geeft u *de principal-id* van de taakidentiteit en de *resource-id* van het doelregister op.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (Optioneel) Referenties toevoegen aan de taak

Als uw taak referenties nodig heeft voor het pullen of pushen van afbeeldingen naar een ander aangepast register of om toegang te krijgen tot andere resources, voegt u referenties toe aan de taak. Voer de [opdracht az acr task credential add][az-acr-task-credential-add] uit om referenties toe te voegen en geef de parameter door om aan te geven dat de identiteit toegang heeft tot de `--use-identity` referenties. 

Als u bijvoorbeeld referenties voor een door het systeem toegewezen identiteit wilt toevoegen om te verifiëren met de *doelregister* van het Azure-containerregister, geeft u `use-identity [system]` door:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Als u referenties voor een door de gebruiker toegewezen identiteit wilt toevoegen om te verifiëren met de *registerdoelregister,* wordt doorvergeven met een waarde van de `use-identity` *client-id* van de identiteit. Bijvoorbeeld:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

U kunt de client-id van de identiteit op halen door de [opdracht az identity show uit te][az-identity-show] voeren. De client-id is een GUID van de vorm `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` .

### <a name="5-run-the-task"></a>5. De taak uitvoeren

Na het configureren van een taak met een beheerde identiteit, moet u de taak uitvoeren. Als u bijvoorbeeld een van de taken wilt testen die in dit artikel zijn gemaakt, activeert u deze handmatig met de [opdracht az acr task run.][az-acr-task-run] Als u aanvullende, geautomatiseerde taaktriggers hebt geconfigureerd, wordt de taak uitgevoerd wanneer deze automatisch wordt geactiveerd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit kunt inschakelen en gebruiken voor een ACR-taak. Zie voor scenario's voor toegang tot beveiligde resources vanuit een ACR-taak met behulp van een beheerde identiteit:

* [Verificatie tussen meerdere registers](container-registry-tasks-cross-registry-authentication.md)
* [Toegang tot externe resources met geheimen die zijn opgeslagen in Azure Key Vault](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[azure-cli-install]: /cli/azure/install-azure-cli
