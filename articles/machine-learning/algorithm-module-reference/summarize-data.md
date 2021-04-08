---
title: Gegevens samenvatten
titleSuffix: Azure Machine Learning
description: Informatie over het gebruik van de module gegevens samenvatten in Azure Machine Learning voor het genereren van een basis beschrijvende statistieken rapport voor de kolommen in een gegevensset.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: 5206565b85d1551e5e551f1dfe75d28c93bc53f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898214"
---
# <a name="summarize-data"></a>Gegevens samenvatten

In dit artikel wordt een module van Azure Machine Learning Designer beschreven.

Gebruik de module gegevens samenvatten om een set standaard statistische maat eenheden te maken waarmee elke kolom in de invoer tabel wordt beschreven.

Samenvattings statistieken zijn handig wanneer u inzicht wilt krijgen in de kenmerken van de volledige gegevensset. U moet bijvoorbeeld het volgende weten:

- Hoeveel ontbrekende waarden zijn er in elke kolom?
- Hoeveel unieke waarden zijn er in een functie kolom?
- Wat is het gemiddelde en de standaard afwijking voor elke kolom?

De module berekent de belang rijke scores voor elke kolom en retourneert een rij met samenvattings statistieken voor elke variabele (gegevens kolom) die is opgegeven als invoer.

## <a name="how-to-configure-summarize-data"></a>Samenvattings gegevens configureren  

1. Voeg de module **gegevens samenvatten** toe aan uw pijp lijn. U kunt deze module vinden in de categorie **statistische functies** in de ontwerp functie.

1. Verbind de gegevensset waarvoor u een rapport wilt genereren.

    Als u alleen op bepaalde kolommen wilt rapporteren, gebruikt u de module [select columns in dataset](select-columns-in-dataset.md) om een subset van kolommen te projecteren waarmee u wilt werken.

1. Er zijn geen aanvullende para meters vereist. Standaard analyseert de module alle kolommen die als invoer worden opgegeven en, afhankelijk van het type waarden in de kolommen, een relevante set statistieken, zoals beschreven in de sectie [resultaten](#results) .

1. Verzend de pijp lijn.

## <a name="results"></a>Resultaten

Het rapport van de module kan de volgende statistieken bevatten. 

|Kolomnaam|Beschrijving|
|------|------|  
|**Functie**|Naam van de kolom|
|**Aantal**|Aantal rijen|
|**Aantal unieke waarden**|Aantal unieke waarden in kolom|
|**Aantal ontbrekende waarden**|Aantal unieke waarden in kolom|
|**Haal**|Laagste waarde in kolom|  
|**Aantal**|Hoogste waarde in kolom|
|**Greenwich**|Gemiddelde van alle kolom waarden|
|**Gemiddelde afwijking**|Gemiddelde afwijking van kolom waarden|
|**1e kwartiel**|Waarde bij eerste kwartiel|
|**Mediaan**|Kolom waarde mediaan|
|**3e kwartiel**|Waarde bij derde kwartiel|
|**Modus**|Modus van kolom waarden|
|**Bereik**|Geheel getal dat het aantal waarden tussen de maximum-en minimum waarden vertegenwoordigt|
|**Voorbeeld afwijking**|Variantie voor kolom; Zie opmerking|
|**Standaard deviatie voor beeld**|Standaard afwijking voor kolom; Zie opmerking|
|**Voor beeld scheefheid**|Scheefheid voor kolom; Zie opmerking|
|**Voor beeld van kurtosis**|Kurtosis voor kolom; Zie opmerking|
|**P 0,5**|0,5% percentiel|
|**P1**|1% percentiel|
|**P5**|5% percentiel|
|**P95**|95% percentiel|
|**P 99,5**|99,5% percentiel |

## <a name="technical-notes"></a>Technische opmerkingen

- Voor niet-numerieke kolommen worden alleen de waarden voor Count, het aantal unieke waarden en het aantal missers berekend. Voor andere statistieken wordt een null-waarde geretourneerd.

- Kolommen die Booleaanse waarden bevatten, worden verwerkt met behulp van deze regels:

    - Bij het berekenen van min, een logische en wordt toegepast.
    
    - Bij het berekenen van maximum, een logische of wordt toegepast
    
    - Bij het berekenen van het bereik controleert de module eerst of het aantal unieke waarden in de kolom gelijk is aan 2.
    
    - Bij het berekenen van een statistiek waarvoor drijvende-komma berekeningen zijn vereist, worden de waarden True beschouwd als 1,0 en worden de waarden False beschouwd als 0,0.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning.  
