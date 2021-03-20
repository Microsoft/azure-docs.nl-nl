---
title: 'Word converteren naar vector: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van drie beschik bare Word2Vec-modellen voor het extra heren van een vocabulaire en de bijbehorende woord insluitingen van een verzameling tekst.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: 5fad3e4862b0c40c9edd00a5b9d47b245e529396
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91536729"
---
# <a name="convert-word-to-vector-module"></a>Woord naar vector module converteren

In dit artikel wordt beschreven hoe u met behulp van de module woord naar vector converteren in Azure Machine Learning Designer deze taken uitvoert:

- Verschillende Word2Vec-modellen (Word2Vec, FastText, ondersteunt pretraind model) Toep assen op de verzameling van de tekst die u als invoer hebt opgegeven.
- Genereer een vocabulaire met Word-insluitingen.

Deze module maakt gebruik van de Gensim-bibliotheek. Ga voor meer informatie over Gensim naar de [officiële website](https://radimrehurek.com/gensim/apiref.html), met zelf studies en een uitleg van algoritmen.

### <a name="more-about-converting-words-to-vectors"></a>Meer informatie over het converteren van woorden naar vectoren

Het converteren van woorden naar vectoren, of woord vectorization, is een NLP-proces (natuurlijke taal verwerking). Het proces gebruikt taal modellen om woorden in de vector ruimte toe te wijzen. Met een vector ruimte wordt elk woord aangeduid met een vector met echte getallen. Ook kunnen woorden met vergelijk bare betekeniss een vergelijk bare weer gave hebben.

Gebruik Word-insluitingen als eerste invoer voor NLP downstream-taken, zoals tekst classificatie en sentiment-analyse.

In deze module hebben we onder verschillende technologieën voor het insluiten van woorden drie veelgebruikte methoden geïmplementeerd. Twee, Word2Vec en FastText, zijn online trainings modellen. De andere is een vooraf getraind model, ondersteunt-wiki-gigaword-100. 

Online trainings modellen worden getraind op basis van uw invoer gegevens. Voortrainde modellen worden offline getraind op een grotere tekst verzameling (bijvoorbeeld Wikipedia, Google News) die meestal ongeveer 100.000.000.000 woorden bevat. Het insluiten van woorden blijft constant tijdens het woord vectorization. Pretrainde woord modellen bieden voor delen zoals gereduceerde training, betere woord vectoren die zijn gecodeerd en verbeterde algehele prestaties.

Hier volgt informatie over de methoden:

+ Word2Vec is een van de populairste technieken voor het leren van woord insluitingen met behulp van een groot Neural-netwerk. De theorie wordt in dit document besproken en beschikbaar als een PDF-down load: een [efficiënte schatting van de representaties van woorden in een vector ruimte](https://arxiv.org/pdf/1301.3781.pdf). De implementatie in deze module is gebaseerd op de [Gensim-bibliotheek voor Word2Vec](https://radimrehurek.com/gensim/models/word2vec.html).

+ De FastTexte theorie wordt in dit artikel beschreven, die beschikbaar is als PDF-down load: [verrijkt woord vectoren met informatie over subwoorden](https://arxiv.org/pdf/1607.04606.pdf). De implementatie in deze module is gebaseerd op de [Gensim-bibliotheek voor FastText](https://radimrehurek.com/gensim/models/fasttext.html).

+ Het vooraf getrainde ondersteunt-model is ondersteunt-wiki-gigaword-100. Het is een verzameling van getrainde vectoren op basis van een Wikipedia-tekst verzameling, die 5.600.000.000-tokens en 400.000 niet-hoofdletter woorden bevat. Er is een PDF-down load beschikbaar: [ondersteunt: globale vectoren voor woord weergave](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>Conversie van woord naar vector configureren

Deze module vereist een gegevensset die een tekst kolom bevat. Voor verwerkte tekst is beter.

1. Voeg de module **woord naar vector converteren** toe aan de pijp lijn.

2. Geef als invoer voor de module een gegevensset op die een of meer tekst kolommen bevat.

3. Kies bij **doel kolom** slechts één kolom die de tekst bevat die moet worden verwerkt.

    Omdat deze module een vocabulaire van tekst maakt, verschilt de inhoud van de kolommen, wat leidt tot een andere woordenlijst inhoud. Daarom accepteert de module slechts één doel kolom.

4. Voor  **Word2Vec-strategie** kiest u **uit ondersteunt pretraind English model**, **Gensim Word2Vec** en **Gensim FastText**.

5. Als de **Word2Vec-strategie** **Gensim Word2Vec** of **Gensim FastText** is:

    + Kies uit **Skip_gram** en **CBOW** voor **Word2Vec-trainings algoritme**. Het verschil wordt geïntroduceerd in het [oorspronkelijke papier (PDF)](https://arxiv.org/pdf/1301.3781.pdf).

        De standaard methode is **Skip_gram**.

    + Voor de **lengte van het insluiten van woorden** geeft u de dimensionaliteit van de woord vectoren op. Deze instelling komt overeen met de `size` para meter in Gensim.

        De standaard insluit grootte is 100.

    + Voor de grootte van het **context venster** geeft u de maximale afstand op tussen het woord dat wordt voorspeld en het huidige woord. Deze instelling komt overeen met de `window` para meter in Gensim.

        De standaard venster grootte is 5.

    + Voor het **aantal epoches** geeft u het aantal epoches (iteraties) op voor de verzameling. Komt overeen met de `iter` para meter in Gensim.

        Het nummer van de standaard-epoche is 5.

6. Voor **maximale woordenlijst grootte** geeft u het maximum aantal woorden in de gegenereerde vocabulaire op.

    Als er meer unieke woorden zijn dan de maximale grootte, haalt u de voor vallen weg.

    De standaard woordenlijst grootte is 10.000.

7. Geef voor het **minimum aantal woorden** een minimum aantal woorden op. Alle woorden met een frequentie die lager is dan deze waarde, worden door de module genegeerd.

    De standaard waarde is 5.

8. Verzend de pijp lijn.

## <a name="examples"></a>Voorbeelden

De module heeft één uitvoer:

+ **Vocabulaire with Embedding**: bevat de gegenereerde vocabulaire, samen met het insluiten van elk woord. Eén dimensie neemt één kolom in beslag.

In het volgende voor beeld ziet u hoe de module woord converteren naar vector werkt. De functie gebruikt voor het converteren van Word naar vector met de standaard instellingen naar de voor verwerking van de gegevensset van de voorverwerkte Wikipedia-SP 500.

### <a name="source-dataset"></a>Brongegevensset

De gegevensset bevat een kolom categorie, samen met de volledige tekst opgehaald uit Wikipedia. De volgende tabel bevat enkele representatieve voor beelden.

|Tekst|
|----------|
|Nasdaq 100 component s p 500 component Foundation oprichter locatie City Apple Campus 1 oneindige lus straat Cupertino Californië Cupertino Californië locatie land Verenigde Staten...|
|BR Nasdaq 100 Nasdaq 100 component BR s p 500 s p 500 component Industry computer Software Foundation BR oprichter Charles Geschke BR John Warnock locatie Adobe Systems...|
|s p 500 s p 500 component Industry automobiel automobiel van de 1908 2009 onderneming voorafgaande aan de opvolgende taak...|
|s p 500 s p 500 component Industry conglomeraat Company conglomeraat Foundation oprichter locatie City Fairfield Connecticut Fairfield Connecticut locatie land USA gebied...|
|BR s p 500 s p 500 component Foundation 1903 oprichter William s Harley BR Arthur Davidson Harley Davidson oprichter Arthur Davidson BR Walter Davidson BR William a Davidson location...|

### <a name="output-vocabulary-with-embeddings"></a>Uitvoer woordenlijst met insluitingen

De volgende tabel bevat de uitvoer van deze module, waarbij de Wikipedia SP 500-gegevensset wordt ingevoerd als invoer. De meest linkse kolom geeft de woorden lijst aan. De Inge sloten vector wordt vertegenwoordigd door de waarden van de resterende kolommen in dezelfde rij.

|Vocabulaire|Dimmen 0 insluiten|DIMM 1 insluiten|DIMM 2 insluiten|Dimensie 3 insluiten|DIMM 4 insluiten|DIMM 5 insluiten|...|Dim-99 insluiten|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|nasdaq|-0,375865|0,609234|0,812797|-0,002236|0,319071|-0,591986|...|0,364276
|component|0,081302|0,40001|0,121803|0,108181|0,043651|-0,091452|...|0,636587
|s|-0,34355|-0,037092|-0,012167|0,151542|0,601019|0,084501|...|0,149419
|p|-0,133407|0,073244|0,170396|0,326706|0,213463|-0,700355|...|0,530901
onderkant|-0,166819|0,10883|-0,07933|-0,073753|0,262137|0,045725|...|0,27487
oprichter|-0,297408|0,493067|0,316709|-0,031651|0,455416|-0,284208|...|0,22798
location|-0,375213|0,461229|0,310698|0,213465|0,200092|0,314288|...|0,14228
city|-0,460828|0,505516|-0,074294|-0,00639|0,116545|0,494368|...|-0,2403
Apple|0,05779|0,672657|0,597267|-0,898889|0,099901|0,11833|...|0,4636
Campus|-0,281835|0,29312|0,106966|-0,031385|0,100777|-0,061452|...|0,05978
onbeperkt|-0,263074|0,245753|0,07058|-0,164666|0,162857|-0,027345|...|-0,0525
lus|-0,391421|0,52366|0,141503|-0,105423|0,084503|-0,018424|...|-0,0521

In dit voor beeld hebben we de standaard **Gensim-Word2Vec** voor **Word2Vec-strategie** gebruikt en is het **trainings algoritme** **Skip-gram**. De **lengte van het insluiten van woorden** is 100, dus hebben we 100 kolommen moeten insluiten.

## <a name="technical-notes"></a>Technische opmerkingen

Deze sectie bevat tips en antwoorden op veelgestelde vragen.

+ Verschil tussen online trainingen en vooraf getrainde modellen:

    In dit voor woord omzetten in een vector module hebben we drie verschillende strategieën gegeven: twee online trainings modellen en één vooraf getraind model. De online trainings modellen maken gebruik van uw invoer gegevensset als trainings gegevens en genereren een vocabulaire-en woord Vector tijdens de training. Het vooraf getrainde model wordt al getraind door een veel grotere tekst verzameling, zoals Wikipedia of Twitter-tekst. Het voortrainde model is in feite een verzameling van woord-en sluitings paren.  

    Het ondersteunt vooraf getrainde model bevat een samen vatting van een vocabulaire van de invoer gegevensset en genereert een insluitings vector voor elk woord uit het vooraf getrainde model. Zonder online training kan het gebruik van een voortraind model trainings tijd besparen. Het heeft betere prestaties, vooral wanneer de grootte van de invoer gegevensset relatief klein is.

+ Grootte van insluiten:

    In het algemeen is de lengte van het insluiten van woorden ingesteld op honderden. Bijvoorbeeld 100, 200, 300. Een kleine insluitings grootte betekent een kleine vector ruimte, wat kan leiden tot conflicten bij het insluiten van woorden.  

    De lengte van woord insluitingen is vast voor voortrainde modellen. In dit voor beeld is de insluitings grootte van ondersteunt-wiki-gigaword-100 100.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

Zie [machine learning fout codes](designer-error-codes.md)voor een lijst met fouten die specifiek zijn voor de ontwerp modules.
