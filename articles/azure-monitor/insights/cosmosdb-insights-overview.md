---
title: Azure Cosmos DB met Azure Monitor bewaken voor Cosmos DB | Microsoft Docs
description: In dit artikel wordt de Azure Monitor voor Cosmos DB functie beschreven waarmee Cosmos DB-eigen aars een duidelijk beeld krijgen van de prestaties en het gebruik van problemen met hun CosmosDB-accounts.
author: lgayhardt
ms.author: lagayhar
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: fdf482f5afc444aff77c2ab528a4e333a0282c3d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582356"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db"></a>Azure Monitor verkennen voor Azure Cosmos DB

Azure Monitor voor Azure Cosmos DB biedt een overzicht van de algehele prestaties, fouten, capaciteit en operationele status van al uw Azure Cosmos DB bronnen in een uniforme interactieve ervaring. In dit artikel vindt u meer informatie over de voor delen van deze nieuwe bewakings ervaring en hoe u de ervaring kunt aanpassen en aanpassen aan de unieke behoeften van uw organisatie.   

## <a name="introduction"></a>Introductie

Voordat u aan de slag gaat, moet u weten hoe de informatie wordt gepresenteerd en gevisualiseerd. 

Het biedt:

* **Op schaal perspectief** van uw Azure Cosmos DB resources in al uw abonnementen op één locatie, met de mogelijkheid om alleen de abonnementen en resources te bepalen die u wilt evalueren.

* **Zoom analyse** van een bepaalde Azure CosmosDB-resource uit om problemen op te lossen of gedetailleerde analyses uit te voeren op categorie gebruik, storingen, capaciteit en bewerkingen. Als u een van deze opties selecteert, krijgt u een gedetailleerde weer gave van de relevante Azure Cosmos DB metrische gegevens.  

* **Aanpasbaar** : deze ervaring is gebaseerd op Azure monitor werkmap sjablonen, zodat u kunt wijzigen welke metrische gegevens worden weer gegeven, wijzigen of instellen van drempel waarden die worden uitgelijnd met uw limieten en vervolgens opslaan in een aangepaste werkmap. Grafieken in de werkmappen kunnen vervolgens worden vastgemaakt aan Azure-Dash boards.  

Voor deze functie hoeft u niets in te scha kelen of te configureren. deze Azure Cosmos DB metrische gegevens worden standaard verzameld.

>[!NOTE]
>Er zijn geen kosten verbonden aan het verkrijgen van toegang tot deze functie en er worden alleen kosten in rekening gebracht voor de Azure Monitor essentiële functies die u configureert of inschakelt, zoals wordt beschreven op de pagina met [Azure monitor prijs informatie](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Metrische gegevens over gebruik en prestaties voor Azure Cosmos DB weer geven

Voer de volgende stappen uit om het gebruik en de prestaties van uw opslag accounts in al uw abonnementen weer te geven.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Zoek naar **monitor** en selecteer **monitor**.

    ![Zoekvak met het woord ' Monitor ' en een vervolg keuzelijst met de tekst ' Monitor ' met een snelheid van de schijf stijl](./media/cosmosdb-insights-overview/search-monitor.png)

3. Selecteer **Cosmos DB**.

    ![Scherm opname van Cosmos DB werkmap overzicht](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Overzicht

In **overzicht** bevat de tabel interactieve Azure Cosmos DB metrische gegevens. U kunt de resultaten filteren op basis van de opties die u selecteert in de volgende vervolg keuzelijsten:

* **Abonnementen** : alleen abonnementen met een Azure Cosmos DB resource worden weer gegeven.  

* **Cosmos DB** : u kunt alle, een subset of één Azure Cosmos DB resource selecteren.

* **Tijds bereik** : de laatste 4 uur aan gegevens worden standaard weer gegeven op basis van de bijbehorende selecties.

De tegel item in de vervolg keuzelijst bevat een samen telling van het totale aantal Azure Cosmos DB resources in de geselecteerde abonnementen. Er zijn voorwaardelijke kleurcoderings-of Heatmaps voor kolommen in de werkmap die de metrische gegevens van trans acties rapporteren. De diepste kleur heeft de hoogste waarde en een lichtere kleur op basis van de laagste waarden. 

Als u een vervolg keuze pijl naast een van de Azure Cosmos DB resources selecteert, wordt er een uitsplitsing van de prestatie gegevens op het niveau van de afzonderlijke database container weer gegeven:

![Uitgevouwen vervolg keuzelijst met afzonderlijke database containers en bijbehorende prestatie analyse](./media/cosmosdb-insights-overview/container-view.png)

Als u de naam van de Azure Cosmos DB resource selecteert, wordt het standaard overzicht van het gekoppelde Azure Cosmos DB account weer **gegeven** . 

### <a name="failures"></a>Fouten

Selecteer **fouten** aan de bovenkant van de pagina en het gedeelte **storingen** van de werkmap sjabloon wordt geopend. Hierin worden de totale aanvragen weer gegeven met de distributie van antwoorden waaruit deze aanvragen worden gemaakt:

![Scherm opname van fouten met uitsplitsing op basis van het type HTTP-aanvraag](./media/cosmosdb-insights-overview/failures.png)

| Code |  Description       | 
|-----------|:--------------------|
| `200 OK`  | Een van de volgende REST-bewerkingen is geslaagd: </br>-Een resource ophalen. </br> : In een resource plaatsen. </br> -POST op een resource. </br> -POST op een opgeslagen procedure resource om de opgeslagen procedure uit te voeren.|
| `201 Created` | Er is een POST-bewerking voor het maken van een resource geslaagd. |
| `404 Not Found` | De bewerking probeert uit te voeren op een resource die niet meer bestaat. De resource is bijvoorbeeld mogelijk al verwijderd. |

Raadpleeg het artikel over de [Azure Cosmos DB HTTP-status code](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)voor een volledige lijst met status codes.

### <a name="capacity"></a>Capaciteit

Selecteer de optie **capaciteit** boven aan de pagina en het gedeelte **capaciteit** van de werkmap sjabloon wordt geopend. U ziet hoe veel documenten u hebt, uw document groei in de loop van de tijd, het gegevens gebruik en de totale hoeveelheid beschik bare opslag die u hebt verlaten.  Dit kan worden gebruikt om mogelijke problemen met de opslag en het gebruik van gegevens te identificeren.

![Capaciteits werkmap](./media/cosmosdb-insights-overview/capacity.png) 

Net als bij de overzichts werkmap selecteert de vervolg keuzelijst naast een Azure Cosmos DB resource in de kolom **abonnement** een uitsplitsing van de afzonderlijke containers waaruit de data base is opgebouwd.

### <a name="operations"></a>Operations 

Selecteer **bewerkingen** boven aan de pagina en het gedeelte **bewerkingen** van de werkmap sjabloon wordt geopend. Het biedt u de mogelijkheid om uw aanvragen weer te geven die zijn gesplitst op basis van het type aanvragen. 

In het onderstaande voor beeld ziet u dat `eastus-billingint` voornamelijk Lees aanvragen ontvangt, maar met een klein aantal upsert en aanvragen maken. Overwegende dat `westeurope-billingint` is alleen-lezen vanuit een aanvraag perspectief, ten minste in de afgelopen vier uur dat de werkmap momenteel is ingesteld op basis van de tijds bereik parameter.

![Operations-werkmap](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>Vastmaken, exporteren en uitvouwen

U kunt een van de metrische gedeelten aan een Azure- [dash board](../../azure-portal/azure-portal-dashboards.md) vastmaken door het pictogram punaise rechtsboven in de sectie te selecteren.

![Voor beeld van de sectie metrische gegevens van de metriek naar dash board](./media/cosmosdb-insights-overview/pin.png)

Als u uw gegevens wilt exporteren naar de Excel-indeling, selecteert u de pijl-omlaag links van het punaise pictogram.

![Pictogram werkmap exporteren](./media/cosmosdb-insights-overview/export.png)

Als u alle vervolg keuzelijsten in de werkmap wilt uitvouwen of samen vouwen, selecteert u het pictogram uitvouwen links van het pictogram exporteren:

![Pictogram werkmap uitvouwen](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db"></a>Azure Monitor voor Azure Cosmos DB aanpassen

Omdat deze ervaring is gebaseerd op Azure monitor werkmap sjablonen, kunt u   >  **bewerken** en een kopie van uw gewijzigde versie in een aangepaste werkmap **Opslaan** . 

![Balk aanpassen](./media/cosmosdb-insights-overview/customize.png)

Werkmappen worden opgeslagen in een resource groep, hetzij in het gedeelte **mijn rapporten** dat persoonlijk is of in de sectie **gedeelde rapporten** dat toegankelijk is voor iedereen die toegang heeft tot de resource groep. Nadat u de aangepaste werkmap hebt opgeslagen, moet u naar de galerie met werkmappen gaan om deze te starten.

![Werkmap galerie starten vanaf de opdracht balk](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>Problemen oplossen

Raadpleeg het [artikel speciale probleemoplossings problemen](troubleshoot-workbooks.md)op basis van werkmappen voor hulp bij het oplossen van problemen.

## <a name="next-steps"></a>Volgende stappen

* [Waarschuwingen voor metrische gegevens](../alerts/alerts-metric.md) en [service status meldingen](../../service-health/alerts-activity-log-service-notifications-portal.md) configureren om automatische waarschuwingen in te stellen voor hulp bij het detecteren van problemen.

* Meer informatie over de scenario's werkmappen zijn ontworpen voor ondersteuning, het ontwerpen van nieuwe en het aanpassen van bestaande rapporten en meer door [interactieve rapporten maken met Azure monitor werkmappen](../visualize/workbooks-overview.md)te controleren.
