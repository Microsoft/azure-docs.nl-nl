---
title: Acties uitvoeren met behulp van Back-upcentrum
description: In dit artikel wordt uitgelegd hoe u acties kunt uitvoeren met behulp van Back-upcentrum
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 8c21475e5a52cdce7e38bbeb9d00df3c3ac3a752
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506446"
---
# <a name="perform-actions-using-backup-center"></a>Acties uitvoeren met behulp van Back-upcentrum

Met backup Center kunt u acties uitvoeren die betrekking hebben op sleutel back-ups vanuit een centrale interface, zonder dat u naar een afzonderlijke kluis hoeft te navigeren. U kunt de volgende acties uitvoeren vanuit het Back-upcentrum:

* Back-ups configureren voor uw gegevens bronnen
* Een back-upexemplaar herstellen
* Een nieuwe kluis maken
* Een nieuw back-upbeleid maken
* Een back-up op aanvraag activeren voor een back-upexemplaar
* Back-up stoppen voor een back-upexemplaar

## <a name="supported-scenarios"></a>Ondersteunde scenario's

* Het Back-upcentrum wordt momenteel ondersteund voor Azure VM backup, SQL in azure VM backup, SAP HANA in azure VM backup, Azure Files backup en Azure Database for PostgreSQL Server Backup.
* Raadpleeg de [ondersteunings matrix](backup-center-support-matrix.md) voor een gedetailleerde lijst met ondersteunde en niet-ondersteunde scenario's.

## <a name="configure-backup"></a>Back-up configureren

Als u een back-up maakt van virtuele Azure-machines, SQL in azure-vm's, SAP HANA in azure Vm's of Azure Files, moet u een Recovery Services kluis gebruiken. Als u een back-up maakt van Azure-data bases voor PostgreSQL-server, moet u een back-upkluis gebruiken. 

Afhankelijk van het type gegevens bron waarvan u een back-up wilt maken, volgt u de juiste instructies zoals hieronder wordt beschreven.

### <a name="configure-backup-to-a-recovery-services-vault"></a>Back-up naar een Recovery Services kluis configureren

1. Navigeer naar het Back-upcentrum en selecteer **+ Backup** boven aan het tabblad **overzicht** .

    ![Overzicht van Back-upcentrum](./media/backup-center-actions/backup-center-overview-configure-backup.png)

2. Selecteer het type gegevens bron waarvan u een back-up wilt maken.

    ![Gegevens bron selecteren voor het configureren van de VM-back-up](./media/backup-center-actions/backup-select-datasource-vm.png)

3. Kies een Recovery Services kluis en selecteer **door gaan**. Hiermee wordt u naar de configuratie van de back-up geleid die identiek is aan die van een Recovery Services kluis. Meer [informatie over het configureren van back-ups voor virtuele Azure-machines met een Recovery Services kluis](tutorial-backup-vm-at-scale.md).

### <a name="configure-backup-to-a-backup-vault"></a>Back-up naar een back-upkluis configureren

1. Navigeer naar het Back-upcentrum en selecteer **+ Backup** boven aan het tabblad **overzicht** .
2. Selecteer het type gegevens bron waarvan u een back-up wilt maken (Azure Database for PostgreSQL server in dit geval).

    ![Gegevens bron selecteren voor het configureren van Azure Database for PostgreSQL Server back-up](./media/backup-center-actions/backup-select-datasource-type-postgresql.png)

3. Selecteer **Doorgaan**. Hiermee wordt u naar de configuratie van de back-up geleid die identiek is aan die van een back-upkluis. Meer [informatie over het configureren van back-ups voor Azure database for postgresql server met een back-upkluis](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases).

## <a name="restore-a-backup-instance"></a>Een back-upexemplaar herstellen

Afhankelijk van het type gegevens bron dat u wilt herstellen, volgt u de juiste instructies zoals hieronder wordt beschreven.

### <a name="if-youre-restoring-from-a-recovery-services-vault"></a>Als u van een Recovery Services kluis herstelt

1. Ga naar het Back-upcentrum en selecteer **herstellen** bovenaan het tabblad **overzicht** .

    ![Overzicht van Back-upcentrum voor het herstellen van de VM](./media/backup-center-actions/backup-center-overview-restore.png)

2. Selecteer het type gegevens bron dat u wilt herstellen.

    ![Gegevens bron selecteren voor het terugzetten van de VM](./media/backup-center-actions/restore-select-datasource-vm.png)

3. Kies een back-upexemplaar en selecteer **door gaan**. Hiermee krijgt u de mogelijkheid om de instellingen voor herstel te herstellen die identiek zijn aan die van een Recovery Services kluis. Meer [informatie over het herstellen van een virtuele machine van Azure met een Recovery Services kluis](backup-azure-arm-restore-vms.md#before-you-start).

### <a name="if-youre-restoring-from-a-backup-vault"></a>Als u een back-upkluis terugzet

1. Ga naar het Back-upcentrum en selecteer **herstellen** bovenaan het tabblad **overzicht** .
2. Selecteer het type gegevens bron dat u wilt herstellen (Azure Database for PostgreSQL server in dit geval).

    ![Gegevens bron selecteren voor het terugzetten van Azure Database for PostgreSQL server](./media/backup-center-actions/restore-select-datasource-postgresql.png)

3. Kies een back-upexemplaar en selecteer **door gaan**. Hiermee krijgt u de mogelijkheid om de instellingen voor herstel te herstellen die identiek zijn aan die van een Recovery Services kluis. Meer [informatie over het herstellen van een Azure database for postgresql-server met een back-upkluis](backup-azure-database-postgresql.md#restore).

## <a name="create-a-new-vault"></a>Een nieuwe kluis maken

Als u een nieuwe kluis wilt maken, gaat u naar Back-upcentrum en selecteert u **+ kluis** boven aan het tabblad **overzicht** .

![Kluis maken](./media/backup-center-actions/backup-center-create-vault.png)

* [Meer informatie over het maken van een Recovery Services-kluis](backup-create-rs-vault.md)
* [Meer informatie over het maken van een back-upkluis](backup-vault-overview.md)

## <a name="create-a-new-backup-policy"></a>Een nieuw back-upbeleid maken

Afhankelijk van het type gegevens bron waarvan u een back-up wilt maken, volgt u de juiste instructies die hieronder worden beschreven.

### <a name="if-youre-backing-up-to-a-recovery-services-vault"></a>Als u een back-up maakt van een Recovery Services kluis

1. Ga naar het Back-upcentrum en selecteer boven aan het tabblad **overzicht** **+ beleid** .

    ![Overzicht van Back-upcentrum voor back-upbeleid](./media/backup-center-actions/backup-center-overview-policy.png)

2. Selecteer het type gegevens bron waarvan u een back-up wilt maken.

    ![Gegevens bron selecteren voor beleid voor back-up van VM](./media/backup-center-actions/policy-select-datasource-vm.png)

3. Kies een Recovery Services-kluis en selecteer **door gaan**. Dit leidt u naar de ervaring voor het maken van het beleid dat identiek is aan het bereik dat bereikbaar is vanuit een Recovery Services kluis. Meer [informatie over het maken van een nieuw back-upbeleid voor de virtuele machine van Azure met een Recovery Services-kluis](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

### <a name="if-youre-backing-up-to-a-backup-vault"></a>Als u een back-up maakt van een back-upkluis

1. Ga naar het Back-upcentrum en selecteer boven aan het tabblad **overzicht** **+ beleid** .
2. Selecteer het type gegevens bron waarvan u een back-up wilt maken (Azure Database for PostgreSQL server in dit geval).

    ![Gegevens bron selecteren voor beleid voor Azure Database for PostgreSQL Server-back-up](./media/backup-center-actions/policy-select-datasource-postgresql.png)

3. Selecteer **Doorgaan**. Hiermee krijgt u de mogelijkheid om het beleid te maken dat identiek is aan het proces dat bereikbaar is vanaf een back-upkluis. Meer [informatie over het maken van een nieuw back-upbeleid met een back-upkluis](backup-azure-database-postgresql.md#create-backup-policy).

## <a name="execute-an-on-demand-backup-for-a-backup-instance"></a>Een back-up op aanvraag uitvoeren voor een back-upexemplaar

Met backup Center kunt u zoeken naar back-upinstanties in back-ups en back-upbewerkingen op aanvraag uitvoeren.

Als u een back-up op aanvraag wilt activeren, gaat u naar Back-upcentrum en selecteert u de menu opdracht **back-Upinstanties** . Als u dit selecteert, kunt u de details weer geven van alle back-upinstanties waartoe u toegang hebt. U kunt zoeken naar de back-upinstantie waarvan u een back-up wilt maken. Als u met de rechter muisknop op een item in het raster klikt, wordt een lijst met beschik bare acties geopend. Selecteer de optie **Nu back-up** om een back-up op aanvraag uit te voeren.

![Back-ups op aanvraag](./media/backup-center-actions/backup-center-on-demand-backup.png)

Meer [informatie over het uitvoeren van on-demand back-ups voor Azure virtual machines](backup-azure-manage-vms.md#run-an-on-demand-backup).

Meer [informatie over het uitvoeren van back-ups op aanvraag voor Azure database for postgresql server](backup-azure-database-postgresql.md#on-demand-backup).

## <a name="stop-backup-for-a-backup-instance"></a>Back-up stoppen voor een back-upexemplaar

Er zijn scenario's waarin u een back-up wilt stoppen voor een back-upexemplaar, bijvoorbeeld wanneer de onderliggende bron waarvan een back-up wordt gemaakt, niet meer bestaat.

Als u een back-up op aanvraag wilt activeren, gaat u naar Back-upcentrum en selecteert u de menu opdracht **back-Upinstanties** . Selecteer deze optie om de details van alle back-upinstanties weer te geven waartoe u toegang hebt. U kunt zoeken naar de back-upinstantie waarvan u een back-up wilt maken. Als u met de rechter muisknop op een item in het raster klikt, wordt een lijst met beschik bare acties geopend. Selecteer de optie **back-up stoppen** om de back-up van het back-upexemplaar te stoppen.

![Beveiliging stoppen](./media/backup-center-actions/backup-center-stop-protection.png)

Meer [informatie over het stoppen van back-ups voor Azure virtual machines](backup-azure-manage-vms.md#stop-protecting-a-vm).

[Meer informatie over het stoppen van back-ups voor Azure Database for PostgreSQL server](backup-azure-database-postgresql.md#stop-protection)

## <a name="next-steps"></a>Volgende stappen

* [Back-ups controleren en uitvoeren](backup-center-monitor-operate.md)
* [Uw back-up maken](backup-center-govern-environment.md)
* [Inzichten op uw back-ups verkrijgen](backup-center-obtain-insights.md)
