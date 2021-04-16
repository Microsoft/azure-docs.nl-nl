---
title: Azure SQL auditing voor Azure SQL Database en Azure Synapse Analytics
description: Gebruik Azure SQL Database om databasegebeurtenissen bij te houden in een auditlogboek.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/17/2021
ms.custom: azure-synapse, sqldbrb=1
ms.openlocfilehash: bc7ac6b97d10e5941e46b8be3e12baff32bded4a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483058"
---
# <a name="auditing-for-azure-sql-database-and-azure-synapse-analytics"></a>Controle voor Azure SQL Database en Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

De controle voor [Azure SQL Database](sql-database-paas-overview.md) en [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) houdt databasegebeurtenissen bij en schrijft deze naar een auditlogboek in uw Azure-opslagaccount, Log Analytics-werkruimte of Event Hubs.

De controlefunctie biedt ook deze mogelijkheden:

- Naleving van wet- en regelgeving, inzicht in activiteiten in de database en in de afwijkingen en discrepanties die kunnen wijzen op problemen voor het bedrijf of vermoedelijke schendingen van de beveiliging.

- Mogelijk maken en faciliteren van nalevingsstandaarden, hoewel dit geen garantie biedt voor naleving. Zie het [Vertrouwenscentrum van Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), waar u de meest recente lijst met nalevingscertificeringen van Azure SQL kunt vinden, voor meer informatie over Azure-programma's die nalevingsstandaarden ondersteunen.

> [!NOTE]
> Zie het volgende Azure SQL Managed Instance Aan de slag met SQL Managed Instance controle voor meer informatie [over SQL Managed Instance controle.](../managed-instance/auditing-configure.md)

## <a name="overview"></a><a id="overview"></a>Overzicht

U kunt de SQL Database gebruiken om:

- **Behoud** een audittrail van geselecteerde gebeurtenissen. U kunt categorieën van database-acties definiëren die moeten worden gecontroleerd.
- **Rapporteren** over databaseactiviteit. U kunt vooraf geconfigureerde rapporten en een dashboard gebruiken om snel aan de slag te gaan met de rapportage bvavan activiteiten en gebeurtenissen.
- **Rapporten** analyseren. U kunt verdachte gebeurtenissen, ongebruikelijke activiteiten en trends vinden.

> [!IMPORTANT]
> Controle voor Azure SQL Database en Azure Synapse is geoptimaliseerd voor beschikbaarheid en prestaties. Tijdens een zeer hoge activiteit, of een hoge netwerkbelasting, kunnen Azure SQL Database en Azure Synapse bewerkingen worden uitgevoerd en worden sommige gecontroleerde gebeurtenissen mogelijk niet registreren..

### <a name="auditing-limitations"></a>Controlebeperkingen

- **Premium-opslag** wordt **momenteel niet ondersteund.**
- **Hiërarchische naamruimte** **voor Azure Data Lake Storage Gen2 opslagaccount** wordt **momenteel niet ondersteund.**
- Het inschakelen van controle op een **onderbroken Azure Synapse** wordt niet ondersteund. Als u controle wilt inschakelen, hervat u Azure Synapse.
- Controle voor Azure Synapse **SQL-pools ondersteunt** alleen standaardactiegroepen voor **controle.**


#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Controlebeleid op serverniveau versus databaseniveau definiëren

Een controlebeleid kan worden gedefinieerd voor een specifieke database of als een [standaardserverbeleid](logical-servers.md) in Azure (dat als host voor SQL Database of Azure Synapse):

- Een serverbeleid is van toepassing op alle bestaande en nieuw gemaakte databases op de server.

- Als *servercontrole is ingeschakeld, is* dit *altijd van toepassing op de database*. De database wordt gecontroleerd, ongeacht de controle-instellingen van de database.

- Wanneer controlebeleid wordt gedefinieerd op databaseniveau naar een Log Analytics-werkruimte of een Event Hub-bestemming, behouden de volgende bewerkingen het controlebeleid op brondatabaseniveau niet:
    - [Database-kopie](database-copy.md)
    - [Terugzetten naar eerder tijdstip](recovery-using-backups.md)
    - [Geo-replicatie](active-geo-replication-overview.md) (secundaire database heeft geen controle op databaseniveau)

- Het inschakelen van controle op de database, naast  het inschakelen ervan op de server, overschrijven of wijzigen van een van de instellingen van de servercontrole. Beide controles worden naast elkaar uitgevoerd. Met andere woorden, de database wordt twee keer parallel gecontroleerd; eenmaal door het serverbeleid en eenmaal door het databasebeleid.

   > [!NOTE]
   > U moet voorkomen dat zowel servercontrole als databaseblobcontrole samen worden inschakelen, tenzij:
    >
    > - U wilt een ander *opslagaccount,* *retentieperiode of* *Log Analytics-werkruimte gebruiken* voor een specifieke database.
    > - U wilt gebeurtenistypen of categorieën controleren voor een specifieke database die verschillen van de rest van de databases op de server. U kunt bijvoorbeeld tabel invoegen die alleen voor een specifieke database moeten worden gecontroleerd.
   >
   > Anders raden we u aan alleen controle op serverniveau in te stellen en de controle op databaseniveau uitgeschakeld te laten voor alle databases.

#### <a name="remarks"></a>Opmerkingen

- Auditlogboeken worden geschreven naar **Blobs in een** Azure Blob-opslag in uw Azure-abonnement
- Auditlogboeken hebben de indeling .xel en kunnen worden geopend met [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
- Als u een onveranderbaar logboekopslag wilt configureren voor de controlegebeurtenissen op server- of [databaseniveau,](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes)volgt u de instructies van Azure Storage . Zorg ervoor dat u Extra **aaneeningen toestaan hebt geselecteerd wanneer** u de onveranderbare blobopslag configureert.
- U kunt auditlogboeken schrijven naar een Azure Storage-account achter een VNet of firewall. Zie Audit schrijven naar een opslagaccount achter VNet en firewall voor [specifieke instructies.](audit-write-storage-account-behind-vnet-firewall.md)
- Zie voor meer informatie over de logboekindeling, hiërarchie van de opslagmap en naamconventieën de [Blob Audit Log Format Reference](./audit-log-format.md).
- Controle op [alleen-lezen replica's](read-scale-out.md) wordt automatisch ingeschakeld. Zie de SQL Database AuditLogboekindeling voor meer informatie over de hiërarchie van de [opslagmappen, naamconventieën en logboekindeling.](audit-log-format.md)
- Wanneer u Azure AD-verificatie gebruikt, worden records met mislukte *aanmeldingen niet* weergegeven in het SQL-auditlogboek. Als u mislukte auditrecords voor aanmeldingen wilt weergeven, gaat u naar [de Azure Active Directory portal](../../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), waar details van deze gebeurtenissen worden in een logboek oplogt.
- Aanmeldingen worden door de gateway doorgestuurd naar het specifieke exemplaar waarin de database zich bevindt.  In het geval van AAD-aanmeldingen worden de referenties gecontroleerd voordat deze wordt gebruikt om u aan te melden bij de aangevraagde database.  In het geval van een storing wordt de aangevraagde database nooit geopend, dus wordt er geen controle uitgevoerd.  In het geval van SQL-aanmeldingen worden de referenties geverifieerd op de aangevraagde gegevens, zodat ze in dit geval kunnen worden gecontroleerd.  Geslaagde aanmeldingen, die uiteraard de database bereiken, worden in beide gevallen gecontroleerd.
- Nadat u uw controle-instellingen hebt geconfigureerd, kunt u de nieuwe functie voor detectie van bedreigingen in- en configureren om e-mailberichten te configureren voor het ontvangen van beveiligingswaarschuwingen. Wanneer u detectie van bedreigingen gebruikt, ontvangt u proactieve waarschuwingen over afwijkende databaseactiviteiten die kunnen duiden op mogelijke beveiligingsrisico's. Zie Aan de slag met [bedreigingsdetectie voor meer informatie.](threat-detection-overview.md)

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Controle voor uw server instellen

Het standaardcontrolebeleid omvat alle acties en de volgende set actiegroepen, waarmee alle query's en opgeslagen procedures worden gecontroleerd die worden uitgevoerd op de database, evenals geslaagde en mislukte aanmeldingen:
  
- BATCH_COMPLETED_GROUP
- SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
- FAILED_DATABASE_AUTHENTICATION_GROUP
  
U kunt de controle voor verschillende soorten acties en [actiegroepen](#manage-auditing) configureren met behulp van PowerShell, zoals beschreven in de sectie SQL Database controleren met Azure PowerShell beheren.

Azure SQL Database audit Azure Synapse 4000 tekens aan gegevens voor tekenvelden in een controlerecord op. Wanneer  de instructie of **de data_sensitivity_information** waarden die worden geretourneerd door een controleerbare actie meer dan 4000 tekens bevatten, worden alle gegevens buiten de eerste 4000 tekens afgekapt en niet **gecontroleerd.**
In de volgende sectie wordt de configuratie van de controle beschreven met behulp van de Azure Portal.

  > [!NOTE]
  > - Controle inschakelen voor een onderbroken toegewezen SQL-pool is niet mogelijk. Als u controle wilt inschakelen, moet u de onderbreking van de toegewezen SQL-pool inschakelen. Meer informatie over [toegewezen SQL-pool](../..//synapse-analytics/sql/best-practices-dedicated-sql-pool.md).
  > - Wanneer controle is geconfigureerd voor een Log Analytics-werkruimte of een Event Hub-bestemming via de [](../../azure-monitor/essentials/diagnostic-settings.md) Azure Portal- of PowerShell-cmdlet, wordt een diagnostische instelling gemaakt met de categorie SQLSecurityAuditEvents ingeschakeld.

1. Ga naar de [Azure Portal](https://portal.azure.com).
2. **Navigeer naar Controle** onder de kop Beveiliging in het deelvenster **SQL database** **sql-server.**
3. Als u liever een controlebeleid voor de server in te stellen, kunt u de **koppeling Serverinstellingen** weergeven selecteren op de controlepagina van de database. Vervolgens kunt u de controle-instellingen van de server weergeven of wijzigen. Controlebeleid voor servers is van toepassing op alle bestaande en nieuw gemaakte databases op deze server.

    ![Schermopname met de koppeling Serverinstellingen weergeven gemarkeerd op de pagina databasecontrole.](./media/auditing-overview/2_auditing_get_started_server_inherit.png)

4. Als u controle liever inschakelt op databaseniveau, schakelt **u Controle over** naar **AAN.** Als servercontrole is ingeschakeld, bestaat de door de database geconfigureerde controle naast de servercontrole.

5. U hebt meerdere opties voor het configureren van waar auditlogboeken worden geschreven. U kunt logboeken schrijven naar een Azure-opslagaccount, naar een Log Analytics-werkruimte voor gebruik door Azure Monitor logboeken of naar event hub voor verbruik met behulp van Event Hub. U kunt elke combinatie van deze opties configureren en auditlogboeken worden naar elk van deze opties geschreven.
  
   ![opslagopties](./media/auditing-overview/auditing-select-destination.png)

### <a name="auditing-of-microsoft-support-operations"></a><a id="auditing-of-microsoft-support-operations"></a>Controle van Microsoft-ondersteuning bewerkingen

Door de controle van Microsoft-ondersteuning-bewerkingen voor Azure SQL Server kunt u de bewerkingen van microsoft-ondersteuningstechnici controleren wanneer ze toegang nodig hebben tot uw server tijdens een ondersteuningsaanvraag. Het gebruik van deze mogelijkheid, samen met uw controle, zorgt voor meer transparantie in uw werknemers en maakt anomaliedetectie, trendvisualisatie en preventie van gegevensverlies mogelijk.

Als u Controle van Microsoft-ondersteuning-bewerkingen  wilt inschakelen, gaat u naar Controleren onder de kop Beveiliging in het **deelvenster van uw Azure SQL-server** en zet u Controle van **Microsoft-ondersteuningsbewerkingen** op **AAN.**

  > [!IMPORTANT]
  > Controle van Microsoft-ondersteuningsbewerkingen biedt geen ondersteuning voor opslagaccountbestemming. Als u de mogelijkheid wilt inschakelen, moet u een Log Analytics-werkruimte of een Event Hub-bestemming configureren.

![Schermopname van Microsoft-ondersteuning Bewerkingen](./media/auditing-overview/support-operations.png)

Als u de auditlogboeken van Microsoft-ondersteuning bewerkingen in uw Log Analytics-werkruimte wilt bekijken, gebruikt u de volgende query:

```kusto
AzureDiagnostics
| where Category == "DevOpsOperationsAudit"
```

### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>Controleren naar opslagbestemming

Als u het schrijven van auditlogboeken naar een opslagaccount wilt configureren, selecteert u **Opslag** wanneer u naar de **sectie Controle** gaat. Selecteer het Azure-opslagaccount waarin logboeken worden opgeslagen en selecteer vervolgens de bewaarperiode door **Geavanceerde eigenschappen te openen.** Klik vervolgens op **Opslaan**. Logboeken die ouder zijn dan de bewaarperiode, worden verwijderd.

- De standaardwaarde voor de retentieperiode is 0 (onbeperkte retentie). U kunt deze waarde wijzigen door de schuifregelaar **Retentie (dagen)** **in** Geavanceerde eigenschappen te verplaatsen bij het configureren van het opslagaccount voor controle.
  - Als u de retentieperiode van 0 (onbeperkte retentie) wijzigt in een andere waarde, moet u er rekening mee houden dat retentie alleen van toepassing is op logboeken die zijn geschreven nadat de waarde is gewijzigd, (logboeken die zijn geschreven tijdens de periode waarin de retentie was ingesteld op onbeperkt, blijven behouden, zelfs nadat retentie is ingeschakeld).

  ![opslagaccount](./media/auditing-overview/auditing_select_storage.png)

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Controleren naar Log Analytics-bestemming
  
Als u het schrijven van auditlogboeken naar een Log Analytics-werkruimte wilt configureren, selecteert u **Log Analytics en** opent u Log **Analytics-details.** Selecteer de Log Analytics-werkruimte waar logboeken worden geschreven en klik vervolgens op **OK.** Zie Een Log Analytics-werkruimte maken in de Azure Portal als u nog [geen Log Analytics-werkruimte hebt Azure Portal.](../../azure-monitor/logs/quick-create-workspace.md)

   ![LogAnalyticsworkspace](./media/auditing-overview/auditing_select_oms.png)

Zie De implementatie van Azure Monitor logboeken ontwerpen voor meer informatie over [Azure Monitor Log Analytics-werkruimte](../../azure-monitor/logs/design-logs-deployment.md)
   
### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>Controleren naar Event Hub-bestemming

Selecteer Event Hub om het schrijven van auditlogboeken naar een **Event Hub te configureren.** Selecteer de Event Hub waar logboeken worden geschreven en klik vervolgens op **Opslaan.** Zorg ervoor dat de Event Hub zich in dezelfde regio als uw database en server.

   ![Eventhub](./media/auditing-overview/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Auditlogboeken en -rapporten analyseren

Als u ervoor hebt gekozen om auditlogboeken te schrijven Azure Monitor logboeken:

- Gebruik [Azure Portal](https://portal.azure.com). Open de relevante database. Selecteer boven aan de  pagina Controle van de database **auditlogboeken weergeven.**

    ![auditlogboeken weergeven](./media/auditing-overview/auditing-view-audit-logs.png)

- Vervolgens kunt u de logboeken op twee manieren weergeven:

    Als u op **Log Analytics** bovenaan de pagina **Controlerecords** klikt, wordt de weergave Logboeken in de Log Analytics-werkruimte geopend, waar u het tijdsbereik en de zoekquery kunt aanpassen.

    ![openen in Log Analytics-werkruimte](./media/auditing-overview/auditing-log-analytics.png)

    Als u op **Dashboard** weergeven  bovenaan de pagina Controlerecords klikt, wordt een dashboard geopend met informatie over auditlogboeken, waar u kunt inzoomen op beveiligingsinzichten, toegang tot gevoelige gegevens en meer. Dit dashboard is ontworpen om u te helpen beveiligingsinzichten te krijgen voor uw gegevens.
    U kunt ook het tijdsbereik en de zoekquery aanpassen.
    ![Log Analytics-dashboard weergeven](media/auditing-overview/auditing-view-dashboard.png)

    ![Log Analytics-dashboard](media/auditing-overview/auditing-log-analytics-dashboard.png)

    ![Log Analytics-beveiligingsinzichten](media/auditing-overview/auditing-log-analytics-dashboard-data.png)

- U kunt ook toegang krijgen tot de auditlogboeken via de blade Log Analytics. Open uw Log Analytics-werkruimte en klik **onder de sectie** Algemeen op **Logboeken.** U kunt beginnen met een eenvoudige query, zoals: zoek *'SQLSecurityAuditEvents'* om de auditlogboeken weer te geven.
    Hier kunt u ook de logboeken Azure Monitor [om](../../azure-monitor/logs/log-query-overview.md)  geavanceerde zoekopdrachten uit te voeren op uw auditlogboekgegevens. Azure Monitor logboeken biedt u realtime operationele inzichten met behulp van geïntegreerd zoeken en aangepaste dashboards om eenvoudig miljoenen records te analyseren in al uw workloads en servers. Zie de zoekverwijzing voor Azure Monitor logboeken voor meer nuttige informatie over de zoektaal en opdrachten [Azure Monitor logboeken.](../../azure-monitor/logs/log-query-overview.md)

Als u ervoor hebt gekozen om auditlogboeken naar Event Hub te schrijven:

- Als u auditlogboeken van Event Hub wilt gebruiken, moet u een stroom instellen om gebeurtenissen te verbruiken en deze naar een doel te schrijven. Zie documentatie voor [Azure Event Hubs voor meer informatie.](../index.yml)
- Auditlogboeken in Event Hub worden vastgelegd in de body van [Apache Avro-gebeurtenissen](https://avro.apache.org/) en opgeslagen met JSON-opmaak met UTF-8-codering. Als u de auditlogboeken wilt lezen, kunt u [Avro-hulpprogramma's](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) of gelijksoortige hulpmiddelen gebruiken waarmee deze indeling wordt verwerkt.

Als u ervoor hebt gekozen om auditlogboeken naar een Azure-opslagaccount te schrijven, zijn er verschillende methoden die u kunt gebruiken om de logboeken weer te bieden:

- Auditlogboeken worden samengevoegd in het account dat u tijdens de installatie hebt gekozen. U kunt auditlogboeken verkennen met behulp van een hulpprogramma zoals [Azure Storage Explorer](https://storageexplorer.com/). In Azure Storage worden auditlogboeken opgeslagen als een verzameling blob-bestanden in een container met de naam **sqld sqlditlogs.** Zie de SQL Database AuditLogboekindeling voor meer informatie over de hiërarchie van de [opslagmappen, naamconventieën en logboekindeling.](./audit-log-format.md)

- Gebruik [Azure Portal](https://portal.azure.com).  Open de relevante database. Klik boven aan de  pagina Controle van de database op **Auditlogboeken weergeven.**

    ![auditlogboeken weergeven](./media/auditing-overview/auditing-view-audit-logs.png)

    **Controlerecords** worden geopend, van waaruit u de logboeken kunt bekijken.

  - U kunt specifieke datums weergeven door boven aan de **pagina** **Controlerecords** op Filteren te klikken.
  - U kunt schakelen tussen auditrecords die zijn gemaakt door het *controlebeleid van* de server en het *databasecontrolebeleid* door Controlebron **in te schakelen.**

       ![Schermopname met de opties voor het weergeven van de controlerecords.]( ./media/auditing-overview/8_auditing_get_started_blob_audit_records.png)

- Gebruik de systeemfunctie **sys.fn_get_audit_file** (T-SQL) om de auditlogboekgegevens in tabelvorm te retourneren. Zie voor meer informatie over het gebruik van [deze functie sys.fn_get_audit_file.](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)

- Gebruik **Auditbestanden samenvoegen** in SQL Server Management Studio (vanaf SSMS 17):
    1. Selecteer in het menu SSMS bestand  >  **Auditbestanden**  >  **samenvoegen openen.**

        ![Schermopname van de menuoptie Auditbestanden samenvoegen.](./media/auditing-overview/9_auditing_get_started_ssms_1.png)
    2. Het **dialoogvenster Auditbestanden** toevoegen wordt geopend. Selecteer een van de **opties Toevoegen** om te kiezen of u auditbestanden van een lokale schijf wilt samenvoegen of wilt importeren uit Azure Storage. U moet uw gegevens en accountsleutel Azure Storage op te geven.

    3. Nadat alle bestanden die moeten worden samengevoegd zijn toegevoegd, klikt u op **OK om** de samenvoegbewerking te voltooien.

    4. Het samengevoegde bestand wordt geopend in SSMS, waar u het kunt bekijken en analyseren, en het bestand kunt exporteren naar een XEL- of CSV-bestand of naar een tabel.

- Gebruik Power BI. U kunt auditlogboekgegevens weergeven en analyseren in Power BI. Zie Auditlogboekgegevens analyseren in Power BI voor meer informatie en om toegang te [krijgen tot een downloadbare sjabloon.](https://techcommunity.microsoft.com/t5/azure-database-support-blog/sql-azure-blob-auditing-basic-power-bi-dashboard/ba-p/368895)
- Download logboekbestanden van uw Azure Storage blobcontainer via de portal of met behulp van een hulpprogramma zoals [Azure Storage Explorer](https://storageexplorer.com/).
  - Nadat u een logboekbestand lokaal hebt gedownload, dubbelklikt u op het bestand om de logboeken in SSMS te openen, weer te geven en te analyseren.
  - U kunt ook meerdere bestanden tegelijk downloaden via Azure Storage Explorer. Als u dit wilt doen, klikt u met de rechtermuisknop op een specifieke submap en selecteert u **Opslaan als om** op te slaan in een lokale map.

- Aanvullende methoden:

  - Nadat u verschillende bestanden of een submap met logboekbestanden hebt gedownload, kunt u deze lokaal samenvoegen zoals beschreven in de instructies in SSMS Merge Audit Files die eerder zijn beschreven.
  - Logboeken voor blobcontrole programmatisch weergeven: [query's uitvoeren](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) op uitgebreide gebeurtenissenbestanden met behulp van PowerShell.

## <a name="production-practices"></a><a id="production-practices"></a>Productieprocedures


### <a name="auditing-geo-replicated-databases"></a>Geo-gerepliceerde databases controleren

Wanneer u met geo-gerepliceerde databases controle in de primaire database inschakelen, heeft de secundaire database een identiek controlebeleid. Het is ook mogelijk om controle in te stellen voor de secundaire database door controle in te stellen op de secundaire **server**, onafhankelijk van de primaire database.

- Serverniveau **(aanbevolen):** schakel controle in op zowel de primaire **server** als de secundaire **server.** De primaire en secundaire databases worden elk onafhankelijk gecontroleerd op basis van hun respectieve beleid op serverniveau.
- Databaseniveau: Controle op databaseniveau voor secundaire databases kan alleen worden geconfigureerd vanuit de controle-instellingen van de primaire database.
  - Controle moet zijn ingeschakeld op de primaire *database zelf,* niet op de server.
  - Nadat controle is ingeschakeld voor de primaire database, wordt deze ook ingeschakeld op de secundaire database.

    > [!IMPORTANT]
    > Bij controle op databaseniveau zijn de opslaginstellingen voor de secundaire database identiek aan die van de primaire database, waardoor regionaal verkeer wordt veroorzaakt. We raden u aan om alleen controle op serverniveau in te stellen en de controle op databaseniveau uitgeschakeld te laten voor alle databases.

### <a name="storage-key-regeneration"></a>Opnieuw generatie van opslagsleutels

In productie vernieuwt u waarschijnlijk regelmatig uw opslagsleutels. Wanneer u auditlogboeken naar Azure Storage schrijft, moet u uw controlebeleid opnieuw opslaan bij het vernieuwen van uw sleutels. Het proces verloopt als volgt:

1. Open **Geavanceerde eigenschappen** onder **Opslag**. Selecteer secundair **in het vak Toegangssleutel** **voor opslag.** Klik vervolgens **boven** aan de pagina controleconfiguratie op Opslaan.

    ![Schermopname van het proces voor het selecteren van een secundaire toegangssleutel voor opslag.](./media/auditing-overview/5_auditing_get_started_storage_key_regeneration.png)
2. Ga naar de pagina opslagconfiguratie en regenerer de primaire toegangssleutel opnieuw.

    ![Navigatievenster](./media/auditing-overview/6_auditing_get_started_regenerate_key.png)
3. Terug naar de pagina controleconfiguratie, schakelt u de toegangssleutel voor opslag over van secundair naar primair en klikt u vervolgens op **OK.** Klik vervolgens **boven** aan de pagina controleconfiguratie op Opslaan.
4. Terug naar de opslagconfiguratiepagina en de secundaire toegangssleutel opnieuw (ter voorbereiding op de vernieuwingscyclus van de volgende sleutel).

## <a name="manage-azure-sql-database-auditing"></a><a id="manage-auditing"></a>Controle van Azure SQL Database beheren

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

**PowerShell-cmdlets (inclusief ondersteuning voor WHERE-component voor aanvullende filtering)**:

- [Databasecontrolebeleid maken of bijwerken (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Servercontrolebeleid maken of bijwerken (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Databasecontrolebeleid (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Controlebeleid voor servers (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Databasecontrolebeleid verwijderen (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Servercontrolebeleid verwijderen (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Zie Controle en detectie van bedreigingen configureren met Behulp van PowerShell voor een [voorbeeldscript.](scripts/auditing-threat-detection-powershell-configure.md)

### <a name="using-rest-api"></a>REST API gebruiken

**REST API:**

- [Databasecontrolebeleid maken of bijwerken](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Controlebeleid voor servers maken of bijwerken](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Databasecontrolebeleid op halen](/rest/api/sql/database%20auditing%20settings/get)
- [Controlebeleid voor servers op halen](/rest/api/sql/server%20auditing%20settings/get)

Uitgebreid beleid met ondersteuning voor WHERE-component voor aanvullende filtering:

- [Uitgebreide controlebeleid *voor* databases maken of bijwerken](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Uitgebreide controlebeleid *voor* server maken of bijwerken](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Uitgebreid *controlebeleid* voor databases krijgen](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Uitgebreid *controlebeleid* voor de server op halen](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-cli"></a>Azure CLI gebruiken

- [Het controlebeleid van een server beheren](/cli/azure/sql/server/audit-policy)
- [Het controlebeleid van een database beheren](/cli/azure/sql/db/audit-policy)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen gebruiken

U kunt uw Azure SQL Database beheren met behulp van Azure Resource Manager-sjablonen, zoals wordt weergegeven in de volgende voorbeelden: [](../../azure-resource-manager/management/overview.md)

- [Implementeer een Azure SQL Database met Controle ingeschakeld voor het schrijven van auditlogboeken naar een Azure Blob Storage-account](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Implementeer een Azure SQL Database met Controle ingeschakeld om auditlogboeken naar Log Analytics te schrijven](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Implementeer een Azure SQL Database met Controle ingeschakeld voor het schrijven van auditlogboeken naar Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> De gekoppelde voorbeelden staan in een externe openbare opslagplaats en worden zonder garantie geleverd en worden niet ondersteund onder een programma/service van Microsoft.
