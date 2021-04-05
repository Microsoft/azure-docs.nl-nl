---
title: Bulksgewijs kopiëren uit een Data Base met behulp van de controle tabel
description: Meer informatie over het gebruik van een oplossings sjabloon voor het kopiëren van bulk gegevens uit een Data Base met behulp van een externe beheer tabel om een partitie lijst met bron tabellen op te slaan met behulp van Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: eed7a304bdd57846cd038cc9bf9a67e8150ca505
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100392456"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Bulksgewijs kopiëren van een Data Base met een controle tabel

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Als u gegevens wilt kopiëren van een Data Warehouse in Oracle Server, Netezza, Teradata of SQL Server naar Azure Synapse Analytics, moet u grote hoeveel heden gegevens uit meerdere tabellen laden. Normaal gesp roken moeten de gegevens in elke tabel worden gepartitioneerd, zodat u rijen met meerdere threads parallel vanuit één tabel kunt laden. In dit artikel wordt een sjabloon beschreven voor gebruik in deze scenario's.

 >! Opmerking Als u gegevens wilt kopiëren van een klein aantal tabellen met relatief klein gegevens volume naar Azure Synapse Analytics, is het efficiënter om de [Azure Data Factory gegevens kopiëren tool](copy-data-tool.md)te gebruiken. De sjabloon die in dit artikel wordt beschreven, is meer dan u nodig hebt voor dat scenario.

## <a name="about-this-solution-template"></a>Over deze oplossings sjabloon

Met deze sjabloon wordt een lijst opgehaald met de bron database partities die moeten worden gekopieerd uit een externe beheer tabel. Vervolgens wordt elke partitie in de bron database herhaald en worden de gegevens naar het doel gekopieerd.

De sjabloon bevat drie activiteiten:
- Met **zoeken** wordt de lijst met verzekerde database partities opgehaald uit een tabel voor externe controle.
- **Foreach** haalt de partitie lijst op uit de opzoek activiteit en herhaalt elke partitie met de Kopieer activiteit.
- **Copy** kopieert elke partitie uit het archief van de bron database naar het doel archief.

De sjabloon definieert de volgende para meters:
- *Control_Table_Name* is uw externe controle tabel, waarin de partitie lijst voor de bron database wordt opgeslagen.
- *Control_Table_Schema_PartitionID* is de naam van de kolom naam in de tabel met externe controle waarin elke partitie-id wordt opgeslagen. Zorg ervoor dat de partitie-ID uniek is voor elke partitie in de bron database.
- *Control_Table_Schema_SourceTableName* is uw externe beheer tabel waarin elke tabel naam wordt opgeslagen vanuit de bron database.
- *Control_Table_Schema_FilterQuery* is de naam van de kolom in de tabel met externe controle waarin de filter query wordt opgeslagen om de gegevens op te halen uit elke partitie in de bron database. Als u bijvoorbeeld de gegevens per jaar hebt gepartitioneerd, kan de query die in elke rij is opgeslagen, vergelijkbaar zijn met ' Select * from data source where LastModifytime >= ' ' 2015-01-01 00:00:00 ' ' en LastModifytime <= ' ' 2015-12-31 23:59:59.999 ' '.
- *Data_Destination_Folder_Path* is het pad naar de locatie waar de gegevens naar uw doel archief worden gekopieerd (van toepassing als de bestemming die u kiest, is "File System" of "Azure data Lake Storage gen1"). 
- *Data_Destination_Container* is het pad naar de hoofdmap waarnaar de gegevens worden gekopieerd in uw doel archief. 
- *Data_Destination_Directory* het mappad is in de hoofdmap waar de gegevens naar het doel archief worden gekopieerd. 

De laatste drie para meters, waarmee het pad in uw doel archief wordt gedefinieerd, zijn alleen zichtbaar als de bestemming die u kiest, opslag ruimte op basis van bestanden is. Als u ' Azure Synapse Analytics ' als doel archief kiest, zijn deze para meters niet vereist. Maar de tabel namen en het schema in azure Synapse Analytics moeten gelijk zijn aan die in de bron database.

## <a name="how-to-use-this-solution-template"></a>Deze oplossings sjabloon gebruiken

1. Maak een controle tabel in SQL Server of Azure SQL Database om de partitie lijst van de bron database op te slaan voor bulk kopieën. In het volgende voor beeld zijn er vijf partities in de bron database. Drie partities zijn voor de *datasource_table* en twee zijn voor de *project_table*. De kolom *LastModifytime* wordt gebruikt voor het partitioneren van de gegevens in tabel *datasource_table* van de bron database. De query die wordt gebruikt voor het lezen van de eerste partitie is ' Select * from datasource_table where LastModifytime >= ' ' 2015-01-01 00:00:00 ' ' en LastModifytime <= ' ' 2015-12-31 23:59:59.999 ' '. U kunt een vergelijk bare query gebruiken om gegevens uit andere partities te lezen.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Ga naar de sjabloon **bulksgewijs kopiëren vanuit de data base** . Maak een **nieuwe** verbinding naar de externe controle tabel die u in stap 1 hebt gemaakt.

    ![Een nieuwe verbinding maken met de controle tabel](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Maak een **nieuwe** verbinding met de bron database waaruit u gegevens wilt kopiëren.

    ![Een nieuwe verbinding maken met de bron database](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Maak een **nieuwe** verbinding met de doel gegevens opslag waarnaar u de gegevens wilt kopiëren.

    ![Een nieuwe verbinding maken met het doel archief](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Selecteer **deze sjabloon gebruiken**.

6. U ziet de pijp lijn, zoals wordt weer gegeven in het volgende voor beeld:

    ![De pijp lijn controleren](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Selecteer **debug**, voer de **para meters** in en selecteer **volt ooien**.

    ![Klik op * * fout opsporing * *](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Er worden resultaten weer gegeven die vergelijkbaar zijn met het volgende voor beeld:

    ![Bekijk het resultaat](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. Beschrijving Als u de gegevens bestemming ' Azure Synapse Analytics ' hebt gekozen, moet u een verbinding met Azure Blob Storage voor fase ring opgeven, zoals vereist door Azure Synapse Analytics poly base. Met de sjabloon wordt automatisch een pad naar een container gegenereerd voor de Blob-opslag. Controleer of de container is gemaakt na de uitvoering van de pijp lijn.
    
    ![Poly base-instelling](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Data Factory](introduction.md)
