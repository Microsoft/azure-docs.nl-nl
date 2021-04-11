---
title: Tolerantie en herstel na noodgevallen in Azure SignalR Service
description: Een overzicht over het instellen van meerdere exemplaren van SignalR-service om tolerantie en herstel na noodgevallen te bereiken
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 03/01/2019
ms.author: kenchen
ms.openlocfilehash: 996fa53aa105c0bcc27db7134c25d6d00e542a78
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110284"
---
# <a name="resiliency-and-disaster-recovery-in-azure-signalr-service"></a>Tolerantie en herstel na noodgevallen in Azure SignalR Service

Tolerantie en herstel na noodgevallen is een algemene behoefte voor online systemen. Azure SignalR-Service garandeert al een beschikbaarheid van 99,9%, maar het is nog steeds een regionale service.
Uw service-exemplaar wordt altijd uitgevoerd in één regio en failover naar een andere regio wanneer er sprake is van een storing in de hele regio.

In plaats daarvan biedt onze service-SDK een functionaliteit voor het ondersteunen van meerdere exemplaren van SignalR-service en automatisch overschakelen naar de andere exemplaren wanneer sommige hiervan niet beschikbaar zijn.
Met deze functie kunt u herstellen wanneer een nood geval plaatsvindt, maar u moet de juiste systeem topologie zelf instellen. U leert in dit document hoe dit moet.

## <a name="high-available-architecture-for-signalr-service"></a>Architectuur met hoge beschikbaarheid voor SignalR-service

Voor regio-overkoepelende tolerantie voor SignalR-service moet u meerdere exemplaren van de service instellen in verschillende regio's. Dus wanneer één regio niet actief is, kunnen de andere worden gebruikt als back-up.
Wanneer meerdere service-exemplaren verbinding maken met de appserver, zijn er twee rollen, de primaire en secundaire.
De primaire is een exemplaar dat online verkeer gebruikt en de secundaire is een volledig functioneel back-upexemplaar voor de primaire.
In de SDK-implementatie worden alleen primaire eindpunten geretourneerd, zodat in normale gevallen clients alleen verbinding maken met primaire eindpunten.
Maar als het primaire exemplaar niet actief is, worden secundaire eindpunten geretourneerd, zodat de client nog steeds verbinding kan maken.
Het primaire exemplaar en de app-server zijn verbonden via normale server verbindingen, maar het secundaire exemplaar en de app-server zijn verbonden via een speciaal verbindings type met de naam zwakke verbinding.
Het belangrijkste verschil van een zwakke verbinding is dat er geen routing van client verbindingen wordt geaccepteerd, omdat het secundaire exemplaar zich in een andere regio bevindt. Het routeren van een client naar een andere regio is geen optimale keuze (hogere latentie).

Een service-exemplaar kan verschillende rollen hebben bij het verbinden met meerdere app-servers.
Een typische installatie voor een scenario met meerdere regio’s is dat u twee (of meer) paren van SignalR service-exemplaren en app-servers heeft.
Binnen elke combinatie bevinden de app-server en SignalR-service zich in dezelfde regio en is SignalR-service verbonden met de app-server als een primaire rol.
Tussen alle paren zijn de app-server en SignalR-service ook verbonden, maar SignalR verandert in secundair bij het verbinden met de server in een andere regio.

Berichten van de ene server kunnen nog steeds worden afgeleverd bij alle clients als alle appservers en SignalR service-exemplaren onderling verbonden zijn met deze topologie.
Maar wanneer een client is verbonden, wordt deze altijd doorgestuurd naar de app-server in dezelfde regio om optimale netwerklatentie te bereiken.

Hieronder volgt een diagram waarin deze topologie wordt weergegeven:

![Diagram toont twee regio's elk met een app-server en een signalerings service, waarbij elke server wordt gekoppeld aan de seingevings service in de regio als primair en met de service in de andere regio als secundaire.](media/signalr-concept-disaster-recovery/topology.png)

## <a name="configure-multiple-signalr-service-instances"></a>Meerdere seingevings service-instanties configureren

Meerdere seingevings service-instanties worden ondersteund op zowel app-servers als Azure Functions.

Zodra u de Signa lering-service en app-servers/Azure Functions hebt gemaakt in elke regio, kunt u uw app-servers/Azure Functions configureren om verbinding te maken met alle seingevings service-exemplaren.

### <a name="configure-on-app-servers"></a>Configureren op app-servers
Er zijn twee manieren waarop u dit kunt doen:

#### <a name="through-config"></a>Via config

U moet al weten hoe u de seingevings service kunt instellen connection string via omgevings variabelen/app-instellingen/web. cofig, in een configuratie vermelding met de naam `Azure:SignalR:ConnectionString` .
Als u meerdere eindpunten hebt, kunt u ze instellen in meerdere configuratie-items, elk in de volgende indeling:

```
Azure:SignalR:ConnectionString:<name>:<role>
```

Hier is `<name>` de naam van het eindpunt en is `<role>` de rol (primaire of secundaire).
De naam is optioneel, maar dit is handig als u de werking van de routering tussen meerdere eindpunten verder wilt aanpassen.

#### <a name="through-code"></a>Via code

Als u de verbindings reeksen liever ergens anders wilt opslaan, kunt u deze ook in uw code lezen en als para meters gebruiken bij het aanroepen `AddAzureSignalR()` van (in ASP.net core) of `MapAzureSignalR()` (in ASP.net).

Hier volgt de voorbeeldcode:

ASP.NET Core:

```cs
services.AddSignalR()
        .AddAzureSignalR(options => options.Endpoints = new ServiceEndpoint[]
        {
            new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
            new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
        });
```

ASP.NET:

```cs
app.MapAzureSignalR(GetType().FullName, hub,  options => options.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
        new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
    };
```

U kunt meerdere primaire of secundaire exemplaren configureren. Als er meerdere primaire en/of secundaire instanties zijn, retourneert onderhandelen een eind punt in de volgende volg orde:

1. Als er ten minste één primair exemplaar online is, retourneert u een wille keurig primair online exemplaar.
2. Als alle primaire instanties niet beschikbaar zijn, retourneert u een wille keurig secundair online exemplaar.

### <a name="configure-on-azure-functions"></a>Configureren op Azure Functions
Zie [dit artikel](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/docs/sharding.md#configuration-method).

## <a name="failover-sequence-and-best-practice"></a>Failover-volgorde en best practice

U hebt nu de instelling van de juiste systeem-topologie. Wanneer een SignalR-service-exemplaar niet actief is, wordt online netwerkverkeer doorgestuurd naar de andere exemplaren.
Dit is wat er gebeurt wanneer een primaire instantie niet actief is (en na enige tijd herstelt):

1. Het primaire service-exemplaar is niet actief, alle serververbindingen op dit exemplaar worden verwijderd.
2. Alle servers die zijn verbonden met dit exemplaar zullen het markeren als offline, en het retourneren van dit eindpunt wordt gestopt en het retourneren van het secundaire eindpunt wordt gestart.
3. Alle clientverbindingen op dit exemplaar worden ook gesloten, clients worden opnieuw verbonden. Omdat app-servers nu het secundaire eindpunt retourneren, zullen clients verbinding met het secundaire exemplaar maken.
4. Het secundaire exemplaar neemt nu alle online verkeer over. Alle berichten van de server aan clients kunnen nog steeds worden afgeleverd als de secundaire is verbonden met alle app-servers. Maar de client naar server-berichten worden alleen naar de app-server in dezelfde regio gerouteerd.
5. Nadat de primaire instantie is hersteld en weer online is, brengt de app-server opnieuw verbindingen tot stand en markeert deze als online. Het primaire eindpunt wordt nu opnieuw geretourneerd zodat nieuwe clients weer met primair worden verbonden. Maar bestaande clients zullen niet worden verwijderd en worden gerouteerd naar secundair totdat ze dit zelf verbreken.

In de diagrammen hieronder ziet u hoe failover wordt uitgevoerd in de SignalR-service:

Fig. 1 Voor de failover ![Voor de failover](media/signalr-concept-disaster-recovery/before-failover.png)

Fig. 2 Na de failover ![Na de failover](media/signalr-concept-disaster-recovery/after-failover.png)

Fig.3 Kort nadat primair is hersteld ![Kort nadat primair is hersteld](media/signalr-concept-disaster-recovery/after-recover.png)

In normale gevallen ziet u dat alleen de primaire app-server en SignalR-service online verkeer hebben (in het blauw).
Na een failover zijn de secundaire app-server en SignalR-service ook van kracht.
Nadat de primaire SignalR-service weer online is, worden nieuwe clients verbonden met de primaire SignalR. Maar bestaande clients hebben nog steeds verbinding naar secundair, waardoor beide exemplaren verkeer hebben.
Nadat alle bestaande clients de verbinding verbreken, is uw systeem weer normaal (figuur 1).

Er zijn twee belangrijke patronen voor het implementeren van een regio-overkoepelende architectuur voor hoge beschikbaarheid:

1. Het eerste is een combinatie van een app-server en SignalR-service-exemplaar die al het online verkeer aannemen, en een ander paar als back-up (actief/passief genoemd, weergegeven in de figuur 1). 
2. Het andere is om twee (of meer) paren van app-servers en SignalR service-exemplaren te hebben, die elk deelnemen aan het online-verkeer en fungeren als back-up voor andere paren (actief/actief genoemd, vergelijkbaar met Fig.3).

De SignalR-service kan beide patronen ondersteunen, het belangrijkste verschil is hoe u app-servers implementeert.
Als app-servers actief/passief zijn, is SignalR-service ook actief/passief (omdat de primaire app-server alleen het primaire SignalR service-exemplaar retourneert).
Als app-servers actief/actief zijn, is SignalR-service ook actief/actief (omdat alle app-servers hun eigen primaire SignalR-instanties retourneren, zodat ze allemaal verkeer kunnen hebben).

Ongeacht welke patronen u wilt gebruiken, moet u elk exemplaar van de seingevings service verbinden met een app-server als primair.

Ook zullen clients vanwege de aard van SignalR-verbinding (dit is een lange verbinding), ondervinden dat de verbinding wordt verbroken wanneer er een calamiteit en failover plaatsvinden.
U moet dergelijke gevallen aan clientzijde afhandelen om de situatie transparant te maken voor uw eindgebruikers. Bijvoorbeeld, opnieuw verbinden nadat een verbinding is gesloten.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u uw toepassing moet configureren voor het bereiken van tolerantie voor SignalR-service. Voor meer informatie over server/client-verbindingen en verbindingsroutering in SignalR-service, kunt u [dit artikel](signalr-concept-internals.md) voor SignalR-service-inhoud lezen.

Voor schaal scenario's zoals sharding die meerdere instanties gebruiken voor het verwerken van grote hoeveel heden verbindingen, Lees [hoe u meerdere instanties kunt schalen](signalr-howto-scale-multi-instances.md).

Lees voor meer informatie over het configureren van Azure Functions met meerdere signaal service-instanties [meerdere service-exemplaren van Azure signalering in azure functions](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/docs/sharding.md).
