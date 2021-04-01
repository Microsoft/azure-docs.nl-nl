---
title: Schaal baarheid en prestaties-persoonlijker
titleSuffix: Azure Cognitive Services
description: 'Websites en toepassingen met hoge prestaties en hoog verkeer hebben twee belang rijke factoren om te overwegen met persoonlijke instellingen voor schaal baarheid en prestaties: latentie en trainings doorvoer.'
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: 52d22fce64a26267e73cb9a1df8614129bb96c3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "87132684"
---
# <a name="scalability-and-performance"></a>Schaal baarheid en prestaties

Websites en toepassingen met hoge prestaties en hoog verkeer hebben twee belang rijke factoren om te kunnen overwegen met persoonlijke instellingen voor schaal baarheid en prestaties:

* Lage latentie behouden bij het maken van Rank API-aanroepen
* Zorgen dat de door Voer van de training wordt bijgewerkt met gebeurtenis invoer

Persoonlijke instellingen kunnen snel een absolute waarde Retour neren, met de meeste gespreks duur die is toegewezen aan communicatie via de REST API. Azure schaalt automatisch de mogelijkheid om snel te reageren op aanvragen.

##  <a name="low-latency-scenarios"></a>Scenario's met lage latentie

Voor sommige toepassingen is een lage latentie vereist bij het retour neren van een positie. Er zijn lage latenties nodig:

* Om te voor komen dat de gebruiker een opvallende hoeveelheid tijd moet wachten voordat geclassificeerde inhoud wordt weer gegeven.
* Om een server te helpen die extreem veel verkeer ondervindt, vermijdt u het koppelen van schaarse reken tijd en netwerk verbindingen.


## <a name="scalability-and-training-throughput"></a>Door Voer voor schaal baarheid en training

Personaler werkt door het bijwerken van een model dat opnieuw wordt getraind op basis van berichten die asynchroon worden verzonden door Personaler na de classificatie en belonings-Api's. Deze berichten worden verzonden met behulp van een Azure-EventHub voor de toepassing.

 Het is niet waarschijnlijk dat de meeste toepassingen het maximale lidmaatschap en de door Voer van de persoonlijke voor keuren bereiken. Het bereiken van dit maximum zal de toepassing niet vertragen, maar het impliceert dat Event hub-wacht rijen sneller worden gevuld dan ze kunnen worden opgeruimd.

## <a name="how-to-estimate-your-throughput-requirements"></a>Uw doorvoer vereisten ramen

* Schatting van het gemiddelde aantal bytes per rangorde gebeurtenis waarmee de lengte van de context en de JSON-documenten van de actie worden toegevoegd.
* Deling 20 MB per seconde door dit geschatte gemiddelde aantal bytes.

Als uw gemiddelde Payload bijvoorbeeld 500 functies heeft en elk een geschatte 20 tekens is, dan is elke gebeurtenis ongeveer 10 KB. Met deze schattingen 20.000.000/10.000 = 2.000 gebeurtenissen per seconde, die ongeveer 173.000.000 gebeurtenissen per dag zijn. 

Als u deze limieten bereikt, neemt u contact op met ons ondersteunings team voor architectuur advies.

## <a name="next-steps"></a>Volgende stappen

[Persoonlijker maken en configureren](how-to-settings.md).