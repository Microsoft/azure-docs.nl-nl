---
title: Naslag informatie over functie hashing-module
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module functie hashing in de Azure Machine Learning Designer voor het parametriseer van tekst gegevens.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd48b32afee320aa3d252540d566317c374c73a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420781"
---
# <a name="feature-hashing-module-reference"></a>Naslag informatie over functie hashing-module

In dit artikel wordt een module beschreven die is opgenomen in Azure Machine Learning Designer.

Gebruik de functie hashing-module om een stroom van Engelse tekst te transformeren in een set integer-functies. U kunt deze hash-functie vervolgens door geven die is ingesteld op een machine learning algoritme voor het trainen van een tekst analyse model.

De functie hashing-functionaliteit die in deze module wordt gegeven, is gebaseerd op het nimbusml-Framework. Zie [NgramHash class](/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest&preserve-view=true)voor meer informatie.

## <a name="what-is-feature-hashing"></a>Wat is functie-hashing?

Hashing van functies werkt door unieke tokens te converteren naar gehele getallen. Het werkt op de exacte teken reeksen die u opgeeft als invoer en voert geen taal analyse of voor verwerking uit. 

Neem bijvoorbeeld een aantal eenvoudige zinnen zoals deze, gevolgd door een sentiment-Score. Stel dat u deze tekst wilt gebruiken om een model te bouwen.

|Gebruikers tekst|Sentiment|
|--------------|---------------|
|Ik gek dit boek|3|
|Ik Hated dit boek|1|
|Dit boek was geweldig|3|
|Ik hou boeken|2|

Intern maakt de functie hashing-module een woorden lijst van n-gram. De lijst met bigrams voor deze gegevensset zou er bijvoorbeeld als volgt moeten uitzien:

|Term (bigrams)|Frequentie|
|------------|---------------|
|Dit boek|3|
|Ik gek|1|
|Ik Hated|1|
|Ik hou|1|

U kunt de grootte van het n-gram instellen met behulp van de eigenschap **n-gram** . Als u kiest voor bigrams, worden unigrams ook berekend. De woorden lijst bevat ook enkele voor waarden, zoals de volgende:

|Term (unigrams)|Frequentie|
|------------|---------------|
|boek|3|
|I|3|
|boeken|1|
|aangetroffen|1|

Nadat de woorden lijst is gebouwd, zet de functie hashing module de woordenlijst termen om in hash-waarden. Vervolgens wordt berekend of een functie in elk geval is gebruikt. Voor elke rij met tekst gegevens voert de module een set kolommen uit, één kolom voor elke hash-functie.

Zo kunnen de functie kolommen na hashing er ongeveer als volgt uitzien:

|Waardering|Hash-functie 1|Hash-functie 2|Hash-functie 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Als de waarde in de kolom 0 is, bevat de rij het onderdeel hashed niet.
* Als de waarde 1 is, bevat de rij de functie.

Met hashing van functies kunt u tekst documenten met variabele lengte vertegenwoordigen als numerieke functie vectoren van gelijke lengte om de dimensionaliteit te verminderen. Als u probeert de tekst kolom voor training te gebruiken zoals is, wordt deze behandeld als een categorische-functie kolom met veel verschillende waarden.

Met numerieke uitvoer kunt u ook algemene machine learning methoden gebruiken, zoals classificatie, clustering en het ophalen van informatie. Omdat opzoek bewerkingen geheeltallige hashes kunnen gebruiken in plaats van teken reeksen vergelijken, is het ophalen van de functie wegingen veel sneller.

## <a name="configure-the-feature-hashing-module"></a>De functie hashing-module configureren

1.  Voeg de functie hashing module toe aan uw pijp lijn in de ontwerp functie.

1. Verbind de gegevensset die de tekst bevat die u wilt analyseren.

    > [!TIP]
    > Omdat functie-hashing geen lexicale bewerkingen uitvoert, zoals het maken van een stam bewerking of afkap ping, kunt u soms betere resultaten verkrijgen door de tekst voor te verwerken voordat u functie-hashing toepast. 

1. Stel **doel kolommen** in op de tekst kolommen die u wilt converteren naar hash-functies. Denk hierbij aan het volgende:

    * De kolommen moeten het teken reeks gegevens type zijn.
    
    * Het kiezen van meerdere tekst kolommen kan aanzienlijke gevolgen hebben voor de functie dimensionaliteit. Het aantal kolommen voor een 10-bits hash gaat bijvoorbeeld van 1.024 voor één kolom tot 2.048 voor twee kolommen.

1. Gebruik **hash-bitsize** om het aantal bits op te geven dat moet worden gebruikt wanneer u de hash-tabel wilt maken.
    
    De standaard-bits grootte is 10. Deze waarde is voor veel problemen voldoende. Mogelijk hebt u meer ruimte nodig om conflicten te voor komen, afhankelijk van de grootte van de lijst met n-grammen in de tekst van de training.
    
1. Voer voor **N-gram** een getal in dat de maximum lengte van het N-gram definieert dat aan de trainings woordenlijst moet worden toegevoegd. Een n-gram bestaat uit een reeks *n* woorden, behandeld als een unieke eenheid.

    Als u bijvoorbeeld 3, unigrams, bigrams en trigrams invoert, worden deze gemaakt.

1. Verzend de pijp lijn.

## <a name="results"></a>Resultaten

Nadat de verwerking is voltooid, voert de module een getransformeerde gegevensset uit waarin de oorspronkelijke tekst kolom is geconverteerd naar meerdere kolommen. Elke kolom vertegenwoordigt een functie in de tekst. Afhankelijk van hoe groot de woorden lijst is, kan de resulterende gegevensset groot zijn:

|Kolom naam 1|Kolom Type 2|
|-------------------|-------------------|
|USERTEXT|Oorspronkelijke gegevens kolom|
|SENTIMENT|Oorspronkelijke gegevens kolom|
|USERTEXT-hash-functie 1|Gehashte functie kolom|
|USERTEXT-hashing-functie 2|Gehashte functie kolom|
|USERTEXT-hashing-functie n|Gehashte functie kolom|
|USERTEXT-hashing-functie 1024|Gehashte functie kolom|

Nadat u de getransformeerde gegevensset hebt gemaakt, kunt u deze gebruiken als invoer voor de module Train model.
 
## <a name="best-practices"></a>Aanbevolen procedures

Met de volgende aanbevolen procedures kunt u optimaal profiteren van de functie hashing module:

* Voeg een tekst module vóór verwerking toe voordat u functie-hashing gebruikt om de invoer tekst voor te verwerken. 

* Voeg een module select columns toe na de functie hashing module om de tekst kolommen uit de uitvoer gegevensset te verwijderen. U hebt de tekst kolommen niet nodig nadat de hash-functies zijn gegenereerd.
    
* U kunt deze voor verwerkings opties voor tekst gebruiken om de resultaten te vereenvoudigen en de nauw keurigheid te verbeteren:

    * Woord afbreking
    * Het verwijderen van woorden stoppen
    * Case-normalisatie
    * Verwijdering van Lees tekens en speciale tekens
    * Als gevolg  

De optimale set voor verwerkings methoden die in een oplossing moeten worden toegepast, is afhankelijk van domein, vocabulaire en zakelijke behoefte. pijp lijn met uw gegevens om te zien welke tekst verwerkings methoden het meest effectief zijn.

## <a name="next-steps"></a>Volgende stappen
            
De [set met modules bekijken die beschikbaar zijn](module-reference.md) voor Azure machine learning