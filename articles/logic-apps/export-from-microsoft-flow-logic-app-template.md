---
title: Stromen exporteren van automatische stroom naar Azure Logic Apps
description: Migreer stromen van automatische stroom naar Azure Logic Apps door te exporteren als Azure Resource Manager sjablonen
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, sneshaf, pinath, logicappspm
ms.topic: conceptual
ms.date: 01/20/2021
ms.openlocfilehash: 2192ae5f3de551cb8ff2b6c36e233804c3c56bf1
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633244"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Stromen exporteren vanuit Power Automate en implementeren in Azure Logic Apps

> Voor stromen die na oktober 2020 zijn gemaakt, kunt u niet meer exporteren uit automatische stroom en implementeren naar Azure Logic Apps.

Om de mogelijkheden van uw stroom uit te breiden en uit te breiden, kunt u die stroom migreren van [Automatische stroom](https://flow.microsoft.com) naar [Azure Logic apps](../logic-apps/logic-apps-overview.md). U kunt uw stroom exporteren als een Azure Resource Manager sjabloon voor een logische app, de sjabloon Logic app implementeren naar een Azure-resource groep en die logische app vervolgens openen in de ontwerp functie voor logische apps.

> [!NOTE]
> Niet alle stroom verautomatiseerde connectors zijn beschikbaar in Azure Logic Apps. U kunt alleen stroom geautomatiseerde stromen met de equivalente connectors in Azure Logic Apps migreren. De knop trigger, de goedkeurings connector en de notification connector zijn bijvoorbeeld specifiek voor het automatiseren van het energie verbruik. Op dit moment worden OpenAPI stromen in energie automatisering niet ondersteund voor exporteren en implementeren als sjablonen voor logische apps.
>
> * Als u wilt weten welke stroom lijnen voor automatische automatisering geen Logic Apps equivalenten hebben, raadpleegt u [connectors](/connectors/connector-reference/connector-reference-powerautomate-connectors)voor het automatiseren van de stroom.
>
> * Zie [Logic apps connectors](/connectors/connector-reference/connector-reference-powerautomate-connectors)als u wilt weten welke Logic apps connectors geen energie automatisering hebben.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De stroom die u wilt exporteren uit automatische energie

## <a name="export-your-flow"></a>Uw stroom exporteren

1. Meld u aan bij [energie automatisering](https://flow.microsoft.com)en selecteer **mijn stromen**. Zoek en selecteer uw stroom. Selecteer op de werk balk de knop met weglatings tekens (**...**) >   >  **Logic apps sjabloon (. json)** te exporteren.

   ![Stroom exporteren uit het automatiseren van de stroom](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Sla het JSON-bestand van de sjabloon op de gewenste locatie op.

Zie [groeien tot Azure Logic apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/)voor meer informatie.

## <a name="deploy-template-by-using-the-azure-portal"></a>Sjabloon implementeren met behulp van de Azure Portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met uw Azure-account.

1. Typ op de start pagina van Azure in het zoekvak `custom template` . Selecteer in de resultaten de optie **een aangepaste sjabloon implementeren**  >  **maken**.

   ![Zoek en selecteer ' Sjabloonimlementatie '](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. Onder **aangepaste implementatie** selecteert u **uw eigen sjabloon bouwen in de editor**.

   ![Selecteer ' uw eigen sjabloon bouwen in de editor '](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Selecteer op de werk balk **sjabloon bewerken** de optie **bestand laden**.

   ![Selecteer bestand laden](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Blader naar de locatie waar u het JSON-sjabloon bestand hebt opgeslagen dat u hebt geëxporteerd uit het automatiseren van de stroom. Selecteer het sjabloon bestand dat > **geopend**.

1. Selecteer **Opslaan** nadat de JSON, para meters en resources in uw sjabloon zijn weer gegeven in de editor.

   ![Sjabloon opslaan](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Geef nu meer informatie over uw logische app.

   1. De waarden voor de invoer parameters voor uw sjabloon selecteren of opgeven.

      | Eigenschap | Beschrijving |
      |----------|-------------|
      | **Abonnement** | Het Azure-abonnement dat moet worden gebruikt voor facturering |
      | **Resourcegroep** | De Azure-resource groep die moet worden gebruikt voor uw logische app. U kunt een bestaande groep gebruiken of een nieuwe groep maken. |
      | **Locatie** | De Azure-regio die moet worden gebruikt als u een nieuwe resource groep maakt |
      | **Naam van logische app** | De naam die moet worden gebruikt voor de logische app-resource |
      | **Locatie van logische app** | De Azure-regio waar u de logische app-resource wilt maken, als deze verschilt van de Azure-resource groep |
      | <*verbindings naam*> | Een of meer namen voor eerder gemaakte verbindingen die de logische app kan hergebruiken <p><p>**Opmerking**: als deze logische app uw eerste is, worden alle verbindingen gemaakt als nieuw, zodat u de standaard namen kunt accepteren. Anders kunt u de namen opgeven voor eerder gemaakte verbindingen, die u kunt gebruiken in meerdere logische apps. |
      |||

      Bijvoorbeeld:

      ![Invoer parameters voor sjabloon opgeven](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   1. Wanneer u klaar bent, bekijkt u de **voor waarden** voor het maken van de benodigde Azure-resources en het factureren van uw Azure-abonnement.

   1. Wanneer u klaar bent, selecteert u **Ik ga akkoord met de voor waarden die hierboven worden vermeld**  >  .

      Azure implementeert uw sjabloon als een logische app voor uw opgegeven resource groep.

1. Alle Logic apps die u migreert vanuit automatische energie, worden geïmplementeerd met een uitgeschakelde status. Voordat u uw logische app inschakelt, moet u de volgende stappen uitvoeren om nieuwe verbindingen te autoriseren:

   1. Open de logische app die u hebt gemaakt in de Azure Portal. Selecteer in het menu van de logische app de optie **Logic app Designer**.

      Voor elke verbinding waarvoor autorisatie is vereist, wordt een waarschuwings pictogram weer gegeven:

      ![Waarschuwingspictogram](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Voor elke stap waarvoor een geautoriseerde verbinding is vereist, vouwt u die stap uit en selecteert u **nieuwe toevoegen**.

      ![Scherm afbeelding met de knop Nieuw toevoegen geselecteerd in het venster verbindingen van Outlook.](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Meld u aan bij elke service of geef de benodigde referenties op om de verbinding te autoriseren.

   1. Nadat u uw verbindingen hebt bijgewerkt, selecteert u op de werk balk ontwerpen de optie **Opslaan**.

1. Wanneer u klaar bent om uw logische app te activeren, selecteert u **overzicht** in het menu van de logische app en selecteert u vervolgens **inschakelen**.

   ![Logische app inschakelen](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Om te voor komen dat dubbele werk stromen worden uitgevoerd, moet u ervoor zorgen dat u de oorspronkelijke stroom deactiveert of verwijdert.

## <a name="deploy-template-by-using-visual-studio"></a>Een sjabloon implementeren met behulp van Visual Studio

Als u Visual Studio hebt ingesteld met de [vereisten](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) voor het maken van Logic apps, kunt u de geëxporteerde sjabloon implementeren naar Azure Logic apps met behulp van Visual Studio.

1. Ga in Visual Studio naar en open het. JSON-bestand voor de sjabloon van de logische app die u hebt geëxporteerd uit automatische stroom.

1. Maak in Visual Studio een **Azure-resource groep** -project dat gebruikmaakt van de sjabloon **Logic app** door de stappen in Quick Start te volgen [: Maak geautomatiseerde taken, processen en werk stromen met Azure Logic apps-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

   In dit voor beeld wordt een Visual Studio-oplossing met de naam ' ImportedLogicApp ' gemaakt.

   ![Een Azure-resourcegroepproject maken](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. Nadat de oplossing is gemaakt, opent u in Solution Explorer de **LogicApp.jsin** het bestand als het bestand nog niet is geopend.

1. Kopieer de inhoud van de geëxporteerde sjabloon en vervang de inhoud in de **LogicApp.js** in het bestand.

1. Voordat u uw logische app implementeert, moet u de volgende stappen uitvoeren om nieuwe verbindingen te autoriseren:

   1. Open de **LogicApp.jsin** het snelmenu en selecteer vervolgens **openen met Logic app Designer**.

      ![Sjabloon openen met Logic app Designer](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Als u hierom wordt gevraagd, selecteert u het Azure-abonnement en de resource groep die u wilt gebruiken voor het implementeren van uw logische app.

      ![Azure-abonnement en-resource groep selecteren](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Nadat de logische app in de ontwerp functie wordt weer gegeven, geven verbindingen waarvoor autorisatie waarschuwings pictogrammen moeten worden weer gegeven:

      ![Verbindingen met waarschuwings pictogrammen](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Voor elke stap waarvoor een geautoriseerde verbinding is vereist, vouwt u die stap uit en selecteert u **nieuwe toevoegen**.

      ![Nieuwe verbinding toevoegen](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Meld u aan bij elke service of geef de benodigde referenties op om de verbinding te autoriseren.

   1. Sla uw oplossing op voordat u de logische app implementeert.

1. Open in Solution Explorer het snelmenu project en selecteer   >  **nieuwe** implementeren. Meld u aan met uw Azure-account als u daarom wordt gevraagd.

1. Wanneer u hierom wordt gevraagd, bevestigt u het Azure-abonnement, de Azure-resource groep en alle andere instellingen die u wilt gebruiken voor implementatie, zoals een [parameter bestand](../azure-resource-manager/templates/parameter-files.md) dat moet worden gebruikt voor het door geven van sjabloon parameter waarden, en selecteer vervolgens **implementeren**.

   ![Implementatie-instellingen bevestigen](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Als het vak **para meters bewerken** wordt weer gegeven, geeft u de naam op voor de logische app-resource in Azure en selecteert u **Opslaan**.  

   ![Implementatie parameters bewerken](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Wanneer de implementatie wordt gestart, wordt de status van de implementatie van uw app weergegeven in het **Uitvoer**-venster van Visual Studio. Als de status niet wordt weergegeven, opent u de lijst **Uitvoer tonen van** en selecteert u uw Azure-resourcegroep. Bijvoorbeeld:

   ![Uitvoervenster](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Als er verbindingen in uw logische app moeten worden ingevoerd, wordt er een Power shell-venster geopend op de achtergrond en wordt u gevraagd om de benodigde wacht woorden of geheime sleutels. Nadat u deze gegevens hebt ingevoerd, wordt de implementatie voortgezet.

   ![Verbindingen verifiëren](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Nadat de implementatie is voltooid, wordt uw logische app gepubliceerd, maar wordt deze niet geactiveerd in de Azure Portal.

1. Wanneer u klaar bent om uw logische app te activeren in de Azure Portal, kunt u uw logische app zoeken en openen in de ontwerp functie voor logische apps. Selecteer **overzicht** in het menu van de logische app en selecteer vervolgens **inschakelen**.

1. Om te voor komen dat dubbele werk stromen worden uitgevoerd, moet u ervoor zorgen dat u de oorspronkelijke stroom deactiveert of verwijdert.

Voor meer informatie over deze implementaties tappen raadpleegt u [Quick Start: geautomatiseerde taken, processen en werk stromen maken met Azure Logic apps-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [connectors voor Azure Logic apps](../connectors/apis-list.md)
* Meer informatie over [Azure Logic apps](../logic-apps/logic-apps-overview.md)
