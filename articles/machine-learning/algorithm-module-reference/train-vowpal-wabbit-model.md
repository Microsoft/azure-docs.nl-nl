---
title: Vowpal Wabbit-model trainen
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Train Vowpal Wabbit model om een machine learning model te maken met behulp van een instantie van Vowpal Wabbit.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 70d0fc456b3697e3c74a5ec45cc936a02b77e591
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657651"
---
# <a name="train-vowpal-wabbit-model"></a>Vowpal Wabbit-model trainen
In dit artikel wordt beschreven hoe u de **Wabbit-model module Train Vowpal** in azure machine learning Designer kunt gebruiken om een machine learning model te maken met behulp van Vowpal Wabbit.  

Als u Vowpal Wabbit voor machine learning wilt gebruiken, formatteert u uw invoer volgens Vowpal Wabbit-vereisten en bereidt u de gegevens voor in de vereiste indeling. Gebruik deze module om Vowpal Wabbit opdracht regel argumenten op te geven. 

Wanneer de pijp lijn wordt uitgevoerd, wordt een exemplaar van Vowpal Wabbit geladen in de runtime-uitvoering, samen met de opgegeven gegevens. Wanneer de training is voltooid, wordt het model teruggezet naar de werk ruimte. U kunt het model direct gebruiken voor het beoordelen van gegevens. 

Als u een bestaand model op nieuwe gegevens stapsgewijs wilt trainen, verbindt u een opgeslagen model met de **vooraf getrainde Vowpal Wabbit model** invoer poort van **Train Vowpal Wabbit model** en voegt u de nieuwe gegevens toe aan de andere invoer poort.  

## <a name="what-is-vowpal-wabbit"></a>Wat is Vowpal Wabbit?  

Vowpal Wabbit (VW) is een snelle, parallelle machine learning Framework dat is ontwikkeld voor gedistribueerd computing door Yahoo! Nader. Later werd het naar Windows gepoortd en aangepast door John Langford (micro soft Research) voor weten schappelijke computers in parallelle architecturen.  

Functies van Vowpal Wabbit die belang rijk zijn voor machine learning, omvatten doorlopend leren (online leren), driedimensionale reductie en interactief leren. Vowpal Wabbit is ook een oplossing voor problemen wanneer u de model gegevens niet in het geheugen kunt passen.  

De primaire gebruikers van Vowpal Wabbit zijn gegevens wetenschappers die eerder het Framework hebben gebruikt voor machine learning taken zoals classificatie, regressie, topic Modeling of matrix factorization. De Azure wrapper voor Vowpal Wabbit heeft zeer vergelijk bare prestatie kenmerken als de on-premises versie, zodat u de krachtige functies en systeem eigen prestaties van Vowpal Wabbit kunt gebruiken en het getrainde model eenvoudig als een operationele service publiceert.  

De [functie hashing](feature-hashing.md) module bevat ook functionaliteit die is geleverd door Vowpal Wabbit, waarmee u tekst gegevens sets kunt transformeren naar binaire functies met behulp van een hash-algoritme.  

## <a name="how-to-configure-vowpal-wabbit-model"></a>Het Vowpal Wabbit-model configureren  

In deze sectie wordt beschreven hoe u een nieuw model traint en hoe u nieuwe gegevens toevoegt aan een bestaand model.

In tegens telling tot andere modules in Designer geeft deze module beide de module parameters en wordt het model treinen. Als u een bestaand model hebt, kunt u dit als een optionele invoer toevoegen, zodat het model incrementeel kan worden getraind.

+ [Invoer gegevens voorbereiden in een van de vereiste indelingen](#prepare-the-input-data)
+ [Een nieuw model trainen](#create-and-train-a-vowpal-wabbit-model)
+ [Stapsgewijs een bestaand model trainen](#retrain-an-existing-vowpal-wabbit-model)

### <a name="prepare-the-input-data"></a>De invoergegevens voorbereiden

Voor het trainen van een model met behulp van deze module moet de invoer gegevensset bestaan uit één tekst kolom in een van de twee ondersteunde indelingen: **SVMLight** of **VW**. Dit betekent niet dat Vowpal Wabbit alleen tekst gegevens analyseert, alleen dat de functies en waarden moeten worden voor bereid met de vereiste tekst indeling.  

De gegevens kunnen worden gelezen uit twee soorten gegevens sets, bestands-gegevensset of gegevensset in tabel vorm. Beide gegevens sets moeten in de indeling SVMLight of VW zijn. De Vowpal Wabbit-gegevens indeling heeft het voor deel dat er geen kolom indeling is vereist, waardoor ruimte wordt bespaard bij het omgaan met sparse gegevens. Zie de [pagina met Vowpal Wabbit-wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format)voor meer informatie over deze indeling.  

### <a name="create-and-train-a-vowpal-wabbit-model"></a>Een Vowpal Wabbit-model maken en trainen

1. Voeg de module **Train Vowpal Wabbit model** toe aan uw experiment. 
  
2. Voeg de trainings gegevensset toe en verbind deze met **trainings gegevens**. Als training dataset een directory is die het bestand met trainings gegevens bevat, geeft u de naam van het bestand met trainings gegevens op met de **naam van het bestand met trainings gegevens**. Als de trainings gegevensset één bestand is, laat u **de naam van het bestand met trainings gegevens** leeg.

3. In het tekstvak **VW argumenten** typt u de opdracht regel argumenten voor het uitvoer bare bestand Vowpal Wabbit.

     U kunt bijvoorbeeld toevoegen *`–l`* om het leer tempo op te geven, of *`-b`* om het aantal hash-bits aan te geven.  

     Zie de sectie [para meters voor Vowpal Wabbit](#supported-and-unsupported-parameters) voor meer informatie.  

4. **Naam van het bestand met trainings gegevens**: Typ de naam van het bestand dat de invoer gegevens bevat. Dit argument wordt alleen gebruikt als de gegevensset van de training een directory is.

5. **Bestands type opgeven**: Geef aan welke indeling uw trainings gegevens gebruikt. Vowpal Wabbit ondersteunt de volgende twee invoer bestands indelingen:  

    - **VW** vertegenwoordigt de interne indeling die wordt gebruikt door Vowpal Wabbit. Zie de [pagina met Vowpal Wabbit-wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) voor meer informatie. 
    - **SVMLight** is een indeling die wordt gebruikt door andere machine learning-hulpprogram ma's. 

6. **Uitvoer leesbaar model bestand**: Selecteer de optie als u wilt dat de module het Lees bare model opslaat in de uitvoerings records. Dit argument komt overeen met de `--readable_model` para meter in de VW-opdracht regel.  

7. **Omgekeerd hash-bestand van uitvoer**: Selecteer de optie als u wilt dat de module de omgekeerde hash-functie opslaat in één bestand in de records uitvoeren. Dit argument komt overeen met de `--invert_hash` para meter in de VW-opdracht regel.  

8. Verzend de pijp lijn.

### <a name="retrain-an-existing-vowpal-wabbit-model"></a>Een bestaand Vowpal Wabbit-model opnieuw trainen

Vowpal Wabbit ondersteunt incrementele training door nieuwe gegevens toe te voegen aan een bestaand model. Er zijn twee manieren om een bestaand model te verkrijgen voor retraining:

+ Gebruik de uitvoer van een andere **Train Vowpal Wabbit model** module in dezelfde pijp lijn.  
  
+ Zoek naar een opgeslagen model in de categorie **gegevens sets** van het navigatie deel venster van de ontwerp functie en sleep deze naar de pijp lijn.  

1. Voeg de module **Train Vowpal Wabbit model** toe aan de pijp lijn.  
2. Verbind het eerder getrainde model met de **vooraf getrainde Vowpal Wabbit model** -invoer poort van de module.
3. Verbind de nieuwe trainings gegevens met de gegevens invoer poort van de module voor **training** .
4. Geef in het deel venster para meters van het **Wabbit-Model Train Vowpal** de indeling van de nieuwe trainings gegevens op, en ook de naam van het bestand met trainings gegevens als de invoer-gegevensset een directory is.
5. Selecteer het **model bestand uitvoer leesbaar** en **uitvoer teruggedraaid hash-bestand** als de bijbehorende bestanden moeten worden opgeslagen in de uitvoerings records.

6. Verzend de pijp lijn.  
7. Selecteer de module en selecteer **gegevensset registreren** onder **uitvoer, tabblad logboeken** in het rechterdeel venster, om het bijgewerkte model in uw Azure machine learning-werk ruimte te bewaren.  Als u geen nieuwe naam opgeeft, wordt het bestaande opgeslagen model overschreven door het bijgewerkte model.

## <a name="results"></a>Resultaten

+ Als u scores van het model wilt genereren, gebruikt u [Score Vowpal Wabbit model](score-vowpal-wabbit-model.md).

> [!NOTE]
> Als u het getrainde model in de ontwerp functie moet implementeren, moet u ervoor zorgen dat de [Score Vowpal Wabbit model](score-vowpal-wabbit-model.md) in plaats van het **score model** is verbonden met de invoer van de module voor het maken van webservice-uitvoer in de pipeline voor het afnemen van [webservices](web-service-input-output.md) .

## <a name="technical-notes"></a>Technische opmerkingen

Deze sectie bevat implementatie details, tips en antwoorden op veelgestelde vragen.

### <a name="advantages-of-vowpal-wabbit"></a>Voor delen van Vowpal Wabbit

Vowpal Wabbit biedt extreem snel leren over niet-lineaire functies, zoals n-gram.  

Vowpal Wabbit maakt gebruik van *online leer* technieken zoals stochastische Gradient DAAL (SGD) voor het aanpassen van een model per record. Daarom wordt het zeer snel herhaald om onbewerkte gegevens te herhalen en kunt u sneller dan de meeste andere modellen een goede voor spelling ontwikkelen. Deze aanpak voor komt ook dat alle trainings gegevens in het geheugen moeten worden gelezen.  

Vowpal Wabbit converteert alle gegevens naar hashes, niet alleen tekst gegevens, maar ook andere Categorische-variabelen. Door gebruik te maken van hashes is het opzoeken van regressie gewichten efficiënter, wat van essentieel belang is voor effectieve, stochastische verloop Daal.  

###  <a name="supported-and-unsupported-parameters"></a>Ondersteunde en niet-ondersteunde para meters 

In deze sectie wordt de ondersteuning voor Vowpal Wabbit-opdracht regel parameters in Azure Machine Learning Designer beschreven. 

Over het algemeen worden alleen een beperkt aantal argumenten ondersteund. Gebruik de [pagina Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)voor een volledige lijst met argumenten.    

De volgende para meters worden niet ondersteund:

-   De invoer-en uitvoer opties die zijn opgegeven in [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     Deze eigenschappen zijn al automatisch geconfigureerd door de module.  
  
-   Daarnaast is alle opties die meerdere uitvoer genereren of meerdere invoer accepteert niet toegestaan. Dit zijn onder andere *`--cbt`* , *`--lda`* , en *`--wap`* .  
  
-   Alleen bewaakte leer algoritmen worden ondersteund. Deze opties worden daarom niet ondersteund: *`–active`* , `--rank` , *`--search`* enzovoort. 

### <a name="restrictions"></a>Beperkingen

Omdat het doel van de service is om ervaren gebruikers van Vowpal Wabbit te ondersteunen, moeten invoer gegevens worden voor bereid op het moment dat ze de Vowpal Wabbit native Text-indeling gebruiken, in plaats van de indeling van de gegevensset die wordt gebruikt door andere modules.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
