---
title: Gedistribueerde tracering gebruiken met Azure Spring Cloud
description: Meer informatie over het gebruik van gedistribueerde tracering van Spring Cloud via Azure Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: f55a82eeddc8d4515b0f1333b615244976975097
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877978"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Gedistribueerde tracering gebruiken met Azure Spring Cloud

Met de hulpprogramma's voor gedistribueerde tracering in Azure Spring Cloud kunt u eenvoudig complexe problemen opsporen en bewaken. Azure Spring Cloud integreert [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) met [Application Insights](../azure-monitor/app/app-insights-overview.md) van Azure. Deze integratie biedt krachtige functies voor gedistribueerde tracering via de Azure-portal.

::: zone pivot="programming-language-csharp"
In dit artikel leert u hoe u een .NET core Steeltoe-app kunt inschakelen voor het gebruik van gedistribueerde tracering.

## <a name="prerequisites"></a>Vereisten

Als u deze procedures wilt volgen, hebt u een Steeltoe-app nodig die al is [voor bereid voor implementatie naar Azure lente-Cloud](how-to-prepare-app-deployment.md).

## <a name="dependencies"></a>Afhankelijkheden

Voor Steeltoe 2.4.4 voegt u de volgende NuGet-pakketten toe:

* [Steeltoe. Management. TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)
* [Steeltoe. Management. ExporterCore](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/)

Voor Steeltoe 3.0.0 voegt u het volgende NuGet-pakket toe:

* [Steeltoe. Management. TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)

## <a name="update-startupcs"></a>Start. cs bijwerken

1. Voor Steeltoe 2.4.4 roept u `AddDistributedTracing` `AddZipkinExporter` de-methode aan `ConfigureServices` .

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration);
       services.AddZipkinExporter(Configuration);
   }
   ```

   Voor Steeltoe 3.0.0 roept u `AddDistributedTracing` de- `ConfigureServices` methode aan.

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration, builder => builder.UseZipkinWithTraceOptions(services));
   }
   ```

1. Voor Steeltoe 2.4.4 roept u `UseTracingExporter` de- `Configure` methode aan.

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
        app.UseTracingExporter();
   }
   ```

   Voor Steeltoe 3.0.0 zijn geen wijzigingen vereist in de- `Configure` methode.

## <a name="update-configuration"></a>Configuratie bijwerken

Voeg de volgende instellingen toe aan de configuratie bron die wordt gebruikt wanneer de app wordt uitgevoerd in azure lente-Cloud:

1. Stel `management.tracing.alwaysSample` in op Waar.

2. Als u tracerings reeksen wilt zien die worden verzonden tussen de Eureka-server, de configuratie server en de gebruikers-apps: ingesteld `management.tracing.egressIgnorePattern` op '/API/v2/spans |/v2/apps/.*/permissions |/Eureka/.*| /oauth/.*".

*appsettings.jsop* zou bijvoorbeeld de volgende eigenschappen bevatten:
 
```json
"management": {
    "tracing": {
      "alwaysSample": true,
      "egressIgnorePattern": "/api/v2/spans|/v2/apps/.*/permissions|/eureka/.*|/oauth/.*"
    }
  }
```

Zie [gedistribueerde tracering](https://steeltoe.io/docs/3/tracing/distributed-tracing) in de Steeltoe-documentatie voor meer informatie over gedistribueerde tracering in .net core Steeltoe-apps.
::: zone-end
::: zone pivot="programming-language-java"
In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Gedistribueerde tracering in de Azureportal inschakelen.
> * Spring Cloud Sleuth toevoegen aan uw toepassing.
> * Afhankelijkheidskaarten voor uw microservicetoepassingen weergeven.
> * Traceringsgegevens zoeken met verschillende filters.

## <a name="prerequisites"></a>Vereisten

Voor het volgen van deze procedures hebt u een Azure Spring Cloud-service nodig die al is ingericht en actief is. Voltooi de Snelstartgids voor het [implementeren van uw eerste Azure lente-Cloud toepassing](spring-cloud-quickstart.md) om een Azure lente-Cloud service in te richten en uit te voeren.

## <a name="add-dependencies"></a>Afhankelijkheden toevoegen

1. Voeg de volgende regel toe aan het bestand application.properties:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Na deze wijziging kan de Zipkin-afzender naar het web verzenden.

1. Sla deze stap over als u onze [gids voor het voorbereiden van een Azure Spring Cloud-toepassing](how-to-prepare-app-deployment.md) hebt gevolgd. Als dat niet het geval is, gaat u naar uw lokale ontwikkelomgeving en bewerkt u het bestand pom.xml om de volgende Spring Cloud Sleuth-afhankelijkheid toe te voegen:

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-sleuth</artifactId>
                <version>${spring-cloud-sleuth.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sleuth</artifactId>
        </dependency>
    </dependencies>
    ```

1. Bouw en implementeer opnieuw voor de Azure Spring Cloud-service om deze wijzigingen weer te geven.

## <a name="modify-the-sample-rate"></a>De samplefrequentie wijzigen

U kunt de snelheid waarmee uw telemetrie wordt verzameld, wijzigen door de samplefrequentie aan te passen. Als u bijvoorbeeld een sample van de helft wilt uitvoeren, opent u het bestand application.properties en wijzigt u de volgende regel:

```xml
spring.sleuth.sampler.probability=0.5
```

Als u al een toepassing hebt gemaakt en geïmplementeerd, kunt u de samplefrequentie wijzigen. Dit doet u door de vorige regel als een omgevingsvariabele in de Azure CLI of de Azure-portal toe te voegen.
::: zone-end

## <a name="enable-application-insights"></a>Application Insights inschakelen

1. Ga naar de pagina van Azure Spring Cloud in de Azure-portal.
1. Selecteer op de pagina **Bewaking** de optie **Gedistribueerde tracering**.
1. Selecteer **Instelling bewerken** om een nieuwe instelling te bewerken of toe te voegen.
1. Maak een nieuwe Application Insights-query of selecteer een bestaande.
1. Kies welke logboekregistratiecategorie u wilt bewaken en geef de bewaartijd op in dagen.
1. Selecteer **Toepassen** om de nieuwe tracering toe te passen.

## <a name="view-the-application-map"></a>Het toepassingsoverzicht weergeven

Ga terug naar de pagina **Gedistribueerde tracering** en selecteer **Toepassingsoverzicht weergeven**. Bekijk de visuele weergave van uw toepassings- en bewakingsinstellingen. Zie voor meer informatie over het gebruik van het toepassingsoverzicht [Toepassingsoverzicht: Gedistribueerde toepassingen classificeren](../azure-monitor/app/app-map.md).

## <a name="use-search"></a>De zoekfunctie gebruiken

Gebruik de zoekfunctie om te zoeken naar andere specifieke telemetrie-items. Selecteer op de pagina **Gedistribueerde tracering** de optie **Zoeken**. Zie [Zoeken in Application Insights gebruiken](../azure-monitor/app/diagnostic-search.md) voor meer informatie over het gebruik van de zoekfunctie.

## <a name="use-application-insights"></a>Application Insights gebruiken

Application Insights biedt naast het toepassingsoverzicht en de zoekfunctie ook bewakingsmogelijkheden. Zoek in de Azure-portal naar de naam van uw toepassing en open vervolgens een Application Insights-pagina om bewakingsinformatie te vinden. Zie [Azure Monitor-logboekquery's](/azure/data-explorer/kusto/query/) voor meer informatie over het gebruik van deze hulpprogramma's.

## <a name="disable-application-insights"></a>Application Insights uitschakelen

1. Ga naar de pagina van Azure Spring Cloud in de Azure-portal.
1. Selecteer in **Bewaking** de optie **Gedistribueerde tracering**.
1. Selecteer **Uitschakelen** om Application Insights uit te schakelen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u gedistribueerde tracering in Azure Spring Cloud kunt inschakelen en begrijpen. Zie [Een Azure Cosmos DB-database verbinden met een Azure Spring Cloud-toepassing](spring-cloud-howto-bind-cosmos.md) voor meer informatie over het verbinden van services met een toepassing.