---
title: Verbinding maken met Azure Database for MySQL met behulp van dbForge Studio voor MySQL
description: In dit artikel wordt beschreven hoe u verbinding maakt met Azure Database for MySQL server via dbForge Studio voor MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 1c85a07a3d61c80f3871f04c399263a8e210254e
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010788"
---
# <a name="connect-to-azure-database-for-mysql-using-dbforge-studio-for-mysql"></a>Verbinding maken met Azure Database for MySQL met behulp van dbForge Studio voor MySQL

Verbinding maken met Azure Database for MySQL met behulp [van DbForge Studio voor mysql](https://www.devart.com/dbforge/mysql/studio/):

1. Selecteer nieuwe verbinding in het menu Data Base.

2. Geef een hostnaam en aanmeldings referenties op.

3. Selecteer de knop verbinding testen om de configuratie te controleren.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Azure-verbinding":::

## <a name="migrate-a-database-using-the-backup-and-restore-functionality"></a>Een Data Base migreren met de functionaliteit voor back-up en herstel

Met de Studio kunnen data bases op verschillende manieren worden gemigreerd naar Azure, waarvan de keuze alleen afhankelijk is van uw behoeften. Als u de gehele Data Base wilt verplaatsen, kunt u het beste de functionaliteit voor back-up en herstel gebruiken. In dit voor beeld wordt de *sakila* -data base die zich op mysql-server bevindt, naar Azure database for MySQL gemigreerd. De logica achter het migratie proces met behulp van de functionaliteit voor back-up en herstel van dbForge Studio voor MySQL is het maken van een back-up van de MySQL-data base en deze vervolgens te herstellen in Azure Database for MySQL.

### <a name="back-up-the-database"></a>Back-up van de data base maken

1. Ga in het menu Data Base naar back-up en herstellen en selecteer back-updatabase. De wizard Data Base-back-up wordt weer gegeven.

2. Selecteer op het tabblad Back-upinhoud van de wizard Back-up van data base de database objecten waarvan u een back-up wilt maken.

3. Configureer op het tabblad Opties het back-upproces aan uw vereisten.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="Back-up maken van wizard opties":::

4. Vervolgens geeft u de opties voor het verwerken van fouten en logboek registratie op.

5. Selecteer back-up.

### <a name="restore-the-database"></a>De data base herstellen

1. Maak verbinding met Azure voor de Data Base voor MySQL zoals hierboven wordt beschreven.

2. Klik met de rechter muisknop op de hoofd code van Database Explorer, wijs back-up en herstellen aan en selecteer data base herstellen.

3. Selecteer in de wizard Data Base terugzetten een bestand met een back-up van de data base.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="Stap herstellen":::

4. Selecteer Terugzetten.

5. Controleer het resultaat.

## <a name="migrate-a-database-using-the-copy-databases-functionality"></a>Een Data Base migreren met behulp van de functie Copy data bases

De functionaliteit voor het kopiëren van data bases is vergelijkbaar met het maken en herstellen van back-ups, behalve dat er niet twee stappen nodig zijn om een Data Base te migreren. En wat meer is, met behulp van de functie kunt u twee of meer data bases in één gaat overdragen. De functionaliteit voor het kopiëren van data bases is alleen beschikbaar in de Enter prise-editie van dbForge Studio voor MySQL.
In dit voor beeld wordt de *world_x* data base van MySQL-server naar Azure database for MySQL gemigreerd.
Een Data Base migreren met de functionaliteit voor het kopiëren van data bases:

1. Selecteer data bases kopiëren in het menu Data Base. 

2. Op het tabblad Copy data bases die wordt weer gegeven, geeft u de bron-en doel verbinding op en selecteert u de data base (s) die moeten worden gemigreerd. U voert Azure MySQL-verbinding in en selecteert de *world_x* data base. Selecteer de groene pijl om het proces te initiëren.

3. Controleer het resultaat.

Als gevolg van onze data base-migratie, is de *world_x* -data base gepubliceerd in azure mysql.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="Resultaat van kopiëren van data bases":::

## <a name="migrate-a-database-using-schema-and-data-compare-tools"></a>Een Data Base migreren met behulp van hulpprogram ma's voor schema en gegevens vergelijking

dbForge Studio voor MySQL bevat een aantal hulpprogram ma's waarmee MySQL-data bases kunnen worden gemigreerd, MySQL-schema's en/of-gegevens naar Azure. De keuze van de functionaliteit is afhankelijk van uw behoeften en de vereisten van uw project. Als u een Data Base selectief wilt verplaatsen, dat wil zeggen dat u bepaalde MySQL-tabellen naar Azure migreert, kunt u het beste gebruikmaken van de functionaliteit schema en gegevens vergelijking.
In dit voor beeld migreren we de *wereld wijde* data base die zich op mysql-server bevindt, naar Azure database for MySQL. De logica achter het migratie proces met behulp van schema-en gegevens vergelijkings functionaliteit van dbForge Studio voor MySQL is het maken van een lege data base in Azure Database for MySQL, synchroniseer deze met de vereiste MySQL-data base eerst met behulp van schema vergelijken en vervolgens met behulp van data Compare. Op deze manier worden MySQL-schema's en-gegevens nauw keurig naar Azure verplaatst.

### <a name="step-1-connect-to-azure-database-for-mysql-and-create-an-empty-database"></a>Stap 1. Verbinden met Azure Database for MySQL en een lege data base maken

### <a name="step-2-schema-synchronization"></a>Stap 2. Schema synchronisatie

1. Selecteer in het menu vergelijking de optie nieuwe schema vergelijking.
De wizard nieuwe schema vergelijking wordt weer gegeven.

2. Selecteer de bron en het doel en geef vervolgens de opties voor schema vergelijking op. Selecteer Compare.

3. Selecteer in het raster vergelijkings resultaten dat wordt weer gegeven, objecten voor synchronisatie. Selecteer de groene pijl knop om de wizard schema synchronisatie te openen.

4. Door loop de stappen van de wizard om de synchronisatie te configureren. Selecteer synchroniseren om de wijzigingen te implementeren.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="Wizard schema synchronisatie":::

### <a name="data-comparison"></a>Gegevens vergelijking

1. Selecteer in het menu vergelijking de optie nieuwe vergelijking van gegevens. De wizard nieuwe gegevens vergelijking wordt weer gegeven.

2. Selecteer de bron en het doel en geef vervolgens de opties voor de gegevens vergelijking op en wijzig zo nodig toewijzingen. Selecteer Compare.

3. Selecteer in het raster vergelijkings resultaten dat wordt weer gegeven, objecten voor synchronisatie. Selecteer de groene pijl knop om de wizard gegevens synchronisatie te openen.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="Resultaat van gegevens samenstelling":::

4. Door loop de stappen van de wizard om de synchronisatie te configureren. Selecteer synchroniseren om de wijzigingen te implementeren.

5. Controleer het resultaat.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="Resultaat van gegevens synchronisatie":::

## <a name="summary"></a>Samenvatting

Tegenwoordig meer bedrijven hun data bases naar Azure Database for MySQL verplaatsen, omdat deze database service eenvoudig kan worden ingesteld, beheerd en geschaald. Deze migratie hoeft niet te worden gemeld. dbForge Studio voor MySQL biedt Immaculate-migratie hulpprogramma's waarmee het proces aanzienlijk kan worden vergemakkelijkt. Met Studio kan data base-overdracht eenvoudig worden geconfigureerd, opgeslagen, bewerkt, geautomatiseerd en gepland.

## <a name="next-steps"></a>Volgende stappen
- [Overzicht van MySQL](overview.md)
