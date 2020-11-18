---
title: Taken voor groeps beheer voor team data Science process
description: Volg deze gedetailleerde stapsgewijze instructies voor de taken die een groeps Manager uitvoert op een Data Science-Team project.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7ab6d6511d1e2cec82b321003c9d663249ddcf49
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94740223"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Taken voor groeps beheer voor team data Science process

In dit artikel worden de taken beschreven die een *groeps Manager* voor een Data Science-organisatie heeft voltooid. De groeps beheerder beheert de gehele data Science-eenheid in een onderneming. Een Data Science-eenheid kan verschillende teams hebben, die allemaal aan een groot aantal data Science-projecten werken in afzonderlijke zakelijke verticales. Het doel van de groeps beheerder is om een groeps omgeving samen te stellen die wordt gestandaardiseerd op het [team data Science process](overview.md) (TDSP). Voor een overzicht van alle personeels rollen en bijbehorende taken die worden verwerkt door een Data Science-team dat wordt gestandardization op de TDSP, raadpleegt u [rollen en taken voor team data Science process](roles-tasks.md).

In het volgende diagram ziet u de zes installatie taken voor groeps beheer. Groeps beheerders kunnen hun taken overdragen aan vervangingen, maar de taken die aan de rol zijn gekoppeld, worden niet gewijzigd.

![Taken voor groeps beheer](./media/group-manager-tasks/tdsp-group-manager.png)

1. Stel een **Azure DevOps-organisatie** in voor de groep.
2. Maak het standaard **GroupCommon-project** in de Azure DevOps-organisatie.
3. Maak de **GroupProjectTemplate** -opslag plaats in azure opslag plaatsen.
4. Maak de **GroupUtilities** -opslag plaats in azure opslag plaatsen.
5. Importeer de inhoud van de TDSP- **ProjectTemplate** en- **hulpprogram Ma's** van het micro soft-team in de groep algemene opslag plaatsen.
6. Stel **lidmaatschap** en **machtigingen** voor team leden in voor toegang tot de groep.

In de volgende zelf studie worden de stappen in detail besproken. 

> [!NOTE] 
> In dit artikel wordt gebruikgemaakt van Azure DevOps om een TDSP-groeps omgeving in te stellen, omdat dat gaat om TDSP te implementeren bij micro soft. Als uw groep gebruikmaakt van andere code hosting-of ontwikkelings platformen, zijn de taken van de groeps beheerder hetzelfde, maar de manier om deze te volt ooien, kan afwijken.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Een organisatie en project maken in azure DevOps

1. Ga naar [VisualStudio.Microsoft.com](https://visualstudio.microsoft.com), selecteer **in** de rechter bovenhoek aanmelden en meld u aan bij uw Microsoft-account. 
   
   ![Aanmelden bij uw Microsoft-account](./media/group-manager-tasks/signinvs.png)
   
   Als u geen Microsoft-account hebt, selecteert u **nu registreren**, maakt u een Microsoft-account en meldt u zich aan met dit account. Als uw organisatie een Visual Studio-abonnement heeft, meldt u zich aan met de referenties voor dat abonnement.
   
1. Nadat u zich hebt aangemeld, selecteert u in de rechter bovenhoek van de Azure DevOps **-pagina nieuwe organisatie maken**.
   
   ![Nieuwe organisatie maken](./media/group-manager-tasks/create-organization.png)
   
1. Als u wordt gevraagd om akkoord te gaan met de service voorwaarden, de privacyverklaring en de gedrags code, selecteert u **continue**.
   
1. Geef in het dialoog venster voor aanmelding uw Azure DevOps-organisatie een naam en accepteer de toewijzing van de hostgebied en selecteer een andere regio. Selecteer vervolgens **Doorgaan**. 

1. Voer onder **een project maken om aan de slag te gaan**, *GroupCommon* in en selecteer vervolgens **project maken**. 
   
   ![Project maken](./media/group-manager-tasks/create-project.png)

De pagina **samen vatting** van **GroupCommon** -project wordt geopend. De URL van de pagina is *https: \/ / \<servername> / \<organization-name> /GroupCommon*.

![Pagina project overzicht](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>De algemene opslag plaatsen voor groepen instellen

Azure opslag plaatsen fungeert als host voor de volgende typen opslag plaatsen voor uw groep:

- **Algemene opslag** plaatsen: opslag plaatsen voor algemeen gebruik die meerdere teams binnen een Data Science-eenheid kunnen aannemen voor veel data Science-projecten. 
- **Team opslagplaatsen**: opslag plaatsen voor specifieke teams binnen een Data Science-eenheid. Deze opslag plaatsen zijn specifiek voor de behoeften van een team en kunnen worden gebruikt voor meerdere projecten binnen dat team, maar zijn niet algemeen genoeg om te worden gebruikt in meerdere teams binnen een Data Science-eenheid.
- **Project opslagplaatsen**: opslag plaatsen voor specifieke projecten. Dergelijke opslag plaatsen zijn mogelijk niet algemeen genoeg voor meerdere projecten binnen een team of voor andere teams in een Data Science-eenheid.

Als u de algemene opslag plaatsen voor groepen in uw project wilt instellen, kunt u het volgende doen: 
- Wijzig de naam van de standaard **GroupCommon** -opslag plaats in **GroupProjectTemplate**
- Een nieuwe **GroupUtilities** -opslag plaats maken

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>De naam van de standaard project opslagplaats wijzigen in GroupProjectTemplate

De naam van de standaard **GroupCommon** -project opslagplaats wijzigen in **GroupProjectTemplate**:

1. Selecteer op de pagina **GroupCommon** project **Summary** **opslag plaatsen**. Met deze actie gaat u naar de standaard **GroupCommon** -opslag plaats van het GroupCommon-project, dat momenteel leeg is.
   
1. Klik boven aan de pagina op de pijl naast **GroupCommon** en selecteer **opslag plaatsen beheren**.
   
   ![Opslag plaatsen beheren](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. Selecteer op de pagina **project instellingen** de optie **..** . naast **GroupCommon** en selecteer vervolgens **opslag locatie naam wijzigen**. 
   
   ![Selecteren... en selecteer vervolgens bibliotheek naam wijzigen](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. Voer in het pop-upvenster **naam van de GroupCommon-opslag plaats de** optie *GroupProjectTemplate* in en selecteer **naam wijzigen**. 
   
   ![Naam van opslag plaats wijzigen](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>De GroupUtilities-opslag plaats maken

De **GroupUtilities** -opslag plaats maken:

1. Selecteer op de pagina **GroupCommon** project **Summary** **opslag plaatsen**. 
   
1. Klik boven aan de pagina op de pijl naast **GroupProjectTemplate** en selecteer **nieuwe opslag plaats**.
   
   ![Nieuwe opslag plaats selecteren](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. In het dialoog venster **een nieuwe opslag plaats maken** selecteert u **Git** als het **type**, voert u *GroupUtilities* in als de naam van de **opslag plaats** en selecteert u **maken**.
   
   ![GroupUtilities-opslag plaats maken](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. Selecteer op de pagina **project instellingen** de optie **opslag** plaatsen onder **opslag plaatsen** in de linkernavigatiebalk om de twee groeps opslagplaatsen te zien: **GroupProjectTemplate** en **GroupUtilities**.
   
   ![Twee groeps opslagplaatsen](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Importeer de micro soft TDSP-team opslagplaatsen

In dit deel van de zelf studie importeert u de inhoud van de opslag plaatsen **ProjectTemplate** en **hulpprogram ma's** die worden beheerd door het micro soft TDSP-team in uw **GroupProjectTemplate** -en **GroupUtilities** -opslag plaatsen. 

De TDSP-team opslagplaatsen importeren:

1. Selecteer op de start pagina van **GroupCommon** -project **opslag plaatsen** in het linkernavigatievenster. De standaard **GroupProjectTemplate** opslag plaats wordt geopend. 
   
1. Selecteer **importeren** op de pagina **GroupProjectTemplate is leeg** . 
   
   ![Import selecteren](./media/group-manager-tasks/import-repo.png)
   
1. In het dialoog venster **een Git-opslag plaats importeren** selecteert u **Git** als **bron type** en voert u *https: \/ /github.com/azure/Azure-TDSP-ProjectTemplate.git* in voor de **kloon-URL**. Selecteer vervolgens **importeren**. De inhoud van de ProjectTemplate-opslag plaats van micro soft TDSP team wordt geïmporteerd in uw GroupProjectTemplate-opslag plaats. 
   
   ![Micro soft TDSP-team opslagplaats importeren](./media/group-manager-tasks/import-repo-2.png)
   
1. Klik boven aan de pagina **opslag plaatsen** op de **GroupUtilities** -opslag plaats.
   
Elk van de twee groeps opslagplaatsen bevat nu alle bestanden, behalve die in de map *. git* , van de bijbehorende opslag plaats van het micro soft TDSP-team. 

## <a name="customize-the-contents-of-the-group-repositories"></a>De inhoud van de groeps opslagplaatsen aanpassen

Als u de inhoud van uw groeps opslagplaatsen wilt aanpassen om te voldoen aan de specifieke behoeften van uw groep, kunt u dat nu doen. U kunt de bestanden wijzigen, de mapstructuur wijzigen of bestanden toevoegen die uw groep heeft ontwikkeld of die handig zijn voor uw groep.

### <a name="make-changes-in-azure-repos"></a>Wijzigingen aanbrengen in azure opslag plaatsen

De inhoud van de opslag plaats aanpassen:

1. Selecteer op de pagina **GroupCommon** project **Summary** **opslag plaatsen**. 
   
1. Selecteer boven aan de pagina de opslag plaats die u wilt aanpassen.

1. Navigeer in de opslag plaats-mapstructuur naar de map of het bestand dat u wilt wijzigen. 
   
   - Als u nieuwe mappen of bestanden wilt maken, selecteert u de pijl naast **Nieuw**. 
     
     ![Nieuw bestand maken](./media/group-manager-tasks/new-file.png)
     
   - Als u bestanden wilt uploaden, selecteert u **bestand (en) uploaden**. 
     
     ![Bestanden uploaden](./media/group-manager-tasks/upload-files.png)
     
   - Als u bestaande bestanden wilt bewerken, gaat u naar het bestand en selecteert u vervolgens **bewerken**. 
     
     ![Een bestand bewerken](./media/group-manager-tasks/edit-file.png)
     
1. Nadat u bestanden hebt toegevoegd of bewerkt, selecteert u **door voeren**.
   
   ![Wijzigingen door voeren](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Wijzigingen aanbrengen met behulp van uw lokale machine of DSVM

Als u wijzigingen wilt aanbrengen met behulp van uw lokale machine of DSVM en de wijzigingen naar de groeps opslagplaatsen wilt pushen, moet u ervoor zorgen dat u beschikt over de vereisten voor het werken met Git en Dsvm:

- Een Azure-abonnement als u een DSVM wilt maken.
- Git geïnstalleerd op de computer. Als u een DSVM gebruikt, is Git vooraf geïnstalleerd. Anders raadpleegt u de [bijlage platformen en hulpprogram ma's](platforms-and-tools.md#appendix).
- Als u een DSVM wilt gebruiken, wordt de Windows-of Linux-DSVM gemaakt en geconfigureerd in Azure. Zie de [Data Science virtual machine-documentatie](../data-science-virtual-machine/index.yml)voor meer informatie en instructies.
- Voor een Windows DSVM is [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) op uw computer geïnstalleerd. Schuif in het bestand *README.MD* omlaag naar de sectie **downloaden en installeren** en selecteer het **nieuwste installatie programma**. Down load het installatie programma *. exe* op de pagina installatie programma en voer het uit. 
- Voor een Linux-DSVM is een open bare SSH-sleutel ingesteld op uw DSVM en toegevoegd aan Azure DevOps. Zie de sectie **open bare SSH-sleutel maken** in de [bijlage platformen en hulpprogram ma's](platforms-and-tools.md#appendix)voor meer informatie en instructies. 

Kopieer of *kloon* eerst de opslag plaats naar uw lokale machine. 
   
1. Selecteer op de pagina **GroupCommon** project **Summary** **opslag plaatsen** en selecteer boven aan de pagina de opslag plaats die u wilt klonen.
   
1. Selecteer op de pagina opslag plaats rechtsboven **klonen** .
   
1. In het dialoog venster kloon van de **opslag plaats** selecteert u **https** voor een http-verbinding of **SSH** voor een SSH-verbinding en kopieert u de kloon-URL onder de **opdracht regel** naar het klem bord.
   
   ![Opslagplaats klonen](./media/group-manager-tasks/clone.png)
   
1. Maak de volgende mappen op de lokale computer:
   
   - Voor Windows: **C:\GitRepos\GroupCommon**
   - Voor Linux, **$/GitRepos/GroupCommon** in uw basismap 
   
1. Ga naar de map die u hebt gemaakt.
   
1. Voer de opdracht in Git Bash uit. `git clone <clone URL>.`
   
   Een van de volgende opdrachten kloont bijvoorbeeld de **GroupUtilities** -opslag plaats naar de *GroupCommon* -map op uw lokale computer. 
   
   **HTTPS-verbinding:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **SSH-verbinding:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

Nadat u alle gewenste wijzigingen in de lokale kloon van uw opslag plaats hebt aangebracht, kunt u de wijzigingen naar de algemene opslag plaatsen van de gedeelde groep pushen. 

Voer de volgende Git Bash-opdrachten uit vanuit uw lokale **GroupProjectTemplate** -of **GroupUtilities** -map.

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Als dit de eerste keer is dat u een Git-opslag plaats doorvoert, moet u mogelijk de algemene para meters *User.name* en *User. email* configureren voordat u de `git commit` opdracht uitvoert. Voer de volgende twee opdrachten uit:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Als u een aantal Git-opslag plaatsen wilt door voeren, gebruikt u dezelfde naam en hetzelfde e-mail adres voor alle. Het gebruik van dezelfde naam en hetzelfde e-mail adres is handig bij het bouwen van Power BI-Dash boards voor het bijhouden van uw Git-activiteiten in meerdere opslag plaatsen.

## <a name="add-group-members-and-configure-permissions"></a>Groeps leden toevoegen en machtigingen configureren

Leden toevoegen aan de groep:

1. In azure DevOps selecteert u op de start pagina van het **GroupCommon** -project de optie **project instellingen** in het linkernavigatievenster. 
   
1. Selecteer op de pagina **project instellingen** links de optie **teams** en selecteer vervolgens **het** **team GroupCommon**. 
   
   ![Teams configureren](./media/group-manager-tasks/teams.png)
   
1. Selecteer op de pagina **team profiel** de optie **toevoegen**.
   
   ![Toevoegen aan GroupCommon-team](./media/group-manager-tasks/add-to-team.png)
   
1. In het dialoog venster **gebruikers en groepen toevoegen** zoekt en selecteert u leden om aan de groep toe te voegen en selecteert u vervolgens **wijzigingen opslaan**. 
   
   ![Gebruikers en groepen toevoegen](./media/group-manager-tasks/add-users.png)
   

Machtigingen voor leden configureren:

1. Selecteer **machtigingen** in de **project instellingen** links in het navigatie venster. 
   
1. Selecteer op de pagina **machtigingen** de groep waaraan u leden wilt toevoegen. 
   
1. Selecteer op de pagina voor die groep de optie **leden** en selecteer vervolgens **toevoegen**. 
   
1. Zoek en selecteer leden in het pop-upvenster **leden** toevoegen aan de groep en selecteer vervolgens **Opslaan**. 
   
   ![Machtigingen verlenen aan leden](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>Volgende stappen

Hier vindt u koppelingen naar gedetailleerde beschrijvingen van de andere rollen en taken in het team data Science process:

- [Team lead taken voor een Data Science-Team](team-lead-tasks.md)
- [Project Lead taken voor een Data Science-Team](project-lead-tasks.md)
- [Individuele Inzender taken projecteren voor een Data Science-Team](project-ic-tasks.md)
