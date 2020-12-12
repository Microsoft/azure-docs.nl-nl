---
title: Een SQL Server-Data Base migreren naar SQL Server op een virtuele machine | Microsoft Docs
description: Meer informatie over het migreren van een on-premises gebruikers database naar SQL Server op een virtuele machine van Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: migration
ms.topic: how-to
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 4cd37128893309be5a1e362671b9e28dcc436b1b
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356205"
---
# <a name="migrate-a-sql-server-database-to-sql-server-on-an-azure-virtual-machine"></a>Een SQL Server-Data Base migreren naar SQL Server op een virtuele machine van Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Er zijn een aantal manieren om een on-premises SQL Server-gebruikers database te migreren naar SQL Server op een virtuele Azure-machine (VM). In dit artikel worden verschillende methoden beschreven en wordt de beste methode aanbevolen voor verschillende scenario's.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 en SQL Server 2008 R2 nadert het [einde van hun ondersteunings cyclus](https://www.microsoft.com/sql-server/sql-server-2008) voor on-premises exemplaren. Als u ondersteuning wilt uitbreiden, kunt u uw SQL Server-exemplaar naar een Azure-VM migreren of uitgebreide beveiligings updates kopen om deze on-premises te blijven gebruiken. Zie [ondersteuning voor SQL Server 2008 en 2008 R2 uitbreiden met Azure](sql-server-2008-extend-end-of-support.md) voor meer informatie.

## <a name="what-are-the-primary-migration-methods"></a>Wat zijn de primaire migratie methoden?

De primaire migratie methoden zijn:

* Voer een on-premises back-up uit met compressie en kopieer het back-upbestand vervolgens hand matig naar de virtuele Azure-machine.
* Voer een back-up naar een URL uit en herstel de virtuele Azure-machine vanuit de URL.
* Ontkoppel de gegevens-en logboek bestanden, kopieer ze naar Azure Blob-opslag en koppel deze vervolgens aan SQL Server in de Azure VM vanuit de URL.
* Converteer de lokale fysieke machine naar een Hyper-V VHD, upload deze naar Azure Blob-opslag en implementeer deze als nieuwe VM met behulp van geüploade VHD.
* Verzend de harde schijf met behulp van de Windows import/export-service.
* Als u on-premises een implementatie van AlwaysOn-beschikbaarheids groepen hebt, gebruikt u de [wizard Azure replica toevoegen](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability) om een replica te maken in azure, failover en gebruikers naar het Azure data base-exemplaar te verwijzen.
* Gebruik SQL Server [transactionele replicatie](/sql/relational-databases/replication/transactional/transactional-replication) om het Azure SQL Server-exemplaar als een abonnee te configureren, replicatie uit te scha kelen en gebruikers te laten verwijzen naar het Azure data base-exemplaar.

> [!TIP]
> U kunt ook dezelfde technieken gebruiken om data bases te verplaatsen tussen SQL Server Vm's in Azure. Er is bijvoorbeeld geen ondersteunde manier om een upgrade uit te kunnen zetten van een SQL Server galerie-image-VM van de ene versie naar een andere. In dit geval moet u een nieuwe SQL Server VM maken met de nieuwe versie/editie en vervolgens een van de migratie technieken in dit artikel gebruiken om uw data bases te verplaatsen. 

## <a name="choose-a-migration-method"></a>Een migratie methode kiezen

Voor de beste prestaties van gegevens overdracht migreert u de database bestanden naar de Azure-VM met behulp van een gecomprimeerd back-upbestand.

Gebruik de optie AlwaysOn of de optie transactionele replicatie om de downtime te minimaliseren tijdens het database migratie proces.

Als het niet mogelijk is om de bovenstaande methoden te gebruiken, migreert u de data base hand matig. Over het algemeen begint u met een back-up van de data base, volgt u deze met een kopie van de back-up van de data base in Azure en herstelt u de data base. U kunt de database bestanden ook kopiëren naar Azure en deze vervolgens koppelen. Er zijn verschillende manieren waarop u dit hand matige proces van het migreren van een Data Base naar een virtuele machine van Azure kunt uitvoeren.

> [!NOTE]
> Wanneer u een upgrade uitvoert naar SQL Server 2014 of SQL Server 2016 van oudere versies van SQL Server, moet u overwegen of er wijzigingen nodig zijn. U wordt aangeraden alle afhankelijkheden op te lossen voor functies die niet worden ondersteund door de nieuwe versie van SQL Server als onderdeel van uw migratie project. Zie [upgrade naar SQL Server](/sql/database-engine/install-windows/upgrade-sql-server)voor meer informatie over de ondersteunde edities en scenario's.

De volgende tabel bevat een overzicht van de primaire migratie methoden en komt aan de orde wanneer het gebruik van elke methode het meest geschikt is.

| Methode | Versie van de bron database | Versie van de doel database | Beperking van de back-upgrootte van de bron database | Notities |
| --- | --- | --- | --- | --- |
| [Een on-premises back-up uitvoeren met compressie en het back-upbestand hand matig kopiëren naar de virtuele machine van Azure](#back-up-and-restore) |SQL Server 2005 of hoger |SQL Server 2005 of hoger |[Opslag limiet voor Azure VM](../../../index.yml) | Deze techniek is eenvoudig en goed getest voor het verplaatsen van data bases op verschillende computers. |
| [Maak een back-up naar de URL en herstel de virtuele Azure-machine vanuit de URL](#backup-to-url-and-restore-from-url) |SQL Server 2012 SP1 CU2 of hoger | SQL Server 2012 SP1 CU2 of hoger | < 12,8 TB voor SQL Server 2016, anders < 1 TB | Deze methode is een andere manier om het back-upbestand naar de virtuele machine te verplaatsen met behulp van Azure Storage. |
| [Ontkoppel en kopieer vervolgens de gegevens-en logboek bestanden naar Azure Blob-opslag en koppel deze vervolgens aan SQL Server in azure virtual machine van URL](#detach-and-attach-from-a-url) | SQL Server 2005 of hoger |SQL Server 2014 of hoger | [Opslag limiet voor Azure VM](../../../index.yml) | Gebruik deze methode wanneer u van plan bent om [deze bestanden op te slaan met behulp van de Azure Blob Storage-service](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) en deze te koppelen aan SQL Server die worden uitgevoerd in een Azure-VM, met name met zeer grote data bases |
| [De on-premises machine converteren naar Hyper-V-Vhd's, uploaden naar Azure Blob Storage en vervolgens een nieuwe virtuele machine implementeren met geüploade VHD](#convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm) |SQL Server 2005 of hoger |SQL Server 2005 of hoger |[Opslag limiet voor Azure VM](../../../index.yml) |Gebruiken wanneer u [uw eigen SQL Server-licentie](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md)meebrengt bij het migreren van een Data Base die u uitvoert op een oudere versie van SQL Server, of bij het migreren van systeem-en gebruikers databases samen als onderdeel van de migratie van data base afhankelijk van andere gebruikers databases en/of systeem databases. |
| [Harde schijf verzenden met Windows import/export-service](#ship-a-hard-drive) |SQL Server 2005 of hoger |SQL Server 2005 of hoger |[Opslag limiet voor Azure VM](../../../index.yml) |Gebruik de [Windows import/export-service](../../../storage/common/storage-import-export-service.md) als de methode voor hand matig kopiëren te langzaam is, zoals bij zeer grote data bases |
| [De wizard Azure replica toevoegen gebruiken](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability) |SQL Server 2012 of hoger |SQL Server 2012 of hoger |[Opslag limiet voor Azure VM](../../../index.yml) |Minimaliseert downtime, gebruik wanneer u een on-premises implementatie hebt |
| [SQL Server transactionele replicatie gebruiken](/sql/relational-databases/replication/transactional/transactional-replication) |SQL Server 2005 of hoger |SQL Server 2005 of hoger |[Opslag limiet voor Azure VM](../../../index.yml) |Gebruiken wanneer u downtime wilt minimaliseren en geen on-premises implementatie hebt |

## <a name="back-up-and-restore"></a>Back-up en herstel

Maak een back-up van uw data base met compressie, kopieer de back-up naar de virtuele machine en herstel de data base. Als uw back-upbestand groter is dan 1 TB, moet u een striped-set maken, omdat de maximale grootte van een VM-schijf 1 TB is. Gebruik de volgende algemene stappen voor het migreren van een gebruikers database met deze hand matige methode:

1. Voer een volledige back-up van de data base uit naar een on-premises locatie.
2. Maak of upload een virtuele machine met de gewenste versie van SQL Server.
3. Connectiviteit instellen op basis van uw vereisten. Zie [verbinding maken met een SQL Server virtuele machine op Azure (Resource Manager)](ways-to-connect-to-sql.md).
4. Kopieer uw back-upbestand (en) naar uw VM met behulp van extern bureau blad, Windows Verkenner of de Kopieer opdracht van een opdracht prompt.

## <a name="backup-to-url-and-restore-from-url"></a>Back-up naar URL en herstellen vanaf URL

In plaats van een back-up naar een lokaal bestand te maken, kunt u [back-up naar URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) gebruiken en vervolgens van URL naar de virtuele machine herstellen. SQL Server 2016 ondersteunt gestripde back-upsets. Ze worden aanbevolen voor prestaties en zijn vereist om de maximale grootte per BLOB te overschrijden. Voor zeer grote data bases wordt het gebruik van de [Windows import/export-service](../../../storage/common/storage-import-export-service.md) aanbevolen.

## <a name="detach-and-attach-from-a-url"></a>Loskoppelen en koppelen vanuit een URL

Ontkoppel uw data base en logboek bestanden en breng ze over naar [Azure Blob-opslag](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure). Koppel de data base vervolgens van de URL op uw virtuele Azure-machine. Gebruik deze methode als u wilt dat de fysieke database bestanden zich in Blob Storage bevinden. Dit kan nuttig zijn voor zeer grote data bases. Gebruik de volgende algemene stappen voor het migreren van een gebruikers database met deze hand matige methode:

1. Ontkoppel de database bestanden van het on-premises data base-exemplaar.
2. Kopieer de losgekoppelde database bestanden naar Azure Blob-opslag met behulp van het [opdracht regel programma AZCopy](../../../storage/common/storage-use-azcopy-v10.md).
3. Koppel de database bestanden van de Azure-URL aan het SQL Server-exemplaar in de Azure VM.

## <a name="convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm"></a>Converteren naar een virtuele machine, uploaden naar een URL en implementeren als een nieuwe VM

Gebruik deze methode voor het migreren van alle systeem-en gebruikers databases in een on-premises SQL Server-exemplaar naar een virtuele machine van Azure. Gebruik de volgende algemene stappen voor het migreren van een hele SQL Server-exemplaar met behulp van deze hand matige methode:

1. Converteer fysieke of virtuele machines naar Hyper-V-Vhd's.
2. Upload VHD-bestanden naar Azure Storage met behulp van de [cmdlet Add-AzureVHD](/previous-versions/azure/dn495173(v=azure.100)).
3. Implementeer een nieuwe virtuele machine met behulp van de geüploade VHD.

> [!NOTE]
> Als u een volledige toepassing wilt migreren, kunt u overwegen [Azure site Recovery](../../../site-recovery/site-recovery-overview.md)] te gebruiken.

## <a name="ship-a-hard-drive"></a>Een harde schijf verzenden

Gebruik de [service methode van Windows import/export](../../../storage/common/storage-import-export-service.md) om grote hoeveel heden bestands gegevens over te brengen naar Azure Blob-opslag in situaties waarin het uploaden via het netwerk prohibitively kostbaar of niet haalbaar is. Met deze service stuurt u een of meer harde schijven met daarin de gegevens naar een Azure-Data Center waar uw gegevens naar uw opslag account worden geüpload.

## <a name="next-steps"></a>Volgende stappen

Zie [SQL Server op Azure virtual machines Overview](sql-server-on-azure-vm-iaas-what-is-overview.md)voor meer informatie.

> [!TIP]
> Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](frequently-asked-questions-faq.md).

Voor instructies over het maken van SQL Server op een virtuele machine van Azure vanuit een vastgelegde installatie kopie raadpleegt u [Tips & slagen op het klonen van virtuele Azure SQL-machines van vastgelegde installatie kopieën](/archive/blogs/psssql/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images) op de CSS-blog SQL Server engineers.