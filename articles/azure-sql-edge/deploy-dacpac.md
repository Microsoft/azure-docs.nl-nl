---
title: SQL Database DACPAC-en BACPAC-pakketten gebruiken-Azure SQL Edge
description: Meer informatie over het gebruik van dacpacs en bacpacs in Azure SQL Edge
keywords: SQL-Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 40bd0eda16f9f96dd356eef900369ab25854e9f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392245"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>DACPAC-en BACPAC-pakketten in SQL-rand SQL Database

Azure SQL Edge is een geoptimaliseerde relationele database-engine voor IoT- en Edge-implementaties. Het is gebaseerd op de nieuwste versies van de micro soft SQL Database-Engine, waarmee toonaangevende prestaties, beveiliging en verwerkings mogelijkheden voor query's worden geboden. Naast de toonaangevende mogelijkheden voor het beheer van relationele data bases van SQL Server biedt Azure SQL Edge ingebouwde streaming-mogelijkheden voor realtime analyse en complexe gebeurtenis verwerking.

Azure SQL Edge biedt systeem eigen mechanisme waarmee u een [SQL database DACPAC-en BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications) -pakket kunt implementeren tijdens of na de implementatie van SQL Edge.

SQL Database dacpac-en Bacpac-pakketten kunnen worden geïmplementeerd naar SQL Edge met behulp van de `MSSQL_PACKAGE` omgevings variabele. De omgevings variabele kan worden geconfigureerd met een van de volgende.  
- Een locatie van een lokale map binnen de SQL-container met de dacpac-en Bacpac-bestanden. Deze map kan worden toegewezen aan een host volume met behulp van koppel punten of gegevens volume containers. 
- Een lokaal bestandspad binnen de SQL-container toewijzing aan het dacpac-of Bacpac-bestand. Dit bestandspad kan worden toegewezen aan een hostvolume met behulp van koppel punten of gegevens volume containers. 
- Een lokaal bestandspad binnen de SQL-container toewijzing aan een zip-bestand met de dacpac-of Bacpac-bestanden. Dit bestandspad kan worden toegewezen aan een hostvolume met behulp van koppel punten of gegevens volume containers. 
- Een SAS-URL van Azure Blob naar een zip-bestand met de dacpac-en Bacpac-bestanden.
- Een SAS-URL voor Azure-Blob naar een dacpac-of Bacpac-bestand. 

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>Een SQL Database DAC-pakket gebruiken met SQL Edge

Volg de onderstaande stappen om een SQL Database DAC-pakket of een BACPAC-bestand te implementeren (of importeren) `(*.dacpac)` `(*.bacpac)` met behulp van Azure Blob-opslag en een zip-bestand. 

1. Een DAC-pakket maken/extra heren of een Bacpac-bestand exporteren met behulp van het hieronder vermelde mechanisme. 
    - Een SQL Database DAC-pakket maken of extra heren. Zie [een DAC extra heren uit een Data Base](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) voor informatie over het genereren van een DAC-pakket voor een bestaande SQL Server-Data Base.
    - Een geïmplementeerd DAC-pakket of een Data Base exporteren. Zie [een gegevenslaag toepassing exporteren](/sql/relational-databases/data-tier-applications/export-a-data-tier-application/) voor meer informatie over het genereren van een Bacpac-bestand voor een bestaande SQL Server-Data Base.

2. Zip het `*.dacpac` of het `*.bacpac` bestand en upload het naar een Azure Blob Storage-account. Zie [blobs uploaden, downloaden en weer geven met de Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md)voor meer informatie over het uploaden van bestanden naar Azure Blob-opslag.

3. Genereer een Shared Access Signature voor het zip-bestand met behulp van de Azure Portal. Zie [Toegang delegeren met Shared Access signatures (SAS)](../storage/common/storage-sas-overview.md)voor meer informatie.

4. Werk de configuratie van de SQL Edge-module bij om de gedeelde toegangs-URI voor het DAC-pakket op te geven. Voer de volgende stappen uit om de SQL Edge-module bij te werken:

    1. Ga in het Azure Portal naar de implementatie van IoT Hub.

    2. Selecteer **IoT Edge** in het linkerdeelvenster.

    3. Zoek en selecteer op de pagina **IOT Edge** de IOT Edge waar de SQL Edge-module is geïmplementeerd.

    4. Selecteer op de pagina **apparaat IOT edge apparaten** de optie **module instellen**.

    5. Op de pagina **modules instellen** en klikt u op de Azure SQL Edge-module.

    6. In het deel venster **IOT Edge module bijwerken** selecteert u **omgevings variabelen**. Voeg de `MSSQL_PACKAGE` omgevings variabele toe en geef de SAS-URL op die u in stap 3 hierboven hebt gegenereerd als de waarde voor de omgevings variabele. 

    7. Selecteer **Update**.

    8. Selecteer op de pagina **modules instellen** de optie **controleren + maken**.

    9. Selecteer op de pagina **modules instellen** de optie **maken**.

5. Nadat de module is bijgewerkt, worden de pakket bestanden gedownload, uitgepakt en geïmplementeerd op basis van het SQL Edge-exemplaar.

Op elke keer dat de Azure SQL Edge-container opnieuw wordt opgestart, probeert SQL Edge het zip-pakket te downloaden en te evalueren of er wijzigingen zijn. Als er een nieuwe versie van het dacpac-bestand wordt aangetroffen, worden de wijzigingen geïmplementeerd in de data base in SQL-rand.

## <a name="known-issue"></a>Bekend probleem

Tijdens sommige DACPAC-of BACPAC-implementaties kunnen gebruikers een time-out van de opdracht ondervinden, wat resulteert in het mislukken van de implementatie bewerking DACPAC. Als u dit probleem ondervindt, gebruikt u de SQLPackage.exe (of SQL Client Tools) om de DACPAC of BACPAC-maually toe te passen. 

## <a name="next-steps"></a>Volgende stappen

- [Implementeer SQL Edge via Azure Portal](deploy-portal.md).
- [Gegevens streamen](stream-data.md)
- [Machine learning en AI met ONNX in SQL Edge](onnx-overview.md)