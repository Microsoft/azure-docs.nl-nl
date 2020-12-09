---
title: Taken en werkstromen automatiseren met Visual Studio
description: Geautomatiseerde werkstromen maken, plannen en uitvoeren voor bedrijfsintegratie met behulp van Azure Logic Apps en Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/27/2020
ms.openlocfilehash: ff195f7a0071c06d5309f95f77e32ae75f584f82
ms.sourcegitcommit: 003ac3b45abcdb05dc4406661aca067ece84389f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96749161"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Quickstart: Geautomatiseerde taken, processen en werkstromen maken met Azure Logic Apps - Visual Studio

Met [Azure Logic Apps](../logic-apps/logic-apps-overview.md) en Visual Studio kunt u werkstromen maken voor het automatiseren van taken en processen om apps, gegevens, systemen en services van bedrijven en organisaties te integreren. Deze quickstart toont u hoe u deze werkstromen kunt ontwerpen en compileren door het maken van logische apps in Visual Studio en hoe u deze apps kunt implementeren in Azure. Hoewel u deze taken kunt uitvoeren in de Azure-portal, kunt u met Visual Studio logische apps toevoegen aan het besturingselement voor de gegevensbron, verschillende versies publiceren en Azure Resource Manager-sjablonen maken voor verschillende implementatieomgevingen.

Als u niet vertrouwd bent met Azure Logic Apps en u alleen de basisconcepten wilt gebruiken, probeer dan de [quickstart voor het maken van een logische app in de Azure-portal](../logic-apps/quickstart-create-first-logic-app-workflow.md). De ontwerpfunctie Logic App werkt op dezelfde manier in de Azure-portal en in Visual Studio.

In deze quickstart maakt u met Visual Studio dezelfde logische app als in de quickstart voor de Azure-portal. U kunt ook leren hoe u [een voorbeeld-app maakt in Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md), en [logische apps maakt en beheert via de Azure CLI (opdrachtregelinterface)](quickstart-logic-apps-azure-cli.md). Met deze logische app wordt de RSS-feed van een website bewaakt en een e-mailbericht verzonden voor elk nieuw item in deze feed. Uw uiteindelijke logische app ziet eruit als deze algemene werkstroom:

![Schermafbeelding van de algemene werkstroom van een voltooide logische app.](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/). Als u een Azure Government-abonnement hebt, volgt u deze extra stappen om [Visual Studio in te stellen voor Azure Government Cloud](#azure-government).

* Download en installeer deze hulpprogramma's als u ze nog niet hebt:

  * [Visual Studio 2019, 2017 of 2015, Community edition of hoger](https://aka.ms/download-visual-studio). In deze quickstart wordt gebruikgemaakt van Visual Studio Community 2017.

    > [!IMPORTANT]
    > Als u Visual Studio 2019 of 2017 installeert, selecteer dan de workload **Azure development**.

  * [Microsoft Azure SDK voor .NET (2.9.1 of hoger)](https://azure.microsoft.com/downloads/). Meer informatie over [Azure SDK voor .NET](/dotnet/azure/intro).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * De nieuwste Azure Logic Apps-hulpprogramma's voor de Visual Studio-extensie voor de gewenste versie:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    U kunt hulpprogramma's van Azure Logic Apps ofwel rechtstreeks vanuit Visual Studio Marketplace downloaden en installeren, of leer [deze extensie te installeren vanuit Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions). Zorg ervoor dat u Visual Studio opnieuw opstart na de installatie.

* Toegang tot het web tijdens het gebruik van de ingebouwde ontwerpfunctie van Logic App

  De ontwerpfunctie vereist een internetverbinding om resources te maken in Azure en eigenschappen en gegevens van connectors in uw logische app te lezen.

* Een e-mailaccount dat door Logic Apps wordt ondersteund, bijvoorbeeld Outlook voor Microsoft 365, Outlook.com of Gmail. Voor andere providers kunt u [hier](/connectors/) de lijst met connectors bekijken. In dit voorbeeld wordt gebruikgemaakt van Outlook van Office 365. Als u een ander e-mailaccount gebruikt, zijn de algemene stappen hetzelfde, maar ziet de gebruikersinterface er misschien iets anders uit.

  > [!IMPORTANT]
  > Als u de Gmail-connector wilt gebruiken, kunnen alleen bedrijfsaccounts van G Suite deze connector zonder beperking in logische apps gebruiken. Als u een Gmail-consumentenaccount hebt, kunt u deze connector alleen gebruiken met specifieke door Google goedgekeurde services, of u kunt [een Google-client-app maken voor verificatie bij uw Gmail-connector](/connectors/gmail/#authentication-and-bring-your-own-application). Zie [Beleid voor gegevensbeveiliging en privacybeleid voor Google-connectors in Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md) voor meer informatie.

<a name="azure-government"></a>

## <a name="set-up-visual-studio-for-azure-government"></a>Visual Studio instellen voor Azure Government

### <a name="visual-studio-2017"></a>Visual Studio 2017

U kunt de [Visual Studio-extensie Azure Environment-selector](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/) gebruiken, die u kunt downloaden en installeren via de [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector).

### <a name="visual-studio-2019"></a>Visual Studio 2019

Om te kunnen werken met Azure Government-abonnementen in Azure Logic Apps, moet u [een detectie-eindpunt voor Azure Government Cloud toevoegen aan Visual Studio](../azure-government/documentation-government-connect-vs.md). *Voordat u zich bij Visual Studio aanmeldt met uw Azure Government-account*, moet u echter de naam wijzigen van het JSON-bestand dat wordt gegenereerd nadat u het detectie-eindpunt toevoegt, door deze stappen te volgen:

1. Sluit Visual Studio.

1. Zoek het gegenereerde JSON-bestand genaamd `Azure U.S. Government-A3EC617673C6C70CC6B9472656832A26.Configuration` op deze locatie:

   `%localappdata%\.IdentityService\AadConfigurations`
 
1. Geef het JSON-bestand de naam `AadProvider.Configuration.json`.

1. Start Visual Studio opnieuw op.

1. Ga verder met de stappen om u aan te melden met uw Azure Government-account.

Als u deze instelling wilt terugdraaien, verwijdert u het JSON-bestand op de volgende locatie en start u Visual Studio opnieuw op:

`%localappdata%\.IdentityService\AadConfigurations\AadProvider.Configuration.json`

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Een Azure-resourcegroepproject maken

Om te beginnen, maakt u een [Azure-resourcegroepproject](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Meer informatie over [Azure-resourcegroepen en resources](../azure-resource-manager/management/overview.md).

1. Start Visual Studio. Meld u aan met uw Azure-account.

1. In het menu **File** selecteert u **New** > **Project**. (Toetsenbord: Ctrl + Shift + N)

   ![In het menu File selecteert u New > Project](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. Onder **Installed**, selecteert u **Visual C#** of **Visual Basic**. Selecteer **Cloud** > **Azure Resource Group**. Geef uw project een naam, bijvoorbeeld:

   ![Een Azure-resourcegroepproject maken](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > De namen van resourcegroepen mogen alleen letters, cijfers, punten (`.`), onderstrepingstekens (`_`), koppeltekens (`-`) en haakjes (`(` en `)`) bevatten, maar mogen niet *eindigen* op een punt (`.`).
   >
   > Als **Cloud** of **Azure Resource Group** niet wordt weergegeven, installeert u de Azure SDK voor Visual Studio.

   Als u Visual Studio 2019 gebruikt, volgt u deze stappen:

   1. Selecteer in het vak **Een nieuw project maken** het project **Azure Resource Group** voor Visual C# of Visual Basic. Selecteer **Next**.

   1. Geef een naam voor de Azure-resourcegroep die u wilt gebruiken en andere projectgegevens op. Selecteer **Maken**.

1. Selecteer in de lijst met sjablonen de sjabloon **Logische app**. Selecteer **OK**.

   ![Sjabloon voor Logic App kiezen](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Nadat Visual Studio het project maakt, wordt Solution Explorer geopend en ziet u uw oplossing. In uw oplossing slaat het bestand **LogicApp.json** niet alleen de definitie voor uw logische app op, maar is het ook een Azure Resource Manager-sjabloon die u voor de implementatie kunt gebruiken.

   ![Solution Explorer toont de nieuwe logische app-oplossing en het implementatie-bestand](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Een lege, logische app maken

Nadat u uw Azure-resourcegroep-project hebt gemaakt, maakt u uw logische app met de **Lege logische app**-sjabloon.

1. Open in Solution Explorer het contextmenu van het bestand **LogicApp.json**. Selecteer **Openen met Logic App-ontwerpfunctie**. (Toetsenbord: Ctrl + L)

   ![Open het .json-bestand van de logische app met de Logic App-ontwerpfunctie](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Als u deze opdracht niet hebt in Visual Studio 2019, controleert u of u de nieuwste updates voor Visual Studio hebt.

   Visual Studio vraagt u om uw Azure-abonnement en een Azure-resourcegroep voor het maken en implementeren van resources voor uw logische app en verbindingen.

1. Selecteer bij **Abonnement** uw Azure-abonnement. Selecteer bij **Resourcegroep** de optie **Nieuwe maken** om een nieuwe Azure-resourcegroep te maken.

   ![Azure-abonnement, resourcegroep en resourcelocatie selecteren](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Instelling | Voorbeeldwaarde | Beschrijving |
   | ------- | ------------- | ----------- |
   | Gebruikersaccount | Fabrikam <br> sophia-owen@fabrikam.com | Het account dat u hebt gebruikt voor aanmelden bij Visual Studio |
   | **Abonnement** | Betalen per gebruik <br> (sophia-owen@fabrikam.com) | De naam voor uw Azure-abonnement en het bijbehorende account |
   | **Resourcegroep** | MyLogicApp-RG <br> (VS - west) | De Azure-resourcegroep en locatie voor het opslaan en implementeren van resources voor uw logische app |
   | **Locatie** | **Dezelfde als de resourcegroep** | Het locatietype en de specifieke locatie voor het implementeren van uw logische app. Het locatietype is een Azure-regio of een bestaande [Integratieserviceomgeving (ISE)](connect-virtual-network-vnet-isolated-environment.md). <p>Laat voor deze quickstart het locatietype ingesteld op **Regio** en de locatie op **Dezelfde als de resourcegroep**. <p>**Opmerking**: Nadat u uw resourcegroepproject hebt gemaakt, kunt u [het locatietype en de locatie wijzigen](manage-logic-apps-with-visual-studio.md#change-location), maar verschillende locatietypes beïnvloeden uw logische app op verschillende manieren. |
   ||||

1. De Logic Apps-ontwerpfunctie opent een pagina met een inleidende video en veelgebruikte triggers. Scrol omlaag vanaf de video en trigger naar **Sjablonen** en selecteer **Lege logische app**.

   ![Selecteer Lege logische app](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Een werkstroom voor uw logische app compileren

Vervolgens voegt u een RSS-[trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die wordt geactiveerd zodra er een nieuw feeditem wordt weergegeven. Elke logische app begint met een trigger, die wordt geactiveerd wanneer aan specifieke criteria wordt voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-engine een instantie van een logische app die de werkstroom uitvoert.

1. Selecteer in de ontwerpfunctie van de logische app onder het zoekvak de optie **Alle**. Typ 'rss' in het zoekvak. In de lijst met triggers selecteert u deze trigger: **Wanneer een feeditem wordt gepubliceerd**

   ![Uw logische app bouwen door toevoeging van een trigger en acties](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Nadat de trigger in de ontwerpfunctie wordt weergegeven, voltooit u het compileren van de logische app door de werkstroomstappen in de [Quickstart voor de Azure-portal](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger) te volgen. Keer daarna terug naar dit artikel. Wanneer u klaar bent, ziet uw logische app eruit zoals in dit voorbeeld:

   ![Voltooide logische app](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Sla uw Visual Studio-oplossing op. (Toetsenbord: Ctrl + S)

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Logische app implementeren in Azure

Voordat u uw logische app kunt uitvoeren en testen, moet u de app implementeren in Azure vanuit Visual Studio.

1. Selecteer in Solution Explorer in het snelmenu van uw project **Implementeren** > **Nieuw**. Meld u aan met uw Azure-account als u daarom wordt gevraagd.

   ![Een logische app-implementatie maken](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. Houd het standaard Azure-abonnement, de standaard resourcegroep en andere standaardinstellingen aan voor deze implementatie. Selecteer **Implementeren**.

   ![Logische app implementeren naar een Azure-resourcegroep](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Als het vak **Parameters bewerken** wordt weergegeven, geeft u een resourcenaam voor de logische app op. Sla uw instellingen op.

   ![Naam van de implementatie opgeven voor de logische app](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Wanneer de implementatie wordt gestart, wordt de status van de implementatie van uw app weergegeven in het **Uitvoer**-venster van Visual Studio. Als de status niet wordt weergegeven, opent u de lijst **Uitvoer tonen van** en selecteert u uw Azure-resourcegroep.

   ![Uitvoer implementatiestatus](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Als de geselecteerde connectors invoer van u nodig hebben, wordt er op de achtergrond een PowerShell-venster geopend waarin u wordt gevraagd om de noodzakelijke wachtwoorden en geheime sleutels. Nadat u deze gegevens hebt ingevoerd, wordt de implementatie voortgezet.

   ![PowerShell-venster](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   Nadat de implementatie is voltooid, gaat uw logische app live in de Azure-portal en wordt deze uitgevoerd op basis van de opgegeven planning (elke minuut). Als de trigger nieuwe feeditems vindt, wordt de trigger geactiveerd, waardoor er een werkstroomexemplaar wordt gemaakt die de acties van de logische app uitvoert. Uw logische app verstuurt een e-mail voor elk nieuw item. Als de trigger geen nieuwe items vindt, wordt deze niet geactiveerd en wordt het instantiëren van de werkstroom overgeslagen. De logische app wacht dan tot het volgende interval met controleren.

   Dit zijn voorbeelden van e-mailberichten die deze logische app verzendt. Als u geen een e-mailberichten ontvangt, controleert u de map Ongewenste e-mail.

   ![Outlook verzendt een e-mail voor elk nieuw RSS-item](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

Gefeliciteerd, u hebt uw logische app met Visual Studio gemaakt en geïmplementeerd. Zie [Logische apps beheren met Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md) om uw logische app te beheren en de uitvoeringsgeschiedenis te inspecteren.

## <a name="add-new-logic-app"></a>Nieuwe logische app toevoegen

Wanneer u een bestaand Azure-resourcegroepproject hebt, kunt u een nieuwe lege logische app aan dat project toevoegen met behulp van het JSON Outline-venster.

1. Open in Solution Explorer het bestand `<logic-app-name>.json`.

1. Selecteer in het menu **Weergave** **Andere vensters** > **JSON Outline**.

1. Als u een resource aan en sjabloonbestand wilt toevoegen, selecteert u **Resource toevoegen** bovenaan het JSON Outline-venster. U kunt ook in het JSON Outline-venster het contextmenu **resources** openen en **Nieuwe resource toevoegen** selecteren.

   ![JSON Outline-venster](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. Zoek in het zoekvak van het dialoogvenster **Resource toevoegen** naar `logic app` en selecteer **Logische app**. Geef uw logische app een naam en selecteer **Toevoegen**.

   ![Resource toevoegen](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u de logische app niet meer nodig hebt, verwijdert u de resourcegroep die uw logische app en alle gerelateerde resources bevat.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met hetzelfde account dat is gebruikt voor het maken van uw logische app.

1. Selecteer in het menu van de Azure-portal de optie **Resourcegroepen** of zoek naar en selecteer **Resourcegroepen** op een willekeurige pagina. Selecteer de resourcegroep van uw logische app.

1. Selecteer op de pagina **Overzicht** de optie **Resourcegroep verwijderen**. Voer ter bevestiging de naam van de resourcegroep in en selecteer **Verwijderen**.

   !['Resourcegroepen' > 'Overzicht' > 'Resourcegroep verwijderen'](./media/quickstart-create-logic-apps-with-visual-studio/clean-up-resources.png)

1. Verwijder de Visual Studio-oplossing van uw lokale computer.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u uw logische app met Visual Studio gebouwd, geïmplementeerd en uitgevoerd. Voor informatie over het beheren en uitvoeren van de geavanceerde implementatie voor logische apps met Visual Studio, raadpleegt u de volgende artikelen:

> [!div class="nextstepaction"]
> [Logische apps beheren met Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
