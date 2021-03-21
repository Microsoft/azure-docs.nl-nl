---
title: SQL Managed Instance-controle
description: Meer informatie over hoe u aan de slag kunt met Azure SQL Managed instance auditing met behulp van T-SQL
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 05/26/2020
ms.openlocfilehash: ae0d9696d869b2a260de643482a9f86c34bcc824
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575462"
---
# <a name="get-started-with-azure-sql-managed-instance-auditing"></a>Aan de slag met controle van Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Met controle van [Azure SQL Managed instance](sql-managed-instance-paas-overview.md) worden database gebeurtenissen bijgehouden en naar een audit logboek in uw Azure Storage-account geschreven. De controlefunctie biedt ook deze mogelijkheden:

- Naleving van wet- en regelgeving, inzicht in activiteiten in de database en in de afwijkingen en discrepanties die kunnen wijzen op problemen voor het bedrijf of vermoedelijke schendingen van de beveiliging.
- Mogelijk maken en faciliteren van nalevingsstandaarden, hoewel dit geen garantie biedt voor naleving. Zie de [Vertrouwenscentrum van Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), waar u de meest recente lijst met compatibiliteits certificeringen kunt vinden voor meer informatie over Azure-Program ma's die naleving van standaarden ondersteunen.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Controle instellen voor uw server naar Azure Storage

In de volgende sectie wordt de configuratie van de controle op uw beheerde exemplaar beschreven.

1. Ga naar de [Azure Portal](https://portal.azure.com).
2. Een Azure Storage- **container** maken waar audit logboeken worden opgeslagen.

   1. Ga naar het Azure Storage-account waar u de audit logboeken wilt opslaan.

      > [!IMPORTANT]
      > - Gebruik een opslag account in dezelfde regio als het beheerde exemplaar om Lees-en schrijf bewerkingen in meerdere regio's te voor komen. 
      > - Als uw opslag account zich achter een Virtual Network of een firewall bevindt, kunt u [toegang verlenen via een virtueel netwerk](../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network).
      > - Als u de retentieperiode van 0 (onbeperkte retentie) wijzigt in een andere waarde, moet u er rekening mee houden dat retentie alleen van toepassing is op logboeken die zijn geschreven nadat de waarde is gewijzigd, (logboeken die zijn geschreven tijdens de periode waarin de retentie was ingesteld op onbeperkt, blijven behouden, zelfs nadat retentie is ingeschakeld).

   1. Ga in het opslag account naar **overzicht** en klik op **blobs**.

      ![Widget Azure blobs](./media/auditing-configure/1_blobs_widget.png)

   1. Klik in het bovenste menu op **+ container** om een nieuwe container te maken.

      ![Pictogram van een BLOB-container maken](./media/auditing-configure/2_create_container_button.png)

   1. Geef een container **naam** op, stel **openbaar toegangs niveau** in op **privé** en klik vervolgens op **OK**.

      ![Configuratie van BLOB-container maken](./media/auditing-configure/3_create_container_config.png)

    > [!IMPORTANT]
    > Klanten die een onveranderbare logboek opslag voor hun server-of database niveau controle gebeurtenissen willen configureren, moeten de [instructies van Azure Storage](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes)volgen. (Zorg ervoor dat u **Extra toevoegen toestaan** selecteert wanneer u de onveranderbare Blob-opslag configureert.)
  
3. Nadat u de container voor de audit Logboeken hebt gemaakt, zijn er twee manieren om deze te configureren als het doel voor de audit logboeken: het [gebruik van T-SQL](#blobtsql) of [de gebruikers interface van de SQL Server Management Studio (SSMS)](#blobssms):

   - <a id="blobtsql"></a>**Blob-opslag configureren voor audit logboeken met T-SQL:**

     1. Klik in de lijst containers op de zojuist gemaakte container en klik vervolgens op **container eigenschappen**.

        ![Knop Eigenschappen van BLOB-container](./media/auditing-configure/4_container_properties_button.png)

     1. Kopieer de URL van de container door te klikken op het Kopieer pictogram en de URL op te slaan (bijvoorbeeld in Klad blok) voor toekomstig gebruik. De indeling van de container-URL moet `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![Kopie-URL van BLOB-container](./media/auditing-configure/5_container_copy_name.png)

     1. Genereer een Azure Storage **SAS-token** om toegangs rechten voor het beheerde exemplaar te verlenen aan het opslag account:

        - Ga naar het Azure Storage-account waarin u de container hebt gemaakt in de vorige stap.

        - Klik op de **hand tekening voor gedeelde toegang** in het menu **opslag instellingen** .

          ![Pictogram voor de hand tekening voor gedeelde toegang in het menu opslag instellingen](./media/auditing-configure/6_storage_settings_menu.png)

        - Configureer de SAS als volgt:

          - **Toegestane Services**: BLOB

          - **Begin datum**: gebruik de datum van gisteren om te voor komen dat er problemen met de tijd zone te maken hebben

          - **Eind datum**: Kies de datum waarop deze SAS-token verloopt

            > [!NOTE]
            > Vernieuw het token na verloop om mislukte audits te voor komen.

          - Klik op **SAS genereren**.

            ![SAS-configuratie](./media/auditing-configure/7_sas_configure.png)

        - De SAS-token wordt onderaan weer gegeven. Kopieer het token door te klikken op het Kopieer pictogram en sla het op (bijvoorbeeld in Klad blok) voor toekomstig gebruik.

          ![SAS-token kopiëren](./media/auditing-configure/8_sas_copy.png)

          > [!IMPORTANT]
          > Verwijder het vraag teken ('? ') vanaf het begin van het token.

     1. Maak verbinding met uw beheerde exemplaar via SQL Server Management Studio of een ander ondersteund hulp programma.

     1. Voer de volgende T-SQL-instructie uit om **een nieuwe referentie te maken** met behulp van de container-URL en het SAS-token dat u in de vorige stappen hebt gemaakt:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Voer de volgende T-SQL-instructie uit om een nieuwe server controle te maken (Kies uw eigen audit naam en gebruik de container-URL die u hebt gemaakt in de vorige stappen). Als deze niet wordt opgegeven, `RETENTION_DAYS` is de standaard waarde 0 (onbeperkte retentie):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' , RETENTION_DAYS =  integer )
        GO
        ```

     1. Ga door met het [maken van een server audit specificatie of specificatie van de database audit](#createspec).

   - <a id="blobssms"></a>**Blob-opslag configureren voor audit logboeken met behulp van SQL Server Management Studio 18:**

     1. Maak verbinding met het beheerde exemplaar met behulp van de SQL Server Management Studio-gebruikers interface.

     1. Vouw de hoofd notitie van Objectverkenner uit.

     1. Vouw het **beveiligings** knooppunt uit, klik met de rechter muisknop op het knoop punt **controles** en klik op **nieuwe controle**:

        ![Beveiligings-en controle knooppunt uitvouwen](./media/auditing-configure/10_mi_SSMS_new_audit.png)

     1. Zorg ervoor dat de **URL** is geselecteerd in de **controle bestemming** en klik op **Bladeren**:

        ![Azure Storage bladeren](./media/auditing-configure/11_mi_SSMS_audit_browse.png)

     1. Beschrijving Meld u aan bij uw Azure-account:

        ![Aanmelden bij Azure](./media/auditing-configure/12_mi_SSMS_sign_in_to_azure.png)

     1. Selecteer een abonnement, opslag account en BLOB-container in de vervolg keuzelijsten of maak uw eigen container door te klikken op **maken**. Wanneer u klaar bent, klikt u op **OK**:

        ![Azure-abonnement, opslag account en BLOB-container selecteren](./media/auditing-configure/13_mi_SSMS_select_subscription_account_container.png)

     1. Klik op **OK** in het dialoog venster **controle maken** .
     
     1. <a id="createspec"></a>Nadat u de BLOB-container als doel voor de audit Logboeken hebt geconfigureerd, maakt en activeert u een specificatie van de server audit of een database audit die u zou moeten voor SQL Server:

   - [T-SQL-hand leiding voor Server audit Specification maken](/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [T-SQL-hand leiding voor database audit specificatie maken](/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. Schakel de server controle in die u in stap 3 hebt gemaakt:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Meer informatie:

- [Controleren van verschillen tussen Azure SQL Managed instance en een data base in SQL Server](#auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server)
- [SERVER CONTROLE MAKEN](/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hubs-or-azure-monitor-logs"></a>Controle instellen voor uw server naar Event Hubs-of Azure Monitor-logboeken

Audit logboeken van een beheerd exemplaar kunnen worden verzonden naar Azure Event Hubs-of Azure Monitor-Logboeken. In deze sectie wordt beschreven hoe u dit kunt configureren:

1. Navigeer in de [Azure Portal](https://portal.azure.com/) naar het beheerde exemplaar.

2. Klik op **Diagnostische instellingen**.

3. Klik op **Diagnostische gegevens inschakelen**. Als diagnostische gegevens al is ingeschakeld, wordt in plaats daarvan de **Diagnostische instelling toevoegen** weer gegeven.

4. Selecteer **SQLSecurityAuditEvents** in de lijst met Logboeken.

5. Selecteer een doel voor de controle gebeurtenissen: Event Hubs, Azure Monitor Logboeken of beide. Configureer voor elk doel de vereiste para meters (bijvoorbeeld Log Analytics-werk ruimte).

6. Klik op **Opslaan**.

    ![Diagnostische instellingen configureren](./media/auditing-configure/9_mi_configure_diagnostics.png)

7. Maak verbinding met het beheerde exemplaar met behulp van **SQL Server Management Studio (SSMS)** of een andere ondersteunde client.

8. Voer de volgende T-SQL-instructie uit om een server controle te maken:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Maak en schakel een server audit specificatie of specificatie van de database audit in zoals u zou doen voor SQL Server:

   - [T-SQL-hand leiding voor Server audit Specification maken](/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [T-SQL-hand leiding voor database audit specificatie maken](/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Schakel de server controle in die is gemaakt in stap 8:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Controle Logboeken gebruiken

### <a name="consume-logs-stored-in-azure-storage"></a>Logboeken gebruiken die zijn opgeslagen in Azure Storage

Er zijn verschillende methoden die u kunt gebruiken om de controle logboeken van blobs weer te geven.

- Gebruik de systeem functie `sys.fn_get_audit_file` (T-SQL) om de controle logboek gegevens in tabel vorm te retour neren. Zie de [sys.fn_get_audit_file-documentatie](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)voor meer informatie over het gebruik van deze functie.

- U kunt audit logboeken verkennen met behulp van een hulp programma zoals [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). In Azure Storage worden controle Logboeken opgeslagen als een verzameling BLOB-bestanden binnen een container die is gedefinieerd voor het opslaan van de audit Logboeken. Zie voor meer informatie over de hiërarchie van de opslagmap, de naam conventies en de logboek indeling de [verwijzing naar de indeling van het BLOB-controle logboek](../database/audit-log-format.md).

- Raadpleeg aan de [slag met Azure SQL database controle](../../azure-sql/database/auditing-overview.md)voor een volledige lijst met verbruiks methoden voor controle Logboeken.

### <a name="consume-logs-stored-in-event-hubs"></a>Logboeken gebruiken die zijn opgeslagen in Event Hubs

Als u gegevens van de audit logboeken van Event Hubs wilt gebruiken, moet u een stroom instellen om gebeurtenissen te gebruiken en deze naar een doel te schrijven. Zie de documentatie van Azure Event Hubs voor meer informatie.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Logboeken die zijn opgeslagen in Azure Monitor Logboeken gebruiken en analyseren

Als audit logboeken naar Azure Monitor logboeken worden geschreven, zijn ze beschikbaar in de Log Analytics-werk ruimte, waar u geavanceerde zoek opdrachten kunt uitvoeren op de controle gegevens. Ga als uitgangs punt naar de Log Analytics-werk ruimte. Klik onder de sectie **Algemeen** op **Logboeken** en voer een eenvoudige query in, zoals: `search "SQLSecurityAuditEvents"` om de audit logboeken weer te geven.  

Met Azure Monitor-Logboeken kunt u in realtime operationeel inzicht krijgen met behulp van geïntegreerde Zoek-en aangepaste Dash boards waarmee u miljoenen records in al uw workloads en servers eenvoudig kunt analyseren. Zie voor aanvullende nuttige informatie over Azure Monitor Zoek taal en-opdrachten in Logboeken [Azure monitor logboeken zoeken](../../azure-monitor/logs/log-query-overview.md).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server"></a>Controleren van verschillen tussen data bases in Azure SQL Managed instance en data bases in SQL Server

De belangrijkste verschillen tussen controles in data bases in Azure SQL Managed instance en data bases in SQL Server zijn:

- Met Azure SQL Managed instance werkt auditing op server niveau en worden `.xel` logboek bestanden opgeslagen in Azure Blob-opslag.
- In SQL Server werkt audit op het niveau van de server, maar worden gebeurtenissen opgeslagen in gebeurtenis logboeken van bestanden systeem/Windows.

XEvent-controle in beheerde instanties ondersteunt Azure Blob-opslag doelen. Bestands-en Windows-logboeken worden **niet ondersteund**.

De belangrijkste verschillen in de `CREATE AUDIT` syntaxis voor de controle van Azure Blob-opslag zijn:

- Er wordt een nieuwe syntaxis `TO URL` opgegeven, waarmee u de URL kunt opgeven van de Azure Blob Storage-container waar de `.xel` bestanden worden geplaatst.
- Er wordt een nieuwe syntaxis `TO EXTERNAL MONITOR` gegeven om event hubs-en Azure monitor-logboek doelen in te scha kelen.
- De syntaxis `TO FILE` wordt **niet ondersteund** omdat een door Azure SQL beheerd exemplaar geen toegang heeft tot Windows-bestands shares.
- De optie shutdown wordt **niet ondersteund**.
- `queue_delay` van 0 wordt **niet ondersteund**.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg aan de [slag met Azure SQL database controle](../../azure-sql/database/auditing-overview.md)voor een volledige lijst met verbruiks methoden voor controle Logboeken.
- Zie de [Vertrouwenscentrum van Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), waar u de meest recente lijst met compatibiliteits certificeringen kunt vinden voor meer informatie over Azure-Program ma's die naleving van standaarden ondersteunen.

<!--Image references-->
