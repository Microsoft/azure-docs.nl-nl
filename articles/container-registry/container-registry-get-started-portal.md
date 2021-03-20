---
title: 'Quickstart: Register maken in de portal'
description: Leer snel een persoonlijk Azure-containerregister maken met de Azure-portal.
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: ff74232edf5c96f091082f8486c44fed6125b0b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97825838"
---
# <a name="quickstart-create-an-azure-container-registry-using-the-azure-portal"></a>Quickstart: Een Azure-containerregister maken met de Azure-portal

Een Azure-containerregister is een persoonlijk Docker-register in Azure waar u uw persoonlijke installatiekopieën van de Docker-container en verwante artefacten kunt opslaan en beheren. In deze quickstart maakt u een containerregister in Azure Portal. Gebruik vervolgens Docker-opdrachten om een containerinstallatiekopie naar het register pushen, waarna u de installatiekopie ophaalt en uitvoert vanuit het register.

Als u zich wilt aanmelden bij het register om met containerinstallatiekopieën te werken, moet u in deze quickstart de Azure CLI uitvoeren (versie 2.0.55 of hoger wordt aanbevolen). Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Docker moet ook lokaal zijn geïnstalleerd. Docker biedt pakketten die eenvoudig Docker op elke configureren op elk [Mac][docker-mac]-, [Windows][docker-windows]- of [Linux][docker-linux]-systeem.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-container-registry"></a>Een containerregister maken

Selecteer **Een resource maken** > **Containers** > **Container Registry**.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="Navigeren naar containerregister in portal":::

Voer op het tabblad **Basisbeginselen** de waarden in voor **Resourcegroep** en **Registernaam**. De registernaam moet uniek zijn binnen Azure en mag 5 tot 50 alfanumerieke tekens bevatten. Maak voor deze snelstart een nieuwe resourcegroep met de naam `myResourceGroup` in locatie `West US`. Kies voor **SKU** de optie Basic.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="Containerregister maken in de portal":::

Accepteer de standaardwaarden voor de overige instellingen. Selecteer vervolgens **Controleren en maken**. Nadat u de instellingen hebt bekeken, selecteert u **Maken**.

In deze quickstart maakt u een *Basic*-register. Dit is een voor kosten geoptimaliseerde optie voor ontwikkelaars die meer willen leren over Azure Container Registry. Zie [Servicelagen voor containerregister][container-registry-skus] voor meer informatie over de beschikbare servicelagen (SKU's).

Als het bericht **Implementatie voltooid** wordt weergegeven, selecteert u het containerregister in de portal. 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="Overzicht van containerregisters in de portal":::

Noteer de registernaam en de waarde van de **aanmeldingsserver**. U gebruikt deze waarden in de volgende stappen wanneer u installatiekopieën pusht en pullt met Docker.

## <a name="log-in-to-registry"></a>Aanmelden bij register

Voordat u push- en pull-bewerkingen voor containerinstallatiekopieën kunt gebruiken, moet u zich aanmelden bij de registerinstantie. [Meld u aan bij Azure CLI][get-started-with-azure-cli] op uw lokale computer en voer vervolgens de opdracht [az acr login][az-acr-login] uit. Geef alleen de registernaam op wanneer u zich aanmeldt bij Azure CLI. Gebruik niet de naam van de aanmeldingsserver, met inbegrip van een domeinachtervoegsel zoals `azurecr.io`.

```azurecli
az acr login --name <registry-name>
```

Voorbeeld:

```azurecli
az acr login --name mycontainerregistry
```

De opdracht retourneert `Login Succeeded` nadat deze is voltooid. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Containerinstallatiekopieën opvragen

Als u de installatiekopieën in uw register wilt weergeven, gaat u naar het register in de portal en selecteert u **Opslagplaatsen**. Selecteer vervolgens de opslagplaats **hello-world** die u met `docker push` hebt gemaakt.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="Lijst met containerinstallatiekopieën maken in de portal":::

Wanneer u de opslagplaats **hello-world** selecteert, ziet u de met `v1` getagde installatiekopie bij **Tags**.

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u uw resources wilt opschonen, navigeert u naar de resourcegroep **myResourceGroup** in de portal. Wanneer de resourcegroep is geladen, klikt u op **Resourcegroep verwijderen** om de resourcegroep, het containerregister en alle daarin opgeslagen containerinstallatiekopieën te verwijderen.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="Een resourcegroep verwijderen in de portal":::


## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Azure Container Registry gemaakt met de Azure-portal. U hebt een containerinstallatiekopie gepusht en de installatiekopie uit het register opgehaald en uitgevoerd. Ga verder met de zelfstudies voor Azure Container Registry om meer te leren over ACR.

> [!div class="nextstepaction"]
> [Azure Container Registry-zelfstudies][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Zelfstudies over Azure Container Registry-taken][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
