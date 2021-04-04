---
title: Azure-toepassing Insights-telemetrie-gegevens model-telemetrie-context | Microsoft Docs
description: Application Insights-context gegevens model voor telemetrie
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 76f3be510494a1f005b0080ee8f2390a3fbc3622
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91767840"
---
# <a name="telemetry-context-application-insights-data-model"></a>Telemetrie-context: Application Insights gegevens model

Elk telemetrie-item heeft mogelijk een sterk getypeerde context velden. Elk veld biedt een specifiek bewakings scenario. Gebruik de verzameling aangepaste eigenschappen om aangepaste of toepassingsspecifieke contextuele informatie op te slaan.


## <a name="application-version"></a>Toepassings versie

De informatie in de context velden van de toepassing is altijd de toepassing die de telemetrie verzendt. De versie van de toepassing wordt gebruikt voor het analyseren van trend wijzigingen in het toepassings gedrag en de correlatie van de implementaties.

Maximale lengte: 1024


## <a name="client-ip-address"></a>IP-adres van client

Het IP-adres van het client apparaat. IPv4 en IPv6 worden ondersteund. Wanneer telemetrie wordt verzonden vanuit een service, is de locatie context over de gebruiker die de bewerking heeft gestart in de service. Application Insights de geo-locatie gegevens uit het client-IP-adres te extra heren en af te kappen. IP-adres van de client kan daarom niet worden gebruikt als Identificeer bare informatie voor eind gebruikers. 

Maximale lengte: 46


## <a name="device-type"></a>Apparaattype

Oorspronkelijk is dit veld gebruikt om het type apparaat aan te geven dat door de eind gebruiker van de toepassing wordt gebruikt. Vandaag gebruikt, voornamelijk voor het onderscheiden van Java script-telemetrie met het apparaattype ' browser ' van de telemetrie aan de server zijde met het apparaattype ' PC '.

Maximale lengte: 64


## <a name="operation-id"></a>Bewerkings-id

Een unieke id van de hoofd bewerking. Met deze id kunt u telemetrie groeperen over meerdere onderdelen. Zie de [correlatie van telemetrie](./correlation.md) voor meer informatie. De bewerkings-id wordt gemaakt door een aanvraag of een pagina weergave. Alle andere telemetrie stelt dit veld in op de waarde voor de insluitende aanvraag of pagina weergave. 

Maximale lengte: 128


## <a name="parent-operation-id"></a>ID van bovenliggende bewerking

De unieke id van het telemetrie-item direct Parent. Zie de [correlatie van telemetrie](./correlation.md) voor meer informatie.

Maximale lengte: 128


## <a name="operation-name"></a>Naam van bewerking

De naam (groep) van de bewerking. De naam van de bewerking wordt gemaakt door een aanvraag of een pagina weergave. Alle andere telemetrie-items stellen dit veld in op de waarde voor de insluitende aanvraag of pagina weergave. De bewerkings naam wordt gebruikt voor het zoeken van alle telemetriegegevens voor een groep bewerkingen (bijvoorbeeld ' GET Home/index '). Deze context eigenschap wordt gebruikt om vragen te beantwoorden zoals ' wat zijn de typische uitzonde ringen die op deze pagina worden gegenereerd '.

Maximale lengte: 1024


## <a name="synthetic-source-of-the-operation"></a>Synthetische bron van de bewerking

De naam van de synthetische bron. Een bepaalde telemetrie van de toepassing kan synthetisch verkeer vertegenwoordigen. Het kan zijn dat web crawler de website, site beschikbaarheids tests of traceringen van diagnostische bibliotheken zoals Application Insights SDK zelf kan indexeren.

Maximale lengte: 1024


## <a name="session-id"></a>Sessie-id

Sessie-ID: het exemplaar van de interactie van de gebruiker met de app. De informatie in de context velden van de sessie is altijd van de eind gebruiker. Wanneer telemetrie wordt verzonden vanuit een service, is de sessie context over de gebruiker die de bewerking heeft gestart in de service.

Maximale lengte: 64


## <a name="anonymous-user-id"></a>Anonieme gebruikers-id

Anonieme gebruikers-id. Vertegenwoordigt de eind gebruiker van de toepassing. Wanneer telemetrie vanuit een service wordt verzonden, is de gebruikers context de gebruiker die de bewerking in de service heeft gestart.

[Steek proeven](./sampling.md) zijn een van de technieken om de hoeveelheid verzamelde telemetrie te minimaliseren. Er wordt geprobeerd om een voor beeld van een gecorreleerde telemetrie te kiezen in of uit te steek proef algoritme. De anonieme gebruikers-id wordt gebruikt voor het genereren van de sampling Score. Anonieme gebruikers-id moet dus een wille keurige waarde hebben. 

Het gebruik van een anonieme gebruikers-id voor het opslaan van de gebruikers naam is een misbruik van het veld. Geverifieerde gebruikers-id gebruiken.

Maximale lengte: 128


## <a name="authenticated-user-id"></a>Geverifieerde gebruikers-id

Geverifieerde gebruikers-id. Het tegenovergestelde van een anonieme gebruikers-id is dit veld staat voor de gebruiker met een beschrijvende naam. Dit wordt alleen standaard verzameld met de ASP.NET Framework SDK [`AuthenticatedUserIdTelemetryInitializer`](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/WEB/Src/Web/Web/AuthenticatedUserIdTelemetryInitializer.cs) .  

Maximale lengte: 1024


## <a name="account-id"></a>Account-id

In multi tenant-toepassingen is dit de account-ID of naam waarmee de gebruiker werkt. Voor beelden zijn mogelijk abonnements-ID voor Azure Portal of blog naam voor een blog platform.

Maximale lengte: 1024


## <a name="cloud-role"></a>Cloud functie

De naam van de rol waarvan de toepassing deel uitmaakt. Wijst rechtstreeks toe aan de naam van de rol in Azure. Kan ook worden gebruikt voor het onderscheiden van micro Services, die deel uitmaken van één toepassing.

Maximale lengte: 256


## <a name="cloud-role-instance"></a>Cloud rolinstantie

De naam van het exemplaar waarop de toepassing wordt uitgevoerd. Computer naam voor on-premises, exemplaar naam voor Azure.

Maximale lengte: 256


## <a name="internal-sdk-version"></a>Intern: SDK-versie

SDK-versie. Raadpleeg [dit artikel](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/EndpointSpecs/SDK-VERSIONS.md) voor meer informatie.

Maximale lengte: 64


## <a name="internal-node-name"></a>Intern: knooppunt naam

Dit veld vertegenwoordigt de naam van het knoop punt dat wordt gebruikt voor facturerings doeleinden. Gebruik deze functie om de standaard detectie van knoop punten te onderdrukken.

Maximale lengte: 256


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoe u [telemetrie kunt uitbreiden en filteren](./api-filtering-sampling.md).
- Zie [gegevens model](data-model.md) voor Application Insights typen en gegevens model.
- Bekijk de [configuratie](./configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)van de verzameling met standaard context eigenschappen.

