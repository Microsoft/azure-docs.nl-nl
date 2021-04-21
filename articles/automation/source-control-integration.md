---
title: Integratie van broncodebeheer in Azure Automation
description: In dit artikel wordt beschreven hoe Azure Automation met andere opslagplaatsen kunt synchroniseren.
services: automation
ms.subservice: process-automation
ms.date: 03/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d94da9792d40a389e3981163e565d85d82a9cdc9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831236"
---
# <a name="use-source-control-integration"></a>Integratie van bronbeheer gebruiken

 Integratie van broncodebeheer in Azure Automation synchronisatie in één richting vanuit uw opslagplaats voor broncodebeheer. Met broncodebeheer kunt u uw runbooks in uw Automation-account up-to-date houden met scripts in uw GitHub- of Azure Repos-opslagplaats voor broncodebeheer. Met deze functie kunt u eenvoudig code die in uw ontwikkelomgeving is getest, promoveren naar uw Automation-productieaccount.

 Met integratie van broncodebeheer kunt u eenvoudig samenwerken met uw team, wijzigingen bijhouden en terugdraaien naar eerdere versies van uw runbooks. Met broncodebeheer kunt u bijvoorbeeld verschillende vertakkingen in broncodebeheer synchroniseren met uw Automation-accounts voor ontwikkeling, tests en productie.

## <a name="source-control-types"></a>Broncodebeheertypen

Azure Automation ondersteunt drie typen broncodebeheer:

* GitHub
* Azure-repos (Git)
* Azure-repos (TFVC)

## <a name="prerequisites"></a>Vereisten

* Een opslagplaats voor broncodebeheer (GitHub of Azure-opslagplaatsen)
* Een [Uitvoeren als-account](automation-security-overview.md#run-as-accounts)
* De [ `AzureRM.Profile` module](/powershell/module/azurerm.profile/) moet worden geïmporteerd in uw Automation-account. Houd er rekening mee dat de equivalente Az-module ( `Az.Accounts` ) niet werkt met Automation-broncodebeheer.

> [!NOTE]
> Synchronisatietaken voor broncodebeheer worden uitgevoerd onder het Automation-account van de gebruiker en worden gefactureerd tegen hetzelfde tarief als andere Automation-taken.

## <a name="configure-source-control"></a>Broncodebeheer configureren

In deze sectie wordt besturingselement voor broncodebeheer voor uw Automation-account geconfigureerd. U kunt de Azure Portal of PowerShell gebruiken.

### <a name="configure-source-control-in-azure-portal"></a>Broncodebeheer configureren in Azure Portal

Gebruik deze procedure om broncodebeheer te configureren met behulp van de Azure Portal.

1. Selecteer broncodebeheer in **uw** Automation-account en klik op **Toevoegen.**

    ![Broncodebeheer selecteren](./media/source-control-integration/select-source-control.png)

2. Kies **Broncodebeheertype** en klik vervolgens op **Verifiëren.**

3. Er wordt een browservenster geopend waarin u wordt gevraagd u aan te melden. Volg de aanwijzingen om de verificatie te voltooien.

4. Gebruik op de pagina Overzicht van broncodebeheer de velden om de hieronder gedefinieerde broncodebeheereigenschappen in te vullen. Klik **op Opslaan** wanneer u klaar bent.

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Naam van broncodebeheer     | Een gebruiksvriendelijke naam voor het broncodebeheer. Deze naam mag alleen letters en cijfers bevatten.        |
    |Broncodebeheertype     | Type broncodebeheermechanisme. De volgende opties zijn beschikbaar:</br> * GitHub</br>* Azure-repos (Git)</br> * Azure-repos (TFVC)        |
    |Opslagplaats     | Naam van de opslagplaats of het project. De eerste 200 opslagplaatsen worden opgehaald. Als u wilt zoeken naar een opslagplaats, typt u de naam in het veld en klikt **u op Zoeken op GitHub.**|
    |Vertakking     | Vertakking van waaruit de bronbestanden moeten worden opgeslagen. Vertakkingstargeting is niet beschikbaar voor het TFVC-broncodebeheertype.          |
    |Mappad     | Map met de runbooks die moeten worden gesynchroniseerd, bijvoorbeeld **/Runbooks.** Alleen runbooks in de opgegeven map worden gesynchroniseerd. Recursie wordt niet ondersteund.        |
    |Automatische synchronisatie<sup>1</sup>     | Instelling voor het in- of uitschakelen van automatische synchronisatie wanneer een doorsturing wordt gemaakt in de opslagplaats voor broncodebeheer.        |
    |Runbook publiceren     | Instelling van Aan als runbooks automatisch worden gepubliceerd na synchronisatie vanuit broncodebeheer en anders Uit.           |
    |Description     | Tekst waarin aanvullende informatie over het broncodebeheer wordt opgegeven.        |

    <sup>1</sup> Als u Automatische synchronisatie wilt inschakelen bij het configureren van de integratie van broncodebeheer met Azure-repos, moet u een projectbeheerder zijn.

   ![Overzicht van broncodebeheer](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> De aanmelding voor uw opslagplaats voor broncodebeheer kan verschillen van uw aanmelding voor de Azure Portal. Zorg ervoor dat u bent aangemeld met het juiste account voor uw opslagplaats voor broncodebeheer bij het configureren van broncodebeheer. Als er twijfels zijn, opent u een nieuw tabblad in uw browser, meld u zich af bij **dev.azure.com**, **visualstudio.com** of **github.com** en probeert u opnieuw verbinding te maken met broncodebeheer.

### <a name="configure-source-control-in-powershell"></a>Broncodebeheer configureren in PowerShell

U kunt ook PowerShell gebruiken om broncodebeheer te configureren in Azure Automation. Als u de PowerShell-cmdlets voor deze bewerking wilt gebruiken, hebt u een persoonlijk toegangs token (PAT) nodig. Gebruik de [cmdlet New-AzAutomationSourceControl](/powershell/module/az.automation/new-azautomationsourcecontrol) om de verbinding met het broncodebeheer te maken. Deze cmdlet vereist een beveiligde tekenreeks voor het PAT. Zie [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring)voor meer informatie over het maken van een beveiligde tekenreeks.

In de volgende subsecties ziet u hoe PowerShell de verbinding voor broncodebeheer maakt voor GitHub, Azure Repos (Git) en Azure Repos (TFVC).

#### <a name="create-source-control-connection-for-github"></a>Broncodebeheerverbinding maken voor GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Broncodebeheerverbinding maken voor Azure-repos (Git)

> [!NOTE]
> Azure Repos (Git) gebruikt een URL die toegang **heeft tot dev.azure.com** in plaats **visualstudio.com**, gebruikt in eerdere indelingen. De oudere `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` URL-indeling is afgeschaft, maar wordt nog steeds ondersteund. De nieuwe indeling heeft de voorkeur.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Broncodebeheerverbinding maken voor Azure Repos (TFVC)

> [!NOTE]
> Azure Repos (TFVC) gebruikt een URL die toegang heeft **tot dev.azure.com** in plaats **van visualstudio.com**, gebruikt in eerdere indelingen. De oudere `https://<accountname>.visualstudio.com/<projectname>/_versionControl` URL-indeling is afgeschaft, maar wordt nog steeds ondersteund. De nieuwe indeling heeft de voorkeur.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Persoonlijke toegang token (PAT)-machtigingen

Voor broncodebeheer zijn enkele minimale machtigingen voor PAT's vereist. De volgende subsecties bevatten de minimale machtigingen die vereist zijn voor GitHub en Azure-opslagplaatsen.

##### <a name="minimum-pat-permissions-for-github"></a>Minimale PAT-machtigingen voor GitHub

De volgende tabel definieert de minimale PAT-machtigingen die vereist zijn voor GitHub. Zie Creating a personal access token for the command line (Een persoonlijk toegang token maken voor de opdrachtregel) voor meer informatie over het maken van een PAT in [GitHub.](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)

|Bereik  |Beschrijving  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Status van toegangs commit         |
|`repo_deployment`      | Implementatiestatus van toegang         |
|`public_repo`     | Toegang tot openbare opslagplaatsen         |
|`repo:invite` | Uitnodigingen voor opslagplaats openen |
|`security_events` | Beveiligingsgebeurtenissen lezen en schrijven |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Hooks voor opslagplaatsen schrijven         |
|`read:repo_hook`|Opslagplaats-hooks lezen|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Minimale PAT-machtigingen voor Azure-repos

De volgende lijst definieert de minimale PAT-machtigingen die vereist zijn voor Azure-repos. Zie Toegang verifiëren met persoonlijke toegangstokens voor meer informatie over het maken van een PAT in Azure [Repos.](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)

| Bereik  |  Toegangstype  |
|---------| ----------|
| `Code`      | Lezen  |
| `Project and team` | Lezen |
| `Identity` | Lezen     |
| `User profile` | Lezen     |
| `Work items` | Lezen    |
| `Service connections` | Lezen, query's uitvoeren,<sup>1 beheren</sup>    |

<sup>1</sup> De `Service connections` machtiging is alleen vereist als u autosync hebt ingeschakeld.

## <a name="synchronize-with-source-control"></a>Synchroniseren met broncodebeheer

Volg deze stappen om te synchroniseren met broncodebeheer.

1. Selecteer de bron in de tabel op de pagina Broncodebeheer.

2. Klik **op Synchronisatie starten** om het synchronisatieproces te starten.

3. Bekijk de status van de huidige synchronisatietaken of eerdere synchronisatietaken door op het **tabblad Synchronisatietaken te** klikken.

4. Selecteer in **de vervolgkeuzelijst** Broncodebeheer een mechanisme voor broncodebeheer.

    ![Synchronisatiestatus](./media/source-control-integration/sync-status.png)

5. Als u op een taak klikt, kunt u de taakuitvoer bekijken. Het volgende voorbeeld is de uitvoer van een synchronisatie-taak voor broncodebeheer.

    ```output
    ===================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzEnvironment.

    Getting AzureRunAsConnection.

    Logging in to Azure...

    Source control information for syncing:

    [Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

    Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

    Connecting to VSTS...

    Source Control Sync Summary:

    2 files synced:
     - ExampleRunbook1.ps1
     - ExampleRunbook2.ps1

    ==================================================================

    ```

6. Aanvullende logboekregistratie is beschikbaar door Alle **logboeken te selecteren** op de pagina Overzicht van synchronisatie van broncodebeheer. Deze aanvullende logboekgegevens kunnen u helpen bij het oplossen van problemen die zich kunnen voordoen bij het gebruik van broncodebeheer.

## <a name="disconnect-source-control"></a>Verbinding met broncodebeheer verbreken

Verbinding met een broncodebeheeropslagplaats verbreken:

1. Open **Broncodebeheer** onder **Accountinstellingen** in uw Automation-account.

2. Selecteer het broncodebeheermechanisme dat u wilt verwijderen.

3. Klik op de pagina Overzicht van broncodebeheer op **Verwijderen.**

## <a name="handle-encoding-issues"></a>Coderingsproblemen afhandelen

Als meerdere personen runbooks in uw broncodebeheeropslagplaats bewerken met behulp van verschillende editors, kunnen er coderingsproblemen optreden. Zie Veelvoorkomende oorzaken van coderingsproblemen voor meer informatie [over deze situatie.](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="update-the-pat"></a>Het PAT bijwerken

Op dit moment kunt u de Azure Portal pat in broncodebeheer bijwerken. Wanneer uw PAT is verlopen of ingetrokken, kunt u broncodebeheer op een van de volgende manieren bijwerken met een nieuw toegang token:

* Gebruik de [REST API](/rest/api/automation/sourcecontrol/update).
* Gebruik de [cmdlet Update-AzAutomationSourceControl.](/powershell/module/az.automation/update-azautomationsourcecontrol)

## <a name="next-steps"></a>Volgende stappen

* Voor het integreren van broncodebeheer in Azure Automation, [Azure Automation: Integratie van broncodebeheer in Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/).  
* Zie voor het integreren van runbookbronbeheer met Visual Studio Codespaces: [Azure Automation: Integrating Runbook Source Control using Visual Studio Codespaces](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/).
