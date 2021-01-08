---
title: Problemen met Azure Stream Analytics oplossen met resource logboeken
description: In dit artikel wordt beschreven hoe u resource Logboeken in Azure Stream Analytics kunt analyseren.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: troubleshooting
ms.custom: contperf-fy21q1
ms.date: 06/18/2020
ms.openlocfilehash: 56c7ad49fc9af3ca4f30aa5309623c31e9db7385
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011115"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-resource-logs"></a>Problemen met Azure Stream Analytics oplossen met behulp van resource logboeken

Soms wordt een Azure Stream Analytics-taak onverwacht beëindigd. Het is belangrijk om dit type gebeurtenissen te kunnen oplossen. Mislukte taken kunnen worden veroorzaakt door een onverwacht queryresultaat, door storing in de verbinding met apparaten of door een onverwachte serviceonderbreking. Met de resource Logboeken in Stream Analytics kunt u de oorzaak van problemen vaststellen wanneer deze optreden en de herstel tijd verminderen.

Het wordt ten zeerste aanbevolen om resource Logboeken in te scha kelen voor alle taken, omdat dit een aanzienlijk hulp voor fout opsporing en bewaking is.

## <a name="log-types"></a>Logboek typen

Stream Analytics biedt twee typen logboeken:

* [Activiteiten logboeken](../azure-monitor/platform/platform-logs-overview.md) (altijd aan), die inzicht geven in bewerkingen die worden uitgevoerd op taken.

* [Bron logboeken](../azure-monitor/platform/platform-logs-overview.md) (configureerbaar), die uitgebreid inzicht bieden in alles wat er gebeurt met een taak. Bron logboeken worden gestart wanneer de taak wordt gemaakt en beëindigd wanneer de taak wordt verwijderd. Ze omvatten gebeurtenissen wanneer de taak wordt bijgewerkt en terwijl deze wordt uitgevoerd.

> [!NOTE]
> U kunt services zoals Azure Storage, Azure Event Hubs en Azure Monitor Logboeken gebruiken om niet-conforme gegevens te analyseren. Er worden kosten in rekening gebracht op basis van het prijs model voor deze services.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Fout opsporing met activiteiten logboeken

Activiteiten logboeken zijn standaard ingeschakeld en geven inzichten op hoog niveau in bewerkingen die worden uitgevoerd door uw Stream Analytics-taak. Informatie die aanwezig is in activiteiten logboeken kan de hoofd oorzaak van de problemen die invloed hebben op uw taak vinden. Voer de volgende stappen uit om activiteiten Logboeken in Stream Analytics te gebruiken:

1. Meld u aan bij de Azure Portal en selecteer het **activiteiten logboek** in het **overzicht**.

   ![Stream Analytics activiteiten logboek](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. U kunt een lijst weer geven met bewerkingen die zijn uitgevoerd. Elke bewerking waardoor uw taak is mislukt, heeft een rode info-bel.

3. Klik op een bewerking om de samenvattings weergave ervan weer te geven. Informatie is vaak beperkt. Klik op **JSON** voor meer informatie over de bewerking.

   ![Samen vatting van Stream Analytics activiteiten logboek bewerking](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Schuif omlaag naar de sectie **Eigenschappen** van de JSON. Deze bevat details over de fout die de mislukte bewerking heeft veroorzaakt. In dit voor beeld is de fout veroorzaakt door een runtime-fout van de waarden van de afhankelijke Latitude. Het verschil in de gegevens die door een Stream Analytics taak worden verwerkt, veroorzaakt een gegevens fout. U vindt meer informatie over de verschillende [invoer-en uitvoer gegevens fouten en waarom deze optreden](./data-errors.md).

   ![Details van JSON-fout](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. U kunt corrigerende acties uitvoeren op basis van het fout bericht in JSON. In dit voor beeld wordt gecontroleerd om ervoor te zorgen dat de breedte van de Latitude tussen-90 graden ligt en 90 graden aan de query moet worden toegevoegd.

6. Als het fout bericht in de activiteiten logboeken niet nuttig is om de hoofd oorzaak te identificeren, kunt u bron logboeken inschakelen en Azure Monitor Logboeken gebruiken.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Diagnostische gegevens verzenden naar Azure Monitor-logboeken

Het inschakelen van bron logboeken en het verzenden van deze naar Azure Monitor Logboeken wordt sterk aanbevolen. Ze zijn standaard **uitgeschakeld** . Als u deze wilt inschakelen, voert u de volgende stappen uit:

1.  Maak een Log Analytics-werk ruimte als u er nog geen hebt. Het is raadzaam om uw Log Analytics-werk ruimte in dezelfde regio als uw Stream Analytics-taak te hebben.

2.  Meld u aan bij de Azure Portal en navigeer naar uw Stream Analytics-taak. Onder **bewaking** selecteert u **Diagnostische logboeken**. Selecteer vervolgens **Diagnostische gegevens inschakelen**.

    ![Blade navigatie naar resource logboeken](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Geef een **naam** op in de naam van de **Diagnostische instellingen** en schakel de selectie vakjes in voor **uitvoering** en **ontwerp** onder **logboek** en **AllMetrics** onder **metrische gegevens**. Selecteer vervolgens **verzenden naar log Analytics** en kies uw werk ruimte. Klik op **Opslaan**.

    ![Instellingen voor bronnen logboeken](./media/stream-analytics-job-diagnostic-logs/logs-setup.png)

3. Wanneer uw Stream Analytics-taak wordt gestart, worden bron logboeken gerouteerd naar uw Log Analytics-werk ruimte. Als u de resource logboeken voor uw taak wilt weer geven, selecteert u **Logboeken** onder de sectie **bewaking** .

   ![Scherm afbeelding toont het menu algemeen met geselecteerde Logboeken.](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics biedt vooraf gedefinieerde query's waarmee u eenvoudig naar de logboeken kunt zoeken waarin u geïnteresseerd bent. U kunt alle vooraf gedefinieerde query's in het linkerdeel venster selecteren en vervolgens **uitvoeren** selecteren. De resultaten van de query worden weer geven in het onderste deel venster. 

   ![Scherm opname toont logboeken voor een Stream Analytics-taak.](./media/stream-analytics-job-diagnostic-logs/logs-example.png)

## <a name="resource-log-categories"></a>Categorieën van resourcelogboeken

Azure Stream Analytics worden twee categorieën resource logboeken vastgelegd:

* **Ontwerpen**: legt logboek gebeurtenissen vast die verband houden met het ontwerpen van taken, zoals het maken van een taak, het toevoegen en verwijderen van invoer en uitvoer, het toevoegen en bijwerken van de query, en het starten of stoppen van de taak.

* **Uitvoering**: legt gebeurtenissen vast die tijdens de uitvoering van de taak optreden.
    * Connectiviteitsfouten
    * Gegevens verwerkings fouten, waaronder:
        * Gebeurtenissen die niet voldoen aan de definitie van de query (niet-overeenkomende veld typen en waarden, ontbrekende velden, enzovoort)
        * Evaluatie fouten van expressie
    * Andere gebeurtenissen en fouten

## <a name="resource-logs-schema"></a>Schema voor bron logboeken

Alle logboeken worden opgeslagen in JSON-indeling. Elke vermelding heeft de volgende algemene teken reeks velden:

Naam | Beschrijving
------- | -------
tijd | Tijds tempel (in UTC) van het logboek.
resourceId | De ID van de resource waarop de bewerking plaatsvond, in hoofd letters. Het bevat de abonnements-ID, de resource groep en de taak naam. Bijvoorbeeld **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/My-Resource-Group/providers/Microsoft. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | De categorie van het logboek, de **uitvoering** of het **ontwerp**.
operationName | De naam van de bewerking die is geregistreerd. Bijvoorbeeld **Send events: SQL output schrijf fout naar mysqloutput**.
status | De status van de bewerking. Bijvoorbeeld **mislukt** of **geslaagd**.
niveau | Logboek niveau. Bijvoorbeeld: **fout**, **waarschuwing** of **informatief**.
properties | Logboek vermelding-specifieke details, geserialiseerd als een JSON-teken reeks. Zie de volgende secties in dit artikel voor meer informatie.

### <a name="execution-log-properties-schema"></a>Schema voor eigenschappen van uitvoerings logboek

Uitvoerings logboeken bevatten informatie over gebeurtenissen die zijn opgetreden tijdens de uitvoering van Stream Analytics taak. Het schema van eigenschappen varieert, afhankelijk van het feit of de gebeurtenis een gegevens fout of een algemene gebeurtenis is.

### <a name="data-errors"></a>Gegevensfouten

Er is een fout opgetreden tijdens het verwerken van de gegevens in deze logboek categorie. Deze logboeken worden meestal gemaakt tijdens de lees-, serialisatie-en schrijf bewerkingen van gegevens. Deze logboeken bevatten geen verbindings fouten. Connectiviteits fouten worden behandeld als algemene gebeurtenissen. Meer informatie over de oorzaak van verschillende [invoer-en uitvoer gegevens fouten](./data-errors.md).

Naam | Beschrijving
------- | -------
Bron | De naam van de taak invoer of uitvoer waar de fout is opgetreden.
Bericht | Het bericht dat is gekoppeld aan de fout.
Type | Type fout. Bijvoorbeeld, **DataConversionError**, **CsvParserError** of **ServiceBusPropertyColumnMissingError**.
Gegevens | Bevat gegevens die handig zijn om de oorzaak van de fout nauw keurig te vinden. Onderhevig aan afkap ping, afhankelijk van de grootte.

Afhankelijk van de waarde van **operationname** , hebben gegevens fouten het volgende schema:

* Er treden **gebeurtenissen** op tijdens het lezen van gebeurtenissen. Ze treden op wanneer de gegevens bij de invoer niet voldoen aan het query schema om een van de volgende redenen:

   * *Niet-overeenkomend type tijdens gebeurtenis (de) serialiseren*: identificeert het veld dat de fout veroorzaakt.

   * *Kan een gebeurtenis niet lezen, ongeldige serialisatie*: bevat informatie over de locatie in de invoer gegevens waar de fout is opgetreden. Bevat de BLOB-naam voor BLOB-invoer, offset en een voor beeld van de gegevens.

* **Verzenden van gebeurtenissen** vindt plaats tijdens schrijf bewerkingen. Ze identificeren de streaming-gebeurtenis die de fout heeft veroorzaakt.

### <a name="generic-events"></a>Algemene gebeurtenissen

Algemene gebeurtenissen zijn van belang voor alle andere.

Naam | Beschrijving
-------- | --------
Fout | Beschrijving Fout gegevens. Normaal gesp roken is dit uitzonderings gegevens als deze beschikbaar zijn.
Bericht| Logboek bericht.
Type | Type bericht. Wordt toegewezen aan interne categorisatie van fouten. Bijvoorbeeld **JobValidationError** of **BlobOutputAdapterInitializationFailure**.
Correlatie-id | GUID die de uitvoering van de taak op unieke wijze identificeert. Alle vermeldingen in het uitvoerings logboek vanaf het moment dat de taak wordt gestart totdat de taak stopt met dezelfde **correlatie-ID-** waarde.

## <a name="next-steps"></a>Volgende stappen

* [Stream Analytics gegevens fouten](./data-errors.md)
* [Naslag informatie voor de query taal Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)