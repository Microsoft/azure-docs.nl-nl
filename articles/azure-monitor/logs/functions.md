---
title: Functies in Azure Monitor logboekquery's
description: In dit artikel wordt beschreven hoe u functies gebruikt om een query aan te roepen vanuit een andere logboekquery in Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/19/2021
ms.openlocfilehash: fecede1d582232ebc75878a687a24818031f0d80
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752849"
---
# <a name="functions-in-azure-monitor-log-queries"></a>Functies in Azure Monitor logboekquery's
Een functie is een logboekquery in Azure Monitor die kan worden gebruikt in andere logboekquery's alsof het een opdracht is. Met functies kunnen ontwikkelaars oplossingen bieden aan verschillende klanten en kunt u querylogica opnieuw gebruiken in uw eigen omgeving. In dit artikel vindt u meer informatie over het gebruik van functies en het maken van uw eigen functies.

## <a name="types-of-functions"></a>Typen functies
Er zijn twee soorten functies in Azure Monitor:

- **Oplossingsfunctie:** Vooraf gebouwde functies die zijn opgenomen in Azure Monitor. Deze zijn beschikbaar in alle Log Analytics-werkruimten en kunnen niet worden gewijzigd.
- **Werkruimtefuncties:** Functies die zijn geïnstalleerd in een bepaalde Log Analytics-werkruimte en kunnen worden gewijzigd en beheerd door de gebruiker.

## <a name="viewing-functions"></a>Functies weergeven
U kunt oplossingsfuncties en werkruimtefuncties weergeven in de huidige werkruimte via het **tabblad Functies** in het linkerdeelvenster van een Log Analytics-werkruimte. Gebruik de **knop Filteren** om de functies in de lijst te filteren en **groeperen op om** de groepering te wijzigen. Typ een tekenreeks in het **zoekvak** om een bepaalde functie te zoeken. Beweeg de muisaanwijzer over een functie om details ervan weer te geven, inclusief een beschrijving en parameters.

:::image type="content" source="media/functions/view-functions.png" alt-text="Functie Weergeven" lightbox="media/functions/view-functions.png":::

## <a name="use-a-function"></a>Een functie gebruiken
Gebruik een functie in een query door de naam te typen met waarden voor parameters, net zoals u een opdracht zou typen. De uitvoer van de functie kan worden geretourneerd als resultaten of worden doorspijpt naar een andere opdracht.

Voeg een functie toe aan de huidige query door te dubbelklikken op de naam of door er de muisaanwijzer over te bewegen en **In editor gebruiken te selecteren.** Functies in de werkruimte worden tijdens het typen van een query ook opgenomen in IntelliSense. 

Als voor een query parameters zijn vereist, geeft u deze op met behulp van de syntaxis: `function_name(param1,param2,...)` .

:::image type="content" source="media/functions/function-use.png" alt-text="Een functie gebruiken" lightbox="media/functions/function-use.png":::

## <a name="create-a-function"></a>Een functie maken
Als u een functie wilt maken van de huidige query in de editor, selecteert u **Opslaan** en **vervolgens Opslaan als functie.** 

:::image type="content" source="media/functions/function-save.png" alt-text="Een functie maken" lightbox="media/functions/function-save.png":::

Maak een functie met Log Analytics in de Azure Portal door op **Opslaan** te klikken en vervolgens de informatie in de volgende tabel op te geven.

| Instelling | Beschrijving |
|:---|:---|
| Functienaam  | Naam voor de functie. Dit mag geen spatie of speciale tekens bevatten. Het kan ook niet beginnen met een onderstrepingsteken (_), omdat dit teken is gereserveerd voor oplossingsfuncties. |
| Verouderde categorie | Door de gebruiker gedefinieerde categorie om functies te filteren en te groeperen.   |
| Opslaan als computergroep | Sla de query op als [een computergroep.](computer-groups.md)  |
| Parameters | Voeg een parameter toe voor elke variabele in de functie die een waarde vereist wanneer deze wordt gebruikt. Zie [Functieparameters](#function-parameters) voor meer informatie. |

:::image type="content" source="media/functions/function-details.png" alt-text="Functiedetails" lightbox="media/functions/function-details.png":::

## <a name="function-parameters"></a>Functieparameters 
U kunt parameters toevoegen aan een functie, zodat u waarden voor bepaalde variabelen kunt opgeven bij het aanroepen ervan. Hierdoor kan dezelfde functie worden gebruikt in verschillende query's, elk met verschillende waarden voor de parameters. Parameters worden gedefinieerd door de volgende eigenschappen.

| Instelling | Beschrijving |
|:---|:---|
| Type  | Gegevenstype voor de waarde. |
| Name  | Naam voor de parameter . Dit is de naam die moet worden gebruikt in de query om te vervangen door de parameterwaarde.  |
| Standaardwaarde | De waarde die moet worden gebruikt voor de parameter als er geen waarde is opgegeven. |

Parameters worden geordend wanneer ze worden gemaakt met parameters die geen standaardwaarde hebben die wordt geplaatst voor parameters die een standaardwaarde hebben.

## <a name="working-with-function-code"></a>Werken met functiecode
U kunt de code van een functie bekijken om inzicht te krijgen in de werking ervan of om de code voor een werkruimtefunctie te wijzigen. Selecteer **De functiecode laden om** de functiecode toe te voegen aan de huidige query in de editor. Als u deze toevoegt aan een lege query of de eerste regel van een bestaande query, wordt de functienaam aan het tabblad toevoegen. Als het een werkruimtefunctie is, kunt u hiermee de functiedetails bewerken.

:::image type="content" source="media/functions/function-code.png" alt-text="Functiecode laden" lightbox="media/functions/function-code.png":::

## <a name="edit-a-function"></a>Een functie bewerken
Bewerk de eigenschappen of de code van een functie door een nieuwe query te maken, beweeg de muisaanwijzer over de naam van de functie en selecteer **Functiecode laden.** Maak eventuele wijzigingen in de code en selecteer **Opslaan** en vervolgens **Functiedetails bewerken.** Maak eventuele wijzigingen in de eigenschappen en parameters van de functie voordat u op **Opslaan klikt.**

:::image type="content" source="media/functions/function-edit.png" alt-text="Functie bewerken" lightbox="media/functions/function-edit.png":::
## <a name="example"></a>Voorbeeld
De volgende voorbeeldfunctie retourneert alle gebeurtenissen in het Azure-activiteitenlogboek sinds een bepaalde datum en die overeenkomen met een bepaalde categorie. 

Begin met de volgende query met behulp van hardcoded waarden. Hiermee wordt gecontroleerd of de query werkt zoals verwacht.

```Kusto
AzureActivity
| where CategoryValue == "Administrative"
| where TimeGenerated > todatetime("2021/04/05 5:40:01.032 PM")
```

:::image type="content" source="media/functions/example-query.png" alt-text="Voorbeeldfunctie : initiële query" lightbox="media/functions/example-query.png":::

Vervang vervolgens de hardcoded waarden door parameternamen en sla de functie op door **Opslaan** en vervolgens **Opslaan als functie te selecteren.**

```Kusto
AzureActivity
| where CategoryValue == CategoryParam
| where TimeGenerated > DateParam
```

:::image type="content" source="media/functions/example-save-function.png" alt-text="Voorbeeldfunctie - functie opslaan" lightbox="media/functions/example-save-function.png":::

 Geef de volgende waarden op voor de functie-eigenschappen.

| Eigenschap | Waarde |
|:---|:---|
| Functienaam | AzureActivityByCategory |
| Verouderde categorie | Demofuncties |

Definieer de volgende parameters voordat u de functie opspart.

| Type | Naam | Standaardwaarde |
|:---|:---|:---|
| tekenreeks   | CategoryParam | 'Beheer' |
| datum/tijd | DateParam     | |

:::image type="content" source="media/functions/example-function-properties.png" alt-text="Voorbeeldfunctie - functie-eigenschappen" lightbox="media/functions/example-function-properties.png":::

Maak een nieuwe query en bekijk de nieuwe functie door er de muisaanwijzer over te bewegen. Noteer de volgorde van de parameters, aangezien dit de volgorde is die moet worden opgegeven wanneer u de functie gebruikt.

:::image type="content" source="media/functions/example-view-details.png" alt-text="Voorbeeldfunctie : details weergeven" lightbox="media/functions/example-view-details.png":::

Selecteer **Gebruiken in editor om** de nieuwe functie toe te voegen aan een query en voeg vervolgens waarden toe voor de parameters. U hoeft geen waarde op te geven voor CategoryParam omdat deze een standaardwaarde heeft.

:::image type="content" source="media/functions/example-use-function.png" alt-text="Voorbeeldfunctie - functie gebruiken" lightbox="media/functions/example-use-function.png":::



## <a name="next-steps"></a>Volgende stappen
Zie andere lessen voor het schrijven van Azure Monitor logboekquery's:

- [Tekenreeksbewerkingen](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)

