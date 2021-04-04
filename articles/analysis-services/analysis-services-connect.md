---
title: Verbinding maken met Azure Analysis Services servers | Microsoft Docs
description: Meer informatie over hoe u verbinding kunt maken met en gegevens kunt ophalen van een Analysis Services-server in Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 4abe1e9c6f9d7b62792936f816b9c46a937be41a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96499421"
---
# <a name="connecting-to-servers"></a>Verbinding maken met servers

In dit artikel wordt beschreven hoe u verbinding maakt met een server met behulp van gegevens modellering en beheer toepassingen als SQL Server Management Studio (SSMS) of Visual Studio met Analysis Services projecten, of met client Reporting-toepassingen, zoals micro soft Excel, Power BI Desktop of aangepaste toepassingen. Verbindingen met Azure Analysis Services HTTPS gebruiken.

## <a name="client-libraries"></a>Clientbibliotheken

[De nieuwste client bibliotheken ophalen](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)

Alle verbindingen met een server, ongeacht het type, vereisen bijgewerkte AMO-, ADOMD.NET-en OLEDB-client bibliotheken om verbinding te maken met en een interface met een Analysis Services-server. Voor SSMS, Visual Studio, Excel 2016 en hoger, en Power BI, worden de meest recente client bibliotheken geïnstalleerd of bijgewerkt met maandelijkse releases. In sommige gevallen is het echter mogelijk dat een toepassing niet de meest recente heeft. Bijvoorbeeld wanneer beleids regels updates vertragen of Microsoft 365 updates op het uitgestelde kanaal staan.

> [!NOTE]
> De client bibliotheken kunnen geen verbinding maken met Azure Analysis Services via proxy servers waarvoor een gebruikers naam en wacht woord zijn vereist. 

## <a name="server-name"></a>Servernaam

Wanneer u een Analysis Services-server maakt in azure, geeft u een unieke naam en de regio op waar de server moet worden gemaakt. Wanneer u de server naam in een verbinding opgeeft, is het server naamgevings schema:

```
<protocol>://<region>/<servername>
```
 Waarbij het protocol de teken reeks **asazure** is, is de regio de URI waarin de server is gemaakt (bijvoorbeeld westus.asazure.Windows.net) en servername de naam is van uw unieke server in de regio.

### <a name="get-the-server-name"></a>De server naam ophalen

Kopieer de volledige naam van de server in **Azure Portal** > server > **overzicht** van de server  >  **naam**. Als andere gebruikers in uw organisatie ook verbinding maken met deze server, kunt u deze naam met de server delen. Wanneer u een server naam opgeeft, moet het volledige pad worden gebruikt.

![Servernaam bepalen in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> Het protocol voor de regio VS Oost 2 is **aspaaseastus2**.

## <a name="connection-string"></a>Verbindingsreeks

Wanneer u verbinding maakt met Azure Analysis Services met behulp van het object model in tabel vorm, gebruikt u de volgende connection string indelingen:

###### <a name="integrated-azure-active-directory-authentication"></a>Geïntegreerde Azure Active Directory verificatie

Geïntegreerde verificatie haalt de Azure Active Directory referentie cache op, indien beschikbaar. Als dat niet het geval is, wordt het venster Azure-aanmelding weer gegeven.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Verificatie Azure Active Directory met gebruikers naam en wacht woord

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Windows-verificatie (geïntegreerde beveiliging)

Gebruik het Windows-account dat het huidige proces uitvoert.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Verbinding maken via een. ODC-bestand

Met oudere versies van Excel kunnen gebruikers verbinding maken met een Azure Analysis Services server met behulp van een ODC-bestand (Office Data Connection). Zie [een ODC-bestand (Office Data Connection) maken](analysis-services-odc.md)voor meer informatie.

## <a name="connect-as-a-linked-server-from-sql-server"></a>Verbinding maken als een gekoppelde server van SQL Server

SQL Server kunt verbinding maken met een Azure Analysis Services resource als een [gekoppelde server](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine) door MSOLAP op te geven als de gegevens bron provider. Voordat u een gekoppelde server verbinding configureert, moet u ervoor zorgen dat u de meest recente [Msolap-client bibliotheek](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) (provider) installeert. 

Voor gekoppelde server verbindingen met Azure Analysis Services moet de MSOLAP-provider worden geïnstantieerd buiten het SQL Server proces. Zorg ervoor dat bij het configureren van de opties voor de gekoppelde server de optie inschakeling **toestaan** **niet is geselecteerd**.

Als inschakeling **toestaan** is geselecteerd en de provider wordt geïnstantieerd in het SQL Server proces, wordt de volgende fout geretourneerd:

```
OLE DB provider "MSOLAP" for linked server "(null)" returned message "The following system error occurred: ".

OLE DB provider "MSOLAP" for linked server "(null)" returned message "The connection failed because user credentials are needed and Sign-In UI is not allowed.".

Msg 7303, Level 16, State 1, Line 2
Cannot initialize the data source object of OLE DB provider "MSOLAP" for linked server "(null)".
```



## <a name="next-steps"></a>Volgende stappen

[Verbinding maken met Excel](analysis-services-connect-excel.md)    
[Verbinding maken met Power BI](analysis-services-connect-pbi.md)   
[Uw server beheren](analysis-services-manage.md)