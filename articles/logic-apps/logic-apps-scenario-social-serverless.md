---
title: Het dash board voor klanten inzichten maken
description: Het beheren van feedback van klanten, gegevens van sociale media en meer door het maken van een dash board van klanten met Azure Logic Apps en Azure Functions
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: 22e0c7304f7a53a86bc5c6739a2061352d738d29
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784809"
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Maak een streaming-dash board voor klanten inzichten met Azure Logic Apps en Azure Functions

Azure biedt [serverloze](https://azure.microsoft.com/solutions/serverless/) hulpprogram ma's waarmee u snel apps kunt bouwen en hosten in de Cloud, zonder dat u rekening moet houden met de infra structuur. In deze zelf studie kunt u een dash board maken dat de feedback van klanten activeert, de feedback met machine learning analyseert en inzichten publiceert naar een bron, zoals Power BI of Azure Data Lake.

Voor deze oplossing gebruikt u de volgende belang rijke Azure-onderdelen voor serverloze apps: [Azure functions](https://azure.microsoft.com/services/functions/) en [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/).
Azure Logic Apps biedt een serverloze werk stroom-engine in de Cloud, zodat u met serverloze onderdelen kunt maken en verbinding met de services en Api's van 200 en de API. Azure Functions biedt serverloze computers in de Cloud. Deze oplossing maakt gebruik van Azure Functions voor het markeren van klant tweets op basis van vooraf gedefinieerde tref woorden.

In dit scenario maakt u een logische app die activeert om feedback van klanten te vinden. Voor sommige connectors kunt u reageren op feedback van klanten, waaronder Outlook.com, Office 365, Survey aap, Twitter en een [HTTP-aanvraag van een webformulier](/archive/blogs/logicapps/calling-a-logic-app-from-an-html-form). De werk stroom die u maakt, bewaakt een hashtag op Twitter.

U kunt [de volledige oplossing in Visual Studio bouwen](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) en [de oplossing implementeren met Azure Resource Manager sjabloon](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md). [Bekijk deze Channel 9-video](https://aka.ms/logicappsdemo)voor een video-overzicht waarin wordt getoond hoe u deze oplossing maakt. 

## <a name="trigger-on-customer-data"></a>Activeren op klant gegevens

1. Maak in de Azure Portal of Visual Studio een lege logische app. 

   Als u geen ervaring hebt met Logic apps, raadpleegt u de [Snelstartgids voor de Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md) of de [Snelstartgids voor Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. In de ontwerp functie voor logische apps zoekt en voegt u de Twitter-trigger toe die deze actie heeft: **Wanneer een nieuwe tweet wordt geplaatst**

3. Stel de trigger in om te Luis teren naar tweets op basis van een sleutel woord of hashtag.

   Op polling gebaseerde triggers, zoals de Twitter-trigger, bepaalt de eigenschap Recurrence hoe vaak de logische app op nieuwe items controleert.

   ![Voor beeld van Twitter-trigger][1]

Deze logische app wordt nu geactiveerd op alle nieuwe tweets. U kunt vervolgens de Tweet-gegevens nemen en analyseren, zodat u beter inzicht krijgt in de gevoel die worden weer gegeven. 

## <a name="analyze-tweet-text"></a>Tweet tekst analyseren

Als u de sentiment achter een bepaalde tekst wilt detecteren, kunt u [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)gebruiken.

1. Kies in Logic app Designer onder de trigger **nieuwe stap**.

2. Zoek de **Text Analytics** -connector.

3. Selecteer de actie **sentiment detecteren** .

4. Als u hierom wordt gevraagd, geeft u een geldige Cognitive Services sleutel op voor de Text Analytics-service.

5. Selecteer onder **hoofd tekst van aanvraag** het **tekst veld Tweet** , waarmee de Tweet-tekst als invoer voor analyse wordt verstrekt.

Nadat u de Tweet-gegevens en inzichten over de Tweet hebt opgehaald, kunt u nu verschillende andere relevante connectors en de bijbehorende acties gebruiken:

* **Power bi-rijen toevoegen aan streaming-gegevensset**: binnenkomende tweets op een Power bi dash board weer geven.
* **Azure data Lake-bestand** toevoegen: Voeg klant gegevens toe aan een Azure data Lake-gegevensset die in analyse taken moet worden meegenomen.
* **SQL-rijen toevoegen**: Sla gegevens op in een Data Base om deze later op te halen.
* **Toegestane vertraging-bericht verzenden**: een vertragings kanaal melden over negatieve feedback waarvoor mogelijk actie is vereist.

U kunt ook een Azure-functie maken, zodat u aangepaste verwerking voor uw gegevens kunt uitvoeren. 

## <a name="process-data-with-azure-functions"></a>Gegevens verwerken met Azure Functions

Voordat u een functie maakt, maakt u een functie-app in uw Azure-abonnement. Voor uw logische app is het ook mogelijk om een functie rechtstreeks aan te roepen, de functie moet een binding met een HTTP-trigger hebben, bijvoorbeeld de **http trigger** -sjabloon. Meer informatie [over het maken van uw eerste functie-app en functie in de Azure Portal](../azure-functions/functions-get-started.md).

Voor dit scenario gebruikt u de Tweet-tekst als de aanvraag tekst voor uw Azure-functie. Definieer in uw functie code de logica die bepaalt of de Tweet-tekst een sleutel woord of een woord groep bevat. Zorg ervoor dat de functie zo eenvoudig of complex is als nodig is voor het scenario.
Aan het einde van de functie retourneert een antwoord op de logische app met enkele gegevens, bijvoorbeeld een eenvoudige Booleaanse waarde, zoals `containsKeyword` of een complex object.

> [!TIP]
> Gebruik de actie **JSON parseren** om toegang te krijgen tot een complexe reactie van een functie in een logische app.

Wanneer u klaar bent, slaat u de functie op en voegt u de functie toe als een actie in de logische app die u ontwikkelt.

## <a name="add-azure-function-to-logic-app"></a>Azure function toevoegen aan de logische app

1. Kies in Logic app Designer de optie **nieuwe stap** onder de actie **sentiment detecteren** .

2. Zoek de **Azure functions** -connector en selecteer vervolgens de functie die u hebt gemaakt.

3. Onder **aanvraag tekst** selecteert u **Tweet tekst**.

![Azure-functie stap is geconfigureerd][2]

## <a name="run-and-monitor-your-logic-app"></a>Uw logische app uitvoeren en controleren

Als u de huidige of vorige uitvoeringen voor uw logische app wilt controleren, kunt u de geavanceerde functies voor fout opsporing en controle gebruiken die Azure Logic Apps biedt in de Azure Portal, Visual Studio of via de REST Api's en Sdk's van Azure.

Als u uw logische app eenvoudig wilt testen, kiest u in Logic app Designer de optie **trigger uitvoeren**. De trigger vraagt om tweets op basis van uw opgegeven planning tot een tweet die voldoet aan uw criteria is gevonden. Tijdens de uitvoering wordt de ontwerp functie weer gegeven in een live weer gave voor die uitvoering.

U kunt als volgt eerdere run-runs bekijken in Visual Studio of de Azure Portal: 

* Open Visual Studio Cloud Explorer. Zoek uw logische app, open het snelmenu van de app. Selecteer **Open run-geschiedenis**.

  > [!TIP]
  > Als u deze opdracht niet hebt in Visual Studio 2019, controleert u of u de nieuwste updates voor Visual Studio hebt.

* Zoek in de Azure Portal uw logische app. Kies **overzicht** in het menu van de logische app. 

## <a name="create-automated-deployment-templates"></a>Sjablonen voor automatische implementatie maken

Nadat u een logische app-oplossing hebt gemaakt, kunt u uw app vastleggen en implementeren als [Azure Resource Manager sjabloon](../azure-resource-manager/templates/overview.md) voor elke Azure-regio in de wereld. U kunt deze mogelijkheid gebruiken om para meters te wijzigen voor het maken van verschillende versies van uw app en voor het integreren van uw oplossing in azure-pijp lijnen. U kunt ook Azure Functions in uw implementatie sjabloon toevoegen, zodat u de volledige oplossing met alle afhankelijkheden als één sjabloon kunt beheren. Meer informatie over het automatiseren van de [implementatie van logische apps](logic-apps-azure-resource-manager-templates-overview.md).

Voor een voorbeeld implementatie sjabloon met een Azure-functie raadpleegt u de [opslag plaats van de Azure Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic)start-sjabloon.

## <a name="next-steps"></a>Volgende stappen

* [Andere voor beelden en scenario's voor Azure Logic Apps zoeken](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png