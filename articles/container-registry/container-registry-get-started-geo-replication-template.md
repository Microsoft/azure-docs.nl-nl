---
title: 'Quickstart: geo-gerepliceerd register maken - Azure Resource Manager-sjabloon'
description: Meer informatie over het maken van een geo-gerepliceerd Azure Container Registry met behulp van een Azure Resource Manager-sjabloon.
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 10/06/2020
ms.openlocfilehash: 97b556e0329644b973def8333ddb5e70e370b0bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91826971"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-an-arm-template"></a>Quickstart: Meer informatie over het maken van een geo-gerepliceerd Azure Container Registry met behulp van een ARM-sjabloon

Deze quickstart laat zien hoe u een Azure Container Registry-exemplaar maakt met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon). Met de sjabloon wordt een [geo-gerepliceerd](container-registry-geo-replication.md) register ingesteld, waarmee registerinhoud automatisch wordt gesynchroniseerd in meerdere Azure-regio's. Geo-replicatie biedt mogelijkheden voor netwerktoegang tot installatiekopieën van regionale implementaties, met een centrale beheerervaring. Het is een functie van de [Premium](container-registry-skus.md)-registerservicelaag.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/). Met de sjabloon wordt een register en een extra regionale replica ingesteld.

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json":::

De volgende resources zijn gedefinieerd in de sjabloon:

* **[Microsoft.ContainerRegistry/registries](/azure/templates/microsoft.containerregistry/registries)** : een Azure Container Registry maken
* **[Microsoft.ContainerRegistry/registries/replications](/azure/templates/microsoft.containerregistry/registries/replications)** : een replica van een containerregister maken

Meer voorbeelden van Azure Container Registry-exemplaren vindt u in de [galerie met quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>De sjabloon implementeren

 1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 1. Typ of selecteer de volgende waarden.

    * **Abonnement**: selecteer een Azure-abonnement.
    * **Resourcegroep**: selecteer **Nieuwe maken**, geef een unieke naam op voor de resourcegroep en selecteer **OK**.
    * **Regio**: selecteer een locatie voor de resourcegroep. Voorbeeld: **VS - centraal**.
    * **ACR-naam**: accepteer de gegenereerde naam voor het register of voer een naam in. De naam moet wereldwijd uniek zijn.
    * **ACR-gebruikersbeheerder ingeschakeld**: accepteer de standaardwaarde.
    * **Locatie**: accepteer de gegenereerde locatie voor de startreplica van het register of voer een locatie in, zoals **VS - centraal**. 
    * **ACR SKU**: accepteer de standaardwaarde.
    * **Locatie van ACR-replica**: voer een locatie in voor de replica van het register, met behulp van de korte naam van de regio. Dit moet een andere locatie zijn dan de locatie van het startregister. Voorbeeld: **EU - west**.

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="Sjablooneigenschappen":::

1. Selecteer **Beoordelen + maken** en controleer vervolgens de voorwaarden. Als u akkoord gaat, selecteert u **Maken**.

1. Nadat het register is gemaakt, krijgt u een melding:

     :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="Melding in de portal":::

 Voor het implementeren van de sjabloon wordt de Azure-portal gebruikt. Naast de Azure-portal kunt u de Azure PowerShell, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-cli.md) voor meer informatie over andere implementatiemethoden.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Gebruik de Azure-portal of een hulpprogramma zoals de Azure CLI om de eigenschappen van het containerregister te controleren.

1. Zoek in de portal naar containerregisters en selecteer het containerregister dat u hebt gemaakt.

1. Op de pagina **Overzicht** noteert u de **aanmeldingsserver** van het register. Gebruik deze URI wanneer u Docker gebruikt om installatiekopieën te taggen en te pushen naar het register. Zie [Uw eerste installatiekopie pushen met de Docker-CLI](container-registry-get-started-docker-cli.md) voor meer informatie.

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="Overzicht van register":::

1. Controleer op de pagina **Replicaties** de locaties van de startreplica en de replica die u via de sjabloon hebt toegevoegd. Voeg desgewenst meer replica's toe op deze pagina.

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="Registerreplicaties":::

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, verwijdert u de resourcegroep, het register en de replica van het register. Hiertoe gaat u naar de Azure-portal, selecteert u de resourcegroep die het register bevat en selecteert u vervolgens **Resourcegroep verwijderen**.

Resourcegroep verwijderen

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Azure Container Registry gemaakt met behulp van een ARM-sjabloon. Daarnaast hebt u een registerreplica op een andere locatie geconfigureerd. Ga verder met de zelfstudies voor Azure Container Registry om meer te leren over ACR.

> [!div class="nextstepaction"]
> [Azure Container Registry-zelfstudies](container-registry-tutorial-prepare-registry.md)

Zie voor een stapsgewijze zelfstudie die u door het proces van het maken van een sjabloon leidt:

> [!div class="nextstepaction"]
> [Zelfstudie: Uw eerste ARM-sjabloon maken en implementeren](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
