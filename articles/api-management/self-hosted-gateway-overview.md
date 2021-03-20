---
title: Zelf-hostende gateway-overzicht | Microsoft Docs
description: Meer informatie over hoe zelf-hostende gateway functies van Azure API Management organisaties kunnen helpen bij het beheren van Api's in hybride en omgevingen met multiclouds.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/25/2021
ms.author: apimpm
ms.openlocfilehash: 48abce693ca22163c0a1742ba71faf36fc6156a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99989098"
---
# <a name="self-hosted-gateway-overview"></a>Overzicht van zelf-hostende gateway

In dit artikel wordt uitgelegd hoe zelf-hostende gateway functies van Azure API Management hybride en API-beheer met meerdere clouds mogelijk maken, de architectuur op hoog niveau wordt gepresenteerd en de mogelijkheden ervan worden benadrukt.

## <a name="hybrid-and-multi-cloud-api-management"></a>Hybride en API-beheer met meerdere clouds

De zelf-hostende gateway functie breidt API Management ondersteuning uit voor hybride en omgevingen met meerdere clouds en stelt organisaties in staat om op efficiënte en veilige wijze Api's te beheren die on-premises en tussen Clouds worden gehost vanuit één API Management service in Azure.

Met de zelf-hostende gateway hebben klanten de flexibiliteit om een container versie van het onderdeel API Management Gateway te implementeren in dezelfde omgevingen waar ze hun Api's hosten. Alle zelf-hostende gateways worden beheerd vanuit de API Management-service waarmee ze zijn federatief, waardoor klanten de zicht baarheid en Unified management-ervaring bieden voor alle interne en externe Api's. Als u de gateways dicht bij de Api's plaatst, kunnen klanten het API-verkeer optimaliseren en voldoen aan de vereisten voor beveiliging en naleving.

Elke API Management-service bestaat uit de volgende belang rijke onderdelen:

-   Beheer vlak, beschikbaar als een API, dat wordt gebruikt om de service te configureren via de Azure Portal, Power shell en andere ondersteunde mechanismen.
-   De gateway (of het gegevens vlak) is verantwoordelijk voor de API-aanvragen van de proxy, het Toep assen van beleid en het verzamelen van telemetrie
-   Ontwikkelaars portal die door ontwikkel aars wordt gebruikt voor het detecteren, leren en onboarden van de Api's

Standaard worden al deze onderdelen geïmplementeerd in azure, waardoor alle API-verkeer (weer gegeven als effen zwarte pijlen op de onderstaande afbeelding) wordt uitgevoerd om door Azure te stromen, ongeacht waar de back-ends die de Api's implementeren, worden gehost. De operationele eenvoud van dit model is de kosten van verhoogde latentie, nalevings problemen en in sommige gevallen extra kosten voor gegevens overdracht.

![API-verkeers stroom zonder zelf-hostende gateways](media/self-hosted-gateway-overview/without-gateways.png)

Als u zelf-hostende gateways implementeert in dezelfde omgevingen waarin de back-end-API-implementaties worden gehost, kan API-verkeer rechtstreeks naar de back-end-Api's stromen, waardoor de latentie wordt verbeterd, de kosten voor gegevens overdracht worden geoptimaliseerd en de naleving wordt gehandhaafd en de voor delen van het beheer van alle Api's binnen de organisatie worden gehost.

![API-verkeer stroom met zelf-hostende gateways](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Verpakking en functies

De zelf-hostende gateway is een container, functioneel equivalente versie van de beheerde gateway die is geïmplementeerd in azure als onderdeel van elke API Management service. De zelf-hostende gateway is beschikbaar als een op Linux gebaseerde docker- [container](https://aka.ms/apim/sputnik/dhub) vanuit de micro soft-container Registry. Het kan worden geïmplementeerd voor docker, Kubernetes of een andere oplossing voor container indeling die wordt uitgevoerd op een server cluster op locatie, Cloud infrastructuur, of voor evaluatie-en ontwikkelings doeleinden, op een personal computer.

De volgende functionaliteit gevonden in de beheerde gateways is **niet beschikbaar** in de zelf-hostende gateways:

- Azure Monitor-logboeken
- Upstream (back-end) TLS-versie en versleutelings beheer
- Validatie van server-en client certificaten met [CA-basis certificaten](api-management-howto-ca-certificates.md) die zijn geüpload naar API Management service. Zie [certificaat validatie in zelf-hostende gateway](api-management-howto-mutual-certificates-for-clients.md#certificate-validation-in-self-hosted-gateway)voor meer informatie.
- Integratie met de [service Fabric](../service-fabric/service-fabric-api-management-overview.md)
- Hervatting van TLS-sessies
- Opnieuw onderhandelen van client certificaat. Dit betekent dat voor [verificatie van client certificaten](api-management-howto-mutual-certificates-for-clients.md) aan work API-consumers hun certificaten moeten worden weer gegeven als onderdeel van de eerste TLS-handshake. Schakel de instelling onderhandelen client certificaat in bij het configureren van een zelf-hostende gateway aangepaste hostnaam.
- Ingebouwde cache. Raadpleeg dit [document](api-management-howto-cache-external.md) voor meer informatie over het gebruik van externe cache in zelf-hostende gateways.

## <a name="connectivity-to-azure"></a>Connectiviteit met Azure

Voor zelf-hostende gateways is uitgaande TCP/IP-verbinding met Azure op poort 443 vereist. Elke zelf-hostende gateway moet worden gekoppeld aan een enkele API Management-service en via het beheer vlak worden geconfigureerd. Zelf-hostende gateway maakt gebruik van connectiviteit met Azure voor:

-   De status rapporteren door per minuut heartbeat-berichten te verzenden
-   Regel matig controleren op (elke 10 seconden) en configuratie-updates Toep assen wanneer deze beschikbaar zijn
-   Het verzenden van Logboeken en metrische gegevens voor aanvragen naar Azure Monitor, indien geconfigureerd om dit te doen
-   Gebeurtenissen verzenden naar Application Insights, indien ingesteld op...

Wanneer de verbinding met Azure is verbroken, kunnen zelf-hostende gateways geen configuratie-updates ontvangen, de status van het rapport rapporteren of telemetrie uploaden.

De zelf-hostende gateway is ontworpen om statisch te mislukken en kan het tijdelijke verlies van de verbinding met Azure overlaten. Het kan worden geïmplementeerd met of zonder lokale configuratie back-up. In het eerste geval worden zelf-hostende gateways regel matig een back-up opgeslagen van de meest recente gedownloade configuratie op een permanent volume dat is gekoppeld aan de container of pod.

Wanneer de configuratie back-up is uitgeschakeld en de verbinding met Azure wordt onderbroken:

-   Het uitvoeren van zelf-hostende gateways blijft werken met een kopie in het geheugen van de configuratie
-   Gestopt zelf-hostende gateways kunnen niet worden gestart

Wanneer configuratie back-up is ingeschakeld en de verbinding met Azure wordt onderbroken:

-   Het uitvoeren van zelf-hostende gateways blijft werken met een kopie in het geheugen van de configuratie
-   Gestopt zelf-hostende gateways kunnen beginnen met het gebruik van een back-up van de configuratie

Wanneer de verbinding wordt hersteld, zal elke zelf-hostende gateway die wordt beïnvloed door de storing automatisch opnieuw verbinding maken met de bijbehorende API Management-service en alle configuratie-updates downloaden die zijn opgetreden terwijl de gateway ' offline ' was.

## <a name="next-steps"></a>Volgende stappen

-   [Lees een technisch document voor aanvullende achtergrond informatie over dit onderwerp](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Zelf-hostende Gateway implementeren naar docker](how-to-deploy-self-hosted-gateway-docker.md)
-   [Zelf-hostende Gateway implementeren op Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
