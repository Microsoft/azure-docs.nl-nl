---
title: Een toepassing voorbereiden voor implementatie in azure lente Cloud
description: Meer informatie over het voorbereiden van een toepassing voor implementatie in azure lente-Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: a90d4c2a8649e270365af9b11fa408b865945f92
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878105"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>Een toepassing voorbereiden voor implementatie in azure lente-Cloud

::: zone pivot="programming-language-csharp"
Azure lente Cloud biedt krachtige services voor het hosten, controleren, schalen en bijwerken van een Steeltoe-app. In dit artikel wordt beschreven hoe u een bestaande Steeltoe-toepassing voorbereidt voor implementatie naar Azure lente-Cloud. 

In dit artikel worden de afhankelijkheden, configuratie en code beschreven die nodig zijn voor het uitvoeren van een .NET core Steeltoe-app in azure lente Cloud. Voor informatie over het implementeren van een toepassing in azure lente-Cloud, raadpleegt u [uw eerste Azure lente-Cloud toepassing implementeren](spring-cloud-quickstart.md).

>[!Note]
> Steeltoe-ondersteuning voor Azure Spring Cloud wordt momenteel aangeboden als openbare preview. Met openbare preview-aanbiedingen kunnen klanten voorafgaand aan de officiële release met nieuwe functies experimenteren.  Openbare preview-functies en -services zijn niet bedoeld voor gebruik in productie.  Voor meer informatie over ondersteuning tijdens previews kunt u de [Veelgestelde vragen](https://azure.microsoft.com/support/faq/) raadplegen of een [Ondersteuningsaanvraag](../azure-portal/supportability/how-to-create-azure-support-request.md) indienen.

##  <a name="supported-versions"></a>Ondersteunde versies

Azure lente Cloud ondersteunt:

* .NET Core 3.1
* Steeltoe 2,4 en 3,0

## <a name="dependencies"></a>Afhankelijkheden

Voor Steeltoe 2,4 voegt u het meest recente pakket [micro soft. Azure. SpringCloud. client 1. x. x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) toe aan het project bestand:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.ExporterCore" Version="2.4.4" />
</ItemGroup>
```

Voor Steeltoe 3,0 voegt u het meest recente pakket [micro soft. Azure. SpringCloud. client 2. x. x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) toe aan het project bestand:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="3.0.0" />
</ItemGroup>
```

## <a name="update-programcs"></a>Programma bijwerken. cs

Roep de-methode in de `Program.Main` -methode aan `UseAzureSpringCloudService` .

Voor Steeltoe 2.4.4 roept `UseAzureSpringCloudService` u na `ConfigureWebHostDefaults` en na `AddConfigServer` het volgende in:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer()
        .UseAzureSpringCloudService();
```

Voor Steeltoe 3.0.0 moet u `UseAzureSpringCloudService` vóór `ConfigureWebHostDefaults` en voor elke Steeltoe configuratie code aanroepen:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .UseAzureSpringCloudService()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer();
```

## <a name="enable-eureka-server-service-discovery"></a>Detectie van Eureka Server-service inschakelen

Stel in de configuratie bron die wordt gebruikt wanneer de app wordt uitgevoerd in azure lente-Cloud, de naam in van de `spring.application.name` Azure lente-Cloud toepassing waarop het project wordt geïmplementeerd.

Als u bijvoorbeeld een .NET-project `EurekaDataProvider` met de naam naar een Azure lente-Cloud toepassing implementeert met `planet-weather-provider` de naam *appSettings.jsin* file moet de volgende JSON bevatten:

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>Service detectie gebruiken

Als u een service wilt aanroepen met behulp van de service detectie van de Eureka-server, moet u HTTP-aanvragen naar `http://<app_name>` een locatie met de `app_name` waarde van `spring.application.name` de doel-app. Met de volgende code wordt bijvoorbeeld de `planet-weather-provider` service aangeroepen:

```csharp
using (var client = new HttpClient(discoveryHandler, false))
{
    var responses = await Task.WhenAll(
        client.GetAsync("http://planet-weather-provider/weatherforecast/mercury"),
        client.GetAsync("http://planet-weather-provider/weatherforecast/saturn"));
    var weathers = await Task.WhenAll(from res in responses select res.Content.ReadAsStringAsync());
    return new[]
    {
        new KeyValuePair<string, string>("Mercury", weathers[0]),
        new KeyValuePair<string, string>("Saturn", weathers[1]),
    };
}
```
::: zone-end

::: zone pivot="programming-language-java"
In dit onderwerp wordt beschreven hoe u een bestaande Java-toepassing voorbereidt voor implementatie naar Azure lente-Cloud. Als het goed is geconfigureerd, biedt Azure lente Cloud krachtige services voor het bewaken, schalen en bijwerken van uw Java-lente-Cloud toepassing.

Voordat u dit voorbeeld kunt uitvoeren, kunt u de [eenvoudige quickstart](spring-cloud-quickstart.md) uitproberen.

Andere voor beelden wordt uitgelegd hoe u een toepassing implementeert in azure lente-Cloud wanneer het POM-bestand wordt geconfigureerd. 
* [Uw eerste app starten](spring-cloud-quickstart.md)
* [Microservices bouwen en uitvoeren](spring-cloud-quickstart-sample-app-introduction.md)

In dit artikel worden de vereiste afhankelijkheden beschreven en wordt uitgelegd hoe u deze toevoegt aan het POM-bestand.

## <a name="java-runtime-version"></a>Java runtime-versie

Alleen veer-en Java-toepassingen kunnen worden uitgevoerd in azure lente-Cloud.

Azure lente-Cloud ondersteunt Java 8 en Java 11. De hosting omgeving bevat de nieuwste versie van Azul Zulu OpenJDK voor Azure. Zie [install the jdk](/azure/developer/java/fundamentals/java-jdk-install)(Engelstalig) voor meer informatie over Azul Zulu openjdk voor Azure.

## <a name="spring-boot-and-spring-cloud-versions"></a>Lente-boot-en lente-Cloud versies

Als u een bestaande Spring boot-toepassing wilt voorbereiden voor implementatie in azure lente-Cloud, neemt u de voor-en achterliggende Cloud afhankelijkheden op in het POM-bestand van de toepassing, zoals wordt weer gegeven in de volgende secties.

De Azure lente-Cloud ondersteunt alleen veer boot-apps van veer boot versie 2,1 of versie 2,2. De volgende tabel geeft een overzicht van de ondersteunde veer boot-en lente-Cloud combinaties:

Spring boot-versie | Lente-Cloud versie
---|---
2.2 | Hoxton.SR8
2.3 | Hoxton.SR8
2.4.1 + | 2020.0.0

> [!NOTE]
> We hebben een probleem geïdentificeerd met een Spring boot-2.4.0 voor TLS-verificatie tussen uw apps en Eureka. gebruik 2.4.1 of hoger. Raadpleeg de [Veelgestelde vragen](./spring-cloud-faq.md?pivots=programming-language-java#development) over de tijdelijke oplossing als u 2.4.0T.

### <a name="dependencies-for-spring-boot-version-2223"></a>Afhankelijkheden voor Spring boot-versie 2.2/2.3

Voor Spring boot versie 2,2 voegt u de volgende afhankelijkheden toe aan het POM-bestand van de toepassing.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-24"></a>Afhankelijkheden voor Spring boot versie 2,4

Voor Spring boot versie 2,2 voegt u de volgende afhankelijkheden toe aan het POM-bestand van de toepassing.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.1.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>2020.0.0</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

> [!WARNING]
> Geef niets `server.port` op in uw configuratie. Deze instelling wordt door Azure lente Cloud OVERiDE naar een vast poort nummer. U moet deze instelling ook respecteren en de server poort niet opgeven in uw code.

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Andere aanbevolen afhankelijkheden voor het inschakelen van Azure lente-Cloud functies

Als u de ingebouwde functies van Azure veer cloud van service register wilt inschakelen voor gedistribueerde tracering, moet u ook de volgende afhankelijkheden in uw toepassing toevoegen. U kunt sommige van deze afhankelijkheden verwijderen als u geen bijbehorende functies voor de specifieke apps nodig hebt.

### <a name="service-registry"></a>Service register

Als u de beheerde Azure Service Registry-service wilt gebruiken, neemt u de `spring-cloud-starter-netflix-eureka-client` afhankelijkheid op in het pom.xml-bestand, zoals hier wordt weer gegeven:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Het eind punt van de service register server wordt automatisch geïnjecteerd als omgevings variabelen met uw app. Toepassingen kunnen zich bij de service register server registreren en andere afhankelijke micro services ontdekken.


#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient aantekening

Voeg de volgende aantekening toe aan de bron code van de toepassing.
```java
@EnableDiscoveryClient
```
Zie bijvoorbeeld de piggymetrics-toepassing uit eerdere voor beelden:
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>Gedistribueerde configuratie

Als u gedistribueerde configuratie wilt inschakelen, neemt u de volgende `spring-cloud-config-client` afhankelijkheden op in het gedeelte met afhankelijkheden van uw pom.xml bestand:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Geef niets `spring.cloud.config.enabled=false` op in de configuratie van uw Boots trap. Anders werkt de toepassing niet meer met de configuratie server.

### <a name="metrics"></a>Metrische gegevens

Neem de `spring-boot-starter-actuator` afhankelijkheid op in de sectie afhankelijkheden van uw pom.xml-bestand, zoals hier wordt weer gegeven:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Metrische gegevens worden periodiek opgehaald uit de JMX-eind punten. U kunt de metrische gegevens visualiseren met behulp van de Azure Portal.

 > [!WARNING]
 > Geef `spring.jmx.enabled=true` de configuratie-eigenschap op. Als dat niet het geval is, kunnen er geen metrische gegevens worden gevisualiseerd in Azure Portal.

### <a name="distributed-tracing"></a>Gedistribueerde tracering

U moet ook een Azure-toepassing Insights-exemplaar inschakelen om te werken met uw Azure lente-Cloud service-exemplaar. Zie de [documentatie over gedistribueerde tracering](spring-cloud-tutorial-distributed-tracing.md)voor meer informatie over het gebruik van Application Insights met Azure lente Cloud.

#### <a name="spring-boot-2223"></a>Spring boot 2.2/2.3
Neem de volgende `spring-cloud-starter-sleuth` en `spring-cloud-starter-zipkin` afhankelijkheden op in het gedeelte met afhankelijkheden van uw pom.xml-bestand:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

#### <a name="spring-boot-24"></a>Spring boot 2,4
Neem de volgende `spring-cloud-sleuth-zipkin` afhankelijkheden op in het gedeelte met afhankelijkheden van uw pom.xml-bestand:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>
```

## <a name="see-also"></a>Zie ook
* [Toepassings logboeken en metrische gegevens analyseren](./diagnostic-services.md)
* [Uw configuratieserver instellen](spring-cloud-tutorial-config-server.md)
* [Gedistribueerde tracering gebruiken met Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md)
* [Hand leiding voor veer Snelstartgids](https://spring.io/quickstart)
* [Spring boot-documentatie](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Volgende stappen

In dit onderwerp hebt u geleerd hoe u uw Java lente-toepassing kunt configureren voor implementatie in azure lente-Cloud. Zie [een configuratie Server-exemplaar instellen](spring-cloud-tutorial-config-server.md)voor meer informatie over het instellen van een configuratie Server-exemplaar.

Meer voorbeelden zijn beschikbaar in GitHub: [Azure Spring Cloud-voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
::: zone-end