---
title: Broncode beheer in Synapse Studio
description: Meer informatie over het configureren van broncode beheer in azure Synapse Studio
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 1f1a74f3a26a079039e68eb8e59fac4c18ff0c32
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98219739"
---
# <a name="source-control-in-azure-synapse-studio"></a>Broncode beheer in azure Synapse Studio

Standaard worden auteurs van Azure Synapse Studio direct vergeleken met de Synapse-service. Deze ervaring heeft echter de volgende beperkingen:

- Synapse Studio bevat geen tijdelijke opslag voor het opslaan van uw wijzigingen. De enige manier om wijzigingen op te slaan en te delen is via het **publiceren** en alle wijzigingen worden rechtstreeks naar de Synapse-service gepubliceerd.

- Synapse Studio is niet geoptimaliseerd voor samen werking en versie beheer.

Met Synapse Studio kunt u uw werk ruimte koppelen aan een Git-opslag plaats, Azure DevOps of GitHub om de functionaliteit van broncode beheer te bieden. In dit artikel wordt beschreven hoe u een Synapse-werk ruimte configureert en werkt met git-opslag plaats ingeschakeld. En we hebben ook een aantal aanbevolen procedures en een hand leiding voor het oplossen van problemen gemarkeerd.

> [!NOTE]
> Azure Synapse Studio Git-integratie is niet beschikbaar in de Azure Government Cloud.

## <a name="configure-git-repository-in-synapse-studio"></a>Git-opslag plaats configureren in Synapse Studio 

Nadat u uw Synapse Studio hebt gestart, kunt u een Git-opslag plaats in uw werk ruimte configureren. Een Synapse studio-werk ruimte kan slechts worden gekoppeld aan één Git-opslag plaats. 

### <a name="configuration-method-1-global-bar"></a>Configuratie methode 1: algemene balk

Selecteer in de Synapse Studio Global-balk de vervolg keuzelijst **Synapse Live** en selecteer vervolgens **code opslagplaats instellen**.

![De instellingen van de code opslagplaats configureren vanuit ontwerpen](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>Configuratie methode 2: hub beheren

Ga naar de Managed hub van Synapse Studio. Selecteer **Git-configuratie** in de sectie **bron beheer** . Als u geen opslag plaats hebt verbonden, klikt u op **configureren**.

![De instellingen van de code opslagplaats van de Management hub configureren](media/configure-repo-2.png)

> [!NOTE]
> Gebruikers die worden verleend als Inzender voor werk ruimten, eigenaar of hoger niveau kunnen worden geconfigureerd, instellingen bewerken en git-opslag verbreken in azure Synapse Studio 

U kunt verbinding maken met een Azure DevOps-of GitHub Git-opslag plaats in uw werk ruimte.

## <a name="connect-with-azure-devops-git"></a>Verbinding maken met Azure DevOps git 

U kunt een Synapse-werk ruimte koppelen aan een Azure DevOps-opslag plaats voor broncode beheer, samen werking, versies, enzovoort. Als u geen Azure DevOps-opslag plaats hebt, volgt u [deze instructies](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) voor het maken van uw opslag plaats-resources.

### <a name="azure-devops-git-repository-settings"></a>Instellingen voor Azure DevOps Git-opslag plaats

Wanneer u verbinding maakt met uw Git-opslag plaats, selecteert u eerst uw opslagplaats type als Azure DevOps Git en selecteert u vervolgens een Azure AD-Tenant in de vervolg keuzelijst en klikt u op **door gaan**.

![De instellingen voor de code opslagplaats configureren](media/connect-with-azuredevops-repo-selected.png)

In het configuratie venster worden de volgende instellingen voor Azure DevOps Git weer gegeven:

| Instelling | Beschrijving | Waarde |
|:--- |:--- |:--- |
| **Type opslag plaats** | Het type van de Azure opslag plaatsen code-opslag plaats.<br/> | Azure DevOps git of GitHub |
| **Azure Active Directory** | De naam van uw Azure AD-Tenant. | `<your tenant name>` |
| **Azure DevOps-account** | De naam van uw Azure opslag plaatsen-organisatie. U kunt de naam van uw Azure opslag plaatsen-organisatie vinden op `https://{organization name}.visualstudio.com` . U kunt [zich aanmelden bij uw Azure opslag plaatsen-organisatie](https://www.visualstudio.com/team-services/git/) om toegang te krijgen tot uw Visual Studio-profiel en uw opslag plaatsen en projecten te bekijken. | `<your organization name>` |
| **ProjectName** | De naam van uw Azure opslag plaatsen-project. U kunt de naam van uw Azure opslag plaatsen-project vinden op `https://{organization name}.visualstudio.com/{project name}` . | `<your Azure Repos project name>` |
| **Opslagplaats** | De naam van de opslag plaats van uw Azure opslag plaatsen-code. Azure opslag plaatsen-projecten bevatten Git-opslag plaatsen om uw bron code te beheren naarmate uw project groeit. U kunt een nieuwe opslag plaats maken of een bestaande opslag plaats gebruiken die al in uw project voor komt. | `<your Azure Repos code repository name>` |
| **Collaboration Branch** | Uw Azure opslag plaatsen Collaboration-vertakking die wordt gebruikt voor het publiceren. Standaard zijn dit `master` . Wijzig deze instelling als u resources wilt publiceren vanuit een andere vertakking. U kunt bestaande branches selecteren of nieuwe maken | `<your collaboration branch name>` |
| **Hoofdmap** | Uw hoofdmap in uw Azure opslag plaatsen Collaboration-vertakking. | `<your root folder name>` |
| **Bestaande resources importeren in opslag plaats** | Hiermee geeft u op of bestaande resources van de Synapse Studio moeten worden geïmporteerd in een Git-opslag plaats van Azure opslag plaatsen. Schakel het selectie vakje in om uw werkruimte resources (met uitzonde ring van groepen) te importeren in de bijbehorende Git-opslag plaats in JSON-indeling. Deze actie exporteert elke resource afzonderlijk. Als dit selectie vakje niet is ingeschakeld, worden de bestaande resources niet geïmporteerd. | Ingeschakeld (standaard) |
| **Resource importeren in deze vertakking** | Selecteer de vertakking waarin de resources (SQL-script, notebook, Spark-taak definitie, gegevensset, gegevens stroom, enzovoort) worden geïmporteerd. 

U kunt ook een opslagplaats koppeling gebruiken om snel te verwijzen naar de Git-opslag plaats waarmee u verbinding wilt maken. 

### <a name="use-a-different-azure-active-directory-tenant"></a>Een andere Azure Active Directory Tenant gebruiken

De Azure opslag plaatsen Git opslag plaats kan zich in een andere Azure Active Directory Tenant bevindt. Als u een andere Azure AD-tenant wilt opgeven, moet u beheerdersmachtigingen hebben voor het Azure-abonnement dat u gebruikt. Zie de [abonnements beheerder wijzigen](../../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator) voor meer informatie

> [!IMPORTANT]
> Als u verbinding wilt maken met een andere Azure Active Directory, moet de aangemelde gebruiker deel uitmaken van die Active Directory. 

### <a name="use-your-personal-microsoft-account"></a>Uw persoonlijke Microsoft-account gebruiken

Als u een persoonlijk Microsoft-account wilt gebruiken voor git-integratie, kunt u uw persoonlijke Azure-opslag plaats koppelen aan de Active Directory van uw organisatie.

1. Voeg uw persoonlijke Microsoft-account toe aan de Active Directory van uw organisatie als gast. Zie [Azure Active Directory B2B-samenwerkings gebruikers toevoegen in de Azure Portal](../../active-directory/external-identities/add-users-administrator.md)voor meer informatie.

2. Meld u aan bij de Azure Portal met uw persoonlijke Microsoft-account. Schakel vervolgens over naar de Active Directory van uw organisatie.

3. Ga naar de sectie Azure DevOps, waar u nu uw persoonlijke opslag plaats ziet. Selecteer de opslag plaats en maak verbinding met Active Directory.

Na deze configuratie stappen is uw persoonlijke opslag plaats beschikbaar wanneer u Git-integratie instelt in de Synapse Studio.

Zie [verbinding maken tussen uw organisatie en Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)voor meer informatie over het verbinden van Azure opslag plaatsen met de Active Directory van uw organisatie.

## <a name="connect-with-github"></a>Verbinding maken met GitHub 

 U kunt een werk ruimte koppelen aan een GitHub-opslag plaats voor broncode beheer, samen werking, het beheren van versies. Als u geen GitHub-account of-opslag plaats hebt, volgt u [deze instructies](https://github.com/join) om uw resources te maken.

De GitHub-integratie met Synapse Studio ondersteunt zowel open bare GitHub (dat wil zeggen [https://github.com](https://github.com) ) als github-onderneming. U kunt zowel open bare als privé GitHub-opslag plaatsen gebruiken als u lees-en schrijf machtigingen hebt voor de opslag plaats in GitHub.

### <a name="github-settings"></a>GitHub-instellingen

Wanneer u verbinding maakt met uw Git-opslag plaats, selecteert u eerst uw opslagplaats als GitHub en geeft u vervolgens uw GitHub-account of GitHub Enter prise server-URL op als u GitHub Enter prise server gebruikt en klikt u op **door gaan**.

![Instellingen voor GitHub-opslag plaats](media/connect-with-github-repo-1.png)

In het deel venster configuratie worden de volgende instellingen voor de GitHub-opslag plaats weer gegeven:

| **Instelling** | **Beschrijving**  | **Waarde**  |
|:--- |:--- |:--- |
| **Type opslag plaats** | Het type van de Azure opslag plaatsen code-opslag plaats. | GitHub |
| **GitHub Enter prise gebruiken** | Selectie vakje om GitHub Enter prise te selecteren | selectie opheffen (standaard) |
| **GitHub Enter prise-URL** | De GitHub van de Enter prise-basis-URL (moet HTTPS zijn voor lokale GitHub Enter prise-server). Bijvoorbeeld: `https://github.mydomain.com`. Alleen vereist als **use github Enter prise** is geselecteerd | `<your GitHub enterprise url>` |                                                           
| **GitHub-account** | De naam van uw GitHub-account. Deze naam kan worden gevonden vanuit https: \/ /github.com/{account name}/{repository name}. Als u naar deze pagina navigeert, wordt u gevraagd om GitHub OAuth-referenties in te voeren voor uw GitHub-account. | `<your GitHub account name>` |
| **Naam van opslag plaats**  | De naam van de opslag plaats van uw GitHub-code. GitHub-accounts bevatten Git-opslag plaatsen voor het beheren van de bron code. U kunt een nieuwe opslag plaats maken of een bestaande opslag plaats gebruiken die al in uw account is. | `<your repository name>` |
| **Collaboration Branch** | Uw GitHub-samenwerkings vertakking die wordt gebruikt voor het publiceren. Standaard is dit het hoofd. Wijzig deze instelling als u resources wilt publiceren vanuit een andere vertakking. | `<your collaboration branch>` |
| **Hoofdmap** | Uw hoofdmap in uw GitHub-samenwerkings vertakking. |`<your root folder name>` |
| **Bestaande resources importeren in opslag plaats** | Hiermee geeft u op of bestaande resources van de Synapse Studio moeten worden geïmporteerd in een Git-opslag plaats. Schakel het selectie vakje in om uw werkruimte resources (met uitzonde ring van groepen) te importeren in de bijbehorende Git-opslag plaats in JSON-indeling. Deze actie exporteert elke resource afzonderlijk. Als dit selectie vakje niet is ingeschakeld, worden de bestaande resources niet geïmporteerd. | Geselecteerd (standaard) |
| **Resource importeren in deze vertakking** | Selecteer welke vertakking de resources (SQL-script, notebook, Spark-taak definitie, gegevensset, gegevens stroom, enzovoort) worden geïmporteerd. 

### <a name="github-organizations"></a>GitHub organisaties

Als u verbinding wilt maken met een GitHub-organisatie, moet de organisatie toestemming geven voor Synapse Studio. Een gebruiker met beheerders machtigingen voor de organisatie moet de onderstaande stappen uitvoeren.

#### <a name="connecting-to-github-for-the-first-time"></a>Voor de eerste keer verbinding maken met GitHub

Als u voor de eerste keer verbinding maakt met GitHub vanuit Synapse Studio, volgt u deze stappen om verbinding te maken met een GitHub-organisatie.

1. Voer in het deel venster Git-configuratie de naam van de organisatie in het veld *github-account* in. Er verschijnt een prompt om u aan te melden bij GitHub. 

1. Meld u aan met uw gebruikers referenties.

1. U wordt gevraagd om Synapse te autoriseren als een toepassing met de naam *Azure Synapse*. In dit scherm ziet u een optie voor het verlenen van machtigingen voor Synapse om toegang te krijgen tot de organisatie. Als u de optie om toestemming te verlenen niet ziet, vraagt u een beheerder om de machtiging hand matig via GitHub toe te kennen.

Zodra u deze stappen hebt uitgevoerd, kan uw werk ruimte verbinding maken met zowel open bare als privé-opslag plaatsen binnen uw organisatie. Als u geen verbinding kunt maken, probeert u de cache van de browser te wissen en opnieuw te proberen.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Al verbonden met GitHub met een persoonlijk account

Als u al verbinding hebt gemaakt met GitHub en alleen machtigingen hebt verleend voor toegang tot een persoonlijk account, volgt u de onderstaande stappen om machtigingen toe te kennen aan een organisatie.

1. Ga naar GitHub en open **instellingen**.

    ![Instellingen voor GitHub openen](media/github-settings.png)

1. Selecteer **toepassingen**. Op het tabblad **geautoriseerde OAuth-apps** ziet u *Azure Synapse*.

    ![OAuth-apps autoriseren](media/authorize-app.png)

1. Selecteer de *Azure-Synapse* en verleen de toegang tot uw organisatie.

    ![Organisatie machtiging verlenen](media/grant-organization-permission.png)

Zodra u deze stappen hebt voltooid, kan uw werk ruimte verbinding maken met zowel open bare als privé-opslag plaatsen binnen uw organisatie.

## <a name="version-control"></a>Versiebeheer

Met versie besturings systemen (ook wel bekend als _broncode beheer_) kunnen ontwikkel aars samen werken aan code en wijzigingen bijhouden. Broncode beheer is een essentieel hulp programma voor projecten met meerdere ontwikkel aars.

### <a name="creating-feature-branches"></a>Functie vertakkingen maken

Elke Git-opslag plaats die is gekoppeld aan een Synapse Studio heeft een Collaboration Branch. ( `main` of `master` is de standaard vertakking voor samen werking). Gebruikers kunnen ook onderdeel vertakkingen maken door op **+ nieuwe vertakking** in de vervolg keuzelijst vertakking te klikken. Zodra het deel venster nieuwe vertakking wordt weer gegeven, voert u de naam van uw functie vertakking in.

![Een nieuwe vertakking maken](media/create-new-branch.png)

Wanneer u klaar bent om de wijzigingen van uw functie vertakking samen te voegen met uw vertakking voor samen werking, klikt u op de vervolg keuzelijst vertakking en selecteert u **pull-aanvraag maken**. Met deze actie gaat u naar een Git-provider waar u pull-aanvragen kunt genereren, code beoordelingen moet uitvoeren en wijzigingen kunt samen voegen in uw samenwerkings vertakking. U mag alleen publiceren naar de Synapse-service vanuit uw vertakking voor samen werking. 

![Een nieuwe pull-aanvraag maken](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Publicatie-instellingen configureren

Standaard worden in Synapse Studio de werkruimte sjablonen gegenereerd en opgeslagen in een vertakking met de naam `workspace_publish` . Als u een aangepaste publicatie vertakking wilt configureren, voegt `publish_config.json` u een bestand toe aan de hoofdmap in de vertakking voor samen werking. Wanneer het wordt gepubliceerd, wordt dit bestand door Synapse Studio gelezen, wordt gezocht naar het veld `publishBranch` en worden de werkruimte sjabloon bestanden opgeslagen op de opgegeven locatie. Als de vertakking niet bestaat, wordt deze automatisch door Synapse Studio gemaakt. Hieronder ziet u een voor beeld van hoe dit bestand eruitziet:

```json
{
    "publishBranch": "workspace_publish"
}
```

Azure Synapse Studio kan slechts één Publiceer vertakking tegelijk hebben. Wanneer u een nieuwe publicatie vertakking opgeeft, zou de vorige publicatie vertakking niet worden verwijderd. Als u de vorige Publish-vertakking wilt verwijderen, moet u deze hand matig verwijderen.


### <a name="publish-code-changes"></a>Code wijzigingen publiceren

Na het samen voegen van de wijzigingen in de vertakking voor samen werking, klikt u op **publiceren** om de wijzigingen in de code hand matig te publiceren in de vertakking samen werking met de Synapse-service.

![Wijzigingen publiceren](media/gitmode-publish.png)

Er wordt een deel venster geopend waarin u bevestigt dat de publicatie vertakking en in behandeling zijnde wijzigingen juist zijn. Nadat u uw wijzigingen hebt gecontroleerd, klikt u op **OK** om de publicatie te bevestigen.

![De juiste publicatie vertakking bevestigen](media/publish-change.png)

> [!IMPORTANT]
> De samenwerkings vertakking is niet representatief voor wat er in de service is geïmplementeerd. De wijzigingen in de samenwerkings vertakking *moeten* hand matig worden gepubliceerd.

## <a name="switch-to-a-different-git-repository"></a>Overschakelen naar een andere Git-opslag plaats

Als u wilt overschakelen naar een andere Git-opslag plaats, gaat u naar de pagina Git-configuratie in de beheer hub onder **broncode beheer**. Selecteer **verbinding verbreken**. 

![Git-pictogram](media/remove-repository.png)

Voer de naam van uw werk ruimte in en klik op **verbinding verbreken** om de Git-opslag plaats te verwijderen die is gekoppeld aan uw werk

Nadat u de koppeling met de huidige opslag plaats hebt verwijderd, kunt u uw Git-instellingen configureren voor het gebruik van een andere opslag plaats en vervolgens bestaande resources importeren in de nieuwe opslag plaats.

> [!IMPORTANT]
> Het verwijderen van de Git-configuratie uit een werk ruimte verwijdert niets uit de opslag plaats. De Synapse-werk ruimte bevat alle gepubliceerde resources. U kunt de werk ruimte rechtstreeks bewerken met de service.

## <a name="best-practices-for-git-integration"></a>Aanbevolen procedures voor git-integratie

-   **Machtigingen**. Nadat u een Git-opslag plaats hebt verbonden met uw werk ruimte, kan iedereen die toegang heeft tot uw Git-opslag plaats met een wille keurige rol in uw werk ruimte, artefacten, zoals SQL-script, notebook, Spark-taak definitie, gegevensset, gegevens stroom en pijp lijn, in Git-modus bijwerken. Doorgaans wilt u niet dat elk teamlid gemachtigd is om werk ruimte bij te werken. Ken alleen machtigingen voor git-opslag plaats toe voor auteurs van Synapse-werk ruimte artefacten. 
-   **Samen werking**. Het is raadzaam om directe incheckers niet toe te staan voor de vertakking voor samen werking. Deze beperking kan helpen te voor komen dat er fouten optreden bij elke check-in om een pull-aanvraag beoordelings proces te door lopen dat wordt beschreven in [functie vertakkingen maken](source-control.md#creating-feature-branches).
-   **Live-modus Synapse**. Nadat de publicatie in de Git-modus is uitgevoerd, worden alle wijzigingen weer gegeven in de Live-modus Synapse. In de Live-modus van Synapse is publiceren uitgeschakeld. En u kunt artefacten weer geven in de modus Live als u de juiste machtiging hebt gekregen. 
-   **Artefacten bewerken in Studio**. Synapse Studio is de enige plaats waar u het besturings element voor de werk ruimte kunt inschakelen en wijzigingen in Git automatisch wilt synchroniseren. Wijzigingen via SDK, Power shell, worden niet gesynchroniseerd met git. Het is raadzaam om artefacten in Studio altijd te bewerken als Git is ingeschakeld.

## <a name="troubleshooting-git-integration"></a>Problemen met git-integratie oplossen

### <a name="access-to-git-mode"></a>Toegang tot de Git-modus 

Als u de machtiging hebt gekregen voor de GitHub Git-opslag plaats die is gekoppeld aan uw werk ruimte, maar u geen toegang hebt tot de Git-modus: 

1. Wis de cache en vernieuw de pagina. 

1. Meld u aan bij uw GitHub-account.

### <a name="stale-publish-branch"></a>Verouderde publicatie vertakking

Als de publicatie vertakking niet is gesynchroniseerd met de vertakking voor samen werking en verouderde bronnen bevat ondanks een recente publicatie, voert u de volgende stappen uit:

1. Uw huidige Git-opslag plaats verwijderen

1. Configureer Git opnieuw met dezelfde instellingen, maar zorg ervoor dat **bestaande resources importeren in opslag plaats** is ingeschakeld en kies dezelfde vertakking.  

1. Een pull-aanvraag maken om de wijzigingen in de samenwerkingsvertakking samen te voegen 

## <a name="unsupported-features"></a>Niet-ondersteunde functies

- Synapse studio staat geen kers toe voor het verzamelen van door voeringen of selectief publiceren van resources. 
- Synapse Studio biedt geen ondersteuning voor het aanpassen van commit-berichten.
- Op basis van het ontwerp wordt de actie verwijderen in Studio direct doorgevoerd in de git

## <a name="next-steps"></a>Volgende stappen

* Zie [continue integratie en levering (CI/cd)](continuous-integration-deployment.md)voor meer informatie over het implementeren van continue integratie en implementatie.