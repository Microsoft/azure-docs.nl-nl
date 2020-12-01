---
title: Metrische gegevens en onbewerkte logboeken bewaken voor Azure CDN van micro soft
description: In dit artikel wordt de Azure CDN beschreven in metrische gegevens en onbewerkte logboeken van micro soft monitoring.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/25/2020
ms.author: allensu
ms.openlocfilehash: e73b4b2eefeb26dad4d028f617cbe453dbd1870d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96342461"
---
# <a name="monitoring-metrics-and-raw-logs-for-azure-cdn-from-microsoft"></a>Metrische gegevens en onbewerkte logboeken bewaken voor Azure CDN van micro soft
Met Azure CDN van micro soft kunt u bronnen bewaken op de volgende manieren om problemen op te lossen, op te sporen en op te sporen. 

* Onbewerkte logboeken bieden uitgebreide informatie over elke aanvraag die door CDN wordt ontvangen. Onbewerkte logboeken verschillen van activiteiten Logboeken. Met activiteiten logboeken krijgt u inzicht in de bewerkingen die worden uitgevoerd op Azure-resources.
* Metrische gegevens, waarbij vier metrische sleutel gegevens worden weer gegeven op CDN, inclusief de verhouding tussen de bytes en de som van aanvragen, de grootte van de antwoorden en de totale latentie. Het biedt ook verschillende dimensies voor het opsplitsen van metrische gegevens.
* Waarschuwing, waarmee klanten een waarschuwing voor belang rijke metrische gegevens kunnen instellen
* Aanvullende metrische gegevens, waarmee klanten Azure Log Analytics kunnen gebruiken om extra meet waarden in te scha kelen. We bieden ook query voorbeelden voor enkele andere metrische gegevens onder Azure Log Analytics.

> [!IMPORTANT]
> De functie voor onbewerkte HTTP-Logboeken is beschikbaar voor Azure CDN van micro soft.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration---azure-portal"></a>Configuratie-Azure Portal

RAW-logboeken voor uw Azure CDN van het micro soft-profiel configureren: 

1. Selecteer **alle resources** in het menu Azure Portal  >>  **\<your-CDN-profile>** .

2. Selecteer **Diagnostische instellingen** onder **Bewaking**.

3. Selecteer **+ Diagnostische instelling toevoegen**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-01.png" alt-text="Diagnostische instelling voor CDN-profiel toevoegen." border="true":::
    
    > [!IMPORTANT]
    > Onbewerkte logboeken zijn alleen beschikbaar in het profiel niveau, terwijl geaggregeerde HTTP-status code logboeken beschikbaar zijn op het niveau van het eind punt.

4. Voer onder **Diagnostische instellingen** een naam in voor de diagnostische instelling onder **naam van diagnostische instellingen**.

5. Selecteer de **AzureCdnAccessLog** en stel de Bewaar periode in op dagen.

6. Selecteer de **doel gegevens**. Bestemmings opties zijn:
    * **Verzenden naar Log Analytics**
        * Selecteer het **abonnement** en de **log Analytics-werk ruimte**.
    * **Archiveren naar een opslag account**
        * Selecteer het **abonnement** en het **opslag account**.
    * **Streamen naar een Event Hub**
        * Selecteer het **abonnement**, de **Event hub-naam ruimte**, de naam van de **Event hub (optioneel)** en de naam van het **Event hub-beleid**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-02.png" alt-text="Configureer de doel locatie voor de logboek instellingen." border="true":::

7. Selecteer **Opslaan**.

## <a name="configuration---azure-powershell"></a>Configuratie-Azure PowerShell

Gebruik [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) om de diagnostische instelling voor onbewerkte logboeken te configureren.

Bewaar gegevens worden gedefinieerd door de optie **-RetentionInDays** in de opdracht.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Diagnostische logboeken inschakelen in een opslag account

1. Meld u aan bij Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Voer de volgende opdrachten in om Diagnostische logboeken in te scha kelen in een opslag account. Vervang de variabelen door de waarden:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Diagnostische logboeken voor Log Analytics werk ruimte inschakelen

1. Meld u aan bij Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Als u Diagnostische logboeken wilt inschakelen voor een Log Analytics-werk ruimte, voert u deze opdrachten in. Vervang de variabelen door de waarden:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Diagnostische logboeken inschakelen voor Event Hub naam ruimte

1. Meld u aan bij Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Als u Diagnostische logboeken wilt inschakelen voor een Event Hub naam ruimte, voert u deze opdrachten in. Vervang de variabelen door de waarden:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>Eigenschappen van onbewerkte logboeken

Azure CDN van de micro soft-service biedt momenteel onbewerkte Logboeken. RAW-logboeken bieden afzonderlijke API-aanvragen voor elke vermelding met het volgende schema: 

| Eigenschap              | Beschrijving                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | De unieke verwijzings reeks die een aanvraag identificeert die wordt geleverd door de voor deur, ook verzonden als X-Azure-ref-header naar de client. Vereist voor het zoeken naar details in de logboeken van de toegang voor een specifieke aanvraag. |
| HttpMethod            | De HTTP-methode die door de aanvraag wordt gebruikt.                                                                                                                                                                     |
| HttpVersion           | Het type van de aanvraag of verbinding.                                                                                                                                                                   |
| RequestUri            | De URI van de ontvangen aanvraag.                                                                                                                                                                         |
| RequestBytes          | De grootte van het HTTP-aanvraag bericht in bytes, met inbegrip van de aanvraag headers en de hoofd tekst van de aanvraag.                                                                                                   |
| ResponseBytes         | Bytes dat als reactie door de back-endserver wordt verzonden.                                                                                                                                                    |
| User agent             | Het browser type dat door de client wordt gebruikt.                                                                                                                                                               |
| ClientIp              | Het IP-adres van de client die de aanvraag heeft ingediend.                                                                                                                                                  |
| TimeTaken             | De duur van de actie in milliseconden.                                                                                                                                            |
| Exemplaar      | De TLS/SSL-protocol versie die wordt gebruikt door de aanvraag of null als er geen versleuteling is.                                                                                                                           |
| Eindpunt              | De CDN-eindpunt-host is geconfigureerd onder het bovenliggende CDN-profiel.                                                                                                                                   |
| Hostnaam van back-end     | De naam van de back-end-host of de oorsprong waar aanvragen worden verzonden.                                                                                                                                |
| Verzonden naar het oorspronkelijke schild </br> (afgeschaft) * **Zie opmerking bij afschaffing hieronder.** | Als dit het geval is, betekent dit dat de aanvraag is beantwoord van de oorspronkelijke schild in de cache in plaats van de Edge-pop. Schild van oorsprong is een bovenliggende cache die wordt gebruikt om de verhouding van de cache treffers te verbeteren.                                       |
| isReceivedFromClient | Als deze waarde True is, betekent dit dat de aanvraag afkomstig is van de client. Als de waarde False is, is de aanvraag een Missing in de rand (Child POP) en wordt gereageerd vanaf het afschermen van de oorsprong (bovenliggende POP). 
| Http status code        | De HTTP-status code die is geretourneerd door de proxy.                                                                                                                                                        |
| HttpStatusDetails     | De resulterende status van de aanvraag. De betekenis van deze teken reeks waarde is te vinden in een status verwijzings tabel.                                                                                              |
| Keuzemenu                   | De rand pop, die op de aanvraag van de gebruiker reageerde. De afkortingen van Pop's zijn luchthaven codes van hun respectieve metro lijnen.                                                                                   |
| Cache status          | Geeft aan of het object is geretourneerd uit de cache of afkomstig is van de oorsprong.                                                                                                             |
> [!NOTE]
> De logboeken kunnen worden weer gegeven onder uw Log Analytics-profiel door een query uit te voeren. Een voorbeeld query zou er als volgt uitzien:
    ```
    AzureDiagnostics | where Category == "AzureCdnAccessLog"
    ```

### <a name="sent-to-origin-shield-deprecation"></a>Verzonden naar afkomst van oorsprong afschermen
De onbewerkte logboek eigenschap **isSentToOriginShield** is afgeschaft en vervangen door een nieuw veld **isReceivedFromClient**. Gebruik het nieuwe veld als u het afgeschafte veld al gebruikt. 

Onbewerkte logboeken bevatten logboeken die zijn gegenereerd op basis van CDN Edge (onderliggende POP) en Afscherm van oorsprong. Schild van oorsprong verwijst naar bovenliggende knoop punten die zich wereld wijd bevinden. Deze knoop punten communiceren met de oorspronkelijke servers en verminderen de belasting van het verkeer op de oorsprong. 

Voor elke aanvraag die naar de oorsprong van het schild gaat, zijn er twee logboek vermeldingen:

* Eén voor Edge-knoop punten
* Eén voor het afschermen van oorsprong. 

U kunt het veld **isReceivedFromClient** gebruiken om de juiste gegevens op te halen om de uitkomsten of reacties te onderscheiden van de Edge-knoop punten versus het oorsprongs schild. 

Als de waarde ONWAAR is, betekent dit dat de aanvraag wordt gereageerd van het afschermen van de oorsprong van het Schild-knoop punt. Deze aanpak is van kracht om onbewerkte logboeken te vergelijken met facturerings gegevens. Er worden geen kosten in rekening gebracht voor het afbreken van de oorsprong van het schild naar de Edge-knoop punten. Er worden kosten in rekening gebracht voor het uitschrijven van de Edge-knoop punten naar clients. 

**Kusto-query voorbeeld voor het uitsluiten van logboeken die zijn gegenereerd op basis van een schild in Log Analytics.**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> De functie voor onbewerkte HTTP-Logboeken is automatisch beschikbaar voor profielen die na **25 februari 2020** zijn gemaakt of bijgewerkt. Voor CDN-profielen die u eerder hebt gemaakt, moet het CDN-eind punt bij het instellen van de logboek registratie worden bijgewerkt. Een voor beeld: er kan worden genavigeerd naar geo-filtering onder CDN-eind punten en alle landen/regio's blok keren die niet relevant zijn voor de werk belasting en op Opslaan drukken.


## <a name="metrics"></a>Metrische gegevens
Azure CDN van micro soft is geïntegreerd met Azure Monitor en publiceert vier CDN-metrische gegevens om problemen bij te houden, op te lossen en fouten op te sporen. 

De metrische gegevens worden weer gegeven in grafieken en toegankelijk via Power shell, CLI en API. De CDN-metrische gegevens zijn gratis.

Azure CDN van micro soft-metingen en verzendt de metrische gegevens over 60-seconden intervallen. De metrische gegevens kunnen Maxi maal drie minuten in de portal worden weer gegeven. 

Zie [Azure monitor metrische](../azure-monitor/platform/data-platform-metrics.md)gegevens voor meer informatie.

**Metrische gegevens die door Azure CDN van micro soft worden ondersteund**

| Metrische gegevens         | Beschrijving                                                                                                      | Dimensie                                                                                   |
|-----------------|------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| Percentage treffers * | Het percentage van de uitvoer van de CDN-cache dat wordt berekend op basis van het totale aantal uitgangen.                                      | Eindpunt                                                                                    |
| RequestCount    | Het aantal client aanvragen dat door CDN wordt geleverd.                                                                     | Eindpunt </br> Land van de client. </br> Client regio. </br> HTTP-status. </br> HTTP-status groep. |
| ResponseSize    | Het aantal bytes dat is verzonden als antwoorden van CDN Edge naar clients.                                                  |Eindpunt </br> Land van de client. </br> Client regio. </br> HTTP-status. </br> HTTP-status groep.                                                                                          |
| TotalLatency    | De totale tijd van de client aanvraag die door CDN is ontvangen **totdat de laatste reactie byte van CDN naar de client is verzonden**. |Eindpunt </br> Land van de client. </br> Client regio. </br> HTTP-status. </br> HTTP-status groep.                                                                                             |

**_Aantal bytes treffers = (uitgang van rand-uitgang van oorsprong)/egress vanaf rand_*

Scenario's die zijn uitgesloten voor de berekening van de verhouding treffers in bytes:

* U kunt geen cache expliciet configureren door middel van regel Engine of cache gedrag van query reeks.
* U kunt de instructie cache-Control expliciet configureren met No-Store of private cache.

### <a name="metrics-configuration"></a>Configuratie van metrische gegevens

1. Selecteer **alle resources** in het menu Azure Portal  >>  **\<your-CDN-profile>** .

2. Onder **bewaking** selecteert u **metrische gegevens**:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-03.png" alt-text="Metrische gegevens voor het CDN-profiel." border="true":::

3. Selecteer **metrische gegevens toevoegen** en selecteer de toe te voegen metriek:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-04.png" alt-text="Voeg de metrische gegevens voor het CDN-profiel toe en selecteer deze." border="true":::

4. Selecteer **filter toevoegen** om een filter toe te voegen:
    
    :::image type="content" source="./media/cdn-raw-logs/raw-logs-05.png" alt-text="Filter Toep assen op metrieke waarden." border="true":::

5. Selecteer splitsing **Toep assen** om trend op verschillende dimensies te bekijken:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-06.png" alt-text="Pas het splitsen toe op de metriek." border="true":::

6. Selecteer **nieuwe** grafiek om een nieuwe grafiek toe te voegen:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-07.png" alt-text="Voeg een nieuwe grafiek toe aan de metrische weer gave." border="true":::

### <a name="alerts"></a>Waarschuwingen

U kunt waarschuwingen instellen op micro soft CDN door **bewakings**  >>  **waarschuwingen** te selecteren.

Selecteer **nieuwe waarschuwings regel** voor metrische gegevens die worden weer gegeven in de sectie metrische gegevens:

:::image type="content" source="./media/cdn-raw-logs/raw-logs-08.png" alt-text="Waarschuwingen configureren voor het CDN-eind punt." border="true":::

Er wordt een waarschuwing berekend op basis van Azure Monitor. Zie [Azure monitor-waarschuwingen](../azure-monitor/platform/alerts-overview.md)voor meer informatie over waarschuwingen.

### <a name="additional-metrics"></a>Aanvullende metrische gegevens
U kunt extra metrische gegevens inschakelen met behulp van Azure Log Analytics en onbewerkte logboeken voor extra kosten.

1. Volg de bovenstaande stappen voor het inschakelen van diagnostische gegevens voor het verzenden van RAW-logboeken naar log Analytics.

2. Selecteer de Log Analytics werk ruimte die u hebt gemaakt:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-09.png" alt-text="Log Analytics-werk ruimte selecteren" border="true":::   

3. Selecteer **Logboeken** onder **Algemeen** in de log Analytics-werk ruimte.  Selecteer vervolgens **aan de slag**:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-10.png" alt-text="Resource-werk ruimte van log Analytics." border="true":::   
 
4. Selecteer **CDN-profielen**.  Selecteer een voorbeeld query voor het uitvoeren of sluiten van het voorbeeld scherm om een aangepaste query in te voeren:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-11.png" alt-text="Voorbeeld scherm van query." border="true":::   

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-12.png" alt-text="Uitvoering van query." border="true":::   

4. Als u gegevens wilt weer geven per grafiek, selecteert u **diagram**.  Selecteer **vastmaken aan dash board** om de grafiek aan het Azure-dash board vast te maken:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-13.png" alt-text="Een grafiek vastmaken aan het dash board." border="true"::: 

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u HTTP RAW-logboeken ingeschakeld voor de micro soft CDN-service.

Zie voor meer informatie over Azure CDN en de andere Azure-Services die in dit artikel worden genoemd:

* [Analyseren](cdn-log-analysis.md) Gebruiks patronen Azure CDN.

* Meer informatie over [Azure monitor](../azure-monitor/overview.md).

* [Log Analytics configureren in azure monitor](../azure-monitor/log-query/log-analytics-tutorial.md).