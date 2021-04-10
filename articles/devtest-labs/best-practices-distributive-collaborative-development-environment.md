---
title: Gedistribueerde samenwerkings ontwikkeling van Azure DevTest Labs resources
description: Voorziet in Aanbevolen procedures voor het instellen van een gedistribueerde en samenwerkings omgeving voor het ontwikkelen van DevTest Labs-resources.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: caf4bd13f2ec9c45db392a027db269b492cbd802
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550073"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Aanbevolen procedures voor gedistribueerde en samen werkende ontwikkeling van Azure DevTest Labs bronnen
Dankzij gedistribueerde samenwerkings ontwikkeling kunnen verschillende teams of mensen een code basis ontwikkelen en onderhouden. Om te slagen, is het ontwikkel proces afhankelijk van de mogelijkheid om informatie te maken, te delen en te integreren. Deze methode voor het ontwikkelen van sleutels kan worden gebruikt in Azure DevTest Labs. Er zijn verschillende soorten resources binnen een lab die vaak tussen verschillende Labs binnen een onderneming worden gedistribueerd. De verschillende typen resources zijn gericht op twee gebieden:

- Bronnen die intern zijn opgeslagen in het lab (op basis van Lab)
- Bronnen die zijn opgeslagen in [externe opslag plaatsen die zijn verbonden met het lab](devtest-lab-add-artifact-repo.md) (op code opslagplaats gebaseerd). 

In dit document worden enkele aanbevolen procedures beschreven voor het toestaan van samen werking en distributie in meerdere teams, terwijl de aanpassing en kwaliteit op alle niveaus worden gegarandeerd.

## <a name="lab-based-resources"></a>Resources op basis van Lab

### <a name="custom-virtual-machine-images"></a>Aangepaste installatie kopieën voor virtuele machines
U kunt een gemeen schappelijke bron van aangepaste installatie kopieën hebben die dagelijks worden geïmplementeerd in Labs. Zie [Image Factory](image-factory-create.md)voor gedetailleerde informatie.    

### <a name="formulas"></a>Formules
[Formules](devtest-lab-manage-formulas.md) zijn specifiek voor het lab en hebben geen distributie mechanisme. De leden van de test omgeving doen de ontwikkeling van formules. 

## <a name="code-repository-based-resources"></a>Resources op basis van code opslagplaats
Er zijn twee verschillende functies die zijn gebaseerd op code opslagplaatsen, artefacten en omgevingen. In dit artikel worden de functies beschreven en wordt uitgelegd hoe u het meest effectief opslag plaatsen en de werk stroom kunt instellen zodat de beschik bare artefacten en omgevingen op organisatie niveau of team niveau kunnen worden aangepast.  Deze werk stroom is gebaseerd op de standaard [strategie voor broncode beheer](/azure/devops/repos/tfvc/branching-strategies-with-tfvc). 

### <a name="key-concepts"></a>Belangrijkste concepten
De bron gegevens voor artefacten bevatten meta gegevens, scripts. De bron gegevens voor omgevingen bevatten meta gegevens en Resource Manager-sjablonen met ondersteunende bestanden zoals Power shell-scripts, DSC-scripts, zip-bestanden enzovoort.  

### <a name="repository-structure"></a>Structuur van opslag plaats  
De meest voorkomende configuratie voor broncode beheer (SCC) is het instellen van een structuur met meerdere lagen voor het opslaan van code bestanden (Resource Manager-sjablonen, meta gegevens en scripts) die in de Labs worden gebruikt. Maak met name verschillende opslag plaatsen om de resources op te slaan die worden beheerd door de verschillende niveaus van het bedrijf:   

- Bronnen voor het hele bedrijf.
- Bronnen voor bedrijfs eenheid/divisie
- Team-specifieke resources.

Elk van deze niveaus is gekoppeld aan een andere opslag plaats waar de hoofd vertakking van de productie kwaliteit moet zijn. De [vertakkingen](/azure/devops/repos/git/git-branching-guidance) in elke opslag plaats zijn bedoeld voor de ontwikkeling van deze specifieke resources (artefacten of sjablonen). Deze structuur wordt goed uitgelijnd met DevTest Labs, omdat u eenvoudig meerdere opslag plaatsen en meerdere vertakkingen tegelijk kunt verbinden met de laboratoria van de organisatie. De naam van de opslag plaats is opgenomen in de gebruikers interface (UI) om Verwar ring te voor komen wanneer er identieke namen, een beschrijving en een uitgever zijn.
     
In het volgende diagram ziet u twee opslag plaatsen: een bedrijfs opslagplaats die wordt onderhouden door de IT-afdeling en een divisie opslagplaats die wordt onderhouden door de R&D-afdeling.

![Een voor beeld van distributie en samen werkende ontwikkel omgeving](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Deze gelaagde structuur maakt het mogelijk om te ontwikkelen met een hoger niveau van kwaliteit bij de hoofd vertakking, terwijl meerdere opslag plaatsen zijn verbonden met een lab, meer flexibiliteit biedt.

## <a name="next-steps"></a>Volgende stappen    
Zie de volgende artikelen:

- Een opslag plaats toevoegen aan een Lab met behulp van de [Azure Portal](devtest-lab-add-artifact-repo.md) of via [Azure resource management-sjabloon](add-artifact-repository.md)
- [DevTest Labs-artefacten](devtest-lab-artifact-author.md)
- [DevTest Labs-omgevingen](devtest-lab-create-environment-from-arm.md).
