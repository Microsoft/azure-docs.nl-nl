---
title: Een back-up maken van een SQL Server-VM vanuit het deel venster VM
description: In dit artikel vindt u informatie over het maken van een back-up van SQL Server-data bases op virtuele machines van Azure vanuit het deel venster VM.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 4f4ea202ee96e93a621c8dd0025c9ebc8b8d445d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88891654"
---
# <a name="back-up-a-sql-server-from-the-vm-pane"></a>Een back-up maken van een SQL Server vanuit het VM-deel venster

In dit artikel wordt uitgelegd hoe u back-ups maakt SQL Server die worden uitgevoerd in virtuele machines van Azure met de [Azure backup](backup-overview.md) -service. U kunt op twee manieren back-ups maken van SQL Server Vm's:

- Eén SQL Server Azure VM: in dit artikel wordt beschreven hoe u rechtstreeks vanuit de VM-weer gave een back-up van een SQL Server-VM maakt.
- Meerdere SQL Server Azure-Vm's: u kunt een Recovery Services kluis instellen en een back-up configureren voor meerdere Vm's. Volg de instructies in [dit artikel](backup-sql-server-database-azure-vms.md) voor het betreffende scenario.

## <a name="before-you-start"></a>Voordat u begint

1. Controleer uw omgeving met de [ondersteunings matrix](sql-support-matrix.md).
2. Bekijk een [overzicht](backup-azure-sql-database.md) van Azure Backup voor SQL Server VM.
3. Controleer of de virtuele machine [verbinding](backup-sql-server-database-azure-vms.md#establish-network-connectivity)heeft met het netwerk.

## <a name="configure-backup-on-the-sql-server"></a>Back-up configureren op de SQL Server

U kunt back-up op uw SQL Server-VM inschakelen vanuit het deel venster **back-up** in de virtuele machine. Deze methode heeft twee dingen:

- Registreert de SQL-VM met de Azure Backup-service om deze toegang te geven.
- Alle SQL Server exemplaren die in de virtuele machine worden uitgevoerd, worden door autobeschermd. Dit betekent dat het back-upbeleid wordt toegepast op alle bestaande data bases, evenals de data bases die in de toekomst aan deze instanties worden toegevoegd.

1. Selecteer de banner aan de bovenkant van de pagina om de weer gave SQL Server back-up te openen.

    ![SQL Server back-upweergave](./media/backup-sql-server-vm-from-vm-pane/sql-server-backup-view.png)

    >[!NOTE]
    >Ziet u de banner niet? De banner wordt alleen weer gegeven voor de SQL Server Vm's die zijn gemaakt met installatie kopieën van Azure Marketplace. Het wordt ook weer gegeven voor de virtuele machines die zijn beveiligd met Azure VM-back-up. Voor andere installatie kopieën kunt u back-up configureren, zoals [hier](backup-sql-server-database-azure-vms.md)wordt uitgelegd.

2. Voer de naam van de Recovery Services kluis in. Een kluis is een logische entiteit voor het opslaan en beheren van al uw back-ups. Als u een nieuwe kluis maakt:

    - Het wordt gemaakt in hetzelfde abonnement en dezelfde regio als de SQL Server VM die u beveiligt.
    - Het wordt gemaakt met de instelling voor geo-redundante opslag (GRS) voor alle back-ups. Als u het redundantie type wilt wijzigen, moet u dit doen voordat u de virtuele machine beveiligt. Raadpleeg [dit artikel](backup-create-rs-vault.md#set-storage-redundancy) voor meer informatie.

3. Kies een **back-upbeleid**. U kunt kiezen uit het standaard beleid of andere bestaande beleids regels die u in de kluis hebt gemaakt. Als u een nieuw beleid wilt maken, raadpleegt u [dit artikel](backup-sql-server-database-azure-vms.md#create-a-backup-policy) voor een stapsgewijze hand leiding.

    ![Een back-upbeleid kiezen](./media/backup-sql-server-vm-from-vm-pane/backup-policy.png)

4. Selecteer **Back-up inschakelen**. Het volt ooien van de bewerking kan enkele minuten duren.

    ![Selecteer back-up inschakelen](./media/backup-sql-server-vm-from-vm-pane/enable-backup.png)

5. Zodra de bewerking is voltooid, ziet u de naam van de **kluis** in de banner.

    ![De naam van de kluis wordt weer gegeven in banner](./media/backup-sql-server-vm-from-vm-pane/vault-name.png)

6. Selecteer de banner om de kluis weergave te starten, waar u alle geregistreerde Vm's en de bijbehorende beveiligings status kunt zien.

    ![Kluis weergave met geregistreerde Vm's](./media/backup-sql-server-vm-from-vm-pane/vault-view.png)

7. Voor niet-Marketplace-installatie kopieën kan de registratie slagen, maar het **configureren van back-up** kan niet worden geactiveerd totdat de extensie van de Azure Backup op de SQL Server is opgegeven. In dergelijke gevallen wordt de kolom voor de **gereedheid van back-ups** **niet gereed**. U moet [de juiste machtigingen](backup-azure-sql-database.md#set-vm-permissions) voor niet-Marketplace-installatie kopieën hand matig toewijzen zodat het configureren van de back-up kan worden geactiveerd.

    ![Gereedheid voor back-ups is niet gereed](./media/backup-sql-server-vm-from-vm-pane/backup-readiness-not-ready.png)

8. Ga naar de bijbehorende Recovery Services kluis voor verdere bewerkingen of bewaking die u moet uitvoeren op de back-up van SQL Server VM. Ga naar **Back-upitems** om alle data bases te bekijken waarvan een back-up is gemaakt in deze kluis en activeer bewerkingen zoals back-up en herstel op aanvraag. Ga op dezelfde manier naar **back-uptaken** voor het [bewaken](manage-monitor-sql-database-backup.md) van taken die overeenkomen met bewerkingen, zoals beveiliging, back-up en herstel.

    ![Zie back-ups van data bases in back-upitems](./media/backup-sql-server-vm-from-vm-pane/backup-items.png)

>[!NOTE]
>De back-up wordt niet automatisch geconfigureerd op een van de nieuwe SQL Server-exemplaren die later kunnen worden toegevoegd aan de beveiligde virtuele machine. Als u een back-up wilt configureren voor de zojuist toegevoegde instanties, moet u de kluis die de virtuele machine heeft geregistreerd en de [hier](backup-sql-server-database-azure-vms.md)beschreven stappen volgen.

## <a name="next-steps"></a>Volgende stappen

Leer hoe u het volgende doet:

- [Back-ups van SQL Server-data bases herstellen](restore-sql-database-azure-vm.md)
- [Back-ups van SQL Server-data bases beheren](manage-monitor-sql-database-backup.md)
