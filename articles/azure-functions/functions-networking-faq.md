---
title: Veelgestelde vragen over netwerken in Azure Functions
description: Antwoord op enkele van de meest voorkomende vragen en scenario's voor netwerken met Azure Functions.
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.reviewer: glenga
ms.openlocfilehash: 24afeeee3207127bb9404156dc390433671dd5da
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592299"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Veelgestelde vragen over netwerken in Azure Functions

In dit artikel vindt u een lijst met veelgestelde vragen over netwerken in Azure Functions. Zie [functions Network options](functions-networking-options.md)(Engelstalig) voor een uitgebreidere beschrijving.

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Hoe kan ik een statisch IP-adres instellen in functions?

Het implementeren van een functie in een App Service Environment is de primaire manier om statische binnenkomende en uitgaande IP-adressen voor uw functies te gebruiken. Voor meer informatie over het gebruik van een App Service Environment begint u met het artikel [een interne Load Balancer met een app service Environment maken en gebruiken](../app-service/environment/create-ilb-ase.md).

U kunt ook een NAT-gateway van het virtuele netwerk gebruiken om uitgaand verkeer te routeren via een openbaar IP-adres dat u beheert. Voor meer informatie, Zie [zelf studie: Control Azure Functionsing van het uitgaande IP-adres met een NAT-gateway van het virtuele netwerk van Azure](functions-how-to-use-nat-gateway.md). 

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Hoe kan ik Internet toegang tot mijn functie beperken?

U kunt Internet toegang op een aantal manieren beperken:

* [IP-beperkingen](../app-service/app-service-ip-restrictions.md): Beperk het binnenkomende verkeer naar uw functie-app op IP-bereik.
    * Onder IP-beperkingen kunt u ook [service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md)configureren, waardoor de functie wordt beperkt zodat alleen binnenkomend verkeer van een bepaald virtueel netwerk wordt geaccepteerd.
* Alle HTTP-triggers worden verwijderd. Voor sommige toepassingen is het voldoende om HTTP-triggers te vermijden en gebruik te maken van andere gebeurtenis bronnen om uw functie te activeren.

Houd er wel voor dat de Azure Portal editor rechtstreekse toegang tot uw actieve functie nodig heeft. Voor code wijzigingen via de Azure Portal is het apparaat dat u gebruikt om door de portal te bladeren, nodig om het IP-adres toe te voegen aan de lijst goedgekeurd. U kunt nog steeds iets gebruiken op het tabblad platform functies met netwerk beperkingen.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Hoe kan ik mijn functie-app beperken tot een virtueel netwerk?

U kunt **Inkomend** verkeer voor een functie-app beperken tot een virtueel netwerk met behulp van [service-eind punten](./functions-networking-options.md#use-service-endpoints). Met deze configuratie kan de functie-app nog steeds uitgaande aanroepen naar Internet maken.

Als u een functie wilt beperken waarmee al het verkeer via een virtueel netwerk wordt doorlopen, kunt u een [privé-eind punt](./functions-networking-options.md#private-endpoint-connections) met uitgaande virtuele netwerk integratie of een app service Environment gebruiken. Zie [Azure functions integreren met een virtueel Azure-netwerk met behulp van privé-eind punten](functions-create-vnet.md)voor meer informatie.

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Hoe kan ik toegang krijgen tot resources in een virtueel netwerk vanuit een functie-app?

U kunt toegang krijgen tot bronnen in een virtueel netwerk van een actieve functie met behulp van virtuele netwerk integratie. Zie [Virtual Network Integration](functions-networking-options.md#virtual-network-integration)(Engelstalig) voor meer informatie.

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Hoe kan ik toegang tot bronnen die worden beveiligd door service-eind punten?

Door gebruik te maken van virtuele netwerk integratie kunt u toegang krijgen tot service-Endpoint-beveiligde resources van een actieve functie. Zie [Virtual Network Integration](functions-networking-options.md#virtual-network-integration)(Engelstalig) voor meer informatie.

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Hoe kan ik een functie activeren vanuit een resource in een virtueel netwerk?

U kunt HTTP-triggers toestaan van een virtueel netwerk met behulp van [service-eind punten](./functions-networking-options.md#use-service-endpoints) of [verbindingen met een particulier eind punt](./functions-networking-options.md#private-endpoint-connections). 

U kunt ook een functie activeren vanuit alle andere resources in een virtueel netwerk door uw functie-app te implementeren in een Premium-abonnement, App Service plan of App Service Environment. Zie [niet-http-triggers voor virtuele netwerken](./functions-networking-options.md#virtual-network-triggers-non-http) voor meer informatie

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Hoe kan ik mijn functie-app in een virtueel netwerk implementeren?

Implementeren op een App Service Environment is de enige manier om een functie-app te maken die volledig in een virtueel netwerk is. Voor meer informatie over het gebruik van een interne load balancer met een App Service Environment, begint u met het artikel [een interne Load Balancer maken en gebruiken met een app service Environment](../app-service/environment/create-ilb-ase.md).

Zie het [Overzicht functies netwerken](functions-networking-options.md)voor scenario's waarbij u slechts één richting toegang wilt geven tot virtuele netwerk bronnen of minder uitgebreide netwerk isolatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over netwerken en functies: 

* [Volg de zelf studie over het aan de slag gaan met virtuele netwerk integratie](./functions-create-vnet.md)
* [Meer informatie over de netwerk opties in Azure Functions](./functions-networking-options.md)
* [Meer informatie over de integratie van virtuele netwerken met App Service en functions](../app-service/web-sites-integrate-with-vnet.md)
* [Meer informatie over virtuele netwerken in azure](../virtual-network/virtual-networks-overview.md)
* [Meer netwerk functies en-beheer inschakelen met App Service omgevingen](../app-service/environment/intro.md)
