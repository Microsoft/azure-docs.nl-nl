---
title: OCI-artefact pushen en pullen
description: OCI-artefacten (Open Container Initiative) pushen en pullen met behulp van een privécontainerregister in Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 02/03/2021
ms.author: stevelas
ms.openlocfilehash: 399bb001432759556cd0ba8bf15f7738dd4edb7c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781483"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Een OCI-artefact pushen en pullen met behulp van een Azure-containerregister

U kunt een Azure-containerregister gebruiken voor het opslaan en beheren van [OCI-artefacten (Open Container Initiative), evenals](container-registry-image-formats.md#oci-artifacts) Docker- en Docker-compatibele containerafbeeldingen.

Om deze mogelijkheid te demonstreren, laat dit artikel zien hoe u met het [hulpprogramma OCI Registry as Storage (ORAS)](https://github.com/deislabs/oras) een voorbeeldartefact ( een tekstbestand) naar een Azure-containerregister pusht. Haal vervolgens het artefact op uit het register. U kunt verschillende OCI-artefacten in een Azure-containerregister beheren met behulp van verschillende opdrachtregelprogramma's die geschikt zijn voor elk artefact.

## <a name="prerequisites"></a>Vereisten

* **Azure-containerregister**: maak een containerregister in uw Azure-abonnement. Gebruik bijvoorbeeld de [Azure Portal](container-registry-get-started-portal.md) of de [Azure CLI](container-registry-get-started-azure-cli.md).
* **ORAS-hulpprogramma:** download en installeer een huidige ORAS-release voor uw besturingssysteem vanuit de [GitHub-opslagplaats](https://github.com/deislabs/oras/releases). Het hulpprogramma wordt vrijgegeven als een gecomprimeerde tarball ( `.tar.gz` bestand). Extraheren en installeren van het bestand met behulp van standaardprocedures voor uw besturingssysteem.
* **Azure Active Directory service-principal (optioneel)** - Als u rechtstreeks wilt verifiëren met ORAS, maakt u een [service-principal](container-registry-auth-service-principal.md) voor toegang tot uw register. Zorg ervoor dat aan de service-principal een rol zoals AcrPush is toegewezen, zodat deze machtigingen heeft om artefacten te pushen en op te halen.
* **Azure CLI (optioneel)** - Als u een afzonderlijke identiteit wilt gebruiken, hebt u een lokale installatie van de Azure CLI nodig. Versie 2.0.71 of hoger wordt aanbevolen. Voer uit `az --version ` om de versie te vinden. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.
* **Docker (optioneel)** : als u een afzonderlijke identiteit wilt gebruiken, moet Docker ook lokaal zijn geïnstalleerd om te verifiëren met het register. Docker biedt pakketten die eenvoudig Docker configureren op elk [macOS][docker-mac]-, [Windows][docker-windows]- of [Linux][docker-linux]-systeem.


## <a name="sign-in-to-a-registry"></a>Aanmelden bij een register

Deze sectie bevat twee voorgestelde werkstromen om u aan te melden bij het register, afhankelijk van de gebruikte identiteit. Kies de methode die geschikt is voor uw omgeving.

### <a name="sign-in-with-oras"></a>Aanmelden met ORAS

Gebruik een [service-principal met](container-registry-auth-service-principal.md) push-rechten en voer de opdracht uit om u aan te melden bij het register met behulp van de toepassings-id en het wachtwoord van `oras login` de service-principal. Geef de volledig gekwalificeerde registernaam op (in kleine letters), in dit geval *myregistry.azurecr.io*. De toepassings-id van de service-principal wordt doorgegeven in de omgevingsvariabele `$SP_APP_ID` en het wachtwoord in de variabele `$SP_PASSWD` .

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Als u het wachtwoord uit Stdin wilt lezen, gebruikt `--password-stdin` u .

### <a name="sign-in-with-azure-cli"></a>Aanmelden met Azure CLI

[Meld u](/cli/azure/authenticate-azure-cli) met uw identiteit aan bij de Azure CLI om artefacten uit het containerregister te pushen en op te halen.

Gebruik vervolgens de Azure CLI-opdracht [az acr login om toegang](/cli/azure/acr#az_acr_login) te krijgen tot het register. Bijvoorbeeld om te verifiëren bij een register met de *naam myregistry*:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login` gebruikt de Docker-client om een Azure Active Directory in te stellen in het `docker.config` bestand. De Docker-client moet zijn geïnstalleerd en worden uitgevoerd om de afzonderlijke verificatiestroom te voltooien.

## <a name="push-an-artifact"></a>Een artefact pushen

Maak een tekstbestand in een lokale werkmap met wat voorbeeldtekst. Bijvoorbeeld in een bash-shell:

```bash
echo "Here is an artifact" > artifact.txt
```

Gebruik de `oras push` opdracht om dit tekstbestand naar het register te pushen. In het volgende voorbeeld wordt het voorbeeldtekstbestand naar de `samples/artifact` repo pusht. Het register wordt geïdentificeerd met de volledig gekwalificeerde *registernaam myregistry.azurecr.io* (in kleine letters). Het artefact heeft het label `1.0` . Het artefact heeft een niet-gedefinieerd type, dat standaard wordt geïdentificeerd door de *mediatypereeks* die volgt op de bestandsnaam `artifact.txt` . Zie [OCI-artefacten](https://github.com/opencontainers/artifacts) voor aanvullende typen. 

**Linux of macOS**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

De uitvoer voor een geslaagde push is vergelijkbaar met het volgende:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Als u artefacten in uw register wilt beheren, voert u, als u de Azure CLI gebruikt, standaardopdrachten `az acr` uit voor het beheren van afbeeldingen. Haal bijvoorbeeld de kenmerken van het artefact op met de [opdracht az acr repository show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

De uitvoer lijkt op het volgende:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Een artefact pullen

Voer de `oras pull` opdracht uit om het artefact uit het register op te halen.

Verwijder eerst het tekstbestand uit uw lokale werkmap:

```bash
rm artifact.txt
```

Voer `oras pull` uit om het artefact op te halen en geef het mediatype op dat wordt gebruikt om het artefact te pushen:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Controleer of de pull is geslaagd:

```bash
$ cat artifact.txt
Here is an artifact
```

## <a name="remove-the-artifact-optional"></a>Het artefact verwijderen (optioneel)

Als u het artefact uit uw Azure-containerregister wilt verwijderen, gebruikt u [de opdracht az acr repository delete.][az-acr-repository-delete] In het volgende voorbeeld wordt het artefact verwijderd dat u daar hebt opgeslagen:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="example-build-docker-image-from-oci-artifact"></a>Voorbeeld: Een Docker-afbeelding maken van een OCI-artefact

Broncode en binaire bestanden voor het bouwen van een containerafbeelding kunnen worden opgeslagen als OCI-artefacten in een Azure-containerregister. U kunt verwijzen naar een bronartefact als de buildcontext voor een [ACR-taak.](container-registry-tasks-overview.md) In dit voorbeeld ziet u hoe u een Dockerfile opgeslagen als een OCI-artefact en vervolgens naar het artefact verwijst om een containerafbeelding te bouwen.

Maak bijvoorbeeld een Dockerfile met één regel:

```bash
echo "FROM mcr.microsoft.com/hello-world" > hello-world.dockerfile
```

Meld u aan bij het doelcontainerregister.

```azurecli
az login
az acr login --name myregistry
```

Maak een nieuw OCI-artefact en push dit naar het doelregister met behulp van de `oras push` opdracht . In dit voorbeeld wordt het standaard mediatype voor het artefact ingesteld.

```bash
oras push myregistry.azurecr.io/dockerfile:1.0 hello-world.dockerfile
```

Voer de [opdracht az acr build](/cli/azure/acr#az_acr_build) uit om de hello-world-afbeelding te bouwen met behulp van het nieuwe artefact als buildcontext:

```azurecli
az acr build --registry myregistry --image builds/hello-world:v1 \
  --file hello-world.dockerfile \
  oci://myregistry.azurecr.io/dockerfile:1.0
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [de ORAS-bibliotheek,](https://github.com/deislabs/oras/tree/master/docs)waaronder het configureren van een manifest voor een artefact
* Ga naar [de OCI Artifacts-repo](https://github.com/opencontainers/artifacts) voor naslaginformatie over nieuwe artefacttypen



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
