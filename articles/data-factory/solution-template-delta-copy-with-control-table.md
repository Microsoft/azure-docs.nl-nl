---
title: Delta kopie van een Data Base met behulp van een controle tabel
description: Leer hoe u een oplossings sjabloon kunt gebruiken om nieuwe of bijgewerkte rijen incrementeel te kopiëren vanuit een Data Base met Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: 3f15ffd6f7ed784cce398d419e013a69c60289d8
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96921805"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Delta kopie van een Data Base met een controle tabel

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt een sjabloon beschreven die beschikbaar is voor het stapsgewijs laden van nieuwe of bijgewerkte rijen van een database tabel naar Azure met behulp van een externe controle tabel waarin een waarde met een bovengrens wordt opgeslagen.

Voor deze sjabloon moet het schema van de bron database een time stamp-kolom of een incrementele sleutel bevatten om nieuwe of bijgewerkte rijen te identificeren.

>[!NOTE]
> Als u een time stamp-kolom in uw bron database hebt om nieuwe of bijgewerkte rijen te identificeren, maar u geen externe beheer tabel wilt maken om te gebruiken voor een Delta kopie, kunt u in plaats daarvan het [hulp programma Azure Data Factory gegevens kopiëren](copy-data-tool.md) gebruiken om een pijp lijn te verkrijgen. Dit hulp programma gebruikt een trigger-geplande tijd als een variabele om nieuwe rijen te lezen uit de bron database.

## <a name="about-this-solution-template"></a>Over deze oplossings sjabloon

Deze sjabloon haalt eerst de oude watermerk waarde op en vergelijkt deze met de huidige watermerk waarde. Daarna kopieert het alleen de wijzigingen uit de bron database, op basis van een vergelijking tussen de twee watermerk waarden. Ten slotte wordt de nieuwe waarde voor het hoge water merk opgeslagen in een externe controle tabel voor de volgende keer dat de Delta gegevens worden geladen.

De sjabloon bevat vier activiteiten:
- Met **lookup** wordt de oude waarde met een bovengrens opgehaald, die wordt opgeslagen in een externe beheer tabel.
- Met een andere **opzoek** activiteit wordt de huidige waarde van het hoogste water merk opgehaald uit de bron database.
- **Copy** kopieert alleen wijzigingen van de bron database naar het doel archief. De query waarmee de wijzigingen in de bron database worden geïdentificeerd, is vergelijkbaar met ' SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > ' laatste bovengrens ' en TIMESTAMP_Column <= ' huidige bovengrens ' '.
- **SqlServerStoredProcedure** schrijft de huidige waarde met hoge water merk naar een externe controle tabel voor de volgende keer dat de Delta kopie wordt gekopieerd.

De sjabloon definieert de volgende para meters:
- *Data_Source_Table_Name* is de tabel in de bron database waaruit u gegevens wilt laden.
- *Data_Source_WaterMarkColumn* is de naam van de kolom in de bron tabel die wordt gebruikt om nieuwe of bijgewerkte rijen te identificeren. Het type van deze kolom is doorgaans *DateTime*, *int* of soortgelijk.
- *Data_Destination_Container* is het hoofdpad van de locatie waarnaar de gegevens worden gekopieerd in uw doel archief.
- *Data_Destination_Directory* het mappad is in de hoofdmap van de locatie waarnaar de gegevens worden gekopieerd in uw doel archief.
- *Data_Destination_Table_Name* is de plaats waar de gegevens naar worden gekopieerd in uw doel archief (van toepassing als ' Azure Synapse Analytics ' is geselecteerd als de bestemming van de gegevens).
- *Data_Destination_Folder_Path* is de plaats waar de gegevens naar worden gekopieerd in uw doel archief (van toepassing als ' bestands systeem ' of ' Azure data Lake Storage gen1 ' is geselecteerd als de bestemming van de gegevens).
- *Control_Table_Table_Name* is de tabel voor externe controle waarin de waarde met een hoog watermerk niveau wordt opgeslagen.
- *Control_Table_Column_Name* is de kolom in de tabel voor externe controle waarin de waarde met een hoog watermerk niveau wordt opgeslagen.

## <a name="how-to-use-this-solution-template"></a>Deze oplossings sjabloon gebruiken

1. Verken de bron tabel die u wilt laden en definieer de kolom met het hoogste water merk die kan worden gebruikt om nieuwe of bijgewerkte rijen te identificeren. Het type van deze kolom kan *DateTime*, *int* of soortgelijk zijn. De waarde van deze kolom neemt toe wanneer er nieuwe rijen worden toegevoegd. In het volgende voor beeld van een bron tabel (data_source_table), kunnen we de kolom *LastModifytime* gebruiken als de kolom met hoge water merken.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. Maak een controle tabel in SQL Server of Azure SQL Database om de bovengrens voor het laden van Delta gegevens op te slaan. In het volgende voor beeld is de naam van de beheer tabel *watermarktable*. In deze tabel is *WatermarkValue* de kolom waarin de waarde met een hoge water merk wordt opgeslagen en het type is *DateTime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Maak een opgeslagen procedure in hetzelfde SQL Server-of Azure SQL Database-exemplaar dat u hebt gebruikt om de controle tabel te maken. De opgeslagen procedure wordt gebruikt om de nieuwe waarde met een hoog water merk te schrijven naar de externe controle tabel voor het laden van de volgende keer dat de Delta gegevens worden geladen.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Ga naar de **Delta kopie van de database** sjabloon. Maak een **nieuwe** verbinding met de bron database van waaruit u gegevens wilt kopiëren.

    ![Een nieuwe verbinding maken met de bron tabel](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Maak een **nieuwe** verbinding met de doel gegevens opslag waarnaar u de gegevens wilt kopiëren.

    ![Een nieuwe verbinding maken met de doel tabel](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Maak een **nieuwe** verbinding met de tabel voor externe controle en opgeslagen procedure die u hebt gemaakt in stap 2 en 3.

    ![Een nieuwe verbinding maken met het gegevens archief van de controle tabel](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Selecteer **deze sjabloon gebruiken**.
    
8. U ziet de beschik bare pijp lijn, zoals wordt weer gegeven in het volgende voor beeld:
  
    ![De pijp lijn controleren](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Selecteer **opgeslagen procedure**. Kies [dbo] voor de naam van de **opgeslagen procedure** **. [ update_watermark]**. Selecteer **para meter importeren** en selecteer vervolgens **dynamische inhoud toevoegen**.  

    ![De opgeslagen procedure-activiteit instellen](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. Schrijf de inhoud **\@ {activity (' LookupCurrentWaterMark '). output. FirstRow. NewWatermarkValue}** en selecteer vervolgens **volt ooien**.  

    ![De inhoud voor de para meters van de opgeslagen procedure schrijven](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. Selecteer **debug**, voer de **para meters** in en selecteer **volt ooien**.

    ![Selecteer * * fout opsporing * *](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Resultaten die vergelijkbaar zijn met het volgende voor beeld worden weer gegeven:

    ![Bekijk het resultaat](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. U kunt nieuwe rijen in de bron tabel maken. Hier volgt een voor beeld van een SQL-taal om nieuwe rijen te maken:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. Als u de pijp lijn opnieuw wilt uitvoeren, selecteert u **fout opsporing**, voert u de **para meters** in en selecteert u vervolgens **volt ooien**.

    U ziet dat alleen nieuwe rijen zijn gekopieerd naar de bestemming.

15. Beschrijving Als u Azure Synapse Analytics als de gegevens bestemming selecteert, moet u ook een verbinding met Azure Blob Storage voor fase ring opgeven, wat vereist is voor Azure Synapse Analytics poly base. Met de sjabloon wordt een pad naar een container gegenereerd. Controleer na de uitvoering van de pijp lijn of de container is gemaakt in Blob Storage.
    
    ![Poly base configureren](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Volgende stappen

- [Bulksgewijs kopiëren van een Data Base met behulp van een controle tabel met Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Bestanden van meerdere containers met Azure Data Factory kopiëren](solution-template-copy-files-multiple-containers.md)
