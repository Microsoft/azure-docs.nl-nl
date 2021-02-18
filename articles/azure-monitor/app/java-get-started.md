---
title: 'Snelstartgids: analyse van Java-Web-apps met Azure-toepassing Insights'
description: 'Toepassingsprestaties bewaken met Application Insights voor Java-web-apps. '
ms.topic: conceptual
ms.date: 11/22/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: cdad5a0ec158f216a63ae7e1e2abc10d2841b3bc
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593738"
---
# <a name="quickstart-get-started-with-application-insights-in-a-java-web-project"></a>Quickstart: Aan de slag met Application Insights in een Java-webproject


> [!CAUTION]
> Vanaf november 2020 wordt voor het bewaken van Java-toepassingen de automatische instrumentatie aanbevolen met de Azure Monitor Application Insights Java 3,0-agent. Zie [Application Insights Java 3,0-agent](./java-in-process-agent.md)voor meer informatie over hoe u aan de slag kunt gaan.

In deze Snelstartgids gebruikt u Application Insights SDK om de aanvraag te instrumenteren, afhankelijkheden bij te houden en prestatie meter items te verzamelen, prestatie problemen en uitzonde ringen te diagnosticeren en code te schrijven om te volgen wat gebruikers met uw app doen.

Application Insights is een uitbreidbare analyseservice voor webontwikkelaars die u helpt de prestaties en het gebruik van uw live-toepassing te begrijpen. Application Insights biedt ondersteuning voor Java-apps die in Linux, Unix of Windows worden uitgevoerd.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Een werkende Java-toepassing.

## <a name="get-an-application-insights-instrumentation-key"></a>Een Application Insights-instrumentatiesleutel ophalen

> [!IMPORTANT]
> Nieuwe Azure-regio's **vereisen** het gebruik van verbindings reeksen in plaats van instrumentatie sleutels. Met de [verbindings reeks](./sdk-connection-string.md?tabs=java) wordt de resource geïdentificeerd waaraan u de telemetriegegevens wilt koppelen. U kunt ook de eind punten wijzigen die door de resource worden gebruikt als een bestemming voor uw telemetrie. U moet de connection string kopiëren en toevoegen aan de code van uw toepassing of aan een omgevings variabele.
1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Maak in de Azure Portaleen Application Insights-resource. Stel het toepassingstype in op Java-webtoepassing.

3. Zoek de instrumentatiesleutel van de nieuwe resource. U moet deze sleutel zo dadelijk in de code van uw project plakken.

    ![Op Eigenschappen klikken in het overzicht van de nieuwe resource en de instrumentatiesleutel kopiëren](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>De Application Insights-SDK voor Java toevoegen aan uw project

*Kies het project type.*

# <a name="maven"></a>[Maven](#tab/maven)

Als uw project al is ingesteld voor het gebruik van Maven voor Build, voegt u de volgende code samen in uw *pom.xml* -bestand.

Vervolgens vernieuwt u de projectafhankelijkheden om de binaire bestanden te downloaden.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.6.2</version>
      </dependency>
    </dependencies>
```

# <a name="gradle"></a>[Gradle](#tab/gradle)

Als uw project al is ingesteld voor het gebruik van Gradle voor Build, voegt u de volgende code samen in uw *Build. Gradle* -bestand.

Vervolgens vernieuwt u de projectafhankelijkheden om de binaire bestanden te downloaden.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.6.2'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

---

### <a name="questions"></a>Vragen
* *Wat is de relatie tussen de `-web-auto` - `-web` en- `-core` onderdelen?*
  * `applicationinsights-web-auto` geeft u metrische gegevens die het aantal HTTP servlet-aanvragen en-reactie tijden bijhouden door de Application Insights servlet-filter tijdens runtime automatisch te registreren.
  * `applicationinsights-web` voorziet u ook van metrische gegevens voor het bijhouden van het aantal servlet en de reactie tijden van HTTP-aanvragen, maar vereist hand matige registratie van het Application Insights servlet-filter in uw toepassing.
  * `applicationinsights-core` geeft u alleen de bare API, bijvoorbeeld als uw toepassing niet op servlet is gebaseerd.
  
* *Hoe moet ik de SDK bijwerken naar de nieuwste versie?*
  * Vanaf november 2020 wordt voor het bewaken van Java-toepassingen de automatische instrumentatie aanbevolen met de Azure Monitor Application Insights Java 3,0-agent. Zie [Application Insights Java 3,0-agent](./java-in-process-agent.md)voor meer informatie over hoe u aan de slag kunt gaan.

## <a name="add-an-applicationinsightsxml-file"></a>Een *ApplicationInsights.xml* -bestand toevoegen
Voeg *ApplicationInsights.xml* toe aan de map resources in uw project of zorg ervoor dat deze is toegevoegd aan het pad van de implementatie klasse van uw project. Kopieer de volgende XML-code naar het bestand.

Vervang de instrumentatie sleutel door de toets die u hebt ontvangen van de Azure Portal.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">

   <!-- The key from the portal: -->
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>

   <!-- HTTP request component (not required for bare API) -->
   <TelemetryModules>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
   </TelemetryModules>

   <!-- Events correlation (not required for bare API) -->
   <!-- These initializers add context data to each event -->
   <TelemetryInitializers>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
   </TelemetryInitializers>

</ApplicationInsights>
```

Het configuratie bestand kan optioneel zijn op elke locatie die toegankelijk is voor uw toepassing.  De eigenschap System `-Dapplicationinsights.configurationDirectory` geeft de map op die *ApplicationInsights.xml* bevat. Bijvoorbeeld: een configuratiebestand in `E:\myconfigs\appinsights\ApplicationInsights.xml` wordt geconfigureerd met eigenschap `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* De instrumentatiesleutel wordt samen met alle telemetrie-items verzonden en instrueert Application Insights om deze in de resource weer te geven.
* Het onderdeel voor de HTTP-aanvraag is optioneel. Het verzendt automatisch telemetrie over aanvragen en reactietijden naar de portal.
* Correlatie tussen gebeurtenissen is een aanvulling op het onderdeel voor de HTTP-aanvraag. Er wordt een id toegewezen aan elke aanvraag die door de server wordt ontvangen. Vervolgens wordt deze id als eigenschap toegevoegd aan elk item van telemetrie als de eigenschap Operation.Id. Op deze manier kunt u correlaties zichtbaar maken tussen de telemetrie die aan elke aanvraag is gekoppeld. Dit doet u door een filter in te stellen in [Diagnostische gegevens doorzoeken][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Andere manieren om de instrumentatiesleutel in te stellen
De Application Insights-SDK zoekt in deze volgorde naar de sleutel:

1. Systeem eigenschap:-DAPPINSIGHTS_INSTRUMENTATIONKEY = your_ikey
2. Omgevings variabele: APPINSIGHTS_INSTRUMENTATIONKEY
3. Configuratie bestand: *ApplicationInsights.xml*

U kunt de instrumentatiesleutel ook [instellen in code](./api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>Agent toevoegen

[Installeer de Java-Agent voor het](java-agent.md) vastleggen van uitgaande HTTP-aanroepen, JDBC-query's, toepassings logboeken en betere bewerkings namen.

## <a name="run-your-application"></a>Uw toepassing uitvoeren
Voer uw app uit in de foutopsporingsmodus op uw ontwikkelcomputer of publiceer de app op uw server.

## <a name="view-your-telemetry-in-application-insights"></a>Uw telemetrie in Application Insights weergeven
Ga terug naar uw Application Insights-resource in [Microsoft Azure-Portal](https://portal.azure.com).

Gegevens van HTTP-aanvragen worden weergegeven op de overzichtsblade. (Als dit niet het geval is, wacht u een paar seconden en klikt u vervolgens op Vernieuwen.)

![Scherm opname van voorbeeld gegevens van overzicht](./media/java-get-started/overview-graphs.png)

[Meer informatie over metrische gegevens.][metrics]

Klik in een grafiek voor gedetailleerdere cumulatieve metrische gegevens.

![Deel venster met Application Insights fouten met grafieken](./media/java-get-started/006-barcharts.png)

### <a name="instance-data"></a>Gegevens van exemplaren
Klik op een specifiek aanvraagtype om de afzonderlijke exemplaren weer te geven.

![Inzoomen op een specifieke voorbeeld weergave](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analyse: krachtige querytaal
Naarmate u meer gegevens verzamelt, kunt u query's uitvoeren voor zowel het samenvoegen van gegevens als het zoeken naar afzonderlijke exemplaren.  [Analyse](../logs/log-query-overview.md) is een krachtig hulpprogramma om inzicht te krijgen in prestaties en gebruik, en om diagnoses uit te voeren.

![Voorbeeld van het hulpprogramma Analyse](./media/java-get-started/0025.png)

## <a name="install-your-app-on-the-server"></a>Uw app installeren op de server
Publiceer nu uw app op de server, geef de app vrij voor gebruik en bekijk de telemetrische gegevens die in de portal binnenkomen.

* Controleer of de firewall het verzenden van telemetrie door uw app naar deze poorten toestaat:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Als uitgaand verkeer via een firewall moet worden gerouteerd, definieert u de systeemeigenschappen `http.proxyHost` en `http.proxyPort`.

* Installeer op Windows-servers:

  * [Microsoft Visual C++ Redistributable](https://www.microsoft.com/download/details.aspx?id=40784)

    (Dit onderdeel schakelt prestatiemeteritems in.)

## <a name="azure-app-service-aks-vms-config"></a>Azure App Service, AKS, Vm's configuratie

De beste en eenvoudigste benadering voor het bewaken van uw toepassingen die worden uitgevoerd op een van de Azure-resource providers is het gebruik van Application Insights automatische instrumentatie via [Java 3,0-agent](./java-in-process-agent.md).


## <a name="exceptions-and-request-failures"></a>Uitzonderingen en mislukte aanvragen
Niet-verwerkte uitzonde ringen en mislukte aanvragen worden automatisch door het webfilter van Application Insights verzameld.

Als u gegevens over andere uitzonde ringen wilt verzamelen, kunt u [aanroepen naar trackException () in uw code invoegen][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Methodeaanroepen en externe afhankelijkheden bewaken
[Installeer de Java-agent](java-agent.md) om gespecificeerde interne methoden en oproepen via JDBC vast te leggen, inclusief timinggegevens.

En voor automatische bewerkings naam.

## <a name="w3c-distributed-tracing"></a>Gedistribueerde W3C-tracering

De Application Insights Java SDK ondersteunt nu [W3C-gedistribueerde tracering](https://w3c.github.io/trace-context/).

De configuratie van de inkomende SDK wordt verder uitgelegd in ons artikel over [correlatie](correlation.md).

De uitgaande SDK-configuratie wordt gedefinieerd in het [AI-Agent.xml](java-agent.md) -bestand.

## <a name="performance-counters"></a>Prestatiemeteritems
Open **onderzoek**, **metrische gegevens** om een aantal prestatie meter items weer te geven.

![Scherm afbeelding van het deel venster metrische gegevens met het proces eigen bytes geselecteerd](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Het verzamelen van prestatiemeteritems aanpassen
Als u het verzamelen van de standaard set prestatie meter items wilt uitschakelen, voegt u de volgende code toe onder het hoofd knooppunt van het *ApplicationInsights.xml* -bestand:

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Verzamelen van aanvullende prestatiemeteritems
U kunt opgeven dat er aanvullende prestatiemeteritems moeten worden verzameld.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>JMX-tellers (weergegeven door de virtuele Java-machine)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` - De naam die wordt weergegeven in de Application Insights-portal.
* `objectName` - De JMX-objectnaam.
* `attribute` - Het kenmerk van de JMX-objectnaam dat moet worden opgehaald
* `type` (optioneel)-het type kenmerk van het JMX-object:
  * Standaard: een eenvoudig type, zoals int of long.
  * `composite`: de gegevens van de prestatiemeteritems hebben de indeling 'Attribute.Data'
  * `tabular`: de gegevens van de prestatiemeteritems hebben de vorm van een rij in een tabel

#### <a name="windows-performance-counters"></a>Windows-prestatiemeteritems
Elk [Windows-prestatiemeteritem](/windows/win32/perfctrs/performance-counters-portal) maakt deel uit van een categorie (net zoals een veld deel uitmaakt van een klasse). Categorieën kunnen globaal zijn, maar ook genummerde of benoemde exemplaren hebben.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName: de naam die wordt weergegeven in de Application Insights-portal.
* categorynaam: de prestatiemeteritemcategorie (prestatie-object) waaraan dit prestatiemeteritem is gekoppeld.
* counterName: de naam van het prestatiemeteritem.
* instanceName: de naam van het exemplaar van de prestatiemeteritemcategorie, of een lege tekenreeks ("") als de categorie slechts één exemplaar bevat. Als de categorienaam Process is en het prestatiemeteritem dat u wilt verzamelen, afkomstig is uit het huidige JVM-proces waarop uw app wordt uitgevoerd, specificeert u `"__SELF__"`.

### <a name="unix-performance-counters"></a>Unix-prestatiemeteritems
* [Installeer collectd met de Application Insights-invoegtoepassing](java-collectd.md) om een scala aan systeem- en netwerkgegevens op te halen.

## <a name="get-user-and-session-data"></a>Gebruikers- en sessiegegevens ophalen
U verzendt nu telemetrie vanaf de webserver. Wilt u echt een volledig inzicht in uw toepassing, dan kunt u nog meer bewakingsopties toevoegen:

* [Voeg telemetrie toe aan uw webpagina's][usage] voor het bewaken van paginaweergaven en metrische gegevens over gebruikers.
* [Stel webtests in][availability] om te controleren of de toepassing live en responsief blijft.

## <a name="send-your-own-telemetry"></a>Uw eigen telemetrie verzenden
Nu u de SDK hebt geïnstalleerd, kunt u de API gebruiken voor het verzenden van uw eigen telemetrie.

* [Houd aangepaste gebeurtenissen en metrische gegevens bij][api] voor meer informatie over wat gebruikers met uw toepassing doen.
* [Doorzoek gebeurtenissen en logboeken][diagnostic] om problemen beter te kunnen analyseren.

## <a name="availability-web-tests"></a>Webtests voor beschikbaarheid
Application Insights kan uw website regelmatig testen om te controleren of deze actief is en goed reageert.

[Meer informatie over het instellen van Beschik baarheid van webtesten.][availability]

## <a name="questions-problems"></a>Vragen? Problemen?
[Problemen met Java oplossen](java-troubleshoot.md)

## <a name="next-steps"></a>Volgende stappen
* [Afhankelijkheidsaanroepen bewaken](java-agent.md)
* [Unix-prestatiemeteritems bewaken](java-collectd.md)
* Voeg [bewaking toe aan uw webpagina's](javascript.md) om de laadtijden, AJAX-aanroepen en browseruitzonderingen te bewaken.
* Typ [aangepaste telemetrie](./api-custom-events-metrics.md) om het gebruik in de browser of op de server bij te houden.
* Gebruik  [analyses](../logs/log-query-overview.md) voor krachtige query's via telemetrie van uw app
* Voor meer informatie gaat u naar [Azure voor Java-ontwikkelaars](/java/azure).

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#trackexception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../essentials/metrics-charts.md
[usage]: javascript.md