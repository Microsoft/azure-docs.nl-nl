---
title: Azure-toepassing Insights-app-gegevens weer geven | Microsoft Docs
description: U kunt de Application Insights-connector-oplossing gebruiken om prestatie problemen vast te stellen en inzicht te krijgen in wat gebruikers met uw app doen wanneer ze met Application Insights worden bewaakt.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: c21ed461280dfa617c852fe6c18c30e5d697b704
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031273"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Application Insights-connector-beheer oplossing (afgeschaft)

![Application Insights-symbool](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Met de ondersteuning van [query's voor meerdere bronnen](../logs/cross-workspace-query.md)is de Application Insights-connector beheer oplossing niet meer nodig. Het is afgeschaft en verwijderd uit Azure Marketplace, samen met de OMS-portal die officieel werd afgeschaft op 15 januari 2019 voor de commerciële cloud van Azure. Deze wordt ingetrokken op 30 maart 2019 voor Azure US Government-Cloud.
>
>Bestaande verbindingen blijven werken tot en met 30 juni 2019.  In de OMS-Portal is het niet mogelijk om bestaande verbindingen te configureren en te verwijderen vanuit de portal. Zie [de onderstaande connector verwijderen met Power shell](#removing-the-connector-with-powershell) voor een script in Power shell gebruiken om bestaande verbindingen te verwijderen.
>
>Zie [meerdere Azure Monitor Application Insights resources](./unify-app-resource-data.md)samen voegen voor hulp bij het opvragen van Application Insights logboek gegevens voor meerdere toepassingen. Voor meer informatie over de afschaffing van de OMS-Portal raadpleegt [u OMS Portal verplaatsen naar Azure](./oms-portal-transition.md).
>
> 

De oplossing van de Application Insights-connector helpt u bij het diagnosticeren van prestatie problemen en inzicht te krijgen in wat gebruikers met uw app doen wanneer deze worden bewaakt met [Application Insights](../app/app-insights-overview.md). Weer gaven van dezelfde telemetrie van de toepassing die ontwikkel aars in Application Insights zien, zijn beschikbaar in Log Analytics. Wanneer u echter uw Application Insights-apps integreert met Log Analytics, wordt de zicht baarheid van uw toepassingen verhoogd door de bewerkings-en toepassings gegevens op één plek te hebben. Met dezelfde weer gaven kunt u samen werken met uw app-ontwikkel aars. De algemene weer gaven kunnen helpen de tijd te verminderen voor het detecteren en oplossen van problemen met toepassingen en platforms.

Wanneer u de oplossing gebruikt, kunt u het volgende doen:

- Bekijk al uw Application Insights-apps op één locatie, zelfs als ze zich in verschillende Azure-abonnementen bevinden
- Infrastructuur gegevens correleren met toepassings gegevens
- Toepassings gegevens visualiseren met perspectieven in zoeken in Logboeken
- Draai de gegevens van Log Analytics naar uw Application Insights-app in de Azure Portal


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Verbonden bronnen

In tegens telling tot de meeste andere Log Analytics oplossingen, worden er geen gegevens verzameld voor de Application Insights-connector door agents. Alle gegevens die door de oplossing worden gebruikt, zijn rechtstreeks afkomstig van Azure.

| Verbonden bron | Ondersteund | Beschrijving |
| --- | --- | --- |
| [Windows-agents](./../agents/agent-windows.md) | Nee | De oplossing verzamelt geen gegevens van Windows-agents. |
| [Linux-agents](../vm/quick-collect-linux-computer.md) | Nee | De oplossing verzamelt geen gegevens van Linux-agents. |
| [SCOM-beheer groep](../agents/om-agents.md) | Nee | De oplossing verzamelt geen gegevens van agents in een verbonden SCOM-beheer groep. |
| [Azure Storage-account](../essentials/resource-logs.md#send-to-log-analytics-workspace) | Nee | De oplossing verzamelt geen gegevens uit Azure Storage. |

## <a name="prerequisites"></a>Vereisten

- U hebt een Azure-abonnement nodig om toegang te krijgen tot Application Insights-connector gegevens
- U moet ten minste één geconfigureerde Application Insights Resource hebben.
- U moet de eigenaar of bijdrager zijn van de Application Insights resource.

## <a name="configuration"></a>Configuratie

1. Schakel de Azure Web Apps-analyse-oplossing in via de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps) of via het proces dat wordt beschreven in [log Analytics oplossingen toevoegen van de Oplossingengalerie](../insights/solutions.md).
2. Blader naar [Azure Portal](https://portal.azure.com). Selecteer **alle services** om Application Insights te openen. Zoek vervolgens naar Application Insights. 
3. Onder **abonnementen** selecteert u een abonnement met Application Insights resources en selecteert u vervolgens een of meer toepassingen onder **naam**.
4. Klik op **Opslaan**.

In ongeveer 30 minuten wordt gegevens beschikbaar en wordt de Application Insights tegel bijgewerkt met gegevens, zoals de volgende afbeelding:

![Application Insights tegel](./media/app-insights-connector/app-insights-tile.png)

Andere punten waarop u moet letten:

- U kunt Application Insights-apps alleen koppelen aan een Log Analytics-werk ruimte.
- U kunt alleen [basis-of ondernemings Application Insights resources](https://azure.microsoft.com/pricing/details/application-insights) koppelen aan log Analytics. U kunt echter de gratis laag van Log Analytics gebruiken.

## <a name="management-packs"></a>Management packs

Met deze oplossing worden geen Management Packs in verbonden beheer groepen geïnstalleerd.

## <a name="use-the-solution"></a>De oplossing gebruiken

In de volgende secties wordt beschreven hoe u de Blades kunt gebruiken die worden weer gegeven in het dash board van Application Insights om gegevens uit uw apps te bekijken en ermee te werken.

### <a name="view-application-insights-connector-information"></a>Application Insights-connector gegevens weer geven

Klik op de tegel **Application Insights** om het **Application Insights** dash board te openen om de volgende Blades weer te geven.

![Scherm afbeelding van het Application Insights dash board met de Blades voor toepassingen, gegevens volume en beschik baarheid.](./media/app-insights-connector/app-insights-dash01.png)

![Scherm afbeelding van het Application Insights dash board met de Blades voor server aanvragen, fouten en uitzonde ringen.](./media/app-insights-connector/app-insights-dash02.png)

Het dash board bevat de Blades die in de tabel worden weer gegeven. Elke blade bevat maximaal tien items die overeenkomen met de criteria voor het opgegeven bereik en de duur van die blade. U kunt een zoek opdracht in Logboeken uitvoeren die alle records retourneert wanneer u op **alles weer geven** onder aan de Blade klikt of wanneer u op de Blade-kop klikt.


| **Kolom** | **Beschrijving** |
| --- | --- |
| Toepassingen-aantal toepassingen | Toont het aantal toepassingen in toepassings bronnen. Hierin worden ook de toepassings namen en voor elk het aantal toepassings records weer gegeven. Klik op het nummer om een zoek opdracht voor logboeken uit te voeren voor <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Klik op de naam van een toepassing om een logboek zoekopdracht uit te voeren voor de toepassing waarin toepassings records per host worden weer gegeven, records per type telemetrie en alle gegevens op type (op basis van de laatste dag). |
| Gegevens volume – hosts die gegevens verzenden | Toont het aantal computers waarop de gegevens worden verzonden. Hierin worden ook de hosts en het aantal records van de computer voor elke host weer gegeven. Klik op het nummer om een zoek opdracht voor logboeken uit te voeren voor <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Klik op de naam van een computer om een logboek zoekopdracht uit te voeren voor de host die toepassings records per host weergeeft, records per type telemetrie en alle gegevens op type (op basis van de laatste dag). |
| Beschik baarheid – webtest-resultaten | Toont een ring diagram voor de resultaten van webtests en geeft aan of het mislukt. Klik op de grafiek om een zoek opdracht voor logboeken uit te voeren voor <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Met resultaten wordt het aantal gangen en fouten voor alle tests weer gegeven. Alle Web Apps met verkeer voor de laatste minuut worden weer gegeven. Klik op de naam van een toepassing om een zoek opdracht in logboeken weer te geven met details van mislukte webtests. |
| Server aanvragen-aanvragen per uur | Geeft een lijn diagram weer van de server aanvragen per uur voor verschillende toepassingen. Beweeg de muis aanwijzer over een regel in de grafiek om de bovenste 3 toepassingen te zien die aanvragen voor een bepaald tijdstip ontvangen. Toont ook een lijst met de toepassingen die aanvragen ontvangen en het aantal aanvragen voor de geselecteerde periode. <br><br>Klik op de grafiek om een zoek opdracht in Logboeken uit te voeren <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> waarin een gedetailleerd lijn diagram wordt weer gegeven van de server aanvragen per uur voor verschillende toepassingen. <br><br> Klik op een toepassing in de lijst om een logboek zoekopdracht uit te voeren voor <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> een lijst met aanvragen, grafieken voor aanvragen in de loop van de tijd en de duur van aanvragen en een lijst met antwoord codes voor aanvragen.   |
| Fouten: mislukte aanvragen per uur | Geeft een lijn diagram weer van mislukte toepassings aanvragen per uur. Beweeg de muis aanwijzer over de grafiek om de bovenste 3 toepassingen met mislukte aanvragen voor een bepaald tijdstip weer te geven. Toont ook een lijst met toepassingen met het aantal mislukte aanvragen voor elke toepassing. Klik op de grafiek om een logboek zoekopdracht uit te voeren voor <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> een gedetailleerd lijn diagram van mislukte toepassings aanvragen. <br><br>Klik op een item in de lijst om een logboek zoekopdracht uit te voeren voor <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code>  het weer geven van mislukte aanvragen, grafieken voor mislukte aanvragen gedurende een bepaalde periode en een lijst met mislukte antwoord codes voor aanvragen. |
| Uitzonde ringen: uitzonde ringen per uur | Toont een lijn diagram met uitzonde ringen per uur. Beweeg de muis aanwijzer over de grafiek om de bovenste 3 toepassingen met uitzonde ringen voor een bepaald tijdstip weer te geven. Toont ook een lijst met toepassingen met het aantal uitzonde ringen voor elk. Klik op de grafiek om een logboek zoekopdracht uit te voeren voor <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> een gedetailleerdere koppelings grafiek met uitzonde ringen. <br><br>Klik op een item in de lijst om een logboek zoekopdracht uit te voeren voor <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> een lijst met uitzonde ringen, grafieken voor uitzonde ringen in de loop van de tijd en mislukte aanvragen, en een lijst met uitzonderings typen.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Het Application Insights perspectief weer geven met zoeken in Logboeken

Wanneer u op een item in het dash board klikt, ziet u een Application Insights perspectief dat wordt weer gegeven in de zoek opdracht. Het perspectief bevat een uitgebreide visualisatie, gebaseerd op het type telemetrie dat is geselecteerd. Visualisatie-inhoud verandert dus voor verschillende typen telemetrie.

Wanneer u op een wille keurige plaats in de Blade toepassingen klikt, wordt het perspectief standaard **toepassingen** weer geven.

![Perspectief van Application Insights toepassingen](./media/app-insights-connector/applications-blade-drill-search.png)

In het perspectief ziet u een overzicht van de toepassing die u hebt geselecteerd.

Op de Blade voor de **Beschik baarheid** ziet u een andere perspectief weergave waarin de resultaten van webtesten en gerelateerde mislukte aanvragen worden weer gegeven.

![Perspectief Application Insights beschikbaarheid](./media/app-insights-connector/availability-blade-drill-search.png)

Wanneer u op een wille keurige plaats in de Blade **server aanvragen** of **fouten** klikt, worden de perspectief onderdelen gewijzigd om u een visualisatie te geven die betrekking heeft op aanvragen.

![Blade met Application Insights fouten](./media/app-insights-connector/server-requests-failures-drill-search.png)

Wanneer u op een wille keurige plaats in de Blade **uitzonde ringen** klikt, ziet u een visualisatie die is afgestemd op uitzonde ringen.

![Blade Application Insights uitzonde ringen](./media/app-insights-connector/exceptions-blade-drill-search.png)

Ongeacht of u op een van de **Application Insights-connector** dash board klikt, binnen de **Zoek** pagina zelf, wordt met elke query die Application Insights gegevens retourneert, het Application Insights perspectief weer gegeven. Als u bijvoorbeeld Application Insights gegevens bekijkt, wordt in een **&#42;** query ook het tabblad perspectief weer gegeven, zoals in de volgende afbeelding:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Perspectief onderdelen worden bijgewerkt op basis van de zoek query. Dit betekent dat u de resultaten kunt filteren met behulp van een zoek veld dat u de mogelijkheid biedt om de gegevens te bekijken:

- Al uw toepassingen
- Eén geselecteerde toepassing
- Een groep toepassingen

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Naar een app in de Azure Portal draaien

Application Insights-connector Blades zijn ontworpen om u in staat te stellen om naar de geselecteerde Application Insights-app te draaien *Wanneer u de Azure Portal gebruikt*. U kunt de oplossing gebruiken als een bewakings platform op hoog niveau waarmee u een app kunt oplossen. Wanneer u een mogelijk probleem ziet in een van de verbonden toepassingen, kunt u deze inzoomen in Log Analytics zoeken of u kunt rechtstreeks naar de Application Insights-app draaien.

Als u wilt draaien, klikt u op de weglatings tekens (**...**) die aan het einde van elke regel worden weer gegeven en selecteert u **openen in Application Insights**.

>[!NOTE]
>**Openen in Application Insights** is niet beschikbaar in de Azure Portal.

![Openen in Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Voor beeld: gecorrigeerde gegevens

Application Insights biedt een *[steekproef correctie](../app/sampling.md)* om het telemetrische verkeer te verminderen. Wanneer u steek proeven inschakelt voor uw Application Insights-app, krijgt u een beperkt aantal vermeldingen dat zowel in Application Insights als in Log Analytics is opgeslagen. Hoewel gegevens consistentie wordt bewaard op de **Application Insights-connector** pagina en perspectieven, moet u de voorbeeld gegevens voor uw aangepaste query's hand matig corrigeren.

Hier volgt een voor beeld van een voorbeeld correctie in een zoek opdracht in Logboeken:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

Het veld aantal in de **steek proef** is aanwezig in alle vermeldingen en toont het aantal gegevens punten dat de vermelding vertegenwoordigt. Als u steek proeven voor uw Application Insights-app inschakelt, is het **aantal samples** groter dan 1. Als u het werkelijke aantal vermeldingen wilt tellen dat door de toepassing wordt gegenereerd, moet u de velden voor het **aantal samples** optellen.

Steek proeven zijn alleen van invloed op het totale aantal vermeldingen dat door uw toepassing wordt gegenereerd. U hoeft geen steek proeven te corrigeren voor metrische velden, zoals **RequestDuration** of **AvailabilityDuration**  , omdat deze velden het gemiddelde voor weer gegeven vermeldingen tonen.

## <a name="input-data"></a>Invoergegevens

De oplossing ontvangt de volgende telemetriegegevens van gegevens van uw verbonden Application Insights-apps:

- Beschikbaarheid
- Uitzonderingen
- Aanvragen
- Pagina weergaven: voor uw werk ruimte om pagina weergaven te ontvangen, moet u uw apps configureren om die informatie te verzamelen. Zie [page views](../app/api-custom-events-metrics.md#page-views)voor meer informatie.
- Aangepaste gebeurtenissen: voor uw werk ruimte om aangepaste gebeurtenissen te ontvangen, moet u uw apps configureren om die informatie te verzamelen. Zie [track Event](../app/api-custom-events-metrics.md#trackevent)voor meer informatie.

Gegevens worden ontvangen door Log Analytics van Application Insights zodra deze beschikbaar wordt.

## <a name="output-data"></a>Uitvoergegevens

Er wordt een record met een *type* *ApplicationInsights* gemaakt voor elk type invoer gegevens. ApplicationInsights-records hebben eigenschappen die in de volgende secties worden weer gegeven:

### <a name="generic-fields"></a>Algemene velden

| Eigenschap | Beschrijving |
| --- | --- |
| Type | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Tijd van de record |
| ApplicationID | Instrumentatie sleutel van de Application Insights-app |
| ApplicationName | De naam van de Application Insights-app |
| RoleInstance | ID van de server host |
| DeviceType | Client apparaat |
| ScreenResolution |   |
| Continent | Continent waar de aanvraag vandaan komt |
| Land/regio | Land/regio waar de aanvraag vandaan komt |
| Staat | Provincie, provincie of land/regio waar de aanvraag vandaan komt |
| Plaats | Plaats of stad waar de aanvraag vandaan komt |
| isSynthetic | Geeft aan of de aanvraag is gemaakt door een gebruiker of door een automatische methode. True = geautomatiseerde methode of onwaar = door de gebruiker gegenereerd |
| SamplingRate | Het percentage van de telemetrie dat is gegenereerd door de SDK en dat naar de portal wordt verzonden. Bereik 0.0-100,0. |
| SampledCount | 100/(SamplingRate). Bijvoorbeeld 4 = &gt; 25% |
| IsAuthenticated | Waar of onwaar |
| OperationID | Items met dezelfde bewerkings-ID worden weer gegeven als gerelateerde items in de portal. Doorgaans de aanvraag-ID |
| ParentOperationID | ID van de bovenliggende bewerking |
| OperationName |   |
| SessionId | GUID voor een unieke identificatie van de sessie waarin de aanvraag is gemaakt |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Beschik bare velden

| Eigenschap | Beschrijving |
| --- | --- |
| TelemetryType | Beschikbaarheid |
| AvailabilityTestName | De naam van de webtest |
| AvailabilityRunLocation | Geografische bron van de HTTP-aanvraag |
| AvailabilityResult | Hiermee wordt het succes resultaat van de webtest aangegeven |
| AvailabilityMessage | Het bericht dat is gekoppeld aan de webtest |
| AvailabilityCount | 100/(sampling frequentie). Bijvoorbeeld 4 = &gt; 25% |
| DataSizeMetricValue | 1,0 of 0,0 |
| DataSizeMetricCount | 100/(sampling frequentie). Bijvoorbeeld 4 = &gt; 25% |
| AvailabilityDuration | Tijd, in milliseconden, van de duur van de webtest |
| AvailabilityDurationCount | 100/(sampling frequentie). Bijvoorbeeld 4 = &gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | Unieke GUID voor de webtest |
| AvailabilityTimestamp | Nauw keurige tijds tempel van de beschikbaarheids test |
| AvailabilityDurationMin | Voor de voorbeeld records bevat dit veld de minimale duur van de webtest (milliseconden) voor de weer gegeven gegevens punten |
| AvailabilityDurationMax | Voor de voorbeeld records bevat dit veld de maximum duur voor de webtest (milliseconden) voor de weer gegeven gegevens punten |
| AvailabilityDurationStdDev | Voor de voorbeeld records bevat dit veld de standaard afwijking tussen alle web-test duren (in milliseconden) voor de weer gegeven gegevens punten |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Uitzonderings velden

| Type | ApplicationInsights |
| --- | --- |
| TelemetryType | Uitzondering |
| ExceptionType | Type uitzonde ring |
| ExceptionMethod | De methode die de uitzonde ring maakt |
| ExceptionAssembly | Assembly bevat het Framework en de versie, evenals het open bare-sleutel token |
| ExceptionGroup | Type uitzonde ring |
| ExceptionHandledAt | Hiermee wordt het niveau aangegeven dat de uitzonde ring heeft afgehandeld |
| ExceptionCount | 100/(sampling frequentie). Bijvoorbeeld 4 = &gt; 25% |
| ExceptionMessage | Bericht van de uitzonde ring |
| ExceptionStack | Volledige stack van de uitzonde ring |
| ExceptionHasStack | Waar, als uitzonde ring een stack heeft |



### <a name="request-specific-fields"></a>Aanvraag afhankelijke velden

| Eigenschap | Beschrijving |
| --- | --- |
| Type | ApplicationInsights |
| TelemetryType | Aanvraag |
| ResponseCode | HTTP-antwoord verzonden naar client |
| RequestSuccess | Geeft aan of de bewerking is geslaagd of mislukt. Waar of onwaar. |
| RequestID | ID voor een unieke identificatie van de aanvraag |
| Aanvraagnaam | GET/POST + URL-basis |
| RequestDuration | De tijd, in seconden, van de aanvraag duur |
| URL | De URL van de aanvraag bevat geen host |
| Host | Webserver host |
| URLBase | Volledige URL van de aanvraag |
| ApplicationProtocol | Type protocol dat door de toepassing wordt gebruikt |
| RequestCount | 100/(sampling frequentie). Bijvoorbeeld 4 = &gt; 25% |
| RequestDurationCount | 100/(sampling frequentie). Bijvoorbeeld 4 = &gt; 25% |
| RequestDurationMin | Voor de voorbeeld records bevat dit veld de minimale aanvraag duur (milliseconden) voor de weer gegeven gegevens punten. |
| RequestDurationMax | Voor de voorbeeld records bevat dit veld de maximale aanvraag duur (in milliseconden) voor de weer gegeven gegevens punten |
| RequestDurationStdDev | Voor de voorbeeld records bevat dit veld de standaard afwijking tussen alle aanvraag duur (milliseconden) voor de weer gegeven gegevens punten |

## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken

Voor deze oplossing is geen aantal Zoek opdrachten in logboeken weer gegeven op het dash board. Voor beelden van zoek opdrachten in Logboeken met beschrijvingen worden echter weer gegeven in de sectie [Application Insights-connector gegevens weer geven](#view-application-insights-connector-information) .

## <a name="removing-the-connector-with-powershell"></a>De connector verwijderen met Power shell
In de OMS-Portal is het niet mogelijk om bestaande verbindingen te configureren en te verwijderen vanuit de portal. U kunt bestaande verbindingen met het volgende Power shell-script verwijderen. U moet de eigenaar of bijdrager zijn van de werk ruimte en de lezer van Application Insights resource om deze bewerking uit te voeren.

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzAccount
Set-AzContext -SubscriptionId $Subscription_app
$AIApp = Get-AzApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzContext -SubscriptionId $Subscription_workspace
Remove-AzOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

U kunt een lijst met toepassingen ophalen met behulp van het volgende Power shell-script dat een REST API aanroep aanroept. 

```powershell
Connect-AzAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
Voor dit script is een Bearer-verificatie token vereist voor verificatie op basis van Azure Active Directory. Een manier om dit token op te halen, is het gebruik van een artikel op de [Documentatie site van rest API](/rest/api/loganalytics/datasources/createorupdate). Klik op **Probeer het** en meld u aan bij uw Azure-abonnement. U kunt het Bearer-token kopiëren uit de preview van de **aanvraag** , zoals wordt weer gegeven in de volgende afbeelding.


![Bearer-token](media/app-insights-connector/bearer-token.png)


U kunt ook een lijst met toepassingen ophalen een logboek query gebruiken:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Volgende stappen

- Gebruik [Zoeken in Logboeken](./log-query-overview.md) om gedetailleerde informatie weer te geven voor uw Application Insights-apps.