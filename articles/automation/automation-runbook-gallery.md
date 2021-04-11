---
title: Azure Automation runbooks en-modules gebruiken in PowerShell Gallery
description: In dit artikel leest u hoe u runbooks en modules van micro soft GitHub opslag plaatsen en de PowerShell Gallery gebruikt.
services: automation
ms.subservice: process-automation
ms.date: 04/07/2021
ms.topic: conceptual
ms.openlocfilehash: 2df019888d293cd8a25a34e6f0f4e7dd215c6a41
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030630"
---
# <a name="use-existing-runbooks-and-modules"></a>Bestaande runbooks en modules gebruiken

In plaats van uw eigen runbooks en modules te maken in Azure Automation, hebt u toegang tot scenario's die al zijn gemaakt door micro soft en de community. U kunt Azure-gerelateerde Power shell-en python-runbooks ophalen uit de Runbook Gallery in de Azure Portal, en [modules](#modules-in-the-powershell-gallery) en [runbooks](#runbooks-in-the-powershell-gallery) (die al dan niet specifiek zijn voor Azure) van de PowerShell Gallery. U kunt ook bijdragen aan de community door scenario's te delen [die u ontwikkelt](#contribute-to-the-community).

> [!NOTE]
> Het TechNet-Script centrum wordt buiten gebruik gesteld. Alle runbooks uit het script centrum in de Runbook Gallery zijn verplaatst naar onze [Automation github-organisatie](https://github.com/azureautomation) Zie [Azure Automation Runbooks verplaatsen naar github](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)voor meer informatie.

## <a name="import-runbooks-from-github-with-the-azure-portal"></a>Runbooks importeren uit GitHub met de Azure Portal

1. Open uw Automation-account in Azure Portal.
2. Selecteer **Runbooks galerie** onder **proces automatisering**.
3. Selecteer **Bron: github**.
4. U kunt de filters boven de lijst gebruiken om de weer gave te beperken op uitgever, type en sorteren. Zoek het gewenste galerij-item en selecteer dit om de details ervan weer te geven.

   :::image type="content" source="./media/automation-runbook-gallery/browse-gallery-github.png" alt-text="De runbook Gallery doorzoeken." lightbox="./media/automation-runbook-gallery/browse-gallery-github-expanded.png":::

5. Als u een item wilt importeren, klikt u op **importeren** op de pagina Details.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import.png" alt-text="Galerie-item importeren.":::

6. Wijzig desgewenst de naam van het runbook op de Blade importeren en klik vervolgens op **OK** om het runbook te importeren.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import-blade.png" alt-text="Blade import van galerij-item.":::

7. Het runbook wordt weer gegeven op het tabblad **Runbooks** voor het Automation-account.
 
## <a name="runbooks-in-the-powershell-gallery"></a>Runbooks in de PowerShell Gallery

> [!IMPORTANT]
> U moet de inhoud van alle runbooks valideren die u uit de PowerShell Gallery haalt. Wees uiterst voorzichtig bij het installeren en uitvoeren van deze in een productie omgeving.

De [PowerShell Gallery](https://www.powershellgallery.com/packages) biedt verschillende Runbooks van micro soft en de community die u in azure Automation kunt importeren. Als u één wilt gebruiken, moet u een runbook downloaden uit de galerie of u kunt vanuit de galerie of vanuit uw Automation-account rechtstreeks runbooks importeren in de Azure Portal.

> [!NOTE]
> Grafische runbooks worden niet ondersteund in PowerShell Gallery.

U kunt alleen rechtstreeks importeren vanuit de PowerShell Gallery met behulp van de Azure Portal. U kunt deze functie niet uitvoeren met Power shell. De procedure is hetzelfde als die wordt weer gegeven in [Runbooks importeren uit github met de Azure Portal](#import-runbooks-from-github-with-the-azure-portal), behalve dat de **bron** wordt **PowerShell Gallery**.

:::image type="content" source="./media/automation-runbook-gallery/source-runbook-gallery-small.png" alt-text="De bron selectie van de runbook-galerie wordt weer gegeven." lightbox="./media/automation-runbook-gallery/source-runbook-gallery-large.png":::

## <a name="modules-in-the-powershell-gallery"></a>Modules in de PowerShell Gallery

Power shell-modules bevatten cmdlets die u in uw runbooks kunt gebruiken. Bestaande modules die u in Azure Automation kunt installeren, zijn beschikbaar in de [PowerShell Gallery](https://www.powershellgallery.com). U kunt deze galerie starten vanuit het Azure Portal en de modules rechtstreeks installeren in Azure Automation, of u kunt ze hand matig downloaden en installeren.

U kunt ook modules vinden om te importeren in de Azure Portal. Ze worden weer gegeven voor uw Automation-account in de **Galerie met modules** onder **gedeelde bronnen**.

## <a name="common-scenarios-available-in-the-powershell-gallery"></a>Algemene scenario's die beschikbaar zijn in de PowerShell Gallery

De onderstaande lijst bevat enkele runbooks die veelvoorkomende scenario's ondersteunen. Zie [AzureAutomationTeam-profiel](https://www.powershellgallery.com/profiles/AzureAutomationTeam)voor een volledige lijst met runbooks die zijn gemaakt door het Azure Automation team.

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) : Hiermee wordt de nieuwste versie van alle modules in een Automation-account uit PowerShell Gallery geïmporteerd.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) : configureert Azure Diagnostics en Log Analytics om Azure Automation logboeken te ontvangen met taak status en taak stromen.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) : kopieert een extern bestand van een virtuele Windows Azure-machine.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) : kopieert een lokaal bestand naar een virtuele machine van Azure.

## <a name="contribute-to-the-community"></a>Bijdragen aan de Community

We raden u ten zeerste aan om bijdragen te leveren en de Azure Automation community te verg Roten. Deel de fantastische runbooks die u met de Community hebt gemaakt. Uw bijdragen worden gewaardeerd.

### <a name="add-a-runbook-to-the-github-runbook-gallery"></a>Een runbook toevoegen aan de GitHub-Runbook-galerie

U kunt nieuwe Power shell-of python-runbooks toevoegen aan de Runbook-galerie met deze GitHub-werk stroom.

1. Maak een open bare opslag plaats op GitHub en voeg het runbook en eventuele andere benodigde bestanden toe (zoals readme.md, Description, enzovoort).
1. Voeg het onderwerp toe `azureautomationrunbookgallery` om er zeker van te zijn dat de opslag plaats wordt gedetecteerd door onze service, zodat deze kan worden weer gegeven in de galerie met Automation-Runbook.
1. Als het runbook dat u hebt gemaakt een Power shell-werk stroom is, voegt u het onderwerp toe `PowerShellWorkflow` . Als het een python 3-runbook is, voegt u toe `Python3` . Er zijn geen andere specifieke onderwerpen vereist voor Azure-runbooks, maar we raden u aan andere onderwerpen toe te voegen die kunnen worden gebruikt voor categorisatie en zoek opdrachten in de Runbook-galerie.

   >[!NOTE]
   >Bekijk bestaande runbooks in de galerie voor zaken als opmaak, kopteksten en bestaande tags die u kunt gebruiken (zoals `Azure Automation` of `Linux Azure Virtual Machines` ).

Als u wijzigingen in een bestaand runbook wilt Voorst Ellen, moet u een pull-aanvraag indienen. 

Als u besluit om een bestaand runbook te klonen en te bewerken, moet best practice een andere naam geven. Als u de oude naam opnieuw gebruikt, wordt deze twee keer weer gegeven in de lijst met Runbook gallerys.

>[!NOTE]
>Zorg ervoor dat er ten minste 12 uur voor synchronisatie tussen GitHub en de Automation Runbook Gallery voor zowel bijgewerkte als nieuwe runbooks.

### <a name="add-a-powershell-runbook-to-the-powershell-gallery"></a>Een Power shell-runbook toevoegen aan de Power shell-galerie

Micro soft raadt u aan runbooks toe te voegen aan de PowerShell Gallery die u nuttig vindt voor andere klanten. De PowerShell Gallery accepteert Power shell-modules en Power shell-scripts. U kunt een runbook toevoegen door [het te uploaden naar de PowerShell Gallery](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-modules-gallery-in-the-azure-portal"></a>Importeer een module uit de galerie met modules in de Azure Portal

1. Open uw Automation-account in Azure Portal.
1. Onder **gedeelde bronnen** selecteert u **modules galerie** om de lijst met modules te openen.

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="Weer gave van de module galerie." lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. Op de pagina Blade galerie kunt u zoeken op de volgende velden:

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

## <a name="request-a-runbook-or-module"></a>Een runbook of module aanvragen

U kunt aanvragen verzenden naar de stem van de [gebruiker](https://feedback.azure.com/forums/246290-azure-automation/).  Als u hulp nodig hebt bij het schrijven van een runbook of als u een vraag hebt over Power shell, plaatst u een vraag op onze vraag [pagina van micro soft Q&](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie: een Power shell-Runbook maken](learn/automation-tutorial-runbook-textual-powershell.md)om aan de slag te gaan met Power shell-runbooks.
* Zie [Runbooks beheren in azure Automation](manage-runbooks.md)voor het werken met runbooks.
* Zie [Power shell docs](/powershell/scripting/overview)(Engelstalig) voor meer informatie over Power shell-scripts.
* Zie [Az.Automation](/powershell/module/az.automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
