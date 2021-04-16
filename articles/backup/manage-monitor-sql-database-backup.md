---
title: Uw DB SQL Server beheren en bewaken op een Azure-VM
description: In dit artikel wordt beschreven hoe u SQL Server databases beheert en bewaakt die worden uitgevoerd op een Azure-VM.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 3938e26e134f7d823d8a6f6fac631ebf4442e6ab
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519133"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Back-ups van SQL Server-databases beheren en bewaken

In dit artikel worden algemene taken beschreven voor het beheren en bewaken van SQL Server-databases die worden uitgevoerd op een virtuele Machine (VM) van Azure en die door de Azure Backup-service worden back-upt naar een Azure Backup Recovery [Services-kluis.](backup-overview.md) U leert hoe u taken en waarschuwingen bewaakt, de databasebeveiliging stopt en hervat, back-uptaken kunt uitvoeren en de registratie van een VM bij back-ups ongedaan kunt maken.

Als u nog geen back-ups hebt geconfigureerd voor uw SQL Server databases, zie [Back-ups maken SQL Server databases op virtuele Azure-VM's](backup-azure-sql-database.md)

## <a name="monitor-backup-jobs-in-the-portal"></a>Back-uptaken bewaken in de portal

Azure Backup toont alle geplande en on-demand  bewerkingen onder Back-uptaken in de portal, met uitzondering van de geplande logboekback-ups, omdat deze zeer vaak kunnen zijn. De taken die u in deze portal ziet, omvatten databasedetectie en -registratie, het configureren van back-up- en back-up- en herstelbewerkingen.

![De portal Back-uptaken](./media/backup-azure-sql-database/sql-backup-jobs-list.png)

Ga voor meer informatie over bewakingsscenario's naar [Bewaking in de Azure Portal](backup-azure-monitoring-built-in-monitor.md) bewaking met behulp van [Azure Monitor.](backup-azure-monitoring-use-azuremonitor.md)  

## <a name="view-backup-alerts"></a>Waarschuwingen voor back-ups weergeven

Omdat er elke 15 minuten back-ups van logboeken worden gemaakt, kan het lastig zijn om back-uptaken te controleren. Azure Backup bewaking wordt vergemakkelijkt door het verzenden van e-mailwaarschuwingen. E-mailwaarschuwingen zijn:

- Geactiveerd voor alle back-upfouten.
- Geconsolideerd op databaseniveau op foutcode.
- Alleen verzonden voor de eerste back-upfout van een database.

Waarschuwingen voor databaseback-ups controleren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer back-upwaarschuwingen op het **kluisdashboard.**

   ![Waarschuwingen voor back-ups selecteren](./media/backup-azure-sql-database/sql-backup-alerts-list.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Beveiliging van een SQL Server-database stoppen

U kunt op verschillende manieren stoppen met SQL Server van een database:

- Stop alle toekomstige back-uptaken en verwijder alle herstelpunten.
- Stop alle toekomstige back-uptaken en laat de herstelpunten intact.

Houd rekening met het volgende als u ervoor kiest de herstelpunten intact te laten:

- Alle herstelpunten blijven voor onbepaalde tijd intact en alle verwijderbewerkingen zullen stoppen bij het stoppen van de beveiliging met behoud van gegevens.
- Er worden kosten in rekening gebracht voor het beveiligde exemplaar en de verbruikte opslag. Zie prijzen voor Azure Backup [meer informatie.](https://azure.microsoft.com/pricing/details/backup/)
- Als u een gegevensbron verwijdert zonder back-ups te stoppen, mislukken nieuwe back-ups. Oude herstelpunten verlopen volgens het beleid, maar het meest recente herstelpunt blijft altijd behouden totdat u de back-ups stopt en de gegevens verwijdert.

Ga als volgt te werk om de beveiliging van een database te stoppen:

1. Selecteer back-upitems op het **kluisdashboard.**

2. Selecteer sql in Azure **VM** onder Type back-upbeheer. 

    ![SQL in Azure VM selecteren](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Selecteer de database waarvoor u de beveiliging wilt stoppen.

    ![De database selecteren waarvoor u de beveiliging wilt stoppen](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. Selecteer Back-up stoppen in het **databasemenu.**

    ![Back-up stoppen selecteren](./media/backup-azure-sql-database/stop-db-button.png)

5. Selecteer in het menu **Back-up** stoppen of u gegevens wilt behouden of verwijderen. Als u wilt, geeft u een reden en opmerking op.

    ![Gegevens behouden of verwijderen in het menu Back-up stoppen](./media/backup-azure-sql-database/stop-backup-button.png)

6. Selecteer **Back-up stoppen.**

> [!NOTE]
>
>Zie de onderstaande veelgestelde vragen voor meer informatie over de optie gegevens verwijderen:
>
>- [Wat gebeurt er met de back-ups als ik een database uit een automatisch beveiligd exemplaar verwijder?](faq-backup-sql-server.yml#if-i-delete-a-database-from-an-autoprotected-instance--what-will-happen-to-the-backups-)
>- [Als ik de back-upbewerking van een automatisch beveiligde database stop, wat is dan het gedrag ervan?](faq-backup-sql-server.yml#if-i-change-the-name-of-the-database-after-it-has-been-protected--what-will-be-the-behavior-)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Beveiliging voor een SQL-database hervatten

Wanneer u de beveiliging voor de SQL database en de optie **Back-upgegevens** behouden selecteert, kunt u de beveiliging later hervatten. Als u de back-upgegevens niet behoudt, kunt u de beveiliging niet hervatten.

De beveiliging voor een SQL database:

1. Open het back-upitem en selecteer **Back-up hervatten.**

    ![Back-up hervatten selecteren om de databasebeveiliging te hervatten](./media/backup-azure-sql-database/resume-backup-button.png)

2. Selecteer een beleid in het menu **Back-upbeleid** en selecteer vervolgens **Opslaan**.

## <a name="run-an-on-demand-backup"></a>Een on-demand back-up uitvoeren

U kunt verschillende soorten back-ups op aanvraag uitvoeren:

- Volledige back-up
- Alleen te kopiëren volledige back-up
- Differentiële back-up
- Logboekback-up

Hoewel u de retentieduur voor alleen-kopiëren volledige back-up moet opgeven, wordt de bewaartermijn voor een volledige back-up op aanvraag automatisch ingesteld op 45 dagen vanaf de huidige tijd.

Zie back-uptypen [SQL Server meer informatie.](backup-architecture.md#sql-server-backup-types)

## <a name="modify-policy"></a>Beleid wijzigen

Wijzig het beleid om de back-upfrequentie of bewaartermijn te wijzigen.

> [!NOTE]
> Elke wijziging in de retentieperiode wordt met terugwerkende kracht toegepast op alle oudere herstelpunten naast de nieuwe herstelpunten.

Ga in het kluisdashboard naar   >  **Back-upbeleid beheren** en kies het beleid dat u wilt bewerken.

  ![Back-upbeleid beheren](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Back-upbeleid wijzigen](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

Beleidswijziging is van invloed op alle gekoppelde back-upitems en activeert **bijbehorende beveiligingstaken configureren.**

### <a name="inconsistent-policy"></a>Inconsistent beleid

Soms kan een bewerking voor het wijzigen van het beleid leiden tot een **inconsistente** beleidsversie voor sommige back-upitems. Dit gebeurt wanneer de bijbehorende **taak voor het configureren van** de beveiliging mislukt voor het back-upitem nadat een bewerking voor het wijzigen van het beleid is geactiveerd. Deze wordt als volgt weergegeven in de weergave back-upitem:

  ![Inconsistent beleid](./media/backup-azure-sql-database/inconsistent-policy.png)

U kunt de beleidsversie voor alle beïnvloede items in één klik herstellen:

  ![Inconsistent beleid herstellen](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="unregister-a-sql-server-instance"></a>Registratie van een SQL Server-exemplaar ongedaan maken

De registratie van een SQL Server ongedaan maken nadat u de beveiliging hebt uitgeschakeld, maar voordat u de kluis verwijdert:

1. Selecteer op het kluisdashboard onder **Beheren de** optie **Back-upinfrastructuur**.  

   ![Back-upinfrastructuur selecteren](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Onder **Beheerservers** selecteert u **Beveiligde servers**.

   ![Beveiligde servers selecteren](./media/backup-azure-sql-database/protected-servers.png)

3. Selecteer **in Beveiligde servers** de server waarop u de registratie ongedaan wilt maken. Als u de kluis wilt verwijderen, moet u de registratie van alle servers ongedaan maken.

4. Klik met de rechtermuisknop op de beveiligde server en selecteer **Registratie ongedaan maken.**

   ![Verwijderen selecteren](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Registreer de extensie opnieuw op de SQL Server VM

Soms kan de workloadextensie op de VM om de een of andere reden worden beïnvloed. In dergelijke gevallen mislukken alle bewerkingen die op de VM worden geactiveerd. Mogelijk moet u de extensie vervolgens opnieuw registreren op de VM. Met **de bewerking Opnieuw registreren** wordt de back-upextensie voor workloads opnieuw geïnstalleerd op de VM om bewerkingen door te kunnen gaan. U vindt deze optie onder **Back-upinfrastructuur** in de Recovery Services-kluis.

![Beveiligde servers onder Back-upinfrastructuur](./media/backup-azure-sql-database/protected-servers-backup-infrastructure.png)

Wees voorzichtig met deze optie. Wanneer deze bewerking wordt geactiveerd op een VM met een extensie die al in orde is, wordt de extensie opnieuw opgestart. Dit kan ertoe leiden dat alle taken die worden uitgevoerd, mislukken. Controleer op een of meer symptomen [voordat](backup-sql-server-azure-troubleshoot.md#re-registration-failures) u de bewerking voor opnieuw registreren activeert.

## <a name="next-steps"></a>Volgende stappen

Zie Troubleshoot [backups on a SQL Server database (Problemen met back-ups op een SQL Server database oplossen) voor meer informatie.](backup-sql-server-azure-troubleshoot.md)
