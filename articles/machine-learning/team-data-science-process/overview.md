---
title: Wat is Team Data Science Process?
description: Hiermee wordt een data science-methodologie geboden waarmee predictive analytics-oplossingen en intelligente toepassingen kunnen worden geleverd.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: overview
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4a341d33c957af8fe7423bda80f1d34a7f59cdd6
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94737996"
---
# <a name="what-is-the-team-data-science-process"></a>Wat is Team Data Science Process?

Het Team Data Science Process (TDSP) is een flexibele, iteratieve data science-methodologie waarmee op een efficiënte manier predictive analytics-oplossingen en intelligente toepassingen worden geboden. TDSP helpt teamsamenwerking en het leren te verbeteren door suggesties te doen van hoe teamrollen het beste samenwerken. TDSP bevat de best practices en structuren van Microsoft en andere toonaangevende bedrijven in deze branche om de implementatie van initiatieven op het gebied van data science te laten slagen. Het doel is bedrijven te helpen om de voordelen van hun analyseprogramma volledig te kunnen benutten.

Dit artikel bevat een overzicht van TDSP en de belangrijkste onderdelen hiervan. We bieden hier een algemene beschrijving van het proces dat kan worden geïmplementeerd met verschillende soorten hulpprogramma's. Een gedetailleerde beschrijving van de projecttaken en rollen die bij de levenscyclus van het proces betrokken zijn, vindt u in aanvullende gekoppelde onderwerpen. Ook worden er richtlijnen geboden voor het implementeren van de TDSP met behulp van een specifieke set hulpprogramma's en infrastructuur van Microsoft die we gebruiken voor het implementeren van de TDSP in onze teams.

## <a name="key-components-of-the-tdsp"></a>Belangrijke onderdelen van de TDSP

TDSP bevat de volgende belangrijke onderdelen:

- Een definitie van de **data science-levenscyclus**
- Een **gestandaardiseerde projectstructuur**
- Aanbevolen **infrastructuur en bronnen** voor data science-projecten
- Aanbevolen **hulpprogramma's** voor het uitvoeren van projecten


## <a name="data-science-lifecycle"></a>Data science-levenscyclus

Het Team Data Science Process (TDSP) biedt een levenscyclus voor het structureren van de ontwikkeling van uw data science-projecten. De levenscyclus geeft een overzicht van de volledige stappen die voor succesvolle projecten moeten worden gevolgd.

Als u een andere data science-levenscyclus gebruikt, zoals [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) of een eigen aangepast proces van uw organisatie, kunt u nog steeds de op taken gebaseerde TDSP in de context van die ontwikkelingslevenscycli gebruiken. Deze verschillende methodologieën hebben in de basis veel gemeen. 

Deze levenscyclus is ontworpen voor data science-projecten die als onderdeel van intelligente toepassingen worden geleverd. Met deze toepassingen worden modellen voor machine learning of kunstmatige intelligentie geïmplementeerd voor predictive analytics. Verkennende data science- of geïmproviseerde analytics-projecten kunnen ook van dit proces profiteren. Maar in dergelijke gevallen zijn enkele van de beschreven stappen mogelijk niet nodig.    

De levenscyclus bevat de belangrijkste fasen die projecten meestal doorlopen, vaak iteratief:

* **Inzicht in het bedrijf**
* **Gegevens verzamelen en begrijpen**
* **Modelleren**
* **Implementatie**

Hier volgt een visuele weergave van de **Team Data Science Process-levenscyclus**. 

![In het diagram ziet u de levenscyclus van de gegevenswetenschap, inclusief inzicht in het bedrijf, het verkrijgen/begrijpen van gegevens, modellering en implementatie.](./media/overview/tdsp-lifecycle2.png) 

De doelen, taken en documentatie-artefacten voor elke fase van de levenscyclus in TDSP worden beschreven in het onderwerp [Team Data Science Process-levenscyclus](lifecycle.md). Deze taken en artefacten zijn gekoppeld aan projectrollen:

- Oplossingsarchitect
- Projectmanager
- Gegevenstechnicus
- Gegevenswetenschapper
- Toepassingsontwikkelaar
- Projectleider 

In het volgende diagram ziet u een rasterweergave van de taken (in het blauw) en artefacten (in het groen) die zijn gekoppeld aan elke fase van de levenscyclus (op de horizontale as) voor deze rollen (op de verticale as). 

[![TDSP-roles-and-tasks](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Gestandaardiseerde projectstructuur

Wanneer alle projecten een mappenstructuur delen en sjablonen gebruiken voor projectdocumenten, kunnen teamleden gemakkelijk informatie over hun projecten vinden. Alle code en documenten worden opgeslagen in een systeem met versiebeheer (VCS), zoals Git, TFS of Subversion, om teamsamenwerking mogelijk te maken. Het bijhouden van taken en functies in een flexibel systeem voor projecttracering, zoals Jira, Rally en Azure DevOps, maakt nauwkeurige tracering van de code voor afzonderlijke functies mogelijk. Met dergelijke tracering beschikken teams ook over betere kostenramingen. TDSP adviseert het maken van een afzonderlijke opslagplaats voor elk project in het VCS ten behoeve van versiebeheer, gegevensbeveiliging en samenwerking. De gestandaardiseerde structuur voor alle projecten helpt bij het bouwen van institutionele kennis binnen de hele organisatie.

We bieden sjablonen voor de mappenstructuur en vereiste documenten op standaardlocaties. In deze mappenstructuur worden de bestanden geordend die code bevatten voor het verkennen van gegevens en het uitpakken van onderdelen en waarin modeliteraties zijn vastgelegd. Deze sjablonen maken het gemakkelijker voor teamleden om inzicht te krijgen in werk dat door anderen wordt uitgevoerd en om nieuwe leden aan teams toe te voegen. U kunt gemakkelijk documentsjablonen bekijken en bijwerken in de Markdown-indeling. Gebruik sjablonen om controlelijsten met belangrijke vragen voor elk project te leveren. Zo zorgt u ervoor dat het probleem goed is gedefinieerd en dat de resultaten voldoen aan de verwachte kwaliteit. Voorbeelden zijn:

- een projectcharter om het bedrijfsprobleem en de reikwijdte van het project te documenteren
- gegevensrapporten om de structuur en statistieken van de onbewerkte gegevens te documenteren
- modelrapporten om de afgeleide functies te documenteren
- metrische prestatiegegevens voor modellen, zoals ROC-curven of MSE


[![TDSP-directories](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

De mappenstructuur kan worden gekloond van [GitHub](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastructuur en bronnen voor data science-projecten  

TDSP biedt aanbevelingen voor het beheren van gedeelde analyses en opslaginfrastructuur, zoals:

- cloudbestandssystemen voor het opslaan van gegevenssets 
- databases
- big data-clusters (SQL of Spark) 
- machine learning-service 

De analyse- en opslaginfrastructuur, waar onbewerkte en verwerkte gegevenssets worden opgeslagen, kan zich in de cloud of on-premises bevinden. Met deze infrastructuur zijn reproduceerbare analyses mogelijk. Ook vermijdt u hiermee duplicaties die tot inconsistenties en onnodige infrastructuurkosten kunnen leiden. Er zijn hulpprogramma's beschikbaar om gedeelde resources in te richten en de resources te volgen en om elk teamlid toe te staan veilig verbinding te maken met deze resources. Het is ook een goed idee om projectleden een consistente rekenomgeving te laten maken. Andere teamleden kunnen dan experimenten repliceren en valideren.

Hier volgt een voorbeeld van een team dat aan meerdere projecten werkt en dat verschillende infrastructuuronderdelen voor analyses in de cloud deelt.

[![TDSP-infrastructure](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Hulpprogramma's voor het uitvoeren van projecten

In de meeste organisaties is het lastig nieuwe processen te introduceren. De geleverde hulpprogramma's voor het implementeren van data science-processen en -levenscycli helpen barrières te slechten en verbeteren de consistentie van de ingebruikname. TDSP biedt een initiële set hulpprogramma's en scripts waarmee u snel met TDSP binnen een team aan de slag kunt gaan. Ook kunt u hiermee bepaalde veelvoorkomende taken in de data science-levenscyclus automatiseren, zoals het verkennen van gegevens en het maken van basismodellen. Er wordt een goed gedefinieerde structuur geboden waarmee individuen hun gedeelde hulpprogramma's kunnen leveren aan de opslagplaats voor gedeelde code van hun team. Deze resources kunnen vervolgens worden gebruikt door andere projecten binnen het team of de organisatie.  Microsoft biedt uitgebreide hulpprogramma's in [Azure Machine Learning](../index.yml) die ondersteuning bieden voor open-sourceprogramma's (Python, R, ONNX en algemene Deep Learning-frameworks) en voor Microsoft-hulpprogramma's (AutoML).


## <a name="next-steps"></a>Volgende stappen

[Team Data Science Process: rollen en taken](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) geeft een overzicht van de belangrijkste personeelsrollen en de bijbehorende taken voor een data science-team dat wordt gestandaardiseerd voor dit proces. 
