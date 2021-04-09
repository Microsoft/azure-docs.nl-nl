---
title: Azure-resourcegegevens bekijken en filteren
description: Filter gegevens en gebruik verschillende weergaven om uw Azure-resources beter te begrijpen.
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: bb48d0b0a7bf6017fbf407a95c33ef17729e34e3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771631"
---
# <a name="view-and-filter-azure-resource-information"></a>Azure-resourcegegevens bekijken en filteren

Met de Azure-portal kunt u door gedetailleerde gegevens over resources in uw Azure-abonnementen bladeren. In dit artikel ziet u hoe u gegevens filtert en verschillende weergaven gebruikt om uw resources beter te begrijpen.

Het artikel richt zich op het scherm **Alle resources** dat wordt weergegeven in de volgende schermopname. Schermen voor afzonderlijke resourcetypen, zoals virtuele machines, hebben verschillende opties, zoals het starten en stoppen van een VM.

:::image type="content" source="media/manage-filter-resource-views/all-resources.png" alt-text="Weergave van alle resources in de Azure-portal":::

## <a name="filter-resources"></a>Resources filteren

Begin met het verkennen van **Alle resources** door filters te gebruiken om te focussen op een subset resources. In de volgende schermopname is gefilterd op resourcegroepen en zijn twee van de zes resourcegroepen in een abonnement geselecteerd.

:::image type="content" source="media/manage-filter-resource-views/filter-resource-group.png" alt-text="Weergave filteren op basis van resourcegroepen":::

U kunt filters combineren, inclusief filters die zijn gebaseerd op zoekopdrachten in tekst, zoals weergegeven in de volgende schermopname. In dit geval ziet u de resultaten voor resources die 'SimpleWinVM' bevatten in een van de twee reeds geselecteerde resourcegroepen.

:::image type="content" source="media/manage-filter-resource-views/filter-simplewinvm.png" alt-text="Weergave filteren op basis van tekstinvoer":::

Als u wilt wijzigen welke kolommen in een weergave worden opgenomen, selecteert u **Weergave beheren** en vervolgens **Kolommen bewerken**.

:::image type="content" source="media/manage-filter-resource-views/edit-columns.png" alt-text="Kolommen bewerken die te zien zijn in de weergave":::

## <a name="save-use-and-delete-views"></a>Weergaven opslaan, gebruiken en verwijderen

U kunt weergaven opslaan die de filters en kolommen bevatten die u hebt geselecteerd. Een weergave opslaan en gebruiken:

1. Selecteer **Weergave beheren** en **Weergave opslaan**.

1. Voer een naam in voor de weergave en selecteer **OK**. De opgeslagen weergave wordt nu weergegeven in het menu **Weergave beheren**.

    :::image type="content" source="media/manage-filter-resource-views/simple-view.png" alt-text="Opgeslagen weergave":::

1. Als u een weergave wilt gebruiken, schakelt u tussen **Standaard** en een van uw eigen weergaven om te zien hoe dit de weergegeven lijst met resources beïnvloedt.

Een weergave verwijderen:

1. Selecteer **Weergave beheren** en vervolgens **In alle weergaven bladeren**.

1. Selecteer in het deel venster **opgeslagen weer gaven** de weer gave en selecteer vervolgens het pictogram pictogram **verwijderen** ![ verwijderen weer gave ](media/manage-filter-resource-views/icon-delete.png) .

## <a name="export-information-from-a-view"></a>Informatie exporteren uit een weer gave

U kunt de resource gegevens exporteren vanuit een weer gave. Informatie exporteren in CSV-indeling:

1. Selecteer **exporteren naar CSV**.

    :::image type="content" source="media/manage-filter-resource-views/export-csv.png" alt-text="Scherm opname van exporteren naar CSV-indeling":::

1. Sla het bestand lokaal op en open het in Excel of een andere toepassing die ondersteuning biedt voor de CSV-indeling. 

Wanneer u door de portal gaat, ziet u andere gebieden waar u gegevens kunt exporteren, zoals een afzonderlijke resource groep.

## <a name="summarize-resources-with-visuals"></a>Resources samenvatten met visuals

De weergaven die we tot nu toe hebben bekeken, zijn _lijstweergaven_, maar er zijn ook _samenvattingsweergaven_ die visuals bevatten. U kunt deze weer gaven opslaan en gebruiken, net als bij lijst weergaven. Filters blijven behouden tussen de twee typen weergaven. Er zijn standaardweergaven, zoals de weergave **Locatie**, die hieronder wordt weergegeven, maar ook weergaven die relevant zijn voor specifieke services, zoals de weergave **Status** voor Azure Storage.

:::image type="content" source="media/manage-filter-resource-views/summary-map.png" alt-text="Overzicht van resources in een kaartweergave":::

Een samenvattingsweergave opslaan en gebruiken:

1. Selecteer in het weergavemenu de optie **Samenvattingsweergave**.

    :::image type="content" source="media/manage-filter-resource-views/menu-summary-view.png" alt-text="Menu Samenvattingsweergave":::

1. In de samenvattingsweergave kunt u samenvatten op basis van verschillende kenmerken, waaronder **Locatie** en **Type**. Selecteer een optie bij **Samenvatten op** en kies een geschikte visual. In de volgende schermopname ziet u het **Samenvattingstype** met een **Staafdiagram**-visual.

    :::image type="content" source="media/manage-filter-resource-views/type-summary-bar-chart.png" alt-text="Samenvattingstype met een staafdiagram":::

1. Selecteer **Weergave beheren** en vervolgens **Opslaan** om deze weergave op te slaan, zoals u hebt gedaan bij de lijstweergave.

1. Selecteer in de samenvattingsweergave onder **Samenvattingstype** een staaf in het diagram. Als u de balk selecteert, wordt er een lijst gefilterd op één type resource.

    :::image type="content" source="media/manage-filter-resource-views/all-resources-filtered-type.png" alt-text="Alle resources gefilterd op type":::

## <a name="run-queries-in-azure-resource-graph"></a>Query’s uitvoeren in Azure Resource Graph

Azure Resource Graph biedt efficiënte en goed presterende verkenning van resources, met de mogelijkheid om op schaal query’s uit te voeren in een set abonnementen. Het scherm **Alle resources** in de Azure-portal bevat een koppeling om een Resource Graph-query te openen die als bereik de huidige gefilterde weergave heeft.

Een Resource Graph-query uitvoeren:

1. Select **Query openen**.

    :::image type="content" source="media/manage-filter-resource-views/open-query.png" alt-text="Azure Resource Graph-query openen":::

1. Selecteer in **Azure Resource Graph Explorer** de optie **Query uitvoeren** om de resultaten te zien.

    :::image type="content" source="media/manage-filter-resource-views/run-query.png" alt-text="Azure Resource Graph-query uitvoeren":::

    Zie [Uw eerste Resource Graph-query uitvoeren met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Overzicht van de Azure Portal

[Dashboards maken en delen in de Azure-portal](azure-portal-dashboards.md)
