---
title: Azure Automation Veelgestelde vragen | Microsoft Docs
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 2b40cc3d4cea4476ffde8bee8cec694975eb5083
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724269"
---
# <a name="azure-automation-frequently-asked-questions"></a>Veelgestelde vragen over Azure Automation

Deze veelgestelde vragen over micro soft vindt u een lijst met veel gestelde antwoorden over Azure Automation. Als u andere vragen over de mogelijkheden hebt, gaat u naar het discussie forum en plaatst u uw vragen. Wanneer een vraag regel matig wordt gesteld, voegen we deze toe aan dit artikel zodat het snel en eenvoudig kan worden gevonden.

## <a name="update-management"></a>Updatebeheer

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Kan ik onverwachte upgrades op besturingssysteem niveau voor komen?

Bij sommige Linux-varianten, zoals Red Hat Enterprise Linux, kunnen upgrades op besturingssysteem niveau worden uitgevoerd via pakketten. Dit kan leiden tot Updatebeheer uitvoeringen waarin het versie nummer van het besturings systeem wordt gewijzigd. Omdat Updatebeheer dezelfde methoden gebruikt voor het bijwerken van pakketten die een beheerder lokaal op een Linux-computer gebruikt, is dit gedrag opzettelijk.

Gebruik de functie **uitsluiting** om te voor komen dat u de versie van het besturings systeem bijwerkt via updatebeheer-implementaties.

In Red Hat Enterprise Linux is de naam van het pakket die moet worden uitgesloten `redhat-release-server.x86_64` .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Waarom worden essentiële/beveiligings updates niet toegepast?

Wanneer u updates op een Linux-machine implementeert, kunt u update classificaties selecteren. Met deze optie worden de updates die voldoen aan de opgegeven criteria, gefilterd. Dit filter wordt lokaal op de computer toegepast wanneer de update wordt geïmplementeerd.

Omdat Updatebeheer verrijking update uitvoert in de Cloud, kunt u sommige updates in Updatebeheer markeren als gevolg van een beveiligings effect, zelfs als de lokale computer deze informatie niet bevat. Als u essentiële updates toepast op een Linux-computer, zijn er mogelijk updates die niet zijn gemarkeerd als een beveiligings effect op die computer en daarom niet worden toegepast. Updatebeheer kan de computer echter toch als niet-compatibel rapporteren omdat deze aanvullende informatie over de relevante update heeft.

Het implementeren van updates op update classificatie werkt niet in de RTM-versies van CentOS. Als u updates voor CentOS correct wilt implementeren, selecteert u alle classificaties om er zeker van te zijn dat er updates worden toegepast. Voor SUSE selecteert u alleen **andere updates** als de classificatie enkele andere beveiligings updates kan installeren als deze zijn gerelateerd aan Zypper (pakket beheer) of de afhankelijkheden ervan zijn vereist. Dit gedrag is een beperking van Zypper. In sommige gevallen kan het nodig zijn om de update-implementatie opnieuw uit te voeren en de implementatie te controleren via het Update logboek.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Kan ik updates implementeren in azure-tenants?

Als u computers hebt die patches in een andere Azure-Tenant rapportage nodig hebben om Updatebeheer, moet u een volgende tijdelijke oplossing gebruiken om ze te laten plannen. U kunt de cmdlet [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) gebruiken met de `ForUpdateConfiguration` para meter die is opgegeven voor het maken van een schema. U kunt de cmdlet [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) gebruiken en de computers in de andere Tenant door geven aan de `NonAzureComputer` para meter. In het volgende voor beeld ziet u hoe u dit doet.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>Proces automatisering-python-runbooks

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Welke python 3-versie wordt ondersteund in Azure Automation?

Voor Cloud taken wordt python 3,8 ondersteund. Scripts en pakketten van elke 3. x-versie kunnen werken als de code compatibel is tussen verschillende versies.

Voor hybride taken over Hybrid Runbook Workers van Windows kunt u ervoor kiezen om een wille keurige 3. x-versie te installeren die u wilt gebruiken. Voor hybride taken in Linux Hybrid Runbook Workers is het afhankelijk van python 3-versie die op de computer is geïnstalleerd om DSC-OMSConfig en de Linux-Hybrid Worker uit te voeren. U kunt het beste versie 3,6; installeren. verschillende versies moeten echter ook werken als er geen wijzigingen in de methode handtekeningen of contracten tussen versies van python 3 optreden.

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Kunnen python 2 en python 3-runbooks worden uitgevoerd in hetzelfde Automation-account?

Ja, er is geen beperking voor het gebruik van python 2-en python 3-runbooks in hetzelfde Automation-account.  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>Wat is het plan voor de migratie van bestaande python 2-runbooks en pakketten naar python 3?

Azure Automation is niet van plan om python 2-runbooks en-pakketten naar python 3 te migreren. U moet deze migratie zelf uitvoeren. Bestaande en nieuwe python 2-runbooks en-pakketten blijven werken.

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>Wat zijn de pakketten die standaard worden ondersteund in de python 3-omgeving?

Azure package 4.0.0 wordt standaard geïnstalleerd in de python 3-automatiserings omgeving. U kunt hand matig een hogere versie van Azure pakket importeren om de standaard versie te overschrijven.

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-vice-versa"></a>Wat gebeurt er als ik een python 3-runbook uitvoer dat verwijst naar een python 2-pakket of vice versa?

Python 2 en python 3 hebben verschillende uitvoerings omgevingen. Terwijl een python 2-runbook wordt uitgevoerd, kunnen alleen python 2-pakketten worden geïmporteerd en vergelijkbaar met python 3.

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Hoe kan ik onderscheid te maken tussen python 2-en python 3-runbooks en-pakketten?

Python 3 is een nieuwe runbook-definitie die onderscheidt tussen python 2-en python 3-runbooks. Op dezelfde manier wordt een andere pakket soort geïntroduceerd voor python 3-pakketten.

## <a name="next-steps"></a>Volgende stappen

Als uw vraag hier niet wordt beantwoord, kunt u de volgende bronnen raadplegen voor meer vragen en antwoorden.

- [Azure Automation](/answers/topics/azure-automation.html)
- [Feedbackforum](https://feedback.azure.com/forums/905242-update-management)
