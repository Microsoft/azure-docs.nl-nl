---
title: Log Analytics-zelfstudie
description: In deze zelfstudie leert u hoe u functies van Log Analytics in Azure Monitor kunt gebruiken om een logboekquery uit te voeren en de resultaten ervan in Azure Portal te analyseren.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/07/2020
ms.openlocfilehash: 06a73b495cefc361db88d80413f4f4be50e105d1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041141"
---
# <a name="log-analytics-tutorial"></a>Log Analytics-zelfstudie
Log Analytics is een hulpprogramma in Azure Portal om logboekquery's te bewerken en uit te voeren op basis van gegevens die zijn verzameld door Azure Monitor-logboeken en om de resultaten interactief te analyseren. U kunt Log Analytics-query's gebruiken om records op te halen die overeenkomen met bepaalde criteria, trends te identificeren, patronen te analyseren en een verscheidenheid aan inzichten in uw gegevens te bieden. 

In deze tutorial wordt u begeleid door de Log Analytics-interface, wordt u op weg geholpen met enkele basisquery's en ziet u hoe u met de resultaten kunt werken. U leert het volgende:

> [!div class="checklist"]
> * Inzicht in het schema voor logboekgegevens
> * Eenvoudige query's schrijven en uitvoeren en het tijdsbereik voor query's wijzigen
> * Queryresultaten filteren, sorteren en groeperen
> * Visuals van queryresultaten weergeven, wijzigen en delen
> * Query's en resultaten laden, exporteren en kopiëren

> [!IMPORTANT]
> In deze zelfstudie worden functies van Log Analytics gebruikt om een ​​query te maken en uit te voeren in plaats van met de query zelf te werken. U gebruikt Log Analytics-functies om een ​​query te maken en een andere voorbeeldquery te gebruiken. Als u klaar bent om de syntaxis van query's te leren en de query zelf rechtstreeks te bewerken, doorloopt u de [zelfstudie voor Kusto-querytaal](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor). In deze zelfstudie worden verschillende voorbeeldquery's doorlopen die u kunt bewerken en uitvoeren in Log Analytics, waarbij u gebruikmaakt van verschillende functies die u in deze zelfstudie leert.


## <a name="prerequisites"></a>Vereisten
In deze zelfstudie wordt gebruik gemaakt van de [Log Analytics-demo-omgeving](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), die veel voorbeeldgegevens bevat die de voorbeeldquery's ondersteunen. U kunt ook uw eigen Azure-abonnement gebruiken, maar mogelijk hebt u geen gegevens in dezelfde tabellen.

## <a name="open-log-analytics"></a>Log Analytics openen
Open de [Log Analytics-demo-omgeving](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade) of selecteer **Logboeken** in het Azure Monitor-menu in uw abonnement. Hiermee wordt het oorspronkelijke bereik ingesteld op een Log Analytics-werkruimte, wat betekent dat uw query wordt geselecteerd uit alle gegevens in die werkruimte. Als u **Logboeken** selecteert in het menu van een Azure-resource, wordt het bereik ingesteld op alleen records van die resource. Zie [Bereik van logboekquery's](./scope.md) voor details over het bereik.

U kunt het bereik weergeven in de linkerbovenhoek van het scherm. Als u uw eigen omgeving gebruikt, ziet u een optie om een ​​ander bereik te selecteren, maar deze optie is niet beschikbaar in de demo-omgeving.

[![Querybereik](media/log-analytics-tutorial/scope.png)](media/log-analytics-tutorial/scope.png#lightbox)

## <a name="table-schema"></a>Tabelschema
De linkerkant van het scherm bevat het tabblad **Tabellen** waarmee u de tabellen kunt controleren die beschikbaar zijn in het huidige bereik. Deze worden standaard gegroepeerd op **Oplossing**, maar u kunt de groepering wijzigen of filteren. 

Vouw de oplossing **Logboekbeheer** uit en zoek de tabel **AzureActivity**. U kunt de tabel uitvouwen om het schema te bekijken, of de muisaanwijzer op de naam plaatsen om aanvullende informatie erover weer te geven. 

[![Tabelweergave](media/log-analytics-tutorial/table-details.png)](media/log-analytics-tutorial/table-details.png#lightbox)

Klik op **meer informatie** om naar de tabelverwijzing te gaan waarin elke tabel en de bijbehorende kolommen worden gedocumenteerd. Klik op **Preview-gegevens** om een paar recente records in de tabel snel te bekijken. Dit kan handig zijn om ervoor te zorgen dat dit de gegevens zijn die u verwacht voordat u er daadwerkelijk een query mee uitvoert.

[![Voorbeeldgegevens](media/log-analytics-tutorial/sample-data.png)](media/log-analytics-tutorial/sample-data.png#lightbox)

## <a name="write-a-query"></a>Een query schrijven
Laten we een query schrijven met de tabel **AzureActivity**. Dubbelklik op de naam om deze toe te voegen aan het queryvenster. U kunt ook rechtstreeks in het venster typen en IntelliSense gebruiken om de namen van tabellen in het huidige bereik en KQL-opdrachten te voltooien.

Dit is de eenvoudigste query die we kunnen schrijven. Hiermee worden gewoon alle records in een tabel geretourneerd. Voer de opdracht uit door op de knop **Uitvoeren** te klikken of door op Shift + Enter te drukken terwijl de cursor ergens in de querytekst staat.

[![Queryresultaten](media/log-analytics-tutorial/query-results.png)](media/log-analytics-tutorial/query-results.png#lightbox)

U kunt zien dat er resultaten zijn. Het aantal records dat door de query wordt geretourneerd, wordt in de rechterbenedenhoek weergegeven. 

## <a name="filter"></a>Filteren

We gaan een filter toevoegen aan de query om het aantal records te verminderen dat wordt geretourneerd. Selecteer het tabblad **filter** in het linkerdeelvenster. Hiermee worden verschillende kolommen in de queryresultaten weergegeven die u kunt gebruiken om de resultaten te filteren. De bovenste waarden in deze kolommen worden weergegeven met het aantal records met die waarde. Klik op **Beheer** onder **CategoryValue** en vervolgens op **Toepassen en uitvoeren**. 

[![Queryvenster](media/log-analytics-tutorial/query-pane.png)](media/log-analytics-tutorial/query-pane.png#lightbox)

Een **waar**-instructie wordt toegevoegd aan de zoekopdracht met de waarde die u hebt geselecteerd. De resultaten bevatten nu alleen die records met die waarde, zodat u kunt zien dat het aantal records is verminderd.

[![Queryresultaten gefilterd](media/log-analytics-tutorial/query-results-filter-01.png)](media/log-analytics-tutorial/query-results-filter-01.png#lightbox)


## <a name="time-range"></a>Tijdsbereik
Alle tabellen in een Log Analytics-werkruimte hebben een kolom met de naam **TimeGenerated**, het tijdstip waarop de record is gemaakt. Alle query's hebben een tijdsbereik dat de resultaten beperkt tot records met een **TimeGenerated**-waarde binnen dat bereik. Het tijdsbereik kan worden ingesteld in de query of met de selector bovenaan het scherm.

Standaard worden records in de afgelopen 24 uur door de query geretourneerd. Selecteer de vervolgkeuzelijst **Tijdsbereik** en wijzig deze in **7 dagen**. Klik nogmaals op **Uitvoeren** om de resultaten te retourneren. U ziet dat de resultaten worden geretourneerd, maar we hebben hier een bericht dat we niet alle resultaten zien. Dit komt omdat Log Analytics maximaal 10.000 records kan retourneren en onze query heeft meer records geretourneerd dan dat. 

[![Tijdsbereik](media/log-analytics-tutorial/query-results-max.png)](media/log-analytics-tutorial/query-results-max.png#lightbox)


## <a name="multiple-query-conditions"></a>Meerdere queryvoorwaarden
We gaan het aantal resultaten verder verminderen door een andere filtervoorwaarde toe te voegen. Een query kan een willekeurig aantal filters bevatten om precies de gewenste set records te bereiken. Selecteer **Geslaagd** onder **ActivityStatusValue** en klik op **Toepassen en uitvoeren**. 

[![Queryresultaten met meerdere filters](media/log-analytics-tutorial/query-results-filter-02.png)](media/log-analytics-tutorial/query-results-filter-02.png#lightbox)


## <a name="analyze-results"></a>Resultaten analyseren
Naast hulp bij het schrijven en uitvoeren van query's, biedt Log Analytics functies voor het werken met de resultaten. Begin met het uitbreiden van een record om de waarden voor alle kolommen te bekijken.

[![Record uitbreiden](media/log-analytics-tutorial/expand-record.png)](media/log-analytics-tutorial/expand-record.png#lightbox)

Klik op de naam van een kolom om de resultaten op die kolom te sorteren. Klik op het filterpictogram ernaast om een filtervoorwaarde op te geven. Dit is vergelijkbaar met het toevoegen van een filtervoorwaarde aan de query zelf, behalve dat dit filter wordt gewist als de query opnieuw wordt uitgevoerd. Gebruik deze methode als u snel een set records wilt analyseren als onderdeel van een interactieve analyse.

Stel bijvoorbeeld een filter in op de kolom **CallerIpAddress** om de records te beperken tot één aanroeper. 

[![Filter voor queryresultaten](media/log-analytics-tutorial/query-results-filter.png)](media/log-analytics-tutorial/query-results-filter.png#lightbox)

In plaats van de resultaten te filteren, kunt u records groeperen op een bepaalde kolom. Wis het filter dat u zojuist hebt gemaakt en schakel vervolgens de schuifregelaar voor **Kolommen groeperen** in. 

[![Kolommen groeperen](media/log-analytics-tutorial/query-results-group-columns.png)](media/log-analytics-tutorial/query-results-group-columns.png#lightbox)

Sleep nu de kolom **CallerIpAddress** naar de groeperingsrij. De resultaten zijn nu geordend op die kolom en u kunt elke groep samenvouwen om u te helpen met uw analyse.

[![Queryresultaten gegroepeerd](media/log-analytics-tutorial/query-results-grouped.png)](media/log-analytics-tutorial/query-results-grouped.png#lightbox)

## <a name="work-with-charts"></a>Werken met grafieken
Laten we eens kijken naar een query die gebruikmaakt van numerieke gegevens die we in een grafiek kunnen bekijken. In plaats van een query te maken, selecteert u een voorbeeldquery.

Klik in het linker deelvenster op **Query's**. Dit deelvenster bevat voorbeeldquery's die u aan het queryvenster kunt toevoegen. Als u uw eigen werkruimte gebruikt, hebt u verschillende query’s in meerdere categorieën, maar als u de demo-omgeving gebruikt, ziet u mogelijk slechts één categorie **Log Analytics-werkruimten**. Vouw deze uit om de query's in de categorie weer te geven.

Klik op de query met de naam **Aantal aanvragen per ResponseCode**. Hiermee wordt de query aan het queryvenster toegevoegd. De nieuwe query wordt gescheiden van de andere door een lege regel. Een query in KQL eindigt op het moment dat er een lege regel wordt aangetroffen, zodat deze worden gezien als afzonderlijke query's. 

[![Nieuwe query](media/log-analytics-tutorial/example-query.png)](media/log-analytics-tutorial/example-query.png#lightbox)

De huidige query is het item waarop de cursor zich bevindt. U ziet dat de eerste query is gemarkeerd; dit geeft aan dat het de huidige query is. Klik op een willekeurige plaats in de nieuwe query om deze te selecteren en klik vervolgens op de knop **Uitvoeren** om deze uit te voeren.

[![Grafiek met queryresultaten](media/log-analytics-tutorial/example-query-output-chart.png)](media/log-analytics-tutorial/example-query-output-chart.png#lightbox)

U ziet dat deze uitvoer een grafiek is in plaats van een tabel zoals de laatste query. Dat komt doordat in de voorbeeldquery een opdracht [weergeven](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) aan het einde wordt gebruikt. U ziet dat er verschillende opties zijn voor het werken met de grafiek, zoals het wijzigen ervan in een ander type.

Selecteer **Resultaten** om de uitvoer van de query als een tabel weer te geven. 

[![Tabel met queryresultaten](media/log-analytics-tutorial/example-query-output-table.png)](media/log-analytics-tutorial/example-query-output-table.png#lightbox)



## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u Log Analytics moet gebruiken, voltooit u de zelfstudie over het gebruik van logboekquery's.
> [!div class="nextstepaction"]
> [Azure Monitor-logboekquery's schrijven](get-started-queries.md)