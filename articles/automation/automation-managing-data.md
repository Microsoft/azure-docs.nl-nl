---
title: Azure Automation beveiliging van gegevens
description: Dit artikel helpt u te leren hoe Azure Automation privacy beveiligt en uw gegevens beveiligt.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cb18cca782b85e608c3c7ddb001ecb03b86055f6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833522"
---
# <a name="management-of-azure-automation-data"></a>Beheer van Azure Automation-gegevens

Dit artikel bevat verschillende onderwerpen waarin wordt uitgelegd hoe gegevens worden beveiligd en beveiligd in Azure Automation omgeving.

## <a name="tls-12-enforcement-for-azure-automation"></a>TLS 1.2 afdwingen voor Azure Automation

We raden u ten zeerste aan om het gebruik van Transport Layer Security (TLS) 1.2 te configureren om de beveiliging van gegevens die naar Azure Automation worden doorbeveiligd. Hier volgt een lijst met methoden of clients die via HTTPS communiceren met de Automation-service:

* Webhook-aanroepen

* Hybrid Runbook Workers, die machines bevatten die worden beheerd door Updatebeheer en Wijzigingen bijhouden en inventaris.

* DSC-knooppunten

Oudere versies van TLS/Secure Sockets Layer (SSL) zijn kwetsbaar en hoewel ze momenteel nog werken om compatibiliteit met eerdere versies toe te staan, worden ze **niet aanbevolen.** We raden u niet aan uw agent expliciet in te stellen op het gebruik van TLS 1.2, tenzij dit nodig is, omdat het beveiligingsfuncties op platformniveau kan breken waarmee u automatisch nieuwere, veiligere protocollen kunt detecteren en gebruiken zodra deze beschikbaar komen, zoals TLS 1.3.

Zie Overzicht van Log [Analytics-agent - TLS 1.2](..//azure-monitor/agents/log-analytics-agent.md#tls-12-protocol)voor informatie over TLS 1.2-ondersteuning met de Log Analytics-agent voor Windows en Linux. Dit is een afhankelijkheid voor de Hybrid Runbook Worker-rol.

### <a name="platform-specific-guidance"></a>Platformspecifieke richtlijnen

|Platform/taal | Ondersteuning | Meer informatie |
| --- | --- | --- |
|Linux | Linux-distributies zijn vaak afhankelijk van [OpenSSL](https://www.openssl.org) voor TLS 1.2-ondersteuning.  | Controleer het [OpenSSL-wijzigingslogo](https://www.openssl.org/news/changelog.html) om te bevestigen dat uw versie van OpenSSL wordt ondersteund.|
| Windows 8.0 - 10 | Ondersteund en standaard ingeschakeld. | Bevestig dat u nog steeds de standaardinstellingen [gebruikt.](/windows-server/security/tls/tls-registry-settings)  |
| Windows Server 2012 - 2016 | Ondersteund en standaard ingeschakeld. | Bevestigen dat u nog steeds de [standaardinstellingen gebruikt](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 en Windows Server 2008 R2 SP1 | Ondersteund, maar niet standaard ingeschakeld. | Zie de [Transport Layer Security (TLS)-registerinstellingen](/windows-server/security/tls/tls-registry-settings) voor meer informatie over het inschakelen.  |

## <a name="data-retention"></a>Gegevensretentie

Wanneer u een resource in Azure Automation, wordt deze voor controledoeleinden vele dagen bewaard voordat deze permanent wordt verwijderd. U kunt de resource gedurende deze periode niet zien of gebruiken. Dit beleid is ook van toepassing op resources die deel uitmaken van een verwijderd Automation-account. Het retentiebeleid is van toepassing op alle gebruikers en kan momenteel niet worden aangepast. Als u echter gegevens voor een langere periode wilt bewaren, kunt u de Azure Automation doorsturen naar [Azure Monitor logboeken.](automation-manage-send-joblogs-log-analytics.md)

De volgende tabel bevat een overzicht van het bewaarbeleid voor verschillende resources.

| Gegevens | Beleid |
|:--- |:--- |
| Accounts |Een account wordt 30 dagen nadat een gebruiker het account heeft verwijderd definitief verwijderd. |
| Assets |Een asset wordt permanent verwijderd 30 dagen nadat een gebruiker deze heeft verwijderd, of 30 dagen nadat een gebruiker een account verwijdert dat de asset bevat. Assets omvatten variabelen, planningen, referenties, certificaten, Python 2-pakketten en verbindingen. |
| DSC-knooppunten |Een DSC-knooppunt wordt 30 dagen nadat de registratie van een Automation-account is verwijderd met behulp van Azure Portal of de cmdlet [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode) in Windows PowerShell. Een knooppunt wordt ook permanent verwijderd 30 dagen nadat een gebruiker het account met het knooppunt heeft verwijderd. |
| Taken |Een taak wordt 30 dagen na de wijziging definitief verwijderd, bijvoorbeeld nadat de taak is voltooid, is gestopt of is opgeschort. |
| Modules |Een module wordt 30 dagen nadat een gebruiker deze heeft verwijderd definitief verwijderd, of 30 dagen nadat een gebruiker het account heeft verwijderd dat de module bevat. |
| Knooppuntconfiguraties/MOF-bestanden |Een oude knooppuntconfiguratie wordt 30 dagen nadat een nieuwe knooppuntconfiguratie is gegenereerd permanent verwijderd. |
| Knooppuntrapporten |Een knooppuntrapport wordt 90 dagen nadat een nieuw rapport is gegenereerd voor dat knooppunt definitief verwijderd. |
| Runbooks |Een runbook wordt 30 dagen nadat een gebruiker de resource heeft verwijderd definitief verwijderd, of 30 dagen nadat een gebruiker het account met de resource<sup>1 heeft verwijderd.</sup> |

<sup>1</sup> Het runbook kan binnen het tijdvenster van 30 dagen worden hersteld door een ondersteuning voor Azure incident in te dienen bij Microsoft Azure Support. Ga naar de [ondersteuning voor Azure site en](https://azure.microsoft.com/support/options/) selecteer Een **ondersteuningsaanvraag indienen.**

## <a name="data-backup"></a>Gegevensback-up

Wanneer u een Automation-account in Azure verwijdert, worden alle objecten in het account verwijderd. De objecten omvatten runbooks, modules, configuraties, instellingen, taken en assets. Ze kunnen niet worden hersteld nadat het account is verwijderd. U kunt de volgende informatie gebruiken om een back-up te maken van de inhoud van uw Automation-account voordat u deze kunt verwijderen.

### <a name="runbooks"></a>Runbooks

U kunt uw runbooks exporteren naar scriptbestanden met behulp van de Azure Portal of de cmdlet [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition) in Windows PowerShell. U kunt deze scriptbestanden importeren in een ander Automation-account, zoals besproken in [Runbooks beheren in Azure Automation.](manage-runbooks.md)

### <a name="integration-modules"></a>Integratiemodules

U kunt integratiemodules niet exporteren vanuit Azure Automation, ze moeten buiten het Automation-account beschikbaar worden gesteld.

### <a name="assets"></a>Assets

U kunt geen Azure Automation: certificaten, verbindingen, referenties, planningen en variabelen. In plaats daarvan kunt u de Azure Portal en Azure-cmdlets gebruiken om de details van deze assets te noteren. Gebruik deze gegevens vervolgens om assets te maken die worden gebruikt door runbooks die u in een ander Automation-account importeert.

U kunt de waarden voor versleutelde variabelen of de wachtwoordvelden van referenties niet ophalen met behulp van cmdlets. Als u deze waarden niet weet, kunt u ze ophalen in een runbook. Zie Variabele assets in de Azure Automation voor het ophalen [van variabele Azure Automation.](shared-resources/variables.md) Zie Referentie-assets in Azure Automation voor meer informatie [over het ophalen van referentiewaarden.](shared-resources/credentials.md)

### <a name="dsc-configurations"></a>DSC-configuraties

U kunt uw DSC-configuraties exporteren naar scriptbestanden met behulp van de Azure Portal of de cmdlet [Export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration) in Windows PowerShell. U kunt deze configuraties importeren en gebruiken in een ander Automation-account.

## <a name="geo-replication-in-azure-automation"></a>Geo-replicatie in Azure Automation

Geo-replicatie is standaard in Azure Automation accounts. U kiest een primaire regio bij het instellen van uw account. De interne Automation-geo-replicatieservice wijst automatisch een secundaire regio toe aan het account. De service back-upt vervolgens continu van accountgegevens van de primaire regio naar de secundaire regio. De volledige lijst met primaire en secundaire regio's vindt u in Bedrijfscontinuïteit en herstel na noodherstel [(BCDR): Gekoppelde Azure-regio's.](../best-practices-availability-paired-regions.md)

De back-up die is gemaakt door de Automation-service voor geo-replicatie is een volledige kopie van Automation-assets, configuraties en iets als deze. Deze back-up kan worden gebruikt als de primaire regio uitvallen en gegevens verliest. In het onwaarschijnlijke geval dat gegevens voor een primaire regio verloren gaan, probeert Microsoft deze te herstellen.

> [!NOTE]
> Azure Automation slaat klantgegevens op in de regio die door de klant is geselecteerd. Voor BCDR worden gegevens voor alle regio's behalve Brazilië - zuid en Azië - zuidoost Azure Automation opgeslagen in een andere regio (gekoppelde Azure-regio). Alleen voor de regio Brazilië - zuid (Sao Paulo State) van de geografie Brazilië en de regio Azië - zuidoost (Singapore) van de Azië en Stille Oceaan-geografie slaan we Azure Automation-gegevens op in dezelfde regio om te voldoen aan de vereisten voor gegevensopslag in deze regio's.

De Geo-replicatieservice van Automation is niet rechtstreeks toegankelijk voor externe klanten als er een regionale storing is. Als u automation-configuratie en -runbooks wilt onderhouden tijdens regionale storingen:

1. Selecteer een secundaire regio om te koppelen aan de geografische regio van uw primaire Automation-account.

2. Maak een Automation-account in de secundaire regio.

3. Exporteert uw runbooks als scriptbestanden in het primaire account.

4. Importeer de runbooks naar uw Automation-account in de secundaire regio.

## <a name="next-steps"></a>Volgende stappen

* Zie Versleuteling van beveiligde activa in Azure Automation voor meer informatie over [beveiligde assets in Azure Automation](automation-secure-asset-encryption.md).

* Zie Creating and using active geo-replication (Actieve geo-replicatie maken en gebruiken) voor meer informatie over [geo-replicatie.](../azure-sql/database/active-geo-replication-overview.md)
