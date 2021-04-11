---
title: 'Zelfstudie: Snel een containerinstallatiekopie bouwen'
description: In deze zelfstudie leert u hoe een Docker-containerinstallatiekopie in Azure bouwt met Azure Container Registry Tasks (ACR Tasks) en deze vervolgens implementeert naar Azure Container Instances.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 384c7bebea8ed8120a1bc8134e4189e5e7bcb8db
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060275"
---
# <a name="tutorial-build-and-deploy-container-images-in-the-cloud-with-azure-container-registry-tasks"></a>Zelfstudie: Containerinstallatiekopieën bouwen in de cloud met Azure Container Registry-taken

[ACR Tasks](container-registry-tasks-overview.md) is een suite met functies in Azure Container Registry die gestroomlijnde en efficiënte builds van Docker-containerinstallatiekopieën mogelijk maakt in Azure. In dit artikel leert u hoe u de functie *quick task* van ACR Tasks gebruikt.

De ontwikkelingscyclus voor de 'binnenste lus' is het iteratieve proces van het schrijven van code, en het bouwen en testen van uw toepassing voordat u deze commit naar broncodebeheer. Met een quick task breidt u de 'binnenste lus' van uw ontwikkeling uit naar de cloud, zodat u beschikt over succesvalidatie van builds en het automatisch pushen van met succes samengestelde installatiekopieën naar uw containerregister. Uw installatiekopieën worden systeemeigen gebouwd in de cloud, dicht bij het register, waardoor snellere implementatie mogelijk is.

Al uw Dockerfile-expertise is direct overdraagbaar naar ACR Tasks. U hoeft uw Dockerfiles niet te wijzigen om in de cloud te bouwen met ACR Tasks, alleen de opdracht die u uitvoert. 

In deze zelfstudie, deel één van een reeks:

> [!div class="checklist"]
> * De broncode van een voorbeeldtoepassing ophalen
> * Een containerinstallatiekopie maken in Azure
> * Een container implementeren in Azure Container Instances

In volgende zelfstudies leert u hoe u ACR Tasks gebruikt voor geautomatiseerde builds van containerinstallatiekopieën bij codedoorvoer en updates van basisinstallatiekopieën. Met ACR-taken kunnen ook [taken bestaande uit meerdere stappen](container-registry-tasks-multi-step.md) worden uitgevoerd. Hierbij wordt een YAML-bestand gebruikt om de stappen voor het bouwen, pushen en optioneel testen van meerdere containers te definiëren.

## <a name="prerequisites"></a>Vereisten

### <a name="github-account"></a>GitHub-account

Maak een account op https://github.com als u er nog geen hebt. In deze zelfstudiereeks wordt een GitHub-opslagplaats gebruikt om geautomatiseerde builds van installatiekopieën in ACR Tasks te demonstreren.

### <a name="fork-sample-repository"></a>Voorbeeldopslagplaats splitsen

Gebruik vervolgens de GitHub UI om de voorbeeldopslagplaats te splitsen naar uw GitHub-account. In deze zelfstudie maakt u een containerinstallatiekopie van de bron in de opslagplaats. In de volgende zelfstudie pusht u een commit of doorvoer naar uw fork van de opslagplaats om een geautomatiseerde taak te starten.

Deze opslagplaats splitsen: https://github.com/Azure-Samples/acr-build-helloworld-node

![Schermafbeelding van de knop Fork (gemarkeerd) in GitHub][quick-build-01-fork]

### <a name="clone-your-fork"></a>Uw fork klonen

Wanneer u de opslagplaats hebt gesplitst, kloont u de fork en voert u de map in die de lokale kloon bevat.

Kloon de opslagplaats met `git`, vervang **\<your-github-username\>** door uw GitHub-gebruikersnaam:

```console
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

Geef de map op waarin de broncode zich bevindt:

```console
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Bash-shell

De opdrachten in deze zelfstudiereeks zijn geformatteerd voor de Bash-shell. Als u liever PowerShell, de opdrachtprompt of een andere shell wilt gebruiken, moet u wellicht de regelvoortzetting en indeling van de omgevingsvariabele overeenkomstig aanpassen.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="build-in-azure-with-acr-tasks"></a>Bouwen in Azure met ACR Tasks

Nu u de broncode hebt opgehaald naar uw computer, volgt u deze stappen om een containerregister te maken en de containerinstallatiekopie te bouwen met ACR Tasks.

Om het uitvoeren van de voorbeeldopdrachten eenvoudiger te maken, worden in de zelfstudies in deze serie shell-omgevingsvariabelen gebruikt. Voer de volgende opdracht uit om de variabele `ACR_NAME` in te stellen. Vervang **\<registry-name\>** door een unieke naam voor het nieuwe containerregister. De registernaam moet uniek zijn binnen Azure, mag alleen uit kleine letters bestaan, en moet 5 tot 50 alfanumerieke tekens bevatten. De andere resources die u in de zelfstudie maakt, zijn gebaseerd op deze naam, dus u hoeft alleen deze eerste variabele te wijzigen.

```console
ACR_NAME=<registry-name>
```

Nu de omgevingsvariabele van de containerregister is ingevuld, kunt u de rest van de opdrachten in de zelfstudie kopiëren en plakken zonder waarden te bewerken. Voer de volgende opdrachten uit om een resourcegroep en containerregister te maken:

```azurecli
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

Nu u een register hebt, gebruikt u ACR Tasks om een containerinstallatiekopie te bouwen van de voorbeeldcode. Voer de opdracht [az acr build][az-acr-build] uit om een *quick task* uit te voeren:

```azurecli
az acr build --registry $ACR_NAME --image helloacrtasks:v1 .
```

De uitvoer van de opdracht [az acr build][az-acr-build] is vergelijkbaar met de volgende. U ziet het uploaden van de broncode (de 'context') naar Azure en de details van de `docker build`-bewerking die ACR Tasks uitvoert in de cloud. Omdat ACR Tasks `docker build` gebruikt om uw installatiekopieën samen te stellen, hoeven er geen wijzigingen te worden aangebracht in uw Dockerfiles om ACR Tasks direct te gaan gebruiken.

```output
Packing source code into tar file to upload...
Sending build context (4.813 KiB) to ACR...
Queued a build with build ID: da1
Waiting for build agent...
2020/11/18 18:31:42 Using acb_vol_01185991-be5f-42f0-9403-a36bb997ff35 as the home volume
2020/11/18 18:31:42 Setting up Docker configuration...
2020/11/18 18:31:43 Successfully set up Docker configuration
2020/11/18 18:31:43 Logging in to registry: myregistry.azurecr.io
2020/11/18 18:31:55 Successfully logged in
Sending build context to Docker daemon   21.5kB
Step 1/5 : FROM node:15-alpine
15-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:15-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 88087d7e709a
Step 3/5 : RUN cd /src && npm install
 ---> Running in e80e1263ce9a
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.1s
Removing intermediate container e80e1263ce9a
 ---> 26aac291c02e
Step 4/5 : EXPOSE 80
 ---> Running in 318fb4c124ac
Removing intermediate container 318fb4c124ac
 ---> 113e157d0d5a
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in fe7027a11787
Removing intermediate container fe7027a11787
 ---> 20a27b90eb29
Successfully built 20a27b90eb29
Successfully tagged myregistry.azurecr.io/helloacrtasks:v1
2020/11/18 18:32:11 Pushing image: myregistry.azurecr.io/helloacrtasks:v1, attempt 1
The push refers to repository [myregistry.azurecr.io/helloacrtasks]
6428a18b7034: Preparing
c44b9827df52: Preparing
172ed8ca5e43: Preparing
8c9992f4e5dd: Preparing
8dfad2055603: Preparing
c44b9827df52: Pushed
172ed8ca5e43: Pushed
8dfad2055603: Pushed
6428a18b7034: Pushed
8c9992f4e5dd: Pushed
v1: digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1 size: 1366
2020/11/18 18:32:43 Successfully pushed image: myregistry.azurecr.io/helloacrtasks:v1
2020/11/18 18:32:43 Step ID acb_step_0 marked as successful (elapsed time in seconds: 15.648945)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloacrtasks
    tag: v1
    digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git: {}

Run ID: da1 was successful after 1m9.970148252s
```

Aan het einde van de uitvoer geeft ACR Tasks de afhankelijkheden weer die zijn gevonden voor uw installatiekopie. Zo kan ACR Tasks builds van installatiekopieën op updates van basisinstallatiekopieën automatiseren, bijvoorbeeld wanneer een basisinstallatiekopie wordt bijgewerkt met het besturingssysteem of met frameworkpatches. Later in deze zelfstudiereeks krijgt u meer informatie over de ondersteuning van ACR Tasks voor updates van basisinstallatiekopieën.

## <a name="deploy-to-azure-container-instances"></a>Implementeren naar Azure Container Instances

ACR Tasks pusht met succes samengestelde installatiekopieën standaard naar het register, zodat u ze onmiddellijk vanuit het register kunt implementeren.

In deze sectie maakt u een Azure Key Vault en service-principal. Vervolgens implementeert u de container naar Azure Container Instances (ACI) met de referenties van de service-principal.

### <a name="configure-registry-authentication"></a>Registerverificatie configureren

Alle productiescenario's moeten [service-principals][service-principal-auth] gebruiken voor toegang tot een Azure-containerregister. Met service-principals kunt u voor op rollen gebaseerde toegangsbeheer bieden voor uw containerinstallatiekopieën. U kunt bijvoorbeeld een service-principal configureren met alleen pull-toegang tot een register.

#### <a name="create-a-key-vault"></a>Een sleutelkluis maken

Als u nog geen kluis hebt in [Azure Key Vault](../key-vault/index.yml), kunt u met de volgende opdrachten één maken met de Azure CLI.

```azurecli
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-a-service-principal-and-store-credentials"></a>Een service-principal maken en referenties opslaan

U moet nu een service-principal maken en de referenties ervan opslaan in uw sleutelkluis.

Gebruik de opdracht [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] om de service-principal te maken, en [az keyvault secret set][az-keyvault-secret-set] om het **wachtwoord** van de service-principal op te slaan in de kluis:

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

Het argument `--role` in de voorgaande opdracht configureert de service-principal met de rol *acrpull*, die de principal alleen-push-toegang tot het register geeft. Als u zowel push-als pull-toegang wilt geven, wijzigt u het argument `--role` in *acrpush*.

Vervolgens slaat u de *appId* van de service-principal op in de kluis. Deze ID is de **gebruikersnaam** die u voor verificatie doorgeeft aan Azure Container Registry:

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

U hebt een Azure Key Vault gemaakt en er twee geheimen in opgeslagen:

* `$ACR_NAME-pull-usr`: De service-principal-id, voor gebruik als de **gebruikersnaam** van het containerregister.
* `$ACR_NAME-pull-pwd`: Het service-principal-wachtwoord, voor gebruik als het **wachtwoord** van het containerregister.

U kunt nu op naam naar deze geheime gegevens verwijzen wanneer u of uw toepassingen en services installatiekopieën uit het register halen.

### <a name="deploy-a-container-with-azure-cli"></a>Een container implementeren met Azure CLI

Nu de referenties voor de service-principal zijn opgeslagen als Azure Key Vault-geheimen, kunnen uw toepassingen en services deze gebruiken voor toegang tot uw persoonlijke register.

Voer de volgende op [az container create][az-container-create] uit om een containerinstantie te implementeren. De opdracht maakt gebruik van de referenties van de service-principal die zijn opgeslagen in Azure Key Vault om uw containerregister te verifiëren.

```azurecli
az container create \
    --resource-group $RES_GROUP \
    --name acr-tasks \
    --image $ACR_NAME.azurecr.io/helloacrtasks:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-tasks-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

De waarde `--dns-name-label` moet uniek zijn binnen Azure, dus de voorgaande opdracht voegt de naam van uw containerregister toe aan het label van de DNS-naam van de container. De uitvoer van de opdracht geeft bijvoorbeeld de Fully Qualified Domain Name (FQDN) van de container weer:

```output
FQDN
----------------------------------------------
acr-tasks-myregistry.eastus.azurecontainer.io
```

Let op de FQDN van de container; u gaat deze gebruiken in de volgende sectie.

### <a name="verify-the-deployment"></a>De implementatie controleren

Als u de opstartprocedure van de container wilt bekijken, gebruikt u de opdracht [az container attach][az-container-attach]:

```azurecli
az container attach --resource-group $RES_GROUP --name acr-tasks
```

De `az container attach`-uitvoer van de container geeft eerst de status van de container weer wanneer deze de installatiekopie ophaalt en start, en bindt vervolgens de STDOUT en STDERR van de lokale console aan die van de container.

```output
Container 'acr-tasks' is in state 'Running'...
(count: 1) (last timestamp: 2020-11-18 18:39:10+00:00) pulling image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2020-11-18 18:39:15+00:00) Successfully pulled image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2020-11-18 18:39:17+00:00) Created container
(count: 1) (last timestamp: 2020-11-18 18:39:17+00:00) Started container

Start streaming logs:
Server running at http://localhost:80
```

Wanneer `Server running at http://localhost:80` wordt weergegeven, gaat u naar de FQDN van de container in de browser om de actieve toepassing weer te geven. De FQDN-naam moet zijn opgenomen in de uitvoer van de opdracht `az container create` die u in de vorige sectie hebt uitgevoerd.

:::image type="content" source="media/container-registry-tutorial-quick-build/quick-build-02-browser.png" alt-text="Voorbeeldtoepassing die wordt uitgevoerd in browser":::

Als u de console wilt loskoppelen van de container, klikt u op `Control+C`.

## <a name="clean-up-resources"></a>Resources opschonen

Stop de containerinstantie met de opdracht [az container delete][az-container-delete]:

```azurecli
az container delete --resource-group $RES_GROUP --name acr-tasks
```

Als u *alle* resources wilt verwijderen die u in deze zelfstudie hebt gemaakt, met inbegrip van het containerregister, de sleutelkluis en de service-principal, geeft u de volgende opdrachten. Deze resources worden echter gebruikt in de [volgende zelfstudie](container-registry-tutorial-build-task.md) in de reeks. U kunt ze dus bewaren als u direct verdergaat met de volgende zelfstudie.

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Volgende stappen

Nu u de binnenste lus hebt getest met een quick task, configureert u een **build-taak** om containerinstallatiekopieën te activeren wanneer u broncode doorvoert naar een Git-opslagplaats:

> [!div class="nextstepaction"]
> [Automatische builds activeren met taken](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-login]: /cli/azure/reference-index#az-login
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
