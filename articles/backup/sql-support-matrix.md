---
title: Azure Backup ondersteuningsmatrix voor SQL Server Back-up in Azure-VM's
description: Biedt een overzicht van ondersteuningsinstellingen en beperkingen bij het maken van back-SQL Server in Azure-VM's met de Azure Backup service.
ms.topic: conceptual
ms.date: 04/07/2021
ms.custom: references_regions
ms.openlocfilehash: bcbac4f6a91ad77d21eb6274aa03d251b8fbfe7c
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515053"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Ondersteuningsmatrix voor SQL Server Back-up in Azure-VM's

U kunt deze Azure Backup back-up te maken SQL Server databases in Azure-VM's die worden gehost op Microsoft Azure cloudplatform. Dit artikel bevat een overzicht van de algemene ondersteuningsinstellingen en beperkingen voor scenario's en implementaties van SQL Server Back-up in Azure-VM's.

## <a name="scenario-support"></a>Scenario-ondersteuning

**Ondersteuning** | **Details**
--- | ---
**Ondersteunde implementaties** | SQL Marketplace Azure-VM's en niet-Marketplace-VM's (SQL Server handmatig geïnstalleerd) worden ondersteund.
**Ondersteunde regio’s** | Australië - zuidoost (ASE), Australië - oost (AE), Australië - centraal (AC), Australië - centraal 2 (AC) <br> Brazilië - zuid (BRS)<br> Canada - centraal (CNC), Canada - oost (CE)<br> South Azië - oost (SEA), Azië - oost (EA) <br> VS - oost (EUS), VS - oost 2 (EUS2), VS - west-centraal (WCUS), VS - west (WUS); VS - west 2 (WUS 2) VS - noord-centraal (NCUS) VS - centraal (CUS) VS - zuid-centraal (SCUS) <br> India - centraal (INC), India - zuid (INS), India - west <br> Japan - oost (JPE), Japan - west (JPW) <br> Korea - centraal (KRC), Korea - zuid (KRS) <br> Europa - noord (NE), Europa - west <br> VK - zuid (UKS), VK - west (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Texas, US DoD Central, US DoD East <br> Duitsland - noord, Duitsland - west-centraal <br> Zwitserland - noord, Zwitserland - west <br> Frankrijk - centraal <br> China - oost, China - oost 2, China - noord, China - noord 2
**Ondersteunde besturingssystemen** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux wordt momenteel niet ondersteund.
**Ondersteunde SQL Server-versies** | SQL Server 2019 SQL Server 2017, zoals beschreven [](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)op de pagina Levenscyclus van het zoekproduct, SQL Server 2016 en SPs zoals beschreven op de pagina Levenscyclus van het product [zoeken,](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.<br><br>Lokale Express DB-versies worden niet ondersteund.
**Ondersteunde .NET-versies** | .NET Framework 4.5.2 of hoger geïnstalleerd op de VM

## <a name="feature-considerations-and-limitations"></a>Overwegingen en beperkingen voor functies

|Instelling  |Maximumaantal |
|---------|---------|
|Aantal databases dat kan worden beveiligd op een server (en in een kluis)    |   2000      |
|Ondersteunde databasegrootte (verder kunnen er prestatieproblemen optreden)   |   6 TB*      |
|Aantal ondersteunde bestanden in een database    |   1000      |

_*De limiet voor de databasegrootte is afhankelijk van de gegevensoverdrachtsnelheid die wordt ondersteund en de configuratie van de back-uptijdlimiet. Dit is niet de harde limiet. [Meer informatie over de](#backup-throughput-performance) prestaties van back-updoorvoer._

* SQL Server back-up kan worden geconfigureerd in de Azure Portal of **PowerShell.** CLI wordt niet ondersteund.
* De oplossing wordt ondersteund voor beide soorten [implementaties:](../azure-resource-manager/management/deployment-models.md) Azure Resource Manager virtuele en klassieke VM's.
* Alle back-uptypen (volledig/differentieel/logboek) en herstelmodellen (eenvoudig/volledig/bulksgewijs geregistreerd) worden ondersteund.
* Voor **alleen-lezen** databases: volledige en alleen-kopiëren volledige back-ups zijn de enige ondersteunde back-uptypen.
* Systeemeigen SQL-compressie wordt ondersteund als deze expliciet is ingeschakeld door de gebruiker in het back-upbeleid. Azure Backup overschrijven standaardinstellingen op exemplaarniveau met de component COMPRESSION/NO_COMPRESSION, afhankelijk van de waarde van dit besturingselement zoals ingesteld door de gebruiker.
* TDE: back-ups van ingeschakelde databases worden ondersteund. Als u een met TDE versleutelde database wilt herstellen naar een andere SQL Server, moet u eerst het certificaat herstellen [naar de doelserver](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server). Back-upcompressie voor TDE-databases voor SQL Server 2016 en nieuwere versies is beschikbaar, maar met een lagere overdrachtsgrootte, zoals hier wordt [uitgelegd.](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593)
* Back-up- en herstelbewerkingen voor mirror-databases en databasemomentopnamen worden niet ondersteund.
* SQL Server **Failover Cluster Instance (FCI)** wordt niet ondersteund.
* Het gebruik van meer dan één back-upoplossing voor het maken van een back-up van uw zelfstandige SQL Server-exemplaar of SQL Always on-beschikbaarheidsgroep kan leiden tot een back-upfout. Laat dit niet doen. Het maken van een back-up van twee knooppunten van een beschikbaarheidsgroep afzonderlijk met dezelfde of verschillende oplossingen kan ook leiden tot een back-upfout.
* Wanneer beschikbaarheidsgroepen zijn geconfigureerd, worden back-ups gemaakt van de verschillende knooppunten op basis van een aantal factoren. Het back-upgedrag voor een beschikbaarheidsgroep wordt hieronder samengevat.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Back-up gedrag met AlwaysOn-beschikbaarheidsgroepen

U wordt aangeraden de back-up slechts op één knooppunt van een beschikbaarheidsgroep (AG) te configureren. Configureer altijd back-ups in dezelfde regio als het primaire knooppunt. Met andere woorden, het primaire knooppunt moet altijd aanwezig zijn in de regio waar u de back-up configureert. Als alle knooppunten van de agg zich in dezelfde regio als waar de back-up is geconfigureerd, is er geen probleem.

#### <a name="for-cross-region-ag"></a>Voor ag in regio-overschrijdende regio's

* Ongeacht de back-upvoorkeur worden back-ups alleen uitgevoerd vanaf de knooppunten die zich in dezelfde regio waarin de back-up is geconfigureerd. Dit komt doordat back-ups tussen regio's niet worden ondersteund. Als u slechts twee knooppunten hebt en het secundaire knooppunt zich in de andere regio, blijven de back-ups worden uitgevoerd vanaf het primaire knooppunt (tenzij uw back-upvoorkeur 'alleen secundair' is).
* Als voor een knooppunt een failback wordt gemaakt naar een andere regio dan de regio waarin de back-up is geconfigureerd, mislukken back-ups op de knooppunten in de fail over-over-regio.

Afhankelijk van de back-upvoorkeur en back-upstypen (volledig/differentieel/logboek/alleen-kopiëren) worden back-ups gemaakt van een bepaald knooppunt (primair/secundair).

#### <a name="backup-preference-primary"></a>Back-upvoorkeur: Primair

**Type back-up** | **Knooppunt**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Primair
Copy-Only volledig |  Primair

#### <a name="backup-preference-secondary-only"></a>Back-upvoorkeur: Alleen secundair

**Type back-up** | **Knooppunt**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Secundair
Copy-Only Volledig |  Secundair

#### <a name="backup-preference-secondary"></a>Back-upvoorkeur: Secundair

**Type back-up** | **Knooppunt**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Secundair
Copy-Only Volledig |  Secundair

#### <a name="no-backup-preference"></a>Geen back-upvoorkeur

**Type back-up** | **Knooppunt**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Secundair
Copy-Only Volledig |  Secundair

## <a name="backup-throughput-performance"></a>Prestaties van back-updoorvoer

Azure Backup biedt ondersteuning voor een consistente overdrachtssnelheid van 200 Mbps voor volledige en differentiële back-ups van grote SQL-databases (van 500 GB). Als u de optimale prestaties wilt gebruiken, moet u ervoor zorgen dat:

- De onderliggende VM (met het SQL Server-exemplaar dat als host voor de database wordt gebruikt) is geconfigureerd met de vereiste netwerkdoorvoer. Als de maximale doorvoer van de VM kleiner is dan 200 Mbps, kunnen Azure Backup gegevens niet met de optimale snelheid overdragen.<br></br>Op de schijf met de databasebestanden moet ook voldoende doorvoer zijn ingericht. [Meer informatie over](../virtual-machines/disks-performance.md) schijfdoorvoer en prestaties in Azure-VM's. 
- Processen die in de VM worden uitgevoerd, verbruiken niet de VM-bandbreedte. 
- De back-upschema's zijn verdeeld over een subset van databases. Meerdere back-ups die gelijktijdig op een VM worden uitgevoerd, delen het netwerkverbruik tussen de back-ups. [Meer informatie over](faq-backup-sql-server.yml#can-i-control-how-many-concurrent-backups-run-on-the-sql-server-) het bepalen van het aantal gelijktijdige back-ups.

>[!NOTE]
> [Download de gedetailleerde Resource Planner om het](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) geschatte aantal beveiligde databases te berekenen dat per server wordt aanbevolen op basis van de VM-resources, bandbreedte en het back-upbeleid.

## <a name="next-steps"></a>Volgende stappen

Leer hoe u [een back-up SQL Server database](backup-azure-sql-database.md) die wordt uitgevoerd op een Virtuele Azure-VM.