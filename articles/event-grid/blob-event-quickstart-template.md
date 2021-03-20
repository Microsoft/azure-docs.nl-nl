---
title: Blob Storage-gebeurtenissen verzenden naar een webeindpunt - sjabloon
description: Gebruik Azure Event Grid en een Azure Resource Manager-sjabloon om een Blob-opslagaccount te maken en u te abonneren op de gebeurtenissen ervan. Verstuur de gebeurtenissen naar een webhook.
ms.date: 07/07/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: bfaee324f3e46f64fd4ad0d8b7e1240331b56c27
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92093647"
---
# <a name="quickstart-route-blob-storage-events-to-web-endpoint-by-using-an-arm-template"></a>Quickstart: Blob Storage-gebeurtenissen naar een webeindpunt routeren met behulp van een ARM-sjabloon

Azure Event Grid is een gebeurtenisservice voor de cloud. In dit artikel gebruikt u een Azure Resource Manager-sjabloon (ARM-sjabloon) om een Blob Storage-account te maken, u te abonneren op de gebeurtenissen voor die blob-opslag en een gebeurtenis te activeren om het resultaat weer te geven. Normaal gesproken verzendt u gebeurtenissen naar een eindpunt dat de gebeurtenisgegevens verwerkt en vervolgens in actie komt. Ter vereenvoudiging van dit artikel stuurt u hier de gebeurtenissen echter naar een web-app die de berichten verzamelt en weergeeft.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

### <a name="create-a-message-endpoint"></a>Het eindpunt van een bericht maken

Voordat u zich kunt abonneren op gebeurtenissen voor de Blob-opslag, moet u het eindpunt voor het gebeurtenisbericht maken. Het eindpunt onderneemt normaal gesproken actie op basis van de gebeurtenisgegevens. Ter vereenvoudiging van deze snelstart gaat u een [vooraf gebouwde web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) implementeren waarmee de gebeurtenisberichten worden weergegeven. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

1. Selecteer **Implementeren in Azure** om de oplossing voor uw abonnement te implementeren. Geef in Azure Portal waarden op voor de parameters.

    [Implementeren naar Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)
1. De implementatie kan enkele minuten duren. Controleer of uw web-app wordt uitgevoerd nadat de implementatie is voltooid. Navigeer in een webbrowser naar: `https://<your-site-name>.azurewebsites.net`

1. De site wordt weergegeven, maar er zijn nog geen gebeurtenissen op gepubliceerd.

   ![Nieuwe site weergeven](./media/blob-event-quickstart-portal/view-site.png)

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/101-event-grid-subscription-and-storage/).

:::code language="json" source="~/quickstart-templates/101-event-grid-subscription-and-storage/azuredeploy.json":::

Er worden twee Azure-resources gedefinieerd in de sjabloon:

* [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): een Azure Storage-account maken.
* [**Microsoft.EventGrid/systemTopics**](/azure/templates/microsoft.eventgrid/systemtopics): een systeemonderwerp maken met de naam die is opgegeven voor het opslagaccount.
* [**Microsoft.EventGrid/systemTopics/eventSubscriptions**](/azure/templates/microsoft.eventgrid/systemtopics/eventsubscriptions): een Azure Event Grid-abonnement maken voor het systeemonderwerp.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende koppeling om u aan te melden bij Azure en open een sjabloon. Met de sjabloon worden een sleutelkluis en een geheim gemaakt.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json)

2. Het **eindpunt opgeven**: geef de URL van uw web-app op en voeg `api/updates` toe aan de URL van de startpagina.
3. Selecteer **Aankoop** om de sjabloon te implementeren.

  Hier wordt de Azure Portal gebruikt om de sjabloon te implementeren. U kunt ook Azure PowerShell, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-powershell.md) voor meer informatie over andere implementatiemethoden.

> [!NOTE]
> U vindt [hier](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid&pageNumber=1&sort=Popular) meer Azure Event Grid-sjabloonvoorbeelden.

## <a name="validate-the-deployment"></a>De implementatie valideren

Bekijk opnieuw uw web-app en u zult zien dat er een validatiegebeurtenis voor een abonnement naartoe is verzonden. Selecteer het oogpictogram om de gebeurtenisgegevens uit te breiden. Via Event Grid wordt de validatiegebeurtenis verzonden zodat het eindpunt kan controleren of de gebeurtenisgegevens in aanmerking komen om ontvangen te worden. De web-app bevat code waarmee het abonnement kan worden gevalideerd.

![Een abonnementgebeurtenis weergeven](./media/blob-event-quickstart-portal/view-subscription-event.png)

Nu gaan we een gebeurtenis activeren om te zien hoe het bericht via Event Grid naar het eindpunt wordt gedistribueerd.

U activeert een gebeurtenis voor de Blob-opslag door een bestand te uploaden. Het bestand heeft geen specifieke inhoud nodig. In de artikelen wordt ervan uitgegaan dat u een bestand met de naam testfile.txt hebt, maar het bestand kan elke willekeurige naam hebben.

Wanneer u het bestand uploadt naar de Azure Blob-opslag, stuurt Event Grid een bericht naar het eindpunt dat u hebt geconfigureerd bij het abonneren. Het bericht heeft de JSON-indeling en bevat een matrix met een of meer gebeurtenissen. In het volgende voorbeeld bevat het JSON-bericht een matrix met één gebeurtenis. Bekijk uw web-app en u ziet dat er een gebeurtenis Blob gemaakt is ontvangen.

![Resultaten weergeven](./media/blob-event-quickstart-portal/view-results.png)

## <a name="clean-up-resources"></a>Resources opschonen

[Verwijder de resourcegroep](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group
) als u deze niet meer nodig hebt.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over de Azure Resource Manager-sjablonen:

* [Documentatie voor Azure Resource Manager](../azure-resource-manager/index.yml)
* [Resources definiëren in Azure Resource Manager-sjablonen](/azure/templates/)
* [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/)
* [Azure Event Grid-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid).
