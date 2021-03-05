---
title: Asset Insights op uw gegevens in azure controle sfeer liggen (preview)
description: In deze hand leiding wordt beschreven hoe u controle sfeer liggen Insights Asset Reporting kunt weer geven en gebruiken voor uw gegevens.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: b9a207ffa14a18a5f4421fd21cebed28290b5ea6
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183077"
---
# <a name="asset-insights-on-your-data-in-azure-purview"></a>Asset Insights op uw gegevens in azure controle sfeer liggen

In deze hand leiding wordt beschreven hoe u controle sfeer liggen Asset Insight-rapporten kunt openen, bekijken en filteren voor uw gegevens.

In deze hand leiding leert u het volgende:

> [!div class="checklist"]
> * Bekijk inzichten uit uw controle sfeer liggen-account.
> * Maak een weer gave van uw gegevens in vogel gaten.
> * Inzoomen voor meer informatie over het aantal activa.

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat met controle sfeer liggen Insights, moet u ervoor zorgen dat u de volgende stappen hebt uitgevoerd:

* Stel uw Azure-resources in en vul het account in met gegevens.

* Een scan voor het bron type instellen en volt ooien.

Zie [gegevens bronnen beheren in azure controle sfeer liggen (preview)](manage-data-sources.md)voor meer informatie.

## <a name="use-purview-asset-insights"></a>Controle sfeer liggen Asset Insights gebruiken

In azure controle sfeer liggen kunt u bron typen registreren en scannen. Zodra de scan is voltooid, kunt u de Asset-distributie bekijken in Asset Insights, waarmee u de status van uw gegevens per classificatie en resource sets vertelt. Er wordt ook aangegeven of er wijzigingen zijn in de gegevens grootte.

> [!NOTE]
> Nadat u uw bron typen hebt gescand, geeft u Asset Insights 3-8 uur om de nieuwe activa weer te geven. De vertraging kan worden veroorzaakt door groot verkeer in de implementatie regio of de grootte van uw werk belasting. Neem contact op met het ondersteunings team voor meer informatie.

1. Navigeer naar uw Azure controle sfeer liggen-resource in de Azure Portal.

1. Selecteer op de pagina **overzicht** in de sectie **aan de slag** de tegel **Open controle sfeer liggen Studio** .

   :::image type="content" source="./media/asset-insights/portal-access.png" alt-text="Controle sfeer liggen starten vanuit de Azure Portal":::

1. Op de **Start** pagina van controle sfeer liggen selecteert u de tegel **inzichten weer geven** om toegang te krijgen tot uw **inzichten** - :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: gebied.

   :::image type="content" source="./media/asset-insights/view-insights.png" alt-text="Bekijk uw inzichten in de Azure Portal":::

1. Selecteer in het gebied **inzichten** :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: de optie **assets** om het controle sfeer liggen **Asset Insights** -rapport weer te geven.

### <a name="view-asset-insights"></a>Asset Insights weer geven

1. Op de pagina hoofd **activa Insights** worden de volgende gebieden weer gegeven:

2. KPI'S op hoog niveau om bron typen, geclassificeerde assets en gedetecteerde assets weer te geven
 
3. In de eerste grafiek worden **assets per bron type** weer gegeven.

4. Bekijk de activa distributie per bron type. Kies een classificatie of een volledige classificatie categorie om de activa distributie per bron type weer te geven. 
 
5. Als u meer wilt weer geven, selecteert u **meer weer geven**, waarin een tabel vorm van de bron typen en het aantal assets wordt weer gegeven. De classificatie filters worden uitgevoerd op deze pagina.

   :::image type="content" source="./media/asset-insights/highlight-kpis.png" alt-text="Kpi's en grafieken in Asset Insights weer geven":::
 
6. Selecteer een specifieke bron waarvan u de bovenste mappen wilt zien met het aantal assets. 

   :::image type="content" source="./media/asset-insights/select-data-source.png" alt-text="Bron type selecteren":::
 
7. Selecteer het totale aantal assets voor de bovenste map binnen het bron type dat u hierboven hebt geselecteerd.

   :::image type="content" source="./media/asset-insights/file-path.png" alt-text="Bestands paden weer geven":::

8. De lijst met bestanden in de map weer geven. Ga terug naar inzichten met behulp van de brood crumbs.

   :::image type="content" source="./media/asset-insights/list-page.png" alt-text="Lijst met assets weer geven":::  

### <a name="file-based-source-types"></a>Bron typen op basis van bestanden
In de volgende paar grafieken in Asset Insights ziet u een distributie van bron typen op basis van bestanden. In de eerste grafiek, de zogeheten **grootte trend (GB) van het bestands type binnen bron typen**, wordt de bovenste trend van bestands type grootte in de afgelopen 30 dagen weer gegeven. 
 
1. Kies het bron type om het bestands type in de bron weer te geven. 
 
1. Selecteer **weer gave meer** om de huidige gegevens grootte te bekijken, de grootte van de huidige activa te wijzigen en het aantal activa te wijzigen.
 
   > [!NOTE]
   > Als de scan slechts eenmaal in de afgelopen 30 dagen is uitgevoerd of als een catalogus wijziging, zoals het toevoegen/verwijderen van de classificatie, slechts eenmaal in 30 dagen is voorgekomen, wordt de bovenstaande wijzigings informatie leeg weer gegeven.

1. Zie de bovenste mappen met wijzigingen in het bovenste activum aantal wanneer u op bron type klikt.

1. Selecteer het pad om de lijst met activa weer te geven.

De tweede grafiek in bron typen op basis van bestanden is ***bestanden die niet zijn gekoppeld aan een resourceset***. Als u verwacht dat alle bestanden moeten worden getotaliseerd in een resourceset, kunt u met deze grafiek begrijpen welke assets niet zijn geïmplementeerd. Ontbrekende activa kunnen een indicatie zijn van het onjuiste bestands patroon in de map. Volg dezelfde stappen als in andere grafieken om meer informatie over de bestanden weer te geven.

   :::image type="content" source="./media/asset-insights/file-based-assets.png" alt-text="Op bestanden gebaseerde assets weer geven":::  

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure controle sfeer liggen Insight-rapporten met [Scan inzichten](./scan-insights.md)
