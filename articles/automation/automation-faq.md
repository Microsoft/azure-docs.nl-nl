---
title: Azure Automation veelgestelde vragen | Microsoft Docs
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 12/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9ab4ce9b8691f27fe392d2e3099677d45900d2e3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834224"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation veelgestelde vragen

Deze Veelgestelde vragen over Microsoft is een lijst met veelgestelde vragen over Azure Automation. Als u nog andere vragen hebt over de mogelijkheden ervan, gaat u naar het discussieforum en plaatst u uw vragen. Wanneer een vraag vaak wordt gesteld, voegen we deze toe aan dit artikel, zodat deze snel en eenvoudig kan worden gevonden.

## <a name="update-management"></a>Updatebeheer

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Kan ik onverwachte upgrades op besturingssysteemniveau voorkomen?

Bij sommige Linux-varianten, zoals Red Hat Enterprise Linux, kunnen upgrades op besturingssysteemniveau plaatsvinden via pakketten. Dit kan leiden tot Updatebeheer uitvoeringen waarin het versienummer van het besturingssysteem verandert. Omdat Updatebeheer dezelfde methoden gebruikt om pakketten bij te werken die een beheerder lokaal op een Linux-computer gebruikt, is dit gedrag opzettelijk.

Gebruik de functie Uitsluiting om te voorkomen dat  de versie van het besturingssysteem wordt bijgewerkt via Updatebeheer implementaties.

In Red Hat Enterprise Linux is de pakketnaam die moet worden `redhat-release-server.x86_64` uitgesloten.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Waarom worden er geen essentiële/beveiligingsupdates toegepast?

Wanneer u updates implementeert op een Linux-computer, kunt u updateclassificaties selecteren. Met deze optie worden de updates gefilterd die voldoen aan de opgegeven criteria. Dit filter wordt lokaal toegepast op de computer wanneer de update wordt geïmplementeerd.

Omdat Updatebeheer updateverrijking uitvoert in de cloud, kunt u sommige updates in Updatebeheer markeren als een beveiligingsimpact, ook al heeft de lokale computer die informatie niet. Als u essentiële updates op een Linux-machine toe passen, zijn er mogelijk updates die niet zijn gemarkeerd als een beveiligingsimpact op die computer en daarom niet worden toegepast. Het is Updatebeheer dat de computer nog steeds als niet-compatibel wordt rapporteert, omdat deze aanvullende informatie over de relevante update heeft.

Het implementeren van updates op updateclassificatie werkt niet in RTM-versies van CentOS. Als u updates voor CentOS op de juiste manier wilt implementeren, selecteert u alle classificaties om ervoor te zorgen dat updates worden toegepast. Als u voor SUSE ALLEEN **andere updates** als classificatie selecteert, kunt u eerst enkele andere beveiligingsupdates installeren als ze gerelateerd zijn aan zypper (pakketbeheer) of de bijbehorende afhankelijkheden. Dit gedrag is een beperking van zypper. In sommige gevallen moet u mogelijk de update-implementatie opnieuw en vervolgens de implementatie controleren via het updatelogboek.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Kan ik updates implementeren in Azure-tenants?

Als u machines hebt die moeten worden gepatcht in een andere Azure-tenant die rapporteren aan Updatebeheer, moet u een volgende tijdelijke oplossing gebruiken om ze te plannen. U kunt de cmdlet [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) gebruiken met de `ForUpdateConfiguration` parameter die is opgegeven om een schema te maken. U kunt de cmdlet [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) gebruiken en de machines in de andere tenant doorgeven aan de `NonAzureComputer` parameter . In het volgende voorbeeld ziet u hoe u dit doet.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>Procesautomatisering - Python-runbooks

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Welke Python 3-versie wordt ondersteund in Azure Automation?

Voor cloudtaken wordt Python 3.8 ondersteund. Scripts en pakketten van een 3.x-versie kunnen werken als de code compatibel is in verschillende versies.

Voor hybride taken in Windows Hybrid Runbook Workers kunt u ervoor kiezen om een 3.x-versie te installeren die u wilt gebruiken. Voor hybride taken in Linux Hybrid Runbook Workers zijn we afhankelijk van de Python 3-versie die op de computer is geïnstalleerd om DSC OMSConfig en de Linux-Hybrid Worker. U wordt aangeraden versie 3.6 te installeren. Verschillende versies moeten echter ook werken als er geen wijzigingen zijn die wijzigingen aanbrengen die wijzigingen aanbrengen in handtekeningen of contracten tussen versies van Python 3.

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Kunnen Python 2- en Python 3-runbooks worden uitgevoerd in hetzelfde Automation-account?

Ja, er is geen beperking voor het gebruik van Python 2- en Python 3-runbooks in hetzelfde Automation-account.  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>Wat is het plan voor het migreren van bestaande Python 2-runbooks en -pakketten naar Python 3?

Azure Automation is niet van plan om Python 2-runbooks en -pakketten te migreren naar Python 3. U moet deze migratie zelf uitvoeren. Bestaande en nieuwe Python 2-runbooks en -pakketten blijven werken.

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>Welke pakketten worden standaard ondersteund in de Python 3-omgeving?

Azure-pakket 4.0.0 is standaard geïnstalleerd in de Python 3 Automation-omgeving. U kunt handmatig een hogere versie van het Azure-pakket importeren om de standaardversie te overschrijven.

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-vice-versa"></a>Wat moet ik doen als ik een Python 3-runbook voer dat verwijst naar een Python 2-pakket of vice versa?

Python 2 en Python 3 hebben verschillende uitvoeringsomgevingen. Terwijl een Python 2-runbook wordt uitgevoerd, kunnen alleen Python 2-pakketten worden geïmporteerd en vergelijkbaar voor Python 3.

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Hoe kan ik onderscheid maken tussen Python 2- en Python 3-runbooks en -pakketten?

Python 3 is een nieuwe runbookdefinitie die onderscheid maakt tussen Python 2- en Python 3-runbooks. Op dezelfde manier is er een ander type pakket geïntroduceerd voor Python 3-pakketten.

## <a name="next-steps"></a>Volgende stappen

Als uw vraag hier niet wordt beantwoord, raadpleegt u de volgende bronnen voor meer vragen en antwoorden.

- [Azure Automation](/answers/topics/azure-automation.html)
- [Feedbackforum](https://feedback.azure.com/forums/905242-update-management)
