---
title: Peering instellen met Microsoft
titleSuffix: Azure
description: Overzicht van peering
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 8f960e58d47963cbb239d498af52e7adfc77caa9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97586863"
---
# <a name="internet-peering-overview"></a>Overzicht van internetpeering

Peering is de onderlinge verbinding tussen het wereldwijde netwerk (AS8075) van Microsoft en uw netwerk voor het uitwisselen van internetverkeer van/naar Microsoft Online Services en Microsoft Azure Services. Mobiele providers of serviceproviders kunnen verbinding met Microsoft aanvragen op een van onze Edge-locaties. Elke aanvraag wordt door Microsoft beoordeeld om te zien of deze voldoet aan ons peeringbeleid. U kunt een peering met het netwerk van Microsoft instellen op twee manieren:

* **Directe peering:**

    Peering wordt tot stand gebracht via rechtstreekse fysieke verbindingen tussen het Microsoft-netwerk op een Microsoft Edge en uw netwerk. BGP-sessies worden geconfigureerd via deze verbindingen volgens ons routeringsbeleid en met behulp van een vooraf onderhandelde overeenkomst. Dit wordt ook wel PNI genoemd.

* **Exchange-peering:**

    Dit verwijst naar de standaard openbare peering-verbindingen op Internet Exchanges (IX). De fysieke verbindingen tussen het Microsoft-netwerk en uw netwerk lopen via de switch-infrastructuur die wordt beheerd door de IX. BGP-sessies worden geconfigureerd met behulp van de IP-ruimte die wordt geleverd door de IX.

## <a name="benefits-of-peering-with-microsoft"></a>Voordelen van peering met Microsoft
* Verlaag uw doorvoerkosten door Microsoft-verkeer te leveren met behulp van peering met Microsoft.
* Verbeter de prestaties van uw klanten door de netwerk-hops en latentie naar het Microsoft Edge-netwerk te verminderen.
* Bescherm het verkeer van klanten tegen storingen in uw netwerk of het netwerk van de provider door peering met Microsoft op redundante locaties.
* Meer informatie over metrische prestatiegegevens van uw peering-verbindingen en hoe u inzichten kunt gebruiken om problemen met uw netwerk op te lossen.

## <a name="benefits-of-using-azure-to-set-up-peering"></a>Voordelen van het gebruik van Azure voor het instellen van peering

U kunt peering met Microsoft aanvragen via Azure PowerShell of Azure Portal. Peering die op deze manier is ingesteld, wordt beheerd als een Azure-resource en biedt de volgende voordelen:
* Vereenvoudigde en automatiseerbare stappen voor het instellen en beheren van peering met Microsoft.
* Snelle en eenvoudige manier om al uw peerings op één plaats weer te geven en te beheren.
* Houd de status- en bandbreedtegegevens voor al uw verbindingen bij.
* U kunt hetzelfde abonnement gebruiken voor toegang tot uw Azure Cloud Services.

Als u al peerings met Microsoft tot stand hebt gebracht, worden deze aangeduid als **verouderde peerings**. U kunt ervoor kiezen om dergelijke peerings als Azure-resource te beheren, zodat u optimaal kunt profiteren van de bovenstaande voordelen. Als u een nieuwe peeringaanvraag wilt indienen of een verouderde peering wilt converteren naar een Azure-resource, gebruikt u de koppelingen in de sectie **Volgende stappen** hieronder.

## <a name="peering-policy"></a>Peeringbeleid
Microsoft heeft een selectief, maar doorgaans open peeringbeleid. Peers worden geselecteerd op basis van de prestaties, de mogelijkheden en de plaats waar er sprake is van wederzijds voordeel. Peers zijn onderworpen aan bepaalde technische, commerciële en juridische vereisten. Zie het [peeringbeleid](policy.md) voor meer informatie.

## <a name="faq"></a>Veelgestelde vragen
Zie [Veelgestelde vragen over internetpeering](faqs.md) voor veelgestelde vragen over peering.

## <a name="next-steps"></a>Volgende stappen

* Voor meer informatie over de stappen voor het instellen van directe peering met Microsoft, volgt u het [directe peering-scenario](walkthrough-direct-all.md)
* Voor meer informatie over de stappen voor het instellen van Exchange-peering met Microsoft, volgt u de [kennismaking met Exchange-peering](walkthrough-exchange-all.md)
* Informatie over enkele van de andere belangrijke [netwerkmogelijkheden](../networking/networking-overview.md) van Azure.