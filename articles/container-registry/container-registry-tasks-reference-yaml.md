---
title: YAML-verwijzing-ACR-taken
description: Naslag voor het definiëren van taken in YAML voor ACR-taken, waaronder taak eigenschappen, stap typen, stap eigenschappen en ingebouwde variabelen.
ms.topic: article
ms.date: 07/08/2020
ms.openlocfilehash: 042310d29f5561c2cd77b0b9cccfc587ca4aa767
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88067580"
---
# <a name="acr-tasks-reference-yaml"></a>Naslag informatie over ACR-taken: YAML

Met de definitie van een taak in ACR met meerdere stappen in de taken in een container wordt gestreefd over het bouwen, testen en patchen van containers. In dit artikel worden de opdrachten, para meters, eigenschappen en syntaxis beschreven voor de YAML-bestanden die uw taken met meerdere stappen definiëren.

Dit artikel bevat informatie over het maken van YAML-bestanden met meerdere stappen voor ACR-taken. Als u een inleiding wilt hebben over ACR-taken, raadpleegt u het [overzicht van ACR-taken](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>ACR-taak. yaml-bestands indeling

ACR-taken bieden ondersteuning voor een taak declaratie met meerdere stappen in de standaard YAML-syntaxis. U definieert de stappen van een taak in een YAML-bestand. U kunt de taak vervolgens hand matig uitvoeren door het bestand door te geven aan de opdracht [AZ ACR run][az-acr-run] . U kunt ook het bestand gebruiken om een taak te maken met [AZ ACR Task Create][az-acr-task-create] die automatisch wordt geactiveerd tijdens een Git-door Voer, een update van een basis installatie kopie of een schema. Hoewel in dit artikel wordt verwezen naar `acr-task.yaml` het bestand dat de stappen bevat, ondersteunt ACR-taken een geldige bestands naam met een [ondersteunde extensie](#supported-task-filename-extensions).

`acr-task.yaml`Primitieven op het hoogste niveau zijn **taak eigenschappen**, **typen stappen** en **stap-eigenschappen**:

* [Taak eigenschappen](#task-properties) zijn van toepassing op alle stappen in de taak uitvoering. Er zijn verschillende algemene taak eigenschappen, waaronder:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Taak stap typen](#task-step-types) vertegenwoordigen de typen acties die kunnen worden uitgevoerd in een taak. Er zijn drie typen stappen:
  * `build`
  * `push`
  * `cmd`
* De eigenschappen van de [taak stap](#task-step-properties) zijn para meters die van toepassing zijn op een afzonderlijke stap. Er zijn verschillende stap eigenschappen, waaronder:
  * `startDelay`
  * `timeout`
  * `when`
  * ... en nog veel meer.

De basis indeling van een `acr-task.yaml` bestand, met inbegrip van enkele algemene stap eigenschappen, volgt. Hoewel een volledig overzicht van alle beschik bare stap-eigenschappen of het gebruik van het stap type niet wordt weer gegeven, biedt het een beknopt beeld van de Basic-bestands indeling.

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

### <a name="supported-task-filename-extensions"></a>Ondersteunde bestandsnaam extensies van de taak

ACR-taken hebben verschillende bestandsnaam extensies gereserveerd, waaronder `.yaml` , dat deze worden verwerkt als een taak bestand. Elke uitbrei ding die *niet* in de volgende lijst voor komt, wordt beschouwd door ACR-taken als een Dockerfile:. yaml,. yml,. toml,. json,. sh,. bash,. zsh,. ps1,. ps,. cmd,. bat,.

YAML is de enige bestands indeling die momenteel door ACR-taken wordt ondersteund. De andere bestandsnaam extensies zijn gereserveerd voor mogelijke toekomstige ondersteuning.

## <a name="run-the-sample-tasks"></a>De voorbeeld taken uitvoeren

In de volgende secties van dit artikel vindt u enkele voor beelden van taak bestanden waarnaar wordt verwezen. De voorbeeld taken bevinden zich in een open bare GitHub-opslag plaats, [Azure-samples/ACR-tasks][acr-tasks]. U kunt ze uitvoeren met de Azure CLI-opdracht [AZ ACR run][az-acr-run]. De voorbeeld opdrachten zijn vergelijkbaar met:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

De opmaak van de voorbeeld opdrachten veronderstelt dat u een standaard register hebt geconfigureerd in de Azure CLI, zodat de para meter wordt wegge laten `--registry` . Als u een standaard register wilt configureren, gebruikt u de opdracht [AZ configure][az-configure] met de `--defaults` para meter, waarmee een waarde wordt geaccepteerd `acr=REGISTRY_NAME` .

Als u bijvoorbeeld de Azure CLI wilt configureren met een standaard register met de naam ' myregistry ':

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Taak eigenschappen

Taak eigenschappen worden meestal boven aan een bestand weer gegeven `acr-task.yaml` en zijn algemene eigenschappen die van toepassing zijn tijdens de volledige uitvoering van de taak stappen. Sommige van deze algemene eigenschappen kunnen in een afzonderlijke stap worden overschreven.

| Eigenschap | Type | Optioneel | Beschrijving | Overschrijven ondersteund | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | tekenreeks | Ja | De versie van het `acr-task.yaml` bestand zoals geparseerd door de service ACR tasks. Hoewel ACR-taken streven om achterwaartse compatibiliteit te behouden, kunnen met deze waarde ACR-taken de compatibiliteit binnen een gedefinieerde versie behouden blijven. Als u geen waarde opgeeft, wordt de meest recente versie gebruikt. | Nee | Geen |
| `stepTimeout` | int (seconden) | Ja | Het maximum aantal seconden dat een stap kan worden uitgevoerd. Als de eigenschap is opgegeven voor een taak, wordt de eigenschap Default `timeout` van alle stappen ingesteld. Als de `timeout` eigenschap in een stap is opgegeven, wordt de eigenschap die door de taak is opgegeven, overschreven. | Ja | 600 (10 minuten) |
| `workingDirectory` | tekenreeks | Ja | De werkmap van de container tijdens runtime. Als de eigenschap is opgegeven voor een taak, wordt de eigenschap Default `workingDirectory` van alle stappen ingesteld. Als u een stap opgeeft, wordt de eigenschap die door de taak is opgegeven, overschreven. | Ja | `c:\workspace` in Windows of `/workspace` in Linux |
| `env` | [teken reeks, teken reeks,...] | Ja |  Matrix van teken reeksen in `key=value` een indeling die de omgevings variabelen voor de taak definieert. Als de eigenschap is opgegeven voor een taak, wordt de eigenschap Default `env` van alle stappen ingesteld. Als u een stap opgeeft, worden de omgevings variabelen die zijn overgenomen van de taak, overschreven. | Ja | Geen |
| `secrets` | [geheim, geheim,...] | Ja | Matrix van [geheime](#secret) objecten. | Nee | Geen |
| `networks` | [netwerk, netwerk,...] | Ja | Matrix van [netwerk](#network) objecten. | Nee | Geen |
| `volumes` | [volume, volume,...] | Ja | Matrix van [volume](#volume) -objecten. Hiermee geeft u de volumes met de bron inhoud aan een stap koppelen. | Nee | Geen |

### <a name="secret"></a>geheim

Het geheim object heeft de volgende eigenschappen.

| Eigenschap | Type | Optioneel | Beschrijving | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | tekenreeks | No | De id van het geheim. | Geen |
| `keyvault` | tekenreeks | Ja | De Azure Key Vault geheime URL. | Geen |
| `clientID` | tekenreeks | Ja | De client-ID van de door de [gebruiker toegewezen beheerde identiteit](container-registry-tasks-authentication-managed-identity.md) voor Azure-resources. | Geen |

### <a name="network"></a>network

Het netwerk object heeft de volgende eigenschappen.

| Eigenschap | Type | Optioneel | Beschrijving | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | tekenreeks | No | De naam van het netwerk. | Geen |
| `driver` | tekenreeks | Ja | Het stuur programma voor het beheren van het netwerk. | Geen |
| `ipv6` | booleaans | Ja | Of IPv6-netwerken zijn ingeschakeld. | `false` |
| `skipCreation` | booleaans | Ja | Hiermee wordt aangegeven of het maken van netwerken moet worden overgeslagen. | `false` |
| `isDefault` | booleaans | Ja | Of het netwerk een standaard netwerk is dat wordt meegeleverd met Azure Container Registry. | `false` |

### <a name="volume"></a>volume

Het object volume heeft de volgende eigenschappen.

| Eigenschap | Type | Optioneel | Beschrijving | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | tekenreeks | No | De naam van het volume dat moet worden gekoppeld. Mag alleen alfanumerieke tekens,-en _ bevatten. | Geen |
| `secret` | teken reeks voor map [string] | Nee | Elke sleutel van de kaart is de naam van een bestand dat is gemaakt en ingevuld in het volume. Elke waarde is de teken reeks versie van het geheim. Geheime waarden moeten base64-gecodeerd zijn. | Geen |

## <a name="task-step-types"></a>Taak stap typen

ACR-taken ondersteunen drie stap typen. Elk type stap ondersteunt verschillende eigenschappen, die in de sectie voor elk type stap worden beschreven.

| Type stap | Beschrijving |
| --------- | ----------- |
| [`build`](#build) | Hiermee maakt u een container installatie kopie met vertrouwde `docker build` syntaxis. |
| [`push`](#push) | Hiermee voert `docker push` u een van de zojuist gemaakte of gelabelde installatie kopieën uit in een container register. Azure Container Registry, andere persoonlijke registers en de open bare docker-hub worden ondersteund. |
| [`cmd`](#cmd) | Voert een container als een opdracht uit, met de para meters die zijn door gegeven aan de container `[ENTRYPOINT]` . Het `cmd` stap type biedt ondersteuning voor para meters zoals `env` , `detach` en andere bekende `docker run` opdracht Opties, het inschakelen van eenheids-en functionele tests met gelijktijdige container uitvoering. |

## <a name="build"></a>bouwen

Een container installatie kopie bouwen. Het `build` stap type vertegenwoordigt een multi tenant, veilige manier om `docker build` in de cloud te worden uitgevoerd als een primitieve van de eerste klasse.

### <a name="syntax-build"></a>Syntaxis: Build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Het `build` stap type ondersteunt de para meters in de volgende tabel. Het `build` stap type biedt ook ondersteuning voor alle opbouw opties van de opdracht [docker build](https://docs.docker.com/engine/reference/commandline/build/) , zoals het `--build-arg` instellen van opbouw tijd variabelen.

| Parameter | Beschrijving | Optioneel |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Hiermee wordt het volledig gekwalificeerde `image:tag` van de ingebouwde installatie kopie gedefinieerd.<br /><br />Als installatie kopieën kunnen worden gebruikt voor interne taak validaties, zoals functionele tests, maar niet voor alle installatie kopieën is `push` een REGI ster vereist. Als u echter een installatie kopie van een taak wilt laten uitvoeren, moet de installatie kopie een naam hebben waarnaar kan worden verwezen.<br /><br />In tegens telling tot `az acr build` , het uitvoeren van ACR-taken biedt geen standaard push gedrag. Met ACR-taken wordt in het standaard scenario aangenomen dat u een installatie kopie kunt bouwen, valideren en vervolgens kunt pushen. Zie [Push](#push) voor instructies voor het pushen van ingebouwde installatie kopieën. | Ja |
| `-f` &#124; `--file` | Hiermee geeft u de Dockerfile door gegeven aan `docker build` . Als u niets opgeeft, wordt de standaard-Dockerfile in de hoofdmap van de context gebruikt. Als u een Dockerfile wilt opgeven, geeft u de bestands naam aan ten opzichte van de hoofdmap van de context. | Ja |
| `context` | De hoofdmap is door gegeven aan `docker build` . De hoofdmap van elke taak is ingesteld op een gedeelde [variabele workingdirectory](#task-step-properties)en bevat de hoofdmap van de gekoppelde Git-gekloonde map. | Nee |

### <a name="properties-build"></a>Eigenschappen: Build

Het `build` stap type ondersteunt de volgende eigenschappen. Meer informatie over deze eigenschappen vindt u in de sectie [taak stap eigenschappen](#task-step-properties) van dit artikel.

| Eigenschappen | Type | Vereist |
| -------- | ---- | -------- |
| `detach` | booleaans | Optioneel |
| `disableWorkingDirectoryOverride` | booleaans | Optioneel |
| `entryPoint` | tekenreeks | Optioneel |
| `env` | [teken reeks, teken reeks,...] | Optioneel |
| `expose` | [teken reeks, teken reeks,...] | Optioneel |
| `id` | tekenreeks | Optioneel |
| `ignoreErrors` | booleaans | Optioneel |
| `isolation` | tekenreeks | Optioneel |
| `keep` | booleaans | Optioneel |
| `network` | object | Optioneel |
| `ports` | [teken reeks, teken reeks,...] | Optioneel |
| `pull` | booleaans | Optioneel |
| `repeat` | int | Optioneel |
| `retries` | int | Optioneel |
| `retryDelay` | int (seconden) | Optioneel |
| `secret` | object | Optioneel |
| `startDelay` | int (seconden) | Optioneel |
| `timeout` | int (seconden) | Optioneel |
| `volumeMount` | object | Optioneel |
| `when` | [teken reeks, teken reeks,...] | Optioneel |
| `workingDirectory` | tekenreeks | Optioneel |

### <a name="examples-build"></a>Voor beelden: Build

#### <a name="build-image---context-in-root"></a>Installatie kopie maken-context in hoofdmap

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Installatie kopie maken-context in submap

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>Push

Push een of meer ingebouwde of gelabelde installatie kopieën naar een container register. Ondersteunt het pushen naar persoonlijke registers zoals Azure Container Registry, of naar de open bare docker-hub.

### <a name="syntax-push"></a>Syntaxis: push

Het `push` stap type ondersteunt een verzameling installatie kopieën. De syntaxis van de YAML-verzameling ondersteunt inline-en geneste notaties. Het pushen van één installatie kopie wordt gewoonlijk weer gegeven met inline-syntaxis:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Gebruik geneste syntaxis bij het pushen van meerdere installatie kopieën voor meer Lees baarheid:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Eigenschappen: push

Het `push` stap type ondersteunt de volgende eigenschappen. Meer informatie over deze eigenschappen vindt u in de sectie [taak stap eigenschappen](#task-step-properties) van dit artikel.

| Eigenschap | Type | Vereist |
| -------- | ---- | -------- |
| `env` | [teken reeks, teken reeks,...] | Optioneel |
| `id` | tekenreeks | Optioneel |
| `ignoreErrors` | booleaans | Optioneel |
| `startDelay` | int (seconden) | Optioneel |
| `timeout` | int (seconden) | Optioneel |
| `when` | [teken reeks, teken reeks,...] | Optioneel |

### <a name="examples-push"></a>Voor beelden: push

#### <a name="push-multiple-images"></a>Meerdere installatie kopieën pushen

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

Het `cmd` stap type voert een container uit.

### <a name="syntax-cmd"></a>Syntaxis: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Eigenschappen: cmd

Het `cmd` stap type ondersteunt de volgende eigenschappen:

| Eigenschap | Type | Vereist |
| -------- | ---- | -------- |
| `detach` | booleaans | Optioneel |
| `disableWorkingDirectoryOverride` | booleaans | Optioneel |
| `entryPoint` | tekenreeks | Optioneel |
| `env` | [teken reeks, teken reeks,...] | Optioneel |
| `expose` | [teken reeks, teken reeks,...] | Optioneel |
| `id` | tekenreeks | Optioneel |
| `ignoreErrors` | booleaans | Optioneel |
| `isolation` | tekenreeks | Optioneel |
| `keep` | booleaans | Optioneel |
| `network` | object | Optioneel |
| `ports` | [teken reeks, teken reeks,...] | Optioneel |
| `pull` | booleaans | Optioneel |
| `repeat` | int | Optioneel |
| `retries` | int | Optioneel |
| `retryDelay` | int (seconden) | Optioneel |
| `secret` | object | Optioneel |
| `startDelay` | int (seconden) | Optioneel |
| `timeout` | int (seconden) | Optioneel |
| `volumeMount` | object | Optioneel |
| `when` | [teken reeks, teken reeks,...] | Optioneel |
| `workingDirectory` | tekenreeks | Optioneel |

Meer informatie over deze eigenschappen vindt u in de sectie [taak stap-eigenschappen](#task-step-properties) van dit artikel.

### <a name="examples-cmd"></a>Voor beelden: cmd

#### <a name="run-hello-world-image"></a>Installatie kopie van Hallo-wereld uitvoeren

Met deze opdracht wordt het `hello-world.yaml` taak bestand uitgevoerd, dat verwijst naar de [Hallo wereld-](https://hub.docker.com/_/hello-world/) installatie kopie op docker hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Bash-installatie kopie en echo ' Hallo wereld ' uitvoeren

Met deze opdracht wordt het `bash-echo.yaml` taak bestand uitgevoerd, dat verwijst naar de [bash](https://hub.docker.com/_/bash/) -installatie kopie op docker hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Specifieke bash-afbeeldings code uitvoeren

Als u een specifieke installatie kopie versie wilt uitvoeren, geeft u de tag op in de `cmd` .

Met deze opdracht wordt het `bash-echo-3.yaml` taak bestand uitgevoerd, dat verwijst naar de [bash: 3.0](https://hub.docker.com/_/bash/) -installatie kopie op docker hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Aangepaste installatie kopieën uitvoeren

Het `cmd` stap type verwijst naar afbeeldingen met de standaard `docker run` indeling. Voor installatie kopieën die niet zijn voorzien van een REGI ster, wordt ervan uitgegaan dat deze afkomstig zijn van docker.io. Het vorige voor beeld kan ook als volgt worden weer gegeven:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Met behulp van de standaard `docker run` installatie kopie verwijzing Conventie `cmd` kan installatie kopieën uit een persoonlijk REGI ster of de open bare docker-hub worden uitgevoerd. Als u verwijst naar afbeeldingen in hetzelfde REGI ster waarin de ACR-taak wordt uitgevoerd, hoeft u geen register referenties op te geven.

* Voer een installatie kopie uit vanuit een Azure container Registry. In het volgende voor beeld wordt ervan uitgegaan dat u een REGI ster met de naam `myregistry` en een aangepaste installatie kopie hebt `myimage:mytag` .

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* De register referentie met een uitvoer variabele of-alias generaliseren

    In plaats van de naam van uw REGI ster in een bestand vast te `acr-task.yaml` maken, kunt u het apparaat versleutelen met behulp van een [Run-variabele](#run-variables) of- [alias](#aliases). De `Run.Registry` variabele of `$Registry` alias wordt tijdens runtime uitgebreid naar de naam van het REGI ster waarin de taak wordt uitgevoerd.

    Als u bijvoorbeeld de voor gaande taak wilt generaliseren zodat deze werkt in een Azure container Registry, verwijst u naar de $Registry variabele in de naam van de installatie kopie:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

#### <a name="access-secret-volumes"></a>Toegang tot geheime volumes

`volumes`Met de eigenschap kunnen volumes en hun geheime inhoud worden opgegeven voor `build` en `cmd` stappen in een taak. In elke stap geeft een optionele `volumeMounts` eigenschap een lijst van de volumes en bijbehorende container paden om te koppelen aan de container bij die stap. Geheimen worden als bestanden weer gegeven op het koppelingspad van elk volume.

Een taak uitvoeren en twee geheimen koppelen aan een stap: één opgeslagen in een sleutel kluis en één die is opgegeven op de opdracht regel:

```azurecli
az acr run -f mounts-secrets.yaml --set-secret mysecret=abcdefg123456 https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/mounts-secrets.yaml -->
[!code-yml[task](~/acr-tasks/mounts-secrets.yaml)]

## <a name="task-step-properties"></a>Eigenschappen van taak stap

Elk stap type ondersteunt verschillende eigenschappen die geschikt zijn voor het type. In de volgende tabel worden alle beschik bare stap eigenschappen gedefinieerd. Niet alle stap typen ondersteunen alle eigenschappen. Als u wilt zien welke van deze eigenschappen beschikbaar zijn voor elk stap type, raadpleegt u de sectie met Naslag informatie voor het type [cmd](#cmd), [Build](#build)en [Push](#push) .

| Eigenschap | Type | Optioneel | Beschrijving | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | booleaans | Ja | Hiermee wordt aangegeven of de container moet worden losgekoppeld wanneer deze wordt uitgevoerd. | `false` |
| `disableWorkingDirectoryOverride` | booleaans | Ja | Hiermee wordt aangegeven of `workingDirectory` onderdrukkings functionaliteit moet worden uitgeschakeld. Gebruik dit in combi natie met `workingDirectory` om volledige controle te hebben over de werkmap van de container. | `false` |
| `entryPoint` | tekenreeks | Ja | Overschrijft de `[ENTRYPOINT]` container van een stap. | Geen |
| `env` | [teken reeks, teken reeks,...] | Ja | Matrix van teken reeksen in een `key=value` indeling die de omgevings variabelen voor de stap definieert. | Geen |
| `expose` | [teken reeks, teken reeks,...] | Ja | Matrix van poorten die vanuit de container worden weer gegeven. |  Geen |
| [`id`](#example-id) | tekenreeks | Ja | Identificeert de stap in de taak uniek. Andere stappen in de taak kunnen verwijzen naar een stap `id` , zoals bij afhankelijkheids controle met `when` .<br /><br />De `id` is ook de naam van de container die wordt uitgevoerd. Processen die worden uitgevoerd in andere containers in de taak, kunnen verwijzen naar de naam van de `id` DNS-host, of om deze te openen met docker-Logboeken [id], bijvoorbeeld. | `acb_step_%d`, waarbij `%d` de op 0 gebaseerde index van de stap top-down in het yaml-bestand |
| `ignoreErrors` | booleaans | Ja | Hiermee wordt aangegeven of de stap moet worden gemarkeerd als geslaagd, ongeacht of er een fout is opgetreden tijdens het uitvoeren van de container. | `false` |
| `isolation` | tekenreeks | Ja | Het isolatie niveau van de container. | `default` |
| `keep` | booleaans | Ja | Hiermee wordt aangegeven of de container van de stap na uitvoering moet worden bewaard. | `false` |
| `network` | object | Ja | Identificeert een netwerk waarin de container wordt uitgevoerd. | Geen |
| `ports` | [teken reeks, teken reeks,...] | Ja | Matrix van poorten die vanuit de container naar de host worden gepubliceerd. |  Geen |
| `pull` | booleaans | Ja | Hiermee wordt aangegeven of een pull van de container moet worden gedwongen voordat deze wordt uitgevoerd om het cache gedrag te voor komen. | `false` |
| `privileged` | booleaans | Ja | Hiermee wordt aangegeven of de container moet worden uitgevoerd in de beschermde modus. | `false` |
| `repeat` | int | Ja | Het aantal nieuwe pogingen om de uitvoering van een container te herhalen. | 0 |
| `retries` | int | Ja | Het aantal nieuwe pogingen dat wordt geprobeerd als de uitvoering van een container mislukt. Er wordt alleen geprobeerd een nieuwe poging uit te voeren als de afsluit code van een container niet gelijk is aan nul. | 0 |
| `retryDelay` | int (seconden) | Ja | De vertraging in seconden tussen nieuwe pogingen van de uitvoering van een container. | 0 |
| `secret` | object | Ja | Identificeert een Azure Key Vault geheime of [beheerde identiteit voor Azure-resources](container-registry-tasks-authentication-managed-identity.md). | Geen |
| `startDelay` | int (seconden) | Ja | Aantal seconden dat de uitvoering van een container moet worden vertraagd. | 0 |
| `timeout` | int (seconden) | Ja | Maximum aantal seconden dat een stap kan worden uitgevoerd voordat deze wordt beëindigd. | 600 |
| [`when`](#example-when) | [teken reeks, teken reeks,...] | Ja | Hiermee configureert u de afhankelijkheid van een stap voor een of meer andere stappen in de taak. | Geen |
| `user` | tekenreeks | Ja | De gebruikers naam of UID van een container | Geen |
| `workingDirectory` | tekenreeks | Ja | Hiermee stelt u de werkmap voor een stap in. ACR-taken maken standaard een hoofdmap als werkmap. Als uw build echter verschillende stappen heeft, kunnen eerdere stappen artefacten delen met latere stappen door dezelfde werkmap op te geven. | `c:\workspace` in Windows of `/workspace` in Linux |

### <a name="volumemount"></a>volumeMount

Het object volumeMount heeft de volgende eigenschappen.

| Eigenschap | Type | Optioneel | Beschrijving | Standaardwaarde |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | tekenreeks | No | De naam van het volume dat moet worden gekoppeld. Moet exact overeenkomen met de naam van een `volumes` eigenschap. | Geen |
| `mountPath`   | tekenreeks | nee | Het absolute pad voor het koppelen van bestanden in de container.  | Geen |

### <a name="examples-task-step-properties"></a>Voor beelden: taak stap eigenschappen

#### <a name="example-id"></a>Voor beeld: id

Maak twee installatie kopieën en pas een functionele test installatie kopie toe. Elke stap wordt aangeduid met een unieke naam `id` die andere stappen in de taak verwijzing in hun `when` eigenschap.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Voor beeld: wanneer

De `when` eigenschap geeft een afhankelijkheid van de stap aan van andere stappen in de taak. Het ondersteunt twee parameter waarden:

* `when: ["-"]` -Geeft geen afhankelijk van andere stappen. Een stap die aangeeft dat de `when: ["-"]` uitvoering onmiddellijk wordt gestart en de uitvoering van gelijktijdige stappen wordt ingeschakeld.
* `when: ["id1", "id2"]` -Geeft aan dat de stap afhankelijk is van de stappen met `id` ' id1 ' en `id` ' id2 '. Deze stap wordt pas uitgevoerd als de stappen ' id1 ' en ' id2 ' zijn voltooid.

Als `when` niet in een stap is opgegeven, is die stap afhankelijk van de voltooiing van de vorige stap in het `acr-task.yaml` bestand.

Uitvoering van de sequentiële stap zonder `when` :

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Uitvoering van de sequentiële stap met `when` :

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Parallelle installatie kopieën bouwen:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Parallelle installatie kopieën bouwen en afhankelijke tests:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Variabelen uitvoeren

ACR-taken bevatten een standaardset variabelen die beschikbaar zijn voor taak stappen wanneer ze worden uitgevoerd. U kunt toegang krijgen tot deze variabelen met behulp van de notatie `{{.Run.VariableName}}` , waarbij `VariableName` een van de volgende opties is:

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

De namen van variabelen zijn doorgaans zelf uitleg. Details volgen voor veelgebruikte variabelen. Vanaf YAML-versie `v1.1.0` kunt u een verkorte, vooraf gedefinieerde [taak alias](#aliases) gebruiken in plaats van de meeste run-variabelen. Gebruik bijvoorbeeld in plaats van `{{.Run.Registry}}` de `$Registry` alias.

### <a name="runid"></a>Run.ID

Elke uitvoering, tot `az acr run` of trigger op basis van de uitgevoerde taken `az acr task create` heeft een unieke id. De ID vertegenwoordigt de uitvoering die momenteel wordt uitgevoerd.

Wordt meestal gebruikt voor een unieke code ring van een afbeelding:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runsharedvolume"></a>Run. SharedVolume

De unieke id voor een gedeeld volume dat toegankelijk is voor alle taak stappen. Het volume is gekoppeld aan `c:\workspace` in Windows of `/workspace` in Linux. 

### <a name="runregistry"></a>Run. Registry

De volledig gekwalificeerde server naam van het REGI ster. Wordt doorgaans gebruikt om algemeen te verwijzen naar het REGI ster waarin de taak wordt uitgevoerd.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Run. Registrynaam

De naam van het container register. Wordt doorgaans gebruikt in taak stappen waarvoor geen volledig gekwalificeerde server naam is vereist, bijvoorbeeld stappen voor het `cmd` uitvoeren van Azure cli-opdrachten op registers.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run. date

De huidige UTC-tijd waarop de uitvoering begon.

### <a name="runcommit"></a>Uitvoeren. door voeren

Voor een taak die wordt geactiveerd door een door Voer voor een GitHub-opslag plaats, de commit-id.

### <a name="runbranch"></a>Run. Branch

Voor een taak die wordt geactiveerd door een door Voer voor een GitHub-opslag plaats, de naam van de vertakking.

## <a name="aliases"></a>Aliassen

Vanaf `v1.1.0` worden ACR-taken ondersteuning bieden voor aliassen die beschikbaar zijn voor taak stappen wanneer ze worden uitgevoerd. Aliassen zijn vergelijkbaar in het concept voor aliassen (opdracht snelkoppelingen) die worden ondersteund in bash en andere opdracht shells. 

Met een alias kunt u elke opdracht of groep opdrachten (inclusief opties en bestands namen) starten door één woord in te voeren.

ACR-taken bieden ondersteuning voor verschillende vooraf gedefinieerde aliassen en aangepaste aliassen die u maakt.

### <a name="predefined-aliases"></a>Vooraf gedefinieerde aliassen

De volgende taak aliassen zijn beschikbaar voor gebruik in plaats van [uitvoerings variabelen](#run-variables):

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

In taak stappen gaat u vooraf aan een alias met de `$` instructie, zoals in dit voor beeld:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Afbeeldings aliassen

Elk van de volgende aliassen wijst naar een stabiele afbeelding in micro soft Container Registry (MCR). U kunt deze in de `cmd` sectie van een taak bestand raadplegen zonder een instructie te gebruiken.

| Alias | Installatiekopie |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

In het volgende voor beeld wordt gebruikgemaakt van verschillende aliassen voor het [opschonen](container-registry-auto-purge.md) van afbeeldings Tags die ouder zijn dan 7 dagen in de opslag plaats `samples/hello-world` in het REGI ster run:

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Aangepaste alias

Definieer een aangepaste alias in uw YAML-bestand en gebruik deze zoals wordt weer gegeven in het volgende voor beeld. Een alias mag alleen alfanumerieke tekens bevatten. De standaard instructie voor het uitbreiden van een alias is het `$` teken.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

U kunt een koppeling maken naar een extern of lokaal YAML-bestand voor aangepaste alias definities. In het volgende voor beeld wordt een koppeling naar een YAML-bestand in Azure Blob Storage:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Volgende stappen

Voor een overzicht van taken met meerdere stappen raadpleegt u de [taken voor het maken, testen en bijwerken van meerdere stappen in ACR-taken](container-registry-tasks-multi-step.md).

Zie het [overzicht van ACR-taken](container-registry-tasks-overview.md)voor builds met één stap.



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
