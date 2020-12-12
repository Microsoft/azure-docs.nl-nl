---
title: Logboek schijf migreren naar ultra Disk
description: Meer informatie over het migreren van uw SQL Server op de Azure virtual machine (VM)-logboek schijf naar een Azure-Ultradisk om te profiteren van hoge prestaties en lage latentie.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 42d7760d25f6ab591c19889eb2159711d6de1b07
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356749"
---
# <a name="migrate-log-disk-to-ultra-disk"></a>Logboek schijf migreren naar ultra Disk
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure Ultra disks bieden hoge door Voer en hoge IOPS en een consistente schijf opslag met lage latentie voor SQL Server op Azure virtual machine (VM). 

In dit artikel leert u hoe u uw logboek schijf naar een ultra SSD kunt migreren om te profiteren van de prestatie voordelen die worden geboden door Ultra disks. 

## <a name="back-up-database"></a>Back-up maken van data base

Voltooi een [volledige back-up](backup-restore.md) van uw data base. 

## <a name="attach-disk"></a>Schijf koppelen

Koppel de Ultra-SSD aan uw virtuele machine wanneer u ultradisk-compatibiliteit hebt ingeschakeld op de VM. 

Ultra disk wordt ondersteund op een subset van VM-grootten en-regio's. Controleer voordat u doorgaat of uw virtuele machine zich in een regio, zone en grootte bevindt die ondersteuning biedt voor Ultra disk. U kunt de [grootte en regio van de virtuele machine bepalen en valideren](../../../virtual-machines/disks-enable-ultra-ssd.md#determine-vm-size-and-region-availability) met behulp van Azure CLI of Power shell. 

### <a name="enable-compatibility"></a>Compatibiliteit inschakelen

Voer de volgende stappen uit om de compatibiliteit in te scha kelen:

1. Ga naar de virtuele machine in de [Azure Portal](https://portal.azure.com/). 
1. Stop/toewijzing van de virtuele machine ongedaan maken. 
1. Selecteer **schijven** onder **instellingen** en selecteer vervolgens **extra instellingen**. 

   :::image type="content" source="media/storage-migrate-to-ultradisk/additional-disks-settings-azure-portal.png" alt-text="Selecteer extra instellingen voor schijven onder instellingen in het Azure Portal":::

1. Selecteer **Ja** om **compatibiliteit met ultra disk in te scha kelen**. 

   :::image type="content" source="../../../virtual-machines/media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png" alt-text="Scherm opname met de optie Ja.":::

1. Selecteer **Opslaan**. 



### <a name="attach-disk"></a>Schijf koppelen

Gebruik de Azure Portal om een ultra schijf aan uw virtuele machine toe te voegen. Zie [een ultra schijf koppelen](../../../virtual-machines/disks-enable-ultra-ssd.md#attach-an-ultra-disk)voor meer informatie.

Zodra de schijf is gekoppeld, start u de VM opnieuw op met behulp van de Azure Portal. 



## <a name="format-disk"></a>Schijf Format teren

Maak verbinding met uw virtuele machine en Format teer uw Ultra schijf.  

Voer de volgende stappen uit om uw Ultra schijf te Format teren:

1. Maak verbinding met uw virtuele machine met behulp van Remote Desktop Protocol (RDP).
1. Gebruik [schijf beheer](/windows-server/storage/disk-management/overview-of-disk-management) om uw nieuw gekoppelde Ultra schijf te Format teren en te partitioneren. 


## <a name="use-disk-for-log"></a>Schijf gebruiken voor logboek

Configureer SQL Server om het nieuwe logboek station te gebruiken. U kunt dit doen met behulp van Transact-SQL (T-SQL) of SQL Server Management Studio (SSMS). Het account dat wordt gebruikt voor de SQL Server-service account moet volledig beheer hebben over de nieuwe locatie van het logboek bestand. 

### <a name="configure-permissions"></a>Machtigingen configureren

1. Controleer het service account dat wordt gebruikt door SQL Server. U kunt dit doen met behulp van SQL Server Configuration Manager of services. msc.
1. Ga naar de nieuwe schijf. 
1. Maak een map (of meerdere mappen) die moet worden gebruikt voor het logboek bestand. 
1. Klik met de rechter muisknop op de map en selecteer **Eigenschappen**.
1. Geef op het tabblad **beveiliging** volledige controle toegang tot het SQL Server-service account. 
1. Selecteer **OK**  om uw instellingen op te slaan. 
1. Herhaal dit voor elke hoofdmap van het hoofd niveau waar u SQL-gegevens wilt hebben. 

### <a name="use-new-log-drive"></a>Nieuw logboek station gebruiken 

Nadat de machtiging is verleend, gebruikt u Transact-SQL (T-SQL) of SQL Server Management Studio (SSMS) om de data base los te koppelen en bestaande logboek bestanden naar de nieuwe locatie te verplaatsen.

   > [!CAUTION]
   > Als u de data base loskoppelt, wordt deze offline gezet, worden verbindingen gesloten en worden alle trans acties die in de vlucht worden teruggedraaid. Wees voorzichtig en tijdens het onderhouds venster. 



# <a name="transact-sql-t-sql"></a>[Transact-SQL (T-SQL)](#tab/tsql)

T-SQL gebruiken om de bestaande bestanden naar een nieuwe locatie te verplaatsen:

1. Maak verbinding met uw data base in SQL Server Management Studio en open een **Nieuw query** venster. 
1. De bestaande bestanden en locaties ophalen:

   ```sql
   USE AdventureWorks
   GO

   sp_helpfile
   GO
   ```

1. De data base loskoppelen: 

   ```sql
   USE master
   GO

   sp_detach_db 'AdventureWorks'
   GO
   ```

1. Gebruik Verkenner om het logboek bestand te verplaatsen naar de nieuwe locatie op de ultra schijf. 

1. Koppel de data base, waarbij u de nieuwe bestands locaties opgeeft: 

   ```sql
    sp_attach_db 'AdventureWorks'
   'E:\Fixed_FG\AdventureWorks.mdf',
   'E:\Fixed_FG\AdventureWorks_2.ndf',
   'F:\New_Log\AdventureWorks_log.ldf'
   GO
   ```

Op dit moment is de data base online met het logboek op de nieuwe locatie. 



# <a name="sql-server-management-studio-ssms"></a>[SQL Server Management Studio (SSMS)](#tab/ssms)

Gebruik SSMS om de bestaande bestanden te verplaatsen naar een nieuwe locatie:

1. Maak verbinding met uw data base in SQL Server Management Studio (SSMS). 
1. Klik met de rechter muisknop op de data base, selecteer **Eigenschappen** en selecteer vervolgens **bestanden**. 
1. Noteer het pad van de bestaande bestanden. 
1. Selecteer **OK** om het dialoogvenster te sluiten. 
1. Klik met de rechter muisknop op de data base en selecteer **taken**  >  **loskoppelen**. 
1. Volg de wizard om de data base los te koppelen. 
1. Verplaats het logboek bestand hand matig naar de nieuwe locatie met bestanden Verkenner.
1. De data base in SQL Server Management Studio koppelen
   1. Klik met de rechter muisknop op **data bases** in **objectverkenner** en selecteer **Data Base bijvoegen**. 
   1. Voeg in het dialoog venster elk bestand toe, met inbegrip van het logboek bestand op de nieuwe locatie. 
   1. Selecteer **OK** om de data base te koppelen. 

Op dit moment is de data base online met het logboek op de nieuwe locatie.

---


## <a name="next-steps"></a>Volgende stappen

Bekijk de [Aanbevolen procedures](performance-guidelines-best-practices.md) voor het uitvoeren van extra instellingen om de prestaties te verbeteren. 

Raadpleeg de volgende artikelen voor een overzicht van SQL Server op Azure Virtual Machines:

- [Overzicht van SQL Server op Windows-Vm's](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Overzicht van SQL Server on Linux Vm's](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
