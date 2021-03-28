---
title: Logische apps bewerken en beheren met behulp van Visual Studio met Cloud Explorer
description: Bewerken, bijwerken, beheren, toevoegen aan broncode beheer en logische Apps implementeren met behulp van Visual Studio met Cloud Explorer
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/29/2020
ms.openlocfilehash: 56b74e440fcb09ab206bbb069517dd756221f809
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639556"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Logische apps beheren met Visual Studio

Hoewel u in de [Azure Portal](https://portal.azure.com)logische apps kunt maken, bewerken, beheren en implementeren, kunt u Visual Studio ook gebruiken als u uw Logic Apps wilt toevoegen aan broncode beheer, verschillende versies wilt publiceren en [Azure Resource Manager](../azure-resource-manager/management/overview.md) sjablonen voor verschillende implementatie omgevingen wilt maken. Met Visual Studio Cloud Explorer kunt u uw logische apps samen met andere Azure-resources vinden en beheren. U kunt bijvoorbeeld openen, downloaden, bewerken, uitvoeren, uitvoerings geschiedenis bekijken, uitschakelen en logische apps inschakelen die al in de Azure Portal zijn geïmplementeerd. Als u geen ervaring hebt met het werken met Azure Logic Apps in Visual Studio, kunt u leren [hoe u logische apps maakt met Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

U kunt ook [uw Logic apps beheren in de Azure Portal](manage-logic-apps-with-azure-portal.md).

> [!IMPORTANT]
> Als u een logische app implementeert of publiceert vanuit Visual Studio, wordt de versie van die app in de Azure Portal overschreven. Als u wijzigingen aanbrengt in de Azure Portal die u wilt blijven gebruiken, moet u er dus voor zorgen dat u [de logische app in Visual Studio vernieuwt](#refresh) vanuit de Azure Portal voordat u de volgende keer van Visual Studio implementeert of publiceert.

<a name="requirements"></a>

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Download en installeer deze hulpprogramma's als u ze nog niet hebt:

  * [Visual Studio 2019, 2017 of 2015, Community edition of hoger](https://aka.ms/download-visual-studio). Deze snelstart maakt gebruik van Visual Studio Community 2017, dit is gratis.

    > [!IMPORTANT]
    > Als u Visual Studio 2019 of 2017 installeert, selecteer dan de workload **Azure development**.
    > Zie [resources die zijn gekoppeld aan uw Azure-accounts beheren in Visual Studio Cloud Explorer](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer)voor meer informatie.

    Als u Cloud Explorer voor Visual Studio 2015 wilt installeren, [downloadt u Cloud Explorer van Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). Zie [resources beheren die zijn gekoppeld aan uw Azure-accounts in Visual Studio Cloud Explorer (2015)](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015&preserve-view=true)voor meer informatie.

  * [Azure SDK (2.9.1 of hoger)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * De nieuwste Azure Logic Apps-hulpprogramma's voor de Visual Studio-extensie voor de gewenste versie:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    U kunt hulpprogramma's van Azure Logic Apps ofwel rechtstreeks vanuit Visual Studio Marketplace downloaden en installeren, of leer [deze extensie te installeren vanuit Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions). Zorg ervoor dat u Visual Studio opnieuw opstart na de installatie.

  * Lees de volgende onderwerpen voor aanvullende installatiegegevens als u Azure Government-abonnementen met Visual Studio wilt gebruiken:

    * Visual Studio 2019: [Snelstart: Verbinding maken met Azure Government met Visual Studio](../azure-government/documentation-government-connect-vs.md)

    * Visual Studio 2017: [Introductie van de Visual Studio-extensie Azure Environment-selector ](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/), die u kunt downloaden en installeren via de [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector).

* Toegang tot het web terwijl u de Inge sloten Logic Apps Designer gebruikt

  De ontwerpfunctie moet over een internetverbinding beschikken om resources te maken in Azure en de eigenschappen en gegevens van connectoren in uw logische app te lezen.

<a name="find-logic-apps-vs"></a>

## <a name="find-logic-apps"></a>Logische apps zoeken

In Visual Studio vindt u alle Logic apps die zijn gekoppeld aan uw Azure-abonnement en die worden geïmplementeerd in de Azure Portal met behulp van Cloud Explorer.

1. Open Visual Studio. Selecteer in het menu **weer gave** de optie **Cloud Explorer**.

1. Selecteer in Cloud Explorer het pictogram **account beheer** . Selecteer het Azure-abonnement dat is gekoppeld aan uw Logic apps en selecteer **Toep assen**. Bijvoorbeeld:

   ![Selecteer account beheer](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Klik naast het pictogram **account beheer** op **resource typen**. Vouw onder uw Azure-abonnement **Logic apps** uit zodat u alle geïmplementeerde Logic apps kunt weer geven die zijn gekoppeld aan uw abonnement.

Open vervolgens uw logische app in de editor voor logische apps.

<a name="open-designer"></a>

## <a name="open-logic-apps-in-visual-studio"></a>Logic apps openen in Visual Studio

In Visual Studio kunt u logische apps openen die eerder zijn gemaakt en geïmplementeerd, rechtstreeks via de Azure Portal of als Azure-resource groeps projecten met Visual Studio.

1. [Open Cloud Explorer en zoek uw logische app](#find-logic-apps-vs).

1. Selecteer in het snelmenu van de logische app de optie **openen met Logic app-editor**.

   > [!TIP]
   > Als u deze opdracht niet hebt in Visual Studio 2019, controleert u of u de nieuwste updates voor Visual Studio hebt.

   ![Geïmplementeerde logische app openen vanuit Azure Portal](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Nadat de logische app in Logic Apps Designer is geopend, kunt u onder aan de ontwerp functie de **code weergave** selecteren zodat u de onderliggende logische app-definitie structuur kunt bekijken. Als u een implementatie sjabloon wilt maken voor de logische app, leert u [hoe u een Azure Resource Manager sjabloon](#download-logic-app) voor die logische app kunt downloaden. Meer informatie over [Resource Manager-sjablonen](../azure-resource-manager/templates/overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Downloaden van Azure

U kunt Logic apps [downloaden](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource) van de [Azure Portal](https://portal.azure.com) en deze opslaan als [Azure Resource Manager](../azure-resource-manager/management/overview.md) sjablonen. U kunt de sjablonen vervolgens lokaal bewerken met Visual Studio en Logic apps aanpassen voor verschillende implementatie omgevingen.  Bij het downloaden van Logic apps worden de definities in [Resource Manager-sjablonen](../azure-resource-manager/templates/overview.md)automatisch *parameterizes* , die ook JavaScript object Notation (JSON) gebruiken.

1. Open in Visual Studio met behulp van Cloud Explorer [de logische app die u wilt downloaden van Azure](#open-designer).

1. Selecteer in het snelmenu van de logische app de optie **openen met Logic app-editor**.

   > [!TIP]
   > Als u deze opdracht niet hebt in Visual Studio 2019, controleert u of u de nieuwste updates voor Visual Studio hebt.

   De logische app wordt geopend in de ontwerp functie voor logische apps.

1. Selecteer **downloaden** op de werk balk van de ontwerp functie.

   ![Logische app downloaden van Azure Portal](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. Wanneer u wordt gevraagd om een locatie, bladert u naar die locatie en slaat u de Resource Manager-sjabloon voor de definitie van de logische app op in de JSON-bestands indeling (. json).

   De definitie van de logische app wordt weer gegeven in de `resources` Subsectie in de Resource Manager-sjabloon. U kunt nu de definitie van de logische app en de Resource Manager-sjabloon bewerken met Visual Studio. U kunt de sjabloon ook als een [Azure-resource groeps project](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) toevoegen aan een Visual Studio-oplossing. Meer informatie over [Azure-resource groeps projecten voor Logic apps in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Koppeling naar het integratie account

Als u Logic apps voor Business-to-Business (B2B) wilt bouwen, kunt u uw logische app koppelen aan een eerder gemaakt [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) dat zich in dezelfde regio bevindt als uw logische app. Een integratie account bevat B2B-artefacten, zoals handels partners, overeenkomsten, schema's en kaarten, en laat uw logische app B2B-connectors gebruiken voor XML-validatie en platte bestands codering of-decodering. Hoewel u [deze koppeling kunt maken met behulp van de Azure Portal](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account), kunt u ook Visual Studio gebruiken nadat aan de [vereisten is voldaan](#requirements)en uw logische app bestaat als een JSON-bestand (. json) binnen een project van een [Azure-resource groep](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Meer informatie over [Azure-resource groeps projecten voor Logic apps in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project).

1. Open in Visual Studio het Azure-resource groep-project dat uw logische app bevat.

1. Open in Solution Explorer het snelmenu **<Logic-app-name # C1.jsin** het menu bestand en selecteer **openen met Logic app Designer**. (Toetsenbord: Ctrl + L)

   ![Het JSON-bestand van de logische app openen met Logic app Designer](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Als u deze opdracht niet in Visual Studio 2019 hebt, controleert u of u de meest recente updates voor Visual Studio en de uitbrei ding Azure Logic Apps tools hebt.

1. Zorg ervoor dat de ontwerp functie voor logische apps de focus heeft door het tabblad of Opper vlak van de ontwerp functie te selecteren, zodat de venster Eigenschappen de eigenschap van het **integratie account** voor uw logische app wordt weer gegeven.

   ![Venster Eigenschappen-eigenschap integratie account](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Als de venster Eigenschappen nog niet is geopend, selecteert u in het menu **weer gave** **Eigenschappen venster**. (Toetsen bord: druk op F4)

1. Open de lijst met eigenschappen van het **integratie account** en selecteer het integratie account dat u wilt koppelen aan uw logische app, bijvoorbeeld:

   ![De eigenschappen lijst voor het integratie account openen](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Als u klaar bent, kunt u uw Visual Studio-oplossing opslaan.

Wanneer u de eigenschap van het **integratie account** instelt in Visual Studio en uw logische app opslaat als een Azure Resource Manager sjabloon, bevat die sjabloon ook een parameter declaratie voor het geselecteerde integratie account. Zie [overzicht: implementatie van logische apps automatiseren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)voor meer informatie over sjabloon parameters en Logic apps.

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Implementatie locatie wijzigen

Als uw logische app zich in Visual Studio bevindt als een JSON-bestand (. json) binnen een [Azure-resource groeps project](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) dat u gebruikt om de implementatie te automatiseren, wordt die logische app ingesteld op een locatie type en een specifieke locatie. Deze locatie is een Azure-regio of een bestaande [integratie service omgeving (ISE)](connect-virtual-network-vnet-isolated-environment.md).

Als u het locatie type of de locatie van uw logische app wilt wijzigen, moet u het werk stroom definitie bestand (. json) van de logische app openen vanuit Solution Explorer met behulp van de ontwerp functie voor logische apps. U kunt deze eigenschappen niet wijzigen met behulp van Cloud Explorer.

> [!IMPORTANT]
> Het wijzigen van het locatie type van de **regio** in [**Integratieserviceomgeving**](connect-virtual-network-vnet-isolated-environment-overview.md) is van invloed op het [prijs model](logic-apps-pricing.md#fixed-pricing) van uw logische app die wordt gebruikt voor facturering, [limieten](logic-apps-limits-and-config.md#integration-account-limits), [ondersteuning van integratie accounts](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus), enzovoort. Voordat u een ander locatie type selecteert, moet u ervoor zorgen dat u de resulterende impact op uw logische app begrijpt.

1. Open in Visual Studio het Azure-resource groep-project dat uw logische app bevat.

1. Open in Solution Explorer het `<logic-app-name>.json` snelmenu van het bestand en selecteer **openen met Logic app Designer**. (Toetsenbord: Ctrl + L)

   ![Het JSON-bestand van de logische app openen met Logic app Designer](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Als u deze opdracht niet in Visual Studio 2019 hebt, controleert u of u de meest recente updates voor Visual Studio en de uitbrei ding Azure Logic Apps tools hebt.

1. Zorg ervoor dat de ontwerp functie voor logische apps de focus heeft door het tabblad of Opper vlak van de ontwerp functie te selecteren, zodat de venster Eigenschappen het **locatie type** en de **locatie** -eigenschappen kiezen voor uw logische app. Het locatie type van het project is ingesteld op een van de **regio's** of **Integratieserviceomgeving**.

   ![Venster Eigenschappen-' locatie type kiezen ' & ' locatie ' eigenschappen](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Als de venster Eigenschappen nog niet is geopend, selecteert u in het menu **weer gave** **Eigenschappen venster**. (Toetsen bord: druk op F4)

1. Als u het locatie type wilt wijzigen, opent u de lijst **locatie type kiezen** en selecteert u het gewenste locatie type.

   Als bijvoorbeeld het locatie type **Integratieserviceomgeving** is, kunt u **regio** selecteren.

   ![Eigenschap type locatie kiezen-locatie type wijzigen](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Als u de specifieke locatie wilt wijzigen, opent u de lijst met **locatie** -eigenschappen. Selecteer op basis van het locatie type de locatie die u wilt, bijvoorbeeld:

   * Selecteer een andere Azure-regio:

     ![Open de eigenschappen lijst locatie, selecteer een andere Azure-regio](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Selecteer een andere ISE:

     ![Open de eigenschappen lijst locatie, selecteer een andere ISE](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. Als u klaar bent, kunt u uw Visual Studio-oplossing opslaan.

Wanneer u het locatie type of de locatie in Visual Studio wijzigt en uw logische app als Azure Resource Manager sjabloon opslaat, bevat die sjabloon ook parameter declaraties voor dat locatie type en deze locatie. Zie [overzicht: implementatie van logische apps automatiseren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)voor meer informatie over sjabloon parameters en Logic apps.

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Vernieuwen vanuit Azure

Als u de logische app in de Azure Portal bewerkt en u deze wijzigingen wilt hand haven, moet u ervoor zorgen dat u de versie van de app in Visual Studio vernieuwt met die wijzigingen.

* Selecteer in Visual Studio op de werk balk van de Logic app-ontwerp functie **vernieuwen**.

  -of-

* Open in Visual Studio Cloud Explorer het snelmenu van de logische app en selecteer **vernieuwen**.

![Logische app met updates vernieuwen](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>Updates van de logische app publiceren

Wanneer u klaar bent om uw Logic app-updates te implementeren vanuit Visual Studio naar Azure, selecteert u op de werk balk van de Logic app-ontwerp functie **publiceren**.

![Bijgewerkte logische app publiceren naar Azure Portal](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>Logische app handmatig uitvoeren

U kunt een logische app die is geïmplementeerd in azure, hand matig activeren vanuit Visual Studio. Selecteer **trigger uitvoeren** op de werk balk van de Logic app-ontwerp functie.

![De trigger hand matig uitvoeren voor uw logische app](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Uitvoeringsgeschiedenis controleren

Als u de status wilt controleren en problemen wilt vaststellen met uitvoeringen van logische apps, kunt u de Details voor deze uitvoeringen bekijken in Visual Studio, zoals invoer en uitvoer.

1. Open in Cloud Explorer het snelmenu van de logische app en selecteer **Open run-geschiedenis**.

   ![Open uitvoerings geschiedenis voor uw logische app](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Dubbel klik op een uitvoering om de details van een specifieke uitvoering weer te geven. Bijvoorbeeld:

   ![Informatie over specifieke uitvoeringen weer geven](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Als u de tabel wilt sorteren op eigenschap, selecteert u de kolomkop voor die eigenschap.

1. Vouw de stappen uit waarvan u de invoer en uitvoer wilt controleren, bijvoorbeeld:

   ![Invoer en uitvoer voor elke stap weer geven](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Logische app in- of uitschakelen

Zonder uw logische app te verwijderen, kunt u voor komen dat de trigger de volgende keer wordt geactiveerd wanneer aan de voor waarde voor de trigger wordt voldaan. Door uw logische app uit te scha kelen, voor komt u dat de Logic Apps-Engine toekomstige werk stroom exemplaren voor uw logische app maakt en uitvoert. Open in Cloud Explorer het snelmenu van de logische app en selecteer **uitschakelen**.

![Uw logische app uitschakelen in Cloud Explorer](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> Wanneer u een logische app uitschakelt, worden er geen nieuwe uitvoeringen geïnstantieerd. Alle uitvoeringen en in behandeling zijnde uitvoeringen gaan door totdat ze zijn voltooid. Dit kan enige tijd in beslag nemen.

Als u uw logische app opnieuw wilt activeren, opent u in Cloud Explorer het snelmenu van de logische app en selecteert u **inschakelen**.

![Logische app inschakelen in Cloud Explorer](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>Uw logische app verwijderen

Als u uw logische app uit de Azure Portal wilt verwijderen, opent u in Cloud Explorer het snelmenu van de logische app en selecteert u **verwijderen**.

![Uw logische app verwijderen uit Azure Portal](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> Wanneer u een logische app verwijdert, worden geen nieuwe uitvoeringen gemaakt. Alle uitvoeringen die bezig zijn en wachten op uitvoering worden geannuleerd. Als u duizenden uitvoeringen hebt, kan de annulering een aanzienlijke tijd in beslag nemen.

> [!NOTE]
> Als u een onderliggende logische app verwijdert en opnieuw maakt, moet u de bovenliggende logische app opnieuw opslaan. De opnieuw gemaakte onderliggende app heeft andere meta gegevens.
> Als u de bovenliggende logische app niet opnieuw opslaat nadat u het onderliggende element opnieuw hebt gemaakt, zullen de aanroepen naar de onderliggende logische app mislukken met de fout ' niet toegestaan '. Dit gedrag is van toepassing op bovenliggende en onderliggende logische apps, bijvoorbeeld die artefacten gebruiken in integratie accounts of Azure functions aanroepen.

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer u uw Logic app-project in de Logic Apps Designer opent, krijgt u mogelijk niet de optie om uw Azure-abonnement te selecteren. In plaats daarvan wordt uw logische App geopend met een Azure-abonnement dat niet het enige is dat u wilt gebruiken. Dit probleem treedt op omdat Visual Studio, nadat u een JSON-bestand van een logische app hebt geopend, het eerste geselecteerde abonnement in de cache opslaat voor toekomstig gebruik. Voer een van de volgende stappen uit om dit probleem op te lossen:

* Wijzig de naam van het JSON-bestand van de logische app. De abonnements cache is afhankelijk van de bestands naam.

* Als u eerder geselecteerde abonnementen voor *alle* Logic apps in uw oplossing wilt verwijderen, verwijdert u de verborgen map Visual Studio Settings (. VS) in de map van uw oplossing. Op deze locatie worden de abonnements gegevens opgeslagen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u geïmplementeerde logische apps kunt beheren met Visual Studio. Meer informatie over het aanpassen van Logic app-definities voor implementatie:

> [!div class="nextstepaction"]
> [Definities van logische apps in JSON maken](../logic-apps/logic-apps-author-definitions.md)
