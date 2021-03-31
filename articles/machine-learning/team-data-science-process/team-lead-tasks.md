---
title: Taken voor de team leider in het team data Science process team
description: Een gedetailleerd overzicht van de taken voor een team lead op een team data Science process team
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: df7d2278487c1b098615a14562c498b9187c56eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96000025"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>Taken voor de team leider van een team data Science process team

In dit artikel worden de taken beschreven die een *team leider* voor hun data Science-Team heeft voltooid. Het doel van het team is een samenwerkings omgeving te maken die wordt gestandaardiseerd op het [team data Science process](overview.md) (TDSP). De TDSP is ontworpen om samen werking en team educatie te verbeteren. 

De TDSP is een flexibele, iteratieve methode voor gegevens wetenschap om efficiënt predictive analytics oplossingen en intelligente toepassingen te leveren. Het proces destilleert de aanbevolen procedures en structuren van micro soft en de branche.  Het doel is de implementatie van data Science-initiatieven te volt ooien en de voor delen van hun Analytics-Program ma's volledig te realiseren. Voor een overzicht van de personeels rollen en de bijbehorende taken voor een Data Science-team dat wordt gestandardization op de TDSP, raadpleegt u [rollen en taken voor team data Science process](roles-tasks.md).

Een team leider beheert een team dat bestaat uit verschillende gegevens wetenschappers in de data Science-eenheid van een onderneming. Afhankelijk van de grootte en structuur van de data Science-eenheid kunnen de [groeps Manager](group-manager-tasks.md) en de team leider dezelfde persoon zijn of hun taken kunnen delegeren naar vervangingen. Maar de taken worden niet gewijzigd. 

In het volgende diagram ziet u de werk stroom voor de taken die de team leider heeft voltooid voor het instellen van een team omgeving:

![Taak werk stroom team lead](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Maak een **team project** in de organisatie van de groep in azure DevOps. 
  
1. Wijzig de naam van de standaard team opslagplaats in **TeamUtilities**.
  
1. Maak een nieuwe **TeamTemplate** -opslag plaats in het team project. 
  
1. Importeer de inhoud van de **GroupUtilities** -en **GroupProjectTemplate** -opslag plaatsen van de groep in de **TeamUtilities** -en **TeamTemplate** -opslag plaatsen. 
  
1. Stel **beveiligings beheer** in door team leden toe te voegen en hun machtigingen te configureren.
  
1. Indien nodig kunt u team gegevens en analyse resources maken:
   - Voeg team-specifieke hulpprogram ma's toe aan de **TeamUtilities** -opslag plaats. 
   - Maak **Azure File Storage** om gegevensassets op te slaan die nuttig kunnen zijn voor het hele team. 
   - Koppel de Azure-bestands opslag aan de **Data Science virtual machine** van de team lead (DSVM) en voeg hieraan gegevensassets toe.

In de volgende zelf studie worden de stappen in detail besproken.

> [!NOTE] 
> In dit artikel wordt gebruikgemaakt van Azure DevOps en een DSVM om een TDSP-team omgeving in te stellen, omdat u hiermee TDSP implementeert bij micro soft. Als uw team gebruikmaakt van andere code hosting-of ontwikkelings platforms, zijn de taken van de team lead hetzelfde, maar de manier om ze te volt ooien, kan afwijken.

## <a name="prerequisites"></a>Vereisten

In deze zelf studie wordt ervan uitgegaan dat de volgende resources en machtigingen zijn ingesteld door de [groeps Manager](group-manager-tasks.md):

- De Azure DevOps **-organisatie** voor uw gegevens eenheid
- **GroupProjectTemplate** -en **GroupUtilities** -opslag plaatsen, gevuld met de inhoud van de opslag plaatsen **ProjectTemplate** en **hulpprogram ma's** van het micro soft TDSP-team
- Machtigingen voor uw organisatie account voor het maken van projecten en opslag plaatsen voor uw team

Om opslag plaatsen te kunnen klonen en hun inhoud op uw lokale machine of DSVM te wijzigen, of Azure file storage in te stellen en te koppelen aan uw DSVM, hebt u het volgende nodig:

- Een Azure-abonnement.
- Git geïnstalleerd op de computer. Als u een DSVM gebruikt, is Git vooraf geïnstalleerd. Anders raadpleegt u de [bijlage platformen en hulpprogram ma's](platforms-and-tools.md#appendix).
- Als u een DSVM wilt gebruiken, wordt de Windows-of Linux-DSVM gemaakt en geconfigureerd in Azure. Zie de [Data Science virtual machine-documentatie](../data-science-virtual-machine/index.yml)voor meer informatie en instructies.
- Voor een Windows DSVM is [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) op uw computer geïnstalleerd. Schuif in het bestand *README.MD* omlaag naar de sectie **downloaden en installeren** en selecteer het **nieuwste installatie programma**. Down load het installatie programma *. exe* op de pagina installatie programma en voer het uit. 
- Voor een Linux-DSVM is een open bare SSH-sleutel ingesteld op uw DSVM en toegevoegd aan Azure DevOps. Zie de sectie **open bare SSH-sleutel maken** in de [bijlage platformen en hulpprogram ma's](platforms-and-tools.md#appendix)voor meer informatie en instructies. 

## <a name="create-a-team-project-and-repositories"></a>Een team project en-opslag plaatsen maken

In deze sectie maakt u de volgende resources in de Azure DevOps-organisatie van uw groep:

- Het **MyTeam** -project in azure DevOps
- De **TeamTemplate** -opslag plaats
- De **TeamUtilities** -opslag plaats

Voor de namen die zijn opgegeven voor de opslag plaatsen en directory's in deze zelf studie wordt ervan uitgegaan dat u een afzonderlijk project wilt maken voor uw eigen team binnen uw grotere data Science-organisatie. De volledige groep kan er echter voor kiezen om te werken onder één project dat door de groeps Manager of de beheerder van de organisatie wordt gemaakt. Vervolgens maken alle data Science-teams opslag plaatsen onder dit ene project. Dit scenario kan geldig zijn voor:
- Een kleine gegevens Science-groep die geen meerdere data Science-teams heeft. 
- Een grotere gegevens Science-groep met meerdere teams voor data technologie die de samen werking tussen teams wil optimaliseren met activiteiten zoals het plannen van Sprint op groepniveau. 

Als teams ervoor kiezen hun team-specifieke opslag plaatsen te hebben onder één groeps project, moeten de team leiders de opslag plaatsen maken met namen als *\<TeamName> sjabloon* en *\<TeamName> hulpprogram ma's*. Bijvoorbeeld: *TeamATemplate* en *TeamAUtilities*. 

In elk geval moeten team leiders hun team leden laten weten welke opslag plaatsen en hulpprogram ma's voor het instellen en klonen van de sjabloon moeten worden gemaakt. Project leiders moeten de [Project Lead taken volgen voor een Data Science-Team](project-lead-tasks.md) om project opslagplaatsen te maken, hetzij onder afzonderlijke projecten of één project. 

### <a name="create-the-myteam-project"></a>Het MyTeam-project maken

Een apart project maken voor uw team:

1. Ga in uw webbrowser naar de start pagina van de Azure DevOps-organisatie van uw groep op URL *https: \/ / \<server name> / \<organization name>* en selecteer **Nieuw project**. 
   
   ![Selecteer Nieuw project](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. Voer in het dialoog venster **project maken** de naam van uw team in, zoals *MyTeam*, onder **project naam** en selecteer **Geavanceerd**. 
   
1. Onder **versie beheer** selecteert u **Git** en klikt u onder **werk item proces** op **Agile**. Selecteer vervolgens **Maken**. 
   
   ![Project maken](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
De pagina **samen vatting** team project wordt geopend met pagina-URL *https: \/ / \<server name> / \<organization name> / \<team name>*.

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>Wijzig de naam van de MyTeam-standaard opslagplaats in TeamUtilities

1. Selecteer op de pagina **MyTeam** project **Summary** onder de service die **u wilt starten met?** de optie **opslag plaatsen**. 
   
   ![Opslag plaatsen selecteren](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. Selecteer op de pagina **MyTeam** opslag plaats de **MyTeam** -opslag plaats boven aan de pagina en selecteer vervolgens opslag plaatsen **beheren** in de vervolg keuzelijst. 
   
   ![Selecteer opslag plaatsen beheren](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. Selecteer op de pagina **project instellingen** de optie **..** . naast de **MyTeam** -opslag plaats en selecteer de **naam van de opslag plaats**. 
   
   ![Naam opslag plaats selecteren](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. Voer in het pop-upvenster **naam van de MyTeam-opslag plaats de** optie *TeamUtilities* in en selecteer **naam wijzigen**. 

### <a name="create-the-teamtemplate-repository"></a>De TeamTemplate-opslag plaats maken

1. Selecteer op de pagina **project instellingen** de optie **nieuwe opslag plaats.** 
   
   ![Nieuwe opslag plaats selecteren](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   Of selecteer **opslag plaatsen** in de linkernavigatiebalk van de pagina **overzicht** van de **MyTeam** -project, selecteer een opslag plaats boven aan de pagina en selecteer vervolgens **nieuwe opslag plaats** in de vervolg keuzelijst.
   
1. Zorg ervoor dat in het dialoog venster **een nieuwe opslag plaats maken** de tekst **Git** is geselecteerd onder **type**. Voer *TeamTemplate* in bij **naam van opslag plaats** en selecteer vervolgens **maken**.
   
   ![Opslag plaats maken](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. Controleer of de twee opslag plaatsen **TeamUtilities** en **TeamTemplate** op de pagina project instellingen worden weer geven. 
   
   ![Twee team opslagplaatsen](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>De inhoud van de groep algemene opslag plaatsen importeren

Als u uw team opslagplaatsen wilt vullen met de inhoud van de groep algemene opslag plaatsen die door uw groeps Manager zijn ingesteld:

1. Selecteer op de start pagina van uw **MyTeam** -project **opslag plaatsen** in het linkernavigatievenster. Als er een bericht wordt weer gegeven dat de **MyTeam** -sjabloon niet wordt gevonden, selecteert u de koppeling in **andere gevallen, gaat u naar de standaard TeamTemplate-opslag plaats.** 
   
   De standaard- **TeamTemplate** -opslag plaats wordt geopend. 
   
1. Selecteer **importeren** op de pagina **TeamTemplate is leeg** . 
   
   ![Import selecteren](./media/team-lead-tasks/import-repo.png)
   
1. In het dialoog venster **een Git-opslag plaats importeren** selecteert u **Git** als **bron type** en voert u de URL in voor de opslag plaats van de gemeen schappelijke sjabloon onder **kloon-URL**. De URL is *https: \/ / \<server name> / \<organization name> /_git/ \<repository name>*. Bijvoorbeeld: *https: \/ /dev.Azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*. 
   
1. Selecteer **Importeren**. De inhoud van de opslag plaats van de groeps sjabloon wordt geïmporteerd in uw team sjabloon opslagplaats. 
   
   ![Opslag plaats algemene sjabloon import groep](./media/team-lead-tasks/import-repo-2.png)
   
1. Klik boven aan de pagina **opslag plaatsen** van het project en selecteer de **TeamUtilities** -opslag plaats.
   
1. Herhaal het import proces om de inhoud van de gemeen schappelijke opslag plaats van de groep, bijvoorbeeld *GroupUtilities*, te importeren in uw **TeamUtilities** -opslag plaats. 
   
Elk van uw twee team opslagplaatsen bevat nu de bestanden uit de bijbehorende groeps-algemene opslag plaats. 

### <a name="customize-the-contents-of-the-team-repositories"></a>De inhoud van de team opslagplaatsen aanpassen

Als u de inhoud van uw team opslagplaatsen wilt aanpassen zodat deze voldoet aan de specifieke behoeften van uw team, kunt u dat nu doen. U kunt bestanden wijzigen, de mapstructuur wijzigen of bestanden en mappen toevoegen.

U kunt bestanden of mappen rechtstreeks in azure DevOps wijzigen, uploaden of maken:

1. Selecteer op de pagina **MyTeam** project **Summary** **opslag plaatsen**. 
   
1. Selecteer boven aan de pagina de opslag plaats die u wilt aanpassen.

1. Navigeer in de opslag plaats-mapstructuur naar de map of het bestand dat u wilt wijzigen. 
   
   - Als u nieuwe mappen of bestanden wilt maken, selecteert u de pijl naast **Nieuw**. 
     
     ![Nieuw bestand maken](./media/team-lead-tasks/new-file.png)
     
   - Als u bestanden wilt uploaden, selecteert u **bestand (en) uploaden**. 
     
     ![Bestanden uploaden](./media/team-lead-tasks/upload-files.png)
     
   - Als u bestaande bestanden wilt bewerken, gaat u naar het bestand en selecteert u vervolgens **bewerken**. 
     
     ![Een bestand bewerken](./media/team-lead-tasks/edit-file.png)
     
1. Nadat u bestanden hebt toegevoegd of bewerkt, selecteert u **door voeren**.
   
   ![Wijzigingen door voeren](./media/team-lead-tasks/commit.png)

Als u wilt werken met opslag plaatsen op uw lokale machine of DSVM, kopieert u de opslag plaatsen of *kloont* u deze naar uw lokale machine en voert u vervolgens uw wijzigingen door en pusht u deze naar de gedeelde team opslagplaatsen, 

Opslag plaatsen klonen:

1. Selecteer op de pagina **MyTeam** project **Summary** **opslag plaatsen** en selecteer boven aan de pagina de opslag plaats die u wilt klonen.
   
1. Selecteer op de pagina opslag plaats rechtsboven **klonen** .
   
1. In het dialoog venster kloon van de **opslag plaats** , onder **opdracht regel**, selecteert u **https** voor een http-verbinding of **SSH** voor een SSH-verbinding en kopieert u de kloon-URL naar het klem bord.
   
   ![Kloon-URL kopiëren](./media/team-lead-tasks/clone.png)
   
1. Maak de volgende mappen op de lokale computer:
   
   - Voor Windows: **C:\GitRepos\MyTeam**
   - Voor Linux, **$Home/gitrepos/myteam** 
   
1. Ga naar de map die u hebt gemaakt.
   
1. Voer in Git-Bash de opdracht uit `git clone <clone URL>` , waarbij de \<clone URL> URL is die u hebt gekopieerd in het dialoog venster **klonen** .
   
   Gebruik bijvoorbeeld een van de volgende opdrachten om de **TeamUtilities** -opslag plaats te klonen naar de *MyTeam* -map op uw lokale computer. 
   
   **HTTPS-verbinding:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH-verbinding:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

Nadat u de gewenste wijzigingen in de lokale kloon van uw opslag plaats hebt aangebracht, moet u de wijzigingen door voeren en pushen naar de gedeelde team opslagplaatsen. 

Voer de volgende Git Bash-opdrachten uit vanuit uw lokale **GitRepos\MyTeam\TeamTemplate** -of **GitRepos\MyTeam\TeamUtilities** -map.

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Als dit de eerste keer is dat u een Git-opslag plaats doorvoert, moet u mogelijk de algemene para meters *User.name* en *User.email* configureren voordat u de `git commit` opdracht uitvoert. Voer de volgende twee opdrachten uit:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Als u een aantal Git-opslag plaatsen wilt door voeren, gebruikt u dezelfde naam en hetzelfde e-mail adres voor alle. Het gebruik van dezelfde naam en hetzelfde e-mail adres is handig bij het bouwen van Power BI-Dash boards voor het bijhouden van uw Git-activiteiten in meerdere opslag plaatsen.

## <a name="add-team-members-and-configure-permissions"></a>Team leden toevoegen en machtigingen configureren

Leden toevoegen aan het team:

1. In azure DevOps selecteert u op de start pagina van het **MyTeam** -project de optie **project instellingen** in het linkernavigatievenster. 
   
1. Selecteer op de pagina **project instellingen** links de optie **teams** en selecteer vervolgens **het** **team MyTeam**. 
   
   ![Teams configureren](./media/team-lead-tasks/teams.png)
   
1. Selecteer op de pagina **team profiel** de optie **toevoegen**.
   
   ![Toevoegen aan MyTeam-team](./media/team-lead-tasks/add-to-team.png)
   
1. In het dialoog venster **gebruikers en groepen toevoegen** zoekt en selecteert u leden om aan de groep toe te voegen en selecteert u vervolgens **wijzigingen opslaan**. 
   
   ![Gebruikers en groepen toevoegen](./media/team-lead-tasks/add-users.png)
   

Machtigingen voor team leden configureren:

1. Selecteer **machtigingen** in de **project instellingen** links in het navigatie venster. 
   
1. Selecteer op de pagina **machtigingen** de groep waaraan u leden wilt toevoegen. 
   
1. Selecteer op de pagina voor die groep de optie **leden** en selecteer vervolgens **toevoegen**. 
   
1. Zoek en selecteer leden in het pop-upvenster **leden** toevoegen aan de groep en selecteer vervolgens **Opslaan**. 
   
   ![Machtigingen verlenen aan leden](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>Team gegevens en analyse resources maken

Deze stap is optioneel, maar het delen van gegevens en analyse resources met het hele team heeft prestaties en kosten voordelen. Team leden kunnen hun projecten uitvoeren op de gedeelde resources, besparen op budgetten en efficiënter samen werken. U kunt Azure File Storage maken en deze koppelen aan uw DSVM om te delen met team leden. 

Zie [platformen en hulpprogram ma's](platforms-and-tools.md)voor meer informatie over het delen van andere resources met uw team, zoals Azure HDInsight Spark clusters. Dit onderwerp bevat richt lijnen van een Data Science-perspectief over het selecteren van resources die geschikt zijn voor uw behoeften, en koppelingen naar product pagina's en andere relevante en nuttige zelf studies.

>[!NOTE]
> Om te voor komen dat gegevens worden verzonden tussen data centers, die traag en kostbaar kunnen zijn, moet u ervoor zorgen dat uw Azure-resource groep, opslag account en DSVM allemaal worden gehost in dezelfde Azure-regio. 

### <a name="create-azure-file-storage"></a>Azure File Storage maken

1. Voer het volgende script uit om Azure File Storage te maken voor gegevensassets die nuttig zijn voor uw hele team. Het script vraagt u om informatie over uw Azure-abonnement, zodat u het kunt invoeren. 

   - Voer het script uit vanaf de Power shell-opdracht prompt op een Windows-computer:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - Voer op een Linux-computer het script uit van de Linux-shell:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. Meld u aan bij uw Microsoft Azure-account wanneer u hierom wordt gevraagd en selecteer het abonnement dat u wilt gebruiken.
   
1. Selecteer het opslag account dat u wilt gebruiken of maak een nieuw item onder het geselecteerde abonnement. U kunt kleine letters, cijfers en afbreek streepjes gebruiken voor de naam van de Azure file storage.
   
1. Om het koppelen en delen van de opslag te vergemakkelijken, drukt u op ENTER of voert u *Y* in om de gegevens van de Azure-bestands opslag op te slaan in een tekst bestand in de huidige map. U kunt dit tekst bestand controleren in uw **TeamTemplate** -opslag plaats, in het ideale geval onder **Docs\DataDictionaries**, zodat alle projecten in uw team er toegang toe hebben. U hebt ook de bestands informatie nodig om uw Azure-bestands opslag te koppelen aan uw Azure-DSVM in de volgende sectie. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>Azure File Storage koppelen op uw lokale machine of DSVM

1. Gebruik het volgende script om uw Azure-bestands opslag te koppelen aan uw lokale machine of DSVM.
   
   - Voer het script uit vanaf de Power shell-opdracht prompt op een Windows-computer:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - Voer op een Linux-computer het script uit van de Linux-shell:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. Druk op ENTER of Voer *Y* in om door te gaan, als u een Azure File Storage-informatie bestand in de vorige stap hebt opgeslagen. Voer het volledige pad en de naam in van het bestand dat u hebt gemaakt. 
   
   Als u geen bestand met informatie over Azure File Storage hebt, voert u *n* in en volgt u de instructies om uw abonnement, Azure-opslag account en Azure File Storage-gegevens in te voeren.
   
1. Voer de naam in van een lokaal of TDSP station om de bestands share op te koppelen. In het scherm wordt een lijst met bestaande stations weer gegeven. Geef een station naam op die nog niet bestaat.
   
1. Controleer of het nieuwe station en de opslag ruimte op de computer zijn gekoppeld.

## <a name="next-steps"></a>Volgende stappen

Hier vindt u koppelingen naar gedetailleerde beschrijvingen van de andere rollen en taken die worden gedefinieerd door het team data Science process:

- [Groeps Manager-taken voor een Data Science-Team](group-manager-tasks.md)
- [Project Lead taken voor een Data Science-Team](project-lead-tasks.md)
- [Individuele Inzender taken projecteren voor een Data Science-Team](project-ic-tasks.md)