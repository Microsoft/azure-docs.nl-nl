---
title: 'Latente Dirichlet toewijzing: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u de Dirichlet-toewijzings module kunt gebruiken om niet-geclassificeerde tekst in categorieën te groeperen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/05/2020
ms.openlocfilehash: f9f239ea69aaf71e591a447feb300c13a45ba1a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90907849"
---
# <a name="latent-dirichlet-allocation-module"></a>Dirichlet-toewijzings module

In dit artikel wordt beschreven hoe u de verwijzende module Dirichlet toewijzing in Azure Machine Learning Designer kunt gebruiken om niet-geclassificeerde tekst in categorieën te groeperen. 

Latente Dirichlet toewijzing (LDA) wordt vaak gebruikt voor de verwerking van natuurlijke taal om teksten te vinden die vergelijkbaar zijn. Een andere algemene term is een *onderwerp model lering*.

Deze module haalt een tekst kolom op en genereert de volgende uitvoer:

+ De bron tekst, samen met een score voor elke categorie

+ Een functie matrix die geëxtraheerde voor waarden en coëfficiënten voor elke categorie bevat

+ Een trans formatie, die u kunt opslaan en opnieuw Toep assen op nieuwe tekst die als invoer wordt gebruikt

Deze module maakt gebruik van de scikit-Learn-bibliotheek. Zie de [github-opslag plaats](https://github.com/scikit-learn/scikit-learn)met zelf studies en een uitleg van het algoritme voor meer informatie over scikit-informatie.

## <a name="more-about-latent-dirichlet-allocation"></a>Meer informatie over de toewijzing van latente Dirichlet

LDA is doorgaans geen methode voor classificatie. Maar maakt gebruik van een methode voor ontdubbeling, dus u hoeft geen bekende klassen labels te verstrekken en vervolgens de patronen af te leiden.  In plaats daarvan wordt een Probabilistic-model gegenereerd dat wordt gebruikt om groepen met onderwerpen te identificeren. U kunt het Probabilistic-model gebruiken om bestaande trainings cases of nieuwe cases die u aan het model hebt verstrekt, te classificeren als invoer.

U kunt de voor keur geven aan een uitzonderings model omdat het voor komt dat er sterke hypo Thesen worden gemaakt over de relatie tussen de tekst en categorieën. Het gebruikt alleen de distributie van woorden voor wiskundige model onderwerpen.

De theorie wordt in dit document besproken en beschikbaar als een PDF-down load: latente [Dirichlet toewijzing: Blei, ng en Jordanië](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf).

De implementatie in deze module is gebaseerd op de [scikit-Learn-bibliotheek](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) voor Lda.

Zie de sectie met [technische opmerkingen](#technical-notes) voor meer informatie.

## <a name="how-to-configure-latent-dirichlet-allocation"></a>De toewijzing van latente Dirichlet configureren

Deze module vereist een gegevensset die een kolom met tekst bevat, hetzij RAW ofwel preprocesd.

1. Voeg de **Dirichlet-toewijzings** module aan uw pijp lijn toe.

2. Geef als invoer voor de module een gegevensset op die een of meer tekst kolommen bevat.

3. Kies voor **doel kolommen** een of meer kolommen die te analyseren tekst bevatten.

    U kunt meerdere kolommen kiezen, maar deze moeten van het **teken reeks** gegevens type zijn.

    Omdat LDA een grote functie matrix maakt op basis van de tekst, analyseert u doorgaans een enkele tekst kolom.

4. Voer een geheel getal in tussen 1 en 1000 dat aangeeft hoeveel categorieën of onderwerpen u wilt afleiden van de invoer tekst voor het  **aantal onderwerpen dat moet worden gemodelleerd**.

    Standaard worden er vijf onderwerpen gemaakt.

5. Voor **n-gram** geeft u de maximale lengte van n-gram op die tijdens hashing wordt gegenereerd.

    De standaard waarde is 2, wat betekent dat zowel bigrams als unigrams worden gegenereerd.

6. Selecteer de optie **normaliseren** om de uitvoer waarden te converteren naar kansen. 

    In plaats van de getransformeerde waarden als gehele getallen weer te geven, worden de waarden in de uitvoer-en functie gegevensset als volgt getransformeerd:

    + De waarden in de gegevensset worden weer gegeven als een kans waarbij `P(topic|document)` .

    + Waarden in de matrix van het functie-onderwerp worden weer gegeven als een kans waar `P(word|topic)` .

    > [!NOTE] 
    > In Azure Machine Learning Designer biedt de bibliotheek scikit-Learn geen ondersteuning meer voor ongebruikelijke *doc_topic_distr* uitvoer van versie 0,19. In deze module kan de para meter **normaliseren** alleen worden toegepast op de matrix uitvoer van het *functie onderwerp* . De uitvoer van de *getransformeerde gegevensset* is altijd genormaliseerd.

7. Selecteer de optie **alle opties weer geven** en stel deze in op **waar** als u de volgende geavanceerde para meters wilt instellen.

    Deze para meters zijn specifiek voor de scikit-leer implementatie van LDA. Er zijn een aantal goede zelf studies over LDA in scikit-informatie, evenals het officiële [scikit-document](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html).

    + **Rho-para meter**. Geef een eerdere kans op voor de sparsity van onderwerp-distributies. Deze para meter komt overeen met de `topic_word_prior` para meter sklearn. Gebruik de waarde **1** als u verwacht dat de distributie van woorden plat is; dat wil zeggen dat alle woorden worden aangenomen equiprobable. Als u denkt dat de meeste woorden verspreid worden weer gegeven, kunt u deze op een lagere waarde instellen.

    + **Alfa-para meter**. Geef een eerdere kans op voor de sparsity van het onderwerp gewicht per document. Deze para meter komt overeen met de `doc_topic_prior` para meter sklearn.

    + Het **geschatte aantal documenten**. Voer een getal in dat overeenkomt met de beste schatting van het aantal documenten (rijen) dat wordt verwerkt. Met deze para meter kan de module een hash-tabel met voldoende grootte toewijzen. Dit komt overeen met de `total_samples` para meter in scikit-learn.

    + **De grootte van de batch**. Voer een getal in dat aangeeft hoeveel rijen moeten worden ingevoegd in elke batch tekst die wordt verzonden naar het LDA-model. Deze para meter komt overeen met de `batch_size` para meter in scikit-learn.

    + De **begin waarde van de iteratie die wordt gebruikt voor het plannen van de trainings update**. Geef de begin waarde op waarmee het leer tempo voor vroege iteraties in online leren wordt downweights. Deze para meter komt overeen met de `learning_offset` para meter in scikit-learn.

    + **Kracht die tijdens de updates op de herhaling wordt toegepast**. Geef het energie niveau op dat wordt toegepast op het aantal iteraties voor het beheren van het leer tempo tijdens online-updates. Deze para meter komt overeen met de `learning_decay` para meter in scikit-learn.

    + **Aantal Passes over de gegevens**. Geef het maximum aantal keren op dat het algoritme voor de gegevens wordt gerecycled. Deze para meter komt overeen met de `max_iter` para meter in scikit-learn.

8. Selecteer de optie **woorden lijst build van ngrams** of **Build Dictionary van ngrams vóór LDA**. Als u de n-gram lijst in een eerste keer wilt maken voordat u tekst classificeert.

    Als u de eerste woorden lijst vooraf maakt, kunt u later de woorden lijst gebruiken bij het controleren van het model. Het is over het algemeen eenvoudiger om resultaten toe te wijzen aan tekst in plaats van numerieke indexen. Het opslaan van de woorden lijst duurt echter langer en het gebruik van extra opslag ruimte.

9. Voer voor **maximale grootte van ngram-woorden lijst** het totale aantal rijen in dat kan worden gemaakt in de n-gram-woorden lijst.

    Deze optie is handig voor het beheren van de grootte van de woorden lijst. Maar als het aantal ngrams in de invoer groter is dan deze grootte, kunnen er conflicten optreden.

10. Verzend de pijp lijn. De LDA-module maakt gebruik van Bayes theorema om te bepalen welke onderwerpen kunnen worden gekoppeld aan afzonderlijke woorden. Woorden die niet exclusief zijn gekoppeld aan onderwerpen of groepen. In plaats daarvan heeft elke n-gram een geleerde kans om te koppelen aan een van de gedetecteerde klassen.

## <a name="results"></a>Resultaten

De module heeft twee uitvoer:

+ **Getransformeerde gegevensset**: deze uitvoer bevat de invoer tekst, een opgegeven aantal gedetecteerde categorieën en de scores voor elk tekst voorbeeld voor elke categorie.

+ **Functie onderwerp matrix**: de meest linkse kolom bevat de geëxtraheerde tekst functie. Een kolom voor elke categorie bevat de score voor die functie in die categorie.


### <a name="lda-transformation"></a>LDA-trans formatie

Deze module voert ook de *LDA-trans formatie* uit waarmee Lda op de gegevensset wordt toegepast.

U kunt deze trans formatie opslaan en opnieuw gebruiken voor andere gegevens sets. Deze techniek kan nuttig zijn als u hebt getraind op een grote verzameling en u de coëfficiënten of categorieën opnieuw wilt gebruiken.

Als u deze trans formatie opnieuw wilt gebruiken, selecteert u het pictogram **gegevensset registreren** in het rechter paneel van de Dirichlet toewijzings module om de module onder de categorie **gegevens sets** in de module lijst te laten staan. Vervolgens kunt u deze module verbinden met de module [trans formatie Toep assen](apply-transformation.md) om deze trans formatie opnieuw te gebruiken.

### <a name="refining-an-lda-model-or-results"></a>Een LDA-model of-resultaten verfijnen

Normaal gesp roken kunt u geen enkel LDA-model maken dat aan alle behoeften voldoet. Zelfs een model dat is ontworpen voor een taak kan veel herhalingen vereisen om de nauw keurigheid te verbeteren. We raden u aan al deze methoden te proberen om uw model te verbeteren:

+ De para meters van het model wijzigen
+ Visualisatie gebruiken om de resultaten te begrijpen
+ Feedback geven over experts om te bepalen of de gegenereerde onderwerpen handig zijn

Kwalitatieve maat regelen kunnen ook handig zijn voor het beoordelen van de resultaten. Als u de resultaten van een onderwerpen model wilt evalueren, kunt u overwegen:

+ Nauw keurigheid. Zijn Vergelijk bare items heel vergelijkbaar?
+ Situaties. Kan het model een onderscheid tussen vergelijk bare items hebben als dat nodig is voor het bedrijfs probleem?
+ Schaalbaarheid. Werkt het in een breed scala aan tekst categorieën of alleen op een beperkt doel domein?

U kunt de nauw keurigheid van modellen op basis van LDA vaak verbeteren door de verwerking van natuurlijke taal te gebruiken om tekst te reinigen, samen te vatten en te vereenvoudigen of te categoriseren. De volgende technieken, die allemaal worden ondersteund in Azure Machine Learning, kunnen de classificatie nauw keurigheid verbeteren:

+ Verwijderen van woorden stoppen

+ Case-normalisatie

+ Lemmatisering of stam gebruik

+ Herkenning van tekeneenheden

Zie [preproces-tekst](preprocess-text.md)voor meer informatie.

In de ontwerp functie kunt u ook R-of python-bibliotheken gebruiken voor tekst verwerking: [R-script uitvoeren](execute-r-script.md),  [python-script uitvoeren](execute-python-script.md).



## <a name="technical-notes"></a>Technische opmerkingen

Deze sectie bevat implementatie details, tips en antwoorden op veelgestelde vragen.

### <a name="implementation-details"></a>Implementatiegegevens

Standaard worden de distributies van uitvoer voor een getransformeerde gegevensset en functie-topic genormaliseerd als kansen:

+ De getransformeerde gegevensset wordt genormaliseerd als de voorwaardelijke kans van onderwerpen op basis van een document. In dit geval is de som van elke rij gelijk aan 1.

+ De functie-onderwerp matrix wordt genormaliseerd als de voorwaardelijke waarschijnlijkheid van woorden op basis van een onderwerp. In dit geval is de som van elke kolom gelijk aan 1.

> [!TIP]
> Af en toe kan de module een leeg onderwerp retour neren. Meestal is de oorzaak een pseudo-wille keurige initialisatie van de algoritme. Als dit het geval is, kunt u proberen om gerelateerde para meters te wijzigen. Wijzig bijvoorbeeld de maximale grootte van de N-gram woordenlijst of het aantal bits dat moet worden gebruikt voor functie-hashing.

### <a name="lda-and-topic-modeling"></a>LDA en topics model lering

Een latente Dirichlet toewijzing wordt vaak gebruikt voor *model lering op basis van inhoud*, wat in principe de lessen van niet-geclassificeerde tekst aangeeft. In een onderwerp dat is gebaseerd op inhouds modellen, is een onderwerp een distributie over woorden.

Stel dat u een verzameling van klant beoordelingen hebt geleverd die veel producten bevatten. De tekst van beoordelingen die zijn ingediend door klanten in de loop van de tijd bevat veel voor waarden, waarvan sommige worden gebruikt in meerdere onderwerpen.

Een *onderwerp* dat door het LDA-proces wordt geïdentificeerd, kan Beoordelingen voor een afzonderlijk product vertegenwoordigen of kan een groep product beoordelingen vertegenwoordigen. Voor LDA is het onderwerp zelf een waarschijnlijke verdeling van de duur van een aantal woorden.

De voor waarden zijn zelden exclusief voor een product. Ze kunnen verwijzen naar andere producten of algemene voor waarden die van toepassing zijn op alles (' geweldig ', ' Awful '). Andere voor waarden kunnen ruis woorden zijn. De methode LDA probeert echter niet alle woorden in het universum op te nemen of om te begrijpen hoe woorden zijn gerelateerd, afgezien van de waarschijnlijkheid van co-voorval. Alleen woorden die in het doel domein worden gebruikt, kunnen worden gegroepeerd.

Nadat de termen van de term zijn berekend, vergelijkt een op afstand gebaseerde soort gelijke maat regel afzonderlijke tekst rijen om te bepalen of twee stukken tekst vergelijkbaar zijn. U kunt bijvoorbeeld zien dat het product meerdere namen heeft die sterk worden gecorreleerd. Het is ook mogelijk dat zeer negatieve voor waarden doorgaans zijn gekoppeld aan een bepaald product. U kunt de maat eenheid van de overeenkomst gebruiken om gerelateerde voor waarden te identificeren en aanbevelingen te maken.

###  <a name="module-parameters"></a>Module parameters

|Naam|Type|Bereik|Optioneel|Standaard|Beschrijving|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Doel kolom (men)|Kolom selectie||Vereist|StringFeature|De naam of index van de doel kolom.|  
|Aantal te model lerene onderwerpen|Geheel getal|[1; 1000]|Vereist|5|Model de document distributie met N onderwerpen.|  
|N-gram|Geheel getal|[1; 10]|Vereist|2|De volg orde van N-gram die tijdens de hash-bewerking is gegenereerd.|  
|Normaliseren|Booleaans|Waar of onwaar|Vereist|true|Normaliseer uitvoer naar kansen.  De getransformeerde gegevensset is P (onderwerp&#124;document) en de matrix van het functie onderwerp is P (woord&#124;onderwerp).|  
|Alle opties weer geven|Booleaans|Waar of onwaar|Vereist|Niet waar|Geeft aanvullende para meters die specifiek zijn voor scikit-Learn Online LDA.|  
|Rho-para meter|Float|[0.00001; 1.0]|Van toepassing wanneer het selectie vakje **alle opties weer geven** is ingeschakeld|0,01|Voorafgaande distributie van onderwerps woord.|  
|Alpha-para meter|Float|[0.00001; 1.0]|Van toepassing wanneer het selectie vakje **alle opties weer geven** is ingeschakeld|0,01|Document onderwerp voorafgaande distributie.|  
|Geschat aantal documenten|Geheel getal|[1; int. MaxValue|Van toepassing wanneer het selectie vakje **alle opties weer geven** is ingeschakeld|1000|Het geschatte aantal documenten. Komt overeen met de `total_samples` para meter.|  
|Grootte van de batch|Geheel getal|[1; 1024]|Van toepassing wanneer het selectie vakje **alle opties weer geven** is ingeschakeld|32|De grootte van de batch.|  
|Aanvankelijke waarde van herhaling die wordt gebruikt voor de update planning voor het trainings tempo|Geheel getal|[0; int. MaxValue|Van toepassing wanneer het selectie vakje **alle opties weer geven** is ingeschakeld|0|Aanvankelijke waarde voor downweights Learning rate voor vroege iteraties. Komt overeen met de `learning_offset` para meter.|  
|Kracht toegepast op de herhaling tijdens updates|Float|[0,0; 1.0]|Van toepassing wanneer het selectie vakje **alle opties weer geven** is ingeschakeld|0,5|Kracht toegepast op het aantal iteraties om het leer tempo te beheren. Komt overeen met de `learning_decay` para meter. |  
|Aantal opleidings herhalingen|Geheel getal|[1; 1024]|Van toepassing wanneer het selectie vakje **alle opties weer geven** is ingeschakeld|25|Aantal opleidings herhalingen.|  
|Woorden lijst van ngrams bouwen|Booleaans|Waar of onwaar|Van toepassing wanneer het selectie vakje **alle opties weer geven** *is uitgeschakeld*|Waar|Bouwt een woorden lijst van ngrams vóór de Computing LDA. Nuttig voor het controleren van modellen en interpretaties.|  
|Maximale grootte van ngram-woorden lijst|Geheel getal|[1; int. MaxValue|Van toepassing wanneer de keuze **lijst Dictionary van Ngrams** **waar** is|20.000|Maximale grootte van de ngrams-woorden lijst. Als het aantal tokens in de invoer groter is dan deze grootte, kunnen er conflicten optreden.|  
|Het aantal bits dat moet worden gebruikt voor functie-hashing.|Geheel getal|[1; 31]|Van toepassing wanneer het selectie vakje **alle opties weer geven** *niet* is ingeschakeld en het **bouwen van de woorden lijst ngrams** is ingesteld op **False**|12|Het aantal bits dat moet worden gebruikt voor functie-hashing.| 
|Build Dictionary van ngrams vóór LDA|Booleaans|Waar of onwaar|Van toepassing wanneer het selectie vakje **alle opties weer geven** is ingeschakeld|Waar|Hiermee maakt u een woorden lijst met ngrams vóór LDA. Nuttig voor het controleren van modellen en interpretaties.|  
|Maximum aantal ngrams in woorden lijst|Geheel getal|[1; int. MaxValue|Van toepassing wanneer het selectie vakje **alle opties weer geven** is ingeschakeld en de optie **woorden lijst van ngrams** is ingesteld op **True**|20.000|Maximum grootte van de woorden lijst. Als het aantal tokens in de invoer groter is dan deze grootte, kunnen er conflicten optreden.|  
|Aantal hash-bits|Geheel getal|[1; 31]|Van toepassing wanneer het selectie vakje **alle opties weer geven** is ingeschakeld en de optie **woorden lijst van ngrams** is ingesteld op **False**|12|Aantal bits dat moet worden gebruikt tijdens het hashen van functies.|   


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

Zie [uitzonde ringen en fout codes voor de ontwerp functie](designer-error-codes.md)voor een lijst met fouten die specifiek zijn voor de modules.
