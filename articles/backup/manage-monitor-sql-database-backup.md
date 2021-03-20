---
title: SQL Server Db's op een virtuele Azure-machine beheren en controleren
description: In dit artikel wordt beschreven hoe u SQL Server-data bases die worden uitgevoerd op een virtuele machine van Azure kunt beheren en controleren.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: e37e6fc211b34b7e427b66db374a705faafd25f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97858727"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Back-ups van SQL Server-databases beheren en bewaken

In dit artikel worden algemene taken beschreven voor het beheren en bewaken van SQL Server-data bases die worden uitgevoerd op een virtuele Azure-machine (VM) en waarvan een back-up is gemaakt naar een Azure Backup Recovery Services kluis door de [Azure backup](backup-overview.md) -service. U leert hoe u taken en waarschuwingen bewaken, database beveiliging stopt en hervat, back-uptaken uitvoert en de registratie van een virtuele machine ongedaan maakt vanuit back-ups.

Als u nog geen back-ups voor uw SQL Server-data bases hebt geconfigureerd, raadpleegt u back-ups [maken van SQL server-data bases op virtuele](backup-azure-sql-database.md)

## <a name="monitor-backup-jobs-in-the-portal"></a>Back-uptaken in de portal bewaken

Azure Backup worden alle geplande bewerkingen en acties op aanvraag weer gegeven onder **back-uptaken** in de portal, met uitzonde ring van de geplande back-ups van het logboek, omdat ze zeer vaak kunnen worden uitgevoerd. De taken die u in deze Portal ziet, zijn onder andere database detectie en-registratie, het configureren van back-ups en back-up-en herstel bewerkingen.

![De portal voor back-uptaken](./media/backup-azure-sql-database/sql-backup-jobs-list.png)

Ga voor meer informatie over bewakings scenario's naar [bewaking in het Azure Portal](backup-azure-monitoring-built-in-monitor.md) en [bewaking met behulp van Azure monitor](backup-azure-monitoring-use-azuremonitor.md).  

## <a name="view-backup-alerts"></a>Waarschuwingen voor back-ups weergeven

Omdat logboek back-ups om de 15 minuten worden uitgevoerd, kan het lastig zijn om back-uptaken te controleren. Azure Backup vereenvoudigt de bewaking door e-mail waarschuwingen te verzenden. E-mail waarschuwingen zijn:

- Geactiveerd voor alle back-upfouten.
- Geconsolideerd op database niveau op fout code.
- Alleen verzonden voor de eerste back-upfout van een Data Base.

Database back-upwaarschuwingen bewaken:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer **back-upwaarschuwingen** op het kluis dashboard.

   ![Waarschuwingen voor back-ups selecteren](./media/backup-azure-sql-database/sql-backup-alerts-list.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Beveiliging van een SQL Server-database stoppen

U kunt op verschillende manieren stoppen met het maken van een back-up van een SQL Server Data Base:

- Stop alle toekomstige back-uptaken en verwijder alle herstel punten.
- Alle toekomstige back-uptaken stoppen en de herstel punten intact laten.

Houd rekening met het volgende als u ervoor kiest de herstelpunten intact te laten:

- Alle herstelpunten blijven voor onbepaalde tijd intact en alle verwijderbewerkingen zullen stoppen bij het stoppen van de beveiliging met behoud van gegevens.
- Er worden kosten in rekening gebracht voor het beveiligde exemplaar en de verbruikte opslag. Zie [Azure backup prijzen](https://azure.microsoft.com/pricing/details/backup/)voor meer informatie.
- Als u een gegevensbron verwijdert zonder back-ups te stoppen, mislukken nieuwe back-ups. Oude herstel punten verlopen volgens het beleid, maar het meest recente herstel punt wordt altijd bewaard totdat u de back-ups stopt en de gegevens verwijdert.

Ga als volgt te werk om de beveiliging van een database te stoppen:

1. Selecteer **Back-upitems** op het kluis dashboard.

2. Onder **type back-upbeheer** selecteert u **SQL in azure VM**.

    ![SQL in Azure VM selecteren](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Selecteer de data base waarvoor u de beveiliging wilt stoppen.

    ![De database selecteren waarvoor u de beveiliging wilt stoppen](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. Selecteer **back-up stoppen** in het menu Data Base.

    ![Back-up stoppen selecteren](./media/backup-azure-sql-database/stop-db-button.png)

5. Selecteer in het menu **back-up stoppen** of u gegevens wilt behouden of verwijderen. Als u wilt, kunt u een reden en opmerking opgeven.

    ![Gegevens behouden of verwijderen in het menu back-up stoppen](./media/backup-azure-sql-database/stop-backup-button.png)

6. Selecteer **back-up stoppen**.

> [!NOTE]
>
>Zie de veelgestelde vragen hieronder voor meer informatie over de optie gegevens verwijderen:
>
>- [Wat gebeurt er met de back-ups als ik een database uit een automatisch beveiligd exemplaar verwijder?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [Als ik de back-upbewerking van een automatisch beveiligde data base stop, wat is dan het gedrag?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Beveiliging voor een SQL-database hervatten

Wanneer u de beveiliging voor de SQL database stopt, kunt u de beveiliging later hervatten als u de optie **back-upgegevens behouden** selecteert. Als u de back-upgegevens niet behoudt, kunt u de beveiliging niet hervatten.

Als u de beveiliging voor een SQL database wilt hervatten:

1. Open het back-upitem en selecteer **back-up hervatten**.

    ![Back-up hervatten selecteren om de databasebeveiliging te hervatten](./media/backup-azure-sql-database/resume-backup-button.png)

2. Selecteer een beleid in het menu **Back-upbeleid** en selecteer vervolgens **Opslaan**.

## <a name="run-an-on-demand-backup"></a>Een on-demand back-up uitvoeren

U kunt verschillende typen back-ups op aanvraag uitvoeren:

- Volledige back-up
- Alleen te kopiëren volledige back-up
- Differentiële back-up
- Logboekback-up

Wanneer u de Bewaar periode voor een volledige back-up wilt opgeven, wordt het Bewaar bereik voor volledige back-up op aanvraag automatisch ingesteld op 45 dagen vanaf de huidige tijd.

Zie [SQL Server back-uptypen](backup-architecture.md#sql-server-backup-types)voor meer informatie.

## <a name="modify-policy"></a>Beleid wijzigen

Wijzig het beleid om de back-upfrequentie of het Bewaar bereik te wijzigen.

> [!NOTE]
> Elke wijziging in de retentie periode wordt retro actief toegepast op alle oudere herstel punten, naast de nieuwe.

Ga in het kluis dashboard naar beleid voor het **beheren** van  >  **back-ups** en kies het beleid dat u wilt bewerken.

  ![Back-upbeleid beheren](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Back-upbeleid wijzigen](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

Beleids wijzigingen zijn van invloed op alle gekoppelde back-upitems en triggers die overeenkomen met het **configureren van beveiligings** taken.

### <a name="inconsistent-policy"></a>Inconsistent beleid

Soms kan een bewerking voor het wijzigen van een beleid leiden tot een **inconsistente** beleids versie voor sommige back-upitems. Dit gebeurt wanneer de bijbehorende taak **Beveiliging configureren** mislukt voor het back-upitem nadat een bewerking voor het wijzigen van beleid is geactiveerd. Dit ziet er als volgt uit in de weer gave back-upitem:

  ![Inconsistent beleid](./media/backup-azure-sql-database/inconsistent-policy.png)

U kunt de beleids versie voor alle betrokken items met één klik oplossen:

  ![Inconsistent beleid corrigeren](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="unregister-a-sql-server-instance"></a>Registratie van een SQL Server-exemplaar ongedaan maken

Hef de registratie van een SQL Server-exemplaar op nadat u de beveiliging hebt uitgeschakeld, maar voordat u de kluis verwijdert:

1. Selecteer op het kluis dashboard onder **beheren** de optie **back-upinfrastructuur**.  

   ![Back-upinfrastructuur selecteren](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Onder **Beheerservers** selecteert u **Beveiligde servers**.

   ![Beveiligde servers selecteren](./media/backup-azure-sql-database/protected-servers.png)

3. Selecteer in **beveiligde servers** de server die u wilt verwijderen. Als u de kluis wilt verwijderen, moet u de registratie van alle servers ongedaan maken.

4. Klik met de rechter muisknop op de beveiligde server en selecteer **registratie ongedaan maken**.

   ![Verwijderen selecteren](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>De extensie opnieuw registreren op de SQL Server-VM

De uitbrei ding van de werk belasting op de VM kan soms worden beïnvloed door een of meer reden. In dergelijke gevallen zullen alle bewerkingen die op de virtuele machine worden geactiveerd, mislukken. Mogelijk moet u de extensie opnieuw registreren op de VM. Met de bewerking **opnieuw registreren** wordt de back-upextensie van de werk belasting op de VM opnieuw geïnstalleerd zodat bewerkingen kunnen worden voortgezet. U kunt deze optie vinden onder **back-upinfrastructuur** in de Recovery Services kluis.

![Beveiligde servers onder back-upinfrastructuur](./media/backup-azure-sql-database/protected-servers-backup-infrastructure.png)

Gebruik deze optie met een waarschuwing. Wanneer een virtuele machine wordt geactiveerd met een uitbrei ding die al in orde is, wordt de extensie door deze bewerking opnieuw gestart. Dit kan ertoe leiden dat alle taken die in uitvoering zijn, mislukken. Controleer op een of meer van de [symptomen](backup-sql-server-azure-troubleshoot.md#re-registration-failures) voordat u de bewerking opnieuw registreren start.

## <a name="next-steps"></a>Volgende stappen

Zie [problemen met back-ups in een SQL Server-Data Base oplossen](backup-sql-server-azure-troubleshoot.md)voor meer informatie.
