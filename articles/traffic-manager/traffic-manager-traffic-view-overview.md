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
ms.date: 03/16/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: b20357413c62460aba55a2d354b90995a2aa4815
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183690"
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager Verkeersweergave

Traffic Manager biedt u de mogelijkheid om DNS-niveau routering te bieden zodat uw eind gebruikers naar gezonde eind punten worden geleid op basis van de routerings methode die is opgegeven tijdens het maken van het profiel. Verkeersweergave biedt Traffic Manager met een weer gave van de gebruikers bases (op een granulatie niveau van de DNS-resolver) en hun verkeers patroon. Als u Verkeersweergave inschakelt, wordt deze informatie verwerkt om u te voorzien van inzicht in de praktijk. 

Met behulp van Verkeersweergave kunt u het volgende doen:
- Krijg inzicht in de locatie waar uw gebruikers zich bevinden (tot een lokale granulatie van het DNS-resolver niveau).
- Bekijk het volume verkeer (geobserveerd als DNS-query's die zijn verwerkt door Azure Traffic Manager) die afkomstig zijn uit deze regio's.
- Krijg inzicht in wat is de representatieve latentie van deze gebruikers.
- dieper in de specifieke verkeers patronen van elk van deze gebruikers bases naar Azure-regio's waar u eind punten hebt. 

U kunt bijvoorbeeld Verkeersweergave gebruiken om te begrijpen welke regio's een groot aantal verkeer hebben, maar wel van hogere latenties. Vervolgens kunt u deze informatie gebruiken om uw footprint uit te breiden naar nieuwe Azure-regio's zodat deze gebruikers een lagere latentie kunnen ervaren.

## <a name="how-traffic-view-works"></a>Hoe Verkeersweergave werkt

Verkeersweergave werkt door de binnenkomende query's die in de afgelopen zeven dagen zijn ontvangen, te Traffic Manager bekijken op basis van een profiel waarvoor deze functie is ingeschakeld. Van de gegevens van binnenkomende query's haalt Verkeersweergave het bron-IP-adres van de DNS-resolver op dat wordt gebruikt als een representatie van de locatie van de gebruikers. Deze worden vervolgens gegroepeerd op het niveau van de DNS-resolver om gebruikers basis regio's te maken met behulp van de geografische gegevens van IP-adressen die worden beheerd door Traffic Manager. Traffic Manager vervolgens naar de Azure-regio's waarnaar de query is doorgestuurd en wordt er een Traffic Flow-kaart voor gebruikers uit deze regio's gemaakt.  
In de volgende stap vergelijkt Traffic Manager de gebruikers basis regio met de Azure-regio toewijzing met de netwerk Intelligence-latentie tabellen die worden bijgehouden voor verschillende netwerken van eind gebruikers om inzicht te krijgen in de gemiddelde latentie van gebruikers uit deze regio's wanneer er verbinding wordt gemaakt met Azure-regio's. Al deze berekeningen worden vervolgens gecombineerd op een lokaal IP-adres voor de DNS-resolver voordat deze wordt weer gegeven. U kunt de informatie op verschillende manieren gebruiken.

De frequentie van het bijwerken van de verkeers weergave gegevens is afhankelijk van meerdere interne service variabelen. De gegevens worden echter meestal elke 24 uur bijgewerkt.

>[!NOTE]
>De latentie die wordt beschreven in Verkeersweergave is een representatieve latentie tussen de eind gebruiker en de Azure-regio's waarmee ze zijn verbonden, en is niet de latentie van de DNS-zoek opdracht. Verkeersweergave maakt een beste schatting van de latentie tussen de lokale DNS-resolver en de Azure-regio waarnaar de query is doorgestuurd als er onvoldoende gegevens beschikbaar zijn, is de geretourneerde latentie null. 

## <a name="visual-overview"></a>Overzicht van visuele elementen

Wanneer u naar de sectie **Verkeersweergave** op de pagina Traffic Manager gaat, wordt er een geografische kaart weer gegeven met een overlay van Verkeersweergave Insights. De kaart bevat informatie over de gebruikers basis en-eind punten voor uw Traffic Manager profiel.

![Traffic Manager Verkeersweergave geografische weer gave][1]

### <a name="user-base-information"></a>Gebruikers basis gegevens

Voor lokale DNS-resolvers waarvoor locatie gegevens beschikbaar zijn, worden ze weer gegeven in de kaart. De kleur van de DNS-resolver duidt op de gemiddelde latentie van eind gebruikers die de DNS-resolver voor hun Traffic Manager query's hebben gebruikt.

Als u de muis aanwijzer over een locatie van een DNS-resolver in de kaart houdt, wordt het volgende weer gegeven:
- het IP-adres van de DNS-resolver
- het volume van de DNS-query verkeer dat door Traffic Manager wordt weer gegeven
- de eind punten waarop het verkeer van de DNS-resolver is doorgestuurd, als een lijn tussen het eind punt en de DNS-resolver 
- de gemiddelde latentie van die locatie naar het eind punt, weer gegeven als de kleur van de lijn waarmee ze worden verbonden

### <a name="endpoint-information"></a>Eindpunt gegevens

De Azure-regio's waarin de eind punten zich bevinden, worden weer gegeven als blauwe stippen in de kaart. Als uw eind punt extern is en er geen Azure-regio aan is toegewezen, wordt dit boven aan de kaart weer gegeven. Klik op een eind punt om de verschillende locaties (op basis van de gebruikte DNS-resolver) te bekijken vanaf waar verkeer naar dat eind punt werd gestuurd. De verbindingen worden weer gegeven als een lijn tussen het eind punt en de locatie van de DNS-resolver en zijn gekleurd op basis van de representatieve latentie tussen dat paar. Daarnaast ziet u de naam van het eind punt, de Azure-regio waarin deze wordt uitgevoerd, en het totale volume van aanvragen dat door dit Traffic Manager profiel naar de service werd gestuurd.


## <a name="tabular-listing-and-raw-data-download"></a>Lijst met tabellaire en onbewerkte gegevens downloaden

U kunt de Verkeersweergave gegevens weer geven in een tabel indeling in Azure Portal. Er is een vermelding voor elk IP/eind punt van de DNS-resolver met het IP-adres van de DNS-resolver, de naam en geografische locatie van de Azure-regio waarin het eind punt zich bevindt (indien beschikbaar), het volume van aanvragen dat is gekoppeld aan de DNS-resolver voor dat eind punt en de representatieve latentie die aan eind gebruikers wordt gekoppeld met die DNS U kunt de Verkeersweergave gegevens ook downloaden als een CSV-bestand dat kan worden gebruikt als onderdeel van een analytische werk stroom van uw keuze.

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