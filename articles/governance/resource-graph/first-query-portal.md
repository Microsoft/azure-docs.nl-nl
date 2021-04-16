---
title: 'Quickstart: Uw eerste portalquery'
description: In deze quickstart volgt u de stappen om uw eerste query vanuit de Azure-portal uit te voeren met behulp van Azure Resource Graph Explorer.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 07e336121c6338b27c018acb5b2332653663e1b4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533081"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Quickstart: Uw eerste Resource Graph-query uitvoeren met Azure Resource Graph Explorer

De kracht van Azure resource Graph is rechtstreeks beschikbaar in de Azure-portal via Azure Resource Graph Explorer. Resource Graph Explorer biedt doorzoekbare informatie over de typen en eigenschappen van Azure Resource Manager-resources die u kunt opvragen. Resource Graph Explorer biedt ook een duidelijke interface voor het werken met meerdere query's, het evalueren van de resultaten en het converteren van de resultaten van sommige query's naar een grafiek die kan worden vastgemaakt aan een Azure-dashboard.

Aan het einde van deze quickstart hebt u uw eerste Resource Graph-query uitgevoerd en de resultaten vastgemaakt aan een dashboard met behulp van de Azure-portal en Resource Graph Explorer.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="run-your-first-resource-graph-query"></a>Uw eerste Resource Graph-query uitvoeren

Open de [Azure-portal](https://portal.azure.com), zoek Resource Graph Explorer en gebruik deze om uw eerste Resource Graph-query uit te voeren met de volgende stappen:

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek naar en selecteer **Resource Graph Explorer**.

1. Voer in het gedeelte **Query 1** van het venster de query `Resources | project name, type | limit 5` in en selecteer **Query uitvoeren**.

   > [!NOTE]
   > Omdat deze voorbeeldquery geen sorteermodificator geeft, bijvoorbeeld `order by`, zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd.

1. Bekijk de uitvoer van de query op het tabblad **Resultaten**. Selecteer het tabblad **Berichten** om de details van de query te bekijken, inclusief het aantal resultaten en de duur van de query. Eventuele fouten worden weergegeven op dit tabblad.

1. Werk de query als volgt bij om de eigenschap **Naam** te `order by`: `Resources | project name, type | limit 5 | order by name asc`. Selecteer vervolgens **Query uitvoeren**.

   > [!NOTE]
   > Net als bij de eerste query zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd. De volgorde van de queryopdrachten is belangrijk. In dit voorbeeld komt `order by` na `limit`. Met deze opdracht worden de queryresultaten eerst beperkt en vervolgens gerangschikt.

1. Werk de query als volgt bij om eerst te `order by` op de eigenschap **Naam** en daarna de resultaten van de top vijf te `limit`: `Resources | project name, type | order by name asc | limit 5`. Selecteer vervolgens **Query uitvoeren**.

Wanneer de laatste query meerdere keren wordt uitgevoerd, ervan uitgaande dat niets in uw omgeving verandert, zijn de geretourneerde resultaten consistent en gesorteerd op de eigenschap **Naam**, maar nog steeds beperkt tot de top vijf.

### <a name="schema-browser"></a>Schemabrowser

De schemabrowser bevindt zich in het linkerdeelvenster van Resource Graph Explorer. Deze lijst met resources bevat alle _resourcetypen_ van Azure-resources die worden ondersteund door Azure Resource Graph en die zich bevinden in een tenant waartoe u toegang hebt. Als u een resourcetype of subeigenschappen uitvouwt, worden onderliggende eigenschappen weergegeven waarmee u een Resource Graph-query kunt maken.

Als u het resourcetype selecteert, wordt `where type =="<resource type>"` in het queryvak geplaatst. Als u een onderliggende eigenschap selecteert, wordt `where <propertyName> == "INSERT_VALUE_HERE"` toegevoegd aan het queryvak.
De schemabrowser is een uitstekende manier om te verkennen welke eigenschappen u kunt gebruiken in query's. Vervang _INSERT\_VALUE\_HERE_ door uw eigen waarde en pas de query aan met voorwaarden, operators en functies om de beoogde resultaten te verkrijgen.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Een grafiek maken op basis van de Resource Graph-query

Als u na het uitvoeren van de vorige query het tabblad **Grafieken** selecteert, wordt er een bericht weergegeven dat de resultatenset niet compatibel is met een cirkeldiagramvisualisatie. Er kan geen grafiek worden gemaakt van query's die een lijst met resultaten weergeven, maar wel van query's die aantallen resources weergeven. Laten we een visualisatie van de Resource Graph-query maken met behulp van de [voorbeeldquery voor telling van het aantal virtuele machines op type besturingssysteem](./samples/starter.md#count-os).

1. Voer in het gedeelte **Query 1** van het venster de volgende query in en selecteer **Query uitvoeren**.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Selecteer het tabblad **Resultaten** en merk op dat met deze query aantallen als resultaat worden weergegeven.

1. Selecteer het tabblad **Grafieken**. Nu worden er visualisaties als resultaat weergegeven. Wijzig het type van _Grafiektype selecteren..._ in _Staafdiagram_ of _Ringdiagram_ om te experimenteren met de beschikbare visualisatieopties.

## <a name="pin-the-query-visualization-to-a-dashboard"></a>De visualisatie van de query vastmaken aan een dashboard

Wanneer uw resultaten van een query kunnen worden gevisualiseerd, kan deze gegevensvisualisatie worden vastgemaakt aan een van uw dashboards. Voer de volgende stappen uit nadat u de vorige query hebt uitgevoerd:

1. Selecteer **Opslaan** en geef de naam 'VM's per type besturingssysteem' op. Selecteer vervolgens **Opslaan** onder in het rechterdeelvenster.

1. Selecteer **Query uitvoeren** om de opgeslagen query opnieuw uit te voeren.

1. Selecteer een gegevensvisualisatie op het tabblad **Grafieken**. Selecteer vervolgens **Vastmaken aan dashboard**.

1. Selecteer de portalmelding die wordt weergegeven of selecteer **Dashboard** in het linkerdeelvenster.

De query is nu beschikbaar op uw dashboard, waarbij de titel van de tegel overeenkomt met de naam van de query. Als de query niet is opgeslagen bij het vastmaken, heeft deze de naam 'Query 1'.

De query en de resulterende gegevensvisualisatie worden telkens uitgevoerd en bijgewerkt wanneer het dashboard wordt geladen, waardoor rechtstreeks in uw werkstroom realtime en dynamische inzichten in uw Azure-omgeving worden geboden.

> [!NOTE]
> Ook query's waarmee een lijst als resultaat wordt weergegeven, kunnen worden vastgemaakt aan het dashboard. De functie is niet beperkt tot gegevensvisualisaties van query's.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Voorbeelden van Resource Graph Explorer-dashboards importeren

Als u voorbeelden van Resource Graph-query's wilt bekijken of als u wilt weten hoe Resource Graph Explorer kan worden gebruikt om uw werkstroom in de Azure-portal te verbeteren, kunt u deze voorbeelddashboards uitproberen.

- [Resource Graph Explorer-voorbeelddashboard 1](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  :::image type="content" source="./media/arge-sample1-small.png" alt-text="Voorbeeldafbeelding van voorbeelddashboard 1" lightbox="./media/arge-sample1-large.png":::

- [Resource Graph Explorer-voorbeelddashboard 2](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  :::image type="content" source="./media/arge-sample2-small.png" alt-text="Voorbeeldafbeelding van voorbeelddashboard 2" lightbox="./media/arge-sample2-large.png":::

> [!NOTE]
> De aantallen en grafieken in de bovenstaande schermafbeeldingen van de voorbeelddashboards kunnen per Azure-omgeving verschillen.

1. Selecteer en download het voorbeelddashboard dat u wilt evalueren.

1. Selecteer **Dashboard** in het linkerdeelvenster van de Azure-portal.

1. Selecteer **Uploaden** en zoek en selecteer het gedownloade voorbeelddashboardbestand. Selecteer vervolgens **Openen**.

Het geïmporteerde dashboard wordt automatisch weergegeven. Omdat het dashboard nu bestaat in uw Azure-portal, kunt u het naar behoefte verkennen en wijzigen of nieuwe dashboards maken op basis van het voorbeeld en deze met uw teams delen. Zie [Dashboards maken en delen in de Azure-portal](../../azure-portal/azure-portal-dashboards.md) voor meer informatie over het werken met dashboards.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de Resource Graph-voorbeelddashboards uit uw Azure-portalomgeving wilt verwijderen, kunt u dit doen met de volgende stappen:

1. Selecteer **Dashboard** in het linkerdeelvenster.

1. Selecteer in de vervolgkeuzelijst Dashboard het Resource Graph-voorbeelddashboard dat u wilt verwijderen.

1. Selecteer **Verwijderen** in het menu boven aan het dashboard en selecteer **OK** om de verwijdering te bevestigen.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u Azure Resource Graph Explorer gebruikt om uw eerste query uit te voeren en de Resource Graph-dashboardvoorbeelden te bekijken. Ga door naar de pagina met details van de querytaal voor meer informatie over de taal van Resource Graph.

> [!div class="nextstepaction"]
> [Meer informatie over de querytaal](./concepts/query-language.md)
