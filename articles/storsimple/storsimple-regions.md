---
title: Beschik baarheid van StorSimple-regio
description: Hierin worden de Azure-regio's beschreven waarin de verschillende StorSimple beschikbaar zijn.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: 5efb87ad674474b2e21b86b5cdc48785753a51dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90564148"
---
# <a name="available-regions-for-your-storsimple"></a>Beschik bare regio's voor uw StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht

De Azure-data centers werken in meerdere geografische locaties over de hele wereld om te voldoen aan de behoeften van de klant, de vereisten en de voor keuren met betrekking tot de gegevens locatie. Een Azure-geografie is een gedefinieerd gebied van de wereld dat ten minste één Azure-regio bevat. Een Azure-regio is een gebied binnen een geografie met een of meer data centers.

Het kiezen van een Azure-regio is zeer belang rijk en de keuze van de regio wordt beïnvloed door factoren zoals gegevens locatie en soevereiniteit, Beschik baarheid van de service, prestaties, kosten en redundantie. Ga naar de [Azure-regio voor mij](https://azure.microsoft.com/overview/datacenters/how-to-choose/) voor meer informatie over het kiezen van een regio?

Voor een StorSimple-oplossing wordt de keuze van de regio specifiek bepaald door de volgende factoren:

- Regio's waar de StorSimple-Apparaatbeheer service beschikbaar is.
- De landen/regio's waar de fysieke StorSimple, de Cloud of het virtuele apparaat beschikbaar is.
- De regio's waar de opslag accounts die StorSimple gegevens opslaan, voor optimale prestaties moeten worden gevonden.

In deze zelf studie wordt de beschik baarheid van regio's voor de StorSimple-Apparaatbeheer service, de on-premises fysieke en de Cloud apparaten beschreven. De informatie in dit artikel is van toepassing op apparaten uit de StorSimple 8000-en 1200-serie.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Beschik baarheid van regio's voor StorSimple Apparaatbeheer service

StorSimple Apparaatbeheer-service wordt momenteel ondersteund in 12 open bare regio's en 2 Azure Government regio's.

U definieert een regio of locatie wanneer u de StorSimple-Apparaatbeheer service voor het eerst maakt. In het algemeen wordt een locatie gekozen die het dichtst bij de geografische regio ligt waar het apparaat wordt geïmplementeerd. Het apparaat en de service kunnen ook op verschillende locaties worden geïmplementeerd.

Hier volgt een lijst met regio's waar StorSimple-Apparaatbeheer service beschikbaar is voor de open bare Azure-Cloud en kan worden geïmplementeerd.

![Scherm afbeelding toont de Apparaatbeheer van de StorSimple met de locatie lijst gemarkeerd.](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Voor Azure Government Cloud is de StorSimple Apparaatbeheer-service beschikbaar in US Gov-Iowa en US Gov-Virginia data centers.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Beschik baarheid van regio's voor gegevens die zijn opgeslagen in StorSimple

StorSimple gegevens worden fysiek opgeslagen in azure Storage-accounts en deze accounts zijn beschikbaar in alle Azure-regio's. Wanneer u een Azure-opslag account maakt, wordt de primaire locatie van het opslag account gekozen en wordt de regio bepaald waarin de gegevens zich bevinden.

Wanneer u voor het eerst een StorSimple-Apparaatbeheer service maakt en hieraan een opslag account koppelt, kunnen uw StorSimple Apparaatbeheer-service en Azure-opslag zich op twee verschillende locaties bevinden. In dat geval moet u de StorSimple--apparaatbeheerfunctie en het Azure Storage-account afzonderlijk maken.

In het algemeen kiest u de dichtstbijzijnde regio voor uw opslag account voor uw service. De dichtstbijzijnde Microsoft Azure-regio is echter mogelijk niet echt de regio met de laagste latentie. Het is de latentie waarmee de prestaties van de netwerk service en de prestaties van de oplossing worden bepaald. Als u dus een opslag account in een andere regio kiest, is het belang rijk om te weten wat de latentie tussen uw service en de regio die aan uw opslag account is gekoppeld.

Als u een StorSimple Cloud Appliance gebruikt, raden we u aan de service en het bijbehorende opslag account zich in dezelfde regio te bevinden. Opslag accounts in een andere regio kunnen leiden tot slechte prestaties.

## <a name="availability-of-storsimple-device"></a>Beschik baarheid van StorSimple-apparaat

Afhankelijk van het model, kunnen de StorSimple-apparaten beschikbaar zijn in verschillende locaties of landen/regio's.

### <a name="storsimple-physical-device-models-81008600"></a>Fysiek StorSimple-apparaat (modellen 8100/8600)

Als u een fysiek StorSimple 8100-of 8600-apparaat gebruikt, is het apparaat beschikbaar in de volgende landen/regio's.

| #  | Land/regio        | #  | Land/regio     | #  | Land/regio      | #  | Land/regio             |
|----|-----------------------|----|--------------------|----|---------------------|----|----------------------------|
| 1  | Australië             | 16 | Hongkong SAR      | 31 | Nieuw-Zeeland         | 46 | Zuid-Afrika               |
| 2  | Oostenrijk               | 17 | Hongarije            | 32 | Nigeria             | 47 | Zuid-Korea                |
| 3  | Bahrein               | 18 | IJsland            | 33 | Noorwegen              | 48 | Spanje                      |
| 4  | België               | 19 | India              | 34 | Peru                | 49 | Sri Lanka                  |
| 5  | Brazilië                | 20 | Indonesië          | 35 | Filipijnen         | 50 | Zweden                     |
| 6  | Canada                | 21 | Ierland            | 36 | Polen              | 51 | Zwitserland                |
| 7  | Chili                 | 22 | Israël             | 37 | Portugal            | 52 | Taiwan                     |
| 8  | Colombia              | 23 | Italië              | 38 | Puerto Rico         | 53 | Thailand                   |
| 9  | Tsjechische Republiek        | 24 | Japan              | 39 | Qatar               | 54 | Turkije                     |
| 10 | Denemarken               | 25 | Kenia              | 40 | Roemenië             | 55 | Oekraïne                    |
| 11 | Egypte                 | 26 | Koeweit             | 41 | Rusland              | 56 | Verenigde Arabische Emiraten       |
| 12 | Finland               | 27 | Macau SAR          | 42 | Saoedi-Arabië        | 57 | Verenigd Koninkrijk             |
| 13 | Frankrijk                | 28 | Maleisië           | 43 | Singapore           | 58 | Verenigde Staten              |
| 14 | Duitsland               | 29 | Mexico             | 44 | Slowakije            | 59 | Vietnam                    |
| 15 | Griekenland                | 30 | Nederland        | 45 | Slovenië            | 60 | Kroatië                    |

Deze lijst wordt gewijzigd naarmate er meer landen/regio's worden toegevoegd. Voor een actuele lijst van de geographs gaat u naar de voor waarden van de opslag matrix in de [product termen](https://www.microsoft.com/en-us/licensing/product-licensing/products).

Micro soft kan fysieke hardware leveren en vervanging van reserve onderdelen voor StorSimple naar de geographs in de voor gaande lijst.

> [!IMPORTANT]
> Plaats geen StorSimple fysieke apparaat in een regio waarbij StorSimple niet wordt ondersteund. Micro soft kan geen vervangende onderdelen verzenden naar landen/regio's waar StorSimple niet wordt ondersteund.

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple Cloud Appliance (modellen 8010/8020)

Als u een StorSimple Cloud Appliance 8010 of 8020 gebruikt, wordt het apparaat ondersteund en beschikbaar in alle regio's waar de onderliggende virtuele machine wordt ondersteund. De 8010 maakt gebruik van een _Standard_A3_ virtuele machine die wordt ondersteund in alle Azure-regio's.

De 8020 maakt gebruik van Premium Storage en _Standard_DS3_ VM om een Cloud apparaat te maken. De 8020 wordt ondersteund in azure-regio's die ondersteuning bieden voor Premium Storage en _Standard_DS3_ Azure-vm's. Gebruik [deze lijst](https://azure.microsoft.com/regions/services/) om te kijken of zowel **Virtuele Machines > DS-serie** als **Opslag > Schijfruimte** beschikbaar is in uw regio.

### <a name="storsimple-virtual-array-model-1200"></a>StorSimple Virtual array (model 1200)

Als u een virtuele-matrix van 1200 Series StorSimple gebruikt, wordt de installatie kopie van de virtuele schijf in alle Azure-regio's ondersteund.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [prijzen voor verschillende StorSimple-modellen](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Meer informatie over [het beheren van uw StorSimple-opslag account](storsimple-8000-manage-storage-accounts.md).
* Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).
