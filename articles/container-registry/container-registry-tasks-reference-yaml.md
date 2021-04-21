---
title: YAML-naslag - ACR-taken
description: Naslag voor het definiëren van taken in YAML voor ACR-taken, waaronder taakeigenschappen, staptypen, stapeigenschappen en ingebouwde variabelen.
ms.topic: article
ms.date: 07/08/2020
ms.openlocfilehash: 126fcbce0569b2be6d9302cbbb718fa11e3e8046
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780943"
---
# <a name="acr-tasks-reference-yaml"></a>ACR-taken naslag: YAML

Taakdefinitie met meerdere stappen in ACR-taken biedt een containergerichte rekenprimitief die is gericht op het bouwen, testen en patchen van containers. In dit artikel worden de opdrachten, parameters, eigenschappen en syntaxis beschreven voor de YAML-bestanden die uw taken met meerdere stappen definiëren.

Dit artikel bevat naslag voor het maken van YAML-bestanden met meerdere stappen voor ACR-taken. Als u een inleiding tot de ACR-taken wilt, bekijkt u ACR-taken [overzicht.](container-registry-tasks-overview.md)

## <a name="acr-taskyaml-file-format"></a>bestandsindeling acr-task.yaml

ACR-taken ondersteunt taakdeclaratie met meerdere stappen in de standaard YAML-syntaxis. U definieert de stappen van een taak in een YAML-bestand. U kunt de taak vervolgens handmatig uitvoeren door het bestand door te geven aan [de opdracht az acr run.][az-acr-run] Of gebruik het bestand om een taak te maken met [az acr task create][az-acr-task-create] die automatisch wordt geactiveerd bij een Git-door commit, een update van de basisafbeelding of een planning. Hoewel dit artikel verwijst naar als het bestand met de stappen, ACR-taken elke geldige `acr-task.yaml` bestandsnaam met een [ondersteunde extensie .](#supported-task-filename-extensions)

De primitieven op het `acr-task.yaml` hoogste niveau zijn **taakeigenschappen,** **staptypen** en **stapeigenschappen:**

* [Taakeigenschappen zijn](#task-properties) van toepassing op alle stappen tijdens de uitvoering van de taak. Er zijn verschillende algemene taakeigenschappen, waaronder:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Taakstaptypen](#task-step-types) vertegenwoordigen de typen acties die in een taak kunnen worden uitgevoerd. Er zijn drie staptypen:
  * `build`
  * `push`
  * `cmd`
* [Eigenschappen van de taakstap](#task-step-properties) zijn parameters die van toepassing zijn op een afzonderlijke stap. Er zijn verschillende stapeigenschappen, waaronder:
  * `startDelay`
  * `timeout`
  * `when`
  * ... en nog veel meer.

De basisindeling van een `acr-task.yaml` bestand, met inbegrip van enkele algemene stapeigenschappen, volgt. Hoewel het geen volledige weergave is van alle beschikbare stapeigenschappen of het gebruik van staptype, biedt het een snel overzicht van de basisbestandsindeling.

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Ondersteunde bestandsnaamextensies voor taken

ACR-taken heeft verschillende bestandsnaamextensies gereserveerd, waaronder `.yaml` , die worden verwerkt als een taakbestand. Elke extensie die niet *in* de volgende lijst staat, wordt door ACR-taken beschouwd als een Dockerfile: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

YAML is de enige bestandsindeling die momenteel wordt ondersteund door ACR-taken. De andere bestandsnaamextensies zijn gereserveerd voor mogelijke toekomstige ondersteuning.

## <a name="run-the-sample-tasks"></a>De voorbeeldtaken uitvoeren

In de volgende secties van dit artikel wordt verwezen naar verschillende voorbeeldtaakbestanden. De voorbeeldtaken staan in een openbare [GitHub-opslagplaats, Azure-Samples/acr-tasks.][acr-tasks] U kunt ze uitvoeren met de Azure CLI-opdracht [az acr run.][az-acr-run] De voorbeeldopdrachten zijn vergelijkbaar met:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Bij de opmaak van de voorbeeldopdrachten wordt ervan uitgenomen dat u een standaardregister hebt geconfigureerd in de Azure CLI, zodat ze de `--registry` parameter weglaten. Als u een standaardregister wilt configureren, gebruikt [u de opdracht az configure][az-configure] met de parameter , die een waarde `--defaults` `acr=REGISTRY_NAME` accepteert.

Als u de Azure CLI bijvoorbeeld wilt configureren met een standaardregister met de naam 'myregistry':

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Taakeigenschappen

Taakeigenschappen worden doorgaans boven aan een bestand weergegeven en zijn algemene eigenschappen die van toepassing zijn tijdens de volledige `acr-task.yaml` uitvoering van de taakstappen. Sommige van deze algemene eigenschappen kunnen binnen een afzonderlijke stap worden overschrijven.

| Eigenschap | Type | Optioneel | Description | Ondersteunde overschrijven | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | tekenreeks | Yes | De versie van het `acr-task.yaml` bestand zoals geparseerd door de ACR-taken service. Hoewel ACR-taken ernaar streven om achterwaartse compatibiliteit te behouden, kan ACR-taken compatibiliteit binnen een gedefinieerde versie behouden. Als dit niet is gespecificeerd, wordt standaard de nieuwste versie gebruikt. | No | Geen |
| `stepTimeout` | int (seconden) | Yes | Het maximum aantal seconden dat een stap kan worden uitgevoerd. Als de eigenschap is opgegeven voor een taak, wordt de `timeout` standaard-eigenschap van alle stappen ingesteld. Als de eigenschap is opgegeven voor een stap, wordt de eigenschap `timeout` van de taak overschreven. | Yes | 600 (10 minuten) |
| `workingDirectory` | tekenreeks | Yes | De werkmap van de container tijdens runtime. Als de eigenschap is opgegeven voor een taak, wordt de `workingDirectory` standaard-eigenschap van alle stappen ingesteld. Als dit is opgegeven in een stap, wordt de eigenschap overschreven die door de taak is opgegeven. | Yes | `c:\workspace` in Windows of `/workspace` in Linux |
| `env` | [tekenreeks, tekenreeks, ...] | Yes |  Matrix met tekenreeksen in `key=value` indeling die de omgevingsvariabelen voor de taak definiëren. Als de eigenschap is opgegeven voor een taak, wordt de `env` standaard-eigenschap van alle stappen ingesteld. Indien opgegeven in een stap, worden alle omgevingsvariabelen overschreven die zijn overgenomen van de taak. | Yes | Geen |
| `secrets` | [geheim, geheim, ...] | Yes | Matrix met [geheime](#secret) objecten. | No | Geen |
| `networks` | [netwerk, netwerk, ...] | Yes | Matrix met [netwerkobjecten.](#network) | No | Geen |
| `volumes` | [volume, volume, ...] | Yes | Matrix met [volumeobjecten.](#volume) Hiermee geeft u volumes met broninhoud aan een stap te kunnen toevoegen. | No | Geen |

### <a name="secret"></a>geheim

Het geheime object heeft de volgende eigenschappen.

| Eigenschap | Type | Optioneel | Description | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | tekenreeks | No | De id van het geheim. | Geen |
| `keyvault` | tekenreeks | Yes | De Azure Key Vault geheime URL. | Geen |
| `clientID` | tekenreeks | Yes | De client-id van [de door de gebruiker toegewezen beheerde identiteit](container-registry-tasks-authentication-managed-identity.md) voor Azure-resources. | Geen |

### <a name="network"></a>network

Het netwerkobject heeft de volgende eigenschappen.

| Eigenschap | Type | Optioneel | Description | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | tekenreeks | No | De naam van het netwerk. | Geen |
| `driver` | tekenreeks | Yes | Het stuurprogramma voor het beheren van het netwerk. | Geen |
| `ipv6` | booleaans | Yes | Of IPv6-netwerken zijn ingeschakeld. | `false` |
| `skipCreation` | booleaans | Yes | Of het maken van een netwerk moet worden overgeslagen. | `false` |
| `isDefault` | booleaans | Yes | Of het netwerk een standaardnetwerk is dat wordt geleverd met Azure Container Registry. | `false` |

### <a name="volume"></a>volume

Het volumeobject heeft de volgende eigenschappen.

| Eigenschap | Type | Optioneel | Description | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | tekenreeks | No | De naam van het volume dat moet worden bevestigd. Mag alleen alfanumerieke tekens bevatten, '-' en '_'. | Geen |
| `secret` | map[tekenreeks]tekenreeks | No | Elke sleutel van de kaart is de naam van een bestand dat is gemaakt en ingevuld in het volume. Elke waarde is de tekenreeksversie van het geheim. Geheime waarden moeten Base64-gecodeerd zijn. | Geen |

## <a name="task-step-types"></a>Typen taakstap

ACR-taken ondersteunt drie staptypen. Elk staptype ondersteunt verschillende eigenschappen, die worden beschreven in de sectie voor elk staptype.

| Staptype | Description |
| --------- | ----------- |
| [`build`](#build) | Bouwt een container-afbeelding met behulp van vertrouwde `docker build` syntaxis. |
| [`push`](#push) | Hiermee wordt een `docker push` van de nieuw gebouwde of opnieuw getagged afbeeldingen uitgevoerd naar een containerregister. Azure Container Registry, andere privéregisters en de openbare Docker Hub worden ondersteund. |
| [`cmd`](#cmd) | Voert een container uit als een opdracht, met parameters doorgegeven aan de van de `[ENTRYPOINT]` container. Het staptype ondersteunt parameters zoals , en andere bekende opdrachtopties, waardoor eenheids- en functionele tests kunnen worden uitgevoerd `cmd` `env` met `detach` `docker run` gelijktijdige uitvoering van de container. |

## <a name="build"></a>bouwen

Bouw een containerafbeelding. Het `build` staptype vertegenwoordigt een veilige multi-tenant manier om in de cloud te worden uitgevoerd als `docker build` een eersteklas primitieve.

### <a name="syntax-build"></a>Syntaxis: build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Het `build` staptype ondersteunt de parameters in de volgende tabel. Het `build` staptype ondersteunt ook alle buildopties van de [opdracht docker build,](https://docs.docker.com/engine/reference/commandline/build/) zoals om `--build-arg` build-tijdvariabelen in te stellen.

| Parameter | Beschrijving | Optioneel |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Hiermee definieert u de `image:tag` volledig gekwalificeerde van de gebouwde afbeelding.<br /><br />Omdat afbeeldingen kunnen worden gebruikt voor interne taakvalidaties, zoals functionele tests, zijn niet alle afbeeldingen `push` vereist voor een register. Als u echter een afbeelding wilt maken in een taakuitvoering, heeft de afbeelding wel een naam nodig waarnaar moet worden verwezen.<br /><br />In `az acr build` tegenstelling tot biedt ACR-taken geen standaard pushgedrag. Bij ACR-taken wordt in het standaardscenario ervan uitgenomen dat u een afbeelding kunt bouwen, valideren en vervolgens pushen. Zie [push voor](#push) het optioneel pushen van ingebouwde afbeeldingen. | Yes |
| `-f` &#124; `--file` | Hiermee geeft u het Dockerfile op dat wordt doorgegeven aan `docker build` . Als dit niet is opgegeven, wordt ervan uitgegaan dat het standaard-Dockerfile in de hoofdmap van de context wordt gebruikt. Als u een Dockerfile wilt opgeven, geeft u de bestandsnaam door ten opzichte van de hoofdmap van de context. | Yes |
| `context` | De hoofdmap is doorgegeven aan `docker build` . De hoofdmap van elke taak is ingesteld op een gedeelde [workingDirectory](#task-step-properties)en bevat de hoofdmap van de gekoppelde gekloonde Git-map. | No |

### <a name="properties-build"></a>Eigenschappen: bouwen

Het `build` staptype ondersteunt de volgende eigenschappen. Meer informatie over deze eigenschappen vindt u in [de sectie Taakstapeigenschappen](#task-step-properties) van dit artikel.

| Eigenschappen | Type | Vereist |
| -------- | ---- | -------- |
| `detach` | booleaans | Optioneel |
| `disableWorkingDirectoryOverride` | booleaans | Optioneel |
| `entryPoint` | tekenreeks | Optioneel |
| `env` | [tekenreeks, tekenreeks, ...] | Optioneel |
| `expose` | [tekenreeks, tekenreeks, ...] | Optioneel |
| `id` | tekenreeks | Optioneel |
| `ignoreErrors` | booleaans | Optioneel |
| `isolation` | tekenreeks | Optioneel |
| `keep` | booleaans | Optioneel |
| `network` | object | Optioneel |
| `ports` | [tekenreeks, tekenreeks, ...] | Optioneel |
| `pull` | booleaans | Optioneel |
| `repeat` | int | Optioneel |
| `retries` | int | Optioneel |
| `retryDelay` | int (seconden) | Optioneel |
| `secret` | object | Optioneel |
| `startDelay` | int (seconden) | Optioneel |
| `timeout` | int (seconden) | Optioneel |
| `volumeMount` | object | Optioneel |
| `when` | [tekenreeks, tekenreeks, ...] | Optioneel |
| `workingDirectory` | tekenreeks | Optioneel |

### <a name="examples-build"></a>Voorbeelden: bouwen

#### <a name="build-image---context-in-root"></a>Build-afbeelding - context in hoofdmap

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Build-afbeelding - context in subdirectory

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>Push

Push een of meer ingebouwde of geretagged afbeeldingen naar een containerregister. Ondersteunt pushen naar privéregisters, zoals Azure Container Registry, of naar de openbare Docker Hub.

### <a name="syntax-push"></a>Syntaxis: push

Het `push` staptype ondersteunt een verzameling afbeeldingen. De syntaxis van de YAML-verzameling ondersteunt inline en geneste indelingen. Het pushen van één afbeelding wordt doorgaans weergegeven met behulp van een inline syntaxis:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Gebruik voor betere leesbaarheid geneste syntaxis bij het pushen van meerdere afbeeldingen:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Eigenschappen: pushen

Het `push` staptype ondersteunt de volgende eigenschappen. Meer informatie over deze eigenschappen vindt u in [de sectie Eigenschappen van](#task-step-properties) taakstap van dit artikel.

| Eigenschap | Type | Vereist |
| -------- | ---- | -------- |
| `env` | [tekenreeks, tekenreeks, ...] | Optioneel |
| `id` | tekenreeks | Optioneel |
| `ignoreErrors` | booleaans | Optioneel |
| `startDelay` | int (seconden) | Optioneel |
| `timeout` | int (seconden) | Optioneel |
| `when` | [tekenreeks, tekenreeks, ...] | Optioneel |

### <a name="examples-push"></a>Voorbeelden: pushen

#### <a name="push-multiple-images"></a>Meerdere afbeeldingen pushen

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Bouwen, pushen en uitvoeren

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

Met `cmd` het staptype wordt een container uitgevoerd.

### <a name="syntax-cmd"></a>Syntaxis: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Eigenschappen: cmd

Het `cmd` staptype ondersteunt de volgende eigenschappen:

| Eigenschap | Type | Vereist |
| -------- | ---- | -------- |
| `detach` | booleaans | Optioneel |
| `disableWorkingDirectoryOverride` | booleaans | Optioneel |
| `entryPoint` | tekenreeks | Optioneel |
| `env` | [tekenreeks, tekenreeks, ...] | Optioneel |
| `expose` | [tekenreeks, tekenreeks, ...] | Optioneel |
| `id` | tekenreeks | Optioneel |
| `ignoreErrors` | booleaans | Optioneel |
| `isolation` | tekenreeks | Optioneel |
| `keep` | booleaans | Optioneel |
| `network` | object | Optioneel |
| `ports` | [tekenreeks, tekenreeks, ...] | Optioneel |
| `pull` | booleaans | Optioneel |
| `repeat` | int | Optioneel |
| `retries` | int | Optioneel |
| `retryDelay` | int (seconden) | Optioneel |
| `secret` | object | Optioneel |
| `startDelay` | int (seconden) | Optioneel |
| `timeout` | int (seconden) | Optioneel |
| `volumeMount` | object | Optioneel |
| `when` | [tekenreeks, tekenreeks, ...] | Optioneel |
| `workingDirectory` | tekenreeks | Optioneel |

U vindt de details van deze eigenschappen in de [sectie Eigenschappen van](#task-step-properties) taakstap van dit artikel.

### <a name="examples-cmd"></a>Voorbeelden: cmd

#### <a name="run-hello-world-image"></a>Een hello-world-afbeelding uitvoeren

Met deze opdracht wordt het `hello-world.yaml` taakbestand uitgevoerd, dat verwijst naar de [hello-world-afbeelding](https://hub.docker.com/_/hello-world/) op Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Bash-afbeelding en echo 'hallo wereld' uitvoeren

Met deze opdracht wordt het `bash-echo.yaml` taakbestand uitgevoerd, dat verwijst naar de [bash-afbeelding](https://hub.docker.com/_/bash/) op Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Specifieke bash-afbeeldingstag uitvoeren

Als u een specifieke versie van de afbeelding wilt uitvoeren, geeft u de tag op in `cmd` de .

Met deze opdracht wordt het `bash-echo-3.yaml` taakbestand uitgevoerd, dat verwijst naar de [bash:3.0-afbeelding](https://hub.docker.com/_/bash/) op Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Aangepaste afbeeldingen uitvoeren

Het `cmd` staptype verwijst naar afbeeldingen met de `docker run` standaardindeling. Er wordt van uitgegaan dat afbeeldingen die niet voorafgaan aan een register, afkomstig zijn van docker.io. Het vorige voorbeeld kan net zo worden weergegeven als:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Met behulp van de `docker run` standaardreferentieconventie voor afbeeldingen kunt u afbeeldingen uitvoeren vanuit elk privéregister of de `cmd` openbare Docker Hub. Als u verwijst naar afbeeldingen in hetzelfde register waarin de ACR-taak wordt uitgevoerd, hoeft u geen registerreferenties op te geven.

* Voer een -afbeelding uit vanuit een Azure-containerregister. In het volgende voorbeeld wordt ervan uitgenomen dat u een register hebt met `myregistry` de naam en een aangepaste afbeelding `myimage:mytag` .

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* De registerverwijzing generaliseren met een run-variabele of alias

    In plaats van de registernaam in een bestand hard te coderen, kunt u deze draagbaarder maken met behulp van een `acr-task.yaml` [run-variabele](#run-variables) of [alias](#aliases). De `Run.Registry` variabele of alias `$Registry` breidt tijdens runtime uit naar de naam van het register waarin de taak wordt uitgevoerd.

    Als u bijvoorbeeld de voorgaande taak zo wilt generaliseren dat deze werkt in een Azure-containerregister, verwijst u naar de variabele $Registry in de naam van de afbeelding:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

#### <a name="access-secret-volumes"></a>Toegang tot geheime volumes

Met `volumes` de eigenschap kunnen volumes en hun geheime inhoud worden opgegeven voor de stappen en in een `build` `cmd` taak. In elke stap worden met een optionele eigenschap de volumes en bijbehorende containerpaden vermeld die in die stap aan de `volumeMounts` container moeten worden bevestigd. Geheimen worden opgegeven als bestanden op elk volume van het pad voor de bevestiging.

Voer een taak uit en bevestig twee geheimen aan een stap: een die is opgeslagen in een sleutelkluis en een die is opgegeven op de opdrachtregel:

```azurecli
az acr run -f mounts-secrets.yaml --set-secret mysecret=abcdefg123456 https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/mounts-secrets.yaml -->
[!code-yml[task](~/acr-tasks/mounts-secrets.yaml)]

## <a name="task-step-properties"></a>Eigenschappen van taakstap

Elk staptype ondersteunt verschillende eigenschappen die geschikt zijn voor het type. De volgende tabel definieert alle beschikbare stapeigenschappen. Niet alle staptypen ondersteunen alle eigenschappen. Als u wilt zien welke van deze eigenschappen beschikbaar zijn voor elk staptype, gaat u naar de secties [cmd,](#cmd) [build](#build)en push-staptype. [](#push)

| Eigenschap | Type | Optioneel | Description | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | booleaans | Yes | Of de container moet worden losgekoppeld bij het uitvoeren. | `false` |
| `disableWorkingDirectoryOverride` | booleaans | Yes | Hiermee wordt bepaald of `workingDirectory` overschrijven van functionaliteit moet worden uitgeschakeld. Gebruik dit in combinatie met `workingDirectory` om volledige controle te hebben over de werkmap van de container. | `false` |
| `entryPoint` | tekenreeks | Yes | Overschrijvingen de `[ENTRYPOINT]` van de container van een stap. | Geen |
| `env` | [tekenreeks, tekenreeks, ...] | Yes | Matrix van tekenreeksen in `key=value` indeling die de omgevingsvariabelen voor de stap definiëren. | Geen |
| `expose` | [tekenreeks, tekenreeks, ...] | Yes | Matrix van poorten die worden blootgesteld vanuit de container. |  Geen |
| [`id`](#example-id) | tekenreeks | Yes | Hiermee wordt de stap binnen de taak uniek geïdentificeerd. Andere stappen in de taak kunnen verwijzen naar de van een stap, zoals voor `id` afhankelijkheidscontrole met `when` .<br /><br />De `id` is ook de naam van de container die wordt uitgevoerd. Processen die worden uitgevoerd in andere containers in de taak kunnen verwijzen naar de als de DNS-hostnaam of voor toegang tot deze met `id` docker-logboeken [id], bijvoorbeeld. | `acb_step_%d`, waarbij de op 0 gebaseerde index van de stap `%d` van boven naar beneden in het YAML-bestand is |
| `ignoreErrors` | booleaans | Yes | Of de stap als geslaagd moet worden markeren, ongeacht of er een fout is opgetreden tijdens het uitvoeren van de container. | `false` |
| `isolation` | tekenreeks | Yes | Het isolatieniveau van de container. | `default` |
| `keep` | booleaans | Yes | Of de container van de stap moet worden bewaard na de uitvoering. | `false` |
| `network` | object | Yes | Identificeert een netwerk waarin de container wordt uitgevoerd. | Geen |
| `ports` | [tekenreeks, tekenreeks, ...] | Yes | Matrix van poorten die vanuit de container naar de host worden gepubliceerd. |  Geen |
| `pull` | booleaans | Yes | Hiermee wordt bepaald of een pull-actie van de container moet worden geforceerd voordat deze wordt uitgevoerd om cachinggedrag te voorkomen. | `false` |
| `privileged` | booleaans | Yes | Of de container moet worden uitgevoerd in de modus Met bevoegdheden. | `false` |
| `repeat` | int | Ja | Het aantal nieuwe proberen om de uitvoering van een container te herhalen. | 0 |
| `retries` | int | Ja | Het aantal nieuwe pogingen om te proberen als de uitvoering van een container mislukt. Een nieuwe poging wordt alleen geprobeerd als de afsluitende code van een container niet nul is. | 0 |
| `retryDelay` | int (seconden) | Yes | De vertraging in seconden tussen het opnieuw proberen van de uitvoering van een container. | 0 |
| `secret` | object | Yes | Identificeert een Azure Key Vault of [beheerde identiteit voor Azure-resources.](container-registry-tasks-authentication-managed-identity.md) | Geen |
| `startDelay` | int (seconden) | Yes | Het aantal seconden dat de uitvoering van een container moet worden vertraagd. | 0 |
| `timeout` | int (seconden) | Yes | Het maximum aantal seconden dat een stap kan worden uitgevoerd voordat deze wordt beëindigd. | 600 |
| [`when`](#example-when) | [tekenreeks, tekenreeks, ...] | Yes | Hiermee configureert u de afhankelijkheid van een stap op een of meer andere stappen binnen de taak. | Geen |
| `user` | tekenreeks | Yes | De gebruikersnaam of UID van een container | Geen |
| `workingDirectory` | tekenreeks | Yes | Hiermee stelt u de map voor een stap. Standaard maakt ACR-taken een hoofdmap als de werkmap. Als uw build echter verschillende stappen heeft, kunnen eerdere stappen artefacten delen met latere stappen door dezelfde map op te geven. | `c:\workspace` in Windows of `/workspace` in Linux |

### <a name="volumemount"></a>volumeMount

Het object volumeMount heeft de volgende eigenschappen.

| Eigenschap | Type | Optioneel | Description | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | tekenreeks | No | De naam van het volume dat moet worden bevestigd. Moet exact overeenkomen met de naam van een `volumes` eigenschap. | Geen |
| `mountPath`   | tekenreeks | nee | Het absolute pad voor het aan de container toevoegen van bestanden.  | Geen |

### <a name="examples-task-step-properties"></a>Voorbeelden: Eigenschappen van taakstap

#### <a name="example-id"></a>Voorbeeld: id

Bouw twee afbeeldingen om een functionele testafbeelding te maken. Elke stap wordt geïdentificeerd door een uniek welke `id` andere stappen in de taakverwijzing in hun `when` eigenschap.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Voorbeeld: wanneer

De `when` eigenschap geeft de afhankelijkheid van een stap aan van andere stappen binnen de taak. Het ondersteunt twee parameterwaarden:

* `when: ["-"]` - Geeft aan dat er geen afhankelijkheid is van andere stappen. Een stap die `when: ["-"]` opgeeft, begint onmiddellijk met de uitvoering en maakt gelijktijdige uitvoering van stappen mogelijk.
* `when: ["id1", "id2"]` - Geeft aan dat de stap afhankelijk is van stappen `id` met 'id1' en `id` 'id2'. Deze stap wordt pas uitgevoerd als de stappen id1 en id2 zijn voltooid.

Als niet is opgegeven in een stap, is die stap afhankelijk van de voltooiing `when` van de vorige stap in het `acr-task.yaml` bestand.

Sequentiële stapuitvoering zonder `when` :

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Sequentiële stapuitvoering met `when` :

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Parallelle afbeeldingen bouwen:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Parallelle build van afbeeldingen en afhankelijke tests:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Variabelen uitvoeren

ACR-taken bevat een standaardset variabelen die beschikbaar zijn voor taakstappen wanneer ze worden uitgevoerd. Deze variabelen zijn toegankelijk met behulp van de indeling `{{.Run.VariableName}}` , waarbij een van de volgende `VariableName` is:

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

De namen van variabelen spreken over het algemeen voor zich. Details volgen voor veelgebruikte variabelen. Vanaf YAML-versie kunt u een verkorte, vooraf gedefinieerde taakalias gebruiken in plaats van de meeste `v1.1.0` runvariabelen. [](#aliases) Gebruik bijvoorbeeld in plaats van `{{.Run.Registry}}` de `$Registry` alias .

### <a name="runid"></a>Run.ID

Elke uitvoering, via `az acr run` of trigger op basis van uitvoering van taken die zijn gemaakt via , heeft een unieke `az acr task create` id. De id vertegenwoordigt de uitvoering die momenteel wordt uitgevoerd.

Doorgaans gebruikt voor het uniek taggen van een afbeelding:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runsharedvolume"></a>Run.SharedVolume

De unieke id voor een gedeeld volume dat toegankelijk is voor alle taakstappen. Het volume wordt in `c:\workspace` Windows of Linux aan het volume `/workspace` bevestigd. 

### <a name="runregistry"></a>Run.Registry

De volledig gekwalificeerde servernaam van het register. Meestal gebruikt om te verwijzen naar het register waar de taak wordt uitgevoerd.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Run.RegistryName

De naam van het containerregister. Wordt doorgaans gebruikt in taakstappen waarvoor geen volledig gekwalificeerde servernaam is vereist, bijvoorbeeld stappen waarmee Azure CLI-opdrachten in `cmd` registers worden uitgevoerd.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run.Date

De huidige UTC-tijd dat de run is gestart.

### <a name="runcommit"></a>Run.Commit

Voor een taak die wordt geactiveerd door een commit naar een GitHub-opslagplaats, de commit-id.

### <a name="runbranch"></a>Run.Branch

Voor een taak die wordt geactiveerd door een door commit naar een GitHub-opslagplaats, de naam van de vertakking.

## <a name="aliases"></a>Aliassen

Vanaf ondersteunt `v1.1.0` ACR-taken aliassen die beschikbaar zijn voor taakstappen wanneer ze worden uitgevoerd. Aliassen zijn in concept vergelijkbaar met aliassen (opdrachtsnelkoppelingen) die worden ondersteund in bash en een aantal andere opdrachtshells. 

Met een alias kunt u elke opdracht of groep opdrachten (inclusief opties en bestandsnamen) starten door één woord in te voeren.

ACR-taken ondersteunt verschillende vooraf gedefinieerde aliassen en ook aangepaste aliassen die u maakt.

### <a name="predefined-aliases"></a>Vooraf gedefinieerde aliassen

De volgende taakaliassen zijn beschikbaar voor gebruik in plaats van [runvariabelen:](#run-variables)

| Alias | Variabele uitvoeren |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

In taakstappen wordt een alias voorafgegaan door de `$` -opdracht, zoals in dit voorbeeld:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Aliassen voor afbeeldingen

Elk van de volgende aliassen wijst naar een stabiele afbeelding in Microsoft Container Registry (MCR). U kunt naar elk van deze verwijzen in de `cmd` sectie van een taakbestand zonder een -opdracht te gebruiken.

| Alias | Installatiekopie |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

In de volgende voorbeeldtaak worden verschillende aliassen gebruikt om [afbeeldingstags](container-registry-auto-purge.md) die ouder zijn dan 7 dagen in de repo in het runregister `samples/hello-world` op te sekken:

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Aangepaste alias

Definieer een aangepaste alias in uw YAML-bestand en gebruik deze zoals wordt weergegeven in het volgende voorbeeld. Een alias mag alleen alfanumerieke tekens bevatten. De standaard-richtlijn voor het uitbreiden van een alias is het `$` teken.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

U kunt een koppeling maken naar een extern of lokaal YAML-bestand voor aangepaste aliasdefinities. In het volgende voorbeeld wordt een koppeling naar een YAML-bestand in Azure Blob Storage gebruikt:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Volgende stappen

Zie Build-, test- en [patchtaken](container-registry-tasks-multi-step.md)met meerdere stappen uitvoeren in ACR-taken voor een overzicht van taken met meerdere ACR-taken.

Zie voor builds met één stap het [ACR-taken overzicht](container-registry-tasks-overview.md).



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-configure]: /cli/azure/reference-index#az_configure
