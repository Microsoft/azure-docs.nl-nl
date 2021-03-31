---
title: 'Quickstart: een Docker-container implementeren in een containerinstantie - Portal'
description: In deze quickstart gebruikt u Azure Portal om snel een in een container geplaatste web-app te implementeren die wordt uitgevoerd in een geïsoleerde Azure-containerinstantie
ms.topic: quickstart
ms.date: 08/24/2020
ms.custom: seodec18, mvc, devx-track-js
ms.openlocfilehash: c8477bd91c3a02a2cd02d341c38c16da251902ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004803"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Quickstart: Een containerinstantie in Azure implementeren met behulp van Azure Portal

Gebruik Azure Container Instances om snel en eenvoudig serverloze Docker-containers uit te voeren in Azure. Een toepassing implementeren in een containerinstantie op aanvraag, wanneer u geen volledig indelingsplatform voor containers nodig hebt zoals Azure Kubernetes Service.

In deze quickstart gebruikt u Azure Portal om een geïsoleerde Docker-container te implementeren en maakt u de bijbehorende toepassing beschikbaar met een FQDN (Fully Qualified Domain Name). Na het configureren van een paar instellingen en de implementatie van de container kunt u bladeren naar de toepassing die wordt uitgevoerd:

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="App die is geïmplementeerd met Azure Container Instances, weergegeven in de browser":::

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account][azure-free-account] aan voordat u begint.

## <a name="create-a-container-instance"></a>Een containerinstantie maken

Selecteer **Een nieuwe resource maken** > **Containers** > **Container Instances**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-01.png" alt-text="Begin met het maken van een nieuwe containerinstantie in Azure Portal":::

Voer op de pagina **Basisgegevens** de volgende waarden in voor de tekstvakken **Resourcegroep**, **Containernaam** en **Containerinstallatiekopie**. Laat de andere waarden op de standaardwaarden staan en selecteer vervolgens **OK**.

* Resourcegroep: **Nieuwe maken** > `myresourcegroup`
* Containernaam: `mycontainer`
* Bron van installatiekopie: **Quickstart-installatiekopieën**
* Containerinstallatiekopie: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-03.png" alt-text="Basisinstellingen configureren voor een nieuwe containerinstantie in Azure Portal":::

Voor deze quickstart gebruikt u de standaardinstellingen voor het implementeren van de openbare Microsoft `aci-helloworld`-installatiekopie. Dit voorbeeld van een Linux-installatiekopie bevat een kleine web-app die is geschreven in Node.js en die een statische HTML-pagina dient. U kunt ook uw eigen containerinstallatiekopieën gebruiken die zijn opgeslagen in Azure Container Registry, Docker Hub of andere registers.

Geef op de pagina **Netwerken** een **DNS-naamlabel** op voor uw container. De naam moet uniek zijn voor de Azure-regio waarin u de containerinstantie maakt. De container zal openbaar bereikbaar zijn op `<dns-name-label>.<region>.azurecontainer.io`. Als u een foutbericht 'DNS-naamlabel niet beschikbaar' ontvangt, probeert u een ander DNS-naamlabel.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-04.png" alt-text="Netwerkinstellingen configureren voor een nieuwe containerinstantie in Azure Portal":::

Laat de standaardwaarden van de andere instellingen ongewijzigd en selecteer vervolgens **Controleren en maken**.

Wanneer de validatie is voltooid, ziet u een overzicht van de containerinstellingen. Selecteer **Maken** om de aanvraag voor de containerimplementatie te verzenden.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-05.png" alt-text="Overzicht van de instellingen voor een nieuwe containerinstantie in Azure Portal":::

Wanneer de implementatie is gestart, wordt een melding weergegeven die aangeeft dat de implementatie wordt uitgevoerd. Er wordt nog een melding weergegeven wanneer de containergroep is geïmplementeerd.

Open het overzicht voor de containergroep door naar **Resourcegroepen** > **myresourcegroup** > **mycontainer** te navigeren. Noteer de **FQDN** (de FQDN-naam) van de container-instantie, evenals de **Status**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-06.png" alt-text="Overzicht van containergroepen in Azure Portal":::

Wanneer de **Status** eenmaal *Uitvoeren* is, gaat u naar de FQDN van de container in uw browser.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="App die is geïmplementeerd met Azure Container Instances, weergegeven in de browser":::

Gefeliciteerd! Als u slechts enkele instellingen configureert, hebt u een openbaar toegankelijke toepassing in Azure Container Instances geïmplementeerd.

## <a name="view-container-logs"></a>Containerlogboeken ophalen

Het weergeven van de logboeken voor een exemplaar van de container is handig bij het oplossen van problemen met de container of de toepassing die wordt uitgevoerd.

Selecteer om de container-logboeken weer te geven onder **Instellingen** de optie **Containers** en vervolgens **Logboeken**. De HTTP GET-aanvraag wordt als het goed is gegenereerd wanneer u de toepassing in uw browser hebt bekeken.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-11.png" alt-text="Container-logboeken in Azure Portal":::


## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u met de container klaar bent, selecteert u **Overzicht** voor de container-instantie *mycontainer* en vervolgens **Verwijderen**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-09.png" alt-text="De containerinstantie verwijderen in de Azure Portal":::

Selecteer **Ja** als het bevestigingsvenster verschijnt.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-10.png" alt-text="Bevestiging van het verwijderen van een containerinstantie in de Azure Portal":::

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een Azure-containerinstantie gemaakt van een openbare Microsoft-installatiekopie. Als u zelf een containerinstallatiekopie wilt bouwen en deze wilt implementeren met behulp van een privé Azure Container-register, gaat u verder met de zelfstudie voor Azure Container Instances.

> [!div class="nextstepaction"]
> [Zelfstudie voor Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/