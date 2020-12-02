---
title: bestand opnemen
description: bestand opnemen
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/04/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6a16106495e584c9acbc02d380242df665f35ce5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019995"
---
## <a name="push-image-to-registry"></a>Installatiekopie pushen naar register

Als u een installatiekopie naar een Azure Container Registry wilt pushen, moet u eerst over een installatiekopie beschikken. Als u nog geen lokale containerinstallatiekopieën hebt, voert u de volgende opdracht [docker pull][docker-pull] uit om een bestaande installatiekopie op te halen uit Docker Hub. Haal voor dit voorbeeld de installatiekopie `hello-world` op.

```
docker pull hello-world
```

Voordat u een installatiekopie naar het register kunt pushen, moet u deze taggen met de volledige gekwalificeerde naam van de registeraanmeldingsserver. De naam van de aanmeldingsserver heeft de notatie *\<registry-name\>.azurecr.io* (verplicht in kleine letters), bijvoorbeeld *mijncontainerregister.azurecr.io*.

Label de installatiekopie met de opdracht [docker tag][docker-tag]. Vervang `<login-server>` door de aanmeldingsnaam van het ACR-exemplaar.

```
docker tag hello-world <login-server>/hello-world:v1
```

Voorbeeld:

```
docker tag hello-world mycontainerregistry.azurecr.io/hello-world:v1
```


Gebruik ten slotte [docker push][docker-push] om de installatiekopie naar het registerexemplaar te pushen. Vervang `<login-server>` door de aanmeldingsnaam van het registerexemplaar. In dit voorbeeld wordt de **hello-world**-opslagplaats met de `hello-world:v1`-installatiekopie gemaakt.

```
docker push <login-server>/hello-world:v1
```

Nadat u de installatiekopie naar uw containerregister hebt gepusht, verwijdert u de `hello-world:v1`-installatiekopie uit uw lokale Docker-omgeving. (U ziet dat met deze opdracht [docker rmi][docker-rmi] de installatiekopie niet wordt verwijderd uit de **hello-world**-opslagplaats in uw Azure-containerregister.)

```
docker rmi <login-server>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

