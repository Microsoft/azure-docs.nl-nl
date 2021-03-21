---
title: Azure Automation runbooks en-modules gebruiken in PowerShell Gallery
description: In dit artikel leest u hoe u runbooks en modules van micro soft en de community in PowerShell Gallery kunt gebruiken.
services: automation
ms.subservice: process-automation
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: c38a6236fe3ad9164d11d94e5563a7dddf5b4b32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452778"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>Runbooks en modules gebruiken in PowerShell Gallery

In plaats van uw eigen runbooks en modules te maken in Azure Automation, hebt u toegang tot scenario's die al zijn gemaakt door micro soft en de community. U kunt Power shell-runbooks en- [modules](#modules-in-powershell-gallery) ophalen uit de PowerShell Gallery-en [python-runbooks](#use-python-runbooks) van de Azure Automation github-organisatie. U kunt ook bijdragen aan de community door scenario's te delen [die u ontwikkelt](#add-a-powershell-runbook-to-the-gallery).

> [!NOTE]
> Het TechNet-Script centrum wordt buiten gebruik gesteld. Alle runbooks uit het script centrum in de Runbook Gallery zijn verplaatst naar onze [Automation github-organisatie](https://github.com/azureautomation) . Zie [hier](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)voor meer informatie.

## <a name="runbooks-in-powershell-gallery"></a>Runbooks in PowerShell Gallery

De [PowerShell Gallery](https://www.powershellgallery.com/packages) biedt verschillende Runbooks van micro soft en de community die u in azure Automation kunt importeren. Als u één wilt gebruiken, moet u een runbook downloaden uit de galerie of u kunt vanuit de galerie of vanuit uw Automation-account rechtstreeks runbooks importeren in de Azure Portal.

> [!NOTE]
> Grafische runbooks worden niet ondersteund in PowerShell Gallery.

U kunt alleen rechtstreeks importeren vanuit de PowerShell Gallery met behulp van de Azure Portal. U kunt deze functie niet uitvoeren met Power shell.

> [!NOTE]
> U moet de inhoud van alle runbooks valideren die u van de PowerShell Gallery ontvangt en de meeste voorzichtigheid gebruiken om ze te installeren en uit te voeren in een productie omgeving.

## <a name="modules-in-powershell-gallery"></a>Modules in PowerShell Gallery

Power shell-modules bevatten cmdlets die u in uw runbooks kunt gebruiken. Bestaande modules die u in Azure Automation kunt installeren, zijn beschikbaar in de [PowerShell Gallery](https://www.powershellgallery.com). U kunt deze galerie starten vanuit het Azure Portal en de modules rechtstreeks installeren in Azure Automation, of u kunt ze hand matig downloaden en installeren.

## <a name="common-scenarios-available-in-powershell-gallery"></a>Algemene scenario's die beschikbaar zijn in PowerShell Gallery

De onderstaande lijst bevat enkele runbooks die veelvoorkomende scenario's ondersteunen. Zie [AzureAutomationTeam-profiel](https://www.powershellgallery.com/profiles/AzureAutomationTeam)voor een volledige lijst met runbooks die zijn gemaakt door het Azure Automation team.

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) : Hiermee wordt de nieuwste versie van alle modules in een Automation-account uit PowerShell Gallery geïmporteerd.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) : configureert Azure Diagnostics en Log Analytics om Azure Automation logboeken te ontvangen met taak status en taak stromen.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) : kopieert een extern bestand van een virtuele Windows Azure-machine.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) : kopieert een lokaal bestand naar een virtuele machine van Azure.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Een Power shell-runbook importeren vanuit de runbook Gallery met de Azure Portal

1. Open uw Automation-account in Azure Portal.
1. Selecteer **Runbooks galerie** onder **proces automatisering**.
1. **Bron selecteren: PowerShell Gallery**. Hier ziet u een lijst met beschik bare runbooks die u kunt bekijken.
1. U kunt het zoekvak boven de lijst gebruiken om de lijst te beperken, of u kunt de filters gebruiken om de weer gave te beperken op uitgever, type en sorteren. Zoek het gewenste galerij-item en selecteer dit om de details ervan weer te geven.

   :::image type="content" source="media/automation-runbook-gallery/browse-gallery-sm.png" alt-text="Door de runbook Gallery bladeren." lightbox="media/automation-runbook-gallery/browse-gallery-lg.png":::

1. Als u een item wilt importeren, klikt u op **importeren** op de Blade Details.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-detail-sm.png" alt-text="Details van een runbook Gallery-item weer geven." lightbox="media/automation-runbook-gallery/gallery-item-detail-lg.png":::

1. Wijzig desgewenst de naam van het runbook en klik vervolgens op **OK** om het runbook te importeren.
1. Het runbook wordt weer gegeven op het tabblad **Runbooks** voor het Automation-account.

## <a name="import-a--powershell-runbook-from-github-with-the-azure-portal"></a>Een Power shell-runbook importeren uit GitHub met de Azure Portal

1. Open uw Automation-account in Azure Portal.
1. Selecteer **Runbooks galerie** onder **proces automatisering**.
1. Selecteer **Bron: github**.
1. U kunt de filters boven de lijst gebruiken om de weer gave te beperken op uitgever, type en sorteren. Zoek het gewenste galerij-item en selecteer dit om de details ervan weer te geven.

   :::image type="content" source="media/automation-runbook-gallery/browse-gallery-github-sm.png" alt-text="Bladeren in de GitHub-galerie." lightbox="media/automation-runbook-gallery/browse-gallery-github-lg.png":::

1. Als u een item wilt importeren, klikt u op **importeren** op de Blade Details.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-github-sm.png" alt-text="Gedetailleerde weer gave van een runbook in de GitHub-galerie." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-github-lg.png":::

1. Wijzig desgewenst de naam van het runbook en klik vervolgens op **OK** om het runbook te importeren.
1. Het runbook wordt weer gegeven op het tabblad **Runbooks** voor het Automation-account.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Een Power shell-runbook toevoegen aan de galerie

Micro soft raadt u aan runbooks toe te voegen aan de PowerShell Gallery die u nuttig vindt voor andere klanten. De PowerShell Gallery accepteert Power shell-modules en Power shell-scripts. U kunt een runbook toevoegen door [het te uploaden naar de PowerShell Gallery](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Een module importeren vanuit de module galerie met de Azure Portal

1. Open uw Automation-account in Azure Portal.
1. Selecteer **modules** onder **gedeelde bronnen** om de lijst met modules te openen.
1. Klik op **Bladeren in Galerie** boven aan de pagina.

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="Weer gave van de module galerie." lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. Op de pagina Blade galerie kunt u het zoekvak gebruiken om overeenkomsten te vinden in een van de volgende velden:

   * Modulenaam
   * Tags
   * Auteur
   * Cmdlet/DSC-resource naam

1. Zoek een module waarin u bent geïnteresseerd en selecteer deze om de details ervan weer te geven.

   Als u een specifieke module inzoomt, kunt u meer informatie weer geven. Deze informatie omvat een koppeling terug naar de PowerShell Gallery, eventueel vereiste afhankelijkheden en alle cmdlets of DSC-resources die de module bevat.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-sm.png" alt-text="Gedetailleerde weer gave van een module in de galerie." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-lg.png":::

1. Als u de module rechtstreeks in Azure Automation wilt installeren, klikt u op **importeren**.
1. In het deel venster importeren ziet u de naam van de module die u wilt importeren. Als alle afhankelijkheden zijn geïnstalleerd, wordt de knop **OK** geactiveerd. Als er afhankelijkheden ontbreken, moet u deze afhankelijkheden importeren voordat u deze module kunt importeren.
1. Klik in het deel venster importeren op **OK** om de module te importeren. Terwijl Azure Automation een module in uw account importeert, worden de meta gegevens van de module en de cmdlets opgehaald. Deze actie kan enkele minuten duren omdat elke activiteit moet worden geëxtraheerd.
1. U ontvangt een eerste melding dat de module wordt geïmplementeerd en een andere melding wanneer deze is voltooid.
1. Nadat de module is geïmporteerd, kunt u de beschik bare activiteiten bekijken. U kunt module resources gebruiken in uw runbooks en DSC-resources.

> [!NOTE]
> Modules die alleen Power shell core ondersteunen, worden niet ondersteund in Azure Automation en kunnen niet worden geïmporteerd in de Azure Portal, of rechtstreeks vanuit de PowerShell Gallery worden geïmplementeerd.

## <a name="use-python-runbooks"></a>Python-runbooks gebruiken

Python-Runbooks zijn beschikbaar in de [Azure Automation github-organisatie](https://github.com/azureautomation). Wanneer u een bijdrage levert aan onze GitHub-opslag plaats, voegt u het tag **(github-onderwerp) toe: Python3** wanneer u uw contributie uploadt.

## <a name="request-a-runbook-or-module"></a>Een runbook of module aanvragen

U kunt aanvragen verzenden naar de stem van de [gebruiker](https://feedback.azure.com/forums/246290-azure-automation/).  Als u hulp nodig hebt bij het schrijven van een runbook of als u een vraag hebt over Power shell, plaatst u een vraag op onze vraag [pagina van micro soft Q&](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie: een Power shell-Runbook maken](learn/automation-tutorial-runbook-textual-powershell.md)om aan de slag te gaan met een Power shell-runbook.
* Zie [Runbooks beheren in azure Automation](manage-runbooks.md)voor het werken met runbooks.
* Zie [Power shell docs](/powershell/scripting/overview)(Engelstalig) voor meer informatie over Power shell.
* Zie [Az.Automation](/powershell/module/az.automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
