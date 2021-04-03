---
title: Quickstart - Een Azure API Management-exemplaar maken met behulp van een Azure Resource Manager-sjabloon
description: Meer informatie over het maken van een Azure API Management-exemplaar in de laag voor ontwikkelaars met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon).
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 10/09/2020
ms.openlocfilehash: 1610c51b613712c06410247e2eb673a50a9988b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792239"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-an-arm-template"></a>Quickstart: Een nieuw exemplaar van het Azure API Management-service-exemplaar maken met behulp van een Resource Manager-sjabloon

In deze quickstart wordt beschreven hoe u een Azure Resource Manager-sjabloon (ARM-sjabloon) gebruikt om een Azure API Management-service-exemplaar (APIM) te maken. API Management helpt organisaties bij het publiceren van API's naar externe, partner- en interne ontwikkelaars om het potentieel van hun gegevens en services te ontsluiten. API Management beschikt over de competenties die belangrijk zijn voor een geslaagd API-programma via ontwikkelaarsbetrokkenheid, zakelijke inzichten, analytische gegevens, beveiliging en bescherming. Met APIM kunt u moderne API-gateways maken en beheren voor bestaande back-endservices die op elke willekeurige locatie worden gehost. Zie het [overzicht](api-management-key-concepts.md) voor meer informatie.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-azure-api-management-create/).

:::code language="json" source="~/quickstart-templates/101-azure-api-management-create/azuredeploy.json":::

De volgende resource is gedefinieerd in de sjabloon:

- **[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service)**

In [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Apimanagement&pageNumber=1&sort=Popular) vindt u meer voorbeelden van Azure API Management-sjablonen.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon wordt een API Management service-exemplaar gemaakt met een automatisch gegenereerde naam.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

    In dit voorbeeld wordt het exemplaar geconfigureerd in de ontwikkelaarslaag, een voordelige optie om Azure API Management te evalueren. Deze laag is niet bedoeld voor productie. Zie [bijwerken en schalen](upgrade-and-scale.md) voor meer informatie over het schalen van API Management-lagen.

1. Typ of selecteer de volgende waarden.
    - **Abonnement**: selecteer een Azure-abonnement.
    - **Resourcegroep**: selecteer **Nieuwe maken**, geef een unieke naam op voor de resourcegroep en selecteer **OK**.
    - **Regio**: selecteer een locatie voor de resourcegroep. Voorbeeld: **VS - centraal**.
    - **E-mail van uitgever**: voer een e-mailadres in om meldingen te ontvangen.
    - **Naam van uitgever**: voer een naam in die u voor de API-uitgever hebt gekozen.
    - **SKU**: accepteer de standaardwaarde van **Ontwikkelaar**.
    - **Aantal SKU's**: accepteer de standaardwaarde.
    - **Locatie**: accepteer de gegenereerde locatie voor de API Management-service.

    :::image type="content" source="media/quickstart-arm-template/create-instance-template.png" alt-text="Eigenschappen van API Management-sjabloon":::

1. Selecteer **Beoordelen + maken** en controleer vervolgens de voorwaarden. Als u akkoord gaat, selecteert u **Maken**.

    > [!TIP]
    >  Het kan tussen de dertig en veertig minuten duren om een API Management-service in de ontwikkelaarslaag te maken en activeren.

1. Nadat de instantie is gemaakt, krijgt u een melding:

    :::image type="content" source="media/quickstart-arm-template/deployment-notification.png" alt-text="Implementatiemelding":::

 Voor het implementeren van de sjabloon wordt de Azure-portal gebruikt. Naast Azure Portal kunt u ook de Azure PowerShell, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-cli.md) voor meer informatie over andere implementatiemethoden.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Gebruik de Azure-portal om de geïmplementeerde resources te controleren of gebruik hulpprogramma's zoals Azure CLI of een Azure PowerShell om de geïmplementeerde resources weer te geven.

1. In de [Azure-portal](https://portal.azure.com)zoekt en selecteert u **API Management-services** en selecteert u het service-exemplaar dat u hebt gemaakt.
1. Controleer de eigenschappen van uw service op de pagina **Overzicht**.

:::image type="content" source="media/quickstart-arm-template/service-instance-created.png" alt-text="Overzichtspagina van service":::

Als het API Management-service-exemplaar online is, kunt u het gebruiken. Begin met de zelfstudie om uw eerste API te [importeren en publiceren](import-and-publish.md).

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om verder te gaan met de volgende zelfstudies, kunt u het API Management-exemplaar het beste laten staan. Als u de resourcegroep niet meer nodig hebt, verwijdert u deze. Hierdoor worden ook de resources in de resourcegroep verwijderd.

1. Zoek en selecteer [Resourcegroepen](https://portal.azure.com) in **Azure Portal**. U kunt ook **Resourcegroepen** selecteren op de **Start** pagina.
1. Selecteer uw resourcegroep op de pagina **Resourcegroepen**.
1. Selecteer **Resourcegroep verwijderen** op de pagina van de resourcegroep.

    Resourcegroep verwijderen
1. Typ de naam van uw resourcegroep en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Uw eerste API importeren en publiceren](import-and-publish.md)
