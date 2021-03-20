---
title: Limieten voor Azure Block Chain-Service
description: Overzicht van de service-en functionele limieten in azure Block Chain Service
ms.date: 04/02/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 71e1bebf10fa0142870d03977182472da1ad031f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80676523"
---
# <a name="limits-in-azure-blockchain-service"></a>Limieten in azure Block Chain Service

De Azure Block Chain-service heeft service-en functionele limieten, zoals het aantal knoop punten dat een lid kan hebben, consortium beperkingen en opslag bedragen.

## <a name="pricing-tier"></a>Prijscategorie

De maximum limieten voor trans acties en validatie knooppunten zijn afhankelijk van of u de Azure Block Chain-Service inricht op basis van de prijs categorie Basic of Standard.

| Prijscategorie | Maximum aantal transactie knooppunten | Maxi maal aantal validatie knooppunten |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

Uw consortium netwerk moet ten minste twee knoop punten van de Standard-laag van Azure Block Chain service hebben. De Standard-laag knooppunten bevatten twee validatie knooppunten. Er zijn vier validatie knooppunten vereist om te voldoen aan de [fout tolerantie consensus van Istanboel Byzantine](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus).

De basis-laag gebruiken is voor het ontwikkelen, testen en controleren van concepten. De Standard-laag gebruiken voor implementaties van productie cijfers. U moet de categorie *Standard* ook gebruiken als u Blockchain Data Manager gebruikt of een groot aantal privé transacties verzendt.

Wanneer een lid is gemaakt, kan de prijscategorie niet meer worden gewijzigd van Basic in Standard en andersom.

## <a name="storage-capacity"></a>Opslagcapaciteit

De maximale hoeveelheid opslag die per knoop punt voor grootboek gegevens en logboeken kan worden gebruikt, is 1,8 terabyte.

Het verminderen van de omvang van het groot boek en de logboek opslag wordt niet ondersteund.
## <a name="consortium-limits"></a>Consortium limieten

* De **namen van consortiums en leden moeten uniek zijn** van andere consortium-en lidnamen in de Azure Block Chain-service.

* **De namen van leden en consortiums kunnen niet worden gewijzigd**

* **Alle leden in een consortium moeten dezelfde prijs categorie hebben**

* **Alle leden die deel uitmaken van een consortium, moeten zich in dezelfde regio bevinden**

    Het eerste lid dat is gemaakt in een consortium, bepaalt de regio. Uitgenodigde leden van het consortium moeten zich in dezelfde regio bevinden als het eerste lid. Als u alle leden beperkt tot dezelfde regio, zorgt u ervoor dat netwerk consensus geen negatieve gevolgen heeft.

* **Een consortium moet ten minste één beheerder hebben**

    Als er slechts één beheerder in een consortium is, kunnen ze zich niet van het consortium verwijderen of hun lid verwijderen totdat een andere beheerder wordt toegevoegd of bevorderd in het consortium.

* **Leden die uit het consortium zijn verwijderd, kunnen niet opnieuw worden toegevoegd**

    Ze moeten daarom opnieuw worden uitgenodigd om lid te worden van het consortium en een nieuw lid te maken. De bestaande leden resources worden niet verwijderd om historische trans acties te behouden.

* **Alle leden van een consortium moeten dezelfde grootboek versie gebruiken**

    Zie [patches, updates en versies](ledger-versions.md)voor meer informatie over de patches, updates en grootboek versies die beschikbaar zijn in de Azure Block Chain-service.

## <a name="performance"></a>Prestaties

Gebruik niet de functie *Eth. schatting* voor het verzenden van elke trans actie. De functie *Eth. estimate* is geheugen intensief. Als u de functie meerdere keren aanroept, worden de trans acties per seconde aanzienlijk verminderd.

Gebruik, indien mogelijk, een conservatieve aardgas waarde voor het verzenden van trans acties en Minimaliseer het gebruik van *Eth. estimate*.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over beleids regels met betrekking tot patches en upgrades voor systemen- [patches, updates en versies](ledger-versions.md).
