---
title: Flexibele ontwikkeling van data Science-projecten-team data Science process
description: Een Data Science-project uitvoeren in een systematische, gecontroleerde versie en op samenwerkings wijze binnen een project team met behulp van het team data Science process.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 113c11b749b42bbaf9e5e40cdf63623288997a92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "89440283"
---
# <a name="agile-development-of-data-science-projects"></a>Flexibele ontwikkeling van data Science-projecten

In dit document wordt beschreven hoe ontwikkel aars met behulp van het [team data Science process](overview.md) (TDSP) een Data Science-project kunnen uitvoeren in een systematische, gecontroleerde versie en op samenwerkings wijze binnen een project team. De TDSP is een framework dat is ontwikkeld door micro soft en een gestructureerde reeks activiteiten biedt om op een efficiënte manier cloud-gebaseerde predictive analytics oplossingen uit te voeren. Voor een overzicht van de rollen en taken die worden verwerkt door een Data Science-team dat wordt gestandardization op de TDSP, raadpleegt u [rollen en taken voor team data Science process](roles-tasks.md). 

In dit artikel vindt u instructies voor het volgende: 

- Doe *Sprint planning* voor werk items die bij een project betrokken zijn.
- *Werk items* toevoegen aan sprints.
- Maak en gebruik een *flexibel, afgeleide werk item sjabloon* die specifiek is afgestemd op TDSP levenscyclus fasen.

De volgende instructies zijn een overzicht van de stappen die nodig zijn om een TDSP-team omgeving in te stellen met behulp van Azure-kaarten en Azure opslag plaatsen in azure DevOps. De instructies gebruiken Azure DevOps omdat u TDSP bij micro soft implementeert. Als uw groep gebruikmaakt van een ander platform voor het hosten van een code, worden de team lead taken over het algemeen niet gewijzigd, maar de manier om de taken uit te voeren, is anders. Zo is het koppelen van een werk item met een Git-vertakking mogelijk niet hetzelfde als bij Azure opslag plaatsen.

In de volgende afbeelding ziet u een typische werk stroom voor Sprint planning,-code ring en-bron beheer voor een Data Science-project:

![Team Data Science Process](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>Typen werk items

In het TDSP Sprint planning Framework zijn vier veelgebruikte typen *werk items* : *functies*, *gebruikers verhalen*, *taken* en *fouten*. De achterstand voor alle werk items is op project niveau, niet op het niveau van de Git-opslag plaats. 

Hier vindt u de definities voor de typen werk items:

- **Functie**: een functie komt overeen met een project engagement. Verschillende afspraken met een client zijn verschillende functies en het is raadzaam om verschillende fasen van een project als verschillende functies te beschouwen. Als u een schema kiest, zoals de *\<ClientName>-\<EngagementName>* naam van uw functies, kunt u de context van het project en de betrokkenheid van de namen zelf herkennen.
  
- **Gebruikers verhaal**: gebruikers verhalen zijn werk items die nodig zijn om end-to-end een functie te volt ooien. Voor beelden van verhalen van gebruikers zijn:
  - Gegevens ophalen 
  - Gegevens verkennen 
  - Functies genereren
  - Modellen bouwen
  - Modellen operationeel maken 
  - Modellen opnieuw trainen
  
- **Taak**: taken zijn toegewezen werk items die moeten worden uitgevoerd om een specifiek gebruikers verhaal te volt ooien. Zo kunnen de taken in het artikel gegevens van de gebruiker worden *opgehaald* :
  - SQL Server referenties ophalen
  - Gegevens uploaden naar Azure Synapse Analytics
  
- **Bug**: bugs zijn problemen in bestaande code of documenten die moeten worden opgelost om een taak te volt ooien. Als fouten worden veroorzaakt door ontbrekende werk items, kunnen ze escaleren naar gebruikers verhalen of taken. 

Gegevens wetenschappers kunnen meer vertrouwd zijn met een flexibel sjabloon waarmee functies, gebruikers verhalen en taken met TDSP levenscyclus fasen en subfaseën worden vervangen. Zie [een flexibele TDSP-werk sjabloon gebruiken](#set-up-agile-dsp-6)voor het maken van een flexibel afgeleide sjabloon die specifiek wordt uitgelijnd met de levenscyclus fase van de TDSP.

> [!NOTE]
> TDSP leent de concepten van functies, ervaringen van gebruikers, taken en bugs vanuit software code Management (SCM). De TDSP-concepten kunnen enigszins verschillen van hun conventionele SCM-definities.

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>Sprints plannen

Veel gegevens wetenschappers zijn voorzien van meerdere projecten, waardoor maanden kunnen worden voltooid en door lopen op verschillende Paces. Sprint planning is handig voor prioriteiten van projecten en het plannen en toewijzen van resources. In azure boards kunt u eenvoudig werk items voor uw projecten maken, beheren en bijhouden, en de Sprint planning uitvoeren om ervoor te zorgen dat projecten naar verwachting worden verplaatst.

Zie [Scrum sprints](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint)(Engelstalig) voor meer informatie over het plannen van sprints. 

Zie [achterstallig artikelen toewijzen aan een sprint](/azure/devops/boards/sprints/assign-work-sprint)voor meer informatie over het plannen van sprints in azure-kaarten. 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>Een functie toevoegen aan de achterstand 

Nadat u uw project-en project code opslagplaats hebt gemaakt, kunt u een functie toevoegen aan de achterstand om het werk voor uw project weer te geven.

1. Op de pagina project selecteert u achterstanden voor **kaarten**  >   in de linkernavigatiebalk. 
   
1. Als op het tabblad **achterstand** het type werk item in de bovenste balk de tekst **verhalen**, vervolg keuzelijst en selecteer **functies**. Selecteer vervolgens **nieuw werk item.**
   
   ![Selecteer Nieuw werk item](./media/agile-development/2-sprint-team-overview.png)
   
1. Voer een titel in voor de functie, meestal de project naam en selecteer vervolgens **bovenaan toevoegen**. 
   
   ![Voer een titel in en selecteer bovenaan toevoegen](./media/agile-development/3-sprint-team-add-work.png)
   
1. Selecteer en open de nieuwe functie in de lijst **achterstand** . Vul de beschrijving in, wijs een teamlid toe en stel plannings parameters in. 
   
   U kunt de functie ook koppelen aan de Azure opslag plaatsen-code opslagplaats van het project door **koppeling toevoegen** te selecteren onder de sectie **ontwikkeling** . 
   
   Nadat u de functie hebt bewerkt, selecteert u **opslaan & sluiten**.
   
   ![Bewerk de functie en selecteer Opslaan & sluiten](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>Een gebruikers verhaal toevoegen aan de functie 

Onder deze functie kunt u gebruikers verhalen toevoegen om de belangrijkste stappen te beschrijven die nodig zijn om het project te volt ooien. 

Een nieuw gebruikers verhaal toevoegen aan een functie:

1. Selecteer op het tabblad **achterstand** de **+** links van de functie. 
   
   ![Een nieuw gebruikers verhaal toevoegen onder de functie](./media/agile-development/4-sprint-add-story.png)
   
1. Geef het gebruikers verhaal een titel en bewerk details zoals toewijzing, status, beschrijving, opmerkingen, planning en prioriteit. 
   
   U kunt de gebruikers hoofdtekst ook koppelen aan een vertakking van de Azure opslag plaatsen code opslagplaats van het project door **koppeling toevoegen** te selecteren onder de sectie **ontwikkeling** . Selecteer de opslag plaats en de vertakking waaraan u het werk item wilt koppelen en selecteer vervolgens **OK**.
   
   ![Koppeling toevoegen](./media/agile-development/5-sprint-edit-story.png)
   
1. Wanneer u klaar bent met het bewerken van de gebruikers hoofdtekst, selecteert u **opslaan & sluiten**. 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>Een taak toevoegen aan een gebruikers verhaal 

Taken zijn specifieke gedetailleerde stappen die nodig zijn om elk gebruikers verhaal te volt ooien. Nadat alle taken van een gebruikers verhaal zijn voltooid, moet het gebruikers verhaal ook worden voltooid. 

Als u een taak aan een gebruikers hoofdtekst wilt toevoegen, selecteert u de optie **+** naast het artikel van de gebruiker en selecteert u **taak**. Vul de titel en andere informatie in de taak in.

![Een taak toevoegen aan een gebruikers verhaal](./media/agile-development/7-sprint-add-task.png)

Nadat u functies, gebruikers verhalen en taken hebt gemaakt, kunt u deze weer geven in de weer gaven **achterstand** of **kamers** om hun status bij te houden.

![Weer gave achterstanden](./media/agile-development/8-sprint-backlog-view.png)

![Weer gave borden](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>Een flexibele TDSP-werk sjabloon gebruiken

Gegevens wetenschappers kunnen meer vertrouwd zijn met een flexibel sjabloon waarmee functies, gebruikers verhalen en taken met TDSP levenscyclus fasen en subfaseën worden vervangen. In azure-kaarten kunt u een flexibel afgeleide sjabloon maken die gebruikmaakt van TDSP levenscyclus fases om werk items te maken en bij te houden. In de volgende stappen wordt uitgelegd hoe u een Data Science-specifiek Agile-proces sjabloon instelt en gegevens wetenschappen werk items maakt op basis van de sjabloon.

### <a name="set-up-an-agile-data-science-process-template"></a>Een flexibele data Science-proces sjabloon instellen

1. Selecteer in de hoofd pagina van de Azure DevOps-organisatie **organisatie-instellingen** in het linkernavigatievenster. 
   
1. Selecteer in de **organisatie-instellingen** links onder **boards** de optie **proces**. 
   
1. Selecteer in het deel venster **alle processen** de **..** . naast **Agile** en selecteer **overgenomen proces maken**.
   
   ![Overgenomen proces van Agile maken](./media/agile-development/10-settings.png) 
   
1. In het dialoog venster **overgenomen proces van Agile maken** voert u de naam *AgileDataScienceProcess* in en selecteert u **proces maken**.
   
   ![AgileDataScienceProcess-proces maken](./media/agile-development/11-agileds.png)
   
1. In **alle processen** selecteert u de nieuwe **AgileDataScienceProcess**. 
   
1. Schakel op het tabblad **typen werk items** de optie **epische**, **functie**, **gebruikers verhaal** en **taak** uit door de **..** . naast elk item te selecteren en vervolgens **uitschakelen** te selecteren. 
   
   ![Typen werk items uitschakelen](./media/agile-development/12-disable.png)
   
1. Selecteer in **alle processen** het tabblad **achterstand niveaus** . Onder **portefeuilles achterstand** selecteert u de **..** . naast **epische (uitgeschakeld)** en selecteert u **bewerken/naam wijzigen**. 
   
1. In het dialoog venster **achterstallig niveau bewerken** :
   1. Vervang onder **naam** **epische** met *TDSP-projecten*. 
   1. Onder **typen werk items op dit achterstallig niveau** selecteert u **nieuw type werk item**, voert u *TDSP-project* in en selecteert u **toevoegen**. 
   1. Onder **standaard type werk items**, vervolg keuzelijst en selecteer **TDSP-project**. 
   1. Selecteer **Opslaan**.
   
   ![Niveau van de portefeuille achterstand instellen](./media/agile-development/13-rename.png)  
   
1. Volg dezelfde stappen om de namen van **functies** te wijzigen in *TDSP-fasen* en voeg de volgende nieuwe typen werk items toe:
   
   - *Inzicht in het bedrijf*
   - *Gegevens ophalen*
   - *Modelleren*
   - *Implementatie*
   
1. Onder **achterstand** voor de behoefte wijzigt u de naam van **artikelen** in *subfases van TDSP*, voegt u het nieuwe werk item type *TDSP subfase* toe en stelt u het type standaard werk item in op **TDSP subfase**.
   
1. Voeg onder **iteratie achterstand** een nieuwe *TDSP-taak* type werk item toe en stel deze in op het standaard type werk item. 
   
Nadat u de stappen hebt voltooid, moeten de achterstands niveaus er als volgt uitzien:
   
 ![TDSP sjabloon achterstand](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>Flexibele data Science-proces werk items maken

U kunt de data Science-proces sjabloon gebruiken voor het maken van TDSP-projecten en het bijhouden van werk items die overeenkomen met de TDSP levenscyclus fasen.

1. Selecteer **Nieuw project** op de hoofd pagina van de Azure DevOps-organisatie. 
   
1. Geef in het dialoog venster **Nieuw project maken** een naam op voor het project en selecteer vervolgens **Geavanceerd**. 
   
1. Onder **werk item proces**, vervolg keuzelijst en selecteer **AgileDataScienceProcess**, en selecteer vervolgens **maken**.
   
   ![Een TDSP-project maken](./media/agile-development/15-newproject.png)
   
1. In het zojuist gemaakte project selecteert u achterstanden **boards**  >   in de linkernavigatiebalk.
   
1. Als u TDSP-projecten zichtbaar wilt maken, selecteert u het pictogram **team instellingen configureren** . Schakel in het scherm **instellingen** het selectie vakje **projecten TDSP** in en selecteer vervolgens **opslaan en sluiten**.
   
   ![Selectie vakje TDSP projecten selecteren](./media/agile-development/16-enabledsprojects1.png)
   
1. Als u een Data Science-specifiek TDSP-project wilt maken, selecteert u **TDSP projecten** in de bovenste balk en selecteert u vervolgens **nieuw werk item**. 
   
1. Geef in de pop-up het werk item TDSP project een naam en selecteer **toevoegen aan de bovenkant**.
   
   ![Werk item van data Science project maken](./media/agile-development/17-dsworkitems0.png)
   
1. Als u een werk item wilt toevoegen onder het TDSP-project, selecteert u de **+** naast het project en selecteert u vervolgens het type werk item dat u wilt maken. 
   
   ![Type gegevens wetenschappen werk item selecteren](./media/agile-development/17-dsworkitems1.png)
   
1. Vul de details in het nieuwe werk item in en selecteer **opslaan & sluiten**.
   
1. Ga door met het selecteren van de **+** symbolen naast werk items om nieuwe TDSP-fasen, subfases en taken toe te voegen. 
   
Hier volgt een voor beeld van hoe de werk items van het data Science-project moeten worden weer gegeven in de weer gave **achterstand** :

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>Volgende stappen

[Samen werking met git](collaborative-coding-with-git.md) bevat informatie over het ontwikkelen van ontwikkelings code voor data technologie projecten met Git als het ontwikkel raamwerk voor gedeelde code en hoe u deze code ring kunt koppelen aan het werk dat met het flexibele proces is gepland.

Een [voor beeld](walkthroughs.md) van een scenario bevat een lijst met scenario's van specifieke scenario's, met koppelingen en miniaturen. De gekoppelde scenario's illustreren het combi neren van Cloud-en on-premises hulpprogram ma's en services in werk stromen of pijp lijnen om intelligente toepassingen te maken.
  
Aanvullende bronnen voor Agile-processen:

- [Agile-proces](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Typen flexibele verwerking van werk items en werk stroom](/azure/devops/boards/work-items/guidance/agile-process-workflow)

