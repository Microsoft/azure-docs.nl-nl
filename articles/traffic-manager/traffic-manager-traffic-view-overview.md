---
title: Verkeersweergave in azure Traffic Manager
description: In deze inleiding leert u hoe Traffic Manager Traffic View werkt.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 01/22/2021
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 66376655c61903761d93ea228c6d72fa05734353
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743046"
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager Verkeersweergave

Traffic Manager biedt u de route ring van DNS-(Domain Name System)-niveaus. Met deze service kunnen uw eind gebruikers worden omgeleid naar gezonde eind punten op basis van de routerings methode van uw keuze. Verkeersweergave biedt Traffic Manager met een weer gave van de gebruikers bases (op een granulatie niveau van de DNS-resolver) en hun verkeers patroon. Als u Verkeersweergave inschakelt, wordt deze informatie verwerkt om u te voorzien van inzicht in de praktijk. 

Met behulp van Verkeersweergave kunt u het volgende doen:
- Krijg inzicht in de locatie waar uw gebruikers zich bevinden (tot een lokale granulatie van het DNS-resolver niveau).
- Bekijk het volume verkeer (geobserveerd als DNS-query's die zijn verwerkt door Azure Traffic Manager) die afkomstig zijn uit deze regio's.
- Krijg inzicht in wat is de representatieve latentie van deze gebruikers.
- dieper in de specifieke verkeers patronen van elk van deze gebruikers bases naar Azure-regio's waar u eind punten hebt. 

U kunt bijvoorbeeld Verkeersweergave gebruiken om te begrijpen welke regio's een grote hoeveelheid verkeer hebben, maar wel van hogere latenties. Vervolgens gebruikt u deze informatie om uw footprint uit te breiden naar nieuwe Azure-regio's. Op die manier hebben uw gebruikers een lagere latentie-ervaring.

## <a name="how-traffic-view-works"></a>Hoe Verkeersweergave werkt

Verkeersweergave werkt door de binnenkomende query's te bekijken die in de afgelopen zeven dagen voor een profiel zijn ontvangen. Van de gegevens van binnenkomende query's haalt Verkeersweergave het bron-IP-adres van de DNS-resolver op dat wordt gebruikt om de locatie van de gebruikers weer te geven. Deze informatie wordt gegroepeerd op het niveau van de DNS-resolver om gebruikers basis regio's te maken. Traffic Manager houdt de geografische gegevens van IP-adressen bij. Traffic Manager vervolgens naar de Azure-regio's waarnaar de query wordt gerouteerd, en wordt er een Traffic Flow-kaart voor gebruikers uit deze regio's gemaakt.
 
In de volgende stap verwijst Traffic Manager de basis regio van de gebruiker aan de Azure-regio toewijzing met de tabellen met netwerk Intelligence-latentie. Deze tabel wordt onderhouden voor verschillende netwerken voor eind gebruikers om inzicht te krijgen in de gemiddelde latentie van gebruikers uit deze regio's wanneer er verbinding wordt gemaakt met Azure-regio's. Al deze berekeningen worden vervolgens gecombineerd op basis van een lokaal IP-adres voor de DNS-resolver voordat deze wordt weer gegeven. U kunt de informatie op verschillende manieren gebruiken.

De frequentie van het bijwerken van de verkeers weergave gegevens is afhankelijk van meerdere interne service variabelen. De gegevens worden echter elke 24 uur bijgewerkt.

>[!NOTE]
>De latentie die wordt beschreven in Verkeersweergave is een representatieve latentie tussen de eind gebruiker en de Azure-regio's waarmee ze zijn verbonden, en is niet de latentie van de DNS-zoek opdracht. Verkeersweergave maakt een beste schatting van de latentie tussen de lokale DNS-resolver en de Azure-regio waarnaar de query is doorgestuurd als er onvoldoende gegevens beschikbaar zijn, is de geretourneerde latentie null. 

## <a name="visual-overview"></a>Overzicht van visuele elementen

Wanneer u naar de sectie **Verkeersweergave** op de pagina Traffic Manager gaat, ziet u een geografische kaart met een overlay van Verkeersweergave Insights. De kaart bevat informatie over de gebruikers basis en-eind punten voor uw Traffic Manager profiel.

![Traffic Manager Verkeersweergave geografische weer gave][1]

### <a name="user-base-information"></a>Gebruikers basis gegevens

Voor lokale DNS-resolvers waarvoor locatie gegevens beschikbaar zijn, worden ze weer gegeven in de kaart. De kleur van de DNS-resolver duidt op de gemiddelde latentie van eind gebruikers die de DNS-resolver voor hun Traffic Manager query's hebben gebruikt.

Als u de muis aanwijzer over een locatie van een DNS-resolver in de kaart houdt, wordt het volgende weer gegeven:
- het IP-adres van de DNS-resolver
- het volume van de DNS-query verkeer dat door Traffic Manager wordt weer gegeven
- de eind punten waarop het verkeer van de DNS-resolver is doorgestuurd, als een lijn tussen het eind punt en de DNS-resolver 
- de gemiddelde latentie van die locatie naar het eind punt, weer gegeven als de kleur van de lijn waarmee ze worden verbonden

### <a name="endpoint-information"></a>Eindpunt gegevens

De Azure-regio's waarin de eind punten zich bevinden, worden weer gegeven als blauwe stippen in de kaart. Als uw eind punt extern is en er geen Azure-regio aan is toegewezen, worden deze bovenaan de kaart weer gegeven. Selecteer een eind punt om de verschillende locaties te bekijken (op basis van de gebruikte DNS-resolver) waar verkeer naar dat eind punt werd gestuurd. De verbindingen worden weer gegeven als een lijn tussen het eind punt en de locatie van de DNS-resolver. Ze zijn gekleurd op basis van de representatieve latentie tussen dat paar. U ziet de naam van het eind punt en de Azure-regio waarin deze wordt uitgevoerd. Het totale volume aan aanvragen dat door dit Traffic Manager profiel wordt doorgestuurd, worden ook weer gegeven.


## <a name="tabular-listing-and-raw-data-download"></a>Lijst met tabellaire en onbewerkte gegevens downloaden

U kunt de Verkeersweergave gegevens weer geven in een tabel indeling in Azure Portal. Er is een vermelding voor een IP/eind punt paar van de DNS-resolver dat er als volgt uitziet:

* Het IP-adres van de DNS-resolver.
* De naam.
* De geografische locatie van de Azure-regio waarin het eind punt zich bevindt (indien beschikbaar).
* Het volume aan aanvragen dat is gekoppeld aan die DNS-resolver voor dat eind punt.
* De representatieve latentie die is gekoppeld aan eind gebruikers die de DNS gebruiken (indien beschikbaar). 

U kunt de Verkeersweergave gegevens ook downloaden als een CSV-bestand dat kan worden gebruikt als onderdeel van een analytische werk stroom van uw keuze.

## <a name="billing"></a>Billing

Wanneer u Verkeersweergave gebruikt, wordt u gefactureerd op basis van het aantal gegevens punten dat wordt gebruikt om de weer gegeven inzichten te maken. Op dit moment is het enige type gegevens punt dat wordt gebruikt de query's die worden ontvangen voor uw Traffic Manager profiel. Ga naar de [pagina met Traffic Manager prijzen](https://azure.microsoft.com/pricing/details/traffic-manager/)voor meer informatie over de prijzen.

## <a name="faqs"></a>Veelgestelde vragen

* [Wat doet Verkeersweergave?](./traffic-manager-faqs.md#what-does-traffic-view-do)

* [Hoe kan ik profiteren van het gebruik van Verkeersweergave?](./traffic-manager-faqs.md#how-can-i-benefit-from-using-traffic-view)

* [Hoe wijkt Verkeersweergave af van de Traffic Manager metrische gegevens die beschikbaar zijn via Azure monitor?](./traffic-manager-faqs.md#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [Gebruikt Verkeersweergave informatie over het subnet van EDNS-client?](./traffic-manager-faqs.md#does-traffic-view-use-edns-client-subnet-information)

* [Hoeveel dagen aan gegevens Verkeersweergave gebruik?](./traffic-manager-faqs.md#how-many-days-of-data-does-traffic-view-use)

* [Hoe verwerkt Verkeersweergave externe eind punten?](./traffic-manager-faqs.md#how-does-traffic-view-handle-external-endpoints)

* [Moet ik Verkeersweergave inschakelen voor elk profiel in mijn abonnement?](./traffic-manager-faqs.md#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Hoe kan ik Verkeersweergave uitschakelen?](./traffic-manager-faqs.md#how-can-i-turn-off-traffic-view)

* [Hoe werkt Verkeersweergave facturering?](./traffic-manager-faqs.md#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie [over de werking van Traffic Manager](traffic-manager-overview.md)
- Meer informatie over de [routerings methoden voor verkeer](traffic-manager-routing-methods.md) die door Traffic Manager worden ondersteund
- Meer informatie over het [maken van een Traffic Manager profiel](./quickstart-create-traffic-manager-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png