---
title: Migreren van Orchestrator naar Azure Automation (bèta)
description: In dit artikel wordt beschreven hoe u runbooks en integratiepakketten migreert van Orchestrator naar Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c929781772b510639e1e5e47eed19927f408d16a
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830498"
---
# <a name="migrate-from-orchestrator-to-azure-automation-beta"></a>Migreren van Orchestrator naar Azure Automation (bèta)

Runbooks in [System Center 2012 - Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)) zijn gebaseerd op activiteiten van integratiepakketten die specifiek zijn geschreven voor Orchestrator, terwijl runbooks in Azure Automation zijn gebaseerd op Windows PowerShell. [Grafische runbooks](automation-runbook-types.md#graphical-runbooks) in Azure Automation hebben een vergelijkbare weergave als Orchestrator-runbooks, met hun activiteiten die PowerShell-cmdlets, onderliggende runbooks en assets vertegenwoordigen. Naast het converteren van runbooks zelf moet u de integratiepakketten converteren met de activiteiten die de runbooks gebruiken naar integratiemodules met Windows PowerShell cmdlets. 

[Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)) (SMA) slaat runbooks op en voert deze uit in uw lokale datacenter, zoals Orchestrator, en maakt gebruik van dezelfde integratiemodules als Azure Automation. De Runbook Converter converteert Orchestrator-runbooks naar grafische runbooks, die niet worden ondersteund in SMA. U kunt nog steeds de module Standard Activities en System Center Orchestrator Integration Modules installeren in SMA, maar u moet uw [runbooks handmatig herschrijven.](/system-center/sma/authoring-automation-runbooks)

## <a name="download-the-orchestrator-migration-toolkit"></a>De Orchestrator-migratietoolkit downloaden

De eerste stap in de migratie is het downloaden [van System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323). Deze toolkit bevat hulpprogramma's waarmee u runbooks kunt converteren van Orchestrator naar Azure Automation.  

## <a name="import-the-standard-activities-module"></a>De module Standaardactiviteiten importeren

Importeer [de module Standard Activities](/system-center/orchestrator/standard-activities) in Azure Automation. Dit omvat geconverteerde versies van standaard Orchestrator-activiteiten die grafische runbooks kunnen gebruiken.

## <a name="import-orchestrator-integration-modules"></a>Orchestrator-integratiemodules importeren

Microsoft biedt [integratiepakketten voor](/previous-versions/system-center/packs/hh295851(v=technet.10)) het bouwen van runbooks om System Center en andere producten te automatiseren. Sommige van deze integratiepakketten zijn momenteel gebaseerd op OIT, maar kunnen momenteel niet worden geconverteerd naar integratiemodules vanwege bekende problemen. Importeer [System Center Orchestrator Integration Modules](https://www.microsoft.com/download/details.aspx?id=49555) in Azure Automation voor de integratiepakketten die worden gebruikt door uw runbooks die toegang hebben tot System Center. Dit pakket bevat geconverteerde versies van de integratiepakketten die kunnen worden geïmporteerd in Azure Automation en Service Management Automation.  

## <a name="convert-integration-packs"></a>Integratiepakketten converteren

Gebruik [Integration Pack Converter](/system-center/orchestrator/orch-integration-toolkit/integration-pack-wizard) om integratiepakketten die zijn gemaakt met behulp van [de Orchestrator Integration Toolkit (OIT)](/previous-versions/system-center/developer/hh855853(v=msdn.10)) te converteren naar PowerShell-integratiemodules die kunnen worden geïmporteerd in Azure Automation of Service Management Automation. Wanneer u Integration Pack Converter hebt uitgevoerd, ziet u een wizard waarmee u een integratiepakketbestand (.oip) kunt selecteren. De wizard vermeldt vervolgens de activiteiten die in dat integratiepakket zijn opgenomen en stelt u in staat om te selecteren welke activiteiten u wilt migreren. Wanneer u de wizard voltooit, wordt er een integratiemodule gemaakt die een bijbehorende cmdlet bevat voor elk van de activiteiten in het oorspronkelijke integratiepakket.

> [!NOTE]
> U kunt de Integration Pack Converter niet gebruiken om integratiepakketten te converteren die niet zijn gemaakt met OIT. Er zijn ook enkele integratiepakketten van Microsoft die momenteel niet kunnen worden geconverteerd met dit hulpprogramma. Geconverteerde versies van deze integratiepakketten kunnen worden gedownload, zodat ze kunnen worden geïnstalleerd in Azure Automation of Service Management Automation.

### <a name="parameters"></a>Parameters

Alle eigenschappen van een activiteit in het integratiepakket worden geconverteerd naar parameters van de bijbehorende cmdlet in de integratiemodule.  Windows PowerShell cmdlets hebben een set algemene [parameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters) die kunnen worden gebruikt met alle cmdlets. De parameter -Verbose zorgt er bijvoorbeeld voor dat een cmdlet gedetailleerde informatie over de werking ervan opgeeft.  Geen enkele cmdlet kan een parameter hebben met dezelfde naam als een algemene parameter. Als een activiteit een eigenschap heeft met dezelfde naam als een algemene parameter, wordt u door de wizard gevraagd een andere naam op te geven voor de parameter.

### <a name="monitor-activities"></a>Activiteiten controleren

Runbooks bewaken in Orchestrator begint met een [monitoractiviteit en](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) wordt continu uitgevoerd in afwachting van aanroep door een bepaalde gebeurtenis. Azure Automation biedt geen ondersteuning voor het bewaken van runbooks, zodat alle controleactiviteiten in het integratiepakket niet worden geconverteerd. In plaats daarvan wordt er een tijdelijke cmdlet gemaakt in de integratiemodule voor de monitoractiviteit.  Deze cmdlet heeft geen functionaliteit, maar hiermee kan elk geconverteerd runbook dat ervan gebruikmaakt, worden geïnstalleerd. Dit runbook kan niet worden uitgevoerd in Azure Automation, maar kan wel worden geïnstalleerd zodat u het kunt wijzigen.

Orchestrator bevat een set [standaardactiviteiten die](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)) niet zijn opgenomen in een integratiepakket, maar door veel runbooks worden gebruikt.  De module Standard Activities is een integratiemodule met een cmdlet die equivalent is voor elk van deze activiteiten. U moet deze integratiemodule installeren in Azure Automation geconverteerde runbooks te importeren die gebruikmaken van een standaardactiviteit.

Naast het ondersteunen van geconverteerde runbooks kunnen de cmdlets in de module Standaardactiviteiten worden gebruikt door iemand die bekend is met Orchestrator om nieuwe runbooks te bouwen in Azure Automation. Hoewel de functionaliteit van alle standaardactiviteiten kan worden uitgevoerd met cmdlets, kunnen ze anders werken. De cmdlets in de module geconverteerde standaardactiviteiten werken op dezelfde manier als de bijbehorende activiteiten en gebruiken dezelfde parameters. Dit kan u helpen bij de overgang naar Azure Automation runbooks.

## <a name="convert-orchestrator-runbooks"></a>Orchestrator-runbooks converteren

Orchestrator Runbook Converter converteert Orchestrator-runbooks naar [grafische runbooks](automation-runbook-types.md#graphical-runbooks) die kunnen worden geïmporteerd in Azure Automation. Runbook Converter wordt geïmplementeerd als een PowerShell-module met de cmdlet `ConvertFrom-SCORunbook` die de conversie maakt. Wanneer u de converter installeert, maakt deze een snelkoppeling naar een PowerShell-sessie die de cmdlet laadt.   

Hier volgen de basisstappen voor het converteren van een runbook en het importeren in Azure Automation. Meer informatie over het gebruik van de cmdlet wordt verder in deze sectie gegeven.

1. Exporteert een of meer runbooks uit Orchestrator.
2. Integratiemodules verkrijgen voor alle activiteiten in het runbook.
3. Converteert de Orchestrator-runbooks in het geëxporteerde bestand.
4. Bekijk de informatie in logboeken om de conversie te valideren en om eventuele vereiste handmatige taken te bepalen.
5. Geconverteerde runbooks importeren in Azure Automation.
6. Maak alle vereiste assets in Azure Automation.
7. Bewerk het runbook in Azure Automation vereiste activiteiten te wijzigen.

De syntaxis voor `ConvertFrom-SCORunbook` is:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath: pad naar het exportbestand met de runbooks die u wilt converteren.
* Module: een door komma's scheidingstekenlijst met integratiemodules met activiteiten in de runbooks.
* OutputFolder: pad naar de map om geconverteerde grafische runbooks te maken.

De volgende voorbeeldopdracht converteert de runbooks in een exportbestand met de **naam MyRunbooks.ois_export**.  Deze runbooks maken gebruik van de Active Directory en Data Protection Manager-integratiepakketten.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="use-runbook-converter-log-files"></a>Runbook Converter-logboekbestanden gebruiken

De Runbook Converter maakt de volgende logboekbestanden op dezelfde locatie als het geconverteerde runbook.  Als de bestanden al bestaan, worden ze overschreven met informatie uit de laatste conversie.

| File | Inhoud |
|:--- |:--- |
| Runbook Converter - Progress.log |Gedetailleerde stappen van de conversie, inclusief informatie voor elke activiteit die is geconverteerd en waarschuwing voor elke activiteit die niet is geconverteerd. |
| Runbook Converter - Summary.log |Samenvatting van de laatste conversie, inclusief eventuele waarschuwingen en vervolgtaken die u moet uitvoeren, zoals het maken van een variabele die vereist is voor het geconverteerde runbook. |

### <a name="export-runbooks-from-orchestrator"></a>Runbooks exporteren vanuit Orchestrator

Runbook Converter werkt met een exportbestand uit Orchestrator dat een of meer runbooks bevat.  Er wordt een bijbehorend Azure Automation runbook gemaakt voor elk Orchestrator-runbook in het exportbestand.  

Als u een runbook uit Orchestrator wilt exporteren, klikt u met de rechtermuisknop op de naam van het runbook in Runbook Designer selecteert u **Exporteren.**  Als u alle runbooks in een map wilt exporteren, klikt u met de rechtermuisknop op de naam van de map en **selecteert u Exporteren.**

### <a name="convert-runbook-activities"></a>Runbookactiviteiten converteren

De Runbook Converter converteert elke activiteit in het Orchestrator-runbook naar een overeenkomstige activiteit in Azure Automation.  Voor activiteiten die niet kunnen worden geconverteerd, wordt een tijdelijke aanduiding gemaakt in het runbook met waarschuwingstekst.  Nadat u het geconverteerde runbook in Azure Automation geïmporteerd, moet u elk van deze activiteiten vervangen door geldige activiteiten die de vereiste functionaliteit uitvoeren.

Orchestrator-activiteiten in de module Standaardactiviteiten worden geconverteerd. Er zijn echter enkele standaard Orchestrator-activiteiten die niet in deze module zijn en die niet worden geconverteerd. Heeft bijvoorbeeld `Send Platform Event` geen equivalent Azure Automation omdat de gebeurtenis specifiek is voor Orchestrator.

[Controleactiviteiten](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) worden niet geconverteerd omdat er geen equivalent aan is in Azure Automation. De uitzonderingen zijn controleactiviteiten in geconverteerde integratiepakketten die worden geconverteerd naar de tijdelijke aanduiding.

Elke activiteit van een geconverteerd integratiepakket wordt geconverteerd als u het pad naar de integratiemodule opgeeft met de `modules` parameter . Voor System Center-integratiepakketten kunt u de System Center Orchestrator Integration Modules gebruiken.

### <a name="manage-orchestrator-resources"></a>Orchestrator-resources beheren

Runbook Converter converteert alleen runbooks, niet andere Orchestrator-resources, zoals tellers, variabelen of verbindingen.  Tellers worden niet ondersteund in Azure Automation.  Variabelen en verbindingen worden ondersteund, maar u moet ze handmatig maken. De logboekbestanden laten u weten of het runbook dergelijke resources vereist en geven de bijbehorende resources op die u in Azure Automation moet maken om het geconverteerde runbook goed te laten werken.

Een runbook kan bijvoorbeeld een variabele gebruiken om een bepaalde waarde in een activiteit in te vullen.  Het geconverteerde runbook converteert de activiteit en geeft een variabele asset in Azure Automation met dezelfde naam als de Orchestrator-variabele. Deze actie wordt vermeld in het **bestand Runbook Converter - Summary.log** dat na de conversie wordt gemaakt. U moet deze variabele asset handmatig maken in Azure Automation voordat u het runbook gebruikt.

### <a name="work-with-orchestrator-input-parameters"></a>Werken met Orchestrator-invoerparameters

Runbooks in Orchestrator accepteren invoerparameters met de `Initialize Data` activiteit.  Als het runbook dat wordt geconverteerd deze activiteit bevat, wordt er een invoerparameter [in](automation-graphical-authoring-intro.md#handle-runbook-input) het Azure Automation runbook gemaakt voor elke parameter in de activiteit.  Er [wordt een werkstroomscriptbesturingselementactiviteit](automation-graphical-authoring-intro.md#use-activities) gemaakt in het geconverteerde runbook dat elke parameter op haalt en retourneert. Alle activiteiten in het runbook die gebruikmaken van een invoerparameter verwijzen naar de uitvoer van deze activiteit.

De reden dat deze strategie wordt gebruikt, is om de functionaliteit in het Orchestrator-runbook het beste te spiegelen.  Activiteiten in nieuwe grafische runbooks moeten rechtstreeks verwijzen naar invoerparameters met behulp van een runbookinvoergegevensbron.

### <a name="invoke-runbook-activity"></a>Runbookactiviteit aanroepen

Runbooks in Orchestrator starten andere runbooks met de `Invoke Runbook` activiteit. Als het runbook dat wordt geconverteerd deze activiteit bevat en de optie is ingesteld, wordt er een runbookactiviteit voor gemaakt `Wait for completion` in het geconverteerde runbook.  Als de optie niet is ingesteld, wordt er een werkstroomscriptactiviteit gemaakt die `Wait for completion` [gebruikmaakt van Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) om het runbook te starten. Nadat u het geconverteerde runbook in Azure Automation geïmporteerd, moet u deze activiteit wijzigen met de informatie die is opgegeven in de activiteit.

## <a name="create-orchestrator-assets"></a>Orchestrator-assets maken

De Runbook Converter converteert orchestrator-assets niet. U moet de vereiste Orchestrator-assets handmatig maken in Azure Automation.

## <a name="configure-hybrid-runbook-worker"></a>Een Hybrid Runbook Worker

Orchestrator slaat runbooks op een databaseserver op en voert deze uit op runbookservers, beide in uw lokale datacenter. Runbooks in Azure Automation worden opgeslagen in de Azure-cloud en kunnen worden uitgevoerd in uw lokale datacenter met behulp van [een Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Configureer een worker om uw runbooks uit te voeren die zijn geconverteerd vanuit Orchestrator, omdat deze zijn ontworpen om te worden uitgevoerd op lokale servers en toegang te krijgen tot lokale resources.

## <a name="related-articles"></a>Verwante artikelen:

* Zie System Center [2012 - Orchestrator voor meer informatie over Orchestrator.](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12))
* Meer informatie over het automatiseren van het beheer van services in [Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)).
* Details van Orchestrator-activiteiten vindt u in [Standaardactiviteiten van Orchestrator.](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12))
* Zie Download System Center Orchestrator Migration Toolkit voor informatie over het verkrijgen van [de Orchestrator Migration Toolkit.](https://www.microsoft.com/download/details.aspx?id=47323)
* Zie overzicht van Azure Automation Hybrid Runbook Worker voor Hybrid Runbook Worker overzicht [van de Azure Automation Hybrid Runbook Worker.](automation-hybrid-runbook-worker.md)
