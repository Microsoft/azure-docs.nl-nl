---
title: Compatibiliteits problemen met toepassingen van derden en Azure Synapse Analytics
description: Hierin worden bekende problemen beschreven die toepassingen van derden kunnen vinden met Azure Synapse
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: ''
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: 27f6f0b1ece7cd1f890d76e912b5e304af46b0cd
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95819267"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>Compatibiliteits problemen met toepassingen van derden en Azure Synapse Analytics

Toepassingen die zijn gebouwd voor SQL Server, werken naadloos met Azure Synapse dedicated SQL-groepen. In sommige gevallen is het echter mogelijk dat functies en taal elementen die meestal in SQL Server worden gebruikt, niet beschikbaar zijn in azure Synapse of dat ze zich anders kunnen gedragen.

In dit artikel vindt u bekende problemen die kunnen optreden bij het gebruik van toepassingen van derden met Azure Synapse Analytics. 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Tableau-fout: ' een poging tot het volt ooien van een trans actie is mislukt. Geen overeenkomende trans actie gevonden "

Vanaf Azure Synapse dedicated SQL pool versie 10.0.11038.0 kunnen sommige tableau-query's die opgeslagen procedure aanroepen maken, mislukken met het volgende fout bericht:**[micro soft] [ODBC-stuur programma 17 voor SQL Server] [SQL Server] 111214; Een poging tot het volt ooien van een trans actie is mislukt. Er is geen overeenkomende trans actie gevonden.**"

### <a name="cause"></a>Oorzaak

Dit is een probleem in de door Azure Synapse toegewezen SQL-pool, veroorzaakt door de introductie van nieuwe opgeslagen systeem procedures die automatisch worden aangeroepen door de ODBC-en JDBC-Stuur Programma's. Een van deze door het systeem opgeslagen procedures kan ertoe leiden dat geopende trans acties worden afgebroken als de uitvoering ervan mislukt. Dit probleem kan zich voordoen afhankelijk van de logica van de client toepassing.

### <a name="solution"></a>Oplossing
Klanten zien dit specifieke probleem bij het gebruik van tableau die zijn verbonden met Azure Synapse dedicated SQL-groepen, moet FMTONLY instellen op Ja in de SQL-verbinding. Voor tableau Desktop en tableau server moet u een TDC-bestand (tableau data source customization) gebruiken om ervoor te zorgen dat tableau deze para meter door gegeven aan het stuur programma.  

> [!NOTE] 
> Micro soft biedt geen ondersteuning voor hulpprogram ma's van derden. Hoewel we hebben getest dat deze oplossing werkt met tableau Desktop 2020.3.2, moet u deze tijdelijke oplossing gebruiken voor uw eigen capaciteit.
>

* [Raadpleeg tableau Desktop Documentation voor meer informatie over het maken van globale aanpassingen met een TDC-bestand op tableau bureau blad.](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [Zie een gebruiken voor meer informatie over het maken van globale aanpassingen met een TDC-bestand op tableau-server. TDC-bestand met tableau-server.](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

In het onderstaande voor beeld ziet u een tableau TDC-bestand waarin de para meter FMTONLY = YES wordt door gegeven aan de SQL connection string:

```json

<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
Neem contact op met de ondersteuning van tableau voor meer informatie over het gebruik van TDC-bestanden. 

## <a name="see-also"></a>Zie ook

* [T-SQL-taal elementen voor een toegewezen SQL-groep in azure Synapse Analytics.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-reference-tsql-language-elements?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [T-SQL-instructies die worden ondersteund voor een toegewezen SQL-groep in azure Synapse Analytics.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-reference-tsql-statements)
