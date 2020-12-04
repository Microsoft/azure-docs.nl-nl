---
title: Overzicht van Azure Service Fabric met API Management
description: Dit artikel is een inleiding tot het gebruik van Azure API Management als een gateway voor uw Service Fabric toepassingen.
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: 32f47d62cc9dda7cc88421dbf616bf69ffe152fc
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575683"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Overzicht van Service Fabric met Azure API Management

Cloudtoepassingen hebben meestal een gateway in de front-end nodig om een centraal ingangspunt te bieden voor gebruikers, apparaten of andere toepassingen. In Service Fabric kan een gateway een stateless service zoals een [ASP.net core toepassing](service-fabric-reliable-services-communication-aspnetcore.md), of een andere service die is ontworpen voor het binnenkomen van verkeer, zoals [Event hubs](../event-hubs/index.yml), [IOT hub](../iot-hub/index.yml)of [Azure API Management](../api-management/index.yml).

Dit artikel is een inleiding tot het gebruik van Azure API Management als een gateway voor uw Service Fabric toepassingen. API Management kan rechtstreeks worden geïntegreerd met Service Fabric, zodat u Api's kunt publiceren met een uitgebreide set routerings regels voor uw back-end-Service Fabric Services.

## <a name="availability"></a>Beschikbaarheid

> [!IMPORTANT]
> Deze functie is beschikbaar in de **Premium** -en **ontwikkelaars** lagen van API Management wegens de vereiste ondersteuning voor het virtuele netwerk.

## <a name="architecture"></a>Architectuur

Een gemeen schappelijke Service Fabric architectuur maakt gebruik van een webtoepassing met één pagina die HTTP-aanroepen naar back-end-services die HTTP-Api's beschikbaar stellen. In de [service Fabric aan de slag-voorbeeld toepassing](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) wordt een voor beeld van deze architectuur weer gegeven.

In dit scenario fungeert een stateless webservice als de gateway in de Service Fabric-toepassing. Voor deze methode moet u een webservice schrijven die HTTP-aanvragen voor back-end-services kan sturen, zoals wordt weer gegeven in het volgende diagram:

![Diagram dat laat zien hoe een stateless webservice fungeert als de gateway in de Service Fabric-toepassing.][sf-web-app-stateless-gateway]

Naarmate toepassingen complex worden, moet u dus de gateways die een API moeten presen teren voor talloze back-end-services. Azure API Management is ontworpen voor het verwerken van complexe Api's met routerings regels, Toegangs beheer, frequentie beperkingen, bewaking, gebeurtenis registratie en het opslaan van antwoorden in de cache met minimale werkzaamheden voor uw kant. Azure API Management ondersteunt Service Fabric service detectie, partitie resolutie en selectie van replica's om aanvragen op intelligente wijze rechtstreeks naar back-end-services in Service Fabric te sturen, zodat u geen eigen stateless API-gateway hoeft te schrijven. 

In dit scenario wordt de Web-UI nog steeds aangeboden via een webservice, terwijl HTTP API-aanroepen worden beheerd en gerouteerd via Azure API Management, zoals wordt weer gegeven in het volgende diagram:

![Diagram dat laat zien hoe de webgebruikersinterface nog steeds via een webservice wordt bediend, terwijl HTTP-API-aanroepen worden beheerd en gerouteerd via Azure API Management.][sf-apim-web-app]

## <a name="application-scenarios"></a>Toepassingsscenario's

Services in Service Fabric kunnen stateless of stateful zijn, en ze kunnen worden gepartitioneerd met behulp van een van de drie schema's: Singleton, int-64-bereik en met de naam. Voor de service-eindpunt resolutie moet een specifieke partitie van een specifiek service-exemplaar worden geïdentificeerd. Bij het omzetten van een eind punt van een service moet zowel de naam van het service-exemplaar (bijvoorbeeld `fabric:/myapp/myservice` ) als de specifieke partitie van de service worden opgegeven, behalve in het geval van een singleton-partitie.

Azure API Management kan worden gebruikt met elke combi natie van stateless Services, stateful Services en elk partitie schema.

## <a name="send-traffic-to-a-stateless-service"></a>Verkeer naar een stateless service verzenden

In het eenvoudigste geval wordt verkeer doorgestuurd naar een stateless service-exemplaar. Hiervoor bevat een API Management-bewerking een beleid voor inkomende verwerking met een Service Fabric back-end dat is gekoppeld aan een specifiek stateless service exemplaar in Service Fabric back-end. Aanvragen die worden verzonden naar deze service, worden verzonden naar een wille keurig exemplaar van de service.

**Voorbeeld**

In het volgende scenario bevat een Service Fabric toepassing een stateless service met de naam `fabric:/app/fooservice` , waarmee een interne HTTP-API wordt weer gegeven. De naam van het service-exemplaar is goed bekend en kan rechtstreeks in het API Management beleid voor inkomende verwerking worden vastgelegd. 

![Diagram met een Service Fabric toepassing bevat een stateless service dat een interne HTTP-API weergeeft.][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Verkeer naar een stateful service verzenden

Net als bij het stateless service scenario kan verkeer worden doorgestuurd naar een stateful service-exemplaar. In dit geval bevat een API Management-bewerking een beleid voor inkomende verwerking met een Service Fabric back-end waarmee een aanvraag wordt toegewezen aan een specifieke partitie van een specifiek *stateful* service-exemplaar. De partitie waarmee elke aanvraag wordt toegewezen, wordt via een lambda-methode berekend met behulp van een invoer van de binnenkomende HTTP-aanvraag, zoals een waarde in het URL-pad. Het beleid kan zodanig worden geconfigureerd dat alleen aanvragen naar de primaire replica worden verzonden of naar een wille keurige replica voor lees bewerkingen.

**Voorbeeld**

In het volgende scenario bevat een Service Fabric toepassing een gepartitioneerd stateful service met een naam `fabric:/app/userservice` die een interne HTTP-API beschrijft. De naam van het service-exemplaar is goed bekend en kan rechtstreeks in het API Management beleid voor inkomende verwerking worden vastgelegd.  

De service is gepartitioneerd met behulp van het Int64-partitie schema met twee partities en een sleutel bereik dat `Int64.MinValue` tot `Int64.MaxValue` . Het back-end-beleid berekent een partitie sleutel binnen dat bereik door de `id` waarde die is verstrekt in het URL-verzoek-pad, te converteren naar een 64-bits geheel getal, hoewel elk algoritme hier kan worden gebruikt om de partitie sleutel te berekenen. 

![Overzicht van Service Fabric met Azure API Management topologie][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Verkeer verzenden naar meerdere stateless Services

In meer geavanceerde scenario's kunt u een API Management bewerking definiëren waarmee aanvragen worden toegewezen aan meer dan één service-exemplaar. In dit geval bevat elke bewerking een beleid waarmee aanvragen worden toegewezen aan een specifiek service-exemplaar op basis van waarden uit de binnenkomende HTTP-aanvraag, zoals het URL-pad of de query reeks, en in het geval van stateful Services, een partitie binnen het service-exemplaar.

Hiervoor bevat een API Management-bewerking een beleid voor inkomende verwerking met een Service Fabric back-end dat is gekoppeld aan een stateless service-exemplaar in Service Fabric back-end op basis van waarden die zijn opgehaald uit de binnenkomende HTTP-aanvraag. Aanvragen voor een service worden verzonden naar een wille keurig exemplaar van de service.

**Voorbeeld**

In dit voor beeld wordt een nieuw stateless service exemplaar gemaakt voor elke gebruiker van een toepassing met een dynamisch gegenereerde naam met behulp van de volgende formule:

- `fabric:/app/users/<username>`

  Elke service heeft een unieke naam, maar de namen zijn niet vooraf bekend, omdat de services worden gemaakt in reactie op de invoer van gebruikers of beheerders en dus niet kunnen worden vastgelegd in APIM-beleid of routerings regels. In plaats daarvan wordt de naam van de service waarnaar een aanvraag wordt verzonden, gegenereerd in de definitie van het back-end-beleid op basis van de `name` waarde die is ingesteld in het URL-verzoek-pad. Bijvoorbeeld:

  - Een aanvraag om `/api/users/foo` naar het service-exemplaar te worden doorgestuurd `fabric:/app/users/foo`
  - Een aanvraag om `/api/users/bar` naar het service-exemplaar te worden doorgestuurd `fabric:/app/users/bar`

![Diagram waarin een voor beeld wordt weer gegeven waarin een nieuw stateless service exemplaar wordt gemaakt voor elke gebruiker van een toepassing met een dynamisch gegenereerde naam.][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Verkeer verzenden naar meerdere stateful Services

Net als bij het stateless service-voor beeld kan een API Management bewerking aanvragen toewijzen aan meer dan één **stateful** service-exemplaar. in dat geval moet u ook mogelijk een partitie resolutie voor elk stateful service exemplaar uitvoeren.

Hiervoor bevat een API Management-bewerking een beleid voor inkomende verwerking met een Service Fabric back-end dat is gekoppeld aan een stateful service-exemplaar in Service Fabric back-end op basis van waarden die zijn opgehaald uit de binnenkomende HTTP-aanvraag. Naast het toewijzen van een aanvraag aan een specifiek service-exemplaar, kan de aanvraag ook worden toegewezen aan een specifieke partitie binnen het service-exemplaar en eventueel aan de primaire replica of een wille keurige secundaire replica binnen de partitie.

**Voorbeeld**

In dit voor beeld wordt een nieuw stateful service exemplaar gemaakt voor elke gebruiker van de toepassing met een dynamisch gegenereerde naam met behulp van de volgende formule:

- `fabric:/app/users/<username>`

  Elke service heeft een unieke naam, maar de namen zijn niet vooraf bekend, omdat de services worden gemaakt in reactie op de invoer van gebruikers of beheerders en dus niet kunnen worden vastgelegd in APIM-beleid of routerings regels. In plaats daarvan wordt de naam van de service waarnaar een aanvraag wordt verzonden, gegenereerd in de definitie van het back-end-beleid op basis van de `name` waarde van het URL-aanvraag pad. Bijvoorbeeld:

  - Een aanvraag om `/api/users/foo` naar het service-exemplaar te worden doorgestuurd `fabric:/app/users/foo`
  - Een aanvraag om `/api/users/bar` naar het service-exemplaar te worden doorgestuurd `fabric:/app/users/bar`

Elk service-exemplaar wordt ook gepartitioneerd met behulp van het Int64-partitie schema met twee partities en een belang rijk bereik van `Int64.MinValue` `Int64.MaxValue` . Het back-end-beleid berekent een partitie sleutel binnen dat bereik door de `id` waarde die is verstrekt in het URL-verzoek-pad, te converteren naar een 64-bits geheel getal, hoewel elk algoritme hier kan worden gebruikt om de partitie sleutel te berekenen. 

![Diagram waarin wordt weer gegeven dat elk service-exemplaar ook is gepartitioneerd met behulp van het Int64-partitie schema met twee partities en een sleutel bereik dat Int64. MinValue overspant naar Int64. MaxValue.][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Volgende stappen

Volg de [zelf studie](service-fabric-tutorial-deploy-api-management.md) om uw eerste service Fabric cluster in te stellen met API management-en flow-aanvragen via API Management naar uw services.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png
