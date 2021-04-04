---
title: Azure Backup-ondersteunings matrix voor SQL Server back-up in virtuele machines van Azure
description: Hierin wordt een overzicht gegeven van de ondersteunings instellingen en beperkingen bij het maken van back-ups van SQL Server in azure Vm's met de Azure Backup-service.
ms.topic: conceptual
ms.date: 03/05/2020
ms.custom: references_regions
ms.openlocfilehash: 78436981c515b95ccda763d8ac916738b4364953
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97734790"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Ondersteunings matrix voor SQL Server back-up in virtuele machines van Azure

U kunt Azure Backup gebruiken om een back-up te maken van SQL Server-data bases in virtuele Azure-machines die worden gehost op het Microsoft Azure Cloud platform. In dit artikel vindt u een overzicht van de algemene ondersteunings instellingen en-beperkingen voor scenario's en implementaties van SQL Server back-up in azure Vm's.

## <a name="scenario-support"></a>Scenario-ondersteuning

**Ondersteuning** | **Details**
--- | ---
**Ondersteunde implementaties** | SQL Marketplace Azure-VM's en niet-Marketplace-VM's (SQL Server handmatig geïnstalleerd) worden ondersteund.
**Ondersteunde regio’s** | Australië-Zuid-Oost (ASE), Oost-Australië (AE), Australië-centraal (AC), Australië-centraal 2 (AC) <br> Brazilië - zuid (BRS)<br> Canada-centraal (CNC), Canada-oost (CE)<br> Zuid-Azië-oost (zee), Azië-oost (EA) <br> VS-Oost (EUS), VS-Oost 2 (EUS2), VS-West-Centraal (WCUS), VS-West (WUS); VS-West 2 (WUS 2) Noord-Centraal VS (NCUS) centraal VS (CUS) Zuid-Centraal (SCUS) <br> India-centraal (INC), India-Zuid (INS), India-West <br> Japan-Oost (JPE), Japan-West (JPW) <br> Korea-centraal (KRC), Korea-zuid (KRS) <br> Europa-noord (NE), Europa-west <br> UK-zuid (UKS), UK-west (UKW) <br> US Gov-Arizona, US Gov-Virginia, US Gov-Texas, US DoD-centraal, US DoD-oost <br> Duitsland-noord, Duitsland-west-centraal <br> Zwitserland-noord, Zwitserland-west <br> Frankrijk - centraal <br> China-oost, China-oost 2, China-noord, China-noord 2
**Ondersteunde besturingssystemen** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux wordt momenteel niet ondersteund.
**Ondersteunde SQL Server-versies** | SQL Server 2019, SQL Server 2017 zoals beschreven op de [pagina product levenscyclus zoeken](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 en SPS zoals beschreven op de [pagina product levenscyclus zoeken](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2 SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.<br><br>De versies van de Express-lokale Data Base worden niet ondersteund.
**Ondersteunde .NET-versies** | .NET Framework 4.5.2 of later geïnstalleerd op de VM

## <a name="feature-considerations-and-limitations"></a>Overwegingen en beperkingen van functies

|Instelling  |Maximumaantal |
|---------|---------|
|Aantal data bases dat kan worden beveiligd op een server (en in een kluis)    |   2000      |
|Ondersteunde database grootte (dit kan leiden tot prestatie problemen)   |   2 TB      |
|Aantal ondersteunde bestanden in een Data Base    |   1000      |

>[!NOTE]
> [Down load de gedetailleerde resource planner](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) om het geschatte aantal beveiligde data bases te berekenen dat wordt aanbevolen per server op basis van de VM-bronnen, de band breedte en het back-upbeleid.

* SQL Server back-up kan worden geconfigureerd in de Azure Portal of **Power shell**. CLI wordt niet ondersteund.
* De oplossing wordt ondersteund op beide soorten [implementaties](../azure-resource-manager/management/deployment-models.md) -Azure Resource Manager vm's en klassieke vm's.
* Alle back-uptypen (volledig/differentieel/logboek) en herstel modellen (eenvoudig/volledig of bulksgewijs geregistreerd) worden ondersteund.
* Voor **alleen-lezen** data bases: volledig en alleen-kopiëren volledige back-ups zijn de enige back-uptypen die worden ondersteund.
* Native compressie van SQL wordt ondersteund als dit expliciet door de gebruiker in het back-upbeleid is ingeschakeld. Azure Backup onderdrukking standaard instellingen op exemplaar niveau met de compressie-NO_COMPRESSION-component, afhankelijk van de waarde van dit besturings element, zoals ingesteld door de gebruiker.
* TDE-database back-up wordt ondersteund. Als u een met TDE versleutelde data base wilt herstellen naar een andere SQL Server, moet u [het certificaat eerst herstellen naar de doel server](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server). Back-upcompressie voor TDE-data bases voor SQL Server 2016 en nieuwere versies is beschikbaar, maar bij lagere overdrachts grootte, zoals [hier](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593)wordt uitgelegd.
* Back-up-en herstel bewerkingen voor spiegel databases en database momentopnamen worden niet ondersteund.
* SQL Server **-FCI (failover cluster instance)** wordt niet ondersteund.
* Het gebruik van meerdere back-upoplossingen om een back-up te maken van uw zelfstandige SQL Server exemplaar of de beschikbaarheids groep SQL always kan leiden tot back-upfouten. Dit wordt onthouden. Als u een back-up maakt van twee knoop punten van een beschikbaarheids groep met dezelfde of een andere oplossing, kan dit ook leiden tot back-upfouten.
* Wanneer beschikbaarheids groepen zijn geconfigureerd, worden er back-ups gemaakt van de verschillende knoop punten op basis van een aantal factoren. Hieronder vindt u een overzicht van het back-upgedrag voor een beschikbaarheids groep.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Back-up gedrag met AlwaysOn-beschikbaarheidsgroepen

Het is raadzaam om de back-up op slechts één knoop punt van een beschikbaarheids groep (AG) te configureren. Configureer altijd een back-up in dezelfde regio als het primaire knoop punt. Met andere woorden, u hebt altijd het primaire knoop punt nodig om aanwezig te zijn in de regio waar u de back-up gaat configureren. Als alle knoop punten van de AG zich in dezelfde regio bevinden waar de back-up is geconfigureerd, is er geen bezorgdheid.

#### <a name="for-cross-region-ag"></a>Voor de kruislingse regio AG

* Back-ups worden alleen uitgevoerd op de knoop punten die zich in dezelfde regio bevinden waar de back-up is geconfigureerd, ongeacht de voorkeurs instelling van de back-up. Dit komt doordat cross-regio back-ups niet worden ondersteund. Als u slechts twee knoop punten hebt en het secundaire knoop punt zich in de andere regio bevindt, worden de back-ups nog steeds uitgevoerd vanaf het primaire knoop punt (tenzij uw voor keur voor de back-up ' secundair ' is).
* Als een knoop punt wordt overgeschakeld naar een andere regio dan die waarvoor de back-up is geconfigureerd, mislukken de back-ups van de knoop punten in de regio waarvoor een failover is uitgevoerd.

Afhankelijk van de voor keuren voor back-ups en back-ups (volledig/differentieel/niet volledig), worden back-ups gemaakt van een bepaald knoop punt (primair/secundair).

#### <a name="backup-preference-primary"></a>Back-upvoorkeur: primair

**Type back-up** | **Knooppunt**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Primair
Copy-Only volledig |  Primair

#### <a name="backup-preference-secondary-only"></a>Back-upvoorkeur: alleen secundair

**Type back-up** | **Knooppunt**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Secundair
Copy-Only volledig |  Secundair

#### <a name="backup-preference-secondary"></a>Back-upvoorkeur: secundair

**Type back-up** | **Knooppunt**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Secundair
Copy-Only volledig |  Secundair

#### <a name="no-backup-preference"></a>Geen voorkeurs instelling voor back-up

**Type back-up** | **Knooppunt**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Secundair
Copy-Only volledig |  Secundair

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het maken van een back-up van [een SQL Server-Data Base](backup-azure-sql-database.md) die wordt uitgevoerd op een virtuele machine van Azure.