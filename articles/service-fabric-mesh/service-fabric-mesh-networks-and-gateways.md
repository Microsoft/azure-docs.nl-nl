---
title: Inleiding tot Azure Service Fabric-netwerken
description: Meer informatie over netwerken, gateways en intelligente verkeers routering in Service Fabric net.
author: georgewallace
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: ef8b40be366afe24b960f7419d84cc2a9976b592
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625698"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Inleiding tot netwerken in Service Fabric-mesh toepassingen

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

In dit artikel worden verschillende soorten load balancers beschreven, hoe gateways het netwerk met uw toepassingen verbinden met andere netwerken en hoe verkeer wordt gerouteerd tussen de services in uw toepassingen.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Laag 4-load balancers Layer 3 vs Layer 7
Taak verdeling kan worden uitgevoerd op verschillende lagen in het [OSI-model](https://en.wikipedia.org/wiki/OSI_model) voor netwerken, vaak in laag 4 (N4) en Layer 7 (N7).  Normaal gesp roken zijn er twee typen load balancers:

- Een N4-load balancer werkt op de netwerk transport laag, die betrekking heeft op de levering van pakketten zonder rekening te houden met de inhoud van de pakketten. Alleen pakket headers worden geïnspecteerd door de load balancer, zodat de criteria voor de balans beperkt zijn tot IP-adressen en poorten. Een client maakt bijvoorbeeld een TCP-verbinding met de load balancer. De load balancer beëindigt de verbinding (door rechtstreeks te reageren op de SYN), selecteert een back-end en maakt een nieuwe TCP-verbinding met de back-end (stuurt een nieuwe SYN). Een N4-load balancer werkt normaal gesp roken alleen op het niveau van de N4 TCP/UDP-verbinding of-sessie. Het taak verdeling omleidt dus bytes rond en zorgt ervoor dat het aantal bytes van dezelfde sessie op dezelfde back-end ontstaat. De N4-load balancer is niet op de hoogte van toepassings Details van de bytes die worden verplaatst. De bytes kunnen elk toepassings protocol zijn.

- Een N7-load balancer werkt op de toepassingslaag, die de inhoud van elk pakket behandelt. De inhoud van het pakket wordt gecontroleerd omdat het protocollen zoals HTTP, HTTPS of websockets begrijpt. Dit geeft de load balancer de mogelijkheid om geavanceerde route ring uit te voeren. Een client maakt bijvoorbeeld één HTTP/2 TCP-verbinding met de load balancer. De load balancer vervolgens teruggaan om twee back-upverbindingen te maken. Wanneer de client twee HTTP/2-streams naar de load balancer verzendt, wordt de stream één naar de back-end verzonden en wordt de stroom twee naar de back-end verzonden. Zelfs multiplexing van clients met veel verschillende aanvragen worden geladen, worden op een efficiënte manier verdeeld over de back-end. 

## <a name="networks-and-gateways"></a>Netwerken en gateways
In het [service Fabric resource model](service-fabric-mesh-service-fabric-resources.md)is een netwerk resource een afzonderlijk Implementeer bare resource, onafhankelijk van een toepassings-of service resource die als afhankelijkheid ervan kan verwijzen. Het wordt gebruikt om een netwerk te maken voor uw toepassingen die zijn geopend op internet. Meerdere services van verschillende toepassingen kunnen deel uitmaken van hetzelfde netwerk. Dit particuliere netwerk wordt gemaakt en beheerd door Service Fabric en is geen virtueel Azure-netwerk (VNET). Toepassingen kunnen dynamisch worden toegevoegd en verwijderd uit de netwerk resource om VNET-connectiviteit in te scha kelen en uit te scha kelen. 

Een gateway wordt gebruikt voor het overbruggen van twee netwerken. De gateway resource implementeert een [Envoy-proxy](https://www.envoyproxy.io/) die N4-route ring biedt voor elk protocol en N7-route ring voor geavanceerde http (S)-toepassings routering. De gateway routeert verkeer naar uw netwerk vanaf een extern netwerk en bepaalt welke service verkeer moet routeren.  Het externe netwerk kan een open netwerk (in wezen, het open bare Internet) of een virtueel Azure-netwerk zijn, zodat u verbinding kunt maken met uw andere Azure-toepassingen en-resources. 

![Netwerk en gateway][Image1]

Wanneer de netwerk bron is gemaakt met `ingressConfig` , wordt een openbaar IP-adres toegewezen aan de netwerk bron. Het open bare IP-adres wordt gekoppeld aan de levens duur van de netwerk bron.

Wanneer een mesh-toepassing wordt gemaakt, moet deze verwijzen naar een bestaande netwerk bron. Nieuwe open bare poorten kunnen worden toegevoegd of bestaande poorten kunnen worden verwijderd uit de ingangs configuratie. Een Verwijder bewerking voor een netwerk bron mislukt als er wordt verwezen naar een toepassings bron. Wanneer de toepassing wordt verwijderd, wordt de netwerk bron verwijderd.

## <a name="next-steps"></a>Volgende stappen 
Lees het overzicht voor meer informatie over Service Fabric Mesh:
- [Service Fabric Mesh - overzicht](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png