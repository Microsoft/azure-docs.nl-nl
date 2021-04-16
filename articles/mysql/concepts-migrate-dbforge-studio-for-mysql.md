---
title: Verbinding maken met Azure Database for MySQL dbForge Studio for MySQL
description: In dit artikel wordt beschreven hoe u verbinding maakt met Azure Database for MySQL Server via dbForge Studio for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 942651aadf4113c1aca32e4e1d2c558b0d764421
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377222"
---
# <a name="connect-to-azure-database-for-mysql-using-dbforge-studio-for-mysql"></a>Verbinding maken met Azure Database for MySQL dbForge Studio for MySQL

Verbinding maken met Azure Database for MySQL [dbForge Studio for MySQL](https://www.devart.com/dbforge/mysql/studio/):

1. Selecteer nieuwe verbinding in het menu Database.

2. Geef een hostnaam en aanmeldingsreferenties op.

3. Selecteer de knop Verbinding testen om de configuratie te controleren.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Azure-verbinding":::

## <a name="migrate-a-database-using-the-backup-and-restore-functionality"></a>Een database migreren met behulp van de back-up- en herstelfunctionaliteit

Met Studio kunt u databases op verschillende manieren migreren naar Azure, waarvan de keuze uitsluitend afhankelijk is van uw behoeften. Als u de hele database wilt verplaatsen, kunt u het beste de functionaliteit Back-up en herstellen gebruiken. In dit voorbeeld migreren we de *sakila-database* die zich op de MySQL-server bevindt naar Azure Database for MySQL. De logica achter het migratieproces met behulp van de back-up- en herstelfunctionaliteit van dbForge Studio for MySQL is het maken van een back-up van de MySQL-database en deze vervolgens herstellen in Azure Database for MySQL.

### <a name="back-up-the-database"></a>Een back-up maken van de database

1. Wijs back-up en herstel in het menu Database aan en selecteer vervolgens Back-updatabase. De wizard Databaseback-up wordt weergegeven.

2. Selecteer op het tabblad Back-upinhoud van de wizard Databaseback-up de databaseobjecten waar u een back-up van wilt maken.

3. Configureer op het tabblad Opties het back-upproces dat aan uw vereisten voldoet.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="Back-up maken van wizardopties":::

4. Geef vervolgens het verwerkingsgedrag voor fouten en de opties voor logboekregistratie op.

5. Selecteer Back-up.

### <a name="restore-the-database"></a>De database herstellen

1. Maak verbinding met Azure for Database for MySQL zoals hierboven wordt beschreven.

2. Klik met de rechtermuisknop op Database Explorer, wijs Back-up en herstel aan en selecteer Database herstellen.

3. Selecteer in de wizard Database herstellen die wordt geopend een bestand met een databaseback-up.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="Herstelstap":::

4. Selecteer Terugzetten.

5. Controleer het resultaat.

## <a name="migrate-a-database-using-the-copy-databases-functionality"></a>Een database migreren met de functionaliteit Databases kopiëren

De functionaliteit Databases kopiëren is vergelijkbaar met back-up en herstel, behalve dat u er geen twee stappen voor nodig hebt om een database te migreren. Bovendien kunt u met de functie twee of meer databases in één keer overdragen. De functionaliteit Databases kopiëren is alleen beschikbaar in de Enterprise-editie van dbForge Studio for MySQL.
In dit voorbeeld migreren we de *world_x database* van MySQL-server naar Azure Database for MySQL.
Een database migreren met de functionaliteit Databases kopiëren:

1. Selecteer databases kopiëren in het menu Database. 

2. Geef op het tabblad Databases kopiëren dat wordt weergegeven de bron- en doelverbinding op en selecteer de database(s) die moeten worden gemigreerd. We voeren Azure MySQL-verbinding in en selecteren de *world_x database.* Selecteer de groene pijl om het proces te starten.

3. Controleer het resultaat.

Als gevolg van onze databasemigratie is *de world_x* in Azure MySQL verschenen.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="Resultaat databases kopiëren":::

## <a name="migrate-a-database-using-schema-and-data-compare-tools"></a>Een database migreren met behulp van hulpprogramma's voor schema en gegevens vergelijken

dbForge Studio for MySQL bevat enkele hulpprogramma's waarmee u MySQL-databases, MySQL-schema's en\of gegevens naar Azure kunt migreren. De keuze van functionaliteit is afhankelijk van uw behoeften en de vereisten van uw project. Als u een database selectief wilt verplaatsen, dat wil zeggen, bepaalde MySQL-tabellen naar Azure wilt migreren, kunt u het beste de functionaliteit Schema en Gegevens vergelijken gebruiken.
In dit voorbeeld migreren we de *werelddatabase* die zich op de MySQL-server bevindt naar Azure Database for MySQL. De logica achter het migratieproces met behulp van de functionaliteit Schema and Data Compare van dbForge Studio for MySQL is het maken van een lege database in Azure Database for MySQL, deze synchroniseren met de vereiste MySQL-database eerst met behulp van het hulpprogramma Schema compare en vervolgens het hulpprogramma Gegevens vergelijken. Op deze manier worden MySQL-schema's en -gegevens nauwkeurig verplaatst naar Azure.

### <a name="step-1-connect-to-azure-database-for-mysql-and-create-an-empty-database"></a>Stap 1. Verbinding maken met Azure Database for MySQL en een lege database maken

### <a name="step-2-schema-synchronization"></a>Stap 2. Schemasynchronisatie

1. Selecteer in het menu Vergelijking de optie Nieuwe schemavergelijking.
De wizard Nieuwe schemavergelijking wordt weergegeven.

2. Selecteer de Bron en het Doel en geef vervolgens de opties voor schemavergelijking op. Selecteer Vergelijken.

3. Selecteer objecten voor synchronisatie in het raster met vergelijkingsresultaten dat wordt weergegeven. Selecteer de groene pijlknop om de wizard Schemasynchronisatie te openen.

4. Doorloop de stappen van de wizard voor het configureren van synchronisatie. Selecteer Synchroniseren om de wijzigingen te implementeren.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="Wizard Schemasynchronisatie":::

### <a name="step-3-data-comparison"></a>Stap 3. Gegevensvergelijking

1. Selecteer in het menu Vergelijking de optie Nieuwe gegevensvergelijking. De wizard Nieuwe gegevensvergelijking wordt weergegeven.

2. Selecteer de Bron en het Doel, geef vervolgens de opties voor gegevensvergelijking op en wijzig indien nodig de toewijzingen. Selecteer Vergelijken.

3. Selecteer objecten voor synchronisatie in het raster met vergelijkingsresultaten dat wordt weergegeven. Selecteer de groene pijl om de wizard Gegevenssynchronisatie te openen.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="Resultaat van gegevenscomp":::

4. Doorloop de stappen van de wizard voor het configureren van synchronisatie. Selecteer Synchroniseren om de wijzigingen te implementeren.

5. Controleer het resultaat.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="Resultaat van gegevenssynchronisatie":::

## <a name="summary"></a>Samenvatting

Tegenwoordig verplaatsen steeds meer bedrijven hun databases naar Azure Database for MySQL, omdat deze databaseservice eenvoudig is in te stellen, te beheren en te schalen. Die migratie hoeft niet erg te zijn. dbForge Studio for MySQL is een van de perfecte migratiehulpprogramma's die het proces aanzienlijk kunnen vergemakkelijken. Met Studio kan databaseoverdracht eenvoudig worden geconfigureerd, opgeslagen, bewerkt, geautomatiseerd en gepland.

## <a name="next-steps"></a>Volgende stappen
- [Overzicht van MySQL](overview.md)
