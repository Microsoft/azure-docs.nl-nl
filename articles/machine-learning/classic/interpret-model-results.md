---
title: 'ML Studio (klassiek): model resultaten interpreteren-Azure'
description: De optimale parameterset voor een algoritme kiezen met behulp van en het visualiseren van score model uitvoer.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.date: 11/29/2017
ms.openlocfilehash: f824cf6e30ed795ef0fe4468542ae16c801bd61b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516121"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio-classic"></a>Modelresultaten in Azure Machine Learning Studio (klassiek) interpreteren

**VAN TOEPASSING OP:**  ![Dit is een vinkje, wat betekent dat dit artikel van toepassing is op Machine Learning Studio (klassiek).](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek)   ![Dit is een X, wat betekent dat dit artikel van toepassing is op Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

In dit onderwerp wordt uitgelegd hoe u de Voorspellings resultaten in Azure Machine Learning Studio (klassiek) kunt visualiseren en interpreteren. Nadat u een model hebt getraind en voor spellingen hebt uitgevoerd (het ' gescoorde ' model '), moet u het Voorspellings resultaat begrijpen en interpreteren.

Er zijn vier hoofd soorten machine learning modellen in Azure Machine Learning Studio (klassiek):

* Classificatie
* Clustering
* Regressie
* Aanbevolen systemen

De modules die worden gebruikt voor de voor spelling boven op deze modellen zijn:

* Module [score model][score-model] voor classificatie en regressie
* [Toewijzen aan clusters][assign-to-clusters] module voor clustering
* [Score matchbox aanbevolen][score-matchbox-recommender] voor aanbevolen systemen

Meer informatie over het [kiezen van para meters voor het optimaliseren van uw algoritmen in ml Studio (klassiek)](algorithm-parameters-optimize.md).

Zie de prestaties van een [model evalueren](evaluate-model-performance.md)voor meer informatie over het evalueren van uw modellen.

Als u geen ervaring hebt met ML Studio (klassiek), kunt u [leren hoe u een eenvoudig experiment kunt maken](create-experiment.md).

## <a name="classification"></a>Classificatie
Er zijn twee subcategorieën van classificatie problemen:

* Problemen met slechts twee klassen (twee klassen of binaire classificatie)
* Problemen met meer dan twee klassen (classificatie met meerdere klassen)

Azure Machine Learning Studio (klassiek) heeft verschillende modules die met elk van deze typen classificatie kunnen omgaan, maar de methoden voor het interpreteren van de resultaten van de voor spellingen zijn vergelijkbaar.

### <a name="two-class-classification"></a>Classificatie met twee klassen
**Voorbeeldexperiment**

Een voor beeld van een classificatie probleem met twee klassen is de classificatie van Iris bloemen. De taak is het classificeren van Iris bloemen op basis van hun functies. De Iris gegevensset die is opgegeven in Azure Machine Learning Studio (klassiek) is een subset van de populaire [Iris gegevensset](https://en.wikipedia.org/wiki/Iris_flower_data_set) met slechts exemplaren van twee bloem soorten (klassen 0 en 1). Er zijn vier functies voor elke bloem (SEPA-lengte, breedte van het SEPA, lengte van de bloem en breedte van het bloem).

![Scherm opname van Iris experiment](./media/interpret-model-results/1.png)

Afbeelding 1. Probleem met het classificeren van twee klassen voor Iris

Er is een experiment uitgevoerd om dit probleem op te lossen, zoals wordt weer gegeven in afbeelding 1. Een gestimuleerd beslissings structuur model met twee klassen is getraind en gescoord. Nu kunt u de Voorspellings resultaten van de module [score model][score-model] visualiseren door te klikken op de uitvoer poort van de module [score model][score-model] en vervolgens te klikken op **visualiseren**.

![Module score model](./media/interpret-model-results/1_1.png)

Hiermee worden de Score resultaten weer gegeven, zoals aangegeven in afbeelding 2.

![Resultaten van een met twee klassen geclassificeerde experimenten](./media/interpret-model-results/2.png)

Afbeelding 2. Een score model visualiseren als gevolg van een classificatie met twee klassen

**Resultaten interpretatie**

De resultaten tabel bevat zes kolommen. De vier meest linkse kolommen zijn de vier functies. De juiste twee kolommen, gescoorde labels en gescoorde kansen, zijn de Voorspellings resultaten. In de kolom gescoorde kansen ziet u de kans dat een bloem deel uitmaakt van de positieve klasse (klasse 1). Het eerste getal in de kolom (0,028571) betekent bijvoorbeeld dat er 0,028571 waarschijnlijk is dat de eerste bloem deel uitmaakt van klasse 1. In de kolom scoored labels wordt de voorspelde klasse voor elke bloem weer gegeven. Dit is gebaseerd op de kolom gescoorde kansen. Als de gescoorde kans van een bloem groter is dan 0,5, wordt het voor speld als klasse 1. Anders wordt het voor speld als klasse 0.

**Publicatie van de webservice**

Nadat de resultaten van de voor spelling zijn geïnterpreteerd en geoordeeld, kan het experiment worden gepubliceerd als een webservice, zodat u het kunt implementeren in verschillende toepassingen en hoe u de voor spellingen van de klasse van een nieuwe Iris bloem kan aanroepen. Zie [zelf studie 3: krediet risico model implementeren](tutorial-part3-credit-risk-deploy.md)voor meer informatie over het wijzigen van een studie experiment in een score experiment en om het te publiceren als webservice. Met deze procedure kunt u een score experiment bekijken, zoals wordt weer gegeven in afbeelding 3.

![Scherm afbeelding van scores experiment](./media/interpret-model-results/3.png)

Afbeelding 3. Het afexperimenteren van een probleem met twee klassen van de Iris

Nu moet u de invoer en uitvoer voor de webservice instellen. De invoer is de juiste invoer poort van het [score model][score-model], wat de invoer van de Iris bloem-functies is. De keuze van de uitvoer is afhankelijk van of u geïnteresseerd bent in de voorspelde klasse (gescoord label), de gescoorde waarschijnlijkheid of beide. In dit voor beeld wordt ervan uitgegaan dat u bent geïnteresseerd in beide. Als u de gewenste uitvoer kolommen wilt selecteren, gebruikt u de module [select columns in data set][select-columns] . Klik op [kolommen selecteren in gegevensset][select-columns], klik op **kolom kiezer starten** en selecteer **gescoorde labels** en **gescoorde kansen**. Nadat u de uitvoer poort van [geselecteerde kolommen in de gegevensset][select-columns] hebt ingesteld en deze opnieuw uitvoert, moet u klaar zijn om het Score experiment als een webservice te publiceren door te klikken op **Web service publiceren**. Het laatste experiment ziet eruit als afbeelding 4.

![Het experiment voor de classificatie met twee klassen van Iris](./media/interpret-model-results/4.png)

Afbeelding 4: Laatste score beoordelings experiment van een Iris probleem met twee klassen

Nadat u de webservice hebt uitgevoerd en enkele onderdeel waarden van een test instantie hebt opgegeven, retourneert het resultaat twee getallen. Het eerste nummer is het gescoorde label en de tweede is de gescoorde kans. Deze bloem wordt voor speld als klasse 1 met een kans van 0,9655.

![Testen van score model analyseren](./media/interpret-model-results/4_1.png)

![Resultaten van Score test](./media/interpret-model-results/5.png)

Afbeelding 5. Webservice resultaat van een twee klassen classificatie van Iris

### <a name="multi-class-classification"></a>Classificatie met meerdere klassen
**Voorbeeldexperiment**

In dit experiment voert u een taak voor het herkennen van een brief uit als voor beeld van een classificatie met een meervoudige klasse. De classificatie probeert een bepaalde letter% 28class %29 te voors pellen op basis van bepaalde hand geschreven kenmerk waarden die zijn geëxtraheerd uit de hand geschreven afbeeldingen.

![Voor beeld van letter herkenning](./media/interpret-model-results/5_1.png)

In de trainings gegevens zijn er 16 functies geëxtraheerd uit handgeschreven letter beelden. 26 letters vormen onze 26 klassen. In afbeelding 6 ziet u een experiment waarmee u een classificatie model met een klasse voor de spraak herkenning kunt trainen en voors pellen op dezelfde functie die is ingesteld voor een test gegevensset.

![Proef experiment voor het classificeren van multi klassen](./media/interpret-model-results/6.png)

Afbeelding 6. Letter herkenning van het probleem met de classificatie van multi klassen

Het visualiseren van de resultaten van de module [score model][score-model] door te klikken op de uitvoer poort van de module [score model][score-model] en vervolgens te klikken op **visualiseren**, ziet u inhoud zoals weer gegeven in afbeelding 7.

![Resultaten van score model](./media/interpret-model-results/7.png)

Afbeelding 7. De resultaten van een score model visualiseren in een classificatie met meerdere klassen

**Resultaten interpretatie**

De links 16 kolommen vertegenwoordigen de functie waarden van de testset. De kolommen met namen zoals een gescoorde waarschijnlijkheid voor klasse "XX" zijn net als de kolom met de gescoorde kansen in het geval van twee klassen. Ze tonen de waarschijnlijkheid dat de overeenkomende vermelding in een bepaalde klasse valt. Voor de eerste vermelding is er bijvoorbeeld 0,003571 kans dat het een "A" 0,000451 waarschijnlijk is dat het een "B,", enzovoort. De laatste kolom (labels met punten) is hetzelfde als de gescoorde labels in de twee klassen. De klasse met de grootste waarschijnlijkheid wordt geselecteerd als de voorspelde klasse van de corresponderende vermelding. Bijvoorbeeld: voor de eerste vermelding is het Score label ' F ', omdat het de grootste kans heeft op ' F ' (0,916995).

**Publicatie van de webservice**

U kunt ook het gescoorde label voor elke vermelding en de waarschijnlijkheid van het label van de Score ophalen. De basis logica is het vinden van de grootste kans op alle gescoorde kansen. Hiervoor moet u de module [R-script uitvoeren][execute-r-script] gebruiken. De R-code wordt weer gegeven in afbeelding 8 en het resultaat van het experiment wordt weer gegeven in afbeelding 9.

![R-code voorbeeld](./media/interpret-model-results/8.png)

Afbeelding 8. R-code voor het uitpakken van gescoorde labels en de bijbehorende kansen van de labels

![Experiment resultaat](./media/interpret-model-results/9.png)

Afbeelding 9. Laatste score beoordelings experiment van het probleem met de classificatie met meervoudige klassen

Nadat u de webservice hebt gepubliceerd en uitgevoerd en enkele invoer waarden hebt ingevoerd, ziet het geretourneerde resultaat eruit als afbeelding 10. Deze hand geschreven letter, met de geëxtraheerde 16 functies, wordt voor speld als ' T ' met een kans van 0,9715.

![De Score module voor het interpreteren testen](./media/interpret-model-results/9_1.png)

![Testresultaat](./media/interpret-model-results/10.png)

Afbeelding 10. Webservice resultaat van een classificatie met meer klassen

## <a name="regression"></a>Regressie
Regressie problemen verschillen van classificatie problemen. In een classificatie probleem probeert u afzonderlijke klassen te voors pellen, bijvoorbeeld op welke klasse een Iris bloem is aangesloten. Maar zoals u kunt zien in het volgende voor beeld van een regressie probleem, probeert u een continue variabele te voors pellen, zoals de prijs van een auto.

**Voorbeeldexperiment**

Gebruik de voor spelling van prijzen voor auto Mobile als voor beeld voor regressie. U probeert de prijs van een auto te voors pellen op basis van de bijbehorende functies, zoals het merk, het brandstof type, het hoofdtekst type en de schijf wiel. Het experiment wordt weer gegeven in afbeelding 11.

![Auto Mobile-experiment voor prijs regressie](./media/interpret-model-results/11.png)

Afbeelding 11. Probleem met prijs regressie voor auto Mobile

Het visualiseren van de module [score model][score-model] , het resultaat ziet eruit als afbeelding 12.

![Score resultaten voor het oplossen van problemen met prijzen voor auto Mobile](./media/interpret-model-results/12.png)

Afbeelding 12. Score resultaat voor het probleem met de Voorspellings prijs voor auto Mobile

**Resultaten interpretatie**

Gescoorde labels is de resultaat kolom in dit Score resultaat. De getallen zijn de voorspelde prijs voor elke auto.

**Publicatie van de webservice**

U kunt het regressie-experiment publiceren naar een webservice en deze aanroepen voor het voors pellen van prijzen voor auto Mobile op dezelfde manier als in de classificatie met twee klassen.

![Score experiment voor het oplossen van problemen met prijs regressie voor auto Mobile](./media/interpret-model-results/13.png)

Afbeelding 13. Score-experiment van een probleem met prijs regressie voor auto Mobile

Als de webservice wordt uitgevoerd, ziet het geretourneerde resultaat eruit als afbeelding 14. De voorspelde prijs voor deze auto is $15.085,52.

![Analyse van Score module interpreteren](./media/interpret-model-results/13_1.png)

![Resultaten van Score module](./media/interpret-model-results/14.png)

Afbeelding 14. Webservice resultaat van een probleem met prijs regressie voor auto Mobile

## <a name="clustering"></a>Clustering
**Voorbeeldexperiment**

We gebruiken de Iris gegevensset opnieuw om een clustering-experiment te maken. Hier kunt u de klassen labels in de gegevensset filteren, zodat deze alleen functies heeft en kan worden gebruikt voor clustering. In dit geval van Iris gebruik geeft u het aantal clusters twee op tijdens het trainings proces. Dit betekent dat u de bloemen in twee klassen zou kunnen verdeelen. Het experiment wordt weer gegeven in afbeelding 15.

![Probleem met het afexperimenteren van Iris clusters](./media/interpret-model-results/15.png)

Afbeelding 15. Probleem met het afexperimenteren van Iris clusters

Clustering verschilt van classificatie omdat de set met trainings gegevens zelf geen labels met een grond waarheid heeft. Clustering groepeert de gegevensverzamelings instanties van de training in afzonderlijke clusters. Tijdens het trainings proces labelt het model de vermeldingen door de verschillen tussen hun functies te leren. Daarna kan het getrainde model worden gebruikt om toekomstige vermeldingen verder te classificeren. Er zijn twee delen van het resultaat die in een cluster probleem zijn geïnteresseerd. Het eerste deel is het labelen van de set met trainings gegevens en de tweede is een nieuwe gegevensset classificeren met het getrainde model.

Het eerste deel van het resultaat kan worden gevisualiseerd door te klikken op de linker uitvoer poort van [Train clustering model][train-clustering-model] en vervolgens te klikken op **visualiseren**. De visualisatie wordt weer gegeven in afbeelding 16.

![Resultaat van clustering](./media/interpret-model-results/16.png)

Afbeelding 16. Het cluster resultaat voor de set met trainings gegevens visualiseren

Het resultaat van het tweede deel, clustering van nieuwe vermeldingen met het getrainde cluster model, wordt weer gegeven in afbeelding 17.

![Resultaat van gevisualiseerde clustering](./media/interpret-model-results/17.png)

Afbeelding 17. Het cluster resultaat in een nieuwe gegevensset visualiseren

**Resultaten interpretatie**

Hoewel de resultaten van de twee onderdelen afkomstig zijn uit verschillende experimenten, zien ze er hetzelfde uit en worden ze op dezelfde manier geïnterpreteerd. De eerste vier kolommen zijn functies. De laatste kolom, toewijzingen, is het Voorspellings resultaat. De vermeldingen waaraan hetzelfde nummer is toegewezen, worden voor speld in hetzelfde cluster, dat wil zeggen dat ze vergelijk bare items op een of andere manier delen (dit experiment maakt gebruik van de standaard Euclidean afstands waarde). Omdat u het aantal clusters hebt opgegeven dat 2 moet zijn, worden de vermeldingen in toewijzingen aangeduid met de waarde 0 of 1.

**Publicatie van de webservice**

U kunt het clustering-experiment publiceren naar een webservice en deze aanroepen voor voor spellingen op dezelfde manier als in de classificatie met twee klassen.

![Score experiment voor het oplossen van problemen met Iris clusters](./media/interpret-model-results/18.png)

Afbeelding 18. Score experiment van een probleem met Iris clustering

Nadat u de-webservice hebt uitgevoerd, ziet het geretourneerde resultaat eruit als afbeelding 19. Deze bloem wordt voor speld als cluster 0.

![Test module interpreteren](./media/interpret-model-results/18_1.png)

![Resultaat van Score module](./media/interpret-model-results/19.png)

Afbeelding 19. Webservice resultaat van een twee klassen classificatie van Iris

## <a name="recommender-system"></a>Aanbevolen systeem
**Voorbeeldexperiment**

Voor aanbevolen systemen kunt u het advies probleem van het restaurant als voor beeld gebruiken: u kunt restaurants aanbevelen voor klanten op basis van hun beoordelings geschiedenis. De invoer gegevens bestaan uit drie delen:

* Restaurant beoordelingen van klanten
* Gegevens van klant functie
* Gegevens over restaurantkenmerken

Er zijn verschillende dingen die u kunt doen met de module [Train matchbox aanbevelen][train-matchbox-recommender] in azure machine learning Studio (klassiek):

* Beoordelingen voor een bepaalde gebruiker en een bepaald item voors pellen
* Het aanbevelen van items aan een bepaalde gebruiker
* Gebruikers zoeken die zijn gerelateerd aan een bepaalde gebruiker
* Items zoeken die betrekking hebben op een bepaald item

U kunt kiezen wat u wilt doen door een van de vier opties te selecteren in het menu **Aanbevolen voor spellingen** . Hier kunt u alle vier de scenario's door lopen.

![Matchbox-aanbeveling](./media/interpret-model-results/19_1.png)

Een typisch Azure Machine Learning Studio (klassiek) experiment voor een aanbevolen systeem ziet eruit als afbeelding 20. Voor informatie over het gebruik van deze aanbevolen systeem modules, raadpleegt u [Train matchbox aanbevelen][train-matchbox-recommender] en [Score matchbox raadt][score-matchbox-recommender].

![Systeem experiment voor aanbevolen systemen](./media/interpret-model-results/20.png)

Afbeelding 20. Systeem experiment voor aanbevolen systemen

**Resultaten interpretatie**

**Beoordelingen voor een bepaalde gebruiker en een bepaald item voors pellen**

Als u de optie **beoordelings voorspelling** selecteert onder **Aanbevolen Voorspellings type**, vraagt u het aanbevolen systeem om de classificatie voor een bepaalde gebruiker en een bepaald item te voors pellen. De visualisatie van de [Score matchbox-aanbevolen][score-matchbox-recommender] uitvoer ziet eruit als afbeelding 21.

![Score resultaat van het aanbevolen systeem-classificatie voor spelling](./media/interpret-model-results/21.png)

Afbeelding 21. Het Score resultaat van de voor spelling van het aanbevolen systeem visualiseren

De eerste twee kolommen zijn de door de invoer gegevens opgegeven combi Naties van gebruikers items. De derde kolom is de voorspelde classificatie van een gebruiker voor een bepaald item. In de eerste rij wordt de klant U1048 bijvoorbeeld voorspeld om de frequentie 135026 te beoordelen.

**Het aanbevelen van items aan een bepaalde gebruiker**

Door **item aanbeveling** te selecteren onder **Aanbevolen Voorspellings type**, vraagt u het aanbevolen systeem om items aan een bepaalde gebruiker aan te bevelen. De laatste para meter die in dit scenario moet worden gekozen, is de *Aanbevolen selectie van items*. De optie **van nominale items (voor de model evaluatie)** is voornamelijk bedoeld voor de model evaluatie tijdens het trainings proces. Voor deze Voorspellings fase kiezen we **uit alle items**. De visualisatie van de [Score matchbox-aanbevolen][score-matchbox-recommender] uitvoer ziet eruit als afbeelding 22.

![Resultaat van de Score van het aanbevolen systeem--item aanbeveling](./media/interpret-model-results/22.png)

Afbeelding 22. Het Score resultaat van het aanbevolen systeem visualiseren--artikel aanbeveling

De eerste van de zes kolommen duidt op de opgegeven gebruikers-Id's voor het aanbevelen van items, zoals opgegeven door de invoer gegevens. De andere vijf kolommen vertegenwoordigen de items die aan de gebruiker worden aanbevolen in aflopende volg orde van relevantie. In de eerste rij is bijvoorbeeld het meest aanbevolen restaurant voor klant U1048 134986, gevolgd door 135018, 134975, 135021 en 132862.

**Gebruikers zoeken die zijn gerelateerd aan een bepaalde gebruiker**

Als u **gerelateerde gebruikers** selecteert onder **Aanbevolen Voorspellings type**, vraagt u het aanbevolen systeem om verwante gebruikers te vinden voor een bepaalde gebruiker. Gerelateerde gebruikers zijn de gebruikers die vergelijk bare voor keuren hebben. De laatste para meter die in dit scenario moet worden gekozen, is *gerelateerde gebruikers selectie*. De optie **van gebruikers die geclassificeerde items (voor de model evaluatie),** is voornamelijk bedoeld voor de model evaluatie tijdens het trainings proces. Kies **uit alle gebruikers** voor deze Voorspellings fase. De visualisatie van de [Score matchbox-aanbevolen][score-matchbox-recommender] uitvoer ziet eruit als afbeelding 23.

![Score resultaat van het aanbevelen van systeem-gerelateerde gebruikers](./media/interpret-model-results/23.png)

Afbeelding 23. De resultaten van de Score van het aanbevelende systeem-gerelateerde gebruikers visualiseren

In de eerste van de zes kolommen worden de opgegeven gebruikers-Id's weer gegeven voor het vinden van gerelateerde gebruikers, zoals opgegeven door de invoer gegevens. De andere vijf kolommen slaan de voorspelde gerelateerde gebruikers van de gebruiker op in aflopende volg orde van relevantie. In de eerste rij is de meest relevante klant voor klant U1048 bijvoorbeeld U1051, gevolgd door U1066, U1044, U1017 en U1072.

**Items zoeken die betrekking hebben op een bepaald item**

Als u **Verwante items** selecteert onder **Voorspellings type**, vraagt u het aanbevolen systeem om verwante items naar een bepaald item te zoeken. Gerelateerde items zijn de items die waarschijnlijk dezelfde gebruiker hebben. De laatste para meter die in dit scenario moet worden gekozen, is *gerelateerde item selectie*. De optie **van nominale items (voor de model evaluatie)** is voornamelijk bedoeld voor de model evaluatie tijdens het trainings proces. We kiezen **uit alle items** voor deze Voorspellings fase. De visualisatie van de [Score matchbox-aanbevolen][score-matchbox-recommender] uitvoer ziet eruit als afbeelding 24.

![Score resultaat van het aanbevelen van systeem-gerelateerde items](./media/interpret-model-results/24.png)

Afbeelding 24. De resultaten van de Score van het aanbevelende systeem-gerelateerde items visualiseren

De eerste van de zes kolommen duidt op de opgegeven item-Id's die nodig zijn om verwante items te zoeken, zoals opgegeven door de invoer gegevens. De andere vijf kolommen bevatten de voorspelde gerelateerde items van het item in aflopende volg orde in termen van relevantie. In de eerste rij is bijvoorbeeld het meest relevante item voor item 135026 135074, gevolgd door 135035, 132875, 135055 en 134992.

**Publicatie van de webservice**

Het proces voor het publiceren van deze experimenten als webservices om voor spellingen te verkrijgen, is vergelijkbaar met elk van de vier scenario's. Hier volgen we het tweede scenario (het aanbevelen van items aan een bepaalde gebruiker) als voor beeld. U kunt dezelfde procedure volgen met de andere drie.

Als u het getrainde aanbevolen systeem opslaat als een getraind model en u de invoer gegevens naar een enkele kolom met gebruikers-id's filtert zoals aangevraagd, kunt u het experiment aansluiten zoals in afbeelding 25 en publiceren als een webservice.

![Score experiment van het advies probleem van het restaurant](./media/interpret-model-results/25.png)

Afbeelding 25. Score experiment van het advies probleem van het restaurant

Als de webservice wordt uitgevoerd, ziet het geretourneerde resultaat eruit als afbeelding 26. De vijf aanbevolen restaurants voor gebruikers U1048 zijn 134986, 135018, 134975, 135021 en 132862.

![Voor beeld van een aanbevolen systeem service](./media/interpret-model-results/25_1.png)

![Voorbeeld resultaten voor experiment](./media/interpret-model-results/26.png)

Afbeelding 26. Webservice resultaat van het advies probleem van het restaurant

<!-- Module References -->
[assign-to-clusters]: /azure/machine-learning/studio-module-reference/assign-data-to-clusters
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[score-matchbox-recommender]: /azure/machine-learning/studio-module-reference/score-matchbox-recommender
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[train-clustering-model]: /azure/machine-learning/studio-module-reference/train-clustering-model
[train-matchbox-recommender]: /azure/machine-learning/studio-module-reference/train-matchbox-recommender