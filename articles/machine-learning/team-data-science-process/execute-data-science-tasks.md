---
title: Data science tasks uitvoeren-team data Science process
description: Hoe een Data wetenschapper een Data Science-project kan uitvoeren in een trackable, gecontroleerde versie en op samenwerkings wijze.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e180ecbf5c68dbd9c179244083a641ac6ed42de0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100371954"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Data science tasks uitvoeren: verkennen, model leren en implementeren

Typische data science tasks omvatten het verkennen, model leren en implementeren van gegevens. In dit artikel vindt u een overzicht van de taken voor het uitvoeren van verschillende algemene gegevens Science-taken, zoals het verkennen van interactieve gegevens, gegevens analyse, rapportage en het maken van modellen. De opties voor het implementeren van een model in een productie omgeving kunnen het volgende omvatten:

- [Azure Machine Learning](../index.yml)
- [SQL-Server met ML-Services](/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> verkennen 

Een gegevens wetenschapper kan op verschillende manieren onderzoek en rapportage uitvoeren: door gebruik te maken van bibliotheken en pakketten die beschikbaar zijn voor python (matplotlib bijvoorbeeld) of met R (ggplot of raster bijvoorbeeld). Gegevens wetenschappers kunnen dergelijke code aanpassen aan de behoeften van het verkennen van gegevens voor specifieke scenario's. De behoeften voor het verwerken van gestructureerde gegevens zijn anders dan voor ongestructureerde gegevens, zoals tekst of afbeeldingen. 

Producten zoals Azure Machine Learning bieden ook [geavanceerde gegevens voorbereiding](../how-to-create-register-datasets.md) voor het wrangling en verkennen van gegevens, inclusief het maken van functies. De gebruiker moet beslissen over de hulpprogram ma's, Bibliotheken en pakketten die het beste aan hun behoeften voldoen. 

Het product aan het einde van deze fase is een rapport voor gegevens onderzoek. Het rapport moet een redelijk uitgebreid overzicht geven van de gegevens die moeten worden gebruikt voor model lering en een evaluatie van de vraag of de gegevens geschikt zijn om door te gaan naar de model stap. 

## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> model lering

Er zijn talloze tool kits en pakketten voor trainings modellen in diverse talen. Gegevens wetenschappers kunnen gemoeds rust gebruik te maken van de ooit die ze kunnen gebruiken, zolang de prestatie overwegingen met betrekking tot nauw keurigheid en latentie voldoen aan de relevante zakelijke gebruiks situaties en productie scenario's.

### <a name="model-management"></a>Modelbeheer
Nadat er meerdere modellen zijn gebouwd, hebt u doorgaans een systeem nodig voor het registreren en beheren van de modellen. Normaal gesp roken hebt u een combi natie van scripts of Api's en een back-end-data base of versie systeem nodig. Hier volgen enkele opties die u kunt overwegen voor deze beheer taken:

1. [Azure Machine Learning-model beheer service](../index.yml)
2. [ModelDB van MIT](https://people.csail.mit.edu/mvartak/papers/modeldb-hilda.pdf) 
3. [SQL-Server als model beheersysteem](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> implementatie

Bij de productie-implementatie kan een model een actieve rol spelen in een bedrijf. Voor spellingen van een geïmplementeerd model kunnen worden gebruikt voor zakelijke beslissingen.

### <a name="production-platforms"></a>Productie platforms
Er zijn verschillende benaderingen en platformen om modellen in productie te brengen. Hier volgen enkele opties:


- [Model implementatie in Azure Machine Learning](../how-to-deploy-and-where.md)
- [Implementatie van een model in SQL-Server](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Voorafgaand aan de implementatie moet u er zeker van zijn dat de latentie van de model Score laag genoeg is voor gebruik in de productie omgeving.
>
>

Meer voor beelden zijn beschikbaar in een scenario waarin alle stappen in het proces voor **specifieke scenario's** worden getoond. Ze worden weer gegeven en gekoppeld aan miniatuur beschrijvingen in het artikel [voorbeeld scenario's](walkthroughs.md) . Ze illustreren het combi neren van Cloud, on-premises hulpprogram ma's en services in een werk stroom of pijp lijn om een intelligente toepassing te maken.

> [!NOTE]
> Zie [Deploy a Azure machine learning web service](../classic/deploy-a-machine-learning-web-service.md)(Engelstalig) voor implementatie met behulp van Azure machine learning Studio.
>
>

### <a name="ab-testing"></a>A/B-tests
Wanneer er meerdere modellen in productie zijn, kan het nuttig zijn om [een/B-test](https://en.wikipedia.org/wiki/A/B_testing) uit te voeren om de prestaties van de modellen te vergelijken. 

 
## <a name="next-steps"></a>Volgende stappen

[Houd de voortgang bij van data Science-projecten](track-progress.md) om te zien hoe een gegevens wetenschapper de voortgang van een Data Science-project kan volgen.

[Model bewerking en CI/cd](ci-cd-flask.md) laat zien hoe CI/cd kan worden uitgevoerd met ontwikkelde modellen.
