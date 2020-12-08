---
title: Zelfstudie - Build van de installatiekopie activeren wanneer een privébasisinstallatiekopie wordt bijgewerkt
description: In deze zelfstudie configureert u een Azure Container Registry-taak om builds van containerinstallatiekopieën automatisch te activeren in de cloud wanneer een basisinstallatiekopie in een ander privécontainerregister in Azure wordt bijgewerkt.
ms.topic: tutorial
ms.date: 11/20/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 50eb89ccfafa27a7dcb0e97f21d14feec0ef9525
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030431"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-another-private-container-registry"></a>Zelfstudie: Builds van containerinstallatiekopieën automatiseren wanneer een basisinstallatiekopie wordt bijgewerkt in een ander privécontainerregister 

[ACR Tasks](container-registry-tasks-overview.md) ondersteunt de automatische uitvoering van builds wanneer [de basisinstallatiekopie van een container wordt bijgewerkt](container-registry-tasks-base-images.md), bijvoorbeeld wanneer u het besturingssysteem of toepassingsframework patcht in een van uw basisinstallatiekopieën. 

In deze zelfstudie leert u hoe u een ACR-taak maakt waarmee een build in de cloud wordt geactiveerd wanneer de basisinstallatiekopie van een container naar een ander Azure-containerregister wordt gepusht. U kunt ook een zelfstudie volgen voor het maken van een ACR-taak waarmee de build van een installatiekopie wordt geactiveerd wanneer een basisinstallatiekopie naar [hetzelfde Azure-containerregister](container-registry-tutorial-base-image-update.md) wordt gepusht.

In deze zelfstudie:

> [!div class="checklist"]
> * De basisinstallatiekopie bouwen in een basisregister
> * Een taak voor de build van de toepassing in een ander register maken om de basisinstallatiekopie te volgen 
> * De basisinstallatiekopie bijwerken om een taak voor een toepassingsinstallatiekopie bij te werken
> * De geactiveerde taak weergeven
> * De bijgewerkte toepassingsinstallatiekopie controleren

## <a name="prerequisites"></a>Vereisten

### <a name="complete-the-previous-tutorials"></a>De vorige zelfstudies voltooien

In deze zelfstudie wordt ervan uitgegaan dat u de omgeving al hebt geconfigureerd en de stappen uit de eerste twee zelfstudies in de reeks hebt uitgevoerd:

* Azure Container Registry maken
* Voorbeeldopslagplaats splitsen
* Voorbeeldopslagplaats klonen
* Persoonlijk toegangstoken van GitHub maken

Als u de volgende zelfstudies nog niet hebt afgerond, moet u dit doen voordat u verdergaat:

[Zelfstudie: containerinstallatiekopieën bouwen in de cloud met Azure Container Registry Tasks](container-registry-tutorial-quick-task.md)

[Builds van containerinstallatiekopieën automatiseren met Azure Container Registry Tasks](container-registry-tutorial-build-task.md)

Naast het containerregister dat is gemaakt voor de voorgaande zelfstudies, moet u een register maken om de basisinstallatiekopieën in op te slaan. U kunt het tweede register eventueel op een andere locatie dan het oorspronkelijke register maken.

### <a name="configure-the-environment"></a>De omgeving configureren

Vul deze variabelen voor de shell-omgeving in met waarden die geschikt zijn voor uw omgeving. Hoewel deze stap strikt genomen niet vereist is, vereenvoudigt u hiermee de uitvoering van meerregelige Azure CLI-opdrachten in deze zelfstudie. Als u deze omgevingsvariabelen niet invult, moet u elke bijbehorende waarde handmatig vervangen wanneer deze wordt weergegeven in de voorbeeldopdrachten.

```azurecli
BASE_ACR=<base-registry-name>   # The name of your Azure container registry for base images
ACR_NAME=<registry-name>        # The name of your Azure container registry for application images
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

### <a name="base-image-update-scenario"></a>Bijwerkscenario van basisinstallatiekopieën

In deze zelfstudie wordt u door een bijwerkscenario van een basisinstallatiekopie geleid. Dit scenario bevat een ontwikkelwerkstroom voor het beheren van basisinstallatiekopieën in een algemeen privécontainerregister bij het maken van toepassingsinstallatiekopieën in andere registers. De basisinstallatiekopieën kunnen algemene besturingssystemen en frameworks opgeven die worden gebruikt door een team, of zelfs algemene serviceonderdelen.

Ontwikkelaars die bijvoorbeeld installatiekopieën van toepassingen ontwikkelen in hun eigen registers, kunnen toegang krijgen tot een set basisinstallatiekopieën die wordt onderhouden in het algemene basisregister. Het basisregister kan zich in een andere regio bevinden of zelfs met geo-replicatie gemaakt zijn.

Het [codevoorbeeld][code-sample] bevat twee Dockerfiles: een toepassingsinstallatiekopie en een installatiekopie als basis. In de volgende gedeeltes maakt u een ACR-taak waarmee automatisch een build van de toepassingsinstallatiekopie wordt geactiveerd wanneer een nieuwe versie van de basisinstallatiekopie naar een ander Azure-containerregister wordt gepusht.

* [Dockerfile-app][dockerfile-app]: Een kleine Node.js-webtoepassing die een statische webpagina maakt waarop de Node.js-versie ervan wordt weergegeven. De versietekenreeks wordt gesimuleerd: deze geeft de inhoud weer van een omgevingsvariabele, `NODE_VERSION`, die is gedefinieerd in de basisinstallatiekopie.

* [Dockerfile-base][dockerfile-base]: De installatiekopie die `Dockerfile-app` opgeeft als basis. Deze is zelf gebaseerd op een [Node][base-node]-installatiekopie en bevat de omgevingsvariabele `NODE_VERSION`.

In de volgende gedeeltes maakt u een taak, werkt u de waarde `NODE_VERSION` in het Docker-bestand van de basisinstallatiekopie bij en gebruikt u ACR Tasks om de basisinstallatiekopie te maken. Zodra de ACR-taak de nieuwe basisinstallatiekopie naar uw register pusht, wordt er automatisch een build van de toepassingsinstallatiekopie geactiveerd. Optioneel kunt u de containerinstallatiekopie van de toepassing lokaal uitvoeren om de verschillende versietekenreeksen in de ingebouwde installatiekopieën te bekijken.

In deze zelfstudie wordt met uw ACR-taak een containerinstallatiekopie voor een toepassing gemaakt en gepusht. De installatiekopie is opgegeven in een Dockerfile. Met ACR-taken kunnen ook [taken bestaande uit meerdere stappen](container-registry-tasks-multi-step.md) worden uitgevoerd. Hierbij wordt een YAML-bestand gebruikt om de stappen voor het bouwen, pushen en optioneel testen van meerdere containers te definiëren.

## <a name="build-the-base-image"></a>De basisinstallatiekopie bouwen

Start met het bouwen van de basisinstallatiekopie met een *snelle taak* met behulp van [az acr build][az-acr-build]. Zoals beschreven in de [eerste zelfstudie](container-registry-tutorial-quick-task.md) in de reeks, wordt met dit proces niet alleen de installatiekopie gebouwd, maar wordt deze ook naar het containerregister gepusht als de build is gelukt. In dit voorbeeld wordt de installatiekopie naar het basisinstallatiekopieregister gepusht.

```azurecli
az acr build --registry $BASE_ACR --image baseimages/node:15-alpine --file Dockerfile-base .
```

## <a name="create-a-task-to-track-the-private-base-image"></a>Een taak maken om de privébasisinstallatiekopie te volgen

Maak vervolgens een taak in het installatiekopieregister van de toepassing met [az acr task create][az-acr-task-create], waardoor een [beheerde identiteit](container-registry-tasks-authentication-managed-identity.md) wordt ingeschakeld. De beheerde identiteit wordt in latere stappen gebruikt, zodat de taak wordt geverifieerd met het basisinstallatiekopieregister. 

In dit voorbeeld wordt een door het systeem toegewezen identiteit gebruikt, maar u kunt een door een gebruiker toegewezen beheerde identiteit maken en inschakelen voor bepaalde scenario's. Zie [Cross-registry authentication in an ACR task using an Azure-managed identity](container-registry-tasks-cross-registry-authentication.md) (Verificatie tussen registers in een ACR-taak met behulp van een beheerde identiteit van Azure) voor meer informatie.

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name baseexample2 \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT \
    --arg REGISTRY_NAME=$BASE_ACR.azurecr.io \
    --assign-identity
```

Deze taak is vergelijkbaar met de taak die u in de [vorige zelfstudie](container-registry-tutorial-build-task.md) hebt gemaakt. De taak geeft ACR Tasks de opdracht om een installatiekopiebuild te activeren wanneer doorvoeracties worden gepusht naar de opslagplaats die met `--context` is opgegeven. De Dockerfile die wordt gebruikt om de installatiekopie te bouwen in de vorige zelfstudie, geeft een openbare basisinstallatiekopie (`FROM node:15-alpine`) op. De Dockerfile in deze taak, [Dockerfile-app][dockerfile-app], geeft een basisinstallatiekopie in het basisinstallatiekopieregister op:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
```

Met deze configuratie kunt u eenvoudig een frameworkpatch in de basisinstallatiekopie simuleren verderop in deze zelfstudie.

## <a name="give-identity-pull-permissions-to-base-registry"></a>Machtigingen voor het ophalen van identiteiten aan het basisregister geven

Als u de beheerde identiteit van de taak machtigingen wilt geven om installatiekopieën uit het basisinstallatiekopieregister te halen, moet u eerst [az acr task show][az-acr-task-show] uitvoeren om de service-principal-id van de identiteit op te halen. Voer vervolgens [az acr show][az-acr-show] uit om de resource-id van het basisregister op te halen:

```azurecli
# Get service principal ID of the task
principalID=$(az acr task show --name baseexample2 --registry $ACR_NAME --query identity.principalId --output tsv) 

# Get resource ID of the base registry
baseregID=$(az acr show --name $BASE_ACR --query id --output tsv) 
```
 
Wijs de pull-machtigingen voor de beheerde identiteit aan het register toe door [az role assignment create][az-role-assignment-create] uit te voeren:

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID --role acrpull 
```

## <a name="add-target-registry-credentials-to-the-task"></a>De referenties voor het doelregister aan de taak toevoegen

Voer [az acr task credential add][az-acr-task-credential-add] uit om referenties toe te voegen aan de taak. Geef de parameter `--use-identity [system]` door om aan te geven dat de door het systeem toegewezen beheerde identiteit van de taak toegang kan krijgen tot de referenties.

```azurecli
az acr task credential add \
  --name baseexample2 \
  --registry $ACR_NAME \
  --login-server $BASE_ACR.azurecr.io \
  --use-identity [system] 
```

## <a name="manually-run-the-task"></a>De taak handmatig uitvoeren

Gebruik [az acr task run][az-acr-task-run] om handmatig de taak te activeren en de toepassingsinstallatiekopie te bouwen. Deze stap is nodig om ervoor te zorgen dat de taak de afhankelijkheid van de toepassingsinstallatiekopie van de basisinstallatiekopie bijhoudt.

```azurecli
az acr task run --registry $ACR_NAME --name baseexample2
```

Zodra de taak is voltooid, moet u de **run-id** (bijvoorbeeld 'da6') noteren als u de volgende optionele stap wilt voltooien.

### <a name="optional-run-application-container-locally"></a>Optioneel: Toepassingscontainer lokaal uitvoeren

Als u lokaal werkt (niet in Cloud Shell) en Docker hebt geïnstalleerd, voert u de container uit om de toepassing te bekijken in een webbrowser voordat u de basisinstallatiekopie opnieuw bouwt. Als u Cloud Shell gebruikt, slaat u deze sectie over (Cloud Shell biedt geen ondersteuning voor `az acr login` of `docker run`).

Verifieer uzelf eerst bij uw containerregister met [az acr login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Voer de container nu lokaal uit met `docker run`. Vervang **\<run-id\>** door de gevonden run-id in de uitvoer van de vorige stap (bijvoorbeeld 'da6'). Dit voorbeeld heeft de container `myapp` en bevat de parameter `--rm` om de container te verwijderen wanneer u deze stopt.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Ga naar `http://localhost:8080` in uw browser. U ziet nu het weergegeven nummer van de Node.js-versie op de webpagina, zoals hieronder weergegeven. In een latere stap verhoogt u de versie door een 'a' toe te voegen aan de versietekenreeks.

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-01.png" alt-text="Schermafbeelding van de voorbeeldtoepassing in de browser":::

Voer de volgende opdracht uit om de container te stoppen en te verwijderen:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>De builds weergeven

Geef vervolgens een overzicht weer van de taken die ACR Tasks heeft voltooid voor het register door de opdracht [az acr task list-runs][az-acr-task-list-runs] te gebruiken:

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

Als u de vorige zelfstudie hebt voltooid (en het register niet hebt verwijderd), ziet u de uitvoer zoals hieronder weergegeven. Noteer het aantal taak-runs en de meest recente run-id, zodat u de uitvoer kunt vergelijken nadat u in het volgende gedeelte de basisinstallatiekopie hebt bijgewerkt.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

UN ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca12      baseexample2    linux       Succeeded  Manual        2020-11-21T00:00:56Z  00:00:36
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
```

## <a name="update-the-base-image"></a>De basisinstallatiekopie bijwerken

Hier kunt u een frameworkpatch in de basisinstallatiekopie simuleren. Bewerk de **Dockerfile-basis**  en voeg een 'a' toe achter het versienummer dat is gedefinieerd in `NODE_VERSION`:

```Dockerfile
ENV NODE_VERSION 15.2.1a
```

Voer een snelle taak uit om de gewijzigde basisinstallatiekopie te bouwen. Noteer de waarde van **run-id** in de uitvoer.

```azurecli
az acr build --registry $BASE_ACR --image baseimages/node:15-alpine --file Dockerfile-base .
```

Zodra de build is voltooid en de ACR-taak de nieuwe basisinstallatiekopie naar uw register heeft gepusht, wordt automatisch een build van de toepassingsinstallatiekopie geactiveerd. Het kan enkele ogenblikken duren voordat de toepassingsinstallatiekopie wordt geactiveerd door de taak die u eerder hebt gemaakt, omdat met de taak de pas gemaakte en gepushte basisinstallatiekopie moet worden gedetecteerd.

## <a name="list-updated-build"></a>Bijgewerkte build weergeven

Nu u de basisinstallatiekopie hebt bijgewerkt, geeft u opnieuw een lijst van uw taken weer om deze te vergelijken met de vorige lijst. Als de uitvoer in eerste instantie niet verschilt, voert u regelmatig de opdracht uit tot de nieuwe taak-run in de lijst wordt weergegeven.

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

De uitvoer lijkt op het volgende. De TRIGGER voor de als laatste uitgevoerde build moet Update van installatiekopie zijn. Hiermee wordt aangegeven dat de taak is gestart door uw snelle taak van de basisinstallatiekopie.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca13      baseexample2    linux       Succeeded  Image Update  2020-11-21T00:06:00Z  00:00:43
ca12      baseexample2    linux       Succeeded  Manual        2020-11-21T00:00:56Z  00:00:36
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
```

Als u de volgende optionele stap van de nieuw gebouwde container wilt uitvoeren om het bijgewerkte versienummer te zien, noteert u de **RUN ID**-waarde voor het bijwerken van de door de installatiekopie getriggerde build (in de vorige uitvoer is dit 'ca13').

### <a name="optional-run-newly-built-image"></a>Optioneel: Nieuw gebouwde installatiekopie uitvoeren

Als u lokaal werkt (niet in Cloud Shell) en u hebt Docker geïnstalleerd, voert u de nieuwe toepassingsinstallatiekopie uit zodra de build is voltooid. Vervang `<run-id>` door de run-id uit de vorige stap. Als u Cloud Shell gebruikt, slaat u deze sectie over (Cloud Shell biedt geen ondersteuning voor `docker run`).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Ga naar http://localhost:8081 in uw browser. U ziet nu het bijgewerkte nummer van de Node.js-versie (met de 'a') op de webpagina:

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-02.png" alt-text="Schermafbeelding van bijgewerkte voorbeeldtoepassing in de browser":::

U hebt uw **basis** installatiekopie bijgewerkt met een nieuw versienummer, maar de **toepassings** installatiekopie die als laatste is gebouwd, geeft nieuwe versie weer. ACR Tasks heeft uw wijziging van de basisinstallatiekopie herkend en heeft de toepassingsinstallatiekopie automatisch opnieuw opgebouwd.

Voer de volgende opdracht uit om de container te stoppen en te verwijderen:

```bash
docker stop updatedapp
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een taak configureert om automatisch builds van containerinstallatiekopieën te activeren wanneer een basisinstallatiekopie is bijgewerkt. Ga nu verder met de volgende zelfstudie voor meer informatie over het activeren van taken volgens een gedefinieerd schema.

> [!div class="nextstepaction"]
> [Een taak volgens een schema uitvoeren](container-registry-tasks-scheduled.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task]: /cli/azure/acr#az-acr-task
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create

