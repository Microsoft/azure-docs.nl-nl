---
title: Release opmerkingen voor Microsoft Azure Backup Server v3
description: In dit artikel vindt u informatie over bekende problemen en tijdelijke oplossingen voor Microsoft Azure Backup Server v3 (MABS).
ms.topic: conceptual
ms.date: 06/03/2020
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: 87bc415c125a387d98ac88255d77fb1867564acf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91254258"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Release opmerkingen voor Microsoft Azure Backup Server

In dit artikel vindt u de bekende problemen en tijdelijke oplossingen voor Microsoft Azure Backup Server v3 (MABS).

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Back-up en herstel mislukt voor geclusterde werk belastingen

**Beschrijving:** Het maken van een back-up/herstel mislukt voor geclusterde gegevens bronnen, zoals Hyper-V-cluster of SQL-cluster (SQL always on) of Exchange in database beschikbaarheids groep (DAG) na de upgrade van MABS v2 naar MABS v3.

**Tijdelijke oplossing:** Als u dit wilt voor komen, opent u SQL Server Management Studio (SSMS)) en voert u het volgende SQL-script uit op de DPM-Data Base:

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Upgrade uitvoeren naar MABS v3 mislukt in Russische land instelling

**Beschrijving:** Upgrade van MABS v2 naar MABS v3 in Russische land instelling mislukt met fout code **4387**.

**Tijdelijke oplossing:** Voer de volgende stappen uit om een upgrade uit te voeren naar MABS V3 met het Russische installatie pakket:

1. Maak een [back-up](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server#SSMSProcedure) van uw SQL database en verwijder MABS v2 (de beveiligde gegevens behouden tijdens het verwijderen).
2. Voer een upgrade uit naar SQL 2017 (Enter prise) en verwijder rapportage als onderdeel van de upgrade.
3. [Installeren](/sql/reporting-services/install-windows/install-reporting-services#install-your-report-server) SQL Server Reporting Services (SSRS).
4. [Installeren](/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS).
5. Rapportage configureren met de para meters zoals beschreven in de [SSRS-configuratie met SQL 2017](./backup-azure-microsoft-azure-backup.md#upgrade-mabs).
6. [Installeren](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Herstellen](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms) SQL door SSMS te gebruiken en het DPM-Sync-hulp programma uit te voeren, zoals [hier](/system-center/dpm/back-up-the-dpm-server#using-dpmsync)wordt beschreven.
8. Werk de eigenschap ' DataBaseVersion ' in dbo.tbl_DLS_GlobalSetting tabel met behulp van de volgende opdracht:

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. Start de MSDPM-service.

## <a name="after-installing-ur1-the-mabs-reports-arent-updated-with-new-rdl-files"></a>Na de installatie van UR1 worden de MABS-rapporten niet bijgewerkt met nieuwe RDL-bestanden

**Beschrijving**: met UR1 is het probleem met de rapport opmaak van MABS opgelost met bijgewerkte RDL-bestanden. De nieuwe RDL-bestanden worden niet automatisch vervangen door bestaande bestanden.

**Tijdelijke oplossing**: Voer de volgende stappen uit om de RDL-bestanden te vervangen:

1. Open SQL Reporting Services Web Portal-URL op de computer MABS.
1. Op de URL van de webportal is de map map dpmreports aanwezig in de indeling van **`DPMReports_<GUID>`**

    >[!NOTE]
    >Er is altijd maar één map met deze naamgevings Conventie. Als MABS is bijgewerkt van een vorige versie, kan er ook nog een oudere map zijn, maar u kunt deze niet openen.

    ![Map map dpmreports](./media/backup-mabs-release-notes-v3/dpm-reports-folder.png)

1. Selecteer en open de **`DPMReports_<GUID>`** map. De afzonderlijke rapport bestanden worden weer gegeven, zoals hieronder wordt weer gegeven.

    ![Lijst met afzonderlijke rapport bestanden](./media/backup-mabs-release-notes-v3/individual-report-files.png)

1. Selecteer de rapport bestanden die niet eindigen met een **rapport**, klik met de rechter muisknop op **optie** en selecteer **beheren**.

    ![Selecteer beheren voor rapport bestanden](./media/backup-mabs-release-notes-v3/manage-files.png)

1. Selecteer op de pagina nieuw de optie **vervangen** om de bestanden te vervangen door de meest recente rapport bestanden.

    De meest recente rapport bestanden vindt u in het pad `<MABS Installation Directory>\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    Bijvoorbeeld: `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    ![De bestanden vervangen door de meest recente rapport bestanden](./media/backup-mabs-release-notes-v3/replace-files.png)

    Nadat de bestanden zijn vervangen, moet u ervoor zorgen dat de **naam** en de **Beschrijving** intact zijn en niet leeg zijn.

1. Nadat de bestanden zijn vervangen, start u de MABS-services opnieuw en gebruikt u de rapport bestanden.

## <a name="next-steps"></a>Volgende stappen

[Wat is er nieuw in MABS](backup-mabs-whats-new-mabs.md)
