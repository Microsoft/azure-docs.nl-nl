---
title: Configuratie tolerantie en herstel na nood gevallen Azure-app
description: Lean implementeren van tolerantie en herstel na nood gevallen met Azure-app configuratie.
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 7910e2092259081aade799fc662052e5a1375e25
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930480"
---
# <a name="resiliency-and-disaster-recovery"></a>Tolerantie en herstel na noodgevallen

Azure-app configuratie is momenteel een regionale service. Elke configuratie opslag wordt in een bepaalde Azure-regio gemaakt. Een onderbreking voor de hele regio is van invloed op alle archieven in die regio. App-configuratie biedt geen automatische failover naar een andere regio. Dit artikel bevat algemene richt lijnen over hoe u meerdere configuratie archieven kunt gebruiken in azure-regio's om de geo-tolerantie van uw toepassing te verg Roten.

## <a name="high-availability-architecture"></a>Architectuur met hoge Beschik baarheid

Als u de redundantie tussen regio's wilt realiseren, moet u meerdere app-configuratie archieven maken in verschillende regio's. Bij deze installatie heeft uw toepassing ten minste één extra configuratie opslag om terug te vallen als het primaire archief niet toegankelijk is. In het volgende diagram ziet u de topologie tussen uw toepassing en de primaire en secundaire configuratie archieven:

![Geografisch redundante opslag](./media/geo-redundant-app-configuration-stores.png)

Uw toepassing laadt de configuratie van de primaire en secundaire archieven parallel. Dit verhoogt de kans dat de configuratie gegevens correct worden opgehaald. U bent verantwoordelijk voor het bewaren van de gegevens in beide archieven. In de volgende secties wordt uitgelegd hoe u geografische tolerantie kunt bouwen in uw toepassing.

## <a name="failover-between-configuration-stores"></a>Failover tussen configuratie archieven

Technisch gesp roken voert uw toepassing geen failover uit. Er wordt geprobeerd om dezelfde set configuratie gegevens uit twee app-configuratie archieven tegelijk op te halen. Rang Schik uw code zo dat deze eerst wordt geladen vanuit de secundaire Store en vervolgens op de primaire opslag. Deze aanpak zorgt ervoor dat de configuratie gegevens in de primaire Store voor rang krijgen wanneer deze beschikbaar zijn. Het volgende code fragment laat zien hoe u deze ordening in .NET Core kunt implementeren:

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    
```

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                    .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
            })
            .UseStartup<Startup>());
```
---

U ziet dat de `optional` para meter wordt door gegeven aan de `AddAzureAppConfiguration` functie. Als `true` deze para meter is ingesteld op, kan de toepassing niet worden voortgezet als de functie geen configuratie gegevens kan laden.

## <a name="synchronization-between-configuration-stores"></a>Synchronisatie tussen configuratie archieven

Het is belang rijk dat uw Geo-redundante configuratie alle dezelfde set gegevens bevat. Er zijn twee manieren om dit te doen:

### <a name="backup-manually-using-the-export-function"></a>Hand matig een back-up maken met de functie exporteren

U kunt de functie **exporteren** in app-configuratie gebruiken om gegevens van het primaire archief te kopiëren naar de secundaire opslag op aanvraag. Deze functie is beschikbaar via zowel de Azure Portal als de CLI.

Vanuit het Azure Portal kunt u een wijziging naar een andere configuratie Store pushen door de volgende stappen uit te voeren.

1. Ga naar het tabblad **importeren/exporteren** en selecteer app **Export**-  >  **configuratie**  >  **doel** exporteren  >  **een resource selecteren**.

1. Op de nieuwe blade die wordt geopend, geeft u het abonnement, de resource groep en de resource naam van uw secundaire archief op. Selecteer vervolgens **Toep assen**.

1. De gebruikers interface wordt bijgewerkt, zodat u kunt kiezen welke configuratie gegevens u wilt exporteren naar uw secundaire archief. U kunt de standaard waarde tijd laten staan en beide **van label** en **Label** op dezelfde waarde instellen. Selecteer **Toepassen**. Herhaal dit voor alle labels in uw primaire archief.

1. Herhaal de vorige stappen wanneer de configuratie wordt gewijzigd.

Het export proces kan ook worden gerealiseerd met behulp van de Azure CLI. De volgende opdracht laat zien hoe u alle configuraties vanuit het primaire archief naar de secundaire exporteert:

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --dest-name {SecondaryStore} --label * --preserve-labels -y
```

### <a name="backup-automatically-using-azure-functions"></a>Automatisch een back-up maken met Azure Functions

Het back-upproces kan worden geautomatiseerd met behulp van Azure Functions. De oplossing maakt gebruik van de integratie met Azure Event Grid in app-configuratie. Wanneer de app-configuratie eenmaal is ingesteld, worden gebeurtenissen naar Event Grid gepubliceerd voor wijzigingen die zijn aangebracht in sleutel waarden in een configuratie opslag. Zo kan een Azure Functions-app naar deze gebeurtenissen en back-upgegevens op de juiste wijze Luis teren. Zie voor meer informatie de zelf studie over het [automatisch maken van back-ups van app-configuratie archieven](./howto-backup-config-store.md).

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u uw toepassing kunt uitbreiden om geo-tolerantie te krijgen tijdens de runtime voor app-configuratie. U kunt ook configuratie gegevens van de app-configuratie insluiten tijdens de build-of implementatie tijd. Zie [integreren met een CI/cd-pijp lijn](./integrate-ci-cd-pipeline.md)voor meer informatie.
