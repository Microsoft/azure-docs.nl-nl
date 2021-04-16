---
title: Problemen met Synapse Studio-connectiviteit oplossen
description: Problemen met Azure Synapse Studio oplossen met behulp van PowerShell
author: saveenr
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 2bbdaef9268239005cdf5ea7fbee6734dadc8113
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566245"
---
# <a name="troubleshoot-synapse-studio-connectivity-with-powershell"></a>Problemen met Synapse Studio powershell oplossen

Azure Synapse Studio is afhankelijk van een set web-API-eindpunten die goed werken. Deze handleiding helpt u bij het identificeren van oorzaken van verbindingsproblemen wanneer u:
- het configureren van uw lokale netwerk (zoals het netwerk achter een bedrijfsfirewall) voor toegang tot Azure Synapse Studio.
- connectiviteitsproblemen ondervindt met Azure Synapse Studio.

## <a name="prerequisite"></a>Vereiste

* PowerShell 5.0 of hoger in Windows, of
* PowerShell Core versie 6.0 of hoger in Windows of Linux.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

Klik met de rechtermuisknop op de volgende koppeling en selecteer Doel opslaan als:

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

U kunt de koppeling ook rechtstreeks openen en het geopende scriptbestand opslaan. Sla het adres van de bovenstaande koppeling niet op, omdat dit in de toekomst kan veranderen.

Klik in Verkenner met de rechtermuisknop op het gedownloade scriptbestand en selecteer 'Uitvoeren met PowerShell'.

![Gedownload scriptbestand uitvoeren met PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Wanneer u hier om wordt gevraagd, voert u Azure Synapse naam van de werkruimte in die momenteel een probleem ondervindt of die u wilt testen op connectiviteit en drukt u op Enter.

![Voer de naam van de werkruimte in](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

De diagnostische sessie wordt gestart. Wacht tot het is voltooid.

![Wacht tot de diagnose is voltooid](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

Uiteindelijk wordt een diagnosesamenvatting weergegeven. Als uw pc geen verbinding kan maken met een of meer van de eindpunten, worden er enkele suggesties in de sectie Samenvatting gegeven.

![Diagnostische samenvatting controleren](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Daarnaast wordt er een diagnostisch logboekbestand voor deze sessie gegenereerd in dezelfde map als het script voor probleemoplossing. De locatie wordt weergegeven in de sectie 'Algemene tips' `D:\TestAzureSynapse_2020....log` (). U kunt dit bestand indien nodig verzenden naar technische ondersteuning.

Als u een netwerkbeheerder bent en uw firewallconfiguratie voor Azure Synapse Studio afstemt, kunnen de technische gegevens die worden weergegeven boven de sectie Samenvatting helpen.

* Alle testitems (aanvragen) die zijn gemarkeerd met Geslaagd betekenen dat ze connectiviteitstests hebben doorstaan, ongeacht de HTTP-statuscode.
 Voor de mislukte aanvragen wordt de reden in het geel weergegeven, zoals `NamedResolutionFailure` of `ConnectFailure` . Deze redenen kunnen u helpen na te gaan of er onjuiste configuraties zijn met uw netwerkomgeving.


## <a name="next-steps"></a>Volgende stappen
Als u het probleem niet kunt oplossen met de vorige stappen, maakt [u een ondersteuningsticket](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md)aan.