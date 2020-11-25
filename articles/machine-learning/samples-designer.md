---
title: Voorbeeldpijplijnen en gegevenssets voor de ontwerpfunctie
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van voorbeelden in de Azure Machine Learning-ontwerpfunctie om snel aan de slag te gaan met uw machine learning-pijplijnen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 10/14/2020
ms.custom: designer
ms.openlocfilehash: a27e18d9b6f6307f1cc8ed48ca897dd4d1f0f1f3
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555495"
---
# <a name="example-pipelines--datasets-for-azure-machine-learning-designer"></a>Voorbeeldpijplijnen en -gegevenssets voor de Azure Machine Learning-ontwerpfunctie

Gebruik de ingebouwde voorbeelden in de Azure Machine Learning-ontwerpfunctie om snel aan de slag te gaan met het ontwerpen van uw eigen machine learning-pijplijnen. De [GitHub-opslagplaats](https://github.com/Azure/MachineLearningDesigner) van de Azure Machine Learning-ontwerpfunctie bevat gedetailleerde documentatie om u te helpen enkele veelvoorkomende machine learning-scenario’s te begrijpen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://aka.ms/AMLFree)
* Een Azure Machine Learning-werkruimte 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-sample-pipelines"></a>Voorbeeldpijplijnen gebruiken

De ontwerpfunctie slaat een kopie van de voorbeeldpijplijnen op in uw Studio-werkruimte. U kunt de pijplijnen bewerken om ze op uw behoeften af te stemmen en als uw eigen opslaan. Gebruik ze als uitgangspunt om snel aan de slag te gaan met uw projecten.

Zo gebruikt u een voorbeeld in de ontwerpfunctie:

1. Meld u aan op <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> en selecteer de werkruimte waarmee u wilt werken.

1. Selecteer **Designer**.

1. Selecteer een voorbeeldpijplijn in de sectie **Nieuwe pijplijn**.

    Selecteer **Meer voorbeelden weergeven** voor een volledige lijst met voorbeelden.

1. Als u een pijplijn wilt uitvoeren, moet u eerst een standaard rekendoel instellen waarop de pijplijn wordt uitgevoerd.

   1. In het deelvenster **Instellingen** rechts van het canvas selecteert u **Rekendoel selecteren**.

   1. In het dialoogvenster dat verschijnt, selecteert u een bestaand rekendoel of maakt u een nieuw rekendoel. Selecteer **Opslaan**.

   1. Selecteer **Verzenden** bovenaan het canvas om een pijplijnuitvoering te verzenden.

   Afhankelijk van de voorbeeldpijplijn en rekeninstellingen kan het een tijdje duren voordat een uitvoering is voltooid. De standaard rekeninstellingen hebben een minimale knooppuntgrootte van 0, wat betekent dat de ontwerpfunctie na inactiviteit resources moet toewijzen. Herhaalde pijplijnuitvoeringen kosten minder tijd, omdat de rekenresources al zijn toegewezen. Bovendien gebruikt de ontwerpfunctie in de cache opgeslagen resultaten voor elke module om de efficiëntie verder te verbeteren.


1. Nadat de pijplijn is uitgevoerd, kunt u de pijplijn controleren en de uitvoer voor elke module bekijken voor meer informatie. Gebruik de volgende stappen om module-uitvoer te bekijken:

   1. Klik met de rechtermuisknop op de module in het canvas waarvan u de uitvoer wilt zien.
   1. Selecteer **Visualiseren**.


   Gebruik de voorbeelden als uitgangspunt voor enkele van de meestvoorkomende machine learning-scenario’s.

## <a name="regression"></a>Regressie

Verken deze ingebouwde regressievoorbeelden.

| Voorbeeldtitel | Beschrijving | 
| --- | --- |
| [Regressie - Autoprijzen voorspellen (basis)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/regression-automobile-price-prediction-basic.md) | Voorspel autoprijzen met behulp van lineaire regressie. |
| [Regressie - Autoprijzen voorspellen (geavanceerd)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/regression-automobile-price-prediction-compare-algorithms.md) | Voorspel autoprijzen met behulp van regressors voor beslissingsforests en versterkte beslissingsstructuren. Vergelijk modellen om het beste algoritme te vinden.

## <a name="classification"></a>Classificatie

Verken deze ingebouwde classificatievoorbeelden. U kunt zonder documentatiekoppelingen meer informatie over de voorbeelden krijgen door de voorbeelden te openen en de moduleopmerkingen te bekijken.

| Voorbeeldtitel | Beschrijving | 
| --- | --- |
| [Binaire classificatie met functieselectie - Inkomen voorspellen](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/binary-classification-feature-selection-income-prediction.md) | Voorspel inkomen als hoog of laag met behulp van een versterkte beslissingsstructuur met twee klassen. Gebruik Pearson-correlatie om functies te selecteren.
| [Binaire classificatie met aangepast Python-script - Kredietrisico voorspellen](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/binary-classification-python-credit-prediction.md) | Classificeer kredietaanvragen als hoog of laag risico. Gebruik de module Python-script uitvoeren om uw gegevens een gewicht te geven.
| [Binaire classificatie - Klantrelaties voorspellen](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/binary-classification-customer-relationship-prediction.md) | Voorspel klantverloop met behulp van versterkte beslissingsstructuren met twee klassen. Gebruik SMOTE bevooroordeelde gegevens te samplen.
| [Tekstclassificatie - Gegevensset Wikipedia SP 500](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/text-classification-wiki.md) | Classificeer bedrijfstypen uit Wikipedia-artikelen met behulp van logistieke regressie met meerdere klassen. |
| Classificatie met meerdere klassen - Brieven herkennen | Maak een set binaire classificaties om geschreven brieven te classificeren. |

## <a name="computer-vision"></a>Computer Vision

Verken deze ingebouwde computervisievoorbeelden. U kunt zonder documentatiekoppelingen meer informatie over de voorbeelden krijgen door de voorbeelden te openen en de moduleopmerkingen te bekijken.

| Afbeeldingsclassificatie met DenseNet |Gebruik computervisie-modules voor het maken van een model voor afbeeldingsclassificatie op basis van PyTorch DenseNet.| 

## <a name="recommender"></a>Aanbevelingsfunctie

Verken deze ingebouwde aanbevelingsvoorbeelden. U kunt zonder documentatiekoppelingen meer informatie over de voorbeelden krijgen door de voorbeelden te openen en de moduleopmerkingen te bekijken.

| Voorbeeldtitel | Beschrijving | 
| --- | --- |
| Wide & Deep-aanbeveling - Voorspelling van restaurantbeoordeling | Bouw een aanbevelingsengine voor restaurants van restaurant/gebruikersfuncties en beoordelingen.|
| Aanbeveling - Tweets met filmbeoordelingen | Maak een filmaanbevelings-engine van filmtitels, gebruikersfuncties en beoordelingen.|

## <a name="utility"></a>Hulpprogramma

Meer informatie over de voorbeelden die machine learning-hulpprogramma’s en -functies demonstreren. U kunt zonder documentatiekoppelingen meer informatie over de voorbeelden krijgen door de voorbeelden te openen en de moduleopmerkingen te bekijken.

| Voorbeeldtitel | Beschrijving | 
| --- | --- |
| Binaire classificatie met behulp van Vowpal Wabbit Model - Volwassen inkomen voorspellen | Vowpal Wabbit is een machine learning-systeem dat de grens van machine learning pusht met technieken zoals online, hashing, allreduce, reducties, learning2search, actief en interactief leren. In dit voorbeeld ziet u hoe u het Vowpal Wabbit-model gebruikt om een binair classificatiemodel te maken. 
| [Aangepast R-script gebruiken - Vertraagde vluchten voorspellen](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/r-script-flight-delay-prediction.md) | Gebruik aangepast R-script om te voorspellen of een geplande vlucht meer dan 15 minuten vertraging gaat hebben.
| Kruisvalidatie voor binaire classificatie - Volwassen inkomen voorspellen | Gebruik kruisvalidatie om een binaire classificatie voor volwassen inkomen te ontwerpen.
| Belang van permutatiefunctie | Gebruik belang van permutatiefunctie om belangscores voor de testgegevensset te berekenen. 
| Parameters afstemmen voor binaire classificatie - Volwassen inkomen voorspellen | Gebruik Model Hyperparameters afstemmen voor het vinden van optimale hyperparameters om een binaire classificatie te ontwerpen. |

## <a name="datasets"></a>Gegevenssets

Wanneer u een nieuwe pijplijn maakt in de Azure Machine Learning-ontwerpfunctie, zijn er standaard een aantal voorbeeldgegevenssets inbegrepen. Deze voorbeeldgegevenssets worden gebruikt door de voorbeeldpijplijnen op de startpagina van de ontwerpfunctie. 

De voorbeeldgegevenssets zijn beschikbaar onder de categorie **Gegevenssets**-**Voorbeelden**. U vindt dit in het modulepalet links van het canvas in de ontwerpfunctie. U kunt elk van deze gegevenssets in uw eigen pijplijn gebruiken door deze naar het canvas te slepen.

| Naam van&nbsp;gegevensset&nbsp;&nbsp;&nbsp;&nbsp;| Beschrijving van gegevensset |
|-------------|:--------------------|
| Gegevensset Binaire classificatie voor volwassen Census-inkomen | Een subset van de 1994 Census-database, gebruikmakend van werkende volwassenen boven de 16 jaar met een aangepaste inkomstenindex van > 100.<br/>**Gebruik**: Classificeer mensen met behulp van demografie om te voorspellen of een persoon meer dan 50.000 per jaar verdient.<br/> **Gerelateerd onderzoek**: Kohavi, R., Becker, B., (1996). [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science|
|Autoprijsgegevens (onbewerkt)|Informatie over auto’s per merk en model, waaronder de prijs, kenmerken zoals het aantal cilinders en MPG, evenals een verzekeringsrisicoscore.<br/> De risicoscore is aanvankelijk gekoppeld aan de autoprijs. Vervolgens wordt het aangepast voor daadwerkelijk risico in een proces dat verzekeringswiskundigen symbolisering noemen. Een waarde van +3 geeft aan dat de auto risicovol is, en een waarde van -3 dat deze waarschijnlijk veilig is.<br/>**Gebruik**: Voorspel de risicoscore per kenmerk met behulp van regressie of multidimensionale classificatie.<br/>**Gerelateerd onderzoek**: Schlimmer, J.C. (1987). [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science. |
| Gedeelde labels CRM-verlangen |Labels uit de KDD Cup 2009-uitdaging Klantrelaties voorspellen ([orange_small_train_appetency.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)).|
|Gedeelde labels CRM-verloop|Labels uit de KDD Cup 2009-uitdaging Klantrelaties voorspellen ([orange_small_train_churn.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)).|
|Gedeelde CRM-gegevensset | Deze gegevens komen uit de KDD Cup 2009-uitdaging Klantrelaties voorspellen ([orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)). <br/>De gegevensset bevat 50.000 klanten van het Franse telecombedrijf Orange. Elke klant heeft 230 geanonimiseerde kenmerken, waarvan 190 numeriek en 40 categorisch zijn. De kenmerken zijn zeer verspreid. |
|Gedeelde labels CRM-upselling|Labels uit de KDD Cup 2009-uitdaging Klantrelaties voorspellen ([orange_large_train_upselling.labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)).|
|Gegevens over vluchtvertragingen|Gegevens over tijdige passagiersvluchten opgehaald uit de TranStats-gegevensverzameling van de U.S. Department of Transportation ([On-Time](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)).<br/>De gegevensset dekt de tijdsperiode april t/m oktober 2013. Voordat de gegevensset naar de ontwerpfunctie werd geüpload, werd deze als volgt verwerkt: <br/>-    De gegevensset werd gefilterd om alleen de 70 drukste luchthavens in de continentale VS te dekken <br/>- Geannuleerde vluchten werden gelabeld als meer dan 15 minuten vertraagd <br/>- Omgeleide vluchten werden uitgefilterd <br/>- De volgende kolommen werden geselecteerd: Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled|
|Gegevensset UCI German Credit Card|De gegevensset UCI Statlog (German Credit Card) ([Statlog+German+Credit+Data](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))), die het german.data-bestand gebruikt.<br/>De gegevensset classificeert personen, beschreven door een set kenmerken, als laag of hoog kredietrisico. Elk voorbeeld vertegenwoordigt een persoon. Er zijn 20 kenmerken, zowel numeriek als categorisch, en een binair label (de kredietrisicowaarde). Vermeldingen van een hoog kredietrisico hebben label = 2, vermeldingen van een laag kredietrisico hebben label = 1. De kosten van het verkeerd classificeren van een voorbeeld met laag risico als hoog is 1, en de kosten van het verkeerd classificeren van een voorbeeld met hoog risico als laag is 5.|
|IMDB-filmtitels|De gegevensset bevat informatie over films die zijn beoordeeld in Twitter-tweets: IMDB-film-ID, filmnaam, genre en productiejaar. De gegevensset bevat 17.000 films. De gegevensset werd geïntroduceerd in het rapport "S. Dooms, T. De Pessemier en L. Martens. MovieTweetings: a Movie Rating Dataset Collected From Twitter. Workshop on Crowdsourcing and Human Computation for Recommender Systems, CrowdRec at RecSys 2013."|
|Filmbeoordelingen|Deze gegevensset is een uitgebreide versie van de gegevensset MovieTweetings. De gegevensset bevat 170.000 beoordelingen van films, geëxtraheerd uit goed gestructureerde tweets op Twitter. Elk exemplaar vertegenwoordigt een tweet en is een tuple: gebruikers-ID, IMDB-film-ID, beoordeling, tijdstempel, aantal vind-ik-leuks van deze tweet en aantal retweets van deze tweet. De gegevensset werd beschikbaar gesteld door A. Said, S. Dooms, B. Loni en D. Tikk voor Recommender Systems Challenge 2014.|
|Gegevensset Weer|Weerobservaties op het land per uur van NOAA ([samengevoegde gegevens van 201304 tot 201310](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>De weersgegevens komen van observaties uit weerstations op luchthavens en dekken de tijdsperiode april t/m oktober 2013. Voordat de gegevensset naar de ontwerpfunctie werd geüpload, werd deze als volgt verwerkt:    <br/> -    Weerstation-ID’s werden toegewezen aan bijbehorende luchthaven-ID’s    <br/> -    Weerstations die niet waren gekoppeld aan de 70 drukste luchthavens, werden uitgefilterd    <br/> -    De kolom Date werd gesplitst in aparte kolommen Year, Month en Day    <br/> - De volgende kolommen werden geselecteerd: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter|
|Gegevensset Wikipedia SP 500|Gegevens worden afgeleid van Wikipedia (https://www.wikipedia.org/) op basis van artikelen van elk S&P 500-bedrijf, opgeslagen als XML-gegevens.    <br/>Voordat de gegevensset naar de ontwerpfunctie werd geüpload, werd deze als volgt verwerkt:    <br/> - Voor elk specifieke bedrijf werd tekstinhoud geëxtraheerd    <br/> -    Wiki-opmaak werd verwijderd    <br/> -    Niet-alfanumerieke tekens werden verwijderd    <br/> -    Alle tekst werd omgezet in kleine letters    <br/> -    Bekende bedrijfscategorieën werden toegevoegd    <br/>Voor sommige bedrijven kon er geen artikel worden gevonden, waardoor het aantal records minder dan 500 is.|
|Gegevens over restaurantkenmerken| Een set metagegevens over restaurants en hun kenmerken, zoals type gerechten, beoogd publiek en locatie. <br/>**Gebruik**: Gebruik deze gegevensset, in combinatie met de andere twee gegevenssets van het restaurant, om een aanbevolen systeem te trainen en te testen.<br/> **Gerelateerd onderzoek**: Bache, K. and Lichman, M. (2013). [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science.|
|Restaurantbeoordelingen| Bevat beoordelingen van gebruikers over restaurants op een schaal van 0 tot 2.<br/>**Gebruik**: Gebruik deze gegevensset, in combinatie met de andere twee gegevenssets van het restaurant, om een aanbevolen systeem te trainen en te testen. <br/>**Gerelateerd onderzoek**: Bache, K. and Lichman, M. (2013). [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science.|
|Gegevens van bezoekers van restaurants| Een set metagegevens over klanten, inclusief demografische informatie en voorkeuren. <br/>**Gebruik**: Gebruik deze gegevensset, in combinatie met de andere twee gegevenssets van het restaurant, om een aanbevolen systeem te trainen en te testen. <br/> **Gerelateerd onderzoek**: Bache, K. and Lichman, M. (2013). [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml) Irvine, CA: University of California, School of Information and Computer Science.|

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Leer de basisprincipes van predictive analytics en machine learning met [Zelfstudie: Autoprijzen voorspellen met de ontwerpfunctie](tutorial-designer-automobile-price-train-score.md)

