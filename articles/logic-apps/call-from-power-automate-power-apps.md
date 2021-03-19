---
title: Logische apps aanroepen vanuit Power Automate en Power Apps
description: Logische apps aanroepen vanuit micro soft power stroom Automatiseer stromen door Logic apps te exporteren als connectors.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: b402dab4c6e94a7634e11f0330b5379315e43abf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91762479"
---
# <a name="call-logic-apps-from-power-automate-and-power-apps"></a>Logische apps aanroepen vanuit Power Automate en Power Apps

Als u uw Logic Apps wilt aanroepen vanuit micro soft power Automatiseer en micro soft power apps, kunt u uw Logic apps exporteren als connectors. Wanneer u een logische app beschikbaar maakt als een aangepaste connector in een Power automatiseerde of Power apps-omgeving, kunt u de logische app vervolgens aanroepen vanuit stromen.

Als u in plaats daarvan uw stroom wilt migreren vanuit Automatische stroom of de stroom naar Logic Apps, raadpleegt u [stromen exporteren vanuit energie automatisering en implementeren naar Azure Logic apps](export-from-microsoft-flow-logic-app-template.md).

> [!NOTE]
> Niet alle stroom verautomatiseerde connectors zijn beschikbaar in Azure Logic Apps. U kunt alleen stroom geautomatiseerde stromen met de equivalente connectors in Azure Logic Apps migreren. De knop trigger, de goedkeurings connector en de notification connector zijn bijvoorbeeld specifiek voor het automatiseren van het energie verbruik. Op dit moment worden OpenAPI stromen in energie automatisering niet ondersteund voor exporteren en implementeren als sjablonen voor logische apps.
>
> * Als u wilt weten welke stroom lijnen voor automatische automatisering geen Logic Apps equivalenten hebben, raadpleegt u [connectors](/connectors/connector-reference/connector-reference-powerautomate-connectors)voor het automatiseren van de stroom.
>
> * Zie [Logic apps connectors](/connectors/connector-reference/connector-reference-powerautomate-connectors)als u wilt weten welke Logic apps connectors geen energie automatisering hebben.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een licentie voor energie automatisering of Power apps.

* Een logische app met een trigger voor het exporteren van een aanvraag.

* Een stroom in energie automatisering of Power apps van waaruit u uw logische app wilt aanroepen.

## <a name="export-your-logic-app-as-a-custom-connector"></a>Uw logische app exporteren als een aangepaste connector

Voordat u uw logische app kunt aanroepen vanuit automatische energie of Power apps, moet u eerst uw logische app exporteren als een aangepaste connector.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Voer in het zoekvak van Azure Portal in `Logic Apps` . Selecteer in de resultaten onder **Services** de optie **Logic apps**.

1. Selecteer de logische app die u wilt exporteren.

1. Selecteer **exporteren** in het menu van de logische app.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app.png" alt-text="Scherm afbeelding van de pagina van de logische app in Azure Portal, waarin het menu met de knop export is geselecteerd.":::

1. Voer in het deel venster **exporteren** bij **naam** een naam in voor de aangepaste connector voor uw logische app. Selecteer in de lijst **omgeving** de omgeving energie automatisering of Power apps waarvan u de logische app wilt aanroepen. Wanneer u gereed bent, selecteert u **OK**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app2.png" alt-text="Scherm afbeelding van het deel venster exporteren voor een logische app, met de vereiste velden voor de naam en omgeving van de aangepaste connector.":::

1. Controleer het deel venster meldingen om te controleren of uw logische app is geëxporteerd.

### <a name="exporting-errors"></a>Fouten exporteren

Hier volgen fouten die kunnen optreden wanneer u uw logische app exporteert als een aangepaste connector en de voorgestelde oplossingen:

* **Kan geen omgevingen ophalen. Zorg ervoor dat uw account is geconfigureerd voor automatische energie en probeer het opnieuw.**: Controleer of uw Azure-account een plan voor het automatiseren van de stroom heeft.

* **De huidige logische app kan niet worden geëxporteerd. Als u wilt exporteren, selecteert u een logische app die een aanvraag trigger heeft.**: Controleer of uw logische app begint met een [trigger voor aanvragen](./logic-apps-workflow-actions-triggers.md#request-trigger).

## <a name="connect-to-your-logic-app-from-power-automate"></a>Verbinding maken met uw logische app vanuit Automatische stroom

Verbinding maken met de logische app die u hebt geëxporteerd met uw stroom voor het automatiseren van de stroom:

1. Meld u aan bij [Power Automate](https://flow.microsoft.com).

1. Selecteer **mijn stromen** in het menu Start pagina van **Power page automatiseren** .

1. Selecteer op de pagina **stromen** de stroom die u wilt verbinden met uw logische app.

1. Selecteer in het menu van de stroom pagina de optie **bewerken**.

1. Selecteer **&#43; nieuwe stap** in de flow-editor.

1. Onder **Kies een actie**, typt u in het zoekvak de naam van de logische app-connector. Als u wilt dat alleen de aangepaste connectors in uw omgeving worden weer gegeven, filtert u de resultaten door het **aangepaste** tabblad te selecteren.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-automate-custom-connector-action.png" alt-text="Scherm afbeelding van automatische stroom editor, met een nieuwe stap die wordt toegevoegd voor de aangepaste connector en beschik bare acties.":::

1. Selecteer de actie die u wilt uitvoeren met de logische app-connector. 

1. Geef de informatie op die door de actie wordt door gegeven aan de logische app-connector.

1. Als u de wijzigingen wilt opslaan, selecteert u in het menu van de Power autoautomatiseer-editor de optie **Opslaan**.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Zoek in de Logic Apps-service de logische app die u hebt geëxporteerd.

1. Controleer of uw logische app werkt zoals u verwacht in uw stroom voor het automatiseren van uw stroom.

## <a name="delete-logic-app-connector-from-power-automate"></a>Logische app-connector verwijderen uit automatische stroom

1. Meld u aan bij [Power Automate](https://flow.microsoft.com).

1. Selecteer op de start pagina voor het **automatisch opstarten** van **gegevens** &gt; **aangepaste connectors** in het menu.

1. Zoek uw aangepaste connector in de lijst en selecteer de knop met weglatings tekens (**...**) &gt; **verwijderen**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Scherm afbeelding van de pagina aangepaste connectors automatiseren, waarmee de aangepaste connector beheer knoppen van de logische app worden weer gegeven.":::

1. Selecteer **OK** om de verwijdering te bevestigen.

## <a name="connect-to-your-logic-app-from-power-apps"></a>Verbinding maken met uw logische app vanuit Power apps

Verbinding maken met de logische app die u hebt geëxporteerd met uw Power apps-stroom:

1. Meld u aan bij [Power apps](https://powerapps.microsoft.com/).

1. Op de start pagina van **Power apps** selecteert u **stromen** in het menu.

1. Selecteer op de pagina **stromen** de stroom die u wilt verbinden met uw logische app.

1. Selecteer op de pagina stroom de optie **bewerken** in het menu van de stroom.

1. Selecteer in de flow-editor de knop **&#43; nieuwe stap** .

1. Onder **Kies een actie** in de nieuwe stap, typt u de naam van de logische app-connector in het zoekvak. U kunt de resultaten ook filteren op het tabblad **aangepast** om alleen aangepaste connectors in uw omgeving weer te geven.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-apps-custom-connector-action.png" alt-text="Scherm afbeelding van de flow-editor van Power-apps, met een nieuwe stap die wordt toegevoegd voor de aangepaste connector en beschik bare acties.":::

1. Selecteer de actie die u wilt uitvoeren met de connector. 

1. Configureer welke informatie uw actie doorgeeft aan de logische app-connector.

1. Selecteer in het menu Power apps-editor de optie **Opslaan** om uw wijzigingen op te slaan. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Zoek in de Logic Apps-service de logische app die u hebt geëxporteerd.

1. Controleer of uw logische app werkt zoals is bedoeld voor uw Power apps-stroom.

## <a name="delete-logic-app-connector-from-power-apps"></a>Logische app-connector verwijderen uit Power apps

1. Meld u aan bij [Power apps](https://powerapps.microsoft.com).

1. Op de start pagina van **Power apps** selecteert u **gegevens** &gt; **aangepaste connectors** in het menu.

1. Zoek uw aangepaste connector in de lijst en selecteer de knop met weglatings tekens (**...**) &gt; **verwijderen**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Scherm afbeelding van de pagina aangepaste connectors van Power apps, met de aangepaste knop beheer knoppen van de logische app.":::

1. Selecteer **OK** om de verwijdering te bevestigen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [connectors voor Azure Logic apps](../connectors/apis-list.md)
* Meer informatie over [Azure Logic apps](../logic-apps/logic-apps-overview.md)
