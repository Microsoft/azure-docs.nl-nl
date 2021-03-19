---
title: Metrische gegevens over JMX configureren-Azure Monitor Application Insights voor Java
description: Extra JMX Metrics-verzameling configureren voor Azure Monitor Application Insights Java-Agent
ms.topic: conceptual
ms.date: 03/16/2021
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 020278bf6e5a823f6b3caa22d03f4b5dd003c0d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609252"
---
# <a name="configuring-jmx-metrics"></a>Metrische gegevens over JMX configureren

Application Insights Java 3,0-agent verzamelt standaard enkele van de JMX-metrische gegevens, maar in veel gevallen is dit niet voldoende. In dit document wordt de configuratie optie JMX in details beschreven.

## <a name="how-do-i-collect-additional-jmx-metrics"></a>Hoe kan ik extra metrische JMX-gegevens verzamelen?

Verzameling metrische JMX-gegevens kan worden geconfigureerd door een ```"jmxMetrics"``` sectie toe te voegen aan de applicationinsights.jsin het bestand. U kunt de naam van de metriek opgeven zoals u wilt dat deze wordt weer gegeven in Azure Portal in Application Insights-resource. U moet de object naam en het kenmerk definiëren voor elk van de metrische gegevens die u wilt verzamelen.

## <a name="how-do-i-know-what-metrics-are-available-to-configure"></a>Hoe kan ik weet u welke metrische gegevens er kunnen worden geconfigureerd?

U hebt dit gespijd: u moet weten wat de object namen en de kenmerken zijn. deze eigenschappen zijn anders voor verschillende bibliotheken, frameworks en toepassings servers en zijn vaak niet goed gedocumenteerd. Als u de object namen en-kenmerken wilt ophalen, moet u de MBean-structuur bekijken. Een MBean is een beheerd Java-object dat een apparaat, een toepassing of een bron kan vertegenwoordigen en een set kenmerken heeft. 

Als u de beschik bare metrische gegevens wilt bekijken en wilt bladeren door de beschik bare metrische gegevens, raden we u aan om [Java-missie beheer](https://www.oracle.com/java/technologies/jdk-mission-control.html)te gebruiken.

### <a name="how-to-navigate-the-java-mission-control-to-get-to-the-right-metrics"></a>Hoe gaat u naar het Java-missie beheer om naar de juiste metrische gegevens te gaan?

Wanneer u het hulp programma voor het beheer van de Java-opdracht uitvoert, hebt u een selectie van JVMs beschikbaar aan de linkerkant en klikt u op het relevante proces op het tabblad JVM browser. Wacht tot JMC het dash board voor het proces heeft geladen, selecteer het tabblad ' MBean browser ' aan de onderkant (zie hieronder). De JMC moet zich in dezelfde map bevinden als de JVM en uw proces/app moet actief zijn.

![Scherm afbeelding van de JMC MBean-browser](media/java-ipa/jmx/jmc-mbean-browser.png)

### <a name="how-to-get-to-the-metrics-i-want-and-the-necessary-attributes"></a>Hoe kan ik de gewenste metrische gegevens en de benodigde kenmerken ophalen?

De MBean-browser opent de MBean-structuur met de lijst met categorieën die kunnen worden uitgevouwen. Als u een categorie aan de linkerkant selecteert, wordt de lijst met kenmerken aan de rechter kant geopend. Hieronder ziet u een voor beeld van een metrische waarde, de bijbehorende object naam en de kenmerken. De kenmerken kunnen zijn genest, zoals in het onderstaande voor beeld.

![Scherm opname van JMC MBean-boom structuur](media/java-ipa/jmx/jmc-metric-sample.png)

### <a name="configuration-example"></a>Configuratie voorbeeld

Met behulp van de selectie zoals weer gegeven in de bovenstaande afbeelding, kunt u een aantal metrische gegevens configureren. De eerste is een voor beeld van een geneste metriek- `LastGcInfo` met verschillende eigenschappen, en we willen de maken `GcThreadCount` .

```json
"jmxMetrics": [
      {
        "name": "Demo - GC Thread Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "LastGcInfo.GcThreadCount"
      },
      {
        "name": "Demo - GC Collection Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "CollectionCount"
      },
      {
        "name": "Demo - Thread Count",
        "objectName": "java.lang:type=Threading",
        "attribute": "ThreadCount"
      }
],
```

### <a name="types-of-collected-metrics-and-available-configuration-options"></a>Typen verzamelde metrische gegevens en beschik bare configuratie opties?

We ondersteunen numerieke en Booleaanse JMX-metrische gegevens, terwijl andere typen niet worden ondersteund en worden genegeerd. 

Op dit moment worden de joker tekens en geaggregeerde kenmerken niet ondersteund. Daarom moet elke kenmerk ' object naam '/' kenmerk ' afzonderlijk worden geconfigureerd. 


## <a name="where-do-i-find-the-jmx-metrics-in-application-insights"></a>Waar vind ik de JMX-metrische gegevens in Application Insights?

Als uw toepassing wordt uitgevoerd en de metrische gegevens voor JMX worden verzameld, kunt u deze weer geven door naar Azure Portal te gaan en naar uw Application Insights-resource te gaan. Selecteer onder metrische gegevens tabblad de vervolg keuzelijst zoals hieronder wordt weer gegeven om de metrische gegevens weer te geven.

![Scherm opname van metrische gegevens in de portal](media/java-ipa/jmx/jmx-portal.png)
