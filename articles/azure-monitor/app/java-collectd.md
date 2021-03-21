---
title: Prestaties van Java-Web-apps op Linux bewaken-Azure | Microsoft Docs
description: Uitgebreide bewaking van de toepassings prestaties van uw Java-website met de verzamelde-invoeg toepassing voor Application Insights.
ms.topic: conceptual
ms.date: 03/14/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 08d8deb4c7769c4f8a202050d7b5515439d691f4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100573854"
---
# <a name="collectd-linux-performance-metrics-in-application-insights-deprecated"></a>verzamelde: metrische gegevens over Linux-prestaties in Application Insights [afgeschaft]

> [!IMPORTANT]
> De **aanbevolen benadering** voor het bewaken van Java-toepassingen is het gebruik van de automatische instrumentatie zonder de code te wijzigen. Volg de richt lijnen voor **[Application Insights Java 3,0-agent](./java-in-process-agent.md)**.

Als u de metrische gegevens voor Linux-systeem prestaties in [Application Insights](./app-insights-overview.md)wilt verkennen, installeert u [verzamelde](https://collectd.org/)samen met de Application Insights-invoeg toepassing. Deze open source-oplossing verzamelt diverse systeem-en netwerk statistieken.

Normaal gesp roken gebruikt u verzamelde als u [uw Java-webservice al hebt instrumenteert met Application Insights][java]. Het biedt u meer gegevens om u te helpen bij het verbeteren van de prestaties van uw app of het vaststellen van problemen. 

## <a name="get-your-instrumentation-key"></a>De instrumentatie sleutel ophalen
Open in de [Microsoft Azure-Portal](https://portal.azure.com)de [Application Insights](./app-insights-overview.md) resource waar u de gegevens wilt weer geven. (Of [Maak een nieuwe resource](./create-new-resource.md).)

Neem een kopie van de instrumentatie sleutel, die de resource identificeert.

![Blader naar iedereen, open uw resource en kopieer de instrumentatie sleutel in de vervolg keuzelijst Essentials.](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>Verzamelde en de invoeg toepassing installeren
Op uw Linux-Server computers:

1. Installeer [verzamelde](https://collectd.org/) -versie 5.4.0 of hoger.
2. Down load de [Application Insights verzamelde Writer-invoeg toepassing](https://github.com/microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal). Noteer het versie nummer.
3. Kopieer de invoeg toepassing JAR naar `/usr/share/collectd/java` .
4. Bewerken `/etc/collectd/collectd.conf` :
   * Zorg ervoor dat [de Java-invoeg toepassing](https://collectd.org/wiki/index.php/Plugin:Java) is ingeschakeld.
   * Werk de JVMArg voor Java. class. Path bij om het volgende JAR op te laten. Het versie nummer bijwerken zodat dit overeenkomt met de naam die u hebt gedownload:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Voeg dit fragment toe met behulp van de instrumentatie sleutel van uw resource:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Hier volgt een voor beeld van een configuratie bestand:

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

Andere [verzamelde-invoeg toepassingen](https://collectd.org/wiki/index.php/Table_of_Plugins)configureren, waarmee verschillende gegevens uit verschillende bronnen kunnen worden verzameld.

Start verzamelde opnieuw op volgens de [hand leiding](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>De gegevens in Application Insights weer geven
Open in uw Application Insights resource [metrische gegevens en Voeg grafieken toe][metrics]om de metrische gegevens te selecteren die u wilt weer geven in de aangepaste categorie.

Standaard worden de metrische gegevens geaggregeerd op alle hostcomputers waarvan de metrische gegevens zijn verzameld. Als u de metrische gegevens per host wilt weer geven, schakelt u op de Blade diagram Details groeperen in en kiest u vervolgens groeperen op verzamelde-host.

## <a name="to-exclude-upload-of-specific-statistics"></a>Het uploaden van specifieke statistieken uitsluiten
Standaard verzendt de Application Insights-invoeg toepassing alle gegevens die worden verzameld door alle ingeschakelde invoeg toepassingen voor verzamelde. 

Gegevens uitsluiten van specifieke invoeg toepassingen of gegevens bronnen:

* Bewerk het configuratie bestand. 
* `<Plugin ApplicationInsightsWriter>`Voeg in de regels van de richt lijnen als volgt toe:

| Richt | Effect |
| --- | --- |
| `Exclude disk` |Alle gegevens uitsluiten die zijn verzameld door de `disk` invoeg toepassing |
| `Exclude disk:read,write` |Sluit de bronnen met de naam `read` en `write` uit van de `disk` invoeg toepassing. |

Afzonderlijke instructies met een nieuwe regel.

## <a name="problems"></a>Problemen?
*Ik zie geen gegevens in de portal*

* Open [zoeken][diagnostic] om te zien of de onbewerkte gebeurtenissen zijn aangekomen. Soms duurt het langer om weer te geven in Metrics Explorer.
* Mogelijk moet u [firewall-uitzonde ringen voor uitgaande gegevens instellen](./ip-addresses.md)
* Schakel tracering in de Application Insights-invoeg toepassing in. Deze regel toevoegen in `<Plugin ApplicationInsightsWriter>` :
  * `SDKLogger true`
* Open een Terminal en start verzamelde in de uitgebreide modus om te zien welke problemen er worden gerapporteerd:
  * `sudo collectd -f`

## <a name="known-issue"></a>Bekend probleem

De Application Insights write-invoeg toepassing is niet compatibel met bepaalde invoeg toepassingen voor lezen. Sommige invoeg toepassingen verzenden soms ' NaN ' waarbij de Application Insights-invoeg toepassing een getal met drijvende komma verwacht.

Symptoom: het verzamelde-logboek bevat fouten met ' AI:... SyntaxError: onverwacht token N.

Tijdelijke oplossing: Sluit de gegevens die door het probleem zijn verzameld, uit. 

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#track-exception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../essentials/metrics-charts.md

