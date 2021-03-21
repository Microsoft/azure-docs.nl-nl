---
title: Zakelijke inzichten in team data Science process
description: De doel stellingen, taken en producten voor de werk fase van uw data-Science-projecten in het team data Science process.
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
ms.openlocfilehash: a7aaed519f8f97a9be77a263568aeed5257c16d6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76710330"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>De fase van de zakelijke activiteiten van de team data Science process Lifecycle

In dit artikel vindt u een overzicht van de doel stellingen, taken en producten die zijn gekoppeld aan de fase van de zakelijke activiteiten van het team data Science process (TDSP). Dit proces biedt een aanbevolen levens cyclus die u kunt gebruiken om uw projecten met een gegevens wetenschap te structureren. De levenscyclus bevat de belangrijkste fasen die projecten meestal doorlopen, vaak iteratief:

   1. **Zakelijke inzichten**
   2. **Gegevens verkrijgen en meer informatie**
   3. **Modelleren**
   4. **Implementatie**
   5. **Aanvaarding van de klant**

Hier volgt een visuele representatie van de levens cyclus van TDSP: 

![TDSP levenscyclus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Doelstellingen
* Geef de sleutel variabelen op die dienen als model doelen en waarvan de gerelateerde metrieken worden gebruikt om het succes van het project te bepalen.
* Identificeer de relevante gegevens bronnen waartoe het bedrijf toegang heeft of moet verkrijgen.

## <a name="how-to-do-it"></a>Hoe u dit doet
Er zijn twee hoofd taken die in deze fase worden behandeld: 

   * **Doel stellingen definiëren**: werk samen met uw klant en andere belanghebbenden om de bedrijfs problemen te begrijpen en te identificeren. Formuleer vragen die de zakelijke doel stellingen definiëren die de data Science-technieken kunnen bereiken.
   * **Gegevens bronnen identificeren**: Zoek de relevante gegevens die u helpen bij het beantwoorden van de vragen die de doel stellingen van het project bepalen.

### <a name="define-objectives"></a>Doel stellingen definiëren
1. Een centraal doel van deze stap is het identificeren van de belangrijkste bedrijfs variabelen die de analyse moet voors pellen. We verwijzen naar deze variabelen als *model doelen* en we gebruiken de metrische gegevens die eraan zijn gekoppeld om het succes van het project te bepalen. Twee voor beelden van dergelijke doelen zijn verkoop prognoses of de kans dat een bestelling frauduleus is.

2. Definieer de doel stellingen van het project door ' scherpe ' vragen te stellen en te verfijnen die relevant, specifiek en ondubbelzinnig zijn. Data Science is een proces waarbij namen en getallen worden gebruikt om deze vragen te beantwoorden. Normaal gesp roken gebruikt u data Science of machine learning om vijf soorten vragen te beantwoorden:
 
   * Hoeveel of hoeveel? regressie
   * Welke categorie? ontbreekt
   * Welke groep? Clustering
   * Is dit klinken? (anomalie detectie)
   * Welke optie moet er worden genomen? advies

   Bepaal welke van deze vragen u vraagt en hoe de antwoorden op uw bedrijfs doelen worden gerealiseerd.

3. Definieer het project team door de rollen en verantwoordelijkheden van de leden op te geven. Ontwikkel een mijlpaal plan op hoog niveau dat u herhaalt wanneer u meer informatie ontdekt. 

4. Definieer de metrische gegevens voor het slagen. Het is bijvoorbeeld mogelijk dat u een klant verloop wilt belichten. U hebt een nauw keurigheid van ' x ' procent aan het einde van dit project van drie maanden nodig. Met deze gegevens kunt u klant aanbiedingen aanbieden om het verloop te verminderen. De metrische gegevens moeten **Slim** zijn: 

   * **S** pecifieke 
   * **M** easurable
   * **Een** chievable 
   * **R** elevant 
   * **T** IME-gebonden 

### <a name="identify-data-sources"></a>Gegevensbronnen identificeren
Identificeer gegevens bronnen die bekende voor beelden van antwoorden op uw scherpe vragen bevatten. Zoek naar de volgende gegevens:

* Gegevens die relevant zijn voor de vraag. Hebt u maat regelen voor het doel en de functies die zijn gerelateerd aan het doel?
* Gegevens die een nauw keurige meting hebben van uw model doel en de functies van belang.

U kunt bijvoorbeeld zien dat de bestaande systemen extra soorten gegevens moeten verzamelen en registreren om het probleem op te lossen en de project doelen te bereiken. In deze situatie wilt u mogelijk zoeken naar externe gegevens bronnen of uw systemen bijwerken om nieuwe gegevens te verzamelen.

## <a name="artifacts"></a>Artifacts
Dit zijn de producten in deze fase:

   * [Document voor hand vesting](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): er wordt een standaard sjabloon in de project structuur definitie TDSP gegeven. Het charter document is een woon document. U werkt de sjabloon tijdens het project bij tijdens het maken van nieuwe detecties en het wijzigen van de bedrijfs vereisten. De sleutel is om dit document te herhalen en meer details toe te voegen tijdens de voortgang van het detectie proces. Zorg ervoor dat de klant en de andere betrokkenen betrokken zijn bij het maken van de wijzigingen en de redenen voor de wijzigingen duidelijk kunnen door geven.  
   * [Gegevens bronnen](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): de sectie **onbewerkte gegevens bronnen** in het rapport met **gegevens definities** die in de map TDSP project **Data report** worden gevonden, bevat de gegevens bronnen. In deze sectie worden de oorspronkelijke en doel locatie voor de onbewerkte gegevens opgegeven. In latere fasen vult u aanvullende gegevens, zoals de scripts, in om de gegevens naar uw analytische omgeving te verplaatsen.  
   * [Gegevens woordenlijsten](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): in dit document vindt u beschrijvingen van de gegevens die door de client worden verstrekt. Deze beschrijvingen bevatten informatie over het schema (de gegevens typen en informatie over de validatie regels, indien van toepassing) en de relatie diagrammen, indien beschikbaar.

## <a name="next-steps"></a>Volgende stappen

Hier vindt u koppelingen naar elke stap in de levens cyclus van de TDSP:

   1. [Inzicht in het bedrijf](lifecycle-business-understanding.md)
   2. [Gegevens verkrijgen en meer informatie](lifecycle-data.md)
   3. [Modelleren](lifecycle-modeling.md)
   4. [Implementatie](lifecycle-deployment.md)
   5. [Aanvaarding van de klant](lifecycle-acceptance.md)

We bieden volledige instructies voor het demonstreren van alle stappen in het proces voor specifieke scenario's. Het artikel [voorbeeld](walkthroughs.md) scenario's bevat een lijst met de scenario's met koppelingen en miniaturen. In de scenario's wordt uitgelegd hoe u Cloud, on-premises hulpprogram ma's en services in een werk stroom of pijp lijn kunt combi neren om een intelligente toepassing te maken. 
