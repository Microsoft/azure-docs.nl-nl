---
title: 'Quickstart: Metrics Advisor-webportal'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de Metrics Advisor-webportal.
services: cognitive-services
author: mrbullwinkle
ms.author: mbullwin
manager: nitinme
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.custom:
- mode-portal
ms.openlocfilehash: aaeaf92df140019db32c326199c9614fd8f5ae37
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531801"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>Quickstart: Uw eerste metrische gegevens controleren met behulp van de webportal

Wanneer u een Metrics Advisor-instantie inricht, kunt u de API's en de webwerkruimte gebruiken om met de service te werken. De webwerkruimte biedt u een eenvoudige manier om snel aan de slag te gaan met de service. Het biedt ook een visuele manier om instellingen te configureren, uw model aan te passen en een hoofdoorzaakanalyse uit te voeren. 

* Uw metrische gegevens onboarden
* Uw metrische gegevens en visualisaties weergeven
* Detectieconfiguraties afstemmen
* Diagnostische inzichten verkennen
* Anomaliewaarschuwingen maken en u er op abonneren

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* Wanneer u uw Azure-abonnement hebt, kunt u <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Een Metrics Advisor-resource maken"  target="_blank">een Metrics Advisor-resource maken</a> in de Azure-portal om uw Metrics Advisor-instantie te implementeren.  

    
> [!TIP]
> * Het kan 10 tot 30 minuten duren voordat uw Metrics Advisor-resources zijn geïmplementeerd. Klik op **Naar de resource gaan** zodra de implementatie is voltooid.
> * Als u de REST API wilt gebruiken om te werken met de service, hebt u de sleutel en het eindpunt nodig van de resource die u maakt. U vindt deze op het tabblad  **Sleutels en eindpunten** in de resource die u hebt gemaakt.

Dit document maakt gebruik van een SQL-voorbeelddatabase voor het maken van uw eerste monitor.

## <a name="sign-in-to-your-workspace"></a>Aanmelden bij uw werkruimte

Nadat de resource is gemaakt, meldt u zich aan bij de [Metrics Advisor-portal](https://go.microsoft.com/fwlink/?linkid=2143774). Selecteer uw werkruimte om te beginnen met het bewaken van uw metrische gegevens. 
 
Op dit moment kunt u één Metrics Advisor-resource maken voor elke beschikbare regio. U kunt op elk gewenst moment schakelen tussen werkruimten in de Metrics Advisor-portal.


## <a name="onboard-time-series-data"></a>Tijdreeksgegevens onboarden

Metrics Advisor bevat connectors voor verschillende gegevensbronnen, zoals SQL Database, Azure Data Explorer en Azure Table Storage. De stappen voor het verbinden van gegevens zijn vrijwel hetzelfde voor alle connectors, hoewel sommige configuratieparameters kunnen variëren. Zie [uw gegevens uit verschillende bronnen verbinden](../data-feeds-from-different-sources.md) voor de vereiste parameters voor specifieke gegevensbronnen.

In deze quickstart wordt een SQL-database als voorbeeld gebruikt. U kunt ook uw eigen gegevens opnemen met behulp van deze stappen.

Meld u aan met uw Active Directory-account om aan de slag te gaan met uw Metrics Advisor-werkruimte. Selecteer op de landingspagina uw **map**, **abonnement** en de zojuist gemaakte **werkruimte** en klik vervolgens op **Aan de slag**. Als de hoofdpagina van de workload is geladen, selecteert u **Gegevensfeed toevoegen** in het menu aan de linkerkant.

### <a name="data-schema-requirements-and-configuration"></a>Vereisten en configuratie voor gegevensschema

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings"></a>Verbindingsinstellingen configureren

> [!TIP]
> Zie [hoe u gegevensfeeds kunt toevoegen](../how-tos/onboard-your-data.md) voor meer informatie over de beschikbare parameters.

Voeg de gegevensfeed toe door verbinding te maken met de gegevensbron van uw tijdreeks. Begin met het selecteren van de volgende parameters:

* **Brontype**: Het type gegevensbron waarin uw tijdreeksgegevens zijn opgeslagen.
* **Granulariteit**: Het interval tussen opeenvolgende gegevenspunten in de tijdreeksgegevens, bijvoorbeeld jaarlijks, maandelijks of dagelijks. Het laagste interval dat wordt ondersteund, is 60 seconden.
* **Gegevens opnemen vanaf (UTC)** : De begintijd voor het eerste tijdstempel dat moet worden opgenomen. 


Geef vervolgens de **verbindingsreeks** met de referenties voor uw gegevensbron en een aangepaste **query** op. De query wordt gebruikt om de gegevens op te geven die moeten worden opgenomen en naar het vereiste schema moeten worden geconverteerd.

[!INCLUDE [query requirements](../includes/query-requirements.md)]

:::image type="content" source="../media/connection-settings.png" alt-text="Verbindingsinstellingen" lightbox="../media/connection-settings.png":::


### <a name="verify-the-connection-and-load-the-data-schema"></a>De verbinding controleren en het gegevensschema laden

Nadat de verbindingsreeks en de queryreeks zijn gemaakt, selecteert u **Controleren en schema ophalen** om de verbinding te controleren en de query uit te voeren om uw gegevensschema van de gegevensbron op te halen. Normaal gesproken duurt dit slechts enkele seconden, afhankelijk van de verbinding met de gegevensbron. Als er een fout optreedt bij deze stap, controleert u het volgende:

1. Uw verbindingsreeks en query zijn juist.
2. Uw Metrics Advisor-instantie kan verbinding maken met de gegevensbron als er firewallinstellingen zijn.

### <a name="schema-configuration"></a>Schemaconfiguratie

Zodra het gegevensschema is geladen en wordt weergegeven zoals hieronder, selecteert u de juiste velden.


|Selectie  |Beschrijving  |Notities  |
|---------|---------|---------|
|**Tijdstempel**     | Het tijdstempel van een gegevenspunt. Als u niets invult, wordt in Metrics Advisor het tijdstempel van opname van de gegevenspunten gebruikt. Voor elke gegevensfeed kunt u maximaal één kolom als tijdstempel opgeven.        | Optioneel. Moet worden opgegeven met maximaal één kolom.       |
|**Measure**     |  De numerieke waarden in de gegevensfeed. Voor elke gegevensfeed kunt u meerdere metingen opgeven, maar u moet ten minste één kolom als meting selecteren.        | Moet worden opgegeven met minimaal één kolom.        |
|**Dimensie**     | Categorische waarden. Een combinatie van verschillende waarden identificeert een bepaalde tijdreeks met één dimensie, bijvoorbeeld: land, taal, tenant. U kunt geen enkele kolom of een willekeurig aantal kolommen als dimensies selecteren. Opmerking: als u een niet-tekenreekskolom als dimensie selecteert, pas dan op voor dimensie-explosie. | Optioneel.        |
|**Negeren**     | De geselecteerde kolom negeren.        |         |


:::image type="content" source="../media/schema-configuration.png" alt-text="Schemaconfiguratie" lightbox="../media/schema-configuration.png":::

### <a name="automatic-roll-up-settings"></a>Instellingen voor automatisch verzamelen

> [!IMPORTANT]
> Als u **hoofdoorzaakanalyse** en andere diagnostische mogelijkheden wilt inschakelen, moet u de instelling voor automatisch verzamelen configureren. Eenmaal ingeschakeld, kunnen de instellingen voor het automatisch verzamelen niet meer worden gewijzigd.

Met Metrics Advisor kan tijdens de gegevensopname automatisch aggregatie (SUM/MAX/MIN...) worden uitgevoerd op elke dimensie. Vervolgens wordt een hiërarchie samengesteld die wordt gebruikt in hoofdoorzaakanalyses en andere diagnostische functies. Zie [Instellingen voor automatisch verzamelen](../how-tos/onboard-your-data.md#automatic-roll-up-settings) voor meer informatie.

Geef een aangepaste naam op voor gegevensfeed op. Deze naam wordt weergegeven in uw werkruimte. Klik op **Verzenden**. 

## <a name="tune-detection-configuration"></a>Detectieconfiguratie afstemmen

Nadat de gegevensfeed is toegevoegd, probeert Metrics Advisor metrische gegevens op te nemen vanaf de opgegeven begindatum. Het duurt enige tijd voordat gegevens volledig zijn opgenomen. U kunt de opnamestatus bekijken door te klikken op **Voortgang van opname** boven aan de pagina van de gegevensfeed. Als de gegevens zijn opgenomen, wordt er detectie op uitgevoerd door Metrics Advisor en wordt de bron continu gecontroleerd op nieuwe gegevens.

Na de detectie kunt u klikken op de metrische gegevens in de feed om de **detailpagina van de metrische gegevens** weer te geven. Op deze pagina kunt u het volgende doen: 
- Visualisaties van alle onderliggende tijdreekssegmenten van het metrische gegeven bekijken
- Detectieconfiguratie bijwerken om de verwachte resultaten te verkrijgen
- Melding voor gedetecteerde anomalieën instellen

:::image type="content" source="../media/metric-details.png" alt-text="Details van metrische gegevens" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>De diagnostische inzichten weergeven

Na het afstemmen van de detectieconfiguratie moeten gevonden anomalieën overeenkomen met daadwerkelijke anomalieën in uw gegevens. Met Metrics Advisor worden analyses op multidimensionale metrische gegevens uitgevoerd, zoals afwijkende clusters, incidentcorrelatie en hoofdoorzaakanalyse. Gebruik deze functies voor het analyseren en diagnosticeren van incidenten in uw gegevens.

Als u de diagnostische inzichten wilt weergeven, klikt u op de rode puntjes in de visualisaties van de tijdreeks. Deze rode puntjes vertegenwoordigen de gedetecteerde anomalieën. Er wordt een venster weergegeven met een koppeling naar de incidentanalysepagina. 

:::image type="content" source="../media/incident-link.png" alt-text="Koppeling naar incident" lightbox="../media/incident-link.png":::

Als u op de koppeling klikt, wordt u gedraaid naar de incidentanalysepagina voor analyse van de betreffende anomalie, met een aantal diagnose-inzichten. Bovenaan ziet u statistische informatie over het incident, zoals de **ernst**, **betrokken anomalieën** en de betreffende **begin-** en **eindtijd**. 

Vervolgens ziet u de bovenliggende anomalie van het incident en het automatische advies voor de hoofdoorzaak. Dit automatische advies voor de hoofdoorzaak wordt gegenereerd op basis van een analyse van de incidentstructuur van alle gerelateerde anomalieën, waaronder de afwijking, de distributie en de bijdrage aan de bovenliggende anomalieën. 

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Diagnostische gegevens voor incident" lightbox="../media/incident-diagnostic.png":::

Op basis hiervan kunt u al een duidelijk beeld krijgen van wat er gebeurt, de impact van het incident, en de meest waarschijnlijke hoofdoorzaak. Zo kan er direct actie worden ondernomen om het incident zo spoedig mogelijk op te lossen. 

Maar u kunt ook draaien naar meer diagnostische inzichten met extra functies om in te zoomen op anomalieën per dimensie, vergelijkbare anomalieën weer te geven en metrische gegevens met elkaar te vergelijken. Zie de [instructies voor het diagnosticeren van een incident](../how-tos/diagnose-incident.md) voor meer informatie. 

## <a name="get-notified-when-new-anomalies-are-found"></a>Een melding ontvangen wanneer er nieuwe anomalieën worden gevonden

Als u wilt worden gewaarschuwd wanneer er een anomalie in uw gegevens wordt gedetecteerd, kunt u een abonnement maken voor een of meer van uw metrische gegevens. In Metrics Advisor worden hooks gebruikt voor het verzenden van waarschuwingen. Er worden drie typen hooks ondersteund: e-mailhooks, webhooks en Azure DevOps-hooks. Als voorbeeld gebruiken we een webhook. 

### <a name="create-a-web-hook"></a>Een webhook maken

Een webhook is het beginpunt voor het ophalen van een anomalie die programmatisch is gevonden door de Metrics Advisor-service, die een door de gebruiker opgegeven API aanroept wanneer een waarschuwing wordt geactiveerd. Raadpleeg de sectie **Een hook maken** in [Instructies: Waarschuwingen configureren en meldingen ontvangen met behulp van een hook](../how-tos/alerts.md#create-a-hook) voor meer informatie over het maken van een hook. 

### <a name="configure-alert-settings"></a>Waarschuwingsinstellingen configureren

Na het maken van een hook bepaalt een waarschuwingsinstelling hoe en welke waarschuwingsmeldingen moeten worden verzonden. U kunt meerdere waarschuwingsinstellingen instellen voor elke metriek. Er zijn twee belangrijke instellingen: **Waarschuwing voor**, die aangeeft welke anomalieën moeten worden opgenomen, en **Filteropties voor anomalieën**, waarmee wordt gedefinieerd welke anomalieën in de waarschuwing moeten worden opgenomen. Zie de sectie **Waarschuwingsinstellingen toevoegen of bewerken** in [Instructies: Waarschuwingen configureren en meldingen ontvangen met behulp van een hook](../how-tos/alerts.md#add-or-edit-alert-settings) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

- [Uw gegevensfeeds onboarden](../how-tos/onboard-your-data.md)
    - [Gegevensfeeds beheren](../how-tos/manage-data-feeds.md)
    - [Configuraties voor verschillende gegevens bronnen](../data-feeds-from-different-sources.md)
- [De REST API of clientbibliotheken gebruiken](./rest-api-and-client-library.md)
- [Metrische gegevens configureren en detectieconfiguratie afstemmen](../how-tos/configure-metrics.md)
