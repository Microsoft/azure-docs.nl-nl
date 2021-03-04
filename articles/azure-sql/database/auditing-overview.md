---
title: Azure SQL auditing voor Azure SQL Database en Azure Synapse Analytics
description: Gebruik Azure SQL Database controle om database gebeurtenissen in een audit logboek bij te houden.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/03/2021
ms.custom: azure-synapse, sqldbrb=1
ms.openlocfilehash: e01f44d363d038bd2ea4b985e12c9afc200f2c20
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046445"
---
# <a name="auditing-for-azure-sql-database-and-azure-synapse-analytics"></a>Controleren op Azure SQL Database en Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Controle voor [Azure SQL database](sql-database-paas-overview.md) en [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) houdt database gebeurtenissen bij en schrijft deze naar een audit logboek in uw Azure storage-account, Log Analytics werk ruimte of event hubs.

De controlefunctie biedt ook deze mogelijkheden:

- Naleving van wet- en regelgeving, inzicht in activiteiten in de database en in de afwijkingen en discrepanties die kunnen wijzen op problemen voor het bedrijf of vermoedelijke schendingen van de beveiliging.

- Mogelijk maken en faciliteren van nalevingsstandaarden, hoewel dit geen garantie biedt voor naleving. Voor meer informatie over Azure-Program ma's die naleving van standaarden ondersteunen, raadpleegt u de [Vertrouwenscentrum van Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) waarin u de meest recente lijst met Azure SQL-compatibiliteits certificeringen kunt vinden.

> [!NOTE]
> Zie het volgende artikel aan de [slag met controle van SQL Managed instance](../managed-instance/auditing-configure.md)voor meer informatie over het controleren van Azure SQL Managed instances.

## <a name="overview"></a><a id="overview"></a>Overzicht

U kunt SQL Database controle gebruiken voor het volgende:

- Een audittrail van geselecteerde gebeurtenissen **bewaren** . U kunt categorieën van database-acties definiëren die moeten worden gecontroleerd.
- **Rapport** over de database activiteit. U kunt vooraf geconfigureerde rapporten en een dashboard gebruiken om snel aan de slag te gaan met de rapportage bvavan activiteiten en gebeurtenissen.
- Rapporten **analyseren** . U kunt verdachte gebeurtenissen, ongebruikelijke activiteiten en trends vinden.

> [!IMPORTANT]
> Controle voor Azure SQL Database en Azure Synapse is geoptimaliseerd voor Beschik baarheid en prestaties. Tijdens een zeer hoge activiteit of hoge netwerk belasting Azure SQL Database en Azure Synapse toestaan dat bewerkingen worden voortgezet en kunnen sommige gecontroleerde gebeurtenissen niet worden vastgelegd.

### <a name="auditing-limitations"></a>Controle beperkingen

- **Premium-opslag** wordt momenteel **niet ondersteund**.
- **Hiërarchische naam ruimte** voor **Azure data Lake Storage Gen2 opslag account** wordt momenteel **niet ondersteund**.
- Het inschakelen van controle op een onderbroken **Azure Synapse** wordt niet ondersteund. Als u controle wilt inschakelen, hervat u Azure Synapse.
- Controle voor **Azure Synapse SQL-Pools** ondersteunt **alleen** standaard controle-actie groepen.


#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Het controle beleid op server niveau versus database niveau definiëren

Er kan een controle beleid worden gedefinieerd voor een specifieke data base of als een standaard [Server](logical-servers.md) beleid in azure (die als host fungeert voor SQL database of Azure Synapse):

- Een server beleid is van toepassing op alle bestaande en nieuw gemaakte data bases op de server.

- Als *Server controle is ingeschakeld*, *is deze altijd van toepassing op de data base*. De data base wordt gecontroleerd, ongeacht de controle-instellingen voor de data base.

- Wanneer het controle beleid op database niveau is gedefinieerd voor een Log Analytics-werk ruimte of een event hub-doel, blijft het controle beleid op database niveau voor de volgende bewerkingen:
    - [Database-kopie](database-copy.md)
    - [Herstel naar een bepaald tijdstip](recovery-using-backups.md)
    - [Geo-replicatie](active-geo-replication-overview.md) (secundaire data base heeft geen controle op database niveau)

- Als u controle inschakelt voor de data base en deze op de server inschakelt, worden de instellingen van de server controle *niet* overschreven of gewijzigd. Beide controles bestaan naast elkaar. Met andere woorden, de data base wordt twee keer parallel gecontroleerd. eenmaal door het Server beleid en eenmaal door het database beleid.

   > [!NOTE]
   > Vermijd het inschakelen van controle van zowel server controle als data base-blobs, tenzij:
    >
    > - U wilt een ander *opslag account*, *bewaar periode* of *log Analytics werk ruimte* gebruiken voor een specifieke data base.
    > - U wilt controle van gebeurtenis typen of categorieën voor een specifieke data base die verschilt van de rest van de data bases op de server. U kunt bijvoorbeeld tabellen invoegen die alleen moeten worden gecontroleerd voor een specifieke data base.
   >
   > Anders is het raadzaam om alleen controle op server niveau in te scha kelen en de controle op database niveau uitgeschakeld te laten voor alle data bases.

#### <a name="remarks"></a>Opmerkingen

- Audit logboeken worden geschreven om **blobs toe te voegen** in een Azure Blob-opslag in uw Azure-abonnement
- Audit logboeken hebben de Xel-indeling en kunnen worden geopend met behulp van [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
- Als u een onveranderbaar logboek archief wilt configureren voor de controle gebeurtenissen op de server of op database niveau, volgt u de [instructies van Azure Storage](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes). Zorg ervoor dat u **Extra toevoegen toestaan** selecteert wanneer u de onveranderbare Blob-opslag configureert.
- U kunt audit logboeken schrijven naar een Azure Storage-account achter een VNet of firewall. Zie voor specifieke instructies [audit schrijven naar een opslag account achter VNet en firewall](audit-write-storage-account-behind-vnet-firewall.md).
- Zie de naslag informatie over de [indeling van BLOB-controle logboeken](./audit-log-format.md)voor meer informatie over de logboek indeling, de hiërarchie van de opslag map en naam conventies.
- Controle op [alleen-lezen replica's](read-scale-out.md) wordt automatisch ingeschakeld. Voor meer informatie over de hiërarchie van de opslag mappen, naam conventies en logboek indeling raadpleegt u de [SQL database controle logboek indeling](audit-log-format.md).
- Wanneer u Azure AD-verificatie gebruikt, worden records met mislukte aanmeldingen *niet* weer gegeven in het SQL-controle logboek. Als u mislukte aanmeldings controle records wilt weer geven, gaat u naar de [Azure Active Directory-Portal](../../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), waarin de details van deze gebeurtenissen worden vastgelegd.
- Aanmeldingen worden door de gateway doorgestuurd naar het specifieke exemplaar waarin de database zich bevindt.  In het geval van AAD-aanmeldingen worden de referenties gecontroleerd voordat deze wordt gebruikt om u aan te melden bij de aangevraagde database.  In het geval van een storing wordt de aangevraagde database nooit geopend, dus wordt er geen controle uitgevoerd.  In het geval van SQL-aanmeldingen worden de referenties gecontroleerd op de aangevraagde gegevens, dus in dit geval kunnen ze worden gecontroleerd.  Geslaagde aanmeldingen, die uiteraard de database bereiken, worden in beide gevallen gecontroleerd.
- Nadat u de controle-instellingen hebt geconfigureerd, kunt u de nieuwe functie voor het detecteren van bedreigingen inschakelen en e-mail berichten configureren voor het ontvangen van beveiligings waarschuwingen. Wanneer u detectie van dreigingen gebruikt, ontvangt u proactieve waarschuwingen over afwijkende database activiteiten die kunnen wijzen op mogelijke beveiligings dreigingen. Zie aan de slag [met detectie van bedreigingen](threat-detection-overview.md)voor meer informatie.

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Controle instellen voor uw server

Het standaard controlebeleid bevat alle acties en de volgende set actie groepen, waarmee alle query's en opgeslagen procedures worden gecontroleerd die worden uitgevoerd op de data base, evenals geslaagde en mislukte aanmeldingen:
  
- BATCH_COMPLETED_GROUP
- SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
- FAILED_DATABASE_AUTHENTICATION_GROUP
  
U kunt controle configureren voor verschillende typen acties en actie groepen met behulp van Power shell, zoals beschreven in de sectie [Manage SQL database auditing using Azure PowerShell](#manage-auditing) .

Azure SQL Database-en Azure Synapse audit slaat 4000 tekens aan gegevens op voor teken velden in een controle record. Wanneer de **instructie** of de **data_sensitivity_information** waarden die zijn geretourneerd door een Controleer bare actie meer dan 4000 tekens bevatten, worden alle gegevens na de eerste 4000 tekens **afgekapt en niet gecontroleerd**.
In de volgende sectie wordt de configuratie van de controle met behulp van de Azure Portal beschreven.

  > [!NOTE]
  > - Het is niet mogelijk om controle in te scha kelen op een onderbroken toegewezen SQL-groep. Als u de controle wilt inschakelen, moet u de exclusieve SQL-groep verwijderen. Meer informatie over een [toegewezen SQL-groep](../..//synapse-analytics/sql/best-practices-sql-pool.md).
  > - Als controle is geconfigureerd voor een Log Analytics-werk ruimte of een even hub-doel via de Azure Portal-of Power shell-cmdlet, wordt een [Diagnostische instelling](../../azure-monitor/essentials/diagnostic-settings.md) gemaakt met de categorie ' SQLSecurityAuditEvents ' ingeschakeld.

1. Ga naar de [Azure Portal](https://portal.azure.com).
2. Navigeer naar **controle** onder de kop beveiliging in het deel venster **SQL database** of **SQL Server** .
3. Als u liever een server controle beleid instelt, kunt u de koppeling **Server instellingen weer geven** op de pagina database controle selecteren. U kunt vervolgens de instellingen voor de controle van de server weer geven of wijzigen. Het controle beleid voor servers is van toepassing op alle bestaande en nieuw gemaakte data bases op deze server.

    ![Scherm afbeelding met de koppeling weer gave server instellingen is gemarkeerd op de pagina voor het controleren van de data base.](./media/auditing-overview/2_auditing_get_started_server_inherit.png)

4. Als u de controle wilt inschakelen op het niveau van de data base, schakelt u **controle** in **op** aan. Als server controle is ingeschakeld, is de door de data base geconfigureerde controle naast de server controle aanwezig.

5. U hebt meerdere opties voor het configureren van de locatie waar audit logboeken worden geschreven. U kunt Logboeken schrijven naar een Azure-opslag account, naar een Log Analytics-werk ruimte voor het gebruik van Azure Monitor-Logboeken of Event Hub voor gebruik met Event Hub. U kunt een wille keurige combi natie van deze opties configureren en er worden controle logboeken naar elke optie geschreven.
  
   ![opslag opties](./media/auditing-overview/auditing-select-destination.png)

### <a name="auditing-of-microsoft-support-operations"></a><a id="auditing-of-microsoft-support-operations"></a>Controle van Microsoft Ondersteuning bewerkingen

Door de controle van Microsoft Ondersteuning-bewerkingen voor Azure SQL Server kunt u de bewerkingen van micro soft-ondersteunings medewerkers controleren wanneer ze toegang nodig hebben tot uw server tijdens een ondersteunings aanvraag. Het gebruik van deze mogelijkheid, samen met uw controle, zorgt voor meer transparantie in uw werk nemers en maakt anomalie detectie, trend visualisatie en preventie van gegevens verlies mogelijk.

Als u de controle van Microsoft Ondersteuning bewerkingen wilt inschakelen, navigeert u naar **controle** onder de kop beveiliging in het deel venster van de **Azure SQL-Server** en schakelt u de **controle van micro soft-ondersteunings bewerkingen** naar **in**.

  > [!IMPORTANT]
  > Controle van micro soft-ondersteunings bewerkingen biedt geen ondersteuning voor opslag account bestemming. Als u de mogelijkheid wilt inschakelen, moet een Log Analytics-werk ruimte of een event hub-doel worden geconfigureerd.

![Scherm opname van Microsoft Ondersteuning bewerkingen](./media/auditing-overview/support-operations.png)

Gebruik de volgende query om de controle logboeken van Microsoft Ondersteuning-bewerkingen in uw Log Analytics-werk ruimte te bekijken:

```kusto
AzureDiagnostics
| where Category == "DevOpsOperationsAudit"
```

### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>Controleren op opslag bestemming

Als u het schrijven van audit logboeken naar een opslag account wilt configureren, selecteert u **opslag** en opent u **opslag Details**. Selecteer het Azure-opslag account waarin de logboeken worden opgeslagen en selecteer vervolgens de Bewaar periode. Klik vervolgens op **OK**. Logboeken die ouder zijn dan de Bewaar periode, worden verwijderd.

- De standaard waarde voor de Bewaar periode is 0 (onbeperkte retentie). U kunt deze waarde wijzigen door de schuif regelaar voor **bewaren (dagen)** in **opslag instellingen** te verplaatsen bij het configureren van het opslag account voor controle.
  - Als u de retentieperiode van 0 (onbeperkte retentie) wijzigt in een andere waarde, moet u er rekening mee houden dat retentie alleen van toepassing is op logboeken die zijn geschreven nadat de waarde is gewijzigd, (logboeken die zijn geschreven tijdens de periode waarin de retentie was ingesteld op onbeperkt, blijven behouden, zelfs nadat retentie is ingeschakeld).

  ![opslagaccount](./media/auditing-overview/auditing_select_storage.png)

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Controleren op Log Analytics bestemming
  
Als u het schrijven van audit logboeken naar een Log Analytics-werk ruimte wilt configureren, selecteert u **log Analytics** en opent u **log Analytics Details**. Selecteer of maak de Log Analytics-werk ruimte waar de logboeken worden geschreven en klik vervolgens op **OK**.

   ![LogAnalyticsworkspace](./media/auditing-overview/auditing_select_oms.png)

Zie [de implementatie van uw Azure monitor-logboeken ontwerpen](../../azure-monitor/logs/design-logs-deployment.md) voor meer informatie over Azure monitor log Analytics-werk ruimte.
   
### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>Controleren op Event hub-doel

Als u audit logboeken wilt configureren voor een Event Hub, selecteert u **Event hub** en opent u **Details van Event hub**. Selecteer de Event Hub waar de logboeken worden geschreven en klik vervolgens op **OK**. Zorg ervoor dat de Event Hub zich in dezelfde regio bevindt als de-data base en-server.

   ![Eventhub](./media/auditing-overview/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Controle logboeken en-rapporten analyseren

Als u ervoor hebt gekozen om audit logboeken naar Azure Monitor-logboeken te schrijven:

- Gebruik [Azure Portal](https://portal.azure.com). Open de relevante database. Klik boven aan de **controle** pagina van de Data Base op **audit logboeken weer geven**.

    ![audit logboeken weer geven](./media/auditing-overview/auditing-view-audit-logs.png)

- Vervolgens hebt u twee manieren om de logboeken weer te geven:

    Als u op **log Analytics** boven aan de pagina **controle records** klikt, wordt de weer gave Logboeken in log Analytics werk ruimte geopend, waar u het tijds bereik en de zoek query kunt aanpassen.

    ![openen in Log Analytics werk ruimte](./media/auditing-overview/auditing-log-analytics.png)

    Als u op het **dash board weer geven** boven aan de pagina **controle records** klikt, wordt er een dash board geopend met informatie over de audit logboeken, waar u kunt inzoomen op beveiligings inzichten, toegang tot gevoelige gegevens en meer. Dit dash board is ontworpen om u te helpen bij het verkrijgen van beveiligings inzichten voor uw gegevens.
    U kunt ook het tijds bereik en de zoek query aanpassen.
    ![Log Analytics dash board weer geven](media/auditing-overview/auditing-view-dashboard.png)

    ![Log Analytics dash board](media/auditing-overview/auditing-log-analytics-dashboard.png)

    ![Log Analytics Security Insights](media/auditing-overview/auditing-log-analytics-dashboard-data.png)

- U kunt ook toegang krijgen tot de audit logboeken vanuit Log Analytics Blade. Open uw Log Analytics-werk ruimte en klik onder **algemene** sectie op **Logboeken**. U kunt beginnen met een eenvoudige query, bijvoorbeeld: *Zoek naar SQLSecurityAuditEvents* om de audit logboeken weer te geven.
    Hier kunt u ook [Azure monitor-logboeken](../../azure-monitor/logs/log-query-overview.md)  gebruiken om geavanceerde zoek opdrachten uit te voeren in uw audit logboek gegevens. Met Azure Monitor-Logboeken kunt u in realtime operationeel inzicht krijgen met behulp van geïntegreerde Zoek-en aangepaste Dash boards waarmee u miljoenen records in al uw workloads en servers eenvoudig kunt analyseren. Zie voor aanvullende nuttige informatie over Azure Monitor Zoek taal en-opdrachten in Logboeken [Azure monitor logboeken zoeken](../../azure-monitor/logs/log-query-overview.md).

Als u ervoor hebt gekozen om audit logboeken naar Event hub te schrijven:

- Als u gegevens van de audit logboeken van Event hub wilt gebruiken, moet u een stroom instellen om gebeurtenissen te gebruiken en deze naar een doel te schrijven. Zie de [documentatie van Azure Event hubs](../index.yml)voor meer informatie.
- Audit Logboeken in Event hub worden vastgelegd in de hoofd tekst van [Apache Avro](https://avro.apache.org/) -gebeurtenissen en opgeslagen met behulp van JSON-indeling met UTF-8-code ring. Als u de auditlogboeken wilt lezen, kunt u [Avro-hulpprogramma's](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) of gelijksoortige hulpmiddelen gebruiken waarmee deze indeling wordt verwerkt.

Als u ervoor hebt gekozen om audit logboeken naar een Azure Storage-account te schrijven, zijn er verschillende methoden om de logboeken weer te geven:

- Audit logboeken worden geaggregeerd in het account dat u tijdens de installatie hebt gekozen. U kunt audit logboeken verkennen met behulp van een hulp programma zoals [Azure Storage Explorer](https://storageexplorer.com/). In azure Storage worden controle Logboeken opgeslagen als een verzameling BLOB-bestanden in een container met de naam **sqldbauditlogs**. Voor meer informatie over de hiërarchie van de opslag mappen, naam conventies en logboek indeling raadpleegt u de [SQL database controle logboek indeling](./audit-log-format.md).

- Gebruik [Azure Portal](https://portal.azure.com).  Open de relevante database. Klik boven aan de **controle** pagina van de Data Base op **audit logboeken weer geven**.

    ![Scherm opname van de knop controle logboeken weer geven die is gemarkeerd op de pagina voor het controleren van de data base.](./media/auditing-overview/7_auditing_get_started_blob_view_audit_logs.png)

    **Controle records** worden geopend, waaruit u de logboeken kunt weer geven.

  - U kunt specifieke datums weer geven door boven aan de pagina **controle records** op **filter** te klikken.
  - U kunt scha kelen tussen controle records die zijn gemaakt door het *Server controlebeleid* en het *database controlebeleid* door te scha kelen op **controle bron**.
  - U kunt alleen controle records met betrekking tot SQL-injectie weer geven door  **alleen controle records voor SQL-injecties weer geven** in te scha kelen.

       ![Scherm afbeelding met de opties voor het weer geven van de controle records.]( ./media/auditing-overview/8_auditing_get_started_blob_audit_records.png)

- Gebruik de systeem functie **sys.fn_get_audit_file** (T-SQL) om de controle logboek gegevens in tabel vorm te retour neren. Zie [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)voor meer informatie over het gebruik van deze functie.

- **Samenvoeg controle bestanden** in SQL Server Management Studio gebruiken (vanaf SSMS 17):
    1. Selecteer in het menu SSMS **File**  >  **Open**  >  **Merge audit files**.

        ![Scherm afbeelding met de menu optie controle bestanden samen voegen.](./media/auditing-overview/9_auditing_get_started_ssms_1.png)
    2. Het dialoog venster **controle bestanden toevoegen** wordt geopend. Selecteer een van de opties voor **toevoegen** om te kiezen of u de audit bestanden van een lokale schijf wilt samen voegen of uit Azure Storage wilt importeren. U moet uw Azure Storage Details en de account sleutel opgeven.

    3. Nadat alle bestanden die u wilt samen voegen, zijn toegevoegd, klikt u op **OK** om de samenvoeg bewerking te volt ooien.

    4. Het samengevoegde bestand wordt geopend in SSMS, waar u het kunt weer geven en analyseren, en het exporteren naar een XEL-of CSV-bestand of naar een tabel.

- Gebruik Power BI. U kunt audit logboek gegevens in Power BI weer geven en analyseren. Zie [audit logboek gegevens analyseren in Power bi](https://techcommunity.microsoft.com/t5/azure-database-support-blog/sql-azure-blob-auditing-basic-power-bi-dashboard/ba-p/368895)voor meer informatie en om toegang te krijgen tot een sjabloon die u kunt downloaden.
- Down load de logboek bestanden van uw Azure Storage BLOB-container via de portal of met behulp van een hulp programma zoals [Azure Storage Explorer](https://storageexplorer.com/).
  - Nadat u een logboek bestand lokaal hebt gedownload, dubbelklikt u op het bestand om de logboeken in SSMS te openen, weer te geven en te analyseren.
  - U kunt ook meerdere bestanden tegelijk downloaden via Azure Storage Explorer. Als u dit wilt doen, klikt u met de rechter muisknop op een specifieke submap en selecteert u **Opslaan als** om op te slaan in een lokale map.

- Aanvullende methoden:

  - Nadat u meerdere bestanden of een submap met logboek bestanden hebt gedownload, kunt u deze lokaal samen voegen zoals beschreven in de instructies voor het samen voegen van de opdracht SSMS merge.
  - Controle logboeken voor blobs weer geven programmatisch: [Query's uitvoeren op bestanden met uitgebreide gebeurtenissen](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) met behulp van Power shell.

## <a name="production-practices"></a><a id="production-practices"></a>Productie-procedures


### <a name="auditing-geo-replicated-databases"></a>Geo-gerepliceerde data bases controleren

Bij geo-gerepliceerde data bases, wanneer u controle inschakelt voor de hoofd database, heeft de secundaire Data Base een identiek controle beleid. Het is ook mogelijk om controle in te stellen voor de secundaire data base door controle in te scha kelen op de **secundaire server**, onafhankelijk van de primaire data base.

- Server niveau (**Aanbevolen**): Schakel de controle op zowel de **primaire server** als de **secundaire server** in. de primaire en secundaire data bases worden elk onafhankelijk gecontroleerd op basis van hun respectieve beleid op server niveau.
- Database niveau: controle op database niveau voor secundaire data bases kan alleen worden geconfigureerd vanuit de instellingen voor de controle van primaire data bases.
  - De controle moet worden ingeschakeld op de *primaire data base zelf*, niet op de server.
  - Nadat de controle is ingeschakeld op de primaire data base, wordt deze ook ingeschakeld op de secundaire data base.

    > [!IMPORTANT]
    > Bij het controleren op database niveau zijn de opslag instellingen voor de secundaire data base gelijk aan die van de primaire data base, waardoor Kruis regionale verkeer wordt veroorzaakt. U wordt aangeraden alleen controle op server niveau in te scha kelen en de controle op database niveau uitgeschakeld te laten voor alle data bases.

### <a name="storage-key-regeneration"></a>Opnieuw genereren van de opslag sleutel

In productie zult u uw opslag sleutels waarschijnlijk periodiek vernieuwen. Wanneer u audit logboeken naar Azure Storage schrijft, moet u uw controle beleid opnieuw opslaan bij het vernieuwen van uw sleutels. Het proces verloopt als volgt:

1. **Opslag Details** openen. Selecteer in het vak **toegangs sleutel voor opslag** de optie **secundair** en klik op **OK**. Klik vervolgens boven aan de pagina controle configuratie op **Opslaan** .

    ![Scherm opname van het proces voor het selecteren van een secundaire toegangs sleutel voor opslag.](./media/auditing-overview/5_auditing_get_started_storage_key_regeneration.png)
2. Ga naar de pagina opslag configuratie en Genereer de primaire toegangs sleutel opnieuw.

    ![Navigatievenster](./media/auditing-overview/6_auditing_get_started_regenerate_key.png)
3. Ga terug naar de pagina controle configuratie, schakel de toegangs sleutel voor opslag van secundair naar primair in en klik vervolgens op **OK**. Klik vervolgens boven aan de pagina controle configuratie op **Opslaan** .
4. Ga terug naar de pagina opslag configuratie en Genereer de secundaire toegangs sleutel opnieuw (in voor bereiding voor de vernieuwings cyclus van de volgende sleutel).

## <a name="manage-azure-sql-database-auditing"></a><a id="manage-auditing"></a>Azure SQL Database controle beheren

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

**Power shell-cmdlets (inclusief WHERE-component ondersteuning voor aanvullende filters)**:

- [Controle beleid voor data bases maken of bijwerken (set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Controle beleid voor servers maken of bijwerken (set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Database controlebeleid ophalen (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Server controle beleid ophalen (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Database controlebeleid verwijderen (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Server controle beleid verwijderen (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Zie [controle en detectie van bedreigingen configureren met Power shell](scripts/auditing-threat-detection-powershell-configure.md)voor een voor beeld van een script.

### <a name="using-rest-api"></a>REST API gebruiken

**rest API**:

- [Controle beleid voor data base maken of bijwerken](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Controle beleid voor servers maken of bijwerken](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Controle beleid voor data base ophalen](/rest/api/sql/database%20auditing%20settings/get)
- [Controle beleid voor server ophalen](/rest/api/sql/server%20auditing%20settings/get)

Uitgebreid beleid met de component WHERE ondersteuning voor extra filtering:

- [Beleid voor *uitgebreide* controle van data base maken of bijwerken](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [*Uitgebreid* controle beleid voor servers maken of bijwerken](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [*Uitgebreide* controle beleid voor data base ophalen](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [*Uitgebreid* controle beleid voor server ophalen](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-cli"></a>Azure CLI gebruiken

- [Het controle beleid van een server beheren](/cli/azure/sql/server/audit-policy?view=azure-cli-latest)
- [Het controle beleid van een Data Base beheren](/cli/azure/sql/db/audit-policy?view=azure-cli-latest)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen gebruiken

U kunt Azure SQL Database controle beheren met behulp van [Azure Resource Manager](../../azure-resource-manager/management/overview.md) -sjablonen, zoals in deze voor beelden wordt weer gegeven:

- [Een Azure SQL Database implementeren met controle ingeschakeld om audit logboeken naar een Azure Blob Storage-account te schrijven](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Een Azure SQL Database implementeren met controle ingeschakeld om audit logboeken te schrijven naar Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Een Azure SQL Database implementeren met controle ingeschakeld om audit logboeken te schrijven naar Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> De gekoppelde voor beelden bevinden zich in een externe open bare opslag plaats en worden als ' as is ' gegeven, zonder enige garantie, en worden niet ondersteund onder een ondersteunings programma/service van micro soft.
