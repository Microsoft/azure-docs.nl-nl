---
title: Bewerkingen uitvoeren op gegevens
description: Gegevens uitvoer en-indelingen in Azure Logic Apps converteren, beheren en bewerken
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: baa6e5732221d120ff71217a3a86a942794c53f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84710368"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Gegevens bewerkingen uitvoeren in Azure Logic Apps

In dit artikel wordt beschreven hoe u kunt werken met gegevens in logische apps door acties toe te voegen voor deze taken en meer:

* Tabellen maken op basis van matrices.
* Matrices maken op basis van een voor waarde van andere matrices.
* Maak gebruikers vriendelijke tokens van JavaScript Object Notation (JSON)-object eigenschappen, zodat u deze eigenschappen eenvoudig kunt gebruiken in uw werk stroom.

Als u hier niet de gewenste actie vindt, kunt u bladeren door de vele verschillende [functies voor gegevens manipulatie](../logic-apps/workflow-definition-language-functions-reference.md) die Azure Logic Apps biedt.

Deze tabellen zijn een overzicht van de gegevens bewerkingen die u kunt gebruiken en zijn ingedeeld op basis van de brongegevens typen waarmee de bewerkingen werken, maar elke beschrijving wordt alfabetisch weer gegeven.

**Matrix acties** 

Deze acties helpen u bij het werken met gegevens in matrices.

| Bewerking | Beschrijving |
|--------|-------------|
| [**CSV-tabel maken**](#create-csv-table-action) | Een tabel met door komma's gescheiden waarden (CSV) maken op basis van een matrix. |
| [**HTML-tabel maken**](#create-html-table-action) | Een HTML-tabel maken op basis van een matrix. |
| [**Matrix filteren**](#filter-array-action) | Maak een matrix subset van een matrix op basis van het opgegeven filter of voor waarde. |
| [**Deelnemen**](#join-action) | Maak een teken reeks van alle items in een matrix en scheid elk item met het opgegeven teken. |
| [**Selecteer**](#select-action) | Een matrix maken op basis van de opgegeven eigenschappen voor alle items in een andere matrix. |
||| 

**JSON-acties**

Deze acties helpen u bij het werken met gegevens in de indeling van JavaScript Object Notation (JSON).

| Bewerking | Beschrijving |
|--------|-------------|
| [**Compose**](#compose-action) | Maak een bericht of teken reeks van meerdere invoer die verschillende gegevens typen kan hebben. U kunt deze teken reeks vervolgens als één invoer gebruiken, in plaats van dezelfde invoer herhaaldelijk in te voeren. U kunt bijvoorbeeld één JSON-bericht maken van verschillende invoer. |
| [**JSON parseren**](#parse-json-action) | Maak gebruikers vriendelijke gegevens tokens voor eigenschappen in JSON-inhoud zodat u de eigenschappen in uw Logic apps gemakkelijker kunt gebruiken. |
|||

Zie [Geavanceerde JSON-trans formaties uitvoeren met vloeistof sjablonen](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md)als u complexere JSON-trans formaties wilt maken.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De logische app waar u de bewerking nodig hebt voor het werken met gegevens

  Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md) en [Quick Start: uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) als de eerste stap in uw logische app 

  Gegevens bewerkingen zijn alleen beschikbaar als acties, dus voordat u deze acties kunt gebruiken, start u de logische app met een trigger en neemt u eventuele andere acties op die nodig zijn voor het maken van de gewenste uitvoer.

<a name="compose-action"></a>

## <a name="compose-action"></a>Actie opstellen

Als u één uitvoer wilt maken, zoals een JSON-object van meerdere invoer, kunt u de actie **opstellen** gebruiken. Uw invoer kan verschillende typen hebben, zoals gehele getallen, Booleaanse waarden, matrices, JSON-objecten en andere systeem eigen typen die Azure Logic Apps ondersteunt, bijvoorbeeld binary en XML. U kunt vervolgens de uitvoer gebruiken in acties die volgen na de actie **opstellen** . Met de actie **opstellen** kan ook worden bespaard dat u herhaaldelijk dezelfde invoer hoeft in te voeren terwijl u de werk stroom van de logische app bouwt.

U kunt bijvoorbeeld een JSON-bericht maken op basis van meerdere variabelen, zoals teken reeks variabelen die de voor-en achternamen van personen opslaan, en een variabele met gehele getallen die de leeftijden van mensen opslaat. Hier accepteert de actie **opstellen** de volgende invoer:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

en maakt deze uitvoer:

`{"age":35,"fullName":"Owens,Sophie"}`

Volg deze stappen met behulp van de ontwerp functie voor logische apps als u een voor beeld wilt uitproberen. Als u liever aan de slag gaat met de code weergave-editor, kunt u het voor beeld **opstellen** en **variabele** actie definities van dit artikel naar de onderliggende werk stroom definitie van uw eigen logische app kopiëren: [voor beelden van gegevens bewerkings code-opstellen](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. Open in de [Azure Portal](https://portal.azure.com) of Visual Studio uw logische app in de ontwerp functie voor logische apps.

   In dit voor beeld wordt gebruikgemaakt van de Azure Portal en een logische app met een **terugkeer patroon** en verschillende **variabele acties initialiseren** . Deze acties worden ingesteld voor het maken van twee teken reeks variabelen en een variabele geheel getal. Wanneer u de logische app later test, kunt u uw app hand matig uitvoeren zonder te wachten tot de trigger wordt geactiveerd.

   ![Voor beeld van een logische app voor de actie ' opstellen ' starten](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. Voer een van de volgende stappen uit in uw logische app waar u de uitvoer wilt maken: 

   * Selecteer **nieuwe stap** om een actie toe te voegen onder de laatste stap.

     ![Selecteer nieuwe stap voor de actie opstellen](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de verbindings pijl zodat het plus teken ( **+** ) wordt weer gegeven. Selecteer het plus teken en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak onder **Kies een actie** `compose` in als uw filter. Selecteer in de lijst acties de actie **opstellen** .

   ![Actie ' opstellen ' selecteren](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. Geef in het vak **invoer** de gewenste invoer op voor het maken van de uitvoer.

   Voor dit voor beeld wordt de lijst met dynamische inhoud weer gegeven wanneer u in het vak **invoer** klikt, zodat u de eerder gemaakte variabelen kunt selecteren:

   ![Invoer selecteren die voor de actie ' opstellen ' moet worden gebruikt](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Dit is de voltooide voorbeeld actie voor het **opstellen** : 

   ![Voltooid voor beeld voor de actie opstellen](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

Zie voor meer informatie over deze actie in uw onderliggende werk stroom definitie de [actie opstellen](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action).

### <a name="test-your-logic-app"></a>Uw logische app testen

Als u wilt controleren of met de actie **opstellen** de verwachte resultaten worden gemaakt, stuurt u een melding die uitvoer van de actie **opstellen** bevat.

1. Voeg in uw logische app een actie toe die u de resultaten van de actie **opstellen** kan sturen.

1. Klik in die actie op een wille keurige plaats waar u de resultaten wilt weer geven. Wanneer de lijst met dynamische inhoud wordt geopend, selecteert u onder de actie **opstellen** de optie **uitvoer**.

   Dit voor beeld maakt gebruik van de actie **een E-mail verzenden** en bevat de **uitvoer** velden in de hoofd tekst van de e-mail en het onderwerp:

   ![Uitvoer velden voor de actie opstellen](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. Voer nu de logische app hand matig uit. Selecteer **uitvoeren** op de werk balk van de ontwerp functie.

   Op basis van de e-mail connector die u hebt gebruikt, zijn dit de resultaten die u krijgt:

   ![E-mail met actie resultaten voor opstellen](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Actie CSV-tabel maken

Gebruik de actie **CSV-tabel maken** om een tabel met door komma's gescheiden waarden (CSV) met de eigenschappen en waarden van JavaScript object NOTATION (JSON)-objecten in een matrix te maken. U kunt de resulterende tabel vervolgens gebruiken in acties die volgen op de actie **CSV-tabel maken** .

Als u liever met de code weergave-editor werkt, kunt u het voor beeld voor het maken van een **CSV-tabel** en het **initialiseren van variabele** actie definities vanuit dit artikel in de onderliggende werk stroom definitie van uw eigen logische app kopiëren: [voor beelden van gegevens bewerkings code-CSV-tabel maken](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. Open in de [Azure Portal](https://portal.azure.com) of Visual Studio uw logische app in de ontwerp functie voor logische apps.

   In dit voor beeld wordt gebruikgemaakt van de Azure Portal en een logische app met een **terugkeer patroon** trigger en een actie voor **het initialiseren van een variabele** . De actie is ingesteld voor het maken van een variabele waarvan de begin waarde een matrix is met een aantal eigenschappen en waarden in de JSON-indeling. Wanneer u de logische app later test, kunt u uw app hand matig uitvoeren zonder te wachten tot de trigger wordt geactiveerd.

   ![Voor beeld van een logische app voor het maken van de actie CSV-tabel wordt gestart](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. Voer een van de volgende stappen uit in uw logische app waar u de CSV-tabel wilt maken: 

   * Selecteer **nieuwe stap** om een actie toe te voegen onder de laatste stap.

     ![Selecteer nieuwe stap voor de actie CSV-tabel maken](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de verbindings pijl zodat het plus teken ( **+** ) wordt weer gegeven. Selecteer het plus teken en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak onder **Kies een actie** `create csv table` in als uw filter. Selecteer in de lijst acties de actie **CSV-tabel maken** .

   ![Selecteer de actie CSV-tabel maken](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. Geef in het vak **van** de gewenste matrix of expressie op voor het maken van de tabel.

   Voor dit voor beeld wordt de lijst met dynamische inhoud weer gegeven wanneer u in het vak **van** klikt, zodat u de eerder gemaakte variabele kunt selecteren:

   ![Matrix uitvoer selecteren voor het maken van een CSV-tabel](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > Als u gebruikers vriendelijke tokens voor de eigenschappen in JSON-objecten wilt maken, moet u deze eigenschappen als invoer selecteren. Gebruik de [JSON parseren](#parse-json-action) voordat u de actie **CSV-tabel maken** aanroept.

   Hier volgt het voltooide voor beeld van een **CSV-tabel actie maken** : 

   ![Voltooid voor beeld voor de actie CSV-tabel maken](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

### <a name="customize-table-format"></a>Tabel indeling aanpassen

De eigenschap **Columns** wordt standaard ingesteld om automatisch de tabel kolommen te maken op basis van de matrix items. Voer de volgende stappen uit om aangepaste kopteksten en waarden op te geven:

1. Open de lijst met **kolommen** en selecteer **aangepast**.

1. Geef in de eigenschap **header** de aangepaste koptekst op die u in plaats daarvan wilt gebruiken.

1. Geef in de eigenschap **waarde** de aangepaste waarde op die u wilt gebruiken.

Als u waarden uit de matrix wilt retour neren, kunt u de [ `item()` functie](../logic-apps/workflow-definition-language-functions-reference.md#item) gebruiken met de actie **CSV-tabel maken** . In een `For_each` lus kunt u de [ `items()` functie](../logic-apps/workflow-definition-language-functions-reference.md#items)gebruiken.

Stel dat u bijvoorbeeld tabel kolommen wilt met alleen de eigenschaps waarden en niet de namen van eigenschappen uit een matrix. Als u alleen deze waarden wilt retour neren, volgt u deze stappen voor het werken in de ontwerp weergave of in de code weergave. Dit is het resultaat dat in dit voor beeld wordt geretourneerd:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Werken in de ontwerp weergave

In de actie moet u de kolom **koptekst** leeg laten. Op elke rij in de kolom **waarde** de verwijzing van elke matrix eigenschap die u wilt. Elke rij onder **waarde** retourneert alle waarden voor de opgegeven matrix eigenschap en wordt een kolom in de tabel.

1. Klik onder **waarde** op elke gewenste rij, in het invoervak zodat de lijst met dynamische inhoud wordt weer gegeven.

1. Selecteer in de lijst met dynamische inhoud de optie **Expressie**.

1. Voer in de expressie-Editor deze expressie in die de waarde van de eigenschap matrix opgeeft en selecteer **OK**.

   `item()?['<array-property-name>']`

   Bijvoorbeeld:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Verwijzing "Description" voor "CSV-tabel maken"](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. Herhaal de vorige stappen voor elke matrix eigenschap die u wilt. Wanneer u klaar bent, ziet uw actie eruit als in dit voor beeld:

   ![de functie item () in de CSV-tabel maken](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. Als u expressies in meer beschrijvende versies wilt omzetten, schakelt u over naar de code weergave en weer gave van de ontwerp functie en opent u de samengevouwen actie opnieuw:

   De actie **CSV-tabel maken** ziet er nu uit als in dit voor beeld:

   !["Create CSV Table"-opgeloste expressies, geen headers](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>Werken in de code weergave

Stel in de JSON-definitie van de actie binnen de `columns` matrix de `header` eigenschap in op een lege teken reeks. Voor elke `value` eigenschap de verwijzing van elke matrix eigenschap die u wilt.

1. Selecteer de **code weergave** op de werk balk van de ontwerp functie.

1. Voeg in de code-editor in de matrix van de actie `columns` de `header` eigenschap Empty en deze `value` expressie toe voor elke kolom met matrix waarden die u wilt:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Bijvoorbeeld:

   ```json
   "Create_CSV_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "CSV",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Ga terug naar de ontwerp weergave en open de samengevouwen actie opnieuw.

   De actie **CSV-tabel maken** wordt nu weer gegeven als in dit voor beeld en de expressies zijn omgezet in meer beschrijvende versies:

   !["Create CSV Table"-opgeloste expressies en geen headers](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

Zie de [tabel actie](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action)voor meer informatie over deze actie in de onderliggende werk stroom definitie.

### <a name="test-your-logic-app"></a>Uw logische app testen

Als u wilt controleren of de actie **CSV-tabel maken** de verwachte resultaten maakt, stuurt u een melding met de uitvoer van de actie **CSV-tabel maken** .

1. Voeg in uw logische app een actie toe die u de resultaten kan sturen van de actie **CSV-tabel maken** .

1. Klik in die actie op een wille keurige plaats waar u de resultaten wilt weer geven. Wanneer de lijst met dynamische inhoud wordt geopend, selecteert u onder de actie **CSV-tabel maken** de optie **uitvoer**. 

   In dit voor beeld wordt de actie Office 365 Outlook **een E-mail verzenden** gebruikt en wordt het veld **uitvoer** in de hoofd tekst van de e-mail opgenomen:

   ![Uitvoer velden voor de actie CSV-tabel maken](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. Voer nu de logische app hand matig uit. Selecteer **uitvoeren** op de werk balk van de ontwerp functie.

   Op basis van de e-mail connector die u hebt gebruikt, zijn dit de resultaten die u krijgt:

   ![E-mail bericht met actie resultaten voor het maken van een CSV-tabel](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Actie HTML-tabel maken

Als u een HTML-tabel wilt maken met de eigenschappen en waarden van de JavaScript Object Notation (JSON)-objecten in een matrix, gebruikt u de actie **HTML-tabel maken** . U kunt de resulterende tabel vervolgens gebruiken in acties die volgen op de actie **HTML-tabel maken** .

Als u liever aan de slag gaat met de code weergave-editor, kunt u het voor beeld voor het maken van een **HTML-tabel** en het **initialiseren van variabele** actie definities vanuit dit artikel in de onderliggende werk stroom definitie van uw eigen logische app kopiëren: [voor beelden van data operation-code-HTML-tabel maken](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. Open in de [Azure Portal](https://portal.azure.com) of Visual Studio uw logische app in de ontwerp functie voor logische apps.

   In dit voor beeld wordt gebruikgemaakt van de Azure Portal en een logische app met een **terugkeer patroon** trigger en een actie voor **het initialiseren van een variabele** . De actie is ingesteld voor het maken van een variabele waarvan de begin waarde een matrix is met een aantal eigenschappen en waarden in de JSON-indeling. Wanneer u de logische app later test, kunt u uw app hand matig uitvoeren zonder te wachten tot de trigger wordt geactiveerd.

   ![Voor beeld van een logische app voor het maken van een HTML-tabel](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. Voer een van de volgende stappen uit in uw logische app waar u een HTML-tabel wilt maken:

   * Selecteer **nieuwe stap** om een actie toe te voegen onder de laatste stap.

     ![Selecteer ' nieuwe stap ' voor de actie ' HTML-tabel maken '](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de verbindings pijl zodat het plus teken ( **+** ) wordt weer gegeven. Selecteer het plus teken en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak onder **Kies een actie** `create html table` in als uw filter. Selecteer in de lijst acties de actie **HTML-tabel maken** .

   ![Selecteer een actie voor het maken van een HTML-tabel](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. Geef in het vak **van** de gewenste matrix of expressie op voor het maken van de tabel.

   Voor dit voor beeld wordt de lijst met dynamische inhoud weer gegeven wanneer u in het vak **van** klikt, zodat u de eerder gemaakte variabele kunt selecteren:

   ![Matrix uitvoer selecteren voor het maken van een HTML-tabel](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > Als u gebruikers vriendelijke tokens voor de eigenschappen in JSON-objecten wilt maken, moet u deze eigenschappen als invoer selecteren. Gebruik de [JSON parseren](#parse-json-action) voordat u de actie **HTML-tabel maken** aanroept.

   Hier volgt het voltooide voor beeld van het maken van een **HTML-tabel** actie:

   ![Voltooid voor beeld voor ' HTML-tabel maken '](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

### <a name="customize-table-format"></a>Tabel indeling aanpassen

De eigenschap **Columns** wordt standaard ingesteld om automatisch de tabel kolommen te maken op basis van de matrix items. Voer de volgende stappen uit om aangepaste kopteksten en waarden op te geven:

1. Open de lijst met **kolommen** en selecteer **aangepast**.

1. Geef in de eigenschap **header** de aangepaste koptekst op die u in plaats daarvan wilt gebruiken.

1. Geef in de eigenschap **waarde** de aangepaste waarde op die u wilt gebruiken.

Als u waarden uit de matrix wilt retour neren, kunt u de [ `item()` functie](../logic-apps/workflow-definition-language-functions-reference.md#item) gebruiken met de actie **HTML-tabel maken** . In een `For_each` lus kunt u de [ `items()` functie](../logic-apps/workflow-definition-language-functions-reference.md#items)gebruiken.

Stel dat u bijvoorbeeld tabel kolommen wilt met alleen de eigenschaps waarden en niet de namen van eigenschappen uit een matrix. Als u alleen deze waarden wilt retour neren, volgt u deze stappen voor het werken in de ontwerp weergave of in de code weergave. Dit is het resultaat dat in dit voor beeld wordt geretourneerd:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Werken in de ontwerp weergave

In de actie moet u de kolom **koptekst** leeg laten. Op elke rij in de kolom **waarde** de verwijzing van elke matrix eigenschap die u wilt. Elke rij onder **waarde** retourneert alle waarden voor de opgegeven eigenschap en wordt een kolom in de tabel.

1. Klik onder **waarde** op elke gewenste rij, in het invoervak zodat de lijst met dynamische inhoud wordt weer gegeven.

1. Selecteer in de lijst met dynamische inhoud de optie **Expressie**.

1. Voer in de expressie-Editor deze expressie in die de waarde van de eigenschap matrix opgeeft en selecteer **OK**.

   `item()?['<array-property-name>']`

   Bijvoorbeeld:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![De verwijzings eigenschap in de actie ' HTML-tabel maken '](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. Herhaal de vorige stappen voor elke matrix eigenschap die u wilt. Wanneer u klaar bent, ziet uw actie eruit als in dit voor beeld:

   ![de functie item () in ' HTML-tabel maken '](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. Als u expressies in meer beschrijvende versies wilt omzetten, schakelt u over naar de code weergave en weer gave van de ontwerp functie en opent u de samengevouwen actie opnieuw:

   De actie **HTML-tabel maken** ziet er nu uit als in dit voor beeld:

   !["HTML-tabel maken"-opgeloste expressies, geen headers](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>Werken in de code weergave

Stel in de JSON-definitie van de actie binnen de `columns` matrix de `header` eigenschap in op een lege teken reeks. Voor elke `value` eigenschap de verwijzing van elke matrix eigenschap die u wilt.

1. Selecteer de **code weergave** op de werk balk van de ontwerp functie.

1. Voeg in de code-editor in de matrix van de actie `columns` de `header` eigenschap Empty en deze `value` expressie toe voor elke kolom met matrix waarden die u wilt:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Bijvoorbeeld:

   ```json
   "Create_HTML_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "HTML",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Ga terug naar de ontwerp weergave en open de samengevouwen actie opnieuw.

   De actie **HTML-tabel maken** wordt nu weer gegeven als in dit voor beeld en de expressies zijn omgezet in meer beschrijvende versies:

   !["HTML-tabel maken"-opgeloste expressies en geen headers](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

Zie de [tabel actie](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action)voor meer informatie over deze actie in de onderliggende werk stroom definitie.

### <a name="test-your-logic-app"></a>Uw logische app testen

Als u wilt controleren of de actie **HTML-tabel maken** de verwachte resultaten genereert, stuurt u een melding die uitvoer bevat van de actie **HTML-tabel maken** .

1. Voeg in uw logische app een actie toe die u de resultaten kan sturen van de actie **HTML-tabel maken** .

1. Klik in die actie op een wille keurige plaats waar u de resultaten wilt weer geven. Wanneer de lijst met dynamische inhoud wordt geopend, selecteert u onder de actie **HTML-tabel maken** de optie **uitvoer**. 

   In dit voor beeld wordt de actie Office 365 Outlook **een E-mail verzenden** gebruikt en wordt het veld **uitvoer** in de hoofd tekst van de e-mail opgenomen:

   ![Uitvoer velden voor ' HTML-tabel maken '](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > Wanneer u de HTML-tabel uitvoer in een e-mail actie opneemt, moet u ervoor zorgen dat u de eigenschap **HTML is** ingesteld op **Ja** in de geavanceerde opties voor de e-mail actie. Op die manier wordt de HTML-tabel op de juiste wijze door de e-mail actie opgemaakt.

1. Voer nu de logische app hand matig uit. Selecteer **uitvoeren** op de werk balk van de ontwerp functie.

   Op basis van de e-mail connector die u hebt gebruikt, zijn dit de resultaten die u krijgt:

   ![E-mail met resultaten voor het maken van een HTML-tabel](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Actie filter matrix

Gebruik de actie **matrix filteren** om een kleinere matrix te maken die items bevat die voldoen aan specifieke criteria, van een bestaande matrix. U kunt vervolgens de gefilterde matrix gebruiken in acties die volgen na de actie **filter matrix** .

> [!NOTE]
> Alle filter tekst die u in uw voor waarde gebruikt, is hoofdletter gevoelig. Deze actie kan ook de indeling of onderdelen van items in de matrix niet wijzigen. 
> 
> Voor acties om de matrix uitvoer van de **filter matrix** actie te gebruiken, moeten die acties matrices accepteren als invoer, of moet u de uitvoer matrix omzetten in een andere compatibele indeling.
> 
> Als u een HTTP-eind punt aanroept en een JSON-antwoord ontvangt, gebruikt u de actie **JSON parseren** om de JSON-reactie te verwerken. 
> Anders kan de actie **filter matrix** alleen de tekst van het antwoord lezen en niet de structuur van de JSON-nettolading.

Als u liever aan de slag gaat met de code weergave-editor, kunt u de voorbeeld **filter matrix** kopiëren en variabele actie definities **initialiseren** vanuit dit artikel in de onderliggende werk stroom definitie van uw eigen logische app: [Data operation code-voor beelden-filter matrix](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. Open in de [Azure Portal](https://portal.azure.com) of Visual Studio uw logische app in de ontwerp functie voor logische apps.

   In dit voor beeld wordt gebruikgemaakt van de Azure Portal en een logische app met een **terugkeer patroon** trigger en een actie voor **het initialiseren van een variabele** . De actie is ingesteld voor het maken van een variabele waarvan de begin waarde een matrix is met een aantal voor beelden van gehele getallen. Wanneer u de logische app later test, kunt u uw app hand matig uitvoeren zonder te wachten tot de trigger wordt geactiveerd.

   > [!NOTE]
   > Hoewel in dit voor beeld een eenvoudige matrix met gehele getallen wordt gebruikt, is deze actie vooral nuttig voor JSON-object matrices, waar u kunt filteren op basis van de eigenschappen en waarden van de objecten.

   ![Voorbeeld logische app voor de actie filter matrix wordt gestart](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. Voer een van de volgende stappen uit in uw logische app waar u de gefilterde matrix wilt maken: 

   * Selecteer **nieuwe stap** om een actie toe te voegen onder de laatste stap.

     ![Selecteer ' nieuwe stap ' voor de actie ' filter matrix '](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de verbindings pijl zodat het plus teken ( **+** ) wordt weer gegeven. Selecteer het plus teken en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak `filter array` als uw filter in. Selecteer in de lijst acties de actie **matrix filteren** .

   ![Selecteer de actie filter matrix](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. Geef in het vak **van** de matrix of expressie op die u wilt filteren.

   Voor dit voor beeld wordt de lijst met dynamische inhoud weer gegeven wanneer u in het vak **van** klikt, zodat u de eerder gemaakte variabele kunt selecteren:

   ![Matrix uitvoer selecteren voor het maken van een gefilterde matrix](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. Voor de voor waarde geeft u de matrix items op die u wilt vergelijken, selecteert u de vergelijkings operator en geeft u de vergelijkings waarde op.

   In dit voor beeld wordt de `item()` functie gebruikt voor het openen van elk item in de matrix terwijl de actie **filter matrix** zoekt naar matrix items waarvan de waarde groter is dan een:

   ![Het voor beeld van een actie ' filter matrix ' is voltooid](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

Zie [query actie](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action)voor meer informatie over deze actie in uw onderliggende werk stroom definitie.

### <a name="test-your-logic-app"></a>Uw logische app testen

Als u wilt controleren of met de actie **filter matrix** de verwachte resultaten worden gemaakt, stuurt u een melding die uitvoer van de actie **filter matrix** bevat.

1. Voeg in uw logische app een actie toe die u de resultaten van de **filter matrix** actie kan sturen.

1. Klik in die actie op een wille keurige plaats waar u de resultaten wilt weer geven. Wanneer de lijst met dynamische inhoud wordt geopend, selecteert u **expressie**. Als u de matrix uitvoer wilt ophalen van de **filter matrix** actie, voert u deze expressie in met de actie naam van de **filter matrix** :

   `@actionBody('Filter_array')`

   In dit voor beeld wordt de actie Office 365 Outlook **een E-mail verzenden** gebruikt en worden de uitvoer van de actionBody-expressie **(' Filter_array ')** in de hoofd tekst van de e-mail opgenomen:

   ![Actie uitvoer van de actie filter matrix](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. Voer nu de logische app hand matig uit. Selecteer **uitvoeren** op de werk balk van de ontwerp functie.

   Op basis van de e-mail connector die u hebt gebruikt, zijn dit de resultaten die u krijgt:

   ![E-mail bericht met actie resultaten van filter matrix](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Actie samen voegen

Als u een teken reeks wilt maken met alle items uit een matrix en deze items wilt scheiden met een specifiek scheidings teken, gebruikt u de actie **toevoegen** . U kunt vervolgens de teken reeks gebruiken in acties die volgen na de actie **toevoegen** .

Als u liever aan de slag gaat met de code weergave-editor, kunt u het voor beeld **koppelen** en variabele actie definities **initialiseren** vanuit dit artikel in de onderliggende werk stroom definitie van uw eigen logische app: [voor beelden van gegevens bewerkings code-koppelen](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. Open in de [Azure Portal](https://portal.azure.com) of Visual Studio uw logische app in de ontwerp functie voor logische apps.

   In dit voor beeld wordt gebruikgemaakt van de Azure Portal en een logische app met een **terugkeer patroon** trigger en een actie voor **het initialiseren van een variabele** . Deze actie is ingesteld voor het maken van een variabele waarvan de begin waarde een matrix is met een aantal voor beelden van gehele getallen. Wanneer u uw logische app op een later tijdstip test, kunt u uw app hand matig uitvoeren zonder te wachten tot de trigger wordt geactiveerd.

   ![Voor beeld van een logische app voor de actie lid worden gestart](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. Voer een van de volgende stappen uit in uw logische app waar u de teken reeks uit een matrix wilt maken:

   * Selecteer **nieuwe stap** om een actie toe te voegen onder de laatste stap.

     ![SSelect ' nieuwe stap ' voor de actie ' samen voegen '](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de verbindings pijl zodat het plus teken ( **+** ) wordt weer gegeven. Selecteer het plus teken en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak `join` als uw filter in. Selecteer in de lijst acties deze actie: **koppelen**

   ![Selecteer actie samen voegen](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. Geef in het vak **van** de matrix op met de items die u wilt samen voegen als een teken reeks.

   Voor dit voor beeld, wanneer u in het vak **van** klikt, wordt de lijst met dynamische inhoud weer gegeven, zodat u de eerder gemaakte variabele kunt selecteren:  

   ![Matrix uitvoer selecteren voor het maken van de teken reeks](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. Voer in het vak **samen voegen met** het teken in dat u wilt voor het scheiden van elk matrix item. 

   In dit voor beeld wordt een dubbele punt (:) Als het scheidings teken.

   ![Het scheidings teken opgeven](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

Zie voor meer informatie over deze actie in uw onderliggende werk stroom definitie de [actie toevoegen](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Uw logische app testen

Als u wilt controleren of de actie **samen voegen** de verwachte resultaten heeft, stuurt u een melding met uitvoer van de actie **toevoegen** .

1. Voeg in uw logische app een actie toe die u de resultaten van de **deelname** actie kan sturen.

1. Klik in die actie op een wille keurige plaats waar u de resultaten wilt weer geven. Wanneer de lijst met dynamische inhoud wordt geopend, selecteert u onder de actie **toevoegen** de optie **uitvoer**. 

   In dit voor beeld wordt de actie Office 365 Outlook **een E-mail verzenden** gebruikt en wordt het veld **uitvoer** in de hoofd tekst van de e-mail opgenomen:

   ![Uitvoer velden voor de actie toevoegen](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. Voer nu de logische app hand matig uit. Selecteer **uitvoeren** op de werk balk van de ontwerp functie.

   Op basis van de e-mail connector die u hebt gebruikt, zijn dit de resultaten die u krijgt:

   ![E-mail bericht met een koppeling voor de actie resultaten](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>JSON-actie parseren

Als u wilt verwijzen naar of toegang wilt krijgen tot eigenschappen in JavaScript Object Notation (JSON)-inhoud, kunt u gebruikers vriendelijke velden of tokens voor die eigenschappen maken met behulp van de actie **JSON parseren** . Op die manier kunt u die eigenschappen selecteren in de lijst met dynamische inhoud wanneer u invoer opgeeft voor uw logische app. Voor deze actie kunt u een JSON-schema opgeven of een JSON-schema genereren op basis van uw voor beeld-JSON-inhoud of-nettolading.

Als u liever aan de slag gaat met de code weergave-editor, kunt u het voor beeld- **JSON parseren** en variabele actie definities **initialiseren** vanuit dit artikel in de onderliggende werk stroom definitie van uw eigen logische app: [voor beelden van bewerkings code voor gegevens-JSON parseren](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. Open in de [Azure Portal](https://portal.azure.com) of Visual Studio uw logische app in de ontwerp functie voor logische apps.

   In dit voor beeld wordt gebruikgemaakt van de Azure Portal en een logische app met een **terugkeer patroon** trigger en een actie voor **het initialiseren van een variabele** . De actie is ingesteld voor het maken van een variabele waarvan de begin waarde een JSON-object is met eigenschappen en waarden. Wanneer u de logische app later test, kunt u uw app hand matig uitvoeren zonder te wachten tot de trigger wordt geactiveerd.

   ![Voor beeld van een logische app voor het parseren van JSON actie starten](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. Voer een van de volgende stappen uit in uw logische app waar u de JSON-inhoud wilt parseren:

   * Selecteer **nieuwe stap** om een actie toe te voegen onder de laatste stap.

     ![Selecteer nieuwe stap voor de actie JSON parseren](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de verbindings pijl zodat het plus teken ( **+** ) wordt weer gegeven. Selecteer het plus teken en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak `parse json` als uw filter in. Selecteer in de lijst acties de actie **JSON parseren** .

   ![Selecteer de actie JSON parseren](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. Geef in het vak **inhoud** de JSON-inhoud op die u wilt parseren.

   Voor dit voor beeld wordt de lijst met dynamische inhoud weer gegeven wanneer u in het vak **inhoud** klikt, zodat u de eerder gemaakte variabele kunt selecteren:

   ![JSON-object selecteren voor het parseren van JSON-actie](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. Voer het JSON-schema in dat de JSON-inhoud beschrijft die u wilt parseren.

   In dit voor beeld is dit het JSON-schema:

   ![Geef het JSON-schema op voor het JSON-object dat u wilt parseren](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Als u het schema niet hebt, kunt u dat schema genereren op basis van de JSON-inhoud, of *Payload*, die u wilt parseren. 
   
   1. Selecteer in de actie **JSON parseren** **voorbeeld lading gebruiken om schema te genereren**.

   1. Geef onder **een voor beeld van een JSON-nettolading op of plak hier** de JSON-inhoud op en selecteer vervolgens **gereed**.

      ![De JSON-inhoud invoeren voor het genereren van het schema](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

Zie [JSON-actie parseren](../logic-apps/logic-apps-workflow-actions-triggers.md)voor meer informatie over deze actie in uw onderliggende werk stroom definitie.

### <a name="test-your-logic-app"></a>Uw logische app testen

Als u wilt controleren of met de actie **JSON parseren** de verwachte resultaten worden gemaakt, stuurt u een melding die uitvoer van de JSON-actie **parseren** bevat.

1. Voeg in uw logische app een actie toe die u de resultaten van de JSON-actie voor **parseren** kan sturen.

1. Klik in die actie op een wille keurige plaats waar u de resultaten wilt weer geven. Wanneer de lijst met dynamische inhoud wordt geopend, kunt u onder de actie **JSON parseren** nu de eigenschappen van de geparseerde JSON-inhoud selecteren.

   In dit voor beeld wordt de actie Office 365 Outlook **een E-mail verzenden** gebruikt en zijn de velden **FirstName**, **LastName** en **email** opgenomen in de hoofd tekst van het e-mail bericht:

   ![JSON-eigenschappen in de actie ' een e-mail verzenden '](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Dit is de voltooide e-mail actie:

   ![Voltooid voor beeld voor e-mail actie](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. Voer nu de logische app hand matig uit. Selecteer **uitvoeren** op de werk balk van de ontwerp functie. 

   Op basis van de e-mail connector die u hebt gebruikt, zijn dit de resultaten die u krijgt:

   ![E-mail met actie resultaten voor het parseren van JSON](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Actie selecteren

Gebruik de actie **selecteren** om een matrix te maken met JSON-objecten die zijn gebouwd op basis van waarden in een bestaande matrix. U kunt bijvoorbeeld een JSON-object maken voor elke waarde in een matrix met gehele getallen door de eigenschappen op te geven die elk JSON-object moeten hebben en hoe de waarden in de bron matrix moeten worden toegewezen aan die eigenschappen. En hoewel u de onderdelen in die JSON-objecten kunt wijzigen, heeft de uitvoer matrix altijd hetzelfde aantal items als de bron matrix.

> [!NOTE]
> Voor acties om de matrix uitvoer van de **Select** -actie te gebruiken, moeten die acties matrices accepteren als invoer, of moet u de uitvoer matrix omzetten in een andere compatibele indeling. 

Als u liever aan de slag gaat met de code weergave-editor, kunt u het voor beeld kopiëren en **variabelen** actie definities van dit artikel **selecteren** in de onderliggende werk stroom definitie van uw eigen logische app: [voor beelden van code voor gegevens bewerking-selecteren](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. Open in de [Azure Portal](https://portal.azure.com) of Visual Studio uw logische app in de ontwerp functie voor logische apps.

   In dit voor beeld wordt gebruikgemaakt van de Azure Portal en een logische app met een **terugkeer patroon** trigger en een actie voor **het initialiseren van een variabele** . De actie is ingesteld voor het maken van een variabele waarvan de begin waarde een matrix is met een aantal voor beelden van gehele getallen. Wanneer u de logische app later test, kunt u uw app hand matig uitvoeren zonder te wachten tot de trigger wordt geactiveerd.

   ![De voor beeld-logische app wordt gestart voor de actie ' selecteren '](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. Voer een van de volgende stappen uit in uw logische app waar u de matrix wilt maken: 

   * Selecteer **nieuwe stap** om een actie toe te voegen onder de laatste stap.

     ![Selecteer ' nieuwe stap ' voor de actie ' selecteren '](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de verbindings pijl zodat het plus teken ( **+** ) wordt weer gegeven. Selecteer het plus teken en selecteer vervolgens **een actie toevoegen**.

1. Selecteer onder **Kies een actie** de optie **Ingebouwd**. Voer in het zoekvak `select` als uw filter in. Selecteer in de lijst acties de actie **selecteren** .

   ![Selecteer de actie ' selecteren '](./media/logic-apps-perform-data-operations/select-select-action.png)

1. Geef in het vak **van** de gewenste bron matrix op.

   Voor dit voor beeld wordt de lijst met dynamische inhoud weer gegeven wanneer u in het vak **van** klikt, zodat u de eerder gemaakte variabele kunt selecteren:

   ![Selecteer een bron matrix voor de actie selecteren](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. Geef in de linkerkolom van de **kaart** de naam op van de eigenschap die u wilt toewijzen aan elke waarde in de bron matrix. Geef in de rechter kolom een expressie op die de waarde vertegenwoordigt die u wilt toewijzen aan de eigenschap.

   In dit voor beeld wordt ' Product_ID ' opgegeven als eigenschaps naam om elke waarde in de gehele matrix toe te wijzen met behulp van de `item()` functie in een expressie waarmee elk matrix item wordt geopend. 

   ![JSON-object eigenschap en-waarden opgeven om matrix te maken](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Dit is de voltooide actie:

   ![Voltooid voor beeld voor de actie ' selecteren '](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

Zie voor meer informatie over deze actie in uw onderliggende werk stroom definitie [actie selecteren](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Uw logische app testen

Als u wilt controleren of met de actie **Select** de verwachte resultaten worden gemaakt, stuurt u een melding met de uitvoer van de actie **selecteren** .

1. Voeg in uw logische app een actie toe die u de resultaten van de **Select** -actie kan sturen.

1. Klik in die actie op een wille keurige plaats waar u de resultaten wilt weer geven. Wanneer de lijst met dynamische inhoud wordt geopend, selecteert u **expressie**. Als u de matrix uitvoer wilt ophalen van de actie **selecteren** , voert u deze expressie in met de naam van de actie **selecteren** :

   `@actionBody('Select')`

   In dit voor beeld wordt de actie Office 365 Outlook **een E-mail verzenden** gebruikt en worden de uitvoer van de `@actionBody('Select')` expressie in de hoofd tekst van het e-mail bericht opgenomen:

   ![Actie uitvoer van de actie ' selecteren '](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. Voer nu de logische app hand matig uit. Selecteer **uitvoeren** op de werk balk van de ontwerp functie.

   Op basis van de e-mail connector die u hebt gebruikt, zijn dit de resultaten die u krijgt:

   ![E-mail bericht met de actie resultaten selecteren](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Logic apps connectors](../connectors/apis-list.md)
