---
title: Rollen en taken voor team data Science process
description: Een overzicht van de belangrijkste onderdelen, mede werkers rollen en de bijbehorende taken voor een Data Science-groep.
author: marktab
manager: marktab
editor: marktab
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4bcbbd9378b6ea861c926eed16d2ceed8131b913
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000960"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Rollen en taken voor team data Science process

Het team data Science process (TDSP) is een door micro soft ontwikkelde framework dat een gestructureerde methodologie biedt om efficiënt predictive analytics oplossingen en intelligente toepassingen te bouwen. In dit artikel vindt u een overzicht van de belangrijkste personeels rollen en bijbehorende taken voor een Data Science-team dat wordt gestandardization op dit proces.

Dit inleidende artikel maakt koppelingen naar zelf studies over het instellen van de TDSP omgeving. De zelf studies bieden gedetailleerde richt lijnen voor het gebruik van Azure DevOps Projects, Azure opslag plaatsen-opslag plaatsen en Azure-kaarten.  Het motiverende doel is van concept tot model en in implementatie.

De zelf studies gebruiken Azure DevOps omdat u TDSP bij micro soft implementeert. Azure DevOps vereenvoudigt samen werking door beveiliging op basis van rollen, beheer en tracering van werk items en code-hosting, delen en broncode beheer te integreren. De zelf studies gebruiken ook een Azure [Data Science virtual machine](https://aka.ms/dsvm) (DSVM) als het Analytics Desktop, met een aantal populaire hulpprogram ma's voor gegevens wetenschap, vooraf geconfigureerd en geïntegreerd met micro soft-software en Azure-Services. 

U kunt de zelf studies gebruiken om TDSP te implementeren met andere code hosting-, flexibele plannings-en ontwikkel hulpprogramma's en-omgevingen, maar sommige functies zijn mogelijk niet beschikbaar.

## <a name="structure-of-data-science-groups-and-teams"></a>Structuur van data Science-groepen en teams

Data Science-functies in ondernemingen zijn vaak ingedeeld in de volgende hiërarchie:

- Data Science-groep
  - Data Science Team/s binnen de groep

In een dergelijke structuur zijn er groeps leads en team leiders. Normaal gesp roken wordt een Data Science-project uitgevoerd door een Data Science-Team. Data Science-teams hebben project leiders voor project management en beheer taken, en individuele gegevens wetenschappers en technici om de gegevens wetenschap en data engineering onderdelen van het project uit te voeren. De eerste project instellingen en governance worden uitgevoerd door de groep, het team of de project leads.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>Definitie en taken voor de vier TDSP-rollen
Met de veronderstelling dat de data Science-eenheid bestaat uit teams binnen een groep, zijn er vier afzonderlijke rollen voor TDSP personeel:

1. **Groeps beheer**: beheert de volledige data Science-eenheid in een onderneming. Een Data Science-eenheid kan meerdere teams hebben, die allemaal aan meerdere data Science-projecten werken in afzonderlijke zakelijke verticales. Een groeps beheerder kan hun taken overdragen aan een surrogaat, maar de taken die aan de rol zijn gekoppeld, worden niet gewijzigd.
   
2. **Team leider**: beheert een team in de data Science-eenheid van een onderneming. Een team bestaat uit meerdere gegevens wetenschappers. Voor een kleine data Science-eenheid kunnen de groeps Manager en de team leider dezelfde persoon zijn.
   
3. **Project leider**: beheert de dagelijkse activiteiten van afzonderlijke gegevens wetenschappers op een specifiek data Science-project.
   
4. **Project individuele mede** werkers: gegevens wetenschappers, bedrijfs analisten, gegevens technici, architecten en anderen die een Data Science-project uitvoeren.

> [!NOTE]
> Afhankelijk van de structuur en grootte van een onderneming mag één persoon meer dan één rol spelen of kan meer dan één persoon een rol invullen.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>Taken die moeten worden uitgevoerd door de vier rollen

In het volgende diagram ziet u de taken op het hoogste niveau voor elke rol van team data Science process. Dit schema en de volgende, gedetailleerder overzicht van taken voor elke TDSP-rol kunnen u helpen bij het kiezen van de zelf studie die u nodig hebt op basis van uw verantwoordelijkheden.

![Overzicht van functies en taken](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Taken voor groeps beheer

De groeps beheerder of een aangewezen TDSP systeem beheerder voert de volgende taken uit om de TDSP te nemen:

- Hiermee maakt u een Azure DevOps **-organisatie** en een groeps project binnen de organisatie. 
- Maakt een **project sjabloon opslagplaats** in het Azure DevOps-groeps project en zaait deze van de project sjabloon opslagplaats die is ontwikkeld door het micro soft TDSP-team. De micro soft TDSP project-sjabloon opslagplaats biedt:
  - Een **gestandaardiseerde mapstructuur**, inclusief mappen voor gegevens, code en documenten.
  - Een reeks **gestandaardiseerde document sjablonen** die een efficiënt data Science proces kunnen begeleiden.
- Hiermee maakt u een **opslag plaats voor hulpprogram ma's** en zaait u deze vanuit de bibliotheek met hulp programma die is ontwikkeld door het micro soft TDSP-team. De opslag plaats TDSP van micro soft biedt een aantal nuttige hulpprogram ma's om het werk van een gegevens wetenschapper efficiënter te maken. De micro soft Utility repository bevat hulpprogram ma's voor interactieve gegevens verkenning, analyse, rapportage en basis modellering en-rapportage.
- Hiermee stelt u het **beleid voor beveiligings beheer** in voor het organisatie account.

Zie voor gedetailleerde instructies [groeps beheer taken voor een Data Science-Team](group-manager-tasks.md).

## <a name="team-lead-tasks"></a>Taken team lead

De team lead of een aangewezen Project beheerder voert de volgende taken uit om de TDSP aan te passen:

- Hiermee maakt u een team **project** in de Azure DevOps-organisatie van de groep.
- Maakt de **project sjabloon opslagplaats** in het project en zaait deze uit de opslag plaats van de groeps project sjabloon die is ingesteld door de groeps Manager of gemachtigde.
- Hiermee maakt u de **opslag plaats voor team hulpprogramma's**, zaait u deze uit de opslag plaats voor groeps hulpprogramma's en voegt u team afhankelijke hulpprogram ma's toe aan de opslag plaats.
- Hiermee maakt u eventueel [Azure File Storage](https://azure.microsoft.com/services/storage/files/) om nuttige gegevensassets voor het team op te slaan. Andere team leden kunnen dit gedeelde Cloud bestands archief koppelen aan hun Analytics-Desk tops.
- De Azure-bestands opslag wordt eventueel gekoppeld aan de **DSVM** van het team en er worden hieraan team gegevensassets toegevoegd.
- Hiermee stelt u **beveiligings beheer** in door team leden toe te voegen en hun machtigingen te configureren.

Zie [team lead taken voor een Data Science-Team](team-lead-tasks.md)voor gedetailleerde instructies.


## <a name="project-lead-tasks"></a>Taken project leider

De project leider voert de volgende taken uit om de TDSP te nemen:

- Hiermee maakt u een **project opslagplaats** in het team project en zaait u deze uit de opslag plaats van de project sjabloon.
- Hiermee maakt u optioneel **Azure File Storage** voor het opslaan van de gegevensassets van het project.
- Koppelt de Azure-bestands opslag eventueel aan de **DSVM** en voegt hieraan project gegevensassets toe.
- Hiermee stelt u **beveiligings beheer** in door Project leden toe te voegen en hun machtigingen te configureren.

Zie [Project Lead tasks for a data Science Team](project-lead-tasks.md)(Engelstalig) voor gedetailleerde instructies.

## <a name="project-individual-contributor-tasks"></a>Individuele Inzender taken projecteren

Het project Individuele Inzender, meestal een Data wetenschapper, voert de volgende taken uit met behulp van de TDSP:

- De **project opslagplaats** die is ingesteld door de project leider wordt gekloond.
- Koppelt eventueel het gedeelde team en project **Azure File Storage** op hun **Data Science virtual machine** (DSVM).
- Het project wordt uitgevoerd.

Zie voor gedetailleerde instructies voor het voorbereiden op een project [afzonderlijke Inzender taken voor een Data Science-Team](project-ic-tasks.md).

## <a name="data-science-project-execution-workflow"></a>Werk stroom voor het uitvoeren van data Science-projecten

Door de relevante zelf studies, gegevens wetenschappers, project leiders en team leiders te volgen, kunnen werk items worden gemaakt om alle taken en fasen voor het project van begin tot eind bij te houden. Het gebruik van Azure opslag plaatsen bevordert de samen werking tussen gegevens wetenschappers en zorgt ervoor dat de artefacten die tijdens de project uitvoering worden gegenereerd, versie worden beheerd en gedeeld door alle project leden. Met Azure DevOps kunt u uw werk items van Azure-kaarten koppelen aan uw Azure opslag plaatsen-opslag plaats vertakkingen en kunt u eenvoudig bijhouden wat er is gedaan voor een werk item.

De volgende afbeelding geeft een overzicht van de TDSP-werk stroom voor het uitvoeren van projecten:

![Typische werk stroom van data Science project](./media/roles-tasks/overview-project-execute.png)

De werk stroom stappen kunnen worden gegroepeerd in drie activiteiten:

- Project leiders voeren Sprint planning
- Gegevens wetenschappers bouwen artefacten op `git` branches voor het oplossen van werk items
- Project leiders of andere team leden doen code beoordelingen en voegen werk vertakkingen samen in de primaire vertakking

Zie [flexibele ontwikkeling van data Science-projecten](agile-development.md)voor gedetailleerde instructies voor de werk stroom voor het uitvoeren van projecten.

## <a name="tdsp-project-template-repository"></a>Opslag plaats voor TDSP-project sjabloon

Gebruik de opslag plaats van de [project sjabloon](https://github.com/Azure/Azure-TDSP-ProjectTemplate) van het micro soft TDSP-team ter ondersteuning van efficiënte project uitvoering en-samen werking. De opslag plaats biedt u een gestandaardiseerde mapstructuur en document sjablonen die u voor uw eigen TDSP-projecten kunt gebruiken.

## <a name="next-steps"></a>Volgende stappen

Bekijk gedetailleerde beschrijvingen van de rollen en taken die zijn gedefinieerd door het team data Science process:

- [Groeps Manager-taken voor een Data Science-Team](group-manager-tasks.md)
- [Team lead taken voor een Data Science-Team](team-lead-tasks.md)
- [Project Lead taken voor een Data Science-Team](project-lead-tasks.md)
- [Individuele Inzender taken projecteren voor een Data Science-Team](project-ic-tasks.md)
