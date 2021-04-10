---
title: Bewaking van Azure Service Fabric op toepassings niveau
description: Meer informatie over gebeurtenissen en logboeken voor toepassings-en service niveau waarmee u Azure Service Fabric-clusters kunt bewaken en diagnosticeren.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: a60eef008afae4185acc266c74c4fb0ce694d560
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627486"
---
# <a name="application-logging"></a>Toepassingslogboeken

Het instrumenteren van uw code is niet alleen een manier om inzicht te krijgen in uw gebruikers, maar u kunt ook zien of er iets mis is in uw toepassing en om te bepalen wat er moet worden opgelost. Hoewel het mogelijk is om een debugger te verbinden met een productie service, is het geen gang bare procedure. Het is dus belang rijk dat er gedetailleerde instrumentatie gegevens zijn.

De code wordt automatisch door sommige producten ingeinstrumenteerd. Hoewel deze oplossingen goed kunnen werken, is hand matige instrumentatie bijna altijd vereist voor specifieke bedrijfs logica. In het eind moet u voldoende informatie hebben om forensically te kunnen opsporen in de toepassing. Service Fabric toepassingen kunnen worden geinstrumenteerd met elk logboek registratie raamwerk. In dit document worden enkele verschillende benaderingen beschreven voor het instrumenteren van uw code en wanneer u een andere benadering voor een andere methode kiest. 

Zie [logboek registratie toevoegen aan uw service Fabric-toepassing](service-fabric-how-to-diagnostics-log.md)voor voor beelden van het gebruik van deze suggesties.

## <a name="application-insights-sdk"></a>Application Insights SDK

Application Insights heeft een uitgebreide integratie met Service Fabric uit het vak. Gebruikers kunnen de AI Service Fabric nuget-pakketten toevoegen en gegevens en logboeken ontvangen die in de Azure Portal zijn gemaakt en verzameld. Daarnaast wordt gebruikers geadviseerd hun eigen telemetrie toe te voegen om de toepassingen te diagnosticeren en fouten op te sporen en te controleren welke services en delen van hun toepassing het meest worden gebruikt. De [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient) -klasse in de SDK biedt veel manieren om telemetrie in uw toepassingen bij te houden. Bekijk een voor beeld van het instrumenteren en toevoegen van Application Insights aan uw toepassing in onze zelf studie voor het [bewaken en diagnosticeren van een .NET-toepassing](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="eventsource"></a>Source

Wanneer u een Service Fabric oplossing maakt op basis van een sjabloon in Visual Studio, wordt een door **Event source** afgeleide klasse (**ServiceEventSource** of **ActorEventSource**) gegenereerd. Er wordt een sjabloon gemaakt waarin u gebeurtenissen voor uw toepassing of service kunt toevoegen. De naam van de **Event source** **moet** uniek zijn en moet worden gewijzigd van de standaard sjabloon teken reeks mijn bedrijf- &lt; oplossings &gt; - &lt; project &gt; . Als er meerdere **Event source** -definities zijn die dezelfde naam gebruiken, treedt er een probleem op tijdens de uitvoering. Elke gedefinieerde gebeurtenis moet een unieke id hebben. Als een id niet uniek is, treedt er een runtime-fout op. Sommige organisaties wijzen een reeks waarden voor id's toe om conflicten tussen afzonderlijke ontwikkel teams te voor komen. Raadpleeg de [blog van Vance](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource) of de [MSDN-documentatie](/previous-versions/msp-n-p/dn774985(v=pandp.20))voor meer informatie.

## <a name="aspnet-core-logging"></a>ASP.NET Core logboek registratie

Het is belang rijk om zorgvuldig te plannen hoe u uw code gaat instrumenteren. Het juiste instrumentatie plan kan u helpen om te voor komen dat uw code basis wordt gestabiliseerd en vervolgens moet u de code opnieuw bedenken. Om het risico te beperken, kunt u een instrumentatie bibliotheek zoals [micro soft. Extensions. logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)kiezen, die deel uitmaakt van micro soft ASP.net core. ASP.NET Core heeft een [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) -interface die u kunt gebruiken met de provider van uw keuze, terwijl u het effect op bestaande code minimaliseert. U kunt de code in ASP.NET Core in Windows en Linux en in de volledige .NET Framework gebruiken zodat uw instrumentatie code wordt gestandaardiseerd.

## <a name="next-steps"></a>Volgende stappen

Wanneer u uw logboek registratie provider hebt gekozen voor het instrumenteren van uw toepassingen en services, moeten uw logboeken en gebeurtenissen worden geaggregeerd voordat ze kunnen worden verzonden naar elk analyse platform. Meer informatie over [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) en [Event flow](service-fabric-diagnostics-event-aggregation-eventflow.md) voor een beter begrip van de aanbevolen opties voor Azure monitor.
