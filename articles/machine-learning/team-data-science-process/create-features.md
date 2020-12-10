---
title: Functie techniek in data wetenschappen-team data Science process
description: Meer informatie over feature engineering en zijn rol in het proces voor gegevens verbetering van machine learning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: conceptual
ms.date: 05/14/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperf-fy20q4
ms.openlocfilehash: 7cf97edcb2f2f7a23d5c899194d497deade1f507
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031858"
---
# <a name="feature-engineering-in-data-science"></a>Functie techniek in data wetenschappen

In dit artikel vindt u informatie over feature engineering en de bijbehorende rol bij het verbeteren van gegevens in machine learning. Leer van voor beelden die zijn getekend uit [Azure machine learning Studio (klassieke)](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio) experimenten. 

* **Functie techniek**: het proces van het maken van nieuwe functies van onbewerkte gegevens om de voorspellende kracht van het leer algoritme te verg Roten. Functies die zijn ontworpen, moeten extra informatie vastleggen die niet eenvoudig zichtbaar is in de oorspronkelijke functieset.
* **Functie selectie**: het proces van het selecteren van de sleutel subset van functies om de dimensionaliteit van het trainings probleem te verminderen.

Normaal gesp roken **functie techniek** wordt eerst toegepast om extra functies te genereren en vervolgens wordt de **functie selectie** uitgevoerd om irrelevante, redundante of zeer gecorreleerde functies te elimineren.

Functie techniek en selectie maken deel uit van de [modelleer fase](lifecycle-modeling.md) van het team data Science process (TDSP). Zie [Wat is de TDSP?](overview.md) voor meer informatie over de TDSP en de data Science-levens cyclus.

## <a name="what-is-feature-engineering"></a>Wat is functie techniek?

Trainings gegevens bestaan uit een matrix die bestaat uit rijen en kolommen. Elke rij in de matrix is een waarneming of record. De kolommen van elke rij zijn de functies waarin elke record wordt beschreven. De functies die in het experimentele ontwerp zijn opgegeven, moeten de patronen in de gegevens kenmerken.

Hoewel veel van de onbewerkte gegevens velden rechtstreeks kunnen worden gebruikt om een model te trainen, is het vaak nodig om extra (ontworpen) functies te maken voor een uitgebreide gegevensset voor training.

Functies die zijn ontworpen om de training te verbeteren, bieden informatie die de patronen in de gegevens beter onderscheidt. Maar dit proces is iets van een art. Voor geluids-en productiviteits beslissingen zijn vaak domein expertise vereist.

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Voor beeld 1: tijdelijke functies toevoegen voor een regressie model

We gebruiken de voor [spellingen van de vraag van fietsen](https://gallery.azure.ai/Experiment/Regression-Demand-estimation-4) in azure machine learning Studio (klassiek) om te demonstreren hoe functies voor een regressie taak kunnen worden ontworpen. Het doel van dit experiment is om de vraag naar fietsen binnen een bepaalde maand/dag/uur te voors pellen.

### <a name="bike-rental-dataset"></a>Huur gegevensset voor fietsen

De [ICB-gegevensset van de fiets huur](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset/) is gebaseerd op echte gegevens van een fiets share bedrijf op basis van de Verenigde Staten. Het vertegenwoordigt het aantal fiets huren binnen een specifiek uur van een dag voor de jaren 2011 en 2012. Het bevat 17.379 rijen en 17 kolommen.

De onbewerkte functieset bevat weers omstandigheden (Tempe ratuur/vochtigheid/wind snelheid) en het type van de dag (feestdag/weekdag). Het veld om te voors pellen is het aantal dat de fiets huren binnen een bepaald uur vertegenwoordigt. Aantal bereiken van 1 tot en met 977.

### <a name="create-a-feature-engineering-experiment"></a>Een technisch experiment met functie maken

Met het doel van het bouwen van efficiënte functies in de trainings gegevens worden vier regressie modellen gebouwd op basis van dezelfde algoritme, maar met vier verschillende trainings gegevens sets. De vier gegevens sets vertegenwoordigen dezelfde onbewerkte invoer gegevens, maar er is een steeds groter aantal functies ingesteld. Deze functies zijn onderverdeeld in vier categorieën:

1. A = weer + feestdag + weekdag + weekend functies voor de voorspelde dag
2. B = aantal fietsen dat in elk van de afgelopen 12 uur is gehuurd
3. C = aantal fietsen dat in elk van de voor gaande 12 dagen op hetzelfde uur is gehuurd
4. D = aantal fietsen dat in elk van de afgelopen 12 weken op hetzelfde uur en dezelfde dag is gehuurd

Naast de functieset A, die al aanwezig is in de oorspronkelijke onbewerkte gegevens, worden de andere drie sets functies gemaakt via het technisch proces van feature. Met de functieset B worden recente vraag voor de fietsen vastgelegd. Met de functieset C worden de vraag naar fietsen op een bepaald uur vastgelegd. De functieset D legt de vraag naar fietsen op een bepaald uur en een bepaalde dag van de week vast. De vier trainings gegevens sets bevatten elk een functieset A, een + B, een + B + C en een + B + C + D.

### <a name="feature-engineering-using-studio-classic"></a>Functie techniek met Studio (klassiek)

In het Studio (klassiek) experiment worden deze vier trainings gegevens sets gevormd via vier vertakkingen van de vooraf verwerkte invoer gegevensset. Met uitzonde ring van de meest linkse vertakking bevat elk van deze vertakkingen een [Execute R-script](/azure/machine-learning/studio-module-reference/execute-r-script) module, waarin de afgeleide functies (functieset B, C en D) zijn gebouwd en worden toegevoegd aan de geïmporteerde gegevensset.

In de volgende afbeelding ziet u het R-script dat wordt gebruikt om de functieset B in de tweede vertakking te maken.

![functies maken](./media/create-features/addFeature-Rscripts.png)

### <a name="results"></a>Resultaten

Een vergelijking van de prestatie resultaten van de vier modellen wordt in de volgende tabel samenvatten: 

![resultaat vergelijking](./media/create-features/result1.png)

De beste resultaten worden weer gegeven door functies A + B + C. De fout frequentie neemt af wanneer extra functie sets zijn opgenomen in de trainings gegevens. Hiermee wordt het vermoeden gecontroleerd dat de functie set B, C aanvullende relevante informatie biedt voor de regressie taak. Het toevoegen van de D-functie lijkt geen extra verlaging van het fout aantal te bieden.

## <a name="example-2-create-features-for-text-mining"></a><a name="example2"></a> Voor beeld 2: functies maken voor tekst analyse

Functie techniek wordt veel toegepast in taken met betrekking tot tekst analyse, zoals document classificatie en sentiment analyse. Omdat afzonderlijke gedeelten van onbewerkte tekst doorgaans als invoer gegevens dienen, is het technisch proces van de functie nodig om de functies te maken met behulp van woord-en woordgroepen frequenties.

### <a name="feature-hashing"></a>Functie-hashing

Ter verkrijging van deze taak wordt een techniek met de naam [hashing](/azure/machine-learning/studio-module-reference/feature-hashing) toegepast om op efficiënte wijze wille keurige tekst functies in indexen in te scha kelen. In plaats van elke tekst functie (woorden/zinsdelen) aan een bepaalde index te koppelen, wordt met deze methode een hash-functie toegepast op de functies en worden de hash-waarden direct als indexen gebruikt.

In Studio (klassiek) bevindt zich een [functie hashing](/azure/machine-learning/studio-module-reference/feature-hashing) -module die de functies van Word/woordgroepen handig maakt. In de volgende afbeelding ziet u een voor beeld van het gebruik van deze module. De invoer gegevensset bevat twee kolommen: de classificatie van het boek van 1 tot 5 en de werkelijke beoordelings inhoud. Het doel van deze module is om een aantal nieuwe functies op te halen die de frequentie van het voorval van de corresponderende woord (en)/phrase (s) in de specifieke boek beoordeling tonen. Als u deze module wilt gebruiken, voert u de volgende stappen uit:

* Selecteer eerst de kolom die de invoer tekst bevat ("col2" in dit voor beeld).
* Stel vervolgens ' hashing bitsize ' in op 8, wat betekent dat 2 ^ 8 = 256 functies worden gemaakt. Het woord/de fase in alle tekst wordt gehasht naar 256 indices. De para meter "hashing bitsize" ligt tussen 1 en 31. De woord (en)/phrase (en) worden waarschijnlijk in dezelfde index gehasht als het een groter getal is.
* Ten derde stelt u de para meter "N-gram" in op 2. Met deze waarde wordt de frequentie van het aantal unigrams (een functie voor elk woord) en bigrams (een functie voor elk paar aaneengesloten woorden) opgehaald uit de invoer tekst. De para meter ' N-g ' variëren van 0 tot 10, waarmee het maximum aantal sequentiële woorden wordt aangegeven dat in een functie moet worden opgenomen.  

![De module ' hashing van functies '](./media/create-features/feature-Hashing1.png)

In de volgende afbeelding ziet u hoe deze nieuwe functie eruitziet.

![Voor beeld van functie-hashing](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Conclusie
Ontworpen en geselecteerde functies verhogen de efficiëntie van het trainings proces, waardoor de belang rijke gegevens in de gegevens worden geëxtraheerd. Ze verbeteren ook de kracht van deze modellen om de invoer gegevens nauw keurig te classificeren en om de resultaten betrouwbaarder te voors pellen.

Functie techniek en selectie kunnen ook worden gecombineerd om het leren van de reken kracht te maken. Dit doet u door het aantal functies te verhogen en te verminderen dat nodig is voor het kalibreren of trainen van een model. Mathematisch, de geselecteerde onderdelen zijn een minimale set onafhankelijke variabelen waarmee de patronen in de gegevens worden uitgelegd en de resultaten kunnen worden voor speld.

Het is niet altijd nood zakelijk om functie-engineering of functie selectie uit te voeren. Dit is afhankelijk van de gegevens, de geselecteerde algoritme en de doel stelling van het experiment.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor informatie over het maken van functies voor gegevens in specifieke omgevingen:

* [Functies maken voor gegevens in SQL Server](create-features-sql-server.md)
* [Functies maken voor gegevens in een Hadoop-cluster met behulp van Hive-query's](create-features-hive.md)