---
title: Bekende problemen en beperkingen met online migraties naar Azure SQL Managed instance
description: Meer informatie over bekende problemen/migratie beperkingen die zijn gekoppeld aan online migraties naar Azure SQL Managed instance.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 46c5f5995c7a1d4eb074f6c1b25ecaad7e2da37e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98695527"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-managed-instance"></a>Bekende problemen/migratie beperkingen met online migraties naar Azure SQL Managed instance

Bekende problemen en beperkingen die zijn gekoppeld aan online migraties van SQL Server naar Azure SQL Managed instance worden hieronder beschreven.

> [!IMPORTANT]
> Met online migraties van SQL Server naar Azure SQL Database wordt de migratie van SQL_variant gegevens typen niet ondersteund.

## <a name="backup-requirements"></a>Back-upvereisten

- **Back-ups met controlesom**

    Azure Database Migration Service maakt gebruik van de methode Backup en Restore om uw on-premises data bases te migreren naar SQL Managed instance. Azure Database Migration Service ondersteunt alleen back-ups die zijn gemaakt met behulp van een controlesom.

    [Back-Upcontrolesoms tijdens het maken of herstellen](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server)van back-ups in-of uitschakelen (SQL Server).

    > [!NOTE]
    > Als u de database back-ups met compressie uitvoert, is de controlesom een standaard gedrag, tenzij expliciet is uitgeschakeld.

    Met offline migraties kunt u, als u kiest voor **ik Azure database Migration service...**, Azure database Migration service dan zal de back-up van de Data Base worden gemaakt met de optie controlesom ingeschakeld.

- **Back-upmedia**

    Zorg ervoor dat elke back-up op een afzonderlijk back-upmedium (back-upbestanden) wordt uitgevoerd. Azure Database Migration Service biedt geen ondersteuning voor back-ups die worden toegevoegd aan één back-upbestand. Maak volledige back-ups en logboek back-ups om back-upbestanden te scheiden.

## <a name="data-and-log-file-layout"></a>Indeling van gegevens en logboek bestanden

- **Aantal logboek bestanden**

    Azure Database Migration Service biedt geen ondersteuning voor data bases met meerdere logboek bestanden. Als u meerdere logboek bestanden hebt, moet u ze verkleinen en opnieuw indelen in één transactie logboek bestand. U moet eerst een back-up maken van het logboek bestand omdat u niet extern bestanden kunt registreren die niet leeg zijn.

## <a name="sql-server-features"></a>SQL Server functies

- **FileStream-FileTables**

    SQL Managed instance biedt momenteel geen ondersteuning voor FileStream en FileTables. Voor werk belastingen die afhankelijk zijn van deze functies, raden we u aan om te kiezen voor SQL-servers die worden uitgevoerd op virtuele Azure-machines als uw Azure-doel.

- **In-Memory tabellen**

    OLTP in het geheugen is beschikbaar in de Premium-en Bedrijfskritiek-lagen voor SQL Managed instance; de laag Algemeen biedt geen ondersteuning voor in-Memory OLTP.

## <a name="migration-resets"></a>Migratie opnieuw instellen

- **Implementaties**

    SQL Managed instance is een PaaS-service met automatische patches en versie-updates. Tijdens de migratie van uw SQL Managed instance worden niet-essentiële updates gedurende 36 uur bewaard. Daarna (en voor essentiële updates), als de migratie wordt onderbroken, wordt het proces opnieuw ingesteld op een volledige herstel status.

    Migratie cutover kan alleen worden aangeroepen nadat de volledige back-up is hersteld en de back-ups van alle logboeken worden onderschept. Als uw productie migratie cutovers wordt beïnvloed, neemt u contact op met de [Azure DMS-feedback alias](mailto:dmsfeedback@microsoft.com).

## <a name="smb-file-share-connectivity"></a>Connectiviteit van SMB-bestands share

Problemen met het verbinden met de SMB-bestands share worden waarschijnlijk veroorzaakt door een probleem met machtigingen. 

Voer de volgende stappen uit om de SMB-bestands share connectiviteit te testen: 

1. Sla een back-up op in de SMB-bestands share. 
1. Controleer de netwerk verbinding tussen het subnet van Azure Database Migration Service en de bron SQL Server. De eenvoudigste manier om dit te doen is door een SQL Server virtuele machine te implementeren in het DMS-subnet en verbinding te maken met de bron SQL Server met behulp van SQL Server Management Studio. 
1. Herstel de header van de bron SQL Server uit de back-up op de bestands share: 

   ```sql
   RESTORE HEADERONLY   
   FROM DISK = N'\\<SMB file share path>\full.bak'
   ```

Als u geen verbinding kunt maken met de bestands share, configureert u de machtigingen met de volgende stappen: 

1. Navigeer naar uw bestands share met bestanden Verkenner. 
1. Klik met de rechter muisknop op de bestands share en selecteer Eigenschappen. 
1. Kies het tabblad **delen** en selecteer **Geavanceerd delen**. 
1. Voeg het Windows-account dat wordt gebruikt voor migratie toe en wijs de toegang voor volledige controle toe. 
1. Voeg het SQL Server-service account toe en wijs de toegang voor volledige controle toe. Controleer de **SQL Server Configuration Manager** voor de SQL Server-service account als u niet zeker weet welk account wordt gebruikt. 

   :::image type="content" source="media/known-issues-azure-sql-db-managed-instance-online/assign-fileshare-permissions.png" alt-text="Geef volledig beheer toegang tot de Windows-accounts die worden gebruikt voor migratie en voor het SQL Server-service account. ":::

