---
title: Logische apps testen met voorbeeldgegevens
description: Statische resultaten instellen voor het testen van logische apps met gegevens over modellen zonder dat dit van invloed is op productie omgevingen
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: 711d753203aeaeba50cea692053a37fcab2e9c7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93027700"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Logische Apps testen met gegevens modeleren door statische resultaten in te stellen

Bij het testen van uw logische apps bent u mogelijk niet in de hand om diverse redenen om apps, services en systemen te bellen of te openen. Normaal gesp roken is het mogelijk dat u verschillende paden voor voor waarden moet uitvoeren, fouten moet afdwingen, specifieke bericht tekst wilt opgeven of zelfs een aantal stappen wilt overs Laan. Door statische resultaten in te stellen voor een actie in uw logische app, kunt u uitvoer gegevens uit die actie detrekken. Als u statische resultaten voor een actie inschakelt, wordt de actie niet uitgevoerd, maar worden de gegevens voor de model ring wel geretourneerd.

Als u bijvoorbeeld statische resultaten voor de e-mail actie Outlook 365-verzen ding hebt ingesteld, retourneert de engine van de Logic Apps alleen de model gegevens die u hebt opgegeven als statische resultaten, in plaats van Outlook aan te roepen en een e-mail te verzenden.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, <a href="https://azure.microsoft.com/free/" target="_blank">registreer u dan nu voor een gratis Azure-account</a>.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u statische resultaten wilt instellen

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Statische resultaten instellen

1. Als u dat nog niet hebt gedaan, opent u de logische app in de Logic Apps designer in de [Azure Portal](https://portal.azure.com).

1. Voer de volgende stappen uit op de actie waar u statische resultaten wilt instellen: 

   1. Klik in de rechter bovenhoek van de actie op de knop met weglatings tekens (*...*) en selecteer **statisch resultaat**, bijvoorbeeld:

      ![Selecteer statisch resultaat > ' statisch resultaat inschakelen '](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Kies **statisch resultaat inschakelen**. Geef voor de vereiste eigenschappen (*) de uitvoer waarden van de model op die u wilt retour neren voor de reactie van de actie.

      Dit zijn bijvoorbeeld de vereiste eigenschappen voor de HTTP-actie:

      | Eigenschap | Beschrijving |
      |----------|-------------|
      | **Status** | De status van de actie die moet worden geretourneerd |
      | **Status code** | De specifieke status code die moet worden geretourneerd |
      | **Kopteksten** | De inhoud van de header die moet worden geretourneerd |
      |||

      ![Selecteer ' statisch resultaat inschakelen '](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Als u de gegevens voor de Modeling in JavaScript Object Notation indeling (JSON) wilt invoeren, kiest **u overschakelen naar JSON-modus** ( ![ Kies overschakelen naar JSON ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) -modus).

   1. Voor optionele eigenschappen opent u de lijst **optionele velden selecteren** en selecteert u de eigenschappen die u wilt weer geven.

      ![Optionele eigenschappen selecteren](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Wanneer u klaar bent om op te slaan, kiest u **gereed**.

   In de rechter bovenhoek van de actie toont de titel balk nu een pictogram voor een test beker glas ( ![ pictogram voor statische resultaten ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ). Dit geeft aan dat u statische resultaten hebt ingeschakeld.

   ![Pictogram met ingeschakelde statische resultaten](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Zie [Zoek uitvoeringen die statische resultaten gebruiken](#find-runs-mock-data) verderop in dit onderwerp voor informatie over eerdere uitvoeringen die gebruikmaken van model gegevens.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Eerdere uitvoer opnieuw gebruiken

Als uw logische app een vorige uitvoering heeft met uitvoer die u kunt hergebruiken als uitvoer van een model, kunt u de uitvoer van die uitvoering kopiëren en plakken.

1. Als u dat nog niet hebt gedaan, opent u de logische app in de Logic Apps designer in de [Azure Portal](https://portal.azure.com).

1. Selecteer **overzicht** in het hoofd menu van de logische app.

1. Selecteer in de sectie uitvoerings **geschiedenis** de gewenste logische App-toepassing.

1. Zoek en vouw in de werk stroom van de logische app de actie uit die de gewenste uitvoer heeft.

1. Kies de koppeling **onbewerkte uitvoer weer geven** .

1. Kopieer het volledige JavaScript Object Notation (JSON)-object of de specifieke Subsectie die u wilt gebruiken, bijvoorbeeld de sectie outputs of zelfs alleen de sectie headers.

1. Volg de stappen voor het openen van het **statische resultaat** vak voor uw actie in [statische resultaten instellen](#set-up-static-results).

1. Nadat het vak **statisch resultaat** wordt geopend, kiest u stap:

   * Als u een volledig JSON-object wilt plakken, kiest **u overschakelen naar JSON-modus** ( ![ Kies overschakelen naar JSON ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) -modus):

     ![Kies overschakelen naar JSON-modus voor een volledig object](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Als u alleen een JSON-gedeelte wilt plakken naast het label van die sectie, kiest **u overschakelen naar JSON-modus** voor die sectie, bijvoorbeeld:

     ![Kies overschakelen naar JSON-modus voor uitvoer](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. Plak in de JSON-editor de eerder gekopieerde JSON.

   ![JSON-modus](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Wanneer u klaar bent, kiest u **Gereed**. Als u wilt terugkeren naar de ontwerp functie, kiest u **modus switch editor** ( ![ Kies de modus switch editor ](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png) ).

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Zoek runs die statische resultaten gebruiken

De uitvoerings geschiedenis van de logische app identificeert de uitvoeringen waar de acties statische resultaten gebruiken. Voer de volgende stappen uit om deze uitvoeringen te vinden:

1. Selecteer **overzicht** in het hoofd menu van de logische app. 

1. Zoek in het rechterdeel venster onder **uitvoerings geschiedenis** de kolom **statische resultaten** . 

   Voor elke uitvoering die acties met resultaten bevat, is de kolom **statische resultaten** ingesteld op **ingeschakeld**, bijvoorbeeld:

   ![Uitvoerings geschiedenis-kolom met statische resultaten](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Als u acties wilt weer geven die statische resultaten gebruiken, selecteert u de gewenste uitvoeren waar de kolom **statische resultaten** is ingesteld op **ingeschakeld**.

   Acties die statische resultaten gebruiken, tonen het pictogram test bekers ( ![ pictogram voor statische resultaten ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ), bijvoorbeeld:

   ![Uitvoerings geschiedenis-acties die statische resultaten gebruiken](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Statische resultaten uitschakelen

Als u statische resultaten uitschakelt, worden de waarden niet uit de laatste installatie verwijderd. Dus wanneer u statische resultaten de volgende keer inschakelt, kunt u de vorige waarden blijven gebruiken.

1. Zoek de actie waarvoor u statische uitvoer wilt uitschakelen. In de rechter bovenhoek van de actie kiest u het pictogram test beker glas ( ![ pictogram voor statische resultaten ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ).

   ![In de scherm afbeelding wordt een H T T P-actie weer gegeven, waar u het pictogram voor het test beker glas kunt selecteren.](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Kies **uitschakelen statisch resultaat**  >  **gereed**.

   ![Scherm afbeelding toont de optie statisch resultaat uitschakelen die u kunt selecteren.](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Referentie

Zie [statische resultaten-schema verwijzing voor werk stroom definitie taal](../logic-apps/logic-apps-workflow-definition-language.md#static-results) en [runtimeConfiguration. staticResult-runtime-configuratie-instellingen](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings) voor meer informatie over deze instelling in uw onderliggende werk stroom definities.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Logic apps](../logic-apps/logic-apps-overview.md)