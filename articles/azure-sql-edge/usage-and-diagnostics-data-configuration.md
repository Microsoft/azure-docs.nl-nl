---
title: Gegevens configuratie voor het gebruik van Azure SQL Edge en diagnose
description: Meer informatie over het configureren van gebruiks-en diagnostische gegevens in Azure SQL Edge.
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 08/04/2020
ms.openlocfilehash: 81b2aabbae148faec06c9ab420bdfecde475b4bb
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97604989"
---
# <a name="azure-sql-edge-usage-and-diagnostics-data-configuration"></a>Gegevens configuratie voor het gebruik van Azure SQL Edge en diagnose

Standaard verzamelt Azure SQL Edge informatie over hoe klanten de toepassing gebruiken. Met name Azure SQL Edge verzamelt informatie over de implementatie-ervaring, het gebruik en de prestaties. Deze informatie helpt micro soft bij het verbeteren van het product om beter te voldoen aan de behoeften van de klant. Micro soft verzamelt bijvoorbeeld informatie over welke soorten fout codes klanten tegen komen, zodat wij gerelateerde bugs kunnen oplossen, onze documentatie voor het gebruik van Azure SQL Edge kan verbeteren en bepalen of er functies moeten worden toegevoegd aan het product om klanten beter te kunnen bezorgen.

Micro soft stuurt met name geen van de volgende soorten informatie via dit mechanisme:

- Alle waarden uit de gebruikers tabellen.
- Eventuele aanmeldings referenties of andere verificatie-informatie.
- Persoonlijke of klant gegevens.

Het volgende voorbeeld scenario bevat informatie over het gebruik van functies waarmee het product kan worden verbeterd.

Hieronder vindt u een voorbeeld query van de query's die worden gebruikt voor het verzamelen van gegevens over gebruik en diagnose. De query identificeert het aantal en de typen van de verschillende streaming-gegevens bronnen die worden gebruikt in de Azure SQL-rand. Deze gegevens helpen micro soft te identificeren welke streaming-gegevens bronnen meestal worden gebruikt, zodat micro soft de prestaties en gebruikers ervaring kan verbeteren die aan deze gegevens bronnen is gekoppeld. 

```sql
select 
count(*) as [count], sum(inputs) as inputs, sum(outputs) as outputs, sum(linked_to_job) 
as linked_to_job, data_source_type
from ( 
select isnull(value,'unknown') as data_source_type, inputs, outputs, linked_to_job
from 
    ( 
        select 
        convert(sysname, lower(substring(ds.location, 0, charindex('://', ds.location))), 1) as data_source_type, 
        isnull(inputs, 0) as inputs, isnull(outputs, 0) as outputs, isnull(js.stream_id/js.stream_id, 0) as linked_to_job 
        from sys.external_streams es
        join sys.external_data_sources ds 
             on es.data_source_id = ds.data_source_id
        left join 
            ( 
            select stream_id, max(cast(is_input as int)) inputs, max(cast(is_output as int)) outputs 
            from sys.external_job_streams group by stream_id 
            ) js
             on js.stream_id = es.object_id 
    ) ds
left join 
    (
        select value from string_split('edgehub,sqlserver,kafka', ',')) as known_ep on data_source_type = value 
    ) known_ds
group by data_source_type
```

## <a name="disable-usage-and-diagnostic-data-collection"></a>Het verzamelen van gebruiks-en diagnostische gegevens uitschakelen

Het verzamelen van gebruiks-en diagnostische gegevens op Azure SQL Edge kan worden uitgeschakeld met behulp van een van de onderstaande methoden.

> [!NOTE]
> Het gebruik en de diagnostische gegevens kunnen niet worden uitgeschakeld voor de versie van de ontwikkelaar.

### <a name="disable-usage-and-diagnostics-using-environment-variables"></a>Gebruik en diagnostische gegevens uitschakelen met omgevings variabelen

Als u het verzamelen van gebruiks-en diagnostische gegevens wilt uitschakelen op Azure SQL Edge, voegt u de volgende omgevings variabele toe en stelt u de waarde ervan in op `*False*` . Voor meer informatie over het configureren van Azure SQL Edge met behulp van omgevings variabelen raadpleegt u [configureren met omgevings variabelen](configure.md#configure-by-using-environment-variables).

`MSSQL_TELEMETRY_ENABLED = TRUE | FALSE`

- TRUE: Hiermee kunt u het gebruik en de diagnostische gegevens verzamelen. Dit is de standaard configuratie.
- FALSE: Hiermee wordt het verzamelen van gebruiks-en diagnostische gegevens uitgeschakeld.

### <a name="disable-usage-and-diagnostics-using-mssqlconf-file"></a>Gebruik en diagnostische gegevens uitschakelen met het bestand MSSQL. conf

Als u het verzamelen van gebruiks-en diagnostische gegevens op de Azure SQL-rand wilt uitschakelen, voegt u de volgende regels toe aan het bestand MSSQL. conf op het permanente opslag station dat is toegewezen aan de map/var/opt/MSSQL/in de SQL-Edge-module. Voor meer informatie over het configureren van Azure SQL Edge met behulp van het bestand MSSQL. conf raadpleegt u [configure using MSSQL. conf file](configure.md#configure-by-using-an-mssqlconf-file).

```ini
[telemetry]
customerfeedback = false
```

## <a name="local-audit-of-usage-and-diagnostic-data-collection"></a>Lokale controle van het verzamelen van gebruiks-en diagnostische gegevens

Het lokale controle onderdeel van het verzamelen van Azure SQL Edge-en diagnostische gegevens kan gegevens die door de service zijn verzameld, naar een aangewezen map schrijven, die de gegevens (Logboeken) vertegenwoordigen die naar micro soft worden verzonden. Het doel van de lokale controle is om te voor komen dat klanten alle gegevens kunnen zien die micro soft verzamelt met deze functie, om redenen voor naleving, regelgevende of privacy-validatie.

### <a name="enable-local-audit-of-usage-and-diagnostics-data"></a>Lokale controle van gebruiks-en diagnostische gegevens inschakelen

Lokale controle-en diagnostische gegevens voor Azure SQL Edge inschakelen

1. Maak een doelmap voor een nieuwe lokale audit logboek opslag. Deze doelmap kan zich op de host of in de container bevinden. In het onderstaande voor beeld wordt de doelmap gemaakt in hetzelfde koppel volume dat is toegewezen aan het/var/opt/MSSQL/-pad op de SQL-rand.

   ```bash
   sudo mkdir <host mount path>/audit
   ```

2. Configureer de controle van gebruiks-en diagnostische gegevens met behulp van omgevings variabelen of het bestand MSSQL. conf.

   - Omgevings variabelen gebruiken: Voeg de volgende omgevings variabele toe aan de implementatie van SQL Edge en geef de doelmap voor de audit bestanden op.
   
     `*MSSQL_TELEMETRY_DIR = <host mount path>/audit*`
   
   - Het bestand MSSQL. conf gebruiken: Voeg de volgende regels toe aan het bestand MSSQL. conf en geef de doelmap voor de audit bestanden op.
       ```ini
       [telemetry]
       userrequestedlocalauditdirectory  = <host mount path>/audit
       ```  

## <a name="next-steps"></a>Volgende stappen

- [Verbinding maken met Azure SQL Edge](connect.md)
- [Een end-to-end IoT-oplossing bouwen met SQL Edge](tutorial-deploy-azure-resources.md)
