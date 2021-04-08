---
title: Een web-app implementeren met een sjabloon-Azure Cosmos DB
description: Meer informatie over het implementeren van een Azure Cosmos-account, Azure App Service Web Apps en een voor beeld-webtoepassing met behulp van een Azure Resource Manager-sjabloon.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mjbrown
ms.openlocfilehash: 55d58a6c4724bd01325db029ed75d77ccc96d0f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93333576"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-with-a-web-app-from-github-using-an-azure-resource-manager-template"></a>Azure Cosmos DB en Azure App Service met een web-app implementeren vanuit GitHub met behulp van een Azure Resource Manager-sjabloon
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In deze zelf studie leert u hoe u een "No touch"-implementatie uitvoert van een webtoepassing die verbinding maakt met Azure Cosmos DB bij de eerste uitvoering zonder dat u verbindings gegevens van Azure Cosmos DB naar `appsettings.json` of naar de toepassings instellingen van Azure-app Services in de Azure Portal hoeft te knippen en plakken. Al deze acties worden uitgevoerd met behulp van een Azure Resource Manager sjabloon in één bewerking. In dit voor beeld implementeren we het [Azure Cosmos DB TODO](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) -voor beeld van een [Web-app-zelf studie](sql-api-dotnet-application.md).

Resource Manager-sjablonen zijn zeer flexibel en bieden u de mogelijkheid om complexe implementaties uit te stellen voor elke service in Azure. Dit omvat geavanceerde taken zoals het implementeren van toepassingen van GitHub en het invoegen van verbindings gegevens in de toepassings instellingen van Azure App Service in de Azure Portal. In deze zelf studie leert u hoe u de volgende zaken kunt doen met één resource manager-sjabloon.

* Implementeer een Azure Cosmos-account.
* Implementeer een Azure App Service-hosting abonnement.
* Implementeer een Azure App Service.
* Injecteer het eind punt en de sleutels van het Azure Cosmos-account in de App Service toepassings instellingen in de Azure Portal.
* Implementeer een webtoepassing vanuit een GitHub-opslag plaats naar de App Service.

De resulterende implementatie heeft een volledig functionele webtoepassing die verbinding kan maken met Azure Cosmos DB zonder dat u de eind punt-URL of verificatie sleutels van de Azure Cosmos DB van de Azure Portal hoeft te knippen en plakken.

## <a name="prerequisites"></a>Vereisten

> [!TIP]
> Hoewel in deze zelf studie geen eerdere ervaring met Azure Resource Manager sjablonen of JSON wordt uitgegaan, moet u de sjablonen of implementatie-opties waarnaar wordt verwezen, wijzigen. vervolgens is kennis van elk van deze gebieden vereist.

## <a name="step-1-deploy-the-template"></a>Stap 1: de sjabloon implementeren

Selecteer eerst de onderstaande knop **implementeren naar Azure** om de Azure portal te openen voor het maken van een aangepaste implementatie. U kunt ook de Azure resource management-sjabloon bekijken in de [Galerie met Azure Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp) start-sjablonen

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementeren in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-webapp%2Fazuredeploy.json)

Selecteer in de Azure Portal het abonnement dat u wilt implementeren en selecteer of maak een nieuwe resource groep. Vul vervolgens de volgende waarden in.

:::image type="content" source="./media/create-website/template-deployment.png" alt-text="Scherm afbeelding van de gebruikers interface van de sjabloon implementatie":::

* **Regio** : dit is vereist voor de Resource Manager. Voer dezelfde regio in die wordt gebruikt door de locatie parameter waar uw resources zich bevinden.
* **Toepassings naam** : deze naam wordt gebruikt door alle resources voor deze implementatie. Zorg ervoor dat u een unieke naam kiest om conflicten met bestaande Azure Cosmos DB-en App Service-accounts te voor komen.
* **Locatie** : de regio waar uw resources worden geïmplementeerd.
* De prijs categorie voor het App Service abonnement **app service** plan.
* **App service plan exemplaren** : het aantal werk rollen voor het app service-plan.
* **URL van de opslag plaats** : de opslag plaats naar de webtoepassing op github.
* **Branch** : de vertakking voor de GitHub-opslag plaats.
* **Database naam** : de naam van de Azure Cosmos-data base.
* **Container naam** -de naam van de Azure Cosmos-container.

Nadat u de waarden hebt ingevuld, selecteert u de knop **maken** om de implementatie te starten. Deze stap moet vijf tot tien minuten duren.

> [!TIP]
> De sjabloon valideert niet dat de Azure App Service naam en de naam van het Azure Cosmos-account die in de sjabloon is opgegeven, geldig zijn en beschikbaar zijn. Het is raadzaam om de beschik baarheid te controleren van de namen die u wilt leveren voordat u de implementatie verzendt.


## <a name="step-2-explore-the-resources"></a>Stap 2: de bronnen verkennen

### <a name="view-the-deployed-resources"></a>De geïmplementeerde resources bekijken

Wanneer de resources zijn geïmplementeerd met de sjabloon, kunt u deze nu weer geven in de resource groep.

:::image type="content" source="./media/create-website/resource-group.png" alt-text="Resourcegroep":::

### <a name="view-cosmos-db-endpoint-and-keys"></a>Cosmos DB-eind punt en-sleutels weer geven

Open vervolgens het Azure Cosmos-account in de portal. De volgende scherm afbeelding toont het eind punt en de sleutels voor een Azure Cosmos-account.

:::image type="content" source="./media/create-website/cosmos-keys.png" alt-text="Cosmos-sleutels":::

### <a name="view-the-azure-cosmos-db-keys-in-application-settings"></a>De Azure Cosmos DB sleutels in toepassings instellingen weer geven

Ga vervolgens naar de Azure App Service in de resource groep. Klik op het tabblad Configuratie om de toepassings instellingen voor de App Service weer te geven. De toepassings instellingen bevatten het Cosmos DB account en de primaire sleutel waarden die nodig zijn om verbinding te maken met Cosmos DB, evenals de naam van de data base en de container die in de sjabloon implementatie zijn door gegeven.

:::image type="content" source="./media/create-website/application-settings.png" alt-text="Toepassingsinstellingen":::

### <a name="view-web-app-in-deployment-center"></a>Web-app weer geven in implementatie centrum

Ga vervolgens naar het implementatie centrum voor de App Service. Hier ziet u opslag plaatsen naar de GitHub-opslag plaats die is door gegeven aan de sjabloon. De onderstaande status duidt ook op geslaagd (actief), wat betekent dat de toepassing is geïmplementeerd en gestart.

:::image type="content" source="./media/create-website/deployment-center.png" alt-text="Implementatiecenter":::

### <a name="run-the-web-application"></a>De webtoepassing uitvoeren

Klik op **Bladeren** bovenaan in Deployment Center om de webtoepassing te openen. De webtoepassing wordt geopend in het Start scherm. Klik op **nieuwe maken** en voer enkele gegevens in de velden in en klik op opslaan. In het scherm worden de gegevens weer gegeven die zijn opgeslagen in Cosmos DB.

:::image type="content" source="./media/create-website/app-home-screen.png" alt-text="Start scherm":::

## <a name="step-3-how-does-it-work"></a>Stap 3: Hoe werkt het?

Er zijn drie elementen die nodig zijn om dit te laten werken.

### <a name="reading-app-settings-at-runtime"></a>App-instellingen lezen tijdens runtime

Ten eerste moet de toepassing het Cosmos DB-eind punt en de sleutel aanvragen in de- `Startup` klasse in de ASP.NET MVC-webtoepassing. Het [Cosmos DB te doen-voor beeld](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) kan lokaal worden uitgevoerd, waarbij u de verbindings gegevens in appsettings.jskunt invoeren. Wanneer dit bestand is geïmplementeerd, wordt het echter met de app geïmplementeerd. Als deze regels in rood geen toegang hebben tot de instellingen van appsettings.jsop, wordt het programma vanuit toepassings instellingen in Azure App Service geprobeerd.

:::image type="content" source="./media/create-website/startup.png" alt-text="Scherm afbeelding toont een methode met verschillende teken reeks variabelen die zijn gemarkeerd in rood, waaronder DATABASENAME, containernaam, account en sleutel.":::

### <a name="using-special-azure-resource-management-functions"></a>Speciale functies van Azure Resource Management gebruiken

Deze waarden zijn alleen beschikbaar voor de toepassing wanneer deze worden geïmplementeerd, maar de Azure Resource Manager-sjabloon kan om deze waarden van het Cosmos DB account vragen met behulp van speciale Azure resource management-functies, inclusief [referentie](../azure-resource-manager/templates/template-functions-resource.md#reference) -en [listkeys ophalen](../azure-resource-manager/templates/template-functions-resource.md#listkeys) die de waarden van het Cosmos DB-account ophaalt en ze invoegen in de waarden van de toepassings instellingen met sleutel namen die overeenkomen met wat wordt gebruikt in de bovenstaande toepassing in een Bijvoorbeeld `CosmosDb:Account`.

:::image type="content" source="./media/create-website/template-keys.png" alt-text="Sjabloon sleutels":::

### <a name="deploying-web-apps-from-github"></a>Web-Apps implementeren vanuit GitHub

Ten slotte moeten we de webtoepassing implementeren vanuit GitHub in de App Service. Dit wordt gedaan met behulp van de JSON hieronder. Er zijn twee dingen die u voorzichtig moet zijn met het type en de naam van deze resource. De waarden van de `"type": "sourcecontrols"` en- `"name": "web"` eigenschap zijn vastgelegd en mogen niet worden gewijzigd.

:::image type="content" source="./media/create-website/deploy-from-github.png" alt-text="Implementeren vanuit GitHub":::

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd! U hebt Azure Cosmos DB, Azure App Service en een voor beeld-webtoepassing geïmplementeerd die automatisch de verbindings gegevens bevat die nodig zijn om verbinding te maken met Cosmos DB, allemaal in één bewerking en zonder dat u gevoelige informatie hoeft te knippen en plakken. Als u deze sjabloon gebruikt als uitgangs punt, kunt u deze wijzigen zodat uw eigen webtoepassingen op dezelfde manier worden geïmplementeerd.

* Ga voor de Azure Resource Manager sjabloon voor dit voor beeld naar de [Galerie met Azure Quick Start sjablonen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)
* Ga voor de bron code voor de voor beeld-app naar [Cosmos DB om de app uit te voeren op github](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app).
