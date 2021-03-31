---
title: Problemen met Application Insights in een Java-webproject oplossen
description: 'Probleemoplossings handleiding: Live java-apps bewaken met Application Insights.'
ms.topic: conceptual
ms.date: 03/14/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: df1f58418452ecafacddd013ab815ebca5cb8a35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100097548"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java-sdk"></a>Probleem oplossing en Q en A voor Application Insights voor Java SDK

> [!IMPORTANT]
> De aanbevolen benadering voor het bewaken van Java-toepassingen is het gebruik van de automatische instrumentatie zonder de code te wijzigen. Volg de richt lijnen voor [Application Insights Java 3,0-agent](./java-in-process-agent.md).

Vragen of problemen met [Azure-toepassing inzichten in Java][java]? Hier volgen enkele tips.

## <a name="build-errors"></a>Build-fouten
**Bij een eclips-of IntelliJ-idee wordt bij het toevoegen van de Application Insights SDK via maven of Gradle de validatie fouten build of checksum weer geven.**

* Als het afhankelijkheids `<version>` element een patroon met Joker tekens (bijvoorbeeld Maven) `<version>[2.0,)</version>` of (Gradle) `version:'2.+'` ) gebruikt, probeert u een specifieke versie op te geven in plaats van op een manier `2.6.2` .

## <a name="no-data"></a>Geen gegevens
**Ik heb Application Insights toegevoegd en mijn app uitgevoerd, maar ik heb nooit gegevens in de portal gezien.**

* Wacht een minuut en klik op vernieuwen. De grafieken worden regel matig vernieuwd, maar u kunt ook hand matig vernieuwen. Het Vernieuwings interval is afhankelijk van het tijds bereik van de grafiek.
* Controleer of er een instrumentatie sleutel is gedefinieerd in het ApplicationInsights.xml bestand (in de map resources in uw project) of is geconfigureerd als omgevings variabele.
* Controleer of er zich geen `<DisableTelemetry>true</DisableTelemetry>` knoop punt in het XML-bestand bevindt.
* In uw firewall moet u mogelijk TCP-poorten 80 en 443 voor uitgaand verkeer naar dc.services.visualstudio.com openen. Bekijk de [volledige lijst met Firewall uitzonderingen](./ip-addresses.md)
* Ga in het Microsoft Azure start bord naar de kaart status van de service. Als er enkele waarschuwings vermeldingen zijn, wacht u totdat ze zijn teruggekeerd naar OK. Sluit vervolgens de Blade Application Insights toepassing en open deze opnieuw.
* [Schakel logboek registratie](#debug-data-from-the-sdk) in door een element toe te voegen `<SDKLogger />` onder het hoofd knooppunt in het ApplicationInsights.xml-bestand (in de map resources in uw project) en te controleren op vermeldingen die zijn voorafgegaan door AI: info/waarschuwing/fout voor verdachte Logboeken. 
* Controleer of het juiste ApplicationInsights.xml bestand door de Java-SDK is geladen door te kijken naar de uitvoer berichten van de console voor een configuratie bestand is gevonden.
* Als het configuratie bestand niet wordt gevonden, controleert u de uitvoer berichten om te zien waar het configuratie bestand wordt doorzocht en zorgt u ervoor dat de ApplicationInsights.xml zich op een van deze zoek locaties bevindt. Als vuist regel kunt u het configuratie bestand in de buurt van de Application Insights SDK-potten plaatsen. Bijvoorbeeld: in Tomcat is dit de map WEB-INF/classes. Tijdens de ontwikkeling kunt u ApplicationInsights.xml in de map resources van uw webproject plaatsen.
* Raadpleeg ook de [pagina met github-problemen](https://github.com/microsoft/ApplicationInsights-Java/issues) voor bekende problemen met de SDK.
* Zorg ervoor dat u dezelfde versie van Application Insights-kern-, Web-, agent-en logboek registratie-toevoegers gebruikt om problemen met versie conflicten te voor komen.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Ik heb de gegevens weer gegeven, maar deze zijn gestopt
* Hebt u uw maandelijkse quotum van gegevens punten bereikt? Open instellingen/quotum en prijzen voor meer informatie. Als dat het geval is, kunt u uw abonnement upgraden of extra capaciteit betalen. Zie het [prijs schema](https://azure.microsoft.com/pricing/details/application-insights/).
* Hebt u onlangs uw SDK bijgewerkt? Zorg ervoor dat er alleen unieke SDK-potten aanwezig zijn in de projectmap. Er mogen niet twee verschillende versies van SDK aanwezig zijn.
* Gaat u naar de juiste AI-resource? Zorg dat de iKey van uw toepassing overeenkomt met de resource waar u telemetrie verwacht. Ze moeten hetzelfde zijn.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Ik zie niet alle gegevens die ik verwacht
* Open de pagina gebruik en geschatte kosten en controleer of er [steek proeven](./sampling.md) worden uitgevoerd. (100% verzen ding houdt in dat steek proeven niet worden uitgevoerd.) De Application Insights-service kan zo worden ingesteld dat er slechts een fractie van de telemetrie van uw app wordt ontvangen. Dit helpt u binnen uw maandelijkse quota van telemetrie te blijven.
* Hebt u SDK-steek proeven ingeschakeld? Als dat het geval is, worden de gegevens als steek proef bemonsterd op basis van de snelheid die voor alle toepasselijke typen is opgegeven.
* Voert u een oudere versie van Java SDK uit? Vanaf versie 2.0.1 hebben we fout tolerantie mechanisme geïntroduceerd voor het afhandelen van terugkerende netwerk-en back-endservers en gegevens persistentie op lokale schijven.
* Krijgt u een beperking door buitensporige telemetrie? Als u INFO logboek registratie inschakelt, wordt er een logboek bericht weer gegeven dat de app wordt beperkt. Onze huidige limiet is 32-telemetrieprocessor-items per seconde.

### <a name="java-agent-cannot-capture-dependency-data"></a>De Java-Agent kan geen afhankelijkheids gegevens vastleggen
* Hebt u Java-agent geconfigureerd door de volgende [Java-Agent te configureren](java-agent.md) ?
* Zorg ervoor dat zowel de Java-Agent jar als het AI-Agent.xml-bestand in dezelfde map worden geplaatst.
* Zorg ervoor dat de afhankelijkheid die u probeert automatisch te verzamelen, wordt ondersteund voor automatische verzameling. Momenteel ondersteunen we alleen MySQL, MsSQL, Oracle DB en Azure cache voor redis-afhankelijkheids verzameling.

## <a name="no-usage-data"></a>Geen gebruiks gegevens
**Ik zie gegevens over aanvragen en reactie tijden, maar geen pagina weergave, browser of gebruikers gegevens.**

U hebt uw app ingesteld voor het verzenden van telemetrie van de server. Nu kunt u [uw webpagina's zo instellen dat telemetrie vanuit de webbrowser wordt verzonden][usage].

Als uw client een app is in een [telefoon of een ander apparaat][platforms], kunt u ook telemetrie verzenden.

Gebruik dezelfde instrumentatie sleutel om de telemetrie van de client en de server in te stellen. De gegevens worden weer gegeven in dezelfde Application Insights resource en u kunt gebeurtenissen van client en server correleren.

## <a name="disabling-telemetry"></a>Telemetrie uitschakelen
**Hoe kan ik telemetrie-verzameling uitschakelen?**

In code:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Of**

ApplicationInsights.xml bijwerken (in de map resources in uw project). Voeg het volgende toe onder het hoofd knooppunt:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Met behulp van de XML-methode moet u de toepassing opnieuw opstarten wanneer u de waarde wijzigt.

## <a name="changing-the-target"></a>Het doel wijzigen
**Hoe kan ik wijzigen met welke Azure-resource mijn project gegevens verzendt?**

* [Haal de instrumentatie sleutel van de nieuwe resource op.][java]
* Als u Application Insights aan uw project hebt toegevoegd met behulp van de Azure-toolkit voor Eclipse, klikt u met de rechter muisknop op uw webproject, selecteert u **Azure**, **configureert u Application Insights** en wijzigt u de sleutel.
* Als u de instrumentatie sleutel als omgevings variabele hebt geconfigureerd, moet u de waarde van de omgevings variabele bijwerken met de nieuwe iKey.
* Als dat niet het geval is, werkt u de sleutel in ApplicationInsights.xml in de map resources in uw project bij.

## <a name="debug-data-from-the-sdk"></a>Fout opsporingsgegevens van de SDK

**Hoe kan ik zien wat de SDK doet?**

Voor meer informatie over wat er gebeurt in de API, voegt u `<SDKLogger/>` onder het hoofd knooppunt van het ApplicationInsights.xml configuratie bestand toe.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

U kunt de logboeken ook een instructie geven om uit te voeren naar een bestand:

```XML
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Spring boot starter

Voeg het volgende toe aan het bestand om de SDK-logboek registratie in te scha kelen met veer boot-apps met behulp van de Application Insights Spring boot starter `application.properties` :

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

of om af te drukken naar de standaard fout:

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Java-agent

Als u logboek registratie van de JVM-agent wilt inschakelen, moet u het [AI-Agent.xml bestand](java-agent.md)bijwerken:

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>Eigenschappen van Java-opdracht regel
_Sinds versie 2.4.0_

Logboek registratie inschakelen met behulp van opdracht regel opties zonder configuratie bestanden te wijzigen:

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

of om af te drukken naar de standaard fout:

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>Het Start scherm van Azure
**Ik kijk naar [de Azure Portal](https://portal.azure.com). Laat de kaart iets weten over mijn app?**

Nee, het toont de status van Azure-servers over de hele wereld.

*Hoe vind ik gegevens over mijn app in het start Board van Azure (Start scherm)?*

Als u [uw app voor Application Insights hebt ingesteld][java], klikt u op Bladeren, selecteert u Application Insights en selecteert u de app-resource die u hebt gemaakt voor uw app. Als u sneller in de toekomst wilt, kunt u uw app vastmaken aan het start Board.

## <a name="intranet-servers"></a>Intranet servers
**Kan ik een server op mijn intranet controleren?**

Ja, de server kan via het open bare internet telemetrie verzenden naar de Application Insights Portal.

Mogelijk moet u [een aantal uitgaande poorten in de firewall van uw server openen](./ip-addresses.md#outgoing-ports) zodat de SDK gegevens naar de portal kan verzenden.

## <a name="data-retention"></a>Gegevensretentie
**Hoe lang worden gegevens in de portal bewaard? Is het veilig?**

Zie [gegevens retentie en privacy][data].

## <a name="debug-logging"></a>Logboek registratie van fouten
Application Insights gebruikt `org.apache.http` . Dit wordt verplaatst binnen Application Insights core-potten onder de naam ruimte `com.microsoft.applicationinsights.core.dependencies.http` . Hierdoor kunnen Application Insights scenario's afhandelen waarbij verschillende versies van dezelfde versie `org.apache.http` bestaan in één code basis.

>[!NOTE]
>Als u logboek registratie van fout opsporing inschakelt voor alle naam ruimten in de app, worden alle modules uitgevoerd, inclusief een andere `org.apache.http` naam `com.microsoft.applicationinsights.core.dependencies.http` . Application Insights kan geen filtering Toep assen op deze aanroepen omdat de logboek aanroep wordt gemaakt door de Apache-bibliotheek. Logboek registratie van fout opsporing produceert een aanzienlijke hoeveelheid logboek gegevens en wordt niet aanbevolen voor live productie-exemplaren.

## <a name="next-steps"></a>Volgende stappen
**Ik heb Application Insights voor mijn Java-Server-app ingesteld. Wat kan ik nog meer doen?**

* [Beschik baarheid van uw webpagina's bewaken][availability]
* [Gebruik van webpagina's bewaken][usage]
* [Gebruik bijhouden en problemen vaststellen in uw apparaat-apps][platforms]
* [Code schrijven om het gebruik van uw app bij te houden][track]
* [Diagnostische logboeken vastleggen][javalogs]

## <a name="get-help"></a>Hulp vragen
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Een probleem in GitHub bestand oplossen](https://github.com/microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[data]: ./data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ./platforms.md
[track]: ./api-custom-events-metrics.md
[usage]: javascript.md

