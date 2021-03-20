---
title: Bulkgewijs kopiëren van bestanden naar database
description: Meer informatie over het gebruik van een oplossings sjabloon voor het bulksgewijs kopiëren van gegevens van Azure Data Lake Storage Gen2 naar Azure Synapse Analytics/Azure SQL Database.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 5f68c7b33a26bc8c01f3f413943351ff8d61b380
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376118"
---
# <a name="bulk-copy-from-files-to-database"></a>Bulkgewijs kopiëren van bestanden naar database

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt een oplossings sjabloon beschreven die u kunt gebruiken om gegevens bulksgewijs te kopiëren van Azure Data Lake Storage Gen2 naar Azure Synapse Analytics/Azure SQL Database.

## <a name="about-this-solution-template"></a>Over deze oplossings sjabloon

Met deze sjabloon worden bestanden opgehaald van Azure Data Lake Storage Gen2 bron. Vervolgens wordt het bestand in de bron doorlopend en gekopieerd naar het doel gegevens archief. 

Deze sjabloon biedt momenteel alleen ondersteuning voor het kopiëren van gegevens in de **DelimitedText** -indeling. Bestanden in andere gegevens indelingen kunnen ook worden opgehaald uit het brongegevens archief, maar kunnen niet worden gekopieerd naar de doel gegevens opslag.  

De sjabloon bevat drie activiteiten:
- Met activity voor **meta gegevens ophalen** worden bestanden van Azure data Lake Storage Gen2 opgehaald en door gegeven aan de volgende *foreach* -activiteit.
- De **foreach** -activiteit haalt bestanden op uit de activiteit *meta gegevens ophalen* en herhaalt elk bestand naar de *Kopieer* activiteit.
- **Kopieer** activiteit bevindt zich in de *foreach* -activiteit voor het kopiëren van elk bestand uit de brongegevens opslag naar het doel gegevens archief.

De sjabloon definieert de volgende twee para meters:
- *SourceContainer* is het pad naar de basis container waar de gegevens in uw Azure data Lake Storage Gen2 worden gekopieerd. 
- *Source Directory* is het mappad onder de hoofd container waaruit de gegevens worden gekopieerd in uw Azure data Lake Storage Gen2.

## <a name="how-to-use-this-solution-template"></a>Deze oplossings sjabloon gebruiken

1. Ga naar de sjabloon **bulksgewijs kopiëren van bestanden naar data base** . Een **nieuwe** verbinding maken met het bron Gen2-archief. Houd er rekening mee dat ' GetMetadataDataset ' en ' source Dataset ' verwijzingen zijn naar dezelfde verbinding als de bron bestands opslag.

    ![Een nieuwe verbinding maken met het brongegevens archief](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Maak een **nieuwe** verbinding met de Sink-gegevens opslag waarnaar u gegevens kopieert.

    ![Een nieuwe verbinding maken met het sink-gegevens archief](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Selecteer **deze sjabloon gebruiken**.

    ![Deze sjabloon gebruiken](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. U ziet een pijp lijn die wordt gemaakt, zoals wordt weer gegeven in het volgende voor beeld:

    ![De pijp lijn controleren](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Als u **Azure Synapse Analytics** hebt gekozen als de gegevens bestemming in **stap 2** hierboven, moet u een verbinding met Azure Blob Storage voor fase ring opgeven, zoals vereist door Azure Synapse Analytics poly base. Zoals in de volgende scherm afbeelding wordt weer gegeven, genereert de sjabloon *automatisch een opslagpad* voor uw Blob Storage. Controleer of de container is gemaakt na de uitvoering van de pijp lijn.
        
    ![Poly base-instelling](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Selecteer **debug**, voer de **para meters** in en selecteer **volt ooien**.

    ![Klik op * * fout opsporing * *](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. Wanneer de uitvoering van de pijp lijn is voltooid, ziet u resultaten die vergelijkbaar zijn met het volgende voor beeld:

    ![Bekijk het resultaat](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Data Factory](introduction.md)