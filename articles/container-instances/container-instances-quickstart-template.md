---
title: 'Quickstart: een containerinstantie maken - Azure Resource Manager-sjabloon'
description: In deze snelstart gebruikt u een Azure Resource Manager-sjabloon om snel een container-web-app te implementeren die op een geïsoleerde Azure-containerinstantie wordt uitgevoerd.
services: azure-resource-manager
ms.date: 04/30/2020
ms.topic: quickstart
ms.service: azure-resource-manager
ms.custom:
- subject-armqs
- devx-track-js
- mode-arm
ms.openlocfilehash: 5e8b9e1b2a396bb20b35dd69efe26758ba0db543
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537504"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-an-arm-template"></a>Quickstart: Een containerinstantie in Azure implementeren met behulp van een ARM-sjabloon

Gebruik Azure Container Instances om snel en eenvoudig serverloze Docker-containers uit te voeren in Azure. Een toepassing implementeren in een containerinstantie op aanvraag, wanneer u geen volledig indelingsplatform voor containers nodig hebt zoals Azure Kubernetes Service. In deze snelstart gebruikt u een Azure Resource Manager-sjabloon (ARM-sjabloon) om een geïsoleerde Docker-container te implementeren en de webtoepassing ervan beschikbaar te maken met een openbaar IP-adres.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aci-linuxcontainer-public-ip%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-aci-linuxcontainer-public-ip/).

:::code language="json" source="~/quickstart-templates/101-aci-linuxcontainer-public-ip/azuredeploy.json":::

De volgende resource is gedefinieerd in de sjabloon:

* **[Microsoft.ContainerInstance/containerGroups](/azure/templates/microsoft.containerinstance/containergroups)** : een Azure-containergroep maken. Met deze sjabloon wordt een groep gedefinieerd die uit één containerinstantie bestaat.

Meer voorbeelden van Azure Container Instances vindt u in de [galerie met snelstartsjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerinstance&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>De sjabloon implementeren

 1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. De sjabloon maakt een register en een replica op een andere locatie.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aci-linuxcontainer-public-ip%2Fazuredeploy.json)

 2. Typ of selecteer de volgende waarden.

    * **Abonnement**: selecteer een Azure-abonnement.
    * **Resourcegroep**: selecteer **Nieuwe maken**, geef een unieke naam op voor de resourcegroep en selecteer **OK**.
    * **Locatie**: selecteer een locatie voor de resourcegroep. Voorbeeld: **VS - centraal**.
    * **Naam**: accepteer de gegenereerde naam voor de instantie of voer een naam in.
    * **Installatiekopie**: accepteer de standaardnaam van de installatiekopie. Dit voorbeeld van een Linux-installatiekopie bevat een kleine web-app die is geschreven in Node.js en die een statische HTML-pagina dient. 

    Accepteer de standaardwaarden voor de overige eigenschappen.

    Bekijk de voorwaarden. Selecteer **Ik ga akkoord met de bovenstaande voorwaarden** als u akkoord gaat.

    ![Sjablooneigenschappen](media/container-instances-quickstart-template/template-properties.png)

 3. Nadat de instantie is gemaakt, krijgt u een melding:

    ![Melding in de portal](media/container-instances-quickstart-template/deployment-notification.png)

 Voor het implementeren van de sjabloon wordt de Azure-portal gebruikt. Naast de Azure-portal kunt u de Azure PowerShell, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-cli.md) voor meer informatie over andere implementatiemethoden.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Gebruik de Azure-portal of een hulpprogramma zoals de [Azure CLI](container-instances-quickstart.md) om de eigenschappen van de containerinstantie te controleren.

1. Zoek in de portal naar Container Instances en selecteer de containerinstantie die u hebt gemaakt.

1. Op de pagina **Overzicht** ziet u de **Status** van de instantie en het **IP-adres**.

    ![Overzicht van instantie](media/container-instances-quickstart-template/aci-overview.png)

2. Wanneer de Status eenmaal *Uitvoeren* is, gaat u naar het IP-adres in uw browser. 

    ![App die is geïmplementeerd met Azure Container Instances, weergegeven in de browser](media/container-instances-quickstart-template/view-application-running-in-an-azure-container-instance.png)

### <a name="view-container-logs"></a>Containerlogboeken ophalen

Het weergeven van de logboeken voor een exemplaar van de container is handig bij het oplossen van problemen met de container of de toepassing die wordt uitgevoerd.

Selecteer om de containerlogboeken weer te geven onder **Instellingen** de optie **Containers** > **Logboeken**. De HTTP GET-aanvraag wordt als het goed is gegenereerd wanneer u de toepassing in uw browser hebt bekeken.

![Container-logboeken in Azure Portal](media/container-instances-quickstart-template/aci-logs.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de container, selecteert u op de pagina **Overzicht** voor de containerinstantie de optie **Verwijderen**. Bevestig de verwijdering als u daarom wordt gevraagd.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een Azure-containerinstantie gemaakt van een openbare Microsoft-installatiekopie. Als u zelf een containerinstallatiekopie wilt bouwen en deze wilt implementeren met behulp van een privé Azure Container-register, gaat u verder met de zelfstudie voor Azure Container Instances.

> [!div class="nextstepaction"]
> [Zelfstudie: een containerinstallatiekopie voor implementatie in Azure Container Instances maken](./container-instances-tutorial-prepare-app.md)

Zie voor een stapsgewijze zelfstudie die u door het proces van het maken van een sjabloon leidt:

> [!div class="nextstepaction"]
> [Zelfstudie: Uw eerste ARM-sjabloon maken en implementeren](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
