---
title: 'Zelfstudie: ACR-taak met meerdere stappen'
description: In deze zelfstudie leert u hoe u een Azure Container Registry-taak configureert om de werkstroom met meerdere stappen voor het maken, uitvoeren en pushen van containerinstallatiekopieën automatisch te activeren in de cloud wanneer u broncode naar een Git-opslagplaats doorvoert.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: be5c8c4012267dc7ce6362502c806a9f238732b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920271"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Zelfstudie: Een containerwerkstroom met meerdere stappen in de cloud uitvoeren bij het doorvoeren van broncode

Naast een [snelle taak](container-registry-tutorial-quick-task.md) bieden ACR-taken ondersteuning voor werkstromen op basis van meerdere-stappen en meerdere containers die automatisch kunnen worden geactiveerd wanneer u broncode doorvoert in een Git-opslagplaats. 

In deze zelfstudie gebruikt u YAML-voorbeeldbestanden om taken met meerdere stappen te definiëren voor het maken, uitvoeren en pushen van een of meer containerinstallatiekopieën naar een register bij het doorvoeren van broncode. Als u een taak wilt maken waarmee slechts één build van een installatiekopie bij het doorvoeren van code wordt geautomatiseerd, raadpleegt u [Zelfstudie: Builds van containerinstallatiekopieën in de cloud automatiseren bij het doorvoeren van broncode](container-registry-tutorial-build-task.md). Zie [Besturingssysteem- en frameworkpatching automatiseren met ACR-taken](container-registry-tasks-overview.md) voor een overzicht van ACR-taken.

In deze zelfstudie:

> [!div class="checklist"]
> * Een taak met meerdere stappen definiëren met behulp van een YAML-bestand
> * Een taak maken
> * Referenties aan de taak toevoegen om toegang tot een ander register mogelijk te maken (optioneel)
> * De taak testen
> * Taakstatus weergeven
> * De taak activeren met een code-doorvoer

In deze zelfstudie wordt ervan uitgegaan dat u de stappen in de [vorige zelfstudie](container-registry-tutorial-quick-task.md) hebt voltooid. Als u dit nog niet hebt gedaan, voert u de stappen in de sectie [Vereisten](container-registry-tutorial-quick-task.md#prerequisites) van de vorige zelfstudie uit voordat u doorgaat.

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]
[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-a-multi-step-task"></a>Een taak met meerdere stappen maken

Nu u de stappen hebt voltooid die nodig zijn om ACR-taken in te schakelen om toewijzingsstatus te lezen en webhooks te maken in een opslagplaats, kunt u een taak met meerdere stappen maken voor het maken, uitvoeren en pushen van een containerinstallatiekopie.

### <a name="yaml-file"></a>YAML-bestand

U definieert de stappen voor een taak met meerdere stappen in een [YAML-bestand](container-registry-tasks-reference-yaml.md). Het eerste voorbeeld van een taak met meerdere stappen in deze zelfstudie is gedefinieerd in het bestand `taskmulti.yaml`, dat zich bevindt in de hoofdmap van de GitHub-opslagplaats die u hebt gekloond:

```yml
version: v1.1.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

Deze taak met meerdere stappen doet het volgende:

1. Er wordt een `build`-stap uitgevoerd om een installatiekopie te maken op basis van de Dockerfile in de werkmap. De installatiekopie wordt uitgevoerd op het doelregister, `Run.Registry`, en wordt getagd met een unieke run-id voor ACR-taken. 
1. Er wordt een `cmd`-stap uitgevoerd om de installatiekopie uit te voeren in een tijdelijke container. In dit voorbeeld wordt een langlopende container op de achtergrond gestart en wordt de container-id geretourneerd, waarna de container wordt gestopt. In een praktijkscenario kunt u stappen voor het testen van de actieve container opnemen om te controleren of deze correct wordt uitgevoerd.
1. In een `push`-stap pusht u de build van de installatiekopie naar het uitvoeringsregister.

### <a name="task-command"></a>Taakopdracht

Vul eerst deze shell-omgevingsvariabelen met waarden die geschikt zijn voor uw omgeving. Hoewel deze stap strikt genomen niet vereist is, vereenvoudigt u hiermee de uitvoering van meerregelige Azure CLI-opdrachten in deze zelfstudie. Als u deze omgevingsvariabelen niet invult, moet u elke bijbehorende waarde handmatig vervangen wanneer deze wordt weergegeven in de voorbeeldopdrachten.

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Maak de taak nu door de volgende [az acr task create][az-acr-task-create]-opdracht uit te voeren:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Deze taak geeft aan dat elke keer dat code wordt doorgevoerd naar de *hoofd* vertakking in de opslagplaats zoals is opgegeven door `--context`, ACR-taken dan de taak met meerdere stappen van de code in die vertakking uitvoert. De stappen worden gedefinieerd in het YAML-bestand dat is opgegeven door `--file` vanuit de hoofdmap van de opslagplaats. 

De uitvoer van een geslaagde [az acr task create][az-acr-task-create]-opdracht is vergelijkbaar met het volgende:

```output
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2020-11-20T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git#main",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "main",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git#main",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-multi-step-workflow"></a>De werkstroom met meerdere stappen testen

Als u de taak met meerdere stappen wilt testen, activeert u deze handmatig door de opdracht [az acr task run][az-acr-task-run] uit te voeren:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Met de opdracht `az acr task run` streamt u standaard de logboekuitvoer naar de console wanneer u de opdracht uitvoert. In de uitvoer ziet u de voortgang van het uitvoeren van elke stap van de taak. De onderstaande uitvoer is ingekort om de belangrijkste stappen weer te geven.

```output
Queued a run with ID: cab
Waiting for an agent...
2020/11/20 00:03:31 Downloading source code...
2020/11/20 00:03:33 Finished downloading source code
2020/11/20 00:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2020/11/20 00:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2020/11/20 00:03:34 Successfully set up Docker network: acb_default_network
2020/11/20 00:03:34 Setting up Docker configuration...
2020/11/20 00:03:34 Successfully set up Docker configuration
2020/11/20 00:03:34 Logging in to registry: myregistry.azurecr.io
2020/11/20 00:03:35 Successfully logged into myregistry.azurecr.io
2020/11/20 00:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:35 Scanning for dependencies...
2020/11/20 00:03:36 Successfully scanned dependencies
2020/11/20 00:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB
[...]
Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2020/11/20 00:03:43 Successfully executed container: acb_step_0
2020/11/20 00:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2020/11/20 00:03:44 Successfully executed container: acb_step_1
2020/11/20 00:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1
[...]
2020/11/20 00:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2020/11/20 00:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2020/11/20 00:03:46 Populating digests for step ID: acb_step_0...
2020/11/20 00:03:47 Successfully populated digests for step ID: acb_step_0
2020/11/20 00:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2020/11/20 00:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2020/11/20 00:03:47 The following dependencies were found:
2020/11/20 00:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cab was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>Een build activeren met een doorvoer

Nu u de taak hebt getest door deze handmatig uit te voeren, gaat u deze automatisch activeren met een wijziging in de broncode.

Controleer eerst of u zich bevindt in de map waarin de lokale kloon van de [opslagplaats][sample-repo] staat:

```console
cd acr-build-helloworld-node
```

Voer vervolgens de volgende opdrachten uit om een nieuw bestand te maken, door te voeren en pushen naar uw fork van de opslagplaats op GitHub:

```console
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin main
```

U wordt mogelijk gevraagd om uw GitHub-referenties op te geven wanneer u de opdracht `git push` uitvoert. Geef uw GitHub-gebruikersnaam op en voer het persoonlijke toegangstoken (PAT) in dat u eerder hebt gemaakt voor het wachtwoord.

```azurecli-interactive
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Zodra u een doorvoer naar de opslagplaats hebt gepusht, wordt de webhook die is gemaakt door ACR-taken geactiveerd en wordt de taak in Azure Container Registry in gang gezet. Geef de logboeken weer voor de taak die momenteel wordt uitgevoerd om de voortgang van de build te verifiëren en controleren:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

De uitvoer is vergelijkbaar met de volgende, waarin de op dit moment uitgevoerde (of de laatst uitgevoerde) taak wordt getoond:

```output
Showing logs of the last created run.
Run ID: cad

[...]

Run ID: cad was successful after 37s
```

## <a name="list-builds"></a>Builds weergeven

Voor een overzicht van de taken die ACR Tasks heeft voltooid voor het register, voert u de opdracht [az acr task-list-runs][az-acr-task-list-runs] uit:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

De uitvoer van de opdracht ziet er ongeveer als volgt uit. De runs die ACR Tasks heeft uitgevoerd, worden weergegeven. 'Git Commit' wordt weergegeven in de kolom TRIGGER voor de meest recente taak:

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ----------  ---------  ---------  --------------------  ----------
cad       example1        linux       Succeeded  Commit     2020-11-20T00:22:15Z  00:00:35
cac       taskhelloworld  linux       Succeeded  Commit     2020-11-20T00:22:15Z  00:00:22
cab       example1        linux       Succeeded  Manual     2020-11-20T00:18:36Z  00:00:47
```

## <a name="create-a-multi-registry-multi-step-task"></a>Een taak met meerdere stappen en registers maken

ACR-taken zijn standaard gemachtigd om installatiekopieën te verzenden naar of op te halen uit het register waarin de taak wordt uitgevoerd (push/pull). U kunt ook een taak met meerdere stappen uitvoeren op een of meer andere registers dan het uitvoeringsregister. U wilt bijvoorbeeld een taak om builds van installatiekopieën te maken in een bepaald register en installatiekopieën met andere tags op te slaan in een tweede register dat wordt gebruikt in een productiesysteem. Dit voorbeeld laat zien hoe u een dergelijke taak maakt en referenties voor een ander register opgeeft.

Als u nog geen tweede register hebt, maakt u er een voor dit voorbeeld. Als u een register nodig hebt, zie dan de [vorige zelfstudie](container-registry-tutorial-quick-task.md) of [Snelstartgids: Een containerregister maken met de Azure CLI](container-registry-get-started-azure-cli.md).

Als u de taak wilt maken, hebt u de naam van de aanmeldingsserver van het register nodig in de vorm *mycontainerregistrydate.azurecr.io* (allemaal kleine letters). In dit voorbeeld gebruikt u het tweede register voor het opslaan van installatiekopieën, getagd op builddatum.

### <a name="yaml-file"></a>YAML-bestand

Het tweede voorbeeld van een taak met meerdere stappen in deze zelfstudie is gedefinieerd in het bestand `taskmulti-multiregistry.yaml`, dat zich bevindt in de hoofdmap van de GitHub-opslagplaats die u hebt gekloond:

```yml
version: v1.1.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

Deze taak met meerdere stappen doet het volgende:

1. Er worden twee `build`-stappen uitgevoerd om installatiekopieën te maken op basis van de Dockerfile in de werkmap:
    * De eerste stap wordt uitgevoerd op het doelregister, `Run.Registry`, en wordt getagd met de run-id voor ACR-taken. 
    * De tweede stap wordt uitgevoerd op het register `regDate` dat u instelt bij het maken van de taak (of opgeeft via een extern bestand, `values.yaml`, dat wordt doorgegeven aan `az acr task create`). Deze installatiekopie wordt getagd met de uitvoeringsdatum.
1. Hiermee wordt een `cmd`-stap uitgevoerd om een van de gemaakte containers uit te voeren. In dit voorbeeld wordt een langlopende container op de achtergrond gestart en wordt de container-id geretourneerd, waarna de container wordt gestopt. In een praktijkscenario kunt u een actieve container testen om te controleren of deze correct wordt uitgevoerd.
1. Met een `push`-stap worden de twee gemaakte installatiekopieën gepusht: de eerste naar het uitvoeringsregister, de tweede naar het register dat is opgegeven met `regDate`.

### <a name="task-command"></a>Taakopdracht

Maak de taak met behulp van de eerder gedefinieerde shell-omgevingsvariabelen door de volgende [az acr task create][az-acr-task-create]-opdracht uit te voeren. Vervang *mycontainerregistrydate* door de naam van uw register.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Taakreferentie toevoegen

Als u installatiekopieën wilt pushen naar het register dat wordt aangeduid met de waarde van `regDate`, gebruikt u de opdracht [az acr task credential add][az-acr-task-credential-add] om aanmeldingsreferenties voor dat register toe te voegen aan de taak.

Voor dit voorbeeld wordt u aangeraden een [service-principal](container-registry-auth-service-principal.md) te maken met toegang tot het register binnen het bereik van de rol *AcrPush*, zodat er installatiekopieën mogen worden gepusht. Als u de service-principal wilt maken, raadpleegt u dit [Azure CLI-script](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh).

Geef de toepassings-id en het wachtwoord van de service-principal op in de volgende `az acr task credential add`-opdracht. Zorg ervoor dat u de naam van de aanmeldingsserver *mycontainerregistrydate* bijwerkt met de naam van het tweede register:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

De CLI retourneert de naam van de door u aanmeldingsserver van het register.

### <a name="test-the-multi-step-workflow"></a>De werkstroom met meerdere stappen testen

Als u de taak met meerdere stappen wilt testen, activeert u deze handmatig door de opdracht [az acr task run][az-acr-task-run] uit te voeren, net zoals in het vorige voorbeeld. Als u de taak met een doorvoer naar de Git-opslagplaats wilt activeren, raadpleegt u de sectie [Een build activeren met een doorvoer](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Met de opdracht `az acr task run` streamt u standaard de logboekuitvoer naar de console wanneer u de opdracht uitvoert. In de uitvoer ziet u, net als voorheen, de voortgang van het uitvoeren van elke stap van de taak. De uitvoer is ingekort om de belangrijkste stappen weer te geven.

Uitvoer:

```output
Queued a run with ID: cf1g
Waiting for an agent...
2020/11/20 04:33:39 Downloading source code...
2020/11/20 04:33:41 Finished downloading source code
2020/11/20 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2020/11/20 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2020/11/20 04:33:43 Successfully set up Docker network: acb_default_network
2020/11/20 04:33:43 Setting up Docker configuration...
2020/11/20 04:33:44 Successfully set up Docker configuration
2020/11/20 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2020/11/20 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2020/11/20 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2020/11/20 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2020/11/20 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:47 Scanning for dependencies...
2020/11/20 04:33:47 Successfully scanned dependencies
2020/11/20 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB
[...]
Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2020/11/20 04:33:55 Successfully executed container: acb_step_0
2020/11/20 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:55 Scanning for dependencies...
2020/11/20 04:33:56 Successfully scanned dependencies
2020/11/20 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB
[...]
Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2020/11/20 04:33:57 Successfully executed container: acb_step_1
2020/11/20 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2020/11/20 04:33:58 Successfully executed container: acb_step_2
2020/11/20 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:58 Launching container with name: acb_step_3
test
2020/11/20 04:34:09 Successfully executed container: acb_step_3
2020/11/20 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]
[...]
2020/11/20 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2020/11/20 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]
[...]
2020/11/20 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2020/11/20 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2020/11/20 04:34:19 Populating digests for step ID: acb_step_0...
2020/11/20 04:34:21 Successfully populated digests for step ID: acb_step_0
2020/11/20 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2020/11/20 04:34:21 Populating digests for step ID: acb_step_1...
2020/11/20 04:34:23 Successfully populated digests for step ID: acb_step_1
2020/11/20 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2020/11/20 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2020/11/20 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2020/11/20 04:34:23 The following dependencies were found:
2020/11/20 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u taken met meerdere stappen en containers maakt die automatisch worden geactiveerd wanneer u broncode naar een Git-opslagplaats doorvoert. Zie de [YAML-referentie voor ACR-taken](container-registry-tasks-reference-yaml.md) voor geavanceerde functies van taken met meerdere stappen, waaronder parallelle en afhankelijke uitvoering van stappen. Ga verder naar de volgende zelfstudie voor informatie over het maken van taken die builds activeren wanneer de basisinstallatiekopie van de containerinstallatiekopie wordt bijgewerkt.

> [!div class="nextstepaction"]
> [Builds automatiseren op basis van installatiekopie-update](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add    
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
