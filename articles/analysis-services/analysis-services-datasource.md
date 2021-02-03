---
title: Gegevens bronnen die worden ondersteund in Azure Analysis Services | Microsoft Docs
description: Hierin worden gegevens bronnen en connectors beschreven die worden ondersteund voor tabellaire 1200 en hogere gegevens modellen in Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6e558962ad8a84b5f44abe21bc7c0ab67a4861ba
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493817"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Ondersteunde gegevensbronnen in Azure Analysis Services

Gegevens bronnen en connectors die worden weer gegeven in de wizard gegevens ophalen of tabel importeren in Visual Studio met Analysis Services projecten worden weer gegeven voor zowel Azure Analysis Services als SQL Server Analysis Services. Niet alle weer gegeven gegevens bronnen en connectors worden echter ondersteund in Azure Analysis Services. De typen gegevens bronnen waarmee u verbinding kunt maken, zijn afhankelijk van veel factoren, zoals model compatibiliteits niveau, beschik bare gegevens connectors, verificatie type en on-premises gegevens gateway ondersteuning. In de volgende tabellen worden de ondersteunde gegevens bronnen voor Azure Analysis Services beschreven.

## <a name="azure-data-sources"></a>Azure-gegevensbronnen

|Gegevensbron  |In het geheugen  |DirectQuery  |Opmerkingen |
|---------|---------|---------|---------|
|Azure SQL Database      |   Ja      |    Ja      |<sup>[2](#azprovider)</sup>, <sup> [3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL DW)      |   Ja      |   Ja       |<sup>[twee](#azprovider)</sup>|
|Azure Blob Storage      |   Ja       |    Nee      | <sup>[i](#tab1400a)</sup> |
|Azure Table Storage     |   Ja       |    Nee      | <sup>[i](#tab1400a)</sup>|
|Azure Cosmos DB     |  Ja        |  Nee        |<sup>[i](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   Ja       |    Nee      |<sup>[i](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   Ja       |    Nee      |<sup>[1](#tab1400a)</sup>, <sup> [5](#gen2)</sup>|
|Azure HDInsight HDFS    |     Ja     |   Nee       |<sup>[i](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Ja       |   Nee       |<sup>[1](#tab1400a)</sup>, <sup> [4](#databricks)</sup>|
||||

**Opmerkingen:**

<a name="tab1400a">1</a> : alleen modellen in tabel vorm 1400 en hoger.  
<a name="azprovider">2</a> -als de gegevens bron van een *provider* in tabellaire 1200 en hoger modellen is opgegeven, is voor zowel in-Memory als DirectQuery-modellen micro soft OLE DB-stuur programma vereist voor SQL Server MSOLEDBSQL (aanbevolen), SQL Server Native Client 11,0 of .NET Framework gegevens provider voor SQL Server.  
<a name="azsqlmanaged">3</a> -Azure SQL Managed instance wordt ondersteund. Omdat het beheerde exemplaar van SQL wordt uitgevoerd in azure VNet met een privé-IP-adres, moet het open bare eind punt zijn ingeschakeld voor het exemplaar. Als deze niet is ingeschakeld, is een [on-premises gegevens gateway](analysis-services-gateway.md) vereist.  
<a name="databricks">4</a> -Azure Databricks het gebruik van de Spark-connector wordt momenteel niet ondersteund.  
<a name="gen2">5</a> -ADLS Gen2 connector wordt momenteel niet ondersteund, maar de Azure Blob Storage-connector kan worden gebruikt met een ADLS Gen2-gegevens bron.

## <a name="other-data-sources"></a>Andere gegevensbronnen

|Gegevensbron | In het geheugen | DirectQuery |Notities   |
|  --- | --- | --- | --- |
|Access-database     |  Ja | Nee |  |
|Active Directory     |  Ja | Nee | <sup>[6,5](#tab1400b)</sup>  |
|Analysis Services     |  Ja | Nee |  |
|Analytics platform systeem     |  Ja | Nee |  |
|CSV-bestand  |Ja | Nee |  |
|Dynamics 365     |  Ja | Nee | <sup>[6,5](#tab1400b)</sup> |
|Excel-werkmap     |  Ja | Nee |  |
|Exchange      |  Ja | Nee | <sup>[6,5](#tab1400b)</sup> |
|Map      |Ja | Nee | <sup>[6,5](#tab1400b)</sup> |
|IBM Informix  |Ja | Nee |  |
|JSON-document      |  Ja | Nee | <sup>[6,5](#tab1400b)</sup> |
|Regels van binair bestand      | Ja | Nee | <sup>[6,5](#tab1400b)</sup> |
|MySQL-database     | Ja | Nee |  |
|OData-feed      |  Ja | Nee | <sup>[6,5](#tab1400b)</sup> |
|ODBC-query     | Ja | Nee |  |
|OLE DB     |   Ja | Nee |  |
|Oracle  | Ja  |Ja  | <sup>[9,4](#oracle)</sup> |
|PostgreSQL-database   | Ja | Nee | <sup>[6,5](#tab1400b)</sup> |
|Salesforce-objecten|  Ja | Nee | <sup>[6,5](#tab1400b)</sup> |
|Salesforce-rapporten |Ja | Nee | <sup>[6,5](#tab1400b)</sup> |
|SAP HANA     |  Ja | Nee |  |
|SAP Business Warehouse    |  Ja | Nee | <sup>[6,5](#tab1400b)</sup> |
|SharePoint-lijst      |   Ja | Nee | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|SQL Server |Ja   | Ja  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|SQL Server Data Warehouse |Ja   | Ja  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|Sybase-database     |  Ja | Nee |  |
|Teradata | Ja  | Ja  | <sup>[6](#teradata)</sup> |
|TXT-bestand  |Ja | Nee |  |
|XML-tabel    |  Ja | Nee | <sup>[6,5](#tab1400b)</sup> |
| | | |

**Opmerkingen:**  
<a name="tab1400b">6</a> : alleen in tabel vorm 1400 en hoger.  
<a name="sqlim">7</a> -als de gegevens bron van een *provider* is opgegeven in tabellaire 1200 en hoger, geeft u micro soft OLE DB driver op voor SQL Server MSOLEDBSQL (aanbevolen), SQL Server Native Client 11,0 of .NET Framework gegevens provider voor SQL Server.  
<a name="instgw">8</a> -als MSOLEDBSQL als gegevens provider worden opgegeven, kan het nodig zijn om het [micro soft OLE DB-stuur programma voor SQL Server](/sql/connect/oledb/oledb-driver-for-sql-server) te downloaden en te installeren op dezelfde computer als de on-premises gegevens gateway.  
<a name="oracle">9</a> : Geef de Oracle 1200-gegevens provider voor .net op, of als een *provider* gegevens bron in tabellaire 1400 en-modellen. Zorg ervoor dat u [Oracle Managed Provider inschakelt](#enable-oracle-managed-provider), indien opgegeven als een gestructureerde gegevens bron.   
<a name="teradata">10</a> -voor in tabel 1200-modellen, of als gegevens bron van een *provider* in tabellaire 1400 + modellen, geeft u de Teradata-gegevens provider voor .net op.  
<a name="filesSP">11</a> -bestanden in on-premises share point worden niet ondersteund.

Voor het maken van verbinding met on-premises gegevens bronnen van een Azure Analysis Services-server is een [on-premises gateway](analysis-services-gateway.md)vereist. Wanneer u een gateway gebruikt, zijn 64-bits-providers vereist.

## <a name="understanding-providers"></a>Wat zijn providers?

Bij het maken van in tabel vorm 1400 en een hoger model project in Visual Studio, geeft u standaard geen gegevens provider op wanneer u verbinding maakt met een gegevens bron met behulp van **gegevens ophalen**. In tabel vorm 1400 en hogere modellen wordt gebruikgemaakt van [Power query](/power-query/power-query-what-is-power-query) connectors voor het beheren van verbindingen, gegevens query's en mashups tussen de gegevens bron en Analysis Services. Dit worden ook wel *gestructureerde* gegevens bron verbindingen genoemd in die instellingen voor de verbindings eigenschappen voor u zijn ingesteld. U kunt echter verouderde gegevens bronnen inschakelen voor een model project in Visual Studio. Als u deze functie inschakelt, kunt u de **wizard tabel importeren** gebruiken om verbinding te maken met bepaalde gegevens bronnen die traditioneel worden ondersteund in tabel 1200 en lagere modellen als *verouderde* of gegevens bronnen van *providers* . Wanneer u de gegevens bron van een provider opgeeft, kunt u een bepaalde gegevens provider en andere geavanceerde verbindings eigenschappen opgeven. U kunt bijvoorbeeld verbinding maken met een SQL Server Data Warehouse-instantie of zelfs een Azure SQL Database als een verouderde gegevens bron. U kunt vervolgens het OLE DB stuur programma voor SQL Server MSOLEDBSQL-gegevens provider selecteren. In dit geval kan het selecteren van een OLE DB gegevens provider betere prestaties bieden ten opzichte van de Power Query-connector. 

Wanneer u de wizard tabel importeren gebruikt in Visual Studio, is voor verbindingen met een gegevens bron een gegevens provider vereist. Er wordt een standaard gegevens provider voor u geselecteerd. U kunt de gegevens provider zo nodig wijzigen. Het type provider dat u kiest, kan afhankelijk zijn van de prestaties, ongeacht of het model gebruikmaakt van in-Memory opslag of DirectQuery, en op welke Analysis Services platform u uw model implementeert.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Gegevens bronnen van providers opgeven in tabellaire 1400 en hoger model projecten

Als u gegevens bronnen van providers wilt inschakelen, klikt u in Visual Studio op **extra**  >  **Opties**  >  **Analysis Services tabellaire**  >  **gegevens importeren**, selecteert u **verouderde gegevens bronnen inschakelen**.

![Verouderde gegevens bronnen inschakelen](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Als verouderde gegevens bronnen zijn ingeschakeld, klikt u in **tabellaire model Verkenner** met de rechter muisknop op **gegevens bronnen**  >  **importeren uit gegevens bron (verouderd)**.

![Verouderde gegevens bronnen in Tabellaire model Verkenner](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Net als bij tabellaire 1200 model projecten gebruikt u de **wizard tabel importeren** om verbinding te maken met een gegevens bron. Klik op de pagina verbinding maken op **Geavanceerd**. Geef de gegevens provider en andere Verbindings instellingen op in **Geavanceerde eigenschappen instellen**.

![Geavanceerde eigenschappen van verouderde gegevens bronnen](media/analysis-services-datasource/aas-import-legacy-advanced.png)

## <a name="impersonation"></a>Imitatie
In sommige gevallen kan het nodig zijn om een ander imitatie account op te geven. Het imitatie account kan worden opgegeven in Visual Studio of SQL Server Management Studio (SSMS).

Voor on-premises gegevens bronnen:

* Als u SQL-verificatie gebruikt, moet imitatie een service account zijn.
* Als u Windows-verificatie gebruikt, stelt u Windows-gebruiker/-wacht woord in. Voor SQL Server wordt Windows-verificatie met een specifiek imitatie account alleen ondersteund voor gegevens modellen in het geheugen.

Voor gegevens bronnen in de Cloud:

* Als u SQL-verificatie gebruikt, moet imitatie een service account zijn.

## <a name="service-principal-authentication"></a>Service-Principal-verificatie

Wanneer de gegevens bron van een *provider* wordt opgegeven, wordt Azure Analysis Services ondersteuning biedt voor [MSOLEDBSQL](/sql/connect/oledb/release-notes-for-oledb-driver-for-sql-server) Azure Active Directory service-principal-verificatie voor Azure SQL database-en Azure Synapse-gegevens bronnen.

`
Provider=MSOLEDBSQL;Data Source=[server];Initial Catalog=[database];Authentication=ActiveDirectoryServicePrincipal;User ID=[Application (client) ID];Password=[Application (client) secret];Use Encryption for Data=true
`

## <a name="oauth-credentials"></a>OAuth-referenties

Voor modellen in tabel vorm met het compatibiliteits niveau 1400 en hoger met behulp *van de in-Memory* modus, Azure SQL database, Azure Synapse, Dynamics 365 en share Point List ondersteunen OAuth-referenties. Als u geldige tokens wilt genereren, stelt u referenties in met behulp van Power Query. Azure Analysis Services beheert het vernieuwen van tokens voor OAuth-gegevens bronnen om time-outs voor langdurige vernieuwings bewerkingen te voor komen. 
> [!NOTE]
> Het vernieuwen van beheerde tokens wordt niet ondersteund voor gegevens bronnen die toegankelijk zijn via een gateway. Een of meer mashup-query gegevens bronnen zijn bijvoorbeeld toegankelijk via een gateway en/of de eigenschap [ASPaaS\AlwaysUseGateway](analysis-services-vnet-gateway.md) is ingesteld op **True**. 

De direct query-modus wordt niet ondersteund met OAuth-referenties.

## <a name="enable-oracle-managed-provider"></a>Oracle Managed Provider inschakelen

In sommige gevallen kunnen DAX-query's naar een Oracle-gegevens bron onverwachte resultaten retour neren. Dit kan worden veroorzaakt door de provider die wordt gebruikt voor de verbinding met de gegevens bron.

Zoals beschreven in de sectie [providers](#understanding-providers) , maken tabellaire modellen verbinding met gegevens bronnen als een *gestructureerde* gegevens bron of een gegevens bron van een *provider* . Voor modellen met een Oracle-gegevens bron die is opgegeven als provider gegevens bron, moet u ervoor zorgen dat de opgegeven provider Oracle data provider voor .NET (Oracle. DataAccess. client) is. 

Als de Oracle-gegevens bron is opgegeven als een gestructureerde gegevens bron, schakelt u de **MDataEngine\UseManagedOracleProvider** -Server eigenschap in. Als u deze eigenschap instelt, zorgt u ervoor dat uw model verbinding maakt met de Oracle-gegevens bron met behulp van de aanbevolen Oracle-gegevens provider voor .NET Managed Provider.
 
Oracle Managed Provider inschakelen:

1. Maak in SQL Server Management Studio verbinding met uw server.
2. Maak een XMLA-query met het volgende script. Vervang **servername** door de volledige server naam en voer de query uit.

    ```xml
    <Alter AllowCreate="true" ObjectExpansion="ObjectProperties" xmlns="http://schemas.microsoft.com/analysisservices/2003/engine">
        <Object />
        <ObjectDefinition>
            <Server xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ddl2="http://schemas.microsoft.com/analysisservices/2003/engine/2" xmlns:ddl2_2="http://schemas.microsoft.com/analysisservices/2003/engine/2/2" 
    xmlns:ddl100_100="http://schemas.microsoft.com/analysisservices/2008/engine/100/100" xmlns:ddl200="http://schemas.microsoft.com/analysisservices/2010/engine/200" xmlns:ddl200_200="http://schemas.microsoft.com/analysisservices/2010/engine/200/200" 
    xmlns:ddl300="http://schemas.microsoft.com/analysisservices/2011/engine/300" xmlns:ddl300_300="http://schemas.microsoft.com/analysisservices/2011/engine/300/300" xmlns:ddl400="http://schemas.microsoft.com/analysisservices/2012/engine/400" 
    xmlns:ddl400_400="http://schemas.microsoft.com/analysisservices/2012/engine/400/400" xmlns:ddl500="http://schemas.microsoft.com/analysisservices/2013/engine/500" xmlns:ddl500_500="http://schemas.microsoft.com/analysisservices/2013/engine/500/500">
                <ID>ServerName</ID>
                <Name>ServerName</Name>
                <ServerProperties>
                    <ServerProperty>
                        <Name>MDataEngine\UseManagedOracleProvider</Name>
                        <Value>1</Value>
                    </ServerProperty>
                </ServerProperties>
            </Server>
        </ObjectDefinition>
    </Alter>
    ```

3. Start de server opnieuw.


## <a name="next-steps"></a>Volgende stappen

* [On-premises gateway](analysis-services-gateway.md)
* [Uw server beheren](analysis-services-manage.md)