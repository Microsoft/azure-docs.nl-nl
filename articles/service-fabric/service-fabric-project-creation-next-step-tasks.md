---
title: Service Fabric volgende stappen voor het maken van een project
description: Meer informatie over het toepassings project dat u zojuist hebt gemaakt in Visual Studio.  Meer informatie over het bouwen van services met zelf studies en meer informatie over het ontwikkelen van services voor Service Fabric.
ms.topic: conceptual
ms.date: 12/21/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 59c8eb0737d2cef1c4b1df34d673b74944fef4e1
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760432"
---
# <a name="your-service-fabric-application-and-next-steps"></a>Uw Service Fabric-toepassing en de volgende stappen
Uw Azure Service Fabric-toepassing is gemaakt. Dit artikel bevat een aantal resources, wat meer informatie u mogelijk interesseert en mogelijke [volgende stappen](#next-steps).

Nieuwe gebruikers kunnen [zelf studies, scenario's en voor beelden](#get-started-with-tutorials-walk-throughs-and-samples) vinden. Het kan ook handig zijn om de [structuur van het gemaakte toepassings project](#the-application-project)te controleren. Ook bevat beschrijvingen van de [Programmeer modellen](#learn-more-about-the-programming-models)van service Fabric, [service communicatie](#learn-about-service-communication), [toepassings beveiliging](#learn-about-configuring-application-security)en [levens cyclus van toepassingen](#learn-about-the-application-lifecycle).

Meer ervaren gebruikers kunnen het gedeelte van de Service Fabric [Best practices](#learn-about-best-practices) vinden om te leren hoe ze profiteren van het platform en de structuur van toepassingen met een maximale effectiviteit.

Zie de [betreffende sectie](#have-questions-or-feedback--need-to-report-an-issue)voor degenen met vragen of feedback of voor wie een probleem moet worden gemeld.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Aan de slag met zelf studies, instructies en voor beelden
Klaar om te beginnen?  

Gebruik de zelf studie voor .NET-toepassingen. Meer informatie over het [bouwen van een app](service-fabric-tutorial-create-dotnet-app.md) met een ASP.net core front-end en een stateful back-end, [het implementeren van de toepassing](service-fabric-tutorial-deploy-app-to-party-cluster.md) naar een cluster, het [configureren van CI/cd](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)en het [instellen van controle en diagnostische gegevens](service-fabric-tutorial-monitoring-aspnet.md).

Of probeer een van de volgende instructies uit en maak uw eerste...
- [C# Reliable Services-service in Windows](service-fabric-reliable-services-quick-start.md) 
- [C# Reliable Actors-service in Windows](service-fabric-reliable-actors-get-started.md) 
- [Uitvoer bare gast-service in Windows](quickstart-guest-app.md) 
- [Windows-containertoepassing](service-fabric-get-started-containers.md) 

Misschien bent u ook geïnteresseerd in het uitproberen van de [voorbeeld toepassingen](/samples/browse/?products=azure).

## <a name="the-application-project"></a>Het toepassings project
Elke nieuwe toepassing bevat een toepassings project. Er kunnen één of twee extra projecten zijn, afhankelijk van het gekozen type service.

Het toepassings project bestaat uit:

* Een verzameling verwijzingen naar de services waaruit uw toepassing is opgebouwd.
* Drie publicatie profielen (lokaal van 1 knoop punt, lokaal 5 knoop punt en Cloud) die u kunt gebruiken om voor keuren te onderhouden voor het werken met verschillende omgevingen, zoals voor keuren die betrekking hebben op een cluster eindpunt en of er standaard upgrade-implementaties moeten worden uitgevoerd.
* Drie toepassings parameter bestanden (hetzelfde als hierboven) die u kunt gebruiken voor het onderhouden van omgevings-specifieke toepassings configuraties, zoals het aantal partities dat moet worden gemaakt voor een service. Meer informatie over het [configureren van uw toepassing voor meerdere omgevingen](service-fabric-manage-multiple-environment-app-configuration.md).
* Een implementatie script dat u kunt gebruiken om uw toepassing te implementeren vanaf de opdracht regel of als onderdeel van een automatische continue integratie-en implementatie pijplijn. Meer informatie over het [implementeren van toepassingen met behulp van Power shell](service-fabric-deploy-remove-applications.md).
* Het toepassings manifest, waarmee de toepassing wordt beschreven. U vindt het manifest in de map Application Package root. Meer informatie over [toepassings-en service manifesten](service-fabric-application-model.md).

## <a name="learn-more-about-the-programming-models"></a>Meer informatie over de programmeer modellen
Service Fabric biedt meerdere manieren om uw services te schrijven en te beheren.  Hier vindt u een overzicht en conceptuele informatie over [stateless en stateful reliable Services](service-fabric-reliable-services-introduction.md), [reliable actors](service-fabric-reliable-actors-introduction.md), [containers](service-fabric-containers-overview.md), [uitvoer bare gast bestanden](service-fabric-guest-executables-introduction.md)en stateless [ASP.net Core Services](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Meer informatie over service communicatie
Een Service Fabric-toepassing bestaat uit verschillende services, waarbij elke service een gespecialiseerde taak uitvoert. Deze services kunnen met elkaar communiceren en er kunnen client toepassingen buiten het cluster zijn die verbinding maken met en communiceren met Services. Meer informatie over het [instellen van communicatie met en tussen uw services](service-fabric-connect-and-communicate-with-services.md) in service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Meer informatie over het configureren van de beveiliging van toepassingen
U kunt toepassingen die in het cluster worden uitgevoerd, beveiligen onder verschillende gebruikers accounts. Service Fabric helpt ook bij het beveiligen van de bronnen die worden gebruikt door toepassingen op het moment van de implementatie onder de gebruikers accounts, bijvoorbeeld bestanden, directory's en certificaten. Dit maakt het uitvoeren van toepassingen, zelfs in een gedeelde gehoste omgeving, veiliger van elkaar.  Meer informatie over het [configureren van beveiligings beleid voor uw toepassing](service-fabric-application-runas-security.md).

Uw toepassing kan gevoelige informatie bevatten, zoals verbindings reeksen voor opslag, wacht woorden of andere waarden die niet in tekst zonder opmaak moeten worden verwerkt. Meer informatie over het [beheren van geheimen in uw toepassing](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Meer informatie over de levens cyclus van toepassingen
Net als bij andere platforms gaat een Service Fabric-toepassing doorgaans door de volgende fasen: ontwerpen, ontwikkelen, testen, implementeren, bijwerken, onderhoud en verwijderen. [Dit artikel](service-fabric-application-lifecycle.md) bevat een overzicht van de api's en hoe deze worden gebruikt door de verschillende rollen in de fasen van de levens cyclus van de service Fabric-toepassing.

## <a name="learn-about-best-practices"></a>Meer informatie over best practices
Service Fabric heeft een aantal artikelen waarin [Aanbevolen procedures](./service-fabric-best-practices-overview.md)worden beschreven. Profiteer van deze informatie om ervoor te zorgen dat het cluster en de toepassing zo goed mogelijk worden uitgevoerd.
De besproken onderwerpen zijn onder andere:
* [Beveiliging](./service-fabric-best-practices-security.md)
* [Netwerken](./service-fabric-best-practices-networking.md)
* [Compute-planning en schalen](./service-fabric-best-practices-capacity-scaling.md)
* [Infrastructure als code](./service-fabric-best-practices-infrastructure-as-code.md)
* [Controle en diagnose](./service-fabric-best-practices-monitoring.md)
* [Toepassingsontwerp](./service-fabric-best-practices-applications.md)

Ook inbegrepen is een [controle lijst voor productie voorbereiding](./service-fabric-production-readiness-checklist.md) waarmee alle best practice informatie in een gemakkelijk te gebruiken indeling wordt geïntegreerd.

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Vragen of feedback?  Wilt u een probleem melden?
Lees de [Veelgestelde vragen](service-fabric-common-questions.md) en vind antwoorden op wat service Fabric kan doen en hoe dit moet worden gebruikt.

[Hand leidingen voor probleem oplossing](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides) kunnen nuttig zijn bij het vaststellen en oplossen van veelvoorkomende problemen in service Fabric clusters.

[Ondersteunings opties bieden](service-fabric-support.md) een lijst met forums over stack overflow en MSDN voor het stellen van vragen, evenals opties voor het rapporteren van problemen, het verkrijgen van ondersteuning en het indienen van product feedback.


## <a name="next-steps"></a>Volgende stappen
- [Maak een Windows-cluster in azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Visualiseer uw cluster, inclusief geïmplementeerde toepassingen en fysieke indeling, met [service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Versie en upgrade van uw services](service-fabric-application-upgrade-tutorial.md)