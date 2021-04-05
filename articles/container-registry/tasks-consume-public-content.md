---
title: Taak werk stroom voor het beheren van open bare register inhoud
description: Een geautomatiseerde Azure Container Registry taken werk stroom maken voor het bijhouden, beheren en gebruiken van inhoud van een open bare installatie kopie in een persoonlijk Azure container Registry.
author: SteveLasker
ms.topic: article
ms.author: stevelas
ms.date: 10/29/2020
ms.custom: ''
ms.openlocfilehash: 4fba6290b4973e797c13943fc9be4fadb19f3274
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96349279"
---
# <a name="how-to-consume-and-maintain-public-content-with-azure-container-registry-tasks"></a>Open bare inhoud gebruiken en onderhouden met Azure Container Registry taken

Dit artikel bevat een voorbeeld werk stroom in Azure Container Registry om u te helpen bij het beheren van het gebruik en de handhaving van open bare inhoud:

1. Lokale kopieën van afhankelijke open bare installatie kopieën importeren.
1. Valideer open bare installatie kopieën door middel van beveiligings scans en functionele tests.
1. Promoot de installatie kopieën naar persoonlijke registers voor intern gebruik.
1. Updates van basis installatie kopieën voor toepassingen die afhankelijk zijn van open bare inhoud.
1. Gebruik [Azure container Registry taken](container-registry-tasks-overview.md) om deze werk stroom te automatiseren.

De werk stroom wordt in de volgende afbeelding samenvatten:

![Werk stroom voor open bare inhoud gebruiken](./media/tasks-consume-public-content/consuming-public-content-workflow.png)

De werk stroom voor het importeren van de gated helpt bij het beheren van de afhankelijkheden van uw organisatie in extern beheerde artefacten, zoals installatie kopieën die zijn bron van open bare registers, waaronder [docker hub][docker-hub], [GCR][gcr], [kade][quay], [github container Registry][ghcr], [micro soft container Registry][mcr]of zelfs andere [Azure-container registers][acr]. 

Voor achtergrond informatie over de Risico's die worden geïntroduceerd door afhankelijkheden van open bare inhoud en hoe u Azure Container Registry kunt gebruiken om ze te beperken, raadpleegt u het blog bericht over het gebruik van [open bare][oci-consuming-public-content] inhoud [met de Azure container Registry](buffer-gate-public-content.md).

U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om deze procedure te volt ooien. Azure CLI-versie 2,10 of hoger wordt aanbevolen. Zie [Azure CLI installeren][install-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="scenario-overview"></a>Overzicht van scenario's

![werk stroom onderdelen importeren](./media/tasks-consume-public-content/consuming-public-content-objects.png)

In dit scenario wordt het volgende ingesteld:

1. Drie **container registers**, die het volgende vertegenwoordigen:
   * Een gesimuleerde [docker hub][docker-hub] ( `publicregistry` ) ter ondersteuning van het wijzigen van de basis installatie kopie
   * Team register ( `contoso` ) voor het delen van persoonlijke installatie kopieën
   * Gedeeld REGI ster/team van het bedrijf ( `baseartifacts` ) voor geïmporteerde open bare inhoud
1. Een **ACR-taak** in elk REGI ster. De taken:  
   1. Een gesimuleerde open bare `node` installatie kopie bouwen
   1. De installatie kopie importeren en valideren `node` in het gedeelde REGI ster van het bedrijf/team
   1. De installatie kopie bouwen en implementeren `hello-world`
1. **ACR-taak definities**, inclusief configuraties voor:
1. Een verzameling **register referenties**, die verwijst naar een sleutel kluis
1. Een verzameling **geheimen**, beschikbaar in een `acr-task.yaml` , die verwijst naar een sleutel kluis
1. Een verzameling **geconfigureerde waarden** die worden gebruikt in een `acr-task.yaml`
1. Een **Azure-sleutel kluis** om alle geheimen te beveiligen
1. Een **Azure-container instantie**, die als host fungeert voor de `hello-world` Build-toepassing

## <a name="prerequisites"></a>Vereisten

In de volgende stappen worden waarden voor resources die zijn gemaakt en gebruikt in de walkthrough geconfigureerd.

### <a name="set-environment-variables"></a>Omgevingsvariabelen instellen

Variabelen configureren die uniek zijn voor uw omgeving. We volgen de aanbevolen procedures voor het plaatsen van resources met duurzame inhoud in hun eigen resource groep om onbedoelde verwijdering te minimaliseren. U kunt deze echter desgewenst in één resource groep plaatsen.

De voor beelden in dit artikel zijn opgemaakt voor de bash-shell.

```bash
# Set the three registry names, must be globally unique:
REGISTRY_PUBLIC=publicregistry
REGISTRY_BASE_ARTIFACTS=contosobaseartifacts
REGISTRY=contoso

# set the location all resources will be created in:
RESOURCE_GROUP_LOCATION=eastus

# default resource groups
REGISTRY_PUBLIC_RG=${REGISTRY_PUBLIC}-rg
REGISTRY_BASE_ARTIFACTS_RG=${REGISTRY_BASE_ARTIFACTS}-rg
REGISTRY_RG=${REGISTRY}-rg

# fully qualified registry urls
REGISTRY_DOCKERHUB_URL=docker.io
REGISTRY_PUBLIC_URL=${REGISTRY_PUBLIC}.azurecr.io
REGISTRY_BASE_ARTIFACTS_URL=${REGISTRY_BASE_ARTIFACTS}.azurecr.io
REGISTRY_URL=${REGISTRY}.azurecr.io

# Azure key vault for storing secrets, name must be globally unique
AKV=acr-task-credentials
AKV_RG=${AKV}-rg

# ACI for hosting the deployed application
ACI=hello-world-aci
ACI_RG=${ACI}-rg
```

### <a name="git-repositories-and-tokens"></a>Git-opslag plaatsen en-tokens

Als u uw omgeving wilt simuleren, splitst u elk van de volgende Git-opslag plaatsen in opslag plaatsen die u kunt beheren. 

* https://github.com/importing-public-content/base-image-node.git
* https://github.com/importing-public-content/import-baseimage-node.git
* https://github.com/importing-public-content/hello-world.git

Werk vervolgens de volgende variabelen bij voor uw gevorkte opslag plaatsen.

De `:main` toevoeging aan het einde van de Git url's vertegenwoordigt de standaard vertakking van de opslag plaats.

```bash
GIT_BASE_IMAGE_NODE=https://github.com/<your-fork>/base-image-node.git#main
GIT_NODE_IMPORT=https://github.com/<your-fork>/import-baseimage-node.git#main
GIT_HELLO_WORLD=https://github.com/<your-fork>/hello-world.git#main
```

U hebt een [github-toegangs token (Pat)][git-token] nodig voor ACR-taken om Git-webhooks te klonen en te maken. Zie [een github-toegangs token maken](container-registry-tutorial-build-task.md#create-a-github-personal-access-token)voor stappen voor het maken van een token met de vereiste machtigingen voor een privé-opslag plaats. 

```bash
GIT_TOKEN=<set-git-token-here>
```

### <a name="docker-hub-credentials"></a>Referenties van docker hub  
Maak een [docker hub-token][docker-hub-tokens]om beperking en identiteits aanvragen te voor komen bij het ophalen van installatie kopieën uit docker hub. Stel vervolgens de volgende omgevings variabelen in:

```bash
REGISTRY_DOCKERHUB_USER=<yourusername>
REGISTRY_DOCKERHUB_PASSWORD=<yourtoken>
```

### <a name="create-registries"></a>Registers maken

Met Azure CLI-opdrachten maakt u drie container registers voor de Premium-laag, elk in een eigen resource groep:

```azurecli-interactive
az group create --name $REGISTRY_PUBLIC_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_PUBLIC_RG --name $REGISTRY_PUBLIC --sku Premium

az group create --name $REGISTRY_BASE_ARTIFACTS_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_BASE_ARTIFACTS_RG --name $REGISTRY_BASE_ARTIFACTS --sku Premium

az group create --name $REGISTRY_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_RG --name $REGISTRY --sku Premium
```

### <a name="create-key-vault-and-set-secrets"></a>Sleutel kluis maken en geheimen instellen

Een sleutel kluis maken:

```azurecli-interactive
az group create --name $AKV_RG --location $RESOURCE_GROUP_LOCATION
az keyvault create --resource-group $AKV_RG --name $AKV
```

Stel de gebruikers naam en het token van docker hub in de sleutel kluis in:

```azurecli-interactive
az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-user \
--value $REGISTRY_DOCKERHUB_USER

az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-password \
--value $REGISTRY_DOCKERHUB_PASSWORD
```

Stel een Git-PAT in en controleer deze in de sleutel kluis:

```azurecli-interactive
az keyvault secret set --vault-name $AKV --name github-token --value $GIT_TOKEN

az keyvault secret show --vault-name $AKV --name github-token --query value -o tsv
```

### <a name="create-resource-group-for-an-azure-container-instance"></a>Een resource groep maken voor een Azure-container exemplaar

Deze resource groep wordt in een latere taak gebruikt bij het implementeren van de `hello-world` installatie kopie.

```azurecli-interactive
az group create --name $ACI_RG --location $RESOURCE_GROUP_LOCATION
```

## <a name="create-public-node-base-image"></a>Open bare `node` basis installatie kopie maken

Als u de `node` installatie kopie op docker hub wilt simuleren, maakt u een [ACR-taak][acr-task] om de open bare installatie kopie te bouwen en te onderhouden. Met deze instelling kunt u wijzigingen door de `node` maintainers van de afbeelding simuleren.

```azurecli-interactive
az acr task create \
  --name node-public \
  -r $REGISTRY_PUBLIC \
  -f acr-task.yaml \
  --context $GIT_BASE_IMAGE_NODE \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_DOCKERHUB_URL}/ \
  --assign-identity
```

Als u wilt voor komen dat docker-beperking wordt toegepast, voegt u [docker hub-referenties][docker-hub-tokens] toe aan de taak. De opdracht referenties van de [ACR-taak][acr-task-credentials] kan worden gebruikt om docker-referenties door te geven aan een REGI ster, inclusief docker hub.

```azurecli-interactive
az acr task credential add \
  -n node-public \
  -r $REGISTRY_PUBLIC \
  --login-server $REGISTRY_DOCKERHUB_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-dockerhub-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-dockerhub-password \
  --use-identity [system]
```

Verleen de taak toegang om waarden te lezen uit de sleutel kluis:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name node-public \
                  --registry $REGISTRY_PUBLIC \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

[Taken kunnen worden geactiveerd][acr-task-triggers] door git-door Voer, updates van basis installatie kopieën, timers of hand matige uitvoeringen. 

Voer de taak hand matig uit om de `node` installatie kopie te genereren:

```azurecli-interactive
az acr task run -r $REGISTRY_PUBLIC -n node-public
```

Vermeld de installatie kopie in het gesimuleerde open bare REGI ster:

```azurecli-interactive
az acr repository show-tags -n $REGISTRY_PUBLIC --repository node
```

## <a name="create-the-hello-world-image"></a>De `hello-world` installatie kopie maken

Bouw een installatie kopie op basis van de gesimuleerde open bare `node` installatie kopie `hello-world` .

### <a name="create-token-for-pull-access-to-simulated-public-registry"></a>Token maken voor pull-toegang tot gesimuleerd openbaar REGI ster

Maak een [toegangs token][acr-tokens] naar het gesimuleerde open bare REGI ster, bereik tot `pull` . Vervolgens stelt u deze in de sleutel kluis in:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-user" \
  --value "registry-${REGISTRY_PUBLIC}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_PUBLIC}-user" \
              --registry $REGISTRY_PUBLIC \
              --scope-map _repositories_pull \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-token-for-pull-access-by-azure-container-instances"></a>Token maken voor pull-toegang door Azure Container Instances

Maak een [toegangs token][acr-tokens] voor het REGI ster dat als host fungeert `hello-world` voor de installatie kopie en bereik dat moet worden opgehaald. Vervolgens stelt u deze in de sleutel kluis in:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-user" \
  --value "registry-${REGISTRY}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY}-user" \
              --registry $REGISTRY \
              --repository hello-world content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-task-to-build-and-maintain-hello-world-image"></a>Taak maken om installatie kopie te bouwen en te onderhouden `hello-world`

Met de volgende opdracht wordt een taak gemaakt op basis van de definitie in `acr-tasks.yaml` in het `hello-world` opslag plaats. Met de taak stappen `hello-world` wordt de installatie kopie gebouwd en vervolgens geïmplementeerd op Azure container instances. De resource groep voor Azure Container Instances is gemaakt in een vorige sectie. `az container create`Als u in de taak aanroept met alleen een verschil in de `image:tag` , wordt de taak in dit overzicht geïmplementeerd op hetzelfde exemplaar.

```azurecli-interactive
az acr task create \
  -n hello-world \
  -r $REGISTRY \
  -f acr-task.yaml \
  --context $GIT_HELLO_WORLD \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --set KEYVAULT=$AKV \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG \
  --assign-identity
```

Referenties toevoegen aan de taak voor het gesimuleerde open bare REGI ster:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Verleen de taak toegang om waarden te lezen uit de sleutel kluis:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name hello-world \
                  --registry $REGISTRY \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Verleen de taak toegang voor het maken en beheren van Azure Container Instances door toegang te verlenen aan de resource groep:

```azurecli-interactive
az role assignment create \
  --assignee $(az acr task show \
  --name hello-world \
  --registry $REGISTRY \
  --query identity.principalId --output tsv) \
  --scope $(az group show -n $ACI_RG --query id -o tsv) \
  --role owner
```

Als de taak is gemaakt en geconfigureerd, voert u de taak uit om de installatie kopie te bouwen en te implementeren `hello-world` :

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

Zodra u het IP-adres van de container die als host fungeert voor de `hello-world` installatie kopie, ophalen.

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

Ga in uw browser naar het IP-adres om de actieve toepassing weer te geven.

## <a name="update-the-base-image-with-a-questionable-change"></a>De basis installatie kopie bijwerken met een ' twijfel achtige ' wijziging

In deze sectie wordt een wijziging in de basis installatie kopie gesimuleerd die problemen kan veroorzaken in de omgeving.

1. Open `Dockerfile` in de gevorkte `base-image-node` opslag plaats.
1. Wijzig de `BACKGROUND_COLOR` naar `Orange` om de wijziging te simuleren.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Orange
```

Voer de wijziging door en kijk voor ACR-taken om automatisch te beginnen met het bouwen.

Bekijk of de taak kan worden uitgevoerd:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Uiteindelijk ziet u de STATUS op `Succeeded` basis van een trigger van `Commit` :

```azurecli-interactive
RUN ID    TASK      PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------  ----------  ---------  ---------  --------------------  ----------
ca4       hub-node  linux       Succeeded  Commit     2020-10-24T05:02:29Z  00:00:22
```

Typ **CTRL + C** om de opdracht Watch af te sluiten en Bekijk de logboeken voor de meest recente uitvoering:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Wanneer de `node` installatie kopie is voltooid, `watch` voor ACR taken om automatisch te beginnen met het bouwen van de `hello-world` installatie kopie:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

Uiteindelijk ziet u de STATUS op `Succeeded` basis van een trigger van `Image Update` :

```azurecli-interactive
RUN ID    TASK         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  -----------  ----------  ---------  ------------  --------------------  ----------
dau       hello-world  linux       Succeeded  Image Update  2020-10-24T05:08:45Z  00:00:31
```

Typ **CTRL + C** om de opdracht Watch af te sluiten en Bekijk de logboeken voor de meest recente uitvoering:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

Zodra dit is voltooid, krijgt u het IP-adres van de site die als host fungeert voor de bijgewerkte `hello-world` installatie kopie:

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

Ga in uw browser naar de site, die een oranje (dubieuze) achtergrond moet hebben.

### <a name="checking-in"></a>Inchecken

U hebt op dit moment een `hello-world` installatie kopie gemaakt die automatisch is gebaseerd op Git-door voeringen en wijzigingen in de basis `node` installatie kopie. In dit voor beeld wordt de taak gebaseerd op een basis installatie kopie in Azure Container Registry, maar elk ondersteund REGI ster kan worden gebruikt.

Met de basis installatie kopie wordt de taak automatisch opnieuw geactiveerd wanneer de `node` installatie kopie wordt bijgewerkt. Zoals hier wordt weer gegeven, zijn niet alle updates gewenst.

## <a name="gated-imports-of-public-content"></a>De invoer van open bare inhoud wordt gegatedd

Om te voor komen dat wijzigingen in de upstream worden uitgevoerd, kunnen er beveiligings scans en functionele tests worden toegevoegd.

In deze sectie maakt u een ACR-taak voor het volgende:

* Een test installatie kopie bouwen
* Een functioneel test script uitvoeren `./test.sh` op de test installatie kopie
* Als de installatie kopie is getest, importeert u de open bare installatie kopie naar het **baseimages** -REGI ster

### <a name="add-automation-testing"></a>Automatiserings tests toevoegen

Als u een upstream-inhoud wilt Gate, wordt automatische tests geïmplementeerd. In dit voor beeld wordt er een `test.sh` gegeven waarmee de wordt gecontroleerd `$BACKGROUND_COLOR` . Als de test mislukt, `EXIT_CODE` `1` wordt geretourneerd dat de taak stap ACR mislukt, waardoor de taak wordt uitgevoerd. De tests kunnen worden uitgebreid in elke vorm van hulpprogram ma's, waaronder logboek registratie resultaten. De poort wordt beheerd door een pass/fail-reactie in het script, die hier opnieuw is geproduceerd:

```bash
if [ ""$(echo $BACKGROUND_COLOR | tr '[:lower:]' '[:upper:]') = 'RED' ]; then
    echo -e "\e[31mERROR: Invalid Color:\e[0m" ${BACKGROUND_COLOR}
    EXIT_CODE=1
else
  echo -e "\e[32mValidation Complete - No Known Errors\e[0m"
fi
exit ${EXIT_CODE}
```
### <a name="task-yaml"></a>Taak YAML 

Bekijk de `acr-task.yaml` in de `import-baseimage-node` opslag plaats, waarmee de volgende stappen worden uitgevoerd:

1. Bouw de test basis installatie kopie met behulp van de volgende Dockerfile:
    ```dockerfile
    ARG REGISTRY_FROM_URL=
    FROM ${REGISTRY_FROM_URL}node:15-alpine
    WORKDIR /test
    COPY ./test.sh .
    CMD ./test.sh
    ```
1. Wanneer u klaar bent, valideert u de installatie kopie door de container uit te voeren. deze wordt uitgevoerd `./test.sh`
1. Alleen als de bewerking is voltooid, voert u de stappen voor het importeren uit, die worden gegatedeerd met `when: ['validate-base-image']`

```yaml
version: v1.1.0
steps:
  - id: build-test-base-image
    # Build off the base image we'll track
    # Add a test script to do unit test validations
    # Note: the test validation image isn't saved to the registry
    # but the task logs captures log validation results
    build: >
      --build-arg REGISTRY_FROM_URL={{.Values.REGISTRY_FROM_URL}}
      -f ./Dockerfile
      -t {{.Run.Registry}}/node-import:test
      .
  - id: validate-base-image
    # only continues if node-import:test returns a non-zero code
    when: ['build-test-base-image']
    cmd: "{{.Run.Registry}}/node-import:test"
  - id: pull-base-image
    # import the public image to base-artifacts
    # Override the stable tag,
    # and create a unique tag to enable rollback
    # to a previously working image
    when: ['validate-base-image']
    cmd: >
        docker pull {{.Values.REGISTRY_FROM_URL}}node:15-alpine
  - id: retag-base-image
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine
  - id: retag-base-image-unique-tag
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine-{{.Run.ID}}
  - id: push-base-image
    when: ['retag-base-image', 'retag-base-image-unique-tag']
    push:
    - "{{.Run.Registry}}/node:15-alpine"
    - "{{.Run.Registry}}/node:15-alpine-{{.Run.ID}}"
```

### <a name="create-task-to-import-and-test-base-image"></a>Taak voor importeren en testen van basis installatie kopie maken

```azurecli-interactive
  az acr task create \
  --name base-import-node \
  -f acr-task.yaml \
  -r $REGISTRY_BASE_ARTIFACTS \
  --context $GIT_NODE_IMPORT \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --assign-identity
```

Referenties toevoegen aan de taak voor het gesimuleerde open bare REGI ster:

```azurecli-interactive
az acr task credential add \
  -n base-import-node \
  -r $REGISTRY_BASE_ARTIFACTS \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Verleen de taak toegang om waarden te lezen uit de sleutel kluis:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name base-import-node \
                  --registry $REGISTRY_BASE_ARTIFACTS \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

De import taak uitvoeren:

```azurecli-interactive
az acr task run -n base-import-node -r $REGISTRY_BASE_ARTIFACTS
```

> [!NOTE]
> Als de taak mislukt als gevolg `./test.sh: Permission denied` van, controleert u of het script uitvoerings machtigingen heeft en gaat u terug naar de Git-opslag plaats:
>```bash
>chmod +x ./test.sh
>```

## <a name="update-hello-world-image-to-build-from-gated-node-image"></a>`hello-world`Afbeelding bijwerken om te bouwen vanuit gegatedte `node` installatie kopie

Maak een [toegangs token][acr-tokens] voor toegang tot het REGI ster van basis artefacten, tot het bereik `read` van de `node` opslag plaats. Stel vervolgens in de sleutel kluis in:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
  --value "registry-${REGISTRY_BASE_ARTIFACTS}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
              --registry $REGISTRY_BASE_ARTIFACTS \
              --repository node content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

Voeg referenties toe aan de **Hello-World-** taak voor het basis artefacten register:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_BASE_ARTIFACTS_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-password \
  --use-identity [system]
```

De taak bijwerken om het `REGISTRY_FROM_URL` gebruik van het `BASE_ARTIFACTS` REGI ster te wijzigen

```azurecli-interactive
az acr task update \
  -n hello-world \
  -r $REGISTRY \
  --set KEYVAULT=$AKV \
  --set REGISTRY_FROM_URL=${REGISTRY_BASE_ARTIFACTS_URL}/ \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG
```

Voer de taak **Hallo wereld** uit om de afhankelijkheid van de basis installatie kopie te wijzigen:

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

## <a name="update-the-base-image-with-a-valid-change"></a>De basis installatie kopie bijwerken met een geldige wijziging

1. Open de `Dockerfile` in `base-image-node` opslag plaats.
1. Wijzig de `BACKGROUND_COLOR` naar `Green` om een geldige wijziging te simuleren.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Green
```

De wijziging door voeren en de volg orde van de updates controleren:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Nadat u hebt uitgevoerd, typt u **CTRL + C** en controleert u de logboeken:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Als u klaar bent, controleert u de taak voor het importeren van de **basis installatie kopie** :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

Nadat u hebt uitgevoerd, typt u **CTRL + C** en controleert u de logboeken:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

Als u klaar bent, controleert u de taak **Hallo wereld** :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

Nadat u hebt uitgevoerd, typt u **CTRL + C** en controleert u de logboeken:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

Zodra dit is voltooid, krijgt u het IP-adres van de site die als host fungeert voor de bijgewerkte `hello-world` installatie kopie:

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

Ga in uw browser naar de site, die een groene (geldige) achtergrond moet hebben.

### <a name="view-the-gated-workflow"></a>De werk stroom voor het gated weer geven

Voer de stappen in de voor gaande sectie opnieuw uit, met een achtergrond kleur rood.

1. Open de `Dockerfile` in de `base-image-node` opslag plaats
1. Wijzig de `BACKGROUND_COLOR` naar `Red` om een ongeldige wijziging te simuleren.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Red
```

De wijziging door voeren en de volg orde van de updates controleren:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Nadat u hebt uitgevoerd, typt u **CTRL + C** en controleert u de logboeken:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Als u klaar bent, controleert u de taak voor het importeren van de **basis installatie kopie** :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

Nadat u hebt uitgevoerd, typt u **CTRL + C** en controleert u de logboeken:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

Op dit moment ziet u de validatie van de taak **basis-import-knoop punt** mislukt en stopt u de reeks om een update te publiceren `hello-world` . De uitvoer ziet er ongeveer zo uit:

```console
[...]
2020/10/30 03:57:39 Launching container with name: validate-base-image
Validating Image
NODE_VERSION: 15-alpine
BACKGROUND_COLOR: Red
ERROR: Invalid Color: Red
2020/10/30 03:57:40 Container failed during run: validate-base-image. No retries remaining.
failed to run step ID: validate-base-image: exit status 1
```

### <a name="publish-an-update-to-hello-world"></a>Een update publiceren naar `hello-world`

Wijzigingen in de `hello-world` afbeelding blijven de laatste gevalideerde `node` installatie kopie gebruiken.

Eventuele aanvullende wijzigingen in de basis `node` installatie kopie die de gegatede validaties door geven, activeren de basis installatie kopie van de `hello-world` installatie kopie.

## <a name="cleaning-up"></a>Opschonen

Als u deze niet meer nodig hebt, verwijdert u de resources die in dit artikel worden gebruikt.

```azurecli-interactive
az group delete -n $REGISTRY_RG --no-wait -y
az group delete -n $REGISTRY_PUBLIC_RG --no-wait -y
az group delete -n $REGISTRY_BASE_ARTIFACTS_RG --no-wait -y
az group delete -n $AKV_RG --no-wait -y
az group delete -n $ACI_RG --no-wait -y
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel. u hebt ACR-taken gebruikt voor het maken van een geautomatiseerde beperking-werk stroom voor het introduceren van bijgewerkte basis installatie kopieën in uw omgeving. Zie Verwante informatie voor het beheren van installatie kopieën in Azure Container Registry.


* [Aanbevelingen voor het labelen en versie beheer van container installatie kopieën](container-registry-image-tag-version.md)
* [Een container installatie kopie in een Azure container Registry vergren delen](container-registry-image-lock.md)

[install-cli]:                  /cli/azure/install-azure-cli
[acr]:                          https://aka.ms/acr
[acr-repo-permissions]:         ./container-registry-repository-scoped-permissions.md
[acr-task]:                     ./container-registry-tasks-overview.md
[acr-task-triggers]:            container-registry-tasks-overview.md#task-scenarios
[acr-task-credentials]:       container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task
[acr-tokens]:                   ./container-registry-repository-scoped-permissions.md
[aci]:                          https://aka.ms/aci
[alpine-public-image]:          https://hub.docker.com/_/alpine
[docker-hub]:                   https://hub.docker.com
[docker-hub-tokens]:            https://hub.docker.com/settings/security
[git-token]:                    https://github.com/settings/tokens
[gcr]:                          https://cloud.google.com/container-registry
[ghcr]:                         https://docs.github.com/en/free-pro-team@latest/packages/getting-started-with-github-container-registry/about-github-container-registry
[helm-charts]:                  https://helm.sh
[mcr]:                          https://aka.ms/mcr
[nginx-public-image]:           https://hub.docker.com/_/nginx
[oci-artifacts]:                ./container-registry-oci-artifacts.md
[oci-consuming-public-content]: https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/
[opa]:                          https://www.openpolicyagent.org/
[quay]:                         https://quay.io