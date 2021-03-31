---
title: Azure front-deur-routerings architectuur | Microsoft Docs
description: Dit artikel helpt u bij het begrijpen van het algemene weergave aspect van de architectuur van de voor deur.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 948cf3c65dfdc912f2f807dfac34076985f1bc89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91449200"
---
# <a name="routing-architecture-overview"></a>Overzicht van routerings architectuur

Wanneer Azure front-deur uw client aanvragen ontvangt, worden er twee dingen gedaan. U kunt deze antwoorden als u caching inschakelt of deze naar de juiste back-end van de toepassing stuurt als een omgekeerde proxy.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>De voor deur omgeving voor verkeers routering (anycast) selecteren

Verkeer dat wordt gerouteerd naar de Azure front-deur omgevingen, gebruikt [anycast](https://en.wikipedia.org/wiki/Anycast) voor zowel het DNS-(Domain Name System) als het http-verkeer (Hypertext Transfer Protocol), waarmee gebruikers aanvragen de dichtstbijzijnde omgeving kunnen bereiken in de minste netwerk-hops. Deze architectuur biedt betere retour tijden voor eind gebruikers door de voor delen van de split-TCP te maximaliseren. Met de voor deur worden de omgevingen in primaire en terugval ' belsignalen ' ingedeeld. De buitenste ring heeft omgevingen die dichter bij gebruikers zijn en die lagere latenties bieden.  De interne ring heeft omgevingen die de failover voor de buitenste ring omgeving kunnen afhandelen wanneer er problemen optreden. De buitenste ring is het voorkeurs doel voor al het verkeer en de binnenste ring is het verwerken van verkeers overloop van de buitenste ring. Aan elke frontend-host of het domein dat wordt bediend door de voor deur, wordt een primaire VIP (virtuele Internet Protocol-adressen) toegewezen, die wordt aangekondigd door omgevingen in zowel de binnenste als buitenste ring. Een terugval-VIP wordt alleen aangekondigd door omgevingen in de binnenste ring. 

Deze architectuur zorgt ervoor dat aanvragen van uw eind gebruikers altijd de dichtstbijzijnde front-deur omgeving bereiken. Zelfs als de voorkeurs omgeving van de voor deur niet in orde is, wordt al het verkeer automatisch verplaatst naar de volgende dichtstbijzijnde omgeving.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Verbinding maken met de front-deur omgeving (gesplitste TCP)

[Gesplitste TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) is een techniek om LATENTIE en TCP-problemen te reduceren door een verbinding te verbreken waardoor een hoge retour tijd in kleinere delen zou ontstaan. Met de front-deur omgevingen dichter bij eind gebruikers worden TCP-verbindingen in de front-deur omgeving beëindigd. Een TCP-verbinding met een grote round-trip tijd (RTT) naar de back-end van de toepassing wordt opgesplitst in twee afzonderlijke verbindingen. De korte verbinding tussen de eind gebruiker en de voor deur houdt in dat de verbinding tot stand wordt gebracht via drie korte retour waarden in plaats van drie lange retour reizen, wat leidt tot een latentie bij het opslaan. De ' lange verbinding ' tussen de front-deur omgeving en de back-end kan vooraf worden ingesteld en vervolgens opnieuw worden gebruikt voor andere eind gebruikers aanvragen om connectiviteits tijd op te slaan. Het effect van de gesplitste TCP wordt vermenigvuldigd wanneer een SSL/TLS-verbinding (Transport Layer Security) tot stand wordt gebracht, omdat er meer ronde trips zijn om een verbinding te beveiligen.

## <a name="processing-request-to-match-a-routing-rule"></a>De aanvraag wordt verwerkt om te voldoen aan een routerings regel
Na het tot stand brengen van een verbinding en het volt ooien van een TLS-Handshake, is de eerste stap na een aanvraag land op een front-deur omgeving gelijk aan de routerings regel. De overeenkomst wordt bepaald door configuraties op de voor deur waaraan een bepaalde routerings regel moet voldoen om de aanvraag af te stemmen. Lees meer informatie over de wijze waarop de voor deur de [route afstemming](front-door-route-matching.md) biedt.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Beschik bare back-end in de back-end-pool identificeren voor de routerings regel
Zodra de voor deur overeenkomt met een routerings regel voor een binnenkomende aanvraag, is de volgende stap het ophalen van de status van de status test voor de back-end-groep die is gekoppeld aan de routerings regel als er geen caching is. Meer informatie over de status van de back-end van de voor deur wordt gecontroleerd met behulp van [Health probe](front-door-health-probes.md) .

## <a name="forwarding-the-request-to-your-application-backend"></a>De aanvraag door sturen naar de back-end van uw toepassing
Ten slotte, als caching niet is geconfigureerd, wordt de gebruikers aanvraag doorgestuurd naar de ' beste ' back-end op basis van de configuratie van de [routerings methode](front-door-routing-methods.md) .

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
