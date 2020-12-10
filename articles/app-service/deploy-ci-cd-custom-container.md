---
title: CI/CD naar aangepaste Linux-containers
description: Meer informatie over het instellen van continue implementatie naar een aangepaste Linux-container in Azure App Service. Continue implementatie wordt ondersteund voor docker hub en ACR.
keywords: Azure app service, Linux, docker, ACR, oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: e879d8370821ea465147e344d0fe95836c843ff4
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008007"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Doorlopende implementatie met Web App for Containers

In deze zelf studie configureert u continue implementatie voor een aangepaste container installatie kopie van beheerde [Azure container Registry](https://azure.microsoft.com/services/container-registry/) -opslag plaatsen of [docker-hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Continue implementatie met ACR inschakelen

![Scherm opname van ACR-webhook](./media/deploy-ci-cd-custom-container/ci-cd-acr-02.png)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer de optie **app service** aan de linkerkant van de pagina.
3. Selecteer de naam van de app waarvoor u een continue implementatie wilt configureren.
4. Selecteer op de pagina **container instellingen** de optie **Eén container**
5. **Azure container Registry** selecteren
6. Selecteer **doorlopende implementatie > op**
7. Selecteer **Opslaan** om continue implementatie in te scha kelen.

## <a name="use-the-acr-webhook"></a>De ACR-webhook gebruiken

Zodra de continue implementatie is ingeschakeld, kunt u de zojuist gemaakte webhook weer geven op de pagina webhooks van Azure Container Registry.

![Scherm afbeelding die laat zien waar u de zojuist gemaakte webhook kunt weer geven op de pagina webhooks van Azure Container Registry.](./media/deploy-ci-cd-custom-container/ci-cd-acr-03.png)

Klik in uw Container Registry op webhooks om de huidige webhooks weer te geven.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Continue implementatie met docker hub inschakelen (optioneel)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer de optie **app service** aan de linkerkant van de pagina.
3. Selecteer de naam van de app waarvoor u een continue implementatie wilt configureren.
4. Selecteer op de pagina **container instellingen** de optie **Eén container**
5. **Docker hub** selecteren
6. Selecteer **doorlopende implementatie > op**
7. Selecteer **Opslaan** om continue implementatie in te scha kelen.

![Scherm afbeelding van de app-instelling](./media/deploy-ci-cd-custom-container/ci-cd-docker-02.png)

Kopieer de webhook-URL. Als u een webhook voor docker hub wilt toevoegen, volgt u <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">webhooks voor docker hub</a>.

## <a name="automate-with-cli"></a>Automatiseren met CLI

Voor het configureren van CI/CD met behulp van de Azure CLI voert u de opdracht [AZ webapp Deployment container config](/cli/azure/webapp/deployment/container#az-webapp-deployment-container-config) uit om de webhook-URL te genereren. De URL kan worden gebruikt om uw DockerHub of Azure Container Registry te configureren.

```azurecli-interactive
az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true
```

## <a name="next-steps"></a>Volgende stappen

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Een .NET Core-web-app maken in App Service in Linux](quickstart-dotnetcore.md?pivots=platform-linux)
* [Een Ruby-Web-app maken in App Service in Linux](quickstart-ruby.md)
* [Quickstart: Een aangepaste container uitvoeren op App Service](quickstart-custom-container.md?pivots=container-linux)
* [Veelgestelde vragen over App Service op Linux](faq-app-service-linux.md)
* [Aangepaste Linux-containers configureren](configure-custom-container.md)
