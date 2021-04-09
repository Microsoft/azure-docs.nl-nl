---
title: Gegevens opvragen uit een gen1-omgeving met C# code-Azure Time Series Insights gen1 | Microsoft Docs
description: Meer informatie over het opvragen van gegevens uit een Azure Time Series Insights gen1-omgeving met behulp van een aangepaste app die is geschreven in C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 724bc80f8887ff2c47db93ecfc4cd2e20be7e7f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95020041"
---
# <a name="query-data-from-the-azure-time-series-insights-gen1-environment-using-c-sharp"></a>Gegevens opvragen uit de Azure Time Series Insights gen1-omgeving met C-Kruis

> [!CAUTION]
> Dit is een Gen1-artikel.

In dit C#-voor beeld ziet u hoe u de [gen1-query-api's](/rest/api/time-series-insights/gen1-query) kunt gebruiken om gegevens uit Azure time series Insights gen1-omgevingen op te vragen.

> [!TIP]
> Gen1 C#-code voorbeelden weer geven op [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample) .

## <a name="summary"></a>Samenvatting

De voorbeeld code hieronder bevat de volgende functies:

* Een toegangs token verkrijgen via Azure Active Directory met behulp van [micro soft. Identity model. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Het aangeschafte toegangs token in de `Authorization` koptekst van volgende query-API-aanvragen door geven.

* Het voor beeld roept elk van de gen1-query-Api's aan om te demonstreren hoe HTTP-aanvragen worden gedaan:
  * De [omgevings-API ophalen](/rest/api/time-series-insights/gen1-query-api#get-environments-api) om de omgevingen te retour neren waartoe de gebruiker toegang heeft
  * [API voor omgevings beschikbaarheid ophalen](/rest/api/time-series-insights/gen1-query-api#get-environment-availability-api)
  * [API voor omgevings gegevens ophalen](/rest/api/time-series-insights/gen1-query-api#get-environment-metadata-api) om de meta gegevens van de omgeving op te halen
  * [API voor omgevings gebeurtenissen ophalen](/rest/api/time-series-insights/gen1-query-api#get-environment-events-api)
  * [API voor samen stellen van omgeving ophalen](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api)

* Hoe u kunt communiceren met de gen1-query-Api's met behulp van WSS voor het volgende bericht:

  * [Gestreamde API voor de omgevings gebeurtenissen ophalen](/rest/api/time-series-insights/gen1-query-api#get-environment-events-streamed-api)
  * [Gestreamde API voor de omgeving ophalen](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Vereisten en installatie

Voer de volgende stappen uit voordat u de voorbeeld code compileert en uitvoert:

1. [Richt een gen1-Azure time series Insights](./time-series-insights-get-started.md) omgeving in.
1. Configureer uw Azure Time Series Insights-omgeving voor Azure Active Directory zoals beschreven in [verificatie en autorisatie](time-series-insights-authentication-and-authorization.md).
1. Installeer de vereiste project afhankelijkheden.
1. Bewerk de voorbeeld code hieronder door elk **#DUMMY #** te vervangen door de juiste omgevings-id.
1. Voer de code in Visual Studio uit.

## <a name="project-dependencies"></a>Projectafhankelijkheden

Het is raadzaam om de nieuwste versie van Visual Studio te gebruiken:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -versie 16.4.2 +

De voorbeeld code bevat twee vereiste afhankelijkheden:

* Het pakket [micro soft. Identity model. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -3.13.9.
* [Newtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json) een 9.0.1-pakket.

Down load de pakketten in Visual Studio 2019 **door de**  >  optie build **Build Solution** te selecteren.

U kunt de pakketten ook toevoegen met behulp van [NuGet 2.12 +](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C#-voorbeeld code

Raadpleeg het [Azure time series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs)] opslag plaats voor toegang tot de C#-voorbeeld code.

## <a name="next-steps"></a>Volgende stappen

* Lees de [query-API-verwijzing](/rest/api/time-series-insights/gen1-query-api)voor meer informatie over het uitvoeren van query's.

* Lees hoe u [met de client-SDK verbinding maakt met een Java script-app](https://github.com/microsoft/tsiclient) om te time series Insights.
Azure-samples/Azure-time-series-Insights/gen1-voor beeld/csharp-TSI-gen1-Sample/Program. cs