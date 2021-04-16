---
title: Broncodebeheer in Synapse Studio
description: Informatie over het configureren van broncodebeheer in Azure Synapse Studio
author: liud
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 8f1b459c2644472463004c231f5827ff653d2da1
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567839"
---
# <a name="source-control-in-azure-synapse-studio"></a>Broncodebeheer in Azure Synapse Studio

Standaard worden Azure Synapse Studio-auteurs rechtstreeks tegen de Synapse-service gebruikt. Als u behoefte hebt aan samenwerking met Git voor broncodebeheer, kunt Synapse Studio uw werkruimte koppelen aan een Git-opslagplaats, Azure DevOps of GitHub. 

In dit artikel wordt beschreven hoe u kunt configureren en werken in een Synapse-werkruimte met git-opslagplaats ingeschakeld. Daarnaast worden enkele best practices en een gids voor probleemoplossing belicht.

> [!NOTE]
> Azure Synapse Studio Git-integratie is niet beschikbaar in Azure Government Cloud.

## <a name="configure-git-repository-in-synapse-studio"></a>Git-opslagplaats configureren in Synapse Studio 

Nadat u uw Synapse Studio, kunt u een Git-opslagplaats configureren in uw werkruimte. Een Synapse Studio kan slechts aan één Git-opslagplaats tegelijk worden gekoppeld. 

### <a name="configuration-method-1-global-bar"></a>Configuratiemethode 1: globale balk

Selecteer in Synapse Studio algemene balk de **vervolgkeuzelijst Synapse Live** en selecteer **vervolgens Codeopslagplaats instellen.**

![De instellingen voor de codeopslagplaats configureren vanuit het ontwerp](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>Configuratiemethode 2: Hub beheren

Ga naar de hub Beheren van Synapse Studio. Selecteer **Git-configuratie** in de **sectie Broncodebeheer.** Als u geen opslagplaats hebt verbonden, klikt u op **Configureren.**

![De instellingen voor de codeopslagplaats configureren vanuit de beheerhub](media/configure-repo-2.png)

> [!NOTE]
> Gebruikers die zijn toegewezen als inzender, eigenaar of hoger niveau van de werkruimte kunnen de Git-opslagplaats in Azure Synapse Studio configureren, bewerken en de verbinding met de opslagplaats verbreken 

U kunt verbinding maken met de Git-opslagplaats van Azure DevOps of GitHub in uw werkruimte.

## <a name="connect-with-azure-devops-git"></a>Verbinding maken met Azure DevOps Git 

U kunt een Synapse-werkruimte koppelen aan een Azure DevOps-opslagplaats voor broncodebeheer, samenwerking, versiebeheer, en meer. Als u geen Azure DevOps-opslagplaats hebt, volgt u deze [instructies om](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) uw opslagplaatsbronnen te maken.

### <a name="azure-devops-git-repository-settings"></a>Instellingen voor Git-opslagplaats voor Azure DevOps

Wanneer u verbinding maakt met uw Git-opslagplaats, selecteert u eerst uw opslagplaatstype als Azure DevOps git, selecteert u vervolgens één Azure AD-tenant in de vervolgkeuzelijst en klikt u **op Doorgaan.**

![De instellingen voor de codeopslagplaats configureren](media/connect-with-azuredevops-repo-selected.png)

In het configuratiedeelvenster worden de volgende Git-instellingen van Azure DevOps weergegeven:

| Instelling | Beschrijving | Waarde |
|:--- |:--- |:--- |
| **Type opslagplaats** | Het type codeopslagplaats van Azure Repos.<br/> | Azure DevOps Git of GitHub |
| **Azure Active Directory** | De naam van uw Azure AD-tenant. | `<your tenant name>` |
| **Azure DevOps-account** | De naam van uw Azure-repos-organisatie. U kunt de naam van uw Azure-repos-organisatie vinden op `https://{organization name}.visualstudio.com` . U kunt [zich aanmelden bij uw Azure-opslagplaatsorganisatie](https://www.visualstudio.com/team-services/git/) om toegang te krijgen tot uw Visual Studio en uw opslagplaatsen en projecten te bekijken. | `<your organization name>` |
| **Projectname** | De naam van uw Azure Repos-project. U kunt de naam van uw Azure Repos-project vinden op `https://{organization name}.visualstudio.com/{project name}` . | `<your Azure Repos project name>` |
| **RepositoryName** | De naam van uw Opslagplaats voor Azure-opslagplaatsen. Azure-opslagplaatsprojecten bevatten Git-opslagplaatsen om uw broncode te beheren naarmate uw project groeit. U kunt een nieuwe opslagplaats maken of een bestaande opslagplaats gebruiken die al in uw project staat. | `<your Azure Repos code repository name>` |
| **Samenwerkingstak** | Uw Azure Repos-samenwerkingsvertakking die wordt gebruikt voor publicatie. De standaardwaarde is `master` . Wijzig deze instelling voor het geval u resources wilt publiceren vanuit een andere vertakking. U kunt bestaande vertakkingen selecteren of nieuwe maken | `<your collaboration branch name>` |
| **Hoofdmap** | Uw hoofdmap in de samenwerkingsvertakking van Uw Azure-repos. | `<your root folder name>` |
| **Bestaande resources importeren in de opslagplaats** | Hiermee geeft u op of bestaande resources uit de Synapse Studio in een Git-opslagplaats van Azure Repos. Vink het selectievakje aan om uw werkruimte-resources (met uitzondering van pools) te importeren in de bijbehorende Git-opslagplaats in JSON-indeling. Met deze actie exporteert u elke resource afzonderlijk. Als dit selectievakje niet is ingeschakeld, worden de bestaande resources niet geïmporteerd. | Ingeschakeld (standaard) |
| **Resource importeren in deze vertakking** | Selecteer in welke vertakking de resources (SQL-script, Notebook, Spark-taakdefinitie, gegevensset, gegevensstroom, enzovoort) worden geïmporteerd. 

U kunt ook de koppeling naar de opslagplaats gebruiken om snel te verwijzen naar de Git-opslagplaats waar u verbinding mee wilt maken. 

### <a name="use-a-different-azure-active-directory-tenant"></a>Een andere tenant Azure Active Directory gebruiken

De Git-repo voor Azure-repo's kan zich in een andere Azure Active Directory tenant. Als u een andere Azure AD-tenant wilt opgeven, moet u beheerdersmachtigingen hebben voor het Azure-abonnement dat u gebruikt. Zie Abonnementsbeheerder wijzigen [voor meer informatie](../../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)

> [!IMPORTANT]
> Als u verbinding wilt maken met Azure Active Directory, moet de aangemelde gebruiker deel uitmaken van die Active Directory. 

### <a name="use-your-personal-microsoft-account"></a>Uw persoonlijke Microsoft-account

Als u een persoonlijk Microsoft-account git-integratie wilt gebruiken, kunt u uw persoonlijke Azure-repo koppelen aan de Active Directory van uw organisatie.

1. Voeg uw persoonlijke Microsoft-account als gast toe aan de Active Directory van uw organisatie. Zie Add [Azure Active Directory B2B collaboration users in the Azure Portal (Gebruikers van B2B-samenwerking toevoegen in de Azure Portal) voor meer informatie.](../../active-directory/external-identities/add-users-administrator.md)

2. Meld u aan bij de Azure Portal met uw persoonlijke Microsoft-account. Schakel vervolgens over naar de Active Directory van uw organisatie.

3. Ga naar de sectie Azure DevOps, waar u nu uw persoonlijke repo ziet. Selecteer de repo en maak verbinding met Active Directory.

Na deze configuratiestappen is uw persoonlijke repo beschikbaar wanneer u Git-integratie in de Synapse Studio.

Zie Connect your organization to Azure Active Directory (Uw organisatie verbinden met een Azure Active Directory) voor meer informatie over het verbinden van [Azure-Azure Active Directory.](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)

## <a name="connect-with-github"></a>Verbinding maken met GitHub 

 U kunt een werkruimte koppelen aan een GitHub-opslagplaats voor broncodebeheer, samenwerking en versiebeheer. Als u geen GitHub-account of -opslagplaats hebt, volgt u [deze instructies om](https://github.com/join) uw resources te maken.

De GitHub-integratie Synapse Studio ondersteuning voor zowel openbare GitHub (dat wil [https://github.com](https://github.com) zeggen) als GitHub Enterprise. U kunt zowel openbare als persoonlijke GitHub-opslagplaatsen gebruiken zolang u lees- en schrijfmachtigingen hebt voor de opslagplaats in GitHub.

### <a name="github-settings"></a>GitHub-instellingen

Wanneer u verbinding maakt met uw Git-opslagplaats, selecteert u eerst het type opslagplaats als GitHub en geeft u vervolgens uw GitHub-account of GitHub Enterprise Server-URL op als u GitHub Enterprise Server gebruikt. Klik vervolgens op **Doorgaan.**

![Instellingen voor GitHub-opslagplaats](media/connect-with-github-repo-1.png)

In het configuratiedeelvenster worden de volgende instellingen voor de GitHub-opslagplaats weergegeven:

| **Instelling** | **Beschrijving**  | **Waarde**  |
|:--- |:--- |:--- |
| **Type opslagplaats** | Het type codeopslagplaats van Azure Repos. | GitHub |
| **GitHub Enterprise gebruiken** | Selectievakje om GitHub Enterprise te selecteren | uitgeschakeld (standaard) |
| **GitHub Enterprise-URL** | De GitHub Enterprise-hoofd-URL (moet HTTPS zijn voor lokale GitHub Enterprise-server). Bijvoorbeeld: `https://github.mydomain.com`. Alleen vereist als **GitHub Enterprise** gebruiken is geselecteerd | `<your GitHub enterprise url>` |                                                           
| **GitHub-account** | De naam van uw GitHub-account. Deze naam vindt u op https: \/ /github.com/{account name}/{repository name}. Als u naar deze pagina navigeert, wordt u gevraagd GitHub OAuth-referenties in te voeren voor uw GitHub-account. | `<your GitHub account name>` |
| **Naam van opslagplaats**  | De naam van uw GitHub-codeopslagplaats. GitHub-accounts bevatten Git-opslagplaatsen om uw broncode te beheren. U kunt een nieuwe opslagplaats maken of een bestaande opslagplaats gebruiken die al in uw account staat. | `<your repository name>` |
| **Samenwerkingstak** | Uw GitHub-samenwerkingstak die wordt gebruikt voor publicatie. Standaard is dit de master. Wijzig deze instelling voor het geval u resources wilt publiceren vanuit een andere vertakking. | `<your collaboration branch>` |
| **Hoofdmap** | Uw hoofdmap in uw GitHub-samenwerkingstak. |`<your root folder name>` |
| **Bestaande resources importeren in de opslagplaats** | Hiermee geeft u op of bestaande resources uit de Synapse Studio in een Git-opslagplaats. Vink het selectievakje aan om uw werkruimte-resources (met uitzondering van pools) te importeren in de bijbehorende Git-opslagplaats in JSON-indeling. Met deze actie exporteert u elke resource afzonderlijk. Als dit selectievakje niet is ingeschakeld, worden de bestaande resources niet geïmporteerd. | Geselecteerd (standaard) |
| **Resource importeren in deze vertakking** | Selecteer welke vertakking de resources (SQL-script, Notebook, Spark-taakdefinitie, gegevensset, gegevensstroom, enzovoort) worden geïmporteerd. 

### <a name="github-organizations"></a>GitHub-organisaties

Voor het maken van verbinding met een GitHub-organisatie moet de organisatie machtigingen verlenen aan Synapse Studio. Een gebruiker met BEHEERDERSmachtigingen voor de organisatie moet de onderstaande stappen uitvoeren.

#### <a name="connecting-to-github-for-the-first-time"></a>De eerste keer verbinding maken met GitHub

Als u voor het eerst verbinding maakt met GitHub vanuit Synapse Studio, volgt u deze stappen om verbinding te maken met een GitHub-organisatie.

1. Voer in het deelvenster Git-configuratie de organisatienaam in het *veld GitHub-account* in. Er wordt een prompt weergegeven om u aan te melden bij GitHub. 

1. Meld u aan met uw gebruikersreferenties.

1. U wordt gevraagd Synapse te autoreren als een toepassing met de *naam Azure Synapse*. In dit scherm ziet u een optie voor het verlenen van machtigingen aan Synapse voor toegang tot de organisatie. Als u de optie voor het verlenen van machtigingen niet ziet, vraagt u een beheerder om de machtiging handmatig te verlenen via GitHub.

Nadat u deze stappen hebt doorlopen, kan uw werkruimte verbinding maken met zowel openbare als privé-opslagplaatsen binnen uw organisatie. Als u geen verbinding kunt maken, wist u de browsercache en probeert u het opnieuw.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Al verbonden met GitHub met behulp van een persoonlijk account

Als u al verbinding hebt gemaakt met GitHub en alleen toestemming hebt verleend voor toegang tot een persoonlijk account, volgt u de onderstaande stappen om machtigingen te verlenen aan een organisatie.

1. Ga naar GitHub en open **Instellingen.**

    ![GitHub-instellingen openen](media/github-settings.png)

1. Selecteer **Toepassingen**. Op het **tabblad Geautoriseerde OAuth-apps** ziet u *Azure Synapse.*

    ![OAuth-apps machtigen](media/authorize-app.png)

1. Selecteer de *Azure Synapse* en verleen de toegang tot uw organisatie.

    ![Organisatiemachtigingen verlenen](media/grant-organization-permission.png)

Nadat u deze stappen hebt voltooid, kan uw werkruimte verbinding maken met zowel openbare als privé-opslagplaatsen binnen uw organisatie.

## <a name="version-control"></a>Versiebeheer

Met versiebeheersystemen (ook wel _broncodebeheer genoemd)_ kunnen ontwikkelaars samenwerken aan code en wijzigingen bijhouden. Broncodebeheer is een essentieel hulpprogramma voor projecten voor meerdere ontwikkelaars.

### <a name="creating-feature-branches"></a>Functie-vertakkingen maken

Elke Git-opslagplaats die is gekoppeld aan een Synapse Studio heeft een samenwerkingstak. ( `main` of is de standaard `master` collaboration-vertakking). Gebruikers kunnen ook functievertakkingen maken door te klikken **op + Nieuwe vertakking** in de vervolgkeuzepagina van de vertakking. Zodra het nieuwe vertakkingsdeelvenster wordt weergegeven, voert u de naam van de functie branch in.

![Een nieuwe vertakking maken](media/create-new-branch.png)

Wanneer u klaar bent om de wijzigingen van uw functie-vertakking samen te voegen met uw samenwerkingstak, klikt u op de vervolgkeuzepagina van de vertakking en **selecteert u Pull-aanvraag maken.** Met deze actie gaat u naar de Git-provider waar u pull-aanvragen kunt doen, codebeoordelingen kunt uitvoeren en wijzigingen kunt samenvoegen in uw samenwerkingsvertakking. U mag alleen publiceren naar de Synapse-service vanuit uw samenwerkingsvertakking. 

![Een nieuwe pull-aanvraag maken](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Publicatie-instellingen configureren

Standaard genereert Synapse Studio werkruimtesjablonen en slaat deze op in een vertakking met de naam `workspace_publish` . Als u een aangepaste publicatie branch wilt configureren, voegt u een bestand `publish_config.json` toe aan de hoofdmap in de samenwerkingstak. Wanneer u publiceert, Synapse Studio dit bestand gelezen, zoekt naar het veld en slaat u werkruimtesjabloonbestanden op `publishBranch` de opgegeven locatie op. Als de vertakking niet bestaat, Synapse Studio automatisch een vertakking. Hieronder ziet u een voorbeeld van hoe dit bestand eruitziet:

```json
{
    "publishBranch": "workspace_publish"
}
```

Azure Synapse Studio kan slechts één publicatie-vertakking tegelijk hebben. Wanneer u een nieuwe publicatie-vertakking opgeeft, wordt de vorige publicatie-vertakking niet verwijderd. Als u de vorige publicatie-vertakking wilt verwijderen, verwijdert u deze handmatig.


### <a name="publish-code-changes"></a>Codewijzigingen publiceren

Nadat u wijzigingen in de  samenwerkingsvertakking hebt samengevoegd, klikt u op Publiceren om uw codewijzigingen in de samenwerkingsvertakking handmatig te publiceren naar de Synapse-service.

![Wijzigingen publiceren](media/gitmode-publish.png)

Er wordt een zijdeelvenster geopend waarin u bevestigt dat de wijzigingen in de publicatie-vertakking en in behandeling juist zijn. Zodra u uw wijzigingen hebt gecontroleerd, klikt u **op OK** om de publicatie te bevestigen.

![Bevestig de juiste publicatie-vertakking](media/publish-change.png)

> [!IMPORTANT]
> De samenwerkingsvertakking vertegenwoordigt niet wat er in de service is geïmplementeerd. De wijzigingen in de *samenwerkingsvertakking* moeten handmatig worden gepubliceerd.

## <a name="switch-to-a-different-git-repository"></a>Overschakelen naar een andere Git-opslagplaats

Als u wilt overschakelen naar een andere Git-opslagplaats, gaat u naar de git-configuratiepagina in de beheerhub onder **Broncodebeheer.** Selecteer **Verbinding verbreken.** 

![Git-pictogram](media/remove-repository.png)

Voer de naam van uw werkruimte in en klik **op Verbinding** verbreken om de Git-opslagplaats te verwijderen die aan uw werkruimte is gekoppeld.

Nadat u de associatie met de huidige repo hebt verwijderd, kunt u uw Git-instellingen configureren voor het gebruik van een andere repo en vervolgens bestaande resources importeren in de nieuwe repo.

> [!IMPORTANT]
> Als u de Git-configuratie uit een werkruimte verwijdert, wordt er niets verwijderd uit de opslagplaats. De Synapse-werkruimte bevat alle gepubliceerde resources. U kunt de werkruimte rechtstreeks op de service blijven bewerken.

## <a name="best-practices-for-git-integration"></a>Best practices voor Git-integratie

-   **Machtigingen**. Nadat u een Git-opslagplaats hebt verbonden met uw werkruimte, kan iedereen die toegang heeft tot uw Git-opslagplaats met elke rol in uw werkruimte artefacten bijwerken, zoals sql-script, notebook, spark-taakdefinitie, gegevensset, gegevensstroom en pijplijn in de Git-modus. Normaal gesproken wilt u niet dat elk teamlid machtigingen heeft om de werkruimte bij te werken. Verleen alleen machtigingen voor git-opslagplaatsen aan auteurs van Synapse-werkruimteartefacten. 
-   **Samenwerking**. Het is raadzaam om geen directe check-ins naar de samenwerkingsvertakking toe te staan. Deze beperking kan helpen fouten te voorkomen, omdat bij elke check-in een controleproces voor pull-aanvragen wordt doorlopen dat wordt beschreven in [Functievertakkingen maken.](source-control.md#creating-feature-branches)
-   **Synapse-livemodus**. Na publicatie in de Git-modus worden alle wijzigingen doorgevoerd in de synapse-livemodus. In de Synapse-livemodus is publiceren uitgeschakeld. En u kunt artefacten weergeven en uitvoeren in de livemodus als u de juiste machtiging hebt gekregen. 
-   **Artefacten bewerken in Studio**. Synapse Studio is de enige plek waar u broncodebeheer voor werkruimten kunt inschakelen en automatisch wijzigingen naar Git kunt synchroniseren. Elke wijziging via SDK, PowerShell, wordt niet gesynchroniseerd met Git. We raden u aan artefacten altijd te bewerken in Studio wanneer Git is ingeschakeld.

## <a name="troubleshooting-git-integration"></a>Problemen met Git-integratie oplossen

### <a name="access-to-git-mode"></a>Toegang tot git-modus 

Als u de machtiging hebt gekregen voor de GitHub Git-opslagplaats die is gekoppeld aan uw werkruimte, maar u geen toegang hebt tot de Git-modus: 

1. De cache wissen en de pagina vernieuwen. 

1. Meld u aan bij uw GitHub-account.

### <a name="stale-publish-branch"></a>Verouderde publicatie-vertakking

Als de publicatievertakking niet synchroon is met de samenwerkingsvertakking en ondanks een recente publicatie niet-bijgewerkte resources bevat, volgt u deze stappen:

1. Uw huidige Git-opslagplaats verwijderen

1. Configureer Git opnieuw met dezelfde instellingen, maar zorg ervoor dat Het importeren **van bestaande resources** in de opslagplaats is ingeschakeld en kies dezelfde vertakking.  

1. Een pull-aanvraag maken om de wijzigingen in de samenwerkingsvertakking samen te voegen 

## <a name="unsupported-features"></a>Niet-ondersteunde functies

- Synapse Studio selectief kiezen van door commits of selectief publiceren van resources is niet toegestaan. 
- Synapse Studio biedt geen ondersteuning voor het aanpassen van het commit-bericht.
- De verwijderactie in Studio wordt per ongeluk rechtstreeks in Git vastgelegd

## <a name="next-steps"></a>Volgende stappen

* Zie Continue integratie en levering [(CI/CD)](continuous-integration-deployment.md)voor het implementeren van continue integratie en implementatie.