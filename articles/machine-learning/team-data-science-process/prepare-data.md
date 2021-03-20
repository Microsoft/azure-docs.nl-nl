---
title: 'Prep-gegevens voor ML Studio (klassiek): team data Science process'
description: Gegevens vooraf verwerken en opschonen om deze voor te bereiden om effectief te worden gebruikt voor machine learning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: caedcf313ab809e9607907545f26ca1b62bbeca7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012404"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Taken om gegevens voor te bereiden voor verbeterde Machine Learning
Gegevens voorafgaand aan verwerking en reiniging zijn belang rijke taken die moeten worden uitgevoerd voordat een gegevensset kan worden gebruikt voor model training. Onbewerkte gegevens zijn vaak ruis en onbetrouwbaar, en mogelijk ontbreken waarden. Het gebruik van dergelijke gegevens voor model lering kan misleidende resultaten opleveren. Deze taken maken deel uit van het team data Science process (TDSP) en volgen doorgaans een eerste onderzoek van een gegevensset die wordt gebruikt voor het detecteren en plannen van de vereiste vooraf-verwerking. Zie de stappen die worden beschreven in het [team data Science process](overview.md)voor meer gedetailleerde instructies voor het TDSP-proces.

Taken voorafgaand aan verwerking en opschoning, zoals de taak voor het verkennen van gegevens, kunnen worden uitgevoerd in een breed scala aan omgevingen, zoals SQL of Hive of Azure Machine Learning Studio (klassiek), en met verschillende hulpprogram ma's en talen, zoals R of python, afhankelijk van waar uw gegevens worden opgeslagen en hoe deze worden ingedeeld. Omdat TDSP is herhaald, kunnen deze taken worden uitgevoerd in verschillende stappen in de werk stroom van het proces.

In dit artikel worden verschillende concepten en taken voor gegevens verwerking geïntroduceerd die kunnen worden uitgevoerd vóór of na het opnemen van gegevens in Azure Machine Learning Studio (klassiek).

Voor een voor beeld van het verkennen van gegevens en vooraf verwerkingen binnen Azure Machine Learning Studio (klassiek), raadpleegt u de video [preprocessing data](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) .

## <a name="why-pre-process-and-clean-data"></a>Waarom gegevens vooraf verwerken en opschonen?
Werkelijke gegevens worden verzameld uit verschillende bronnen en processen en kunnen onregelmatigheden bevatten of beschadigde gegevens de kwaliteit van de gegevensset schaden. De meest voorkomende problemen met de kwaliteit van gegevens zijn:

* **Onvolledig**: gegevens ontbreken kenmerken of bevatten ontbrekende waarden.
* **Ruis**: gegevens bevatten onjuiste records of uitschieters.
* **Inconsistent**: gegevens bevatten conflicterende records of discrepanties.

Kwaliteits gegevens zijn een vereiste voor de kwaliteit van voorspellende modellen. Om te voor komen dat u ' garbageeert in, garbage ' en de kwaliteit van de gegevens verbetert en daarom de prestaties van modellen modelt, is het van cruciaal belang dat u een Data Health-scherm uitvoert om gegevens problemen vroegtijdig op te lossen en te beslissen over de overeenkomende gegevens verwerking en-reiniging.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Wat zijn de typische gegevens status schermen die worden gebruikt?
We kunnen de algemene kwaliteit van de gegevens controleren door het volgende te controleren:

* Het aantal **records**.
* Het aantal **kenmerken** (of **onderdelen**).
* De **gegevens typen** van het kenmerk (nominaal, rang telwoord of doorlopend).
* Het aantal **ontbrekende waarden**.
* **Juist opgemaakte** gegevens.
  * Als de gegevens zich in TSV of CSV bevindt, controleert u of de kolom scheidings tekens en lijn scheidings tekens altijd goed gescheiden zijn van kolommen en lijnen.
  * Als de gegevens zich in HTML-of XML-indeling bevindt, controleert u of de gegevens goed zijn gevormd op basis van hun respectievelijke standaarden.
  * Parseren kan ook nodig zijn om gestructureerde informatie uit semi-gestructureerde of ongestructureerde gegevens te extra heren.
* **Inconsistente gegevens records**. Controleer of het bereik van de waarden is toegestaan. Als de gegevens bijvoorbeeld student GPA bevatten (gemiddelde van cijfers), controleert u of de GPA zich in het aangewezen bereik bevindt, bijvoorbeeld 0 ~ 4.

Wanneer u problemen met gegevens hebt gevonden, zijn de **verwerkings stappen** nood zakelijk, wat vaak nodig is voor het opschonen van ontbrekende waarden, gegevens normalisatie, onderscheidings, tekst verwerking voor het verwijderen en/of vervangen van Inge sloten tekens die van invloed kunnen zijn op de uitlijning van gegevens, gemengde gegevens typen in algemene velden en andere.

**Azure machine learning verbruikt goed gevormde gegevens in tabel vorm**.  Als de gegevens zich al in tabel vorm bevinden, kunnen gegevens vooraf worden verwerkt met Azure Machine Learning Studio (klassiek) in de Machine Learning.  Als de gegevens zich niet in een tabel vorm bevinden, kunt u deze het best in XML zetten, omdat parseren mogelijk vereist is om de gegevens te converteren naar tabellaire vorm.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Wat zijn de belangrijkste taken in het vooraf verwerken van gegevens?
* **Gegevens reiniging**: Vul ontbrekende waarden in, detecteer en Verwijder ruis gegevens en uitschieters.
* **Gegevens transformatie**: gegevens normaliseren om dimensies en ruis te verminderen.
* **Gegevens reductie**: voorbeeld gegevens records of kenmerken voor het gemakkelijker verwerken van gegevens.
* **Data onderscheidings**: u kunt doorlopende kenmerken omzetten in categorische-kenmerken, zodat u ze eenvoudig kunt gebruiken met bepaalde machine learning methoden.
* **Tekst opschonen**: Verwijder Inge sloten tekens die mogelijk een onjuiste uitlijning van gegevens veroorzaken, bijvoorbeeld Inge sloten tabbladen in een door tabs gescheiden gegevens bestand, Inge sloten nieuwe regels die records kunnen verstoren, bijvoorbeeld.

In de volgende secties worden enkele van deze gegevens verwerkings stappen besproken.

## <a name="how-to-deal-with-missing-values"></a>Hoe kan ik omgaan met ontbrekende waarden?
Om te kunnen omgaan met ontbrekende waarden, is het raadzaam eerst de reden voor de ontbrekende waarden te identificeren om het probleem beter te kunnen afhandelen. Typische ontbrekende methoden voor het afhandelen van waarden zijn:

* **Verwijderen**: records met ontbrekende waarden verwijderen
* **Dummy vervanging**: ontbrekende waarden vervangen door een dummy waarde: bijvoorbeeld *onbekend* voor categorische of 0 voor numerieke waarden.
* **Gemiddelde vervanging**: als de ontbrekende gegevens numeriek zijn, vervangt u de ontbrekende waarden door het gemiddelde.
* **Regel matig vervangen**: als de ontbrekende gegevens categorische zijn, vervangt u de ontbrekende waarden door het meest frequente item
* **Regressie substitutie**: gebruik een regressie methode om ontbrekende waarden te vervangen door teruggedraaide-waarden.  

## <a name="how-to-normalize-data"></a>Hoe kan ik gegevens normaliseren?
Met gegevens normalisatie worden numerieke waarden opnieuw geschaald naar een opgegeven bereik. Populaire methoden voor gegevens normalisatie zijn:

* **Min-max. normalisatie**: lineaire Transformeer de gegevens naar een bereik, zeg tussen 0 en 1, waarbij de minimum waarde wordt geschaald naar 0 en maximum waarde 1.
* **Z-score-normalisatie**: schaal gegevens op basis van gemiddelde en standaard afwijking: deel het verschil tussen de gegevens en het gemiddelde van de standaard deviatie.
* **Decimalen schalen**: de gegevens schalen door het decimaal teken van de kenmerk waarde te verplaatsen.  

## <a name="how-to-discretize-data"></a>Hoe kan ik gegevens stelt?
Gegevens kunnen worden onderscheiden door doorlopende waarden te converteren naar nominale kenmerken of intervallen. Hier volgen enkele manieren waarop u dit kunt doen:

* **Binning met gelijke breedte**: deel het bereik van alle mogelijke waarden van een kenmerk in N groepen van dezelfde grootte en wijs de waarden toe die in een bin vallen met het nummer van de opslag locatie.
* **Binning met gelijke hoogte**: deel het bereik van alle mogelijke waarden van een kenmerk in N groepen, elk met hetzelfde aantal instanties, en wijs vervolgens de waarden toe die in een bin vallen met het nummer van de opslag locatie.  

## <a name="how-to-reduce-data"></a>Hoe vermindert u de gegevens?
Er zijn verschillende methoden om de gegevens grootte te reduceren voor eenvoudiger gegevens verwerking. Afhankelijk van de grootte van de gegevens en het domein, kunnen de volgende methoden worden toegepast:

* **Record sampling**: voor beelden van de gegevens records en de representatieve subset van de gegevens kiezen.
* **Kenmerk bemonstering**: Selecteer alleen een subset van de belangrijkste kenmerken van de gegevens.  
* **Aggregatie**: deel de gegevens in groepen en sla de getallen voor elke groep op. De dagelijkse omzet cijfers van een restaurant keten gedurende de afgelopen 20 jaar kunnen bijvoorbeeld worden geaggregeerd naar maandelijkse omzet om de omvang van de gegevens te reduceren.  

## <a name="how-to-clean-text-data"></a>Hoe kunt u tekst gegevens opschonen?
**Tekst velden in tabellaire gegevens** bevatten mogelijk tekens die van invloed zijn op kolommen uitlijning en/of record grenzen. Inge sloten tabbladen in een bestand met door tabs gescheiden waarden veroorzaken bijvoorbeeld kolom verkeerd uitlijnen en Inge sloten nieuwe regel tekens record regels. Ongeldige verwerking van tekst codering tijdens het schrijven of lezen van tekst leidt tot verlies van gegevens, onbedoelde introductie van onleesbare tekens (zoals null-waarden) en kan ook van invloed zijn op het parseren van tekst. U kunt er ook voor zorgen dat het parseren en bewerken is vereist voor het opschonen van tekst velden voor een juiste uitlijning en/of om gestructureerde gegevens uit ongestructureerde of semi-gestructureerde tekst gegevens op te halen.

**Gegevens onderzoek** biedt een vroegtijdige weer gave van de gegevens. Tijdens deze stap kan een aantal gegevens problemen worden gedetecteerd en de bijbehorende methoden kunnen worden toegepast om deze problemen op te lossen.  Het is belang rijk om vragen te stellen, zoals wat de oorzaak is van het probleem en hoe het probleem kan zijn geïntroduceerd. Dit proces helpt u ook bij het bepalen van de gegevens verwerkings stappen die moeten worden uitgevoerd om ze op te lossen. Het identificeren van de eind use cases en personen kunnen ook worden gebruikt voor het bepalen van de gegevens verwerkings inspanning.

## <a name="references"></a>Referenties
> *Data Mining: concepten en technieken*, derde editie, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber en Jian Pei
> 
> 

