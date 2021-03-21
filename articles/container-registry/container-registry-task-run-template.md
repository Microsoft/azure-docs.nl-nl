---
title: Snelle taak uitvoeren met sjabloon
description: Een ACR-taak in een wachtrij plaatsen om een installatie kopie te bouwen met behulp van een Azure Resource Manager sjabloon
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: 6e8023c088ac328c2b6e95fccd0230c4d40325c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98916062"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>ACR-taken uitvoeren met behulp van Resource Manager-sjablonen

[ACR-taken](container-registry-tasks-overview.md) is een reeks functies in Azure Container Registry die u helpen bij het beheren en wijzigen van containerinstallatiekopieën gedurende de levenscyclus van de container. 

In dit artikel vindt u voor beelden van Azure Resource Manager sjablonen voor het in de wachtrij plaatsen van een snelle taak, vergelijkbaar met een die u hand matig kunt maken met de opdracht [AZ ACR build][az-acr-build] .

Een resource manager-sjabloon voor het in de wachtrij plaatsen van een taak uitvoering is handig in automatiserings scenario's en breidt de functionaliteit van uit `az acr build` . Bijvoorbeeld:

* Een sjabloon gebruiken om een container register te maken en een taak uit te voeren in een wachtrij om een container installatie kopie te bouwen en te pushen
* Extra resources maken of inschakelen die u kunt gebruiken in een snelle taak uitvoering, zoals een beheerde identiteit voor Azure-resources

## <a name="limitations"></a>Beperkingen

* U moet een externe context, zoals een GitHub opslag plaats, opgeven als de [bron locatie](container-registry-tasks-overview.md#context-locations) voor het uitvoeren van uw taak. U kunt geen lokale bron context gebruiken.
* Voor taak uitvoeringen met behulp van een beheerde identiteit wordt alleen een door de *gebruiker toegewezen* beheerde identiteit toegestaan.

## <a name="prerequisites"></a>Vereisten

* **Github-account** : Maak een account op https://github.com Als u er nog geen hebt. 
* Voor **beeld-opslag plaats splitsen** : voor de taak voorbeelden die hier worden weer gegeven, gebruikt u de GitHub-gebruikers interface om de volgende voorbeeld opslagplaats te splitsen in uw github-account: https://github.com/Azure-Samples/acr-build-helloworld-node . Deze opslag plaats bevat voorbeeld Dockerfiles en bron code voor het bouwen van kleine container installatie kopieën.

## <a name="example-create-registry-and-queue-task-run"></a>Voor beeld: maken van Regi ster-en wachtrij taak uitvoeren

In dit voor beeld wordt een [voorbeeld sjabloon](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild) gebruikt om een container register te maken en een taak in de wachtrij te plaatsen die een installatie kopie bouwt en pusht. 

### <a name="template-parameters"></a>Sjabloonparameters

Voor dit voor beeld geeft u waarden op voor de volgende sjabloon parameters:

|Parameter  |Waarde  |
|---------|---------|
|registerpad     |De unieke naam van het REGI ster dat is gemaakt         |
|repository     |Doel opslagplaats voor Build-taak        |
|taskRunName     |De naam van de taak uitvoering, waarmee de afbeeldings code wordt opgegeven |
|sourceLocation     |Externe context voor de taak maken, bijvoorbeeld https://github.com/Azure-Samples/acr-build-helloworld-node . De Dockerfile in de opslag plaats root bouwt een container installatie kopie voor een kleine Node.js web-app. Gebruik, indien gewenst, uw Fork van de opslag plaats als de context van de build.         |

### <a name="deploy-the-template"></a>De sjabloon implementeren

Implementeer de sjabloon met de opdracht [AZ Deployment Group Create][az-deployment-group-create] . In dit voor beeld wordt de installatie kopie van het bestand *HelloWorld-node: testrun* gemaakt en gepusht naar een REGI ster met de naam *mycontainerregistry*.

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

Met de vorige opdracht worden de para meters door gegeven aan de opdracht regel. Geef ze desgewenst een voor een [parameter bestand](../azure-resource-manager/templates/parameter-files.md)door.

### <a name="verify-deployment"></a>Implementatie controleren

Nadat de implementatie is voltooid, controleert u of de installatie kopie is gemaakt door [AZ ACR repository show-Tags][az-acr-repository-show-tags]uit te voeren:

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

### <a name="view-run-log"></a>Uitvoerings logboek weer geven

Als u details over het uitvoeren van de taak wilt weer geven, bekijkt u het uitvoerings logboek.

Haal eerst de run-ID op met [AZ ACR Task List-runs][az-acr-task-list-runs]
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

Voer [AZ ACR-taak logboeken][az-acr-task-logs] uit om taak uitvoer logboeken voor de run-id weer te geven, in dit geval *Ca1*:

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

De uitvoer toont het logboek voor taak uitvoering.

U kunt ook het logboek voor taak uitvoering weer geven in de Azure Portal. 

1. Ga naar het container register
2. Onder **Services** selecteert u **taken**  >  **uitvoeren**.
3. Selecteer de run-ID, in dit geval *Ca1*. 

In de portal wordt het taak uitvoer logboek weer gegeven.

## <a name="example-task-run-with-managed-identity"></a>Voor beeld: taak uitvoeren met beheerde identiteit

Een [voorbeeld sjabloon](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity) gebruiken om een taak uit te voeren die een door de gebruiker toegewezen beheerde identiteit mogelijk maakt. Tijdens de uitvoering van de taak wordt de identiteit geverifieerd om een installatie kopie uit een ander Azure container Registry te halen. 

Dit scenario is vergelijkbaar met [Cross-Registry-verificatie in een ACR-taak met behulp van een door Azure beheerde identiteit](container-registry-tasks-cross-registry-authentication.md). Een organisatie kan bijvoorbeeld een gecentraliseerd REGI ster onderhouden met basis installatie kopieën die toegankelijk zijn voor meerdere ontwikkel teams.

### <a name="prepare-base-registry"></a>Basis register voorbereiden

Voor demonstratie doeleinden maakt u een afzonderlijk container register als basis register en pusht u een Node.js basis installatie kopie die is opgehaald uit docker hub.

1. Maak een tweede container register, bijvoorbeeld *mybaseregistry*, om basis installatie kopieën op te slaan.
1. Haal de `node:9-alpine` installatie kopie op uit docker hub, voorzie deze van het basis register en push het naar het basis register:

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>Nieuwe Dockerfile maken

Maak een Dockerfile die de basis installatie kopie uit het basis register ophaalt. Voer de volgende stappen uit in uw lokale Fork van de GitHub-opslag plaats, bijvoorbeeld `https://github.com/myGitHubID/acr-build-helloworld-node.git` .

1. Selecteer in de GitHub-gebruikers interface de optie **nieuw bestand maken**.
1. Geef het bestand de naam *Dockerfile-test* en plak de volgende inhoud. Vervang de register naam door de *mybaseregistry*.
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. Selecteer **nieuw bestand door voeren**.

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Machtigingen voor het verzamelen van identiteiten aan het basis register geven

Geef de beheerde identiteits machtigingen voor het ophalen van het basis register, *mybaseregistry*.

Gebruik de opdracht [AZ ACR show][az-acr-show] om de resource-id van het basis register op te halen en op te slaan in een variabele:

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

Gebruik de opdracht [AZ Role Assignment Create][az-role-assignment-create] om de identiteit van de rol Acrpull toe te wijzen aan het basis register. Deze rol heeft alleen machtigingen voor het ophalen van installatie kopieën uit het REGI ster.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>Sjabloonparameters

Voor dit voor beeld geeft u waarden op voor de volgende sjabloon parameters:

|Parameter  |Waarde  |
|---------|---------|
|registerpad     |Naam van het REGI ster waarin de installatie kopie is gemaakt  |
|repository     |Doel opslagplaats voor Build-taak        |
|taskRunName     |De naam van de taak uitvoering, waarmee de afbeeldings code wordt opgegeven |
|userAssignedIdentity |Resource-ID van de door de gebruiker toegewezen identiteit die in de taak is ingeschakeld|
|customRegistryIdentity | De client-ID van de door de gebruiker toegewezen identiteit die in de taak wordt gebruikt voor verificatie met een aangepast REGI ster |
|customRegistry |De naam van de aanmeldings server van het aangepaste REGI ster dat in de taak wordt geopend, bijvoorbeeld *mybaseregistry.azurecr.io*|
|sourceLocation     |Externe context voor de taak build, bijvoorbeeld *https://github.com/ \<your-GitHub-ID\> /ACR-build-HelloWorld-node.* |
|dockerFilePath | Het pad naar de Dockerfile in de externe context, die wordt gebruikt om de installatie kopie te bouwen. |

### <a name="deploy-the-template"></a>De sjabloon implementeren

Implementeer de sjabloon met de opdracht [AZ Deployment Group Create][az-deployment-group-create] . In dit voor beeld wordt de installatie kopie van het bestand *HelloWorld-node: testrun* gemaakt en gepusht naar een REGI ster met de naam *mycontainerregistry*. De basis installatie kopie wordt opgehaald uit *mybaseregistry.azurecr.io*.

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

Met de vorige opdracht worden de para meters door gegeven aan de opdracht regel. Geef ze desgewenst een voor een [parameter bestand](../azure-resource-manager/templates/parameter-files.md)door.

### <a name="verify-deployment"></a>Implementatie controleren

Nadat de implementatie is voltooid, controleert u of de installatie kopie is gemaakt door [AZ ACR repository show-Tags][az-acr-repository-show-tags]uit te voeren:

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

### <a name="view-run-log"></a>Uitvoerings logboek weer geven

Zie de stappen in de [voor gaande sectie](#view-run-log)om het uitvoerings logboek weer te geven.

## <a name="next-steps"></a>Volgende stappen

 * Meer voor beelden van sjablonen vindt u in de [ACR github opslag plaats](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment).
 * Zie de sjabloon verwijzing voor [taak uitvoeringen](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns) en [taken](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks)voor meer informatie over sjabloon eigenschappen.


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-logs]: /cli/azure/acr/task#az-acr-task-logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
