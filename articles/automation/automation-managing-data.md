---
title: Azure Automation gegevens beveiliging
description: In dit artikel leest u hoe Azure Automation uw privacy beschermt en uw gegevens beveiligt.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/10/2021
ms.topic: conceptual
ms.openlocfilehash: e41e9af418b08210f5f0f40de9951d03711dc8e7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102616113"
---
# <a name="management-of-azure-automation-data"></a>Beheer van Azure Automation-gegevens

Dit artikel bevat verschillende onderwerpen waarin wordt uitgelegd hoe gegevens worden beveiligd en beveiligd in een Azure Automation omgeving.

## <a name="tls-12-enforcement-for-azure-automation"></a>TLS 1,2 afdwingen voor Azure Automation

Om de beveiliging van gegevens die onderweg zijn naar Azure Automation te verzekeren, raden we u aan om het gebruik van Transport Layer Security (TLS) 1,2 te configureren. Hieronder vindt u een lijst met methoden of clients die via HTTPS communiceren met de Automation-Service:

* Webhook-aanroepen

* Hybrid Runbook Workers, die machines bevatten die worden beheerd door Updatebeheer en Wijzigingen bijhouden en inventaris.

* DSC-knoop punten

Er zijn oudere versies van TLS/Secure Sockets Layer (SSL) gevonden die kwetsbaar zijn en terwijl ze nog steeds werken om achterwaartse compatibiliteit mogelijk te maken, worden ze **niet aanbevolen**. Het wordt niet aanbevolen om uw agent expliciet in te stellen voor gebruik van TLS 1,2, tenzij dit nood zakelijk is, omdat deze beveiligings functies op platform niveau kan verstoren, zodat u nieuwe beveiligde protocollen automatisch kunt detecteren en gebruiken wanneer deze beschikbaar zijn, zoals TLS 1,3.

Zie [log Analytics agent Overview-TLS 1,2](..//azure-monitor/agents/log-analytics-agent.md#tls-12-protocol)voor informatie over ondersteuning van TLS 1,2 met de log Analytics-agent voor Windows en Linux. Dit is een afhankelijkheid voor de Hybrid Runbook worker-rol.

### <a name="platform-specific-guidance"></a>Platformspecifieke richt lijnen

|Platform/taal | Ondersteuning | Meer informatie |
| --- | --- | --- |
|Linux | Linux-distributies zijn vaak afhankelijk van [openssl](https://www.openssl.org) voor TLS 1,2-ondersteuning.  | Controleer de [openssl wijzigingen logboek](https://www.openssl.org/news/changelog.html) om te bevestigen dat uw versie van openssl wordt ondersteund.|
| Windows 8,0-10 | Wordt ondersteund en is standaard ingeschakeld. | Om te bevestigen dat u nog steeds de [standaard instellingen](/windows-server/security/tls/tls-registry-settings)gebruikt.  |
| Windows Server 2012-2016 | Wordt ondersteund en is standaard ingeschakeld. | Controleren of u nog steeds de [standaard instellingen](/windows-server/security/tls/tls-registry-settings) gebruikt |
| Windows 7 SP1 en Windows Server 2008 R2 SP1 | Ondersteund, maar is niet standaard ingeschakeld. | Zie de pagina met [register instellingen voor Transport Layer Security (TLS)](/windows-server/security/tls/tls-registry-settings) voor meer informatie over het inschakelen van.  |

## <a name="data-retention"></a>Gegevensretentie

Wanneer u een resource in Azure Automation verwijdert, wordt deze veel dagen bewaard voor controle doeleinden voordat permanent wordt verwijderd. U kunt de resource niet zien of gebruiken tijdens deze periode. Dit beleid is ook van toepassing op bronnen die horen bij een verwijderd Automation-account. Het Bewaar beleid is van toepassing op alle gebruikers en kan momenteel niet worden aangepast. Als u gegevens gedurende een langere periode wilt blijven gebruiken, kunt u de [gegevens van Azure Automation-taak naar Azure monitor-logboeken door sturen](automation-manage-send-joblogs-log-analytics.md).

De volgende tabel bevat een overzicht van het Bewaar beleid voor verschillende resources.

| Gegevens | Beleid |
|:--- |:--- |
| Accounts |Een account wordt definitief verwijderd 30 dagen nadat een gebruiker het heeft verwijderd. |
| Assets |Een activum wordt definitief verwijderd 30 dagen nadat een gebruiker het heeft verwijderd, of 30 dagen nadat een gebruiker een account heeft verwijderd dat de Asset bevat. Activa bevatten variabelen, schema's, referenties, certificaten, Python 2-pakketten en verbindingen. |
| DSC-knoop punten |Een DSC-knoop punt wordt permanent verwijderd 30 dagen nadat de registratie van een Automation-account is ongedaan gemaakt met Azure Portal of de cmdlet [unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode) in Windows Power shell. Een knoop punt wordt ook 30 dagen nadat een gebruiker het account dat het knoop punt bevat verwijderd, definitief verwijderd. |
| Taken |Een taak wordt verwijderd en wordt 30 dagen na de wijziging permanent verwijderd, bijvoorbeeld nadat de taak is voltooid, is gestopt of is onderbroken. |
| Modules |Een module wordt definitief verwijderd 30 dagen nadat een gebruiker deze heeft verwijderd, of 30 dagen nadat een gebruiker het account heeft verwijderd dat de module bevat. |
| Knooppunt configuraties/MOF-bestanden |Een oude knooppunt configuratie wordt definitief verwijderd 30 dagen nadat een nieuwe knooppunt configuratie is gegenereerd. |
| Knooppunt rapporten |Een knooppunt rapport wordt 90 dagen na het genereren van een nieuw rapport voor dat knoop punt definitief verwijderd. |
| Runbooks |Een runbook wordt definitief verwijderd 30 dagen nadat een gebruiker de resource heeft verwijderd, of 30 dagen nadat een gebruiker het account heeft verwijderd dat de resource<sup>1</sup>bevat. |

<sup>1</sup> Het runbook kan worden hersteld in het 30-dagen venster door een ondersteunings incident voor Azure te archiveren met Microsoft Azure ondersteuning. Ga naar de [ondersteunings site van Azure](/support/options) en selecteer **een ondersteunings aanvraag indienen**.

## <a name="data-backup"></a>Gegevens back-up

Wanneer u een Automation-account in azure verwijdert, worden alle objecten in het account verwijderd. De objecten omvatten runbooks, modules, configuraties, instellingen, taken en assets. Ze kunnen niet worden hersteld nadat het account is verwijderd. U kunt de volgende informatie gebruiken om een back-up te maken van de inhoud van uw Automation-account voordat u deze verwijdert.

### <a name="runbooks"></a>Runbooks

U kunt uw runbooks exporteren naar script bestanden met behulp van de Azure Portal of de cmdlet [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition) in Windows Power shell. U kunt deze script bestanden importeren in een ander Automation-account, zoals beschreven in [Runbooks beheren in azure Automation](manage-runbooks.md).

### <a name="integration-modules"></a>Integratiemodules

U kunt geen integratie modules exporteren vanuit Azure Automation, ze moeten beschikbaar worden gesteld buiten het Automation-account.

### <a name="assets"></a>Assets

U kunt Azure Automation assets niet exporteren: certificaten, verbindingen, referenties, schema's en variabelen. In plaats daarvan kunt u de Azure Portal-en Azure-cmdlets gebruiken om de details van deze activa te noteren. Gebruik vervolgens deze gegevens om activa te maken die worden gebruikt door runbooks die u importeert in een ander Automation-account.

U kunt de waarden voor versleutelde variabelen of de wachtwoord velden van referenties niet ophalen met behulp van cmdlets. Als u deze waarden niet weet, kunt u ze ophalen in een runbook. Zie [variabele assets in azure Automation](shared-resources/variables.md)voor het ophalen van variabele waarden. Zie [referentie-assets in azure Automation](shared-resources/credentials.md)voor meer informatie over het ophalen van referentie waarden.

### <a name="dsc-configurations"></a>DSC-configuraties

U kunt uw DSC-configuraties exporteren naar script bestanden met behulp van de Azure Portal of de cmdlet [export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration) in Windows Power shell. U kunt deze configuraties importeren en gebruiken in een ander Automation-account.

## <a name="geo-replication-in-azure-automation"></a>Geo-replicatie in Azure Automation

Geo-replicatie is standaard in Azure Automation-accounts. U kiest een primaire regio bij het instellen van uw account. Met de interne Automation geo-Replication-service wordt automatisch een secundaire regio aan het account toegewezen. De service maakt vervolgens voortdurend een back-up van de account gegevens van de primaire regio naar de secundaire regio. U kunt de volledige lijst met primaire en secundaire regio's vinden op [bedrijfs continuïteit en herstel na nood gevallen (BCDR): gekoppelde Azure-regio's](../best-practices-availability-paired-regions.md).

De back-up die is gemaakt door de geo-replicatie service voor automatisering is een volledige kopie van Automation-assets, configuraties en dergelijke. Deze back-up kan worden gebruikt als de primaire regio uitvalt en gegevens verliest. In het onwaarschijnlijke geval dat de gegevens voor een primaire regio verloren zijn gegaan, probeert micro soft deze te herstellen.

> [!NOTE]
> Azure Automation klant gegevens worden opgeslagen in de regio die door de klant is geselecteerd. Voor BCDR zijn voor alle regio's behalve Brazilië-zuid en Zuidoost-Azië Azure Automation gegevens opgeslagen in een andere regio (Azure gekoppelde regio). Alleen voor de regio van de Brazilië-zuid (Sao Paulo-staat) van Brazilië Geografie en Zuidoost-Azië (Singapore) van de Azië en Stille Oceaan geografie slaan we Azure Automation gegevens op in dezelfde regio voor gegevens locatie vereisten voor deze regio's.

De geo-replicatie service voor automatisering is niet rechtstreeks toegankelijk voor externe klanten als er sprake is van een regionale storing. Als u de automatiserings configuratie en runbooks wilt behouden tijdens regionale storingen:

1. Selecteer een secundaire regio om te koppelen aan de geografische regio van uw primaire Automation-account.

2. Maak een Automation-account in de secundaire regio.

3. Exporteer uw runbooks in het primaire account als script bestanden.

4. Importeer de runbooks in uw Automation-account in de secundaire regio.

## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over beveiligde assets in Azure Automation [versleuteling van beveiligde assets in azure Automation](automation-secure-asset-encryption.md).

* Zie [actieve geo-replicatie maken en gebruiken](../azure-sql/database/active-geo-replication-overview.md)om meer te weten te komen over geo-replicatie.
