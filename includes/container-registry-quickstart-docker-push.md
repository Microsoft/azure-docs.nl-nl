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
ms.openlocfilehash: 404fd10c3e3610671d2b6e5dbc6aba8bcaa70046
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100019546"
---
## <a name="push-image-to-registry"></a>Installatiekopie pushen naar register

Als u een installatiekopie naar een Azure Container Registry wilt pushen, moet u eerst over een installatiekopie beschikken. Als u nog geen lokale container installatie kopieën hebt, voert u de volgende opdracht [docker pull][docker-pull] uit om een bestaande open bare installatie kopie te halen. Voor dit voor beeld haalt u de `hello-world` installatie kopie op uit micro soft container Registry.

```
docker pull mcr.microsoft.com/hello-world
```

Voordat u een installatiekopie naar het register kunt pushen, moet u deze taggen met de volledige gekwalificeerde naam van de registeraanmeldingsserver. De naam van de aanmeldingsserver heeft de notatie *\<registry-name\>.azurecr.io* (verplicht in kleine letters), bijvoorbeeld *mijncontainerregister.azurecr.io*.

Label de installatiekopie met de opdracht [docker tag][docker-tag]. Vervang `<login-server>` door de aanmeldingsnaam van het ACR-exemplaar.

```
docker tag mcr.microsoft.com/hello-world <login-server>/hello-world:v1
```

Voorbeeld:

```
docker tag mcr.microsoft.com/hello-world mycontainerregistry.azurecr.io/hello-world:v1
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

