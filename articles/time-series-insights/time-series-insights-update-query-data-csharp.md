---
title: Gegevens opvragen uit een Gen2-omgeving met C#-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het opvragen van gegevens uit een Azure Time Series Insights Gen2-omgeving met behulp van een app die is geschreven in C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 81725a28102caf0d69a9fb303eaccdcf2151587d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020024"
---
# <a name="query-data-from-the-azure-time-series-insights-gen2-environment-using-c-sharp"></a>Gegevens opvragen uit de Azure Time Series Insights Gen2-omgeving met C-Kruis

Dit C#-voor beeld laat zien hoe u gegevens opvraagt uit de [api's voor Gen2 Data Access](/rest/api/time-series-insights/reference-data-access-overview) in azure time series Insights Gen2-omgevingen.

> [!TIP]
> Gen2 C#-code voorbeelden weer geven op [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample) .

## <a name="summary"></a>Samenvatting

De voorbeeld code hieronder bevat de volgende functies:

* Ondersteuning voor het automatisch genereren van de SDK vanuit [Azure auto rest](https://github.com/Azure/AutoRest).
* Een toegangs token verkrijgen via Azure Active Directory met behulp van [micro soft. Identity model. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Het aangeschafte toegangs token door geven in de `Authorization` koptekst van volgende API-aanvragen voor Data Access.
* Het voor beeld biedt een console interface waarin wordt uitgelegd hoe HTTP-aanvragen worden gedaan:
  * [Gen2 environments-API](/rest/api/time-series-insights/reference-environments-apis)
    * De API voor de [Beschik baarheid van omgevingen ophalen](/rest/api/time-series-insights/dataaccessgen2/query/getavailability) en de [Event schema-API ophalen](/rest/api/time-series-insights/dataaccessgen2/query/geteventschema)
  * [Gen2-query-API](/rest/api/time-series-insights/reference-query-apis)
    * [Get Events API](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents), [Get Series API](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)en [Get Total Series API](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
  * [Api's voor time series model](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
    * [Hiërarchieën ophalen API](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies) en [hiërarchieën batch-API](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    * [Typen API](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes) en [typen ophalen batch-API](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    * [Instances-API](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances) en [instances-batch-API](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch) ophalen

* Geavanceerde [Zoek](/rest/api/time-series-insights/reference-model-apis#search-features) -en [TSX](/rest/api/time-series-insights/reference-time-series-expression-syntax) -mogelijkheden.

## <a name="prerequisites-and-setup"></a>Vereisten en installatie

Voer de volgende stappen uit voordat u de voorbeeld code compileert en uitvoert:

1. [Richt een Gen2-Azure time series Insights](./how-to-provision-manage.md#create-the-environment) omgeving in.
1. Configureer uw Azure Time Series Insights-omgeving voor Azure Active Directory zoals beschreven in [verificatie en autorisatie](time-series-insights-authentication-and-authorization.md).
1. Voer de [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/GenerateCode.bat) uit zoals opgegeven in de [README.MD](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/Readme.md) om de client afhankelijkheden van Azure time series Insights Gen2 te genereren.
1. Open de `TSIPreviewDataPlaneclient.sln` oplossing en stel `DataPlaneClientSampleApp` deze in als het standaard project in Visual Studio.
1. Installeer de vereiste project afhankelijkheden met behulp van de stappen die [hieronder](#project-dependencies) worden beschreven en compileer het voor beeld naar een uitvoerbaar `.exe` bestand.
1. Voer het `.exe` bestand uit door erop te dubbel klikken.

## <a name="project-dependencies"></a>Projectafhankelijkheden

Het is raadzaam om de nieuwste versie van Visual Studio te gebruiken:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -versie 16.4.2 +

De voorbeeld code bevat verschillende vereiste afhankelijkheden die in het [packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/packages.config) bestand kunnen worden weer gegeven.

Down load de pakketten in Visual Studio 2019 **door de**  >  optie build **Build Solution** te selecteren.

U kunt ook elk pakket toevoegen met behulp van [NuGet 2.12 +](https://www.nuget.org/). Bijvoorbeeld:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C#-voorbeeld code

Raadpleeg de [Azure time series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample) opslag plaats voor toegang tot de C#-voorbeeld code.

> [!NOTE]
>
> * Het code voorbeeld kan worden uitgevoerd zonder de standaard omgevings variabelen te wijzigen.
> * Het code voorbeeld wordt gecompileerd naar een .NET-uitvoer bare console-app.

## <a name="next-steps"></a>Volgende stappen

* Lees de [query-API-verwijzing](/rest/api/time-series-insights/reference-query-apis)voor meer informatie over het uitvoeren van query's.

* Lees hoe u [met de client-SDK verbinding maakt met een Java script-app](https://github.com/microsoft/tsiclient) om te Azure time series Insights.