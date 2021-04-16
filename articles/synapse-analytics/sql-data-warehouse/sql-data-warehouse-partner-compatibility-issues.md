---
title: Compatibiliteitsproblemen met toepassingen en Azure Synapse Analytics
description: Beschrijft bekende problemen die toepassingen van derden kunnen vinden met Azure Synapse
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: sql
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: 3bad9d6464b41ff1b7ad03147d3a48c50c787cb0
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568111"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>Compatibiliteitsproblemen met toepassingen en Azure Synapse Analytics

Toepassingen die zijn gebouwd SQL Server werken naadloos met Azure Synapse sql-pools. In sommige gevallen zijn functies en taalelementen die vaak worden gebruikt in SQL Server mogelijk niet beschikbaar in Azure Synapse of gedragen ze zich anders.

In dit artikel vindt u bekende problemen die kunnen voorkomen bij het gebruik van toepassingen van derden met Azure Synapse Analytics. 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Tableau-fout: 'Een poging om een transactie te voltooien is mislukt. Geen bijbehorende transactie gevonden'

Vanaf Azure Synapse sql-poolversie 10.0.11038.0 kunnen sommige Tableau-query's die opgeslagen procedureoproepen doen, mislukken met het volgende foutbericht:**[Microsoft][ODBC-stuurprogramma 17 voor SQL Server][SQL Server]111214; Een poging om een transactie te voltooien is mislukt. Er is geen bijbehorende transactie gevonden.**"

### <a name="cause"></a>Oorzaak

Dit is een probleem in Azure Synapse sql-pool die wordt veroorzaakt door de introductie van nieuwe opgeslagen systeemprocedures die automatisch worden aangeroepen door de ODBC- en JDBC-stuurprogramma's. Een van deze in het systeem opgeslagen procedures kan ertoe leiden dat openstaande transacties worden afgebroken als de uitvoering mislukt. Dit probleem kan zich voor doen, afhankelijk van de logica van de clienttoepassing.

### <a name="solution"></a>Oplossing
Klanten die dit specifieke probleem zien bij het gebruik van Tableau dat is verbonden met Azure Synapse SQL-pools, moeten FMTONLY instellen op JA in de SQL-verbinding. Voor Tableau Desktop en Tableau Server moet u een TDC-bestand (Tableau Data Source Customization) gebruiken om ervoor te zorgen dat Tableau deze parameter doorgeeft aan het stuurprogramma.  

> [!NOTE] 
> Microsoft biedt geen ondersteuning voor hulpprogramma's van derden. Hoewel we hebben getest dat deze oplossing werkt met Tableau Desktop 2020.3.2, moet u deze tijdelijke oplossing gebruiken voor uw eigen capaciteit.
>

* [Raadpleeg de tableau Desktop-documentatie voor meer informatie over het maken van algemene aanpassingen met een TDC-bestand in Tableau Desktop.](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [Raadpleeg Een gebruiken voor meer informatie over het maken van globale aanpassingen met een TDC-bestand op Tableau Server. TDC-bestand met Tableau Server.](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

In het onderstaande voorbeeld ziet u een Tableau TDC-bestand dat de parameter FMTONLY=YES doorgeeft aan de SQL-connection string:

```json
<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
Neem contact op met tableau-ondersteuning voor meer informatie over het gebruik van TDC-bestanden. 

## <a name="see-also"></a>Zie ook

* [T-SQL-taalelementen voor toegewezen SQL-pool in Azure Synapse Analytics.](./sql-data-warehouse-reference-tsql-language-elements.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
* [T-SQL-instructies die worden ondersteund voor toegewezen SQL-pool in Azure Synapse Analytics.](./sql-data-warehouse-reference-tsql-statements.md)