---
title: Snelle taak uitvoeren met sjabloon
description: Een ACR-taak in de wachtrij opslaan om een afbeelding te bouwen met behulp van een Azure Resource Manager sjabloon
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: af7bebc311f81bb489fcc8be419f167ff6f9460a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781231"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>Een ACR-taken uitvoeren met Resource Manager sjablonen

[ACR-taken](container-registry-tasks-overview.md) is een reeks functies in Azure Container Registry die u helpen bij het beheren en wijzigen van containerinstallatiekopieën gedurende de levenscyclus van de container. 

In dit artikel Azure Resource Manager voorbeelden van sjabloon om een snelle taakuit te voeren in de wachtrij, vergelijkbaar met de sjabloon die u handmatig kunt maken met [de opdracht az acr build.][az-acr-build]

Een Resource Manager om een taak uit te voeren in de wachtrij is handig in automatiseringsscenario's en breidt de functionaliteit van `az acr build` uit. Bijvoorbeeld:

* Een sjabloon gebruiken om een containerregister te maken en onmiddellijk een taak uit te voeren in de wachtrij om een containerafbeelding te bouwen en te pushen
* Aanvullende resources maken of inschakelen die u kunt gebruiken in een snelle taak, zoals een beheerde identiteit voor Azure-resources

## <a name="limitations"></a>Beperkingen

* U moet een externe context opgeven, zoals een GitHub-opslagplaats als de [bronlocatie](container-registry-tasks-overview.md#context-locations) voor het uitvoeren van uw taak. U kunt geen lokale broncontext gebruiken.
* Voor taak wordt uitgevoerd met behulp van een beheerde identiteit, is alleen een *door de* gebruiker toegewezen beheerde identiteit toegestaan.

## <a name="prerequisites"></a>Vereisten

* **GitHub-account:** maak een account https://github.com op als u er nog geen hebt. 
* **Voorbeeldopslagplaats vorken:** voor de hier weergegeven taakvoorbeelden gebruikt u de GitHub-gebruikersinterface om de volgende voorbeeldopslagplaats te forken in uw GitHub-account: https://github.com/Azure-Samples/acr-build-helloworld-node . Deze repo bevat voorbeeld-Dockerfiles en broncode voor het bouwen van kleine container-afbeeldingen.

## <a name="example-create-registry-and-queue-task-run"></a>Voorbeeld: Een register- en wachtrijtaak uitvoeren

In dit voorbeeld wordt een [voorbeeldsjabloon gebruikt](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild) om een containerregister te maken en een taakrun in de wachtrij te zetten die een afbeelding bouwt en pusht. 

### <a name="template-parameters"></a>Sjabloonparameters

Geef voor dit voorbeeld waarden op voor de volgende sjabloonparameters:

|Parameter  |Waarde  |
|---------|---------|
|registryName     |Unieke naam van het register dat wordt gemaakt         |
|repository     |Doelopslagplaats voor build-taak        |
|taskRunName     |Naam van taakuit voeren, waarmee de tag van de afbeelding wordt opgegeven |
|sourceLocation     |Externe context voor de build-taak, bijvoorbeeld https://github.com/Azure-Samples/acr-build-helloworld-node . Het Dockerfile in de hoofdmap van de repo bouwt een container-afbeelding voor een kleine Node.js web-app. Gebruik desgewenst uw fork van de repo als de buildcontext.         |

### <a name="deploy-the-template"></a>De sjabloon implementeren

Implementeer de sjabloon met de [opdracht az deployment group create.][az-deployment-group-create] In dit voorbeeld wordt de afbeelding *helloworld-node:testrun* gebouwd en naar een register met de naam *mycontainerregistry ge pusht.*

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuild/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=testrun \
    sourceLocation=https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
 ```

Met de vorige opdracht worden de parameters op de opdrachtregel door gegeven. Geef ze desgewenst door in een [parametersbestand](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Implementatie controleren

Nadat de implementatie is voltooid, controleert u of de installatie afbeelding is gemaakt door [az acr repository show-tags uit te stellen:][az-acr-repository-show-tags]

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Uitvoer:

```console
Result
--------
testrun
```

### <a name="view-run-log"></a>Run-logboek weergeven

Als u details over de taak wilt weergeven, bekijkt u het logboek voor de run.

Haal eerst de run-id op [met az acr task list-runs][az-acr-task-list-runs]
```azurecli
az acr task list-runs \
  --registry mycontainerregistry --output table
```

De uitvoer ziet er ongeveer zo uit:

```console
RUN ID    TASK    PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ------  ----------  ---------  ---------  --------------------  ----------
ca1               linux       Succeeded  Manual     2020-03-23T17:54:28Z  00:00:48
```

Voer [az acr task logs uit om][az-acr-task-logs] logboeken voor taakruns voor de run-id te bekijken, in dit geval *ca1*:

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

In de uitvoer ziet u het taakuitvoerlogboek.

U kunt het taakrunlogboek ook weergeven in het Azure Portal. 

1. Navigeer naar uw containerregister
2. Selecteer **onder Services** de optie Taken **Wordt**  >  **uitgevoerd.**
3. Selecteer de run-id, in dit geval *ca1.* 

In de portal wordt het logboek voor de taakuit voer uitgevoerd.

## <a name="example-task-run-with-managed-identity"></a>Voorbeeld: Taak uitvoeren met beheerde identiteit

Gebruik een [voorbeeldsjabloon](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity) om een taak uit te voeren in de wachtrij die een door de gebruiker toegewezen beheerde identiteit mogelijk maakt. Tijdens het uitvoeren van de taak wordt de identiteit geverifieerd om een afbeelding op te halen uit een ander Azure-containerregister. 

Dit scenario is vergelijkbaar met verificatie tussen registers in een [ACR-taak](container-registry-tasks-cross-registry-authentication.md)met behulp van een door Azure beheerde identiteit . Een organisatie kan bijvoorbeeld een gecentraliseerd register onderhouden met basisafbeeldingen die toegankelijk zijn voor meerdere ontwikkelteams.

### <a name="prepare-base-registry"></a>Basisregister voorbereiden

Voor demonstratiedoeleinden maakt u een afzonderlijk containerregister als basisregister en pusht u een Node.js basisafbeelding die is Docker Hub.

1. Maak een tweede containerregister, bijvoorbeeld *mybaseregistry*, om basisafbeeldingen op te slaan.
1. Haal de afbeelding op Docker Hub, tag deze voor uw `node:9-alpine` basisregister en push deze naar het basisregister:

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>Nieuwe Dockerfile maken

Maak een Dockerfile die de basisafbeelding uit uw basisregister haalt. Voer de volgende stappen uit in uw lokale fork van de GitHub-opslagplaats, bijvoorbeeld `https://github.com/myGitHubID/acr-build-helloworld-node.git` .

1. Selecteer in de GitHub-gebruikersinterface **Nieuw bestand maken.**
1. Noem het bestand *Dockerfile-test* en plak de volgende inhoud. Vervang *mybaseregistry door uw registernaam.*
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. Selecteer **Nieuw bestand commit**.

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Identiteit pull-machtigingen geven voor het basisregister

Geef de beheerde identiteit machtigingen om gegevens op te halen uit het basisregister, *mybaseregistry.*

Gebruik de [opdracht az acr show][az-acr-show] om de resource-id van het basisregister op te halen en op te slaan in een variabele:

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

Gebruik de [opdracht az role assignment create][az-role-assignment-create] om de identiteit de rol Acrpull toe te wijzen aan het basisregister. Deze rol heeft alleen machtigingen om afbeeldingen uit het register op te halen.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>Sjabloonparameters

Geef in dit voorbeeld waarden op voor de volgende sjabloonparameters:

|Parameter  |Waarde  |
|---------|---------|
|registryName     |Naam van het register waarin de afbeelding is gebouwd  |
|repository     |Doelopslagplaats voor build-taak        |
|taskRunName     |Naam van taakuit voeren, waarmee de tag van de afbeelding wordt opgegeven |
|userAssignedIdentity |Resource-id van de door de gebruiker toegewezen identiteit die is ingeschakeld in de taak|
|customRegistryIdentity | Client-id van de door de gebruiker toegewezen identiteit die is ingeschakeld in de taak, die wordt gebruikt voor verificatie met een aangepast register |
|customRegistry |De naam van de aanmeldingsserver van het aangepaste register dat in de taak wordt gebruikt, *bijvoorbeeld mybaseregistry.azurecr.io*|
|sourceLocation     |Externe context voor de build-taak, bijvoorbeeld *https://github.com/ \<your-GitHub-ID\> /acr-build-helloworld-node.* |
|dockerFilePath | Pad naar het Dockerfile in de externe context, dat wordt gebruikt om de afbeelding te bouwen. |

### <a name="deploy-the-template"></a>De sjabloon implementeren

Implementeer de sjabloon met de [opdracht az deployment group create.][az-deployment-group-create] In dit voorbeeld wordt de afbeelding *helloworld-node:testrun* gebouwd en naar een register met de naam *mycontainerregistry ge pusht.* De basisafbeelding wordt uit *mybaseregistry.azurecr.io.*

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=basetask \
    userAssignedIdentity=$resourceID \
    customRegistryIdentity=$clientID \
    sourceLocation=https://github.com/<your-GitHub-ID>/acr-build-helloworld-node.git#main \
    dockerFilePath=Dockerfile-test \
    customRegistry=mybaseregistry.azurecr.io
```

Met de vorige opdracht worden de parameters op de opdrachtregel door gegeven. Geef ze indien gewenst door in een [parametersbestand](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Implementatie controleren

Nadat de implementatie is voltooid, controleert u of de installatieprogramma is gemaakt door [az acr repository show-tags uit te stellen:][az-acr-repository-show-tags]

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Uitvoer:

```console
Result
--------
basetask
```

### <a name="view-run-log"></a>Runlogboek weergeven

Zie de stappen in de vorige sectie als u het [runlogboek wilt weergeven.](#view-run-log)

## <a name="next-steps"></a>Volgende stappen

 * Zie meer sjabloonvoorbeelden in de [ACR GitHub-opslagplaats](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment).
 * Zie de sjabloonverwijzing voor Taak [](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns) wordt uitgevoerd en Taken voor meer informatie over [sjablooneigenschappen.](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks)


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-logs]: /cli/azure/acr/task#az_acr_task_logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
