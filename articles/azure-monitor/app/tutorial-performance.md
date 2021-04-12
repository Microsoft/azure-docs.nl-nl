---
title: Prestatieproblemen analyseren met behulp van Azure Application Insights | Microsoft Docs
description: Zelfstudie voor het vinden en diagnosticeren van prestatieproblemen in uw toepassing met behulp van Azure Application Insights.
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 06/15/2020
ms.custom: mvc
ms.openlocfilehash: 36a581cb958639f4e9d88a8828b341ef1a1919d7
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383215"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Prestatieproblemen vinden en diagnosticeren met behulp van Azure Application Insights

Azure Application Insights verzamelt telemetrie van uw toepassing om de werking en prestaties te analyseren.  U kunt deze informatie gebruiken om problemen te identificeren die zich kunnen voordoen of om te identificeren welke verbeteringen in de toepassing het meeste effect zouden hebben voor gebruikers.  Deze zelfstudie leidt u door het proces van het analyseren van de prestaties van zowel de servercomponenten van uw applicatie als het perspectief van de client.  In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Het identificeren van de prestaties van bewerkingen aan de serverzijde
> * Het analyseren van serverbewerkingen om de hoofdoorzaak van trage prestaties vast te stellen
> * Het identificeren van de traagste bewerkingen aan de clientzijde
> * Details van paginaweergaven analyseren met een querytaal


## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

- Installeer [Visual Studio 2019](https://www.visualstudio.com/downloads/) met de volgende workloads:
    - ASP.NET-ontwikkeling en webontwikkeling
    - Azure-ontwikkeling
- Implementeer een .NET-toepassing in Azure en [schakel de Application Insights-SDK](../app/asp-net.md)in.
- [Schakel de Application Insights-profiler in](../app/profiler.md#installation) voor uw toepassing.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.
Meld u aan bij de Azure-portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="identify-slow-server-operations"></a>Trage serverbewerkingen identificeren
Application Insights verzamelt prestatiedetails over de verschillende bewerkingen in uw toepassing. Door de bewerkingen met de langste duur te identificeren, kunt u potentiële problemen diagnosticeren of de beste ontwikkelingsrichting bepalen voor het verbeteren van de algehele prestaties van de toepassing.

1. Selecteer **Application Insights** en selecteer vervolgens uw abonnement.  
1. U kunt het deelvenster **Prestaties** openen door **Prestaties** te selecteren in het menu **Onderzoeken** of door op de grafiek **Serverreactietijd** te klikken.

    ![Prestaties](media/tutorial-performance/1-overview.png)

2. In het deelvenster **Prestaties** deelvenster ziet u het aantal en de gemiddelde duur van elke bewerking voor de toepassing.  U kunt deze informatie gebruiken om de bewerkingen te identificeren die de meeste impact hebben voor gebruikers. In dit voorbeeld zijn **GET Customers/Details** en **GET Home/Index** goede kandidaten om te onderzoeken, vanwege hun relatief lange duur en hoge aantal aanroepen.  Andere bewerkingen hebben wellicht een langere duur, maar werden zelden aangeroepen, zodat het effect van een verbetering ervan minimaal zou zijn.  

    ![Performance Server-deelvenster](media/tutorial-performance/2-server-operations.png)

3. In de grafiek ziet u nu de gemiddelde duur van de geselecteerde bewerkingen gedurende een bepaalde periode. U kunt overschakelen naar het 95e percentiel om de prestatieproblemen te zoeken. Voeg de bewerkingen waarin u geïnteresseerd bent toe door ze vast te maken aan de grafiek.  Dit laat zien dat er enkele pieken zijn die het onderzoeken waard zijn.  Isoleer dit verder door het tijdvenster van de grafiek te verkleinen.

    ![Bewerkingen vastmaken](media/tutorial-performance/3-server-operations-95th.png)

4.  In het prestatiedeelvenster rechts ziet u de verdeling van de duur voor verschillende aanvragen voor de geselecteerde bewerking.  Maak het venster kleiner om bij ongeveer het 95e percentiel te beginnen. Op de inzichtenkaart 'Top 3 afhankelijkheden' ziet u meteen dat de externe afhankelijkheden waarschijnlijk bijdragen aan de trage transacties.  Klik op de knop met het aantal steekproeven voor een lijst met de steekproeven. U kunt dan elke steekproef selecteren om de transactiedetails te bekijken.

5.  U kunt in één oogopslag zien dat de aanroep van de Azure-tabel Fabrikamaccount het meeste bijdraagt aan de totale duur van de transactie. U kunt ook zien dat deze is mislukt vanwege een uitzondering. U kunt op elk item in de lijst klikken om de details hiervan rechts weer te geven. [Meer informatie over de functionaliteit voor transactiediagnoses](../app/transaction-diagnostics.md)

    ![End-to-end-details van bewerking](media/tutorial-performance/4-end-to-end.png)
    

6.  De [**Profiler**](../app/profiler-overview.md) helpt u verder met diagnoses op codeniveau door de werkelijke code te laten zien die voor de bewerking is uitgevoerd en de tijd die elke stap kostte. Voor bepaalde bewerkingen is er mogelijk geen tracering, omdat de profiler periodiek wordt uitgevoerd.  Na verloop van tijd moeten meer bewerkingen traceringen hebben.  Klik op **Profilertraceringen** om de profiler voor de bewerking te starten.
5.  De tracering toont de afzonderlijke gebeurtenissen voor elke bewerking, zodat u de hoofdoorzaak voor de duur van de algehele bewerking kunt onderzoeken.  Klik op een van de bovenste voorbeelden, die de langste duur hebben.
6.  Klik op **Dynamisch pad** om het specifieke pad van gebeurtenissen die het meest bijdragen aan de totale duur van de bewerking, te markeren.  In dit voorbeeld ziet u dat de traagste aanroep afkomstig is van de methode *FabrikamFiberAzureStorage.GetStorageTableData*. Het onderdeel dat de meeste tijd in beslag neemt is de methode *CloudTable.CreateIfNotExist*. Als deze regel code wordt uitgevoerd telkens wanneer de functie wordt aangeroepen, worden er onnodige netwerkaanroepen en CPU-bronnen verbruikt. De beste manier om uw code te verbeteren is door deze regel in een opstartmethode te plaatsen die slechts één keer wordt uitgevoerd.

    ![Profiler-details](media/tutorial-performance/5-hot-path.png)

7.  De **Prestatietip** bovenaan het scherm ondersteunt de gedachte dat de lange duur te wijten is aan wachten.  Klik op de koppeling **wachten** voor documentatie over het interpreteren van de verschillende typen gebeurtenissen.

    ![Prestatietip](media/tutorial-performance/6-perf-tip.png)

8.   Voor verdere analyse kunt u op **Tracering downloaden** klikken om de tracering te downloaden. U kunt deze gegevens weergeven met behulp van [PerfView](https://github.com/Microsoft/perfview#perfview-overview).

## <a name="use-logs-data-for-server"></a>Logboekgegevens voor server gebruiken
 Logboeken bieden een uitgebreide querytaal voor het analyseren van alle met Application Insights verzamelde gegevens. U kunt deze gebruiken om diepgaande analysen uit te voeren op aanvraag- en prestatiegegevens.

1. Ga terug naar het detailvenster van de bewerking en klik op het ![pictogram Logboeken](media/tutorial-performance/app-viewinlogs-icon.png)**Weergeven in logboeken (Analyse)**

2. Logboeken worden geopend met een query voor elk van de weergaven in het deelvenster.  U kunt deze query’s zó uitvoeren, of ze aanpassen aan uw behoeften.  De eerste query laat de duur van deze bewerking gedurende een bepaalde periode zien.

    ![query voor logboeken](media/tutorial-performance/7-request-time-logs.png)


## <a name="identify-slow-client-operations"></a>Trage clientbewerkingen identificeren
Naast het identificeren van serverprocessen om te optimaliseren, kan Application Insights ook het perspectief van clientbrowsers analyseren.  Dit kan u helpen potentiële verbeteringen aan clientcomponenten te identificeren en zelfs problemen met verschillende browsers of verschillende locaties te identificeren.

1. Selecteer **Browser** onder **Onderzoeken** en klik vervolgens op **Browserprestaties** of selecteer **Prestaties** onder **Onderzoeken** en schakel over naar het tabblad **Browser** door op de wisselknop Server/browser in de rechterbovenhoek te klikken om de samenvatting van de browserprestaties te openen. Dit geeft een visueel overzicht van verschillende telemetrieën van uw toepassing vanuit het perspectief van de browser.

    ![Browseroverzicht](media/tutorial-performance/8-browser.png)

2. Selecteer een van de bewerkingsnamen, klik vervolgens op de blauwe knop met steekproeven in de rechterbenedenhoek en selecteer een bewerking. Hiermee worden de end-to-end-transactiedetails weergegeven. Aan de rechter kant kunt u de **eigenschappen van de paginaweergave** weergeven. Hiermee kunt u details bekijken van de client die de pagina aanvraagt, inclusief het type browser en de locatie. Deze informatie kan u helpen bepalen of er prestatieproblemen zijn die te maken hebben met specifieke typen clients.

    ![Paginaweergave](media/tutorial-performance/9-page-view-properties.png)

## <a name="use-logs-data-for-client"></a>Logboekgegevens voor client gebruiken
Net als de gegevens die worden verzameld voor serverprestaties, maakt Application Insights alle clientgegevens beschikbaar voor diepgaande analyse met behulp van Logs.

1. Ga terug naar het browseroverzicht en klik op het ![pictogram Logboeken](media/tutorial-performance/app-viewinlogs-icon.png) **Weergeven in logboeken (Analyse)**

2. Logboeken worden geopend met een query voor elk van de weergaven in het deelvenster. De eerste query laat de duur zien van verschillende paginaweergaven gedurende een bepaalde periode.

    ![Query voor logboeken](media/tutorial-performance/10-page-view-logs.png)

## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd runtime-uitzonderingen te identificeren, kunt u doorgaan naar de volgende zelfstudie om te leren hoe u waarschuwingen kunt maken in reactie op fouten.

> [!div class="nextstepaction"]
> [Waarschuwing bij toepassingsstatus](./tutorial-alert.md)

