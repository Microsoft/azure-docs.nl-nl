---
title: Collectieve code ring met git-team data Science process
description: Hoe u samenwerkings code kunt ontwikkelen voor data technologie projecten met Git met flexibele planning.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ca24a781f4f3ad5c210813dabbb896de35056ed6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97588706"
---
# <a name="collaborative-coding-with-git"></a>Samenwerken aan code met Git

In dit artikel wordt beschreven hoe u Git kunt gebruiken als het ontwikkelings raamwerk voor gezamenlijke code voor data Science-projecten. In dit artikel wordt beschreven hoe u code kunt koppelen in azure opslag plaatsen aan [flexibele ontwikkelings](agile-development.md) werk items in azure-kaarten, hoe u code beoordelingen kunt uitvoeren en hoe u pull-aanvragen voor wijzigingen kunt maken en samen voegen.

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>Een werk item aan een Azure opslag plaatsen-vertakking koppelen 

Azure DevOps biedt een handige manier om een gebruikers hoofdtekst of taak item van Azure boards te verbinden met een Azure opslag plaatsen Git-opslagplaats vertakking. U kunt uw gebruikers verhaal of taak rechtstreeks koppelen aan de bijbehorende code. 

Als u een werk item wilt verbinden met een nieuwe vertakking, selecteert u het beletsel teken (**...**) naast het werk item en bladert u **naar het context** menu en selecteert u **nieuwe vertakking**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Geef in het dialoog venster **een vertakking maken** de nieuwe vertakkings naam en de basis-opslag plaatsen Git-opslag plaats en-vertakking op. De basis opslagplaats moet zich in hetzelfde Azure DevOps-project betrekken als het werk item. De basis vertakking kan bestaan uit alle bestaande branches. Selecteer **vertakking maken**. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

U kunt ook een nieuwe vertakking maken met behulp van de volgende Git Bash-opdracht in Windows of Linux:

```bash
git checkout -b <new branch name> <base branch name>

```
Als u geen opgeeft \<base branch name> , wordt de nieuwe vertakking gebaseerd op `main` . 

Als u wilt overschakelen naar uw werk vertakking, voert u de volgende opdracht uit: 

```bash
git checkout <working branch name>
```

Nadat u bent overgeschakeld naar de werk vertakking, kunt u beginnen met het ontwikkelen van code of documentatie artefacten om het werk item te volt ooien. Hiermee `git checkout main` schakelt u terug naar de `main` vertakking.

Het is een goed idee om een Git-vertakking te maken voor elk werk item van de gebruikers hoofdtekst. Voor elk taak werk item kunt u vervolgens een vertakking maken op basis van de vertakking van de gebruikers verhaal. Organiseer de vertakkingen in een hiërarchie die overeenkomt met de gebruikers Story-Task relatie wanneer u meerdere personen hebt die aan verschillende gebruikers hoofdtekst werken voor hetzelfde project of op verschillende taken voor hetzelfde gebruikers verhaal. U kunt conflicten minimaliseren door ervoor te zorgen dat elk teamlid aan een andere vertakking werkt of op verschillende code of andere artefacten wanneer een vertakking wordt gedeeld. 

In het volgende diagram ziet u de aanbevolen strategie voor vertakkingen voor TDSP. U hoeft niet zoveel vertakkingen te hebben als hier wordt weer gegeven, vooral wanneer slechts één of twee mensen aan een project werken, of als er slechts één persoon werkt voor alle taken van een gebruikers verhaal. Het scheiden van de ontwikkelings vertakking van de primaire vertakking is altijd een goed idee en kan helpen voor komen dat de release vertakking wordt onderbroken door ontwikkelings activiteiten. Zie voor een volledige beschrijving van het git-vertakkings model [een geslaagd Git-vertakkings model](https://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

U kunt ook een werk item aan een bestaande vertakking koppelen. Selecteer **koppeling toevoegen** op de **detail** pagina van een werk item. Selecteer vervolgens een bestaande vertakking waaraan u het werk item wilt koppelen en selecteer **OK**. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>Werken aan de vertakking en wijzigingen door voeren 

Nadat u een wijziging hebt aangebracht voor uw werk item, zoals het toevoegen van een R-script bestand aan de vertakking van uw lokale computer `script` , kunt u de wijziging door voeren van uw lokale vertakking in de upstream-werk vertakking met behulp van de volgende Git-Bash-opdrachten:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>Een pull-aanvraag maken

Wanneer u klaar bent voor het samen voegen van uw huidige werk vertakking in de basis vertakking van een of meer door voeringen en pushes, kunt u een *pull-aanvraag* maken en verzenden in azure opslag plaatsen. 

Ga op de hoofd pagina van uw Azure DevOps-project naar **opslag plaatsen**  >  **pull-aanvragen** in het linkernavigatievenster. Selecteer vervolgens een van de knoppen van de **nieuwe pull-aanvraag** of de koppeling **een pull-aanvraag maken** .

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Ga, indien nodig, in het scherm **nieuwe pull-aanvraag** naar de Git-opslag plaats en de vertakking waarnaar u uw wijzigingen wilt samen voegen. U kunt andere informatie toevoegen of wijzigen. Voeg onder **revisoren** de namen van de revisoren toe en selecteer vervolgens **maken**. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>Controleren en samen voegen

Wanneer u de pull-aanvraag hebt gemaakt, krijgen uw revisoren een e-mail melding om de pull-aanvraag te bekijken. De revisoren testen of de wijzigingen werken en controleren zo mogelijk de wijzigingen met de aanvrager. De revisoren kunnen opmerkingen maken, wijzigingen aanvragen en de pull-aanvraag goed keuren of afwijzen op basis van hun beoordeling. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

Nadat de revisoren de wijzigingen hebben goedgekeurd, kan u of iemand anders met de machtigingen voor samen voegen de werk vertakking samen voegen met de basis vertakking. Selecteer **volledig** en selecteer vervolgens **volledig samen voegen** in het dialoog venster **pull-aanvraag volt** ooien. U kunt ervoor kiezen om de werk vertakking te verwijderen nadat deze is samengevoegd. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Bevestig dat de aanvraag is gemarkeerd als **voltooid**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Wanneer u teruggaat naar **opslag plaatsen** in de linkernavigatiebalk, kunt u zien dat u bent overgeschakeld naar de hoofd vertakking sinds de `script` vertakking werd verwijderd.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

U kunt ook de volgende Git Bash-opdrachten gebruiken om de `script` werk vertakking samen te voegen met de basis vertakking en de werk vertakking na het samen voegen te verwijderen:

```bash
git checkout main
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>Volgende stappen

[Data Science-taken uitvoeren](execute-data-science-tasks.md) laat zien hoe u hulpprogram ma's kunt gebruiken voor het uitvoeren van verschillende algemene data Science-taken, zoals het verkennen van interactieve gegevens, gegevens analyse, rapportage en het maken van modellen.

Een [voor beeld](walkthroughs.md) van een scenario bevat een lijst met scenario's van specifieke scenario's, met koppelingen en miniaturen. De gekoppelde scenario's illustreren het combi neren van Cloud-en on-premises hulpprogram ma's en services in werk stromen of pijp lijnen om intelligente toepassingen te maken. 

