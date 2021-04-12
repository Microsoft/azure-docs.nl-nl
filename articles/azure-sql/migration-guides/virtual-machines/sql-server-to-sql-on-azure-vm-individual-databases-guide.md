---
title: 'SQL Server SQL Server in azure Virtual Machines: migratie handleiding'
description: In deze hand leiding leert u hoe u uw afzonderlijke SQL Server-data bases kunt migreren naar SQL Server op Azure Virtual Machines.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: e7fc4bacd73cec0fdab3117ada190fb7964b4282
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550894"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-virtual-machines"></a>Migratie handleiding: SQL Server SQL Server op Azure Virtual Machines

[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

In deze hand leiding leert u hoe u uw gebruikers databases kunt *detecteren*, *evalueren* en *migreren* van SQL Server naar een exemplaar van SQL Server op Azure virtual machines met behulp van back-ups maken en terugzetten en de verzen ding van het logboek registreren waarbij [Data Migration Assistant](/sql/dma/dma-overview) wordt gebruikt voor evaluatie.

U kunt SQL Server die on-premises of op worden uitgevoerd, migreren:

- SQL Server op virtuele machines (Vm's).
- Amazon Web Services (AWS) EC2.
- Amazon Relational Data Base service (AWS RDS).
- Compute Engine (Google Cloud Platform [GCP]).

Zie [SQL Server VM-migratie overzicht](sql-server-to-sql-on-azure-vm-migration-overview.md)voor meer informatie over extra migratie strategieën. Raadpleeg de [migratie handleidingen van Azure data base](https://docs.microsoft.com/data-migration)voor andere migratie handleidingen.

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="Diagram waarin een migratie proces stroom wordt weer gegeven.":::

## <a name="prerequisites"></a>Vereisten

Voor de migratie naar SQL Server in azure Virtual Machines zijn de volgende resources vereist:

- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595).
- Een [Azure migrate-project](../../../migrate/create-manage-projects.md).
- Een voor bereide doel [SQL Server op Azure virtual machines](../../virtual-machines/windows/create-sql-vm-portal.md) exemplaar dat dezelfde of meer versie heeft dan de SQL Server bron.
- [Connectiviteit tussen Azure en on-premises](/azure/architecture/reference-architectures/hybrid-networking).
- [Een geschikte migratie strategie kiezen](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).

## <a name="pre-migration"></a>Premigratie

Voordat u met de migratie begint, moet u de topologie van uw SQL-omgeving ontdekken en de uitvoer baarheid van uw beoogde migratie beoordelen.

### <a name="discover"></a>Ontdekken

Azure Migrate beoordeling van de geschiktheid van de migratie van on-premises computers, voert de prestaties op basis van een grootte en biedt kosten ramingen voor on-premises uitvoering. Als u de migratie wilt plannen, gebruikt u Azure Migrate om [bestaande gegevens bronnen te identificeren en Details over de functies](../../../migrate/concepts-assessment-calculation.md) die uw SQL Server-instanties gebruiken. Dit proces omvat het scannen van het netwerk om al uw SQL Server exemplaren in uw organisatie te identificeren met de versie en de functies die in gebruik zijn.

> [!IMPORTANT]
> Wanneer u een virtuele doel machine van Azure voor uw SQL Server-exemplaar kiest, moet u rekening houden met de [richt lijnen voor prestaties voor SQL Server op Azure virtual machines](../../virtual-machines/windows/performance-guidelines-best-practices.md).

Zie voor meer detectie hulpprogramma's de [Services en hulpprogram ma's](../../../dms/dms-tools-matrix.md#business-justification-phase) die beschikbaar zijn voor scenario's voor gegevens migratie.

### <a name="assess"></a>Evalueren

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Nadat u alle gegevens bronnen hebt gedetecteerd, gebruikt u [Data Migration Assistant](/sql/dma/dma-overview) om on-premises SQL Server instanties te evalueren die worden gemigreerd naar een exemplaar van SQL Server op Azure virtual machines om inzicht te krijgen in de hiaten tussen de bron-en doel exemplaren.

> [!NOTE]
> Als u de versie van SQL Server _niet_ bijwerkt, slaat u deze stap over en gaat u naar de sectie [migrate](#migrate) .

#### <a name="assess-user-databases"></a>Gebruikers databases beoordelen

Data Migration Assistant helpt uw migratie naar een modern gegevens platform door compatibiliteits problemen te detecteren die van invloed kunnen zijn op de database functionaliteit in uw nieuwe versie van SQL Server. Data Migration Assistant raadt verbeteringen aan prestaties en betrouw baarheid voor uw doel omgeving aan en u kunt ook uw schema, gegevens en aanmeldings objecten van de bron server naar de doel server verplaatsen.

Zie [evaluatie](/sql/dma/dma-migrateonpremsql)voor meer informatie.

> [!IMPORTANT]
>Op basis van het type evaluatie kunnen de vereiste machtigingen op de bron SQL Server verschillen:
   > - Voor de *functie parity* Advisor moeten de referenties die zijn gegeven om verbinding te maken met de bron SQL Server Data Base lid zijn van de serverrol *sysadmin* .
   > - Voor de *compatibiliteits problemen* Advisor moeten de verstrekte referenties mini maal `CONNECT SQL` , `VIEW SERVER STATE` en `VIEW ANY DEFINITION` machtigingen hebben.
   > - Data Migration Assistant markeert de machtigingen die vereist zijn voor de gekozen Advisor voordat de evaluatie wordt uitgevoerd.

#### <a name="assess-the-applications"></a>De toepassingen evalueren

Normaal gesp roken opent een toepassings laag gebruikers databases om gegevens te behouden en te wijzigen. Data Migration Assistant kunt de Gegevenstoegangslaag van een toepassing op twee manieren beoordelen:

- Door vastgelegde [uitgebreide gebeurtenissen](/sql/relational-databases/extended-events/extended-events) of [SQL Server Profiler traceringen](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) van uw gebruikers databases te gebruiken. U kunt ook de [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-capture-trace) gebruiken om een tracerings logboek te maken dat ook kan worden gebruikt voor een/B-test.
- Met behulp van de [Data Access Migration Toolkit (preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit), waarmee SQL-query's in de code kunnen worden gedetecteerd en geëvalueerd, en wordt gebruikt om de bron code van de toepassing te migreren van het ene database platform naar het andere. Dit hulp programma ondersteunt populaire bestands typen als C#, Java, XML en tekst zonder opmaak. Zie het blog bericht [Data Migration Assistant gebruiken](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) voor meer informatie over het uitvoeren van een evaluatie van de Data Access-migratie Toolkit.

Gebruik Data Migration Assistant om vastgelegde tracerings bestanden of Toolkit bestanden voor Data Access Migration te [importeren](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess) tijdens de evaluatie van gebruikers databases.

#### <a name="assessments-at-scale"></a>Evaluaties op schaal

Als u meerdere servers hebt waarvoor een Data Migration Assistant beoordeling vereist is, kunt u het proces automatiseren met behulp van de [opdracht regel interface](/sql/dma/dma-commandline). Met de-interface kunt u evaluatie opdrachten vooraf voorbereiden voor elk SQL Server exemplaar in het bereik voor migratie.

Data Migration Assistant beoordelingen kunnen nu worden [geconsolideerd in azure migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb)voor een overzicht van de rapportage over grote voor delen.

#### <a name="refactor-databases-with-data-migration-assistant"></a>Refactory-data bases met Data Migration Assistant

Op basis van de Data Migration Assistant evaluatie resultaten hebt u mogelijk een reeks aanbevelingen om ervoor te zorgen dat uw gebruikers databases goed werken en na de migratie correct functioneren. Data Migration Assistant biedt Details over de betrokken objecten en bronnen voor het oplossen van elk probleem. Zorg ervoor dat alle belang rijke wijzigingen en gedrags wijzigingen zijn opgelost voordat u begint met de productie migratie.

Voor afgeschafte functies kunt u ervoor kiezen om uw gebruikers databases uit te voeren in de oorspronkelijke [compatibiliteits](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) modus als u wilt voor komen dat u deze wijzigingen aanbrengt en de migratie wilt versnellen. Met deze actie wordt voor komen dat [uw database compatibiliteit wordt bijgewerkt](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades) totdat de afgeschafte items zijn opgelost.

U moet alle Data Migration Assistant oplossingen scripteren en deze Toep assen op de doel SQL Server-Data Base tijdens de fase [na de migratie](#post-migration) .

> [!CAUTION]
> Niet alle versies van SQL Server ondersteunen alle compatibiliteits modi. Controleer of uw [doel-SQL Server-versie](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) de gekozen database compatibiliteit ondersteunt. SQL Server 2019 biedt bijvoorbeeld geen ondersteuning voor data bases met niveau-90-compatibiliteit (SQL Server 2005). Voor deze data bases is ten minste een upgrade van het compatibiliteits niveau 100 vereist.
>

## <a name="migrate"></a>Migrate

Nadat u de stappen voorafgaand aan de migratie hebt voltooid, bent u klaar om de gebruikers databases en-onderdelen te migreren. Migreer uw data bases met behulp van uw voorkeurs [migratie methode](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).

De volgende secties bevatten stappen voor het uitvoeren van een migratie met behulp van back-ups maken en herstellen of een minimale downtime-migratie met behulp van backup en Restore samen met logboek verzending.

### <a name="backup-and-restore"></a>Back-ups en herstellen

Een standaard migratie uitvoeren met back-up en herstel:

1. Stel de verbinding met SQL Server op Azure Virtual Machines op basis van uw vereisten. Zie [verbinding maken met een SQL Server virtuele machine op Azure (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md)voor meer informatie.
1. Toepassingen onderbreken of stoppen die gebruikmaken van data bases die bedoeld zijn voor migratie.
1. Zorg ervoor dat gebruikers databases niet actief zijn door gebruik te maken van de [modus voor één gebruiker](/sql/relational-databases/databases/set-a-database-to-single-user-mode).
1. Voer een volledige back-up van de data base uit naar een on-premises locatie.
1. Kopieer uw on-premises back-upbestanden naar uw VM met behulp van een extern bureau blad, [Azure Data Explorer](/azure/data-explorer/data-explorer-overview)of het [AZCopy-opdracht regel programma](../../../storage/common/storage-use-azcopy-v10.md). (Meer dan 2 TB back-ups worden aanbevolen.)
1. Herstel volledige back-ups van de Data Base naar de SQL Server op Azure Virtual Machines.

### <a name="log-shipping-minimize-downtime"></a>Logboek verzending (downtime minimaliseren)

Als u een minimale downtime wilt migreren met behulp van back-ups en terugzetten en de verzen ding van het logboek:

1. Stel de connectiviteit in voor de SQL Server op Azure Virtual Machines op basis van uw vereisten. Zie [verbinding maken met een SQL Server virtuele machine op Azure (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md)voor meer informatie.
1. Zorg ervoor dat de on-premises gebruikers databases die moeten worden gemigreerd, zich in het volledige of bulksgewijs geregistreerde herstel model bevinden.
1. Voer een volledige back-up van de Data Base naar een on-premises locatie en wijzig bestaande back-uptaken voor volledige data base om het sleutel woord [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) te gebruiken om de logboek keten te bewaren.
1. Kopieer uw on-premises back-upbestanden naar uw VM met behulp van een extern bureau blad, [Azure Data Explorer](/azure/data-explorer/data-explorer-overview)of het [AZCopy-opdracht regel programma](../../../storage/common/storage-use-azcopy-v10.md). (Meer dan 1 TB back-ups worden aanbevolen.)
1. Herstel volledige back-ups van de Data Base op SQL Server op Azure Virtual Machines.
1. Stel de [back-upfunctie voor logboeken](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) in tussen de on-premises data base en SQL Server op Azure virtual machines. Zorg ervoor dat u de data bases niet opnieuw initialiseert omdat deze taak al in de vorige stappen is voltooid.
1. Knippen naar de doel server.
   1. Toepassingen onderbreken of stoppen door gebruik te maken van de data bases die moeten worden gemigreerd.
   1. Zorg ervoor dat gebruikers databases niet actief zijn door gebruik te maken van de [modus voor één gebruiker](/sql/relational-databases/databases/set-a-database-to-single-user-mode).
   1. Wanneer u klaar bent, voert u een back-upbewerking voor [logboek verzending van](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) on-premises data bases uit om te SQL Server op Azure virtual machines.

### <a name="migrate-objects-outside-user-databases"></a>Objecten buiten gebruikers databases migreren

Meer SQL Server objecten zijn mogelijk vereist voor de naadloze werking van uw gebruikers databases na migratie.

De volgende tabel bevat een lijst met onderdelen en aanbevolen migratie methoden die kunnen worden uitgevoerd vóór of na de migratie van uw gebruikers databases.

| **Functie** | **Onderdeel** | **Migratie methoden** |
| --- | --- | --- |
| **Databases** | Model | Script met SQL Server Management Studio. |
|| TempDB | Plan tempDB te verplaatsen naar de [tijdelijke schijf van de virtuele machine van Azure (SSD)](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk)voor de beste prestaties. Zorg ervoor dat u een VM-grootte kiest die een voldoende lokale SSD heeft om uw tempDB te kunnen verwerken. |
|| Gebruikers databases met FileStream | Gebruik de [back-up-en herstel](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) methoden voor migratie. Data Migration Assistant biedt geen ondersteuning voor data bases met FileStream. |
| **Beveiliging** | SQL Server-en Windows-aanmeldingen | Gebruik Data Migration Assistant om [Gebruikers aanmeldingen te migreren](/sql/dma/dma-migrateserverlogins). |
|| SQL Server rollen | Script met SQL Server Management Studio. |
|| Cryptografische providers | U wordt aangeraden [om te converteren naar gebruik Azure Key Vault](../../virtual-machines/windows/azure-key-vault-integration-configure.md). Deze procedure maakt gebruik van de [resource provider](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)van de SQL-VM. |
| **Server objecten** | Back-upapparaten | Vervang door de back-up van de data base met behulp van [Azure backup](../../../backup/backup-sql-server-database-azure-vms.md)of schrijf back-ups naar [Azure Storage](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) (SQL Server 2012 SP1 Cu2 +). Deze procedure maakt gebruik van de [resource provider](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)van de SQL-VM.|
|| Gekoppelde servers | Script met SQL Server Management Studio. |
|| Server triggers | Script met SQL Server Management Studio. |
| **Replicatie** | Lokale publicaties | Script met SQL Server Management Studio. |
|| Lokale abonnees | Script met SQL Server Management Studio. |
| **Poly base** | PolyBase | Script met SQL Server Management Studio. |
| **Beheer** | Database-e-mail | Script met SQL Server Management Studio. |
| **SQL Server Agent** | Taken | Script met SQL Server Management Studio. |
|| Waarschuwingen | Script met SQL Server Management Studio. |
|| Operators | Script met SQL Server Management Studio. |
|| Proxy's | Script met SQL Server Management Studio. |
| **Besturingssysteem** | Bestanden, bestands shares | Noteer alle andere bestanden of bestands shares die worden gebruikt door uw SQL-servers en repliceer op het Azure Virtual Machines doel. |

## <a name="post-migration"></a>Postmigratie

Nadat u de migratie fase hebt voltooid, moet u een reeks taken na migratie volt ooien om ervoor te zorgen dat alles zo soepel en efficiënt mogelijk werkt.

### <a name="remediate-applications"></a>Toepassingen herstellen

Nadat de gegevens zijn gemigreerd naar de doel omgeving, moeten alle toepassingen die de bron voorheen hebben verbruikt, het doel gaan gebruiken. Voor het uitvoeren van deze taak is het mogelijk dat in sommige gevallen wijzigingen in de toepassingen zijn aangebracht.

Pas de oplossingen toe die door Data Migration Assistant worden aanbevolen voor gebruikers databases. U moet deze oplossingen scripteren om consistentie te garanderen en automatisering mogelijk te maken.

### <a name="perform-tests"></a>Tests uitvoeren

De test benadering voor database migratie bestaat uit de volgende activiteiten:

1. **Validatie tests ontwikkelen**: als u de database migratie wilt testen, moet u SQL-query's gebruiken. Validatie query's maken om uit te voeren op zowel de bron-als de doel database. Uw validatie query's moeten betrekking hebben op het bereik dat u hebt gedefinieerd.
1. **Een test omgeving instellen**: de test omgeving moet een kopie van de bron database en de doel database bevatten. Zorg ervoor dat u de test omgeving isoleert.
1. **Validatie tests uitvoeren**: voer validatie tests uit op de bron en het doel en analyseer vervolgens de resultaten.
1. **Prestatie testen uitvoeren**: prestatie tests uitvoeren op basis van de bron en het doel, en vervolgens de resultaten analyseren en vergelijken.

> [!TIP]
> Gebruik de [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) om te helpen bij het evalueren van de doel-SQL Server prestaties.

### <a name="optimize"></a>Optimaliseren

De fase na de migratie is van cruciaal belang voor het afstemmen van alle problemen met gegevens nauwkeurigheid, het controleren van de volledigheid en het adresseren van potentiële prestatie problemen met de werk belasting.

Zie voor meer informatie over deze problemen en de stappen om deze te verhelpen:

- [Hand leiding voor validatie na migratie en optimalisatie](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Prestaties afstemmen op virtuele machines van Azure SQL](../../virtual-machines/windows/performance-guidelines-best-practices.md)
- [Azure cost Optimization Center](https://azure.microsoft.com/overview/cost-optimization/)

## <a name="next-steps"></a>Volgende stappen

- Als u de beschik baarheid van services wilt controleren die van toepassing zijn op SQL Server, raadpleegt u het [Azure Global Infrastructure Center](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database).
- Zie [Services en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor een matrix van micro soft-Services en hulpprogram ma's van derden die beschikbaar zijn om u te helpen bij het maken van verschillende scenario's voor data base-en gegevens migratie en speciale taken.
- Zie voor meer informatie over Azure SQL:
   - [Implementatieopties](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server op virtuele machines in Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Berekening van de totale eigendoms kosten van Azure (TCO)](https://azure.microsoft.com/pricing/tco/calculator/)

- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties:
   - [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   - [Aanbevolen procedures voor het berekenen en aanpassen van werk belastingen voor migratie naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- Zie voor meer informatie over licentie verlening:
   - [Bring Your Own License met de Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Krijg gratis uitgebreide ondersteuning voor SQL Server 2008 en SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- Zie [Data Access Migration Toolkit (preview) voor informatie](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)over het beoordelen van de Application Access-laag.
- Zie [overzicht van database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview)voor informatie over het uitvoeren van een/B-test voor de gegevenslaag van de gegevens toegang.
