---
title: Migreren van Orchestrator naar Azure Automation (bèta)
description: In dit artikel wordt beschreven hoe u runbooks en integratie pakketten migreert van Orchestrator naar Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 6ee4a09df0f95cb809db0e5c0e63d195ee5cfdff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896932"
---
# <a name="migrate-from-orchestrator-to-azure-automation-beta"></a>Migreren van Orchestrator naar Azure Automation (bèta)

Runbooks in [System Center 2012-Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)) zijn gebaseerd op activiteiten van integratie pakketten die specifiek voor Orchestrator zijn geschreven, terwijl runbooks in azure Automation zijn gebaseerd op Windows Power shell. [Grafische runbooks](automation-runbook-types.md#graphical-runbooks) in azure Automation hebben een vergelijk bare vormgeving van Orchestrator-runbooks, met hun activiteiten die Power shell-cmdlets, onderliggende runbooks en assets vertegenwoordigen. Naast het converteren van runbooks zelf moet u de integratie pakketten converteren met de activiteiten die de runbooks gebruiken om modules te integreren met Windows Power shell-cmdlets. 

[Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)) (SMA) slaat runbooks op in uw lokale Data Center als Orchestrator en voert deze uit met dezelfde integratie modules als Azure Automation. Het Runbook-conversie programma converteert Orchestrator-runbooks naar grafische runbooks, die niet worden ondersteund in SMA. U kunt nog steeds de module Standard-activiteiten en System Center Orchestrator-integratie modules in SMA installeren, maar u moet [uw runbooks hand matig herschrijven](/system-center/sma/authoring-automation-runbooks).

## <a name="download-the-orchestrator-migration-toolkit"></a>De Orchestrator Migration Toolkit downloaden

De eerste stap bij de migratie is het downloaden van de [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323). Deze toolkit bevat hulpprogram ma's om u te helpen bij het converteren van runbooks van Orchestrator naar Azure Automation.  

## <a name="import-the-standard-activities-module"></a>De module Standard activities importeren

Importeer de [module Standard activities](/system-center/orchestrator/standard-activities) in azure Automation. Dit omvat geconverteerde versies van Standard Orchestrator-activiteiten die voor het converteren van grafische runbooks kunnen worden gebruikt.

## <a name="import-orchestrator-integration-modules"></a>Orchestrator-integratie modules importeren

Micro soft biedt [integratie pakketten](/previous-versions/system-center/packs/hh295851(v=technet.10)) voor het bouwen van Runbooks om System Center-onderdelen en andere producten te automatiseren. Sommige van deze integratie pakketten zijn momenteel gebaseerd op OIT, maar kunnen momenteel niet worden geconverteerd naar integratie modules vanwege bekende problemen. Importeer [System Center Orchestrator-integratie modules](https://www.microsoft.com/download/details.aspx?id=49555) in azure Automation voor de integratie pakketten die worden gebruikt door uw runbooks die toegang hebben tot System Center. Dit pakket bevat geconverteerde versies van de integratie pakketten die kunnen worden geïmporteerd in Azure Automation en Service Management Automation.  

## <a name="convert-integration-packs"></a>Integratie pakketten converteren

Gebruik het [conversie pakket Converter](/system-center/orchestrator/orch-integration-toolkit/integration-pack-wizard) om integratie pakketten te converteren die zijn gemaakt met behulp van de [Orchestrator Integration Toolkit (OIT)](/previous-versions/system-center/developer/hh855853(v=msdn.10)) naar op Power shell gebaseerde integratie modules die in azure Automation of Service Management Automation kunnen worden geïmporteerd. Wanneer u het integratie pakket conversieprogramma uitvoert, wordt een wizard weer gegeven waarmee u een integratie pakket bestand (. OIP) kunt selecteren. De wizard geeft een lijst van de activiteiten die zijn opgenomen in dat integratie pakket en Hiermee kunt u selecteren welke activiteiten moeten worden gemigreerd. Wanneer u de wizard voltooit, wordt er een integratie module gemaakt die een bijbehorende cmdlet bevat voor elk van de activiteiten in het oorspronkelijke integratie pakket.

> [!NOTE]
> U kunt het integratie pakket conversie programma niet gebruiken om integratie pakketten te converteren die niet met OIT zijn gemaakt. Er zijn ook enkele integratie pakketten van micro soft die momenteel niet met dit hulp programma kunnen worden geconverteerd. Geconverteerde versies van deze integratie pakketten worden ter down load gedownload zodat ze kunnen worden geïnstalleerd in Azure Automation of Service Management Automation.

### <a name="parameters"></a>Parameters

Alle eigenschappen van een activiteit in het integratie pakket worden geconverteerd naar para meters van de bijbehorende cmdlet in de integratie module.  Windows Power shell-cmdlets hebben een aantal [algemene para meters](/powershell/module/microsoft.powershell.core/about/about_commonparameters) die kunnen worden gebruikt met alle cmdlets. De para meter-verbose zorgt er bijvoorbeeld voor dat een cmdlet gedetailleerde informatie over de bijbehorende bewerking uitvoert.  Geen cmdlet kan een para meter hebben met dezelfde naam als een gemeen schappelijke para meter. Als een activiteit een eigenschap heeft die dezelfde naam heeft als een gemeen schappelijke para meter, wordt u door de wizard gevraagd een andere naam op te geven voor de para meter.

### <a name="monitor-activities"></a>Activiteiten controleren

Runbooks in Orchestrator bewaken start met een [monitor activiteit](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) en voert voortdurend wachten om te worden aangeroepen door een bepaalde gebeurtenis. Azure Automation biedt geen ondersteuning voor het controleren van runbooks, waardoor monitor activiteiten in het integratie pakket niet worden geconverteerd. In plaats daarvan wordt een tijdelijke aanduiding voor cmdlets gemaakt in de integratie module voor de monitor activiteit.  Deze cmdlet heeft geen functionaliteit, maar kan elk geconverteerde runbook dat gebruikt wordt, wel installeren. Dit runbook kan niet worden uitgevoerd in Azure Automation, maar kan wel worden geïnstalleerd, zodat u het kunt wijzigen.

Orchestrator bevat een set [standaard activiteiten](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)) die niet zijn opgenomen in een integratie pakket, maar die worden gebruikt door veel runbooks.  De module Standard activities is een integratie module die een equivalente cmdlet voor elk van deze activiteiten bevat. U moet deze integratie module in Azure Automation installeren voordat u geconverteerde runbooks importeert die gebruikmaken van een standaard activiteit.

Naast de ondersteuning van geconverteerde runbooks, kunnen de cmdlets in de module Standard activities worden gebruikt door iemand die vertrouwd is met Orchestrator om nieuwe runbooks te bouwen in Azure Automation. Hoewel de functionaliteit van alle standaard activiteiten kan worden uitgevoerd met-cmdlets, kunnen ze anders functioneren. De cmdlets in de module geconverteerde standaard activiteiten werken op dezelfde manier als de bijbehorende activiteiten en gebruiken dezelfde para meters. Dit kan u helpen bij het overstappen op Azure Automation runbooks.

## <a name="convert-orchestrator-runbooks"></a>Orchestrator-runbooks converteren

Het Orchestrator Runbook Converter converteert Orchestrator-runbooks naar [grafische runbooks](automation-runbook-types.md#graphical-runbooks) die in azure Automation kunnen worden geïmporteerd. Het Runbook-conversie programma wordt geïmplementeerd als een Power shell-module met de cmdlet `ConvertFrom-SCORunbook` die de conversie uitvoert. Wanneer u het conversie programma installeert, wordt er een snelkoppeling gemaakt naar een Power shell-sessie die de cmdlet laadt.   

Hier volgen de basis stappen voor het converteren van een runbook en het importeren in Azure Automation. Verderop in dit gedeelte vindt u meer informatie over het gebruik van de cmdlet.

1. Een of meer runbooks exporteren vanuit Orchestrator.
2. Verkrijg integratie modules voor alle activiteiten in het runbook.
3. Converteer de Orchestrator-runbooks in het geëxporteerde bestand.
4. Lees de informatie in Logboeken om de conversie te valideren en om vereiste hand matige taken te bepalen.
5. Importeer geconverteerde runbooks in Azure Automation.
6. Maak vereiste assets in Azure Automation.
7. Bewerk het runbook in Azure Automation om de vereiste activiteiten te wijzigen.

De syntaxis voor `ConvertFrom-SCORunbook` is:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath: het pad naar het export bestand met de runbooks die moeten worden geconverteerd.
* Module: door Komma's gescheiden lijst met integratie modules met activiteiten in de runbooks.
* OutputFolder: het pad naar de map voor het maken van geconverteerde grafische runbooks.

Met de volgende voorbeeld opdracht worden de runbooks geconverteerd in een export bestand met de naam **MyRunbooks.ois_export**.  Deze runbooks gebruiken de Active Directory-en Data Protection Manager-integratie pakketten.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="use-runbook-converter-log-files"></a>Logboek bestanden voor Runbook-converter gebruiken

Met het Runbook-conversie programma maakt u de volgende logboek bestanden op dezelfde locatie als het geconverteerde Runbook.  Als de bestanden al bestaan, worden deze overschreven met informatie van de laatste conversie.

| File | Inhoud |
|:--- |:--- |
| Runbook-Converter-Progress. log |Gedetailleerde stappen van de conversie, inclusief informatie voor elke activiteit die is geconverteerd en waarschuwing voor elke activiteit die niet wordt geconverteerd. |
| Runbook-Converter-samenvattings. log |Samen vatting van de laatste conversie, inclusief waarschuwingen en opvolgings taken die u moet uitvoeren, zoals het maken van een variabele die vereist is voor het geconverteerde runbook. |

### <a name="export-runbooks-from-orchestrator"></a>Runbooks exporteren vanuit Orchestrator

Het Runbook-conversie programma werkt met een export bestand van Orchestrator dat een of meer runbooks bevat.  Er wordt een bijbehorend Azure Automation runbook gemaakt voor elk Orchestrator-runbook in het export bestand.  

Als u een runbook van Orchestrator wilt exporteren, klikt u met de rechter muisknop op de naam van het runbook in Runbook Designer en selecteert u **exporteren**.  Als u alle runbooks in een map wilt exporteren, klikt u met de rechter muisknop op de naam van de map en selecteert u **exporteren**.

### <a name="convert-runbook-activities"></a>Runbook-activiteiten converteren

Het Runbook-conversie programma converteert elke activiteit in het Orchestrator-Runbook naar een bijbehorende activiteit in Azure Automation.  Voor activiteiten die niet kunnen worden geconverteerd, wordt een tijdelijke aanduiding van een activiteit in het runbook gemaakt met waarschuwings tekst.  Nadat u het geconverteerde runbook in Azure Automation hebt geïmporteerd, moet u deze activiteiten vervangen door geldige activiteiten die de vereiste functionaliteit uitvoeren.

Alle Orchestrator-activiteiten in de module standaard activiteiten worden geconverteerd. Er zijn een aantal Standard-Orchestrator-activiteiten die zich niet in deze module bevinden en die niet worden geconverteerd. `Send Platform Event`Heeft bijvoorbeeld geen Azure Automation equivalent omdat de gebeurtenis specifiek is voor Orchestrator.

[Bewakings activiteiten](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) worden niet geconverteerd omdat er geen equivalent is in azure Automation. De uitzonde ringen zijn bewakings activiteiten in geconverteerde integratie pakketten die worden geconverteerd naar de activiteit van de tijdelijke aanduiding.

Alle activiteiten van een geconverteerd integratie pakket worden geconverteerd als u het pad naar de integratie module met de `modules` para meter opgeeft. Voor System Center-integratie pakketten kunt u gebruikmaken van de System Center Orchestrator-integratie modules.

### <a name="manage-orchestrator-resources"></a>Orchestrator-resources beheren

Met het Runbook Converter worden alleen runbooks geconverteerd, niet andere Orchestrator-bronnen zoals tellers, variabelen of verbindingen.  Tellers worden niet ondersteund in Azure Automation.  Variabelen en verbindingen worden ondersteund, maar u moet ze hand matig maken. Met de logboek bestanden wordt u gewaarschuwd als het runbook dergelijke bronnen vereist en bijbehorende resources opgeven die u in Azure Automation moet maken om het geconverteerde runbook goed te laten functioneren.

Een runbook kan bijvoorbeeld een variabele gebruiken om een bepaalde waarde in een activiteit te vullen.  Met het geconverteerde runbook wordt de activiteit geconverteerd en wordt een variabele-activum in Azure Automation opgegeven met dezelfde naam als de Orchestrator-variabele. Deze actie wordt vermeld in het **logboek bestand met Runbook-samen vattingen** dat na de conversie is gemaakt. U moet deze variabele Asset hand matig maken in Azure Automation voordat u het runbook gebruikt.

### <a name="work-with-orchestrator-input-parameters"></a>Werken met Orchestrator-invoer parameters

Runbooks in Orchestrator accepteren invoer parameters met de `Initialize Data` activiteit.  Als het runbook dat wordt geconverteerd deze activiteit bevat, wordt een [invoer parameter](automation-graphical-authoring-intro.md#handle-runbook-input) in het Azure Automation runbook gemaakt voor elke para meter in de activiteit.  Er wordt een activiteit voor het [besturings element werk stroom script](automation-graphical-authoring-intro.md#use-activities) gemaakt in het geconverteerde runbook waarmee elke para meter wordt opgehaald en geretourneerd. Alle activiteiten in het runbook die een invoer parameter gebruiken, verwijzen naar de uitvoer van deze activiteit.

De reden hiervoor is dat deze strategie het beste spiegelt van de functionaliteit in het Orchestrator runbook.  Activiteiten in nieuwe grafische runbooks moeten rechtstreeks verwijzen naar invoer parameters met behulp van een invoer gegevens bron van een Runbook.

### <a name="invoke-runbook-activity"></a>Runbook-activiteit aanroepen

Runbooks in Orchestrator starten andere runbooks met de `Invoke Runbook` activiteit. Als het runbook dat wordt geconverteerd, deze activiteit bevat en de `Wait for completion` optie is ingesteld, wordt er een runbook-activiteit voor gemaakt in het geconverteerde runbook.  Als de `Wait for completion` optie niet is ingesteld, wordt er een werk stroom script activiteit gemaakt die [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) gebruikt om het runbook te starten. Nadat u het geconverteerde runbook in Azure Automation hebt geïmporteerd, moet u deze activiteit wijzigen met de informatie die is opgegeven in de activiteit.

## <a name="create-orchestrator-assets"></a>Orchestrator-assets maken

Met het Runbook-conversie programma worden geen Orchestrator-assets geconverteerd. U moet hand matig vereiste Orchestrator-assets maken in Azure Automation.

## <a name="configure-hybrid-runbook-worker"></a>Hybrid Runbook Worker configureren

Orchestrator slaat runbooks op een database server op en voert deze uit op runbook-servers, zowel in uw lokale Data Center. Runbooks in Azure Automation worden opgeslagen in de Azure-Cloud en kunnen worden uitgevoerd in uw lokale Data Center met behulp van een [Hybrid Runbook worker](automation-hybrid-runbook-worker.md). Configureer een werk nemer voor het uitvoeren van uw runbooks die zijn geconverteerd van orchestrator, omdat deze zijn ontworpen om te worden uitgevoerd op lokale servers en toegang te krijgen tot lokale bronnen.

## <a name="related-articles"></a>Verwante artikelen:

* Zie [System Center 2012-Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12))voor Orchestrator-Details.
* Meer informatie over het automatiseren van het beheer van services in [Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)).
* Details van Orchestrator-activiteiten vindt u in de [standaard activiteiten van Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)).
* Zie [System Center Orchestrator Migration Toolkit downloaden](https://www.microsoft.com/download/details.aspx?id=47323)om de Orchestrator Migration Toolkit te verkrijgen.
* Zie [overzicht van Hybrid Runbook worker](automation-hybrid-runbook-worker.md)voor een overzicht van de Azure Automation Hybrid Runbook Worker.
