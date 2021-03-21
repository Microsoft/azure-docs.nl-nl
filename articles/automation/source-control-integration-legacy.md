---
title: Broncode beheer integratie gebruiken in Azure Automation-verouderd
description: In dit artikel leest u hoe u de integratie van broncode beheer kunt gebruiken.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: 4dedbcf58e76b8c969f8607db6922e87a85f08e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97591870"
---
# <a name="use-source-control-integration-in-azure-automation---legacy"></a>Broncode beheer integratie gebruiken in Azure Automation-verouderd

> [!NOTE]
> Er is een nieuwe ervaring voor broncode beheer. Zie [broncode beheer (preview)](source-control-integration.md)voor meer informatie over de nieuwe ervaring.

Met broncode beheer integratie kunt u runbooks in uw Automation-account koppelen aan een GitHub-bron beheer bibliotheek. Met broncode beheer kunt u eenvoudig samen werken met uw team, wijzigingen bijhouden en terugkeren naar eerdere versies van uw runbooks. Met broncode beheer kunt u bijvoorbeeld verschillende vertakkingen in broncode beheer synchroniseren met uw ontwikkel-, test-of productie Automation-accounts, zodat u gemakkelijk code kunt promo veren die in uw ontwikkel omgeving is getest op uw productie Automation-account.

Met broncode beheer kunt u code pushen van Azure Automation naar broncode beheer of uw runbooks van broncode beheer naar Azure Automation halen. In dit artikel wordt beschreven hoe u broncode beheer instelt in uw Azure Automation omgeving. We beginnen met het configureren van Azure Automation om toegang te krijgen tot uw GitHub-opslag plaats en door verschillende bewerkingen door te lopen die kunnen worden uitgevoerd met broncode beheer integratie. 

> [!NOTE]
> Broncode beheer ondersteunt het ophalen en pushen van [Power shell workflow-runbooks](automation-runbook-types.md#powershell-workflow-runbooks) en [Power shell-runbooks](automation-runbook-types.md#powershell-runbooks). [Grafische runbooks](automation-runbook-types.md#graphical-runbooks) worden nog niet ondersteund.

## <a name="configure-source-control"></a>Broncode beheer configureren

Er zijn twee eenvoudige stappen vereist voor het configureren van broncode beheer voor uw Automation-account en slechts één als u al een GitHub-account hebt. 

### <a name="create-a-github-repository"></a>Een GitHub-opslagplaats maken

Als u al een GitHub-account en een opslag plaats hebt die u wilt koppelen aan Azure Automation, meldt u zich aan bij uw bestaande account en begint u bij stap 2 hieronder. Als dat niet het geval is, gaat u naar [github](https://github.com/), meldt u zich aan voor een nieuw account en [maakt u een nieuwe opslag plaats](https://help.github.com/articles/create-a-repo/).

### <a name="set-up-source-control"></a>Broncode beheer instellen

1. Klik op de pagina Automation-account in de Azure Portal, onder **account instellingen**, op **broncode beheer.**

2. De pagina broncode beheer wordt geopend, waar u uw GitHub-account gegevens kunt configureren. Hieronder volgt de lijst met te configureren parameters:  

   | **Parameter** | **Beschrijving** |
   |:--- |:--- |
   | Bron kiezen |Selecteer de bron. Momenteel wordt alleen **github** ondersteund. |
   | Autorisatie |Klik op de knop **autoriseren** om Azure Automation toegang te verlenen tot uw github-opslag plaats. Als u al bent aangemeld bij uw GitHub-account in een ander venster, worden de referenties van dat account gebruikt. Zodra de autorisatie is geslaagd, wordt op de pagina uw GitHub-gebruikers naam weer gegeven onder **autorisatie-eigenschap**. |
   | Opslag plaats kiezen |Selecteer een GitHub-opslag plaats in de lijst met beschik bare opslag plaatsen. |
   | Vertakking kiezen |Selecteer een vertakking in de lijst met beschik bare branches. Alleen de **hoofd** vertakking wordt weer gegeven als u geen vertakkingen hebt gemaakt. |
   | Pad naar runbookmap |Het pad naar de runbookmap specificeert het pad in de GitHub-opslag plaats van waaruit u de code wilt pushen of ophalen. De waarde moet worden opgegeven in de notatie **/FolderName/subfoldername**. Alleen runbooks in het pad naar de runbookmap worden gesynchroniseerd met uw Automation-account. Runbooks in de submappen van het pad naar de runbookmap worden **niet** gesynchroniseerd. Gebruiken **/** voor het synchroniseren van alle runbooks in de opslag plaats. |
3. Bijvoorbeeld als u een opslag plaats hebt met de naam **PowerShellScripts** die een map bevat met de naam **root folder**, die een map met de **naam submap bevat.** U kunt de volgende teken reeksen gebruiken om elk mapniveau te synchroniseren:

   1. Om runbooks vanuit de **opslag plaats** te synchroniseren, is het pad naar de runbookmap **/** .
   2. Als u runbooks wilt synchroniseren vanuit **root folder**, is het pad naar de runbookmap **/RootFolder**.
   3. Als u runbooks wilt **synchroniseren vanuit een submap,** is het pad naar de runbookmap **/RootFolder/subfolder**.
4. Nadat u de para meters hebt geconfigureerd, worden deze weer gegeven op de pagina broncode beheer instellen.  

    ![De bron beheer pagina met de instellingen](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. Nadat u op **OK** hebt geklikt, wordt broncode beheer integratie nu geconfigureerd voor uw Automation-account en moet deze worden bijgewerkt met uw github-gegevens. U kunt nu op dit onderdeel klikken om al uw bron beheer synchronisatie taak geschiedenis weer te geven.  

    ![Waarden voor de huidige geconfigureerde broncode beheer configuratie](media/source-control-integration-legacy/automation-RepoValues.png)
6. Nadat u broncode beheer hebt ingesteld, worden er twee [variabele assets](./shared-resources/variables.md) in uw Automation-account gemaakt. Daarnaast wordt een geautoriseerde toepassing toegevoegd aan uw GitHub-account.

   * De variabele **micro soft. Azure. Automation. SourceControl. Connection** bevat de waarden van de Connection String, zoals hieronder wordt weer gegeven.  

     | **Parameter** | **Waarde** |
     |:--- |:--- |
     | `Name`  |Micro soft. Azure. Automation. SourceControl. Connection |
     | `Type`  |Tekenreeks |
     | `Value` |{"Branch": \<*Your branch name*> , "RunbookFolderPath": \<*Runbook folder path*> , "ProviderType": \<*has a value 1 for GitHub*> , "opslagplaats": \<*Name of your repository*> , "username": \<*Your GitHub user name*> } |

   * De variabele **micro soft. Azure. Automation. SourceControl. OAuthToken**, bevat de beveiligde versleutelde waarde van uw OAuthToken.  

     |**Parameter**            |**Waarde** |
     |:---|:---|
     | `Name`  | `Microsoft.Azure.Automation.SourceControl.OAuthToken` |
     | `Type`  | `Unknown(Encrypted)` |
     | `Value` | <`Encrypted OAuthToken`> |  

     ![Een venster met bron beheer variabelen](media/source-control-integration-legacy/automation-Variables.png)  

   * **Het besturings element voor automatiserings bronnen** wordt toegevoegd als een geautoriseerde toepassing aan uw github-account. Als u de toepassing wilt weer geven, gaat u op de start pagina van github naar **profiel**  >  **instellingen**  >  **toepassingen**. Met deze toepassing kan Azure Automation uw GitHub-opslag plaats synchroniseren met een Automation-account.  

     ![Toepassings instellingen in GitHub](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="use-source-control-in-automation"></a>Broncode beheer gebruiken in Automation

Met het inchecken van een runbook kunt u de wijzigingen die u hebt aangebracht in een runbook in Azure Automation naar uw opslag plaats voor broncode beheer pushen. Hieronder vindt u de stappen voor het controleren van een runbook:

1. Maak vanuit uw Automation-account [een nieuw tekst runbook](./learn/automation-tutorial-runbook-textual.md)of [Bewerk een bestaand tekst runbook](automation-edit-textual-runbook.md). Dit runbook kan een Power shell-werk stroom of een Power shell-script runbook zijn.  
2. Nadat u het runbook hebt bewerkt, slaat u het op en klikt u op **inchecken** op de pagina bewerken.  

    ![Een venster met de knop inchecken naar GitHub](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > Als u incheckt vanuit Azure Automation, wordt de code die momenteel in uw broncode beheer bestaat, overschreven. De Git-equivalente opdracht regel instructie voor het inchecken is **git add + git commit + git push**  

3. Wanneer u op **check-in** klikt, wordt u gevraagd een bevestigings bericht te klikken. Klik op **Ja** om door te gaan.  

    ![Een dialoog venster waarin de checkin wordt bevestigd voor broncode beheer](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. Als u inschakelt, wordt het broncode beheer-runbook gestart: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Met dit runbook maakt u verbinding met GitHub en worden wijzigingen van Azure Automation naar uw opslag plaats gepusht. Als u de ingecheckte taak geschiedenis wilt weer geven, gaat u terug naar het tabblad **bron beheer integratie** en klikt u op de pagina bibliotheek synchronisatie openen. Op deze pagina worden al uw resource beheer taken weer gegeven. Selecteer de taak die u wilt weer geven en klik om de details weer te geven.  

    ![Een venster met de resultaten van een synchronisatie taak](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > Bron beheer-runbooks zijn speciale Automation-runbooks die u niet kunt weer geven of bewerken. Hoewel ze niet worden weer gegeven in de lijst met runbook, ziet u synchronisatie taken die worden weer gegeven in uw taken lijst.

5. De naam van het gewijzigde runbook wordt verzonden als een invoer parameter voor het ingecheckte runbook. U kunt [de taak details weer geven](automation-runbook-execution.md#job-statuses) door het runbook uit te breiden op de pagina bibliotheek synchronisatie.  

    ![Een venster met de invoer voor een synchronisatie taak](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Vernieuw uw GitHub-opslag plaats nadat de taak is voltooid om de wijzigingen weer te geven.  Er moet een door voer worden vastgelegd in uw opslag plaats met een commit-bericht: de **naam van een *Runbook* is bijgewerkt in azure Automation.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Runbooks van broncode beheer naar Azure Automation synchroniseren

Met de knop synchroniseren op de pagina bibliotheek synchronisatie kunt u alle runbooks in het pad naar de runbookmap van uw opslag plaats ophalen naar uw Automation-account. Dezelfde opslag plaats kan worden gesynchroniseerd met meer dan één Automation-account. Hieronder vindt u de stappen voor het synchroniseren van een runbook:

1. Open in het Automation-account waar u broncode beheer hebt ingesteld de pagina integratie van broncode beheer/bibliotheek synchronisatie en klik op **synchroniseren**.  U wordt gevraagd een bevestigings bericht te klikken. Klik op **Ja** om door te gaan.  

    ![De knop synchroniseren met het bericht bevestigen dat alle runbooks worden gesynchroniseerd](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. Sync Start het runbook  **Sync-MicrosoftAzureAutomationAccountFromGitHubV1** , dat verbinding maakt met github en de wijzigingen van uw opslag plaats naar Azure Automation haalt. Er wordt een nieuwe taak weer geven op de pagina bibliotheek synchronisatie voor deze actie. Als u details over de synchronisatie taak wilt weer geven, klikt u om de pagina taak details te openen.  

    ![Een venster met de synchronisatie resultaten van een synchronisatie taak in een GitHub-opslag plaats](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > Een synchronisatie van broncode beheer overschrijft de concept versie van de runbooks die momenteel aanwezig zijn in uw Automation-account voor **alle** runbooks die momenteel zijn opgenomen in broncode beheer. De Git-equivalente opdracht regel instructie voor synchronisatie is een **git-pull**

![Een venster met alle logboeken van een Suspended resource control Sync-taak](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnect-source-control"></a>Broncode beheer verbreken

Als u de verbinding met uw GitHub-account wilt verbreken, opent u de pagina bibliotheek synchronisatie en klikt u op **verbinding verbreken**. Wanneer u de verbinding met broncode beheer verbreekt, blijven de runbooks die eerder zijn gesynchroniseerd in uw Automation-account, maar wordt de pagina synchronisatie van de bibliotheek niet ingeschakeld.  

  ![Een venster met de knop verbinding verbreken om broncode beheer te verbreken](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Automation: broncode beheer integratie in azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/)voor informatie over het integreren van broncode beheer in azure Automation.  
* Zie [Azure Automation: runbook-bron beheer integreren met Visual Studio online](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)voor informatie over het integreren van runbook-bron beheer met Visual Studio online.  
