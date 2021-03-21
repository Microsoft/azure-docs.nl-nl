---
title: JSON-werk stroom definities voor logische apps maken, bewerken of uitbreiden
description: De JSON-werk stroom definities van uw logische app schrijven, bewerken en uitbreiden in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 9163071237041d7c8510a644c573e3763434bb0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96014394"
---
# <a name="create-edit-or-extend-json-for-logic-app-workflow-definitions-in-azure-logic-apps"></a>JSON maken, bewerken of uitbreiden voor werkstroomdefinities voor logische apps in Azure Logic Apps

Wanneer u oplossingen voor bedrijfs integratie met geautomatiseerde werk stromen in [Azure Logic apps](../logic-apps/logic-apps-overview.md)maakt, gebruiken de onderliggende definities van logische apps eenvoudige en declaratieve JavaScript object NOTATION (JSON) samen met het schema voor de [werk stroom definitie taal (WDL)](../logic-apps/logic-apps-workflow-definition-language.md) voor de beschrijving en validatie. Met deze indelingen kunnen definities van logische apps eenvoudiger worden gelezen en begrepen zonder dat u iets hoeft te weten over code.
Als u het maken en implementeren van Logic Apps wilt automatiseren, kunt u logische app-definities als [Azure-resources](../azure-resource-manager/management/overview.md) in [Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md)toevoegen.
Als u logische Apps wilt maken, beheren en implementeren, kunt u vervolgens [Azure PowerShell](/powershell/module/az.logicapp), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)of de [Azure Logic apps rest-api's](/rest/api/logic/)gebruiken.

Als u de definities van logische apps in JSON wilt gebruiken, opent u de code weergave-editor bij het werken in de Azure Portal of in Visual Studio of kopieert u de definitie naar een gewenste editor.
Als u geen ervaring hebt met Logic apps, lees dan [hoe u uw eerste logische app maakt](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Sommige Azure Logic Apps mogelijkheden, zoals het definiëren van para meters en meerdere triggers in definities van logische apps, zijn alleen beschikbaar in JSON, niet op de Logic Apps Designer.
> Voor deze taken moet u dus in de code weergave of een andere editor werken.

## <a name="edit-json---azure-portal"></a>JSON-Azure Portal bewerken

1. Meld u aan bij <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

2. Kies in het menu links **alle services**.
Zoek in het zoekvak ' Logic apps ' en selecteer vervolgens de logische app in de resultaten.

3. Selecteer in het menu van de logische app onder **ontwikkelingsprogram ma's** de optie **Logic app-code weergave**.

   De code weergave-editor wordt geopend en toont de definitie van de logische app in JSON-indeling.

## <a name="edit-json---visual-studio"></a>JSON bewerken-Visual Studio

Voordat u kunt werken met de definitie van de logische app in Visual Studio, moet u ervoor zorgen dat u [de vereiste hulpprogram ma's hebt geïnstalleerd](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites).
Als u een logische app met Visual Studio wilt maken, raadpleegt u [Quick Start: taken en processen automatiseren met Azure Logic apps-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

In Visual Studio kunt u logische apps openen die zijn gemaakt en geïmplementeerd, rechtstreeks vanuit de Azure Portal of als Azure Resource Manager projecten van Visual Studio.

1. Open de Visual Studio-oplossing of het project van de [Azure-resource groep](../azure-resource-manager/management/overview.md) die uw logische app bevat.

2. Zoek en open de definitie van uw logische app, die standaard wordt weer gegeven in een [Resource Manager-sjabloon](../azure-resource-manager/templates/overview.md), met de naam **LogicApp.jsop**.
U kunt deze sjabloon gebruiken en aanpassen voor implementatie naar andere omgevingen.

3. Open het snelmenu voor de definitie en sjabloon van uw logische app.
Selecteer **Openen met Logic App-ontwerpfunctie**.

   ![Logische app openen in een Visual Studio-oplossing](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > Als u deze opdracht niet hebt in Visual Studio 2019, controleert u of u de nieuwste updates voor Visual Studio hebt.

4. Kies aan de onderkant van de ontwerp functie **code weergave**.

   De code weergave-editor wordt geopend en toont de definitie van de logische app in JSON-indeling.

5. Als u wilt terugkeren naar de ontwerp weergave, kiest u onder aan de code weergave-editor **ontwerp**.

## <a name="parameters"></a>Parameters

De levens cyclus van de implementatie heeft doorgaans verschillende omgevingen voor ontwikkeling, testen, faseren en productie. Wanneer u waarden hebt die u opnieuw wilt gebruiken in uw logische app zonder hardcoding of die variëren afhankelijk van uw implementatie behoeften, kunt u een [Azure Resource Manager sjabloon](../azure-resource-manager/management/overview.md) maken voor uw werk stroom definitie, zodat u ook de implementatie van logische apps kunt automatiseren.

Volg deze algemene stappen voor het *para meters* of definiëren en gebruiken van para meters voor deze waarden. U kunt vervolgens de waarden opgeven in een afzonderlijk parameter bestand dat deze waarden doorgeeft aan uw sjabloon. Op die manier kunt u deze waarden gemakkelijker wijzigen zonder dat u uw logische app hoeft bij te werken en opnieuw te implementeren. Zie [overzicht: de implementatie voor Logic apps automatiseren met Azure Resource Manager sjablonen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)voor meer informatie.

1. Definieer in uw sjabloon sjabloon parameters en para meters voor de werk stroom definitie voor het accepteren van de waarden die moeten worden gebruikt in respectievelijk de implementatie en runtime.

   Sjabloon parameters worden gedefinieerd in de sectie para meters die zich buiten uw werk stroom definitie bevinden, terwijl werk stroom definitie parameters worden gedefinieerd in een para meters-sectie in de definitie van uw werk stroom.

1. Vervang de hardcoded-waarden door expressies die verwijzen naar deze para meters. Sjabloon expressies gebruiken syntaxis die verschilt van definitie-expressies van werk stromen.

   Vermijd het bemoeilijken van uw code door geen sjabloon expressies te gebruiken, die tijdens de implementatie worden geëvalueerd, in definitie-expressies van werk stromen, die tijdens runtime worden geëvalueerd. Gebruik alleen sjabloon expressies buiten uw werk stroom definitie. Gebruik alleen werk stroom definitie-expressies in uw werk stroom definitie.

   Wanneer u de waarden voor de para meters voor de werk stroom definitie opgeeft, kunt u verwijzen naar sjabloon parameters met behulp van de para meters sectie buiten uw werk stroom definitie, maar nog steeds binnen de resource definitie voor uw logische app. Op die manier kunt u parameter waarden van een sjabloon door geven aan de para meters van de werk stroom definitie.

1. Sla de waarden voor uw para meters op in een afzonderlijk [parameter bestand](../azure-resource-manager/templates/parameter-files.md) en voeg dat bestand toe aan uw implementatie.

## <a name="process-strings-with-functions"></a>Teken reeksen verwerken met functies

Logic Apps heeft verschillende functies voor het werken met teken reeksen.
Stel bijvoorbeeld dat u een bedrijfs naam wilt door geven van een order naar een ander systeem.
U bent echter niet zeker van de juiste verwerking voor teken codering.
U kunt base64-code ring uitvoeren op deze teken reeks, maar om te voor komen dat er in de URL Escapes worden gebruikt, is het mogelijk om in plaats daarvan meerdere tekens te vervangen. Daarnaast hebt u alleen een subtekenreeks voor de bedrijfs naam nodig, omdat de eerste vijf tekens niet worden gebruikt.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

In deze stappen wordt beschreven hoe dit voor beeld deze teken reeks verwerkt, vanaf de binnen naar buiten:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Haal de [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) naam van het bedrijf op, zodat u het totale aantal tekens krijgt.

2. Voor een kortere teken reeks kunt u aftrekken `5` .

3. Krijg nu een [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md) .
Begin bij index `5` en ga naar de rest van de teken reeks.

4. Deze subtekenreeks naar een [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md) teken reeks converteren.

5. [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md)Alle tekens zijn nu `+` met `-` tekens.

6. Ten slotte, [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) alle `/` tekens met `_` tekens.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Wijs lijst items toe aan eigenschaps waarden en gebruik vervolgens Maps als para meters

Als u verschillende resultaten wilt weer geven op basis van de waarde van een eigenschap, kunt u een kaart maken die overeenkomt met elke eigenschaps waarde van een resultaat en deze vervolgens als para meter gebruiken.

Met deze werk stroom wordt bijvoorbeeld een aantal categorieën gedefinieerd als para meters en een kaart die overeenkomt met die categorieën met een specifieke URL.
Eerst haalt de werk stroom een lijst met artikelen op. Vervolgens gebruikt de werk stroom de kaart om de URL te vinden die overeenkomt met de categorie voor elk artikel.

*   De [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) functie controleert of de categorie overeenkomt met een bekende gedefinieerde categorie.

*   Nadat u een overeenkomende categorie hebt opgehaald, haalt het voor beeld het item uit de kaart op met behulp van vier Kante haken: `parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "https://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=https://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Gegevens ophalen met datum functies

Als u gegevens wilt ophalen uit een gegevens bron die geen systeem eigen ondersteuning biedt voor *Triggers*, kunt u datum functies gebruiken voor het werken met tijden en datums.
Deze expressie detecteert bijvoorbeeld hoe lang de stappen van deze werk stroom worden uitgevoerd, van de binnen naar buiten:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Pak de uit met de `order` actie `startTime` .
2. De huidige tijd ophalen met `utcNow()` .
3. Eén seconde aftrekken:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md)

   U kunt andere tijds eenheden gebruiken, zoals `minutes` of `hours` .

3. Nu kunt u deze twee waarden vergelijken.

   Als de eerste waarde lager is dan de tweede waarde, dan is er meer dan één seconde verstreken sinds de eerste keer dat de order is geplaatst.

Als u datums wilt opmaken, kunt u teken reeks formatters gebruiken. Gebruik bijvoorbeeld om de RFC1123 op te halen [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md) .
Meer informatie over [datum notaties](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "https://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="next-steps"></a>Volgende stappen

* [Stappen uitvoeren op basis van een voor waarde (voorwaardelijke instructies)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Stappen uitvoeren op basis van verschillende waarden (switch-instructies)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Stappen uitvoeren en herhalen (lussen)](../logic-apps/logic-apps-control-flow-loops.md)
* [Parallelle stappen uitvoeren of samen voegen (vertakkingen)](../logic-apps/logic-apps-control-flow-branches.md)
* [Stappen uitvoeren op basis van de gegroepeerde actie status (bereiken)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Meer informatie over het [schema voor de werk stroom definitie taal voor Azure Logic apps](../logic-apps/logic-apps-workflow-definition-language.md)
* Meer informatie over [werk stroom acties en triggers voor Azure Logic apps](../logic-apps/logic-apps-workflow-actions-triggers.md)
