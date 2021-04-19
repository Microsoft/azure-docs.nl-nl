---
title: Een toepassing voorbereiden voor implementatie in Azure Spring Cloud
description: Meer informatie over het voorbereiden van een toepassing voor implementatie naar Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: cabc4784dfb19f569212f4d0cb93e6838473e559
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714484"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>Een toepassing voorbereiden voor implementatie in Azure Spring Cloud

::: zone pivot="programming-language-csharp"
Azure Spring Cloud biedt robuuste services voor het hosten, bewaken, schalen en bijwerken van een Steeltoe-app. In dit artikel wordt beschreven hoe u een bestaande Steeltoe-toepassing voorbereidt voor implementatie in Azure Spring Cloud. 

In dit artikel worden de afhankelijkheden, configuratie en code uitgelegd die nodig zijn om een .NET Core Steeltoe-app uit te voeren in Azure Spring Cloud. Zie Deploy your first Azure Spring Cloud application (Uw eerste toepassing implementeren) Azure Spring Cloud voor meer informatie over het implementeren [van Azure Spring Cloud toepassing.](spring-cloud-quickstart.md)

>[!Note]
> Steeltoe-ondersteuning voor Azure Spring Cloud wordt momenteel aangeboden als openbare preview. Met openbare preview-aanbiedingen kunnen klanten voorafgaand aan de officiële release met nieuwe functies experimenteren.  Openbare preview-functies en -services zijn niet bedoeld voor gebruik in productie.  Voor meer informatie over ondersteuning tijdens previews kunt u de [Veelgestelde vragen](https://azure.microsoft.com/support/faq/) raadplegen of een [Ondersteuningsaanvraag](../azure-portal/supportability/how-to-create-azure-support-request.md) indienen.

##  <a name="supported-versions"></a>Ondersteunde versies

Azure Spring Cloud ondersteunt:

* .NET Core 3.1
* Steeltoe 2.4 en 3.0

## <a name="dependencies"></a>Afhankelijkheden

Voeg voor Steeltoe 2.4 het meest recente [pakket Microsoft.Azure.SpringCloud.Client 1.x.x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) toe aan het projectbestand:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.ExporterCore" Version="2.4.4" />
</ItemGroup>
```

Voeg voor Steeltoe 3.0 het meest recente [pakket Microsoft.Azure.SpringCloud.Client 2.x.x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) toe aan het projectbestand:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="3.0.0" />
</ItemGroup>
```

## <a name="update-programcs"></a>Program.cs bijwerken

Roep in `Program.Main` de methode de methode `UseAzureSpringCloudService` aan.

Voor Steeltoe 2.4.4 roept u na en na aan `UseAzureSpringCloudService` `ConfigureWebHostDefaults` als deze wordt `AddConfigServer` aangeroepen:

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

Roep voor Steeltoe 3.0.0 aan `UseAzureSpringCloudService` vóór `ConfigureWebHostDefaults` en vóór een Steeltoe-configuratiecode:

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

## <a name="enable-eureka-server-service-discovery"></a>Eureka Server-servicedetectie inschakelen

Stel in de configuratiebron die wordt gebruikt wanneer de app wordt uitgevoerd in Azure Spring Cloud in op dezelfde naam als de Azure Spring Cloud-app waarop het `spring.application.name` project wordt geïmplementeerd.

Als u bijvoorbeeld een .NET-project met de naam implementeert in een Azure Spring Cloud-app met de naam `EurekaDataProvider` `planet-weather-provider`appSettings.js *on-file,* moet dit de volgende JSON bevatten:

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>Servicedetectie gebruiken

Als u een service wilt aanroepen met behulp van de Eureka Server-servicedetectie, moet u HTTP-aanvragen indienen waarbij de waarde `http://<app_name>` `app_name` van de `spring.application.name` doel-app is. Met de volgende code wordt bijvoorbeeld de `planet-weather-provider` service aanroepen:

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
In dit onderwerp ziet u hoe u een bestaande Java Spring-toepassing voorbereidt voor implementatie naar Azure Spring Cloud. Indien correct geconfigureerd, biedt Azure Spring Cloud krachtige services voor het bewaken, schalen en bijwerken van uw Java-Spring Cloud toepassing.

Voordat u dit voorbeeld kunt uitvoeren, kunt u de [eenvoudige quickstart](spring-cloud-quickstart.md) uitproberen.

In andere voorbeelden wordt uitgelegd hoe u een toepassing implementeert Azure Spring Cloud wanneer het POM-bestand is geconfigureerd. 
* [Uw eerste app starten](spring-cloud-quickstart.md)
* [Microservices bouwen en uitvoeren](spring-cloud-quickstart-sample-app-introduction.md)

In dit artikel worden de vereiste afhankelijkheden uitgelegd en wordt uitgelegd hoe u deze toevoegt aan het POM-bestand.

## <a name="java-runtime-version"></a>Java Runtime-versie

Alleen Spring/Java-toepassingen kunnen worden uitgevoerd in Azure Spring Cloud.

Azure Spring Cloud ondersteunt zowel Java 8 als Java 11. De hostingomgeving bevat de nieuwste versie van Azul Zulu OpenJDK voor Azure. Zie Install [the JDK](/azure/developer/java/fundamentals/java-jdk-install)(De JDK installeren) voor meer informatie over Azul Zulu OpenJDK voor Azure.

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot en Spring Cloud versies

Als u een bestaande Spring Boot-toepassing wilt voorbereiden voor implementatie naar Azure Spring Cloud moet u de Spring Boot- en Spring Cloud-afhankelijkheden opnemen in het POM-bestand van de toepassing, zoals wordt weergegeven in de volgende secties.

Azure Spring Cloud ondersteunt Spring Boot versie 2.2, 2.3, 2.4. De volgende tabel bevat de ondersteunde Spring Boot en Spring Cloud combinaties:

Spring Boot versie | Spring Cloud versie
---|---
2.2 | Hoxton.SR8
2.3 | Hoxton.SR8
2.4.1+ | 2020.0.0

> [!NOTE]
> We hebben een probleem vastgesteld met Spring Boot 2.4.0 voor TLS-verificatie tussen uw apps en Eureka. Gebruik 2.4.1 of hoger. Raadpleeg onze [veelgestelde vragen](./spring-cloud-faq.md?pivots=programming-language-java#development) over de tijdelijke oplossing als u 2.4.0 wilt gebruiken.

### <a name="dependencies-for-spring-boot-version-2223"></a>Afhankelijkheden voor Spring Boot versie 2.2/2.3

Voeg Spring Boot versie 2.2 de volgende afhankelijkheden toe aan het POM-bestand van de toepassing.

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

### <a name="dependencies-for-spring-boot-version-24"></a>Afhankelijkheden voor Spring Boot versie 2.4

Voeg Spring Boot versie 2.2 de volgende afhankelijkheden toe aan het POM-bestand van de toepassing.

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
> Geef niet op `server.port` in uw configuratie. Azure Spring Cloud wordt deze instelling overgezet naar een vast poortnummer. Respecteer deze instelling ook en geef geen serverpoort op in uw code.

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Andere aanbevolen afhankelijkheden voor het inschakelen Azure Spring Cloud functies

Als u de ingebouwde functies van Azure Spring Cloud serviceregister naar gedistribueerde tracering wilt inschakelen, moet u ook de volgende afhankelijkheden in uw toepassing opnemen. U kunt een aantal van deze afhankelijkheden verwijderen als u geen bijbehorende functies voor de specifieke apps nodig hebt.

### <a name="service-registry"></a>Serviceregister

Als u de beheerde Azure Service Registry-service wilt gebruiken, moet u de afhankelijkheid opnemen in het `spring-cloud-starter-netflix-eureka-client` pom.xml bestand, zoals hier wordt weergegeven:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Het eindpunt van de Service Registry-server wordt automatisch opgenomen als omgevingsvariabelen met uw app. Toepassingen kunnen zichzelf registreren bij de Service Registry-server en andere afhankelijke microservices ontdekken.


#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient-aantekening

Voeg de volgende aantekening toe aan de broncode van de toepassing.
```java
@EnableDiscoveryClient
```
Zie bijvoorbeeld de piggymetrics-toepassing uit eerdere voorbeelden:
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

Als u Gedistribueerde configuratie wilt inschakelen, moet u de volgende `spring-cloud-config-client` afhankelijkheid opnemen in de sectie afhankelijkheden van uw pom.xml bestand:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Geef niet op `spring.cloud.config.enabled=false` in uw bootstrapconfiguratie. Anders werkt uw toepassing niet meer met Config Server.

### <a name="metrics"></a>Metrische gegevens

Neem de `spring-boot-starter-actuator` afhankelijkheid op in de sectie afhankelijkheden van uw pom.xml zoals hier wordt weergegeven:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Metrische gegevens worden periodiek uit de JMX-eindpunten gehaald. U kunt de metrische gegevens visualiseren met behulp van Azure Portal.

 > [!WARNING]
 > Geef op `spring.jmx.enabled=true` in uw configuratie-eigenschap. Anders kunnen metrische gegevens niet worden gevisualiseerd in Azure Portal.

### <a name="distributed-tracing"></a>Gedistribueerde tracering

U moet ook een exemplaar van Azure-toepassing Insights inschakelen om te kunnen werken met uw Azure Spring Cloud service-exemplaar. Zie de documentatie over gedistribueerde tracering Application Insights informatie over Application Insights met [Azure Spring Cloud.](spring-cloud-tutorial-distributed-tracing.md)

#### <a name="spring-boot-2223"></a>Spring Boot 2.2/2.3
Neem de volgende `spring-cloud-starter-sleuth` en `spring-cloud-starter-zipkin` afhankelijkheden op in de sectie afhankelijkheden van uw pom.xml bestand:

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

#### <a name="spring-boot-24"></a>Spring Boot 2.4
Neem de volgende `spring-cloud-sleuth-zipkin` afhankelijkheid op in de sectie Afhankelijkheden van uw pom.xml bestand:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>
```

## <a name="see-also"></a>Zie ook
* [Toepassingslogboeken en metrische gegevens analyseren](./diagnostic-services.md)
* [Uw configuratieserver instellen](spring-cloud-tutorial-config-server.md)
* [Gedistribueerde tracering gebruiken met Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md)
* [Spring-snelstartgids](https://spring.io/quickstart)
* [Spring Boot documentatie](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Volgende stappen

In dit onderwerp hebt u geleerd hoe u uw Java Spring-toepassing configureert voor implementatie naar Azure Spring Cloud. Zie Set up a Config Server instance (Een Config Server instellen) voor meer informatie over het instellen [van Config Server-instantie.](spring-cloud-tutorial-config-server.md)

Meer voorbeelden zijn beschikbaar in GitHub: [Azure Spring Cloud-voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
::: zone-end