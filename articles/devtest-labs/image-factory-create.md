---
title: Een image Factory maken in Azure DevTest Labs | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een aangepaste installatie kopie-Factory instelt met behulp van voorbeeld scripts die beschikbaar zijn in de Git-opslag plaats (Azure DevTest Labs).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 14c8338d6a5ae21847da3a9c774ea6dcdac33ae4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85482068"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Een aangepaste installatie kopie maken in de fabriek in Azure DevTest Labs
In dit artikel wordt beschreven hoe u een aangepaste installatie kopie-Factory instelt met behulp van voorbeeld scripts die beschikbaar zijn in de [Git-opslag plaats](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>Wat is een image Factory?
Een image Factory is een oplossing voor configuratie-as-code waarmee installatie kopieën automatisch worden gebouwd en gedistribueerd op regel matige basis met alle gewenste configuraties. De installatie kopieën in de installatie kopie-Factory zijn altijd up-to-date en het doorlopende onderhoud is bijna nul zodra het hele proces is geautomatiseerd. En omdat alle vereiste configuraties al aanwezig zijn in de installatie kopie, wordt de tijd bespaard om het systeem hand matig te configureren nadat een virtuele machine is gemaakt met het basis besturingssysteem.

De belang rijke Accelerator om een ontwikkelaars Desktop te krijgen in DevTest Labs maakt gebruik van aangepaste installatie kopieën. Het nadeel van aangepaste installatie kopieën is dat er iets extra is om te onderhouden in het lab. Proef versies van producten verlopen bijvoorbeeld na verloop van tijd (of) nieuwe beveiligings updates worden niet toegepast, waardoor we de aangepaste installatie kopie periodiek kunnen vernieuwen. Met een image Factory hebt u een definitie van de installatie kopie die is ingecheckt bij broncode beheer en hebt u een geautomatiseerd proces om aangepaste installatie kopieën te maken op basis van de definitie.

De oplossing maakt het mogelijk om virtuele machines te maken op basis van aangepaste installatie kopieën, terwijl extra onderhouds kosten worden geëlimineerd. Met deze oplossing kunt u automatisch aangepaste installatie kopieën maken, deze naar andere DevTest Labs distribueren en de oude installatie kopieën buiten gebruik stellen. In de volgende video vindt u informatie over de installatie kopie-Factory en hoe deze wordt geïmplementeerd met DevTest Labs.  Alle Azure Power shell-scripts zijn vrij beschikbaar en bevinden zich hier:  [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory) .

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Weer gave op hoog niveau van de oplossing
De oplossing maakt het mogelijk om virtuele machines te maken op basis van aangepaste installatie kopieën, terwijl extra onderhouds kosten worden geëlimineerd. Met deze oplossing kunt u automatisch aangepaste installatie kopieën maken en deze distribueren naar andere DevTest Labs. U gebruikt Azure DevOps (voorheen Visual Studio Team Services) als de Orchestration-engine voor het automatiseren van de bewerkingen in de DevTest Labs.

![Weer gave op hoog niveau van de oplossing](./media/create-image-factory/high-level-view-of-solution.png)

Er is een [VSTS-extensie voor DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) waarmee u deze afzonderlijke stappen kunt uitvoeren:

- Aangepaste installatiekopie maken
- VM maken
- VM verwijderen
- Omgeving maken
- Omgeving verwijderen
- Omgeving vullen

Het gebruik van de DevTest Labs-extensie is een eenvoudige manier om aan de slag te gaan met het automatisch maken van aangepaste installatie kopieën in DevTest Labs.

Er is een alternatieve implementatie met behulp van het Power shell-script voor een complexere scenario. Met Power shell kunt u een installatie kopie-Factory volledig automatiseren op basis van DevTest Labs die kan worden gebruikt in uw doorlopende integratie en doorlopende levering (CI/CD) hulpprogramma keten. De beginselen gevolgd in deze alternatieve oplossing zijn:

- Algemene updates moeten geen wijzigingen in de installatie kopie-Factory vereisen. (bijvoorbeeld het toevoegen van een nieuw type aangepaste installatie kopie, het automatisch buiten gebruik stellen van oude installatie kopieën, het toevoegen van een nieuw ' endpoint ' DevTest Labs om aangepaste installatie kopieën te ontvangen, enzovoort.)
- Algemene wijzigingen worden ondersteund door broncode beheer (infra structuur als code)
- DevTest Labs die aangepaste installatie kopieën ontvangen, bevinden zich mogelijk niet in hetzelfde Azure-abonnement (Labs span-abonnementen)
- Power shell-scripts moeten opnieuw worden gebruikt, zodat we indien nodig extra fabrieken kunnen door nemen

## <a name="next-steps"></a>Volgende stappen
Ga naar het volgende artikel in deze sectie: [een installatie kopie uitvoeren vanuit Azure DevOps](image-factory-set-up-devops-lab.md)
