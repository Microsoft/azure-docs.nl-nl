---
title: Internetpeering vs. Peering Service
titleSuffix: Azure
description: Internetpeering vs. Peering Service
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: overview
ms.date: 5/22/2020
ms.author: derekol
ms.openlocfilehash: 717a8c87c6a1c22d3f75cd4e3054bfdf0c4b5f9a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023442"
---
# <a name="internet-peering-vs-peering-service"></a>Internetpeering vs. Peering Service

Internetpeering verwijst naar een willekeurige verbinding tussen het wereldwijde netwerk van Microsoft (AS8075) en het netwerk van serviceproviders. Een serviceprovider kan een peeringservicepartner worden door de vereisten voor peeringservicesamenwerking te implementeren die hieronder worden uitgelegd om betrouwbare en hoogwaardige openbare connectiviteit te bieden met optimale routering van de klant naar het Microsoft-netwerk.

## <a name="about-peering-service"></a>Info over Peering Service
Peering Service is een partnerprogramma met belangrijke serviceproviders om de beste, openbare internetverbinding te bieden aan hun zakelijke gebruikers. Partners die deel uitmaken van het programma hebben directe, maximaal beschikbare, geografisch redundante verbindingen met en geoptimaliseerde routering naar Microsoft. Peering Service is een aanvulling op het connectiviteitsportfolio van Microsoft:
*   ExpressRoute voor privéverbinding met IaaS- of PaaS-bronnen (ondersteuning voor privé-IP-ruimte)
    *   Verbinding op basis van partner
    *   Directe 100G-verbinding met Microsoft
*   IPSEC via internet voor VPN-verbinding met de cloud
*   SD-WAN-verbinding met Azure via virtueel WAN

Het doelsegment voor Peering Service is SaaS-connectiviteit, SD-WAN-klanten die bereid zijn om internetbreakout bij de vestiging te hebben en klanten die een MPLS met tweeledige strategie hebben en internet op bedrijfsniveau.

Het primaire doel bij het maken van verbinding met Microsoft Cloud moet het minimaliseren van de latentie zijn door de RTT (round-trip time) van een gebruikerssite naar het Microsoft Global Network te beperken. Het Microsoft Global Network is de openbare netwerkbackbone van Microsoft die alle datacentrums van Microsoft en meerdere ingangspunten voor cloudtoepassingen met elkaar verbindt. Zie [De beste verbinding en prestaties in Office 365 krijgen](https://techcommunity.microsoft.com/t5/Office-365-Blog/Getting-the-best-connectivity-and-performance-in-Office-365/ba-p/124694).

> [!div class="mx-imgBorder"]
> ![Afbeelding van gedistribueerde toegang](./media/distributed-access.png)

Op de bovenstaande afbeelding is elke vestiging van een wereldwijde onderneming verbonden met de dichtstbijzijnde Microsoft-locatie via het netwerk van de partner.

**Voordelen van Peering Service voor klanten:**
* Beste openbare routering via internet naar Microsoft Cloud Services voor optimale prestaties en betrouwbaarheid.
* De mogelijkheid om de gewenste serviceprovider te selecteren om verbinding te maken met Microsoft Cloud.
* Verkeersinzichten zoals latentierapportage en controle op voorvoegsels.
* Optimale netwerkhops (AS-hops) van Microsoft Cloud.
* Routeanalyse en -statistieken - gebeurtenissen voor afwijkingen in routes van Border Gateway Protocol ([BGP-](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) (lekken/detectie van overname) en suboptimale routering.

## <a name="peering-service-partnership-requirements"></a>Vereisten voor samenwerking met betrekking tot de Peering Service
* Verbinding met Microsoft Cloud op een locatie die het dichtst bij de klant ligt. Een partnerserviceprovider stuurt gebruikersverkeer naar Microsoft Edge die zich het dichtst bij de gebruiker bevindt. Op soortgelijke wijze stuurt Microsoft verkeer (met behulp van de BGP-tag) naar de dichtstbijzijnde locatie van de gebruiker en levert de serviceprovider het verkeer aan de gebruiker.
* De partner houdt maximaal beschikbare, hoge doorvoer en geo-redundante connectiviteit met Microsoft Global Network.
* De partner kan gebruikmaken van de bestaande peering om Peering Service te ondersteunen als deze aan de vereisten voldoet.

## <a name="faq"></a>Veelgestelde vragen
Zie [Peering Service: veelgestelde vragen](service-faqs.md) voor veelgestelde vragen.

## <a name="next-steps"></a>Volgende stappen

* Lees meer over de voordelen van klanten met [Peering Service](../peering-service/index.yml).
* Lees meer over de stappen voor het inschakelen van een directe peering voor Peering Service in [Overzicht van Peering Service-partners](walkthrough-peering-service-all.md).