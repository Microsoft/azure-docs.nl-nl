---
title: Overzicht van Log Analytics in Azure Monitor
description: Hierin wordt Log Analytics beschreven. Dit is een hulp programma in de Azure Portal die wordt gebruikt om logboek query's voor het analyseren van gegevens in Azure Monitor logboeken te bewerken en uit te voeren.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/04/2020
ms.openlocfilehash: ef8be3d1fd815362971d5ae2beb27edf47766c94
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723160"
---
# <a name="overview-of-log-analytics-in-azure-monitor"></a>Overzicht van Log Analytics in Azure Monitor
Log Analytics is een hulp programma in de Azure Portal dat wordt gebruikt om logboek query's met gegevens in Azure Monitor logboeken te bewerken en uit te voeren. U kunt een eenvoudige query schrijven die een set records retourneert en vervolgens functies van Log Analytics gebruiken om ze te sorteren, te filteren en te analyseren. U kunt ook een meer geavanceerde query schrijven om statistische analyses uit te voeren en de resultaten in een grafiek te visualiseren om een bepaalde trend te identificeren. Of u nu interactief met de resultaten van uw query's werkt of ze gebruikt met andere Azure Monitor functies, zoals waarschuwingen voor logboek query's of werkmappen, Log Analytics is het hulp programma dat u wilt gebruiken om ze te schrijven en te testen. 


> [!TIP]
> In dit artikel vindt u een beschrijving van Log Analytics en elk van de bijbehorende functies. Als u direct naar een zelf studie wilt gaan, raadpleegt u [log Analytics zelf studie](./log-analytics-tutorial.md).



## <a name="starting-log-analytics"></a>Log Analytics starten
Start Log Analytics vanuit de **Logboeken** in het **Azure monitor** menu van de Azure Portal. U ziet deze optie ook in het menu voor de meeste Azure-resources. Ongeacht waar u het start vanaf, is dit hetzelfde Log Analytics hulp programma. Het menu dat u gebruikt om Log Analytics te starten, bepaalt welke gegevens er wel beschikbaar zijn. Als u het opent vanuit het menu **Azure monitor** of het menu **log Analytics werk ruimten** , hebt u toegang tot alle records in een werk ruimte. Als u **Logboeken** van een ander type resource selecteert, zijn uw gegevens beperkt tot logboek gegevens voor die bron. Zie de [logboek query bereik en het tijds bereik in Azure Monitor Log Analytics](./scope.md) voor meer informatie.

[![Log Analytics starten](media/log-analytics-overview/start-log-analytics.png)](media/log-analytics-overview/start-log-analytics.png#lightbox)

Wanneer u Log Analytics start, is het eerste wat u ziet, een dialoog venster met [voorbeeld query's](../logs/example-queries.md). Deze worden gecategoriseerd op basis van de oplossing en u kunt zoeken naar query's die voldoen aan uw specifieke vereisten. Mogelijk kunt u een waarde vinden die precies overeenkomt met wat u nodig hebt, of de ene naar de editor laden en deze zo nodig wijzigen. Bladeren door voorbeeld query's is in feite een fantastische manier om te leren hoe u uw eigen query's schrijft. Als u natuurlijk wilt beginnen met een leeg script en zelf wilt schrijven, kunt u de voorbeeld query's sluiten. Klik op de **query's** boven aan het scherm als u ze weer wilt ophalen.

## <a name="log-analytics-interface"></a>Log Analytics interface
De volgende afbeelding geeft de verschillende onderdelen van Log Analytics.

[![Log Analytics](media/log-analytics-overview/log-analytics.png)](media/log-analytics-overview/log-analytics.png#lightbox)

### <a name="1-top-action-bar"></a>1. bovenste actie balk
Besturings elementen voor het werken met de query in het query venster.

| Optie | Beschrijving |
|:---|:---|
| Bereik | Hiermee geeft u het bereik van de gegevens op die worden gebruikt voor de query. Dit kunnen alle gegevens in een Log Analytics werk ruimte of gegevens voor een bepaalde resource in meerdere werk ruimten zijn. Zie [query bereik](./scope.md). |
| Knop uitvoeren | Klik hierop om de geselecteerde query uit te voeren in het query venster. U kunt ook op SHIFT + ENTER drukken om een query uit te voeren. |
| Tijdkiezer | Selecteer het tijds bereik voor de gegevens die beschikbaar zijn voor de query. Dit wordt genegeerd als u in de query een tijd filter opneemt. Zie de [logboek query bereik en het tijds bereik in Azure Monitor Log Analytics](./scope.md). |
| De knop Opslaan | Sla de query op in de query Verkenner voor de werk ruimte. |
 knop Kopiëren | Een koppeling naar de query, de query tekst of de query resultaten naar het klem bord kopiëren. |
| Knop nieuwe waarschuwings regel | Maak een nieuw tabblad met een lege query. |
| Knop exporteren | De resultaten van de query exporteren naar een CSV-bestand of de query naar Power Query taal indeling van de formule voor gebruik met Power bi. |
| Knop vastmaken aan dash board | Voeg de resultaten van de query toe aan een Azure-dash board. |
| Query knop opmaken | Hiermee rangschikt u de geselecteerde tekst voor de Lees baarheid. |
| Knop voor beeld query's | Open het dialoog venster voorbeeld query's dat wordt weer gegeven wanneer u Log Analytics voor het eerst opent. |
| Knop Query Verkenner | Open **query Explorer** waarmee u toegang hebt tot opgeslagen query's in de werk ruimte. |


### <a name="2-sidebar"></a>2. zijbalk
Lijsten met tabellen in de werk ruimte, voorbeeld query's en filter opties voor de huidige query.

| Tabblad | Beschrijving |
|:---|:---|
| Tabellen | Een lijst met de tabellen die deel uitmaken van de geselecteerde scope. Selecteer **groeperen op** om de groepering van de tabellen te wijzigen. Beweeg de muis aanwijzer over een tabel naam om een dialoog venster weer te geven met een beschrijving van de tabel en opties om de bijbehorende documentatie weer te geven en om een voor beeld van de gegevens te bekijken. Een tabel uitvouwen om de kolommen ervan weer te geven. Dubbel klik op de naam van een tabel of kolom om deze aan de query toe te voegen. |
| Query's | Lijst met voorbeeld query's die u in het query venster kunt openen. Dit is dezelfde lijst die wordt weer gegeven wanneer u Log Analytics opent. Selecteer **groeperen op** om de groepering van de query's te wijzigen. Dubbel klik op een query om deze toe te voegen aan het query venster of Beweeg de muis aanwijzer over andere opties. |
| Filter | Filter opties maken op basis van de resultaten van een query. Nadat u een query hebt uitgevoerd, worden de kolommen weer gegeven met verschillende waarden uit de resultaten. Selecteer een of meer waarden en klik vervolgens op **Toep assen & uitvoeren** om een **where** -opdracht toe te voegen aan de query en deze opnieuw uit te voeren. |

### <a name="3-query-window"></a>3. het query venster
In het query venster kunt u de query bewerken. Dit omvat IntelliSense voor KQL-opdrachten en kleur codering om de Lees baarheid te verbeteren. Klik **+** boven aan het venster om een ander tabblad te openen.

Eén venster kan meerdere query's bevatten. Een query kan geen lege regels bevatten, zodat u meerdere query's in een venster met een of meer lege regels kunt scheiden. De huidige query is het met de cursor op een wille keurige plaats.

Als u de huidige query wilt uitvoeren, klikt u op de knop **uitvoeren** of drukt u op SHIFT + ENTER.

### <a name="4-results-window"></a>4. resultaten venster
De resultaten van de query worden weer gegeven in het venster met resultaten. Standaard worden de resultaten weer gegeven als een tabel. Als u wilt weer geven als een diagram, selecteert u **grafiek** in het resultaten venster of voegt u een **render** -opdracht toe aan uw query.

#### <a name="results-view"></a>Resultaten weergave
Hiermee worden query resultaten weer gegeven in een tabel ingedeeld op kolommen en rijen. Klik links van een rij om de waarden uit te vouwen. Klik op de vervolg keuzelijst **kolommen** om de lijst met kolommen te wijzigen. Sorteer de resultaten door te klikken op de naam van een kolom. Filter de resultaten door te klikken op de trechter naast een kolom naam. Wis de filters en stel de sorteer bewerking opnieuw uit door de query opnieuw uit te voeren.

Selecteer **groeps kolommen** om de groepeer balk boven de query resultaten weer te geven. Groepeer de resultaten op een kolom door deze naar de balk te slepen. Maak geneste groepen in de resultaten door extra kolommen toe te voegen. 

#### <a name="chart-view"></a>Diagramweergave
Hiermee worden de resultaten weer gegeven als een van meerdere beschik bare grafiek typen. U kunt het grafiek type opgeven in een **render** -opdracht in uw query of dit selecteren in de vervolg keuzelijst **type visualisatie** .

| Optie | Beschrijving |
|:---|:---|
| **Visualisatie type** | Type grafiek dat moet worden weer gegeven. |
| **X-as** | Kolom in de resultaten die moeten worden gebruikt voor de X-as 
| **Y-as** | Kolom in de resultaten die moeten worden gebruikt voor de Y-as. Dit is doorgaans een numerieke kolom. |
| **Splitsen op basis van** | Kolom in de resultaten waarmee de reeks in de grafiek wordt gedefinieerd. Er wordt een reeks gemaakt voor elke waarde in de kolom. |
| **Aggregatie** | Type aggregatie dat moet worden uitgevoerd op de numerieke waarden in de Y-as. |

## <a name="relationship-to-azure-data-explorer"></a>Relatie met Azure Data Explorer
Als u al bekend bent met de Web-UI van Azure Data Explorer, moet Log Analytics vertrouwd zijn. Dat komt doordat het is gebouwd op Azure Data Explorer en gebruikmaakt van dezelfde Kusto query language (KQL). Log Analytics voegt functies toe die specifiek zijn voor Azure Monitor, zoals filteren op tijds bereik, en de mogelijkheid om een waarschuwings regel te maken op basis van een query. Beide hulpprogram ma's bevatten een Explorer waarmee u de structuur van beschik bare tabellen kunt scannen, maar de Azure Data Explorer-webinterface werkt voornamelijk samen met tabellen in azure Data Explorer-data bases terwijl Log Analytics werkt met tabellen in een Log Analytics-werk ruimte. 

## <a name="next-steps"></a>Volgende stappen
- Door loop een [zelf studie over het gebruik van log Analytics in het Azure Portal](./log-analytics-tutorial.md).
- Door loop een [zelf studie over het schrijven van query's](./get-started-queries.md).